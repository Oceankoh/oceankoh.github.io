---
layout: post
title: CyberSecurityRumble CTF 2021
---


Web
------
#### Payback

The challenge web page is pretty simple with only a login and register button. After spending a few minutes orientating myself with how the website works (with the aid of the source code), it was pretty evident that we needed to find a way to get some coins to purchase the flag.

The only line that adds coins to our balance is found in the frontend portion of the app. 
```python
user.balance += int(request.args.get('amount', 0))
```

The following illustrates the rough actions one would have to take to purchase the flag.
1. Register an account on payback site
2. Purchase coins (redirected to payment site)
3. Register an account on payment site
4. Purchase coins on payment site
5. Callback to payback site where coins are added
5. Purchase the flag.

The obstacle is in step 4 when a post request is sent to `/pay`.

```python
@app.route('/pay', methods=['GET', 'POST'])
def pay():
    if 'logged_in' not in session:
        return redirect(url_for('login', next=request.url))

    if request.method == "GET":
        return render_template('amount.html')

    amount = int(request.form.get('amount', 0))

    user = User.query.filter_by(name=session['name']).first()

    if amount > user.balance:
        return "Insufficient balance", 400

    if amount < 0:
        return "Invalid amount", 400

    cb = request.args['callback']
    u = request.args['user']
    nonce = request.args['nonce']

    m = f"user{u}amount{amount}nonce{nonce}".encode()

    sig = SIG_KEY.sign(m, encoding='hex')

    user.balance -= amount
    db.session.commit()

    return redirect(f"{cb}/callback?user={u}&amount={amount}&nonce={nonce}&sig={sig.decode()}", code=302)
```

Analysing the code above, we see that our request is stringed together and signed with a secret key. 

`m = f"user{u}amount{amount}nonce{nonce}".encode()`

This secret key ensures that we are unable to change the payload from the payment to payback sites. We also find that we are unable to purchase any amount of coins other than 0. 

On the payback site, we see how our request from the payment site is processed. 

```python
@app.route('/callback')
@is_logged_in
def callback():
    message, signature = b"", b""

    for param in request.args:
        if param == "sig":
            signature = request.args[param].encode()
            continue

        for value in request.args.getlist(param):
            message += param.encode()
            message += value.encode()

    try:
        verify_key.verify(signature, message, encoding='hex')

        user = User.query.filter_by(name=session['name']).first()
        nonce = int(request.args['nonce'])

        if nonce <= user.nonce:
            raise Exception

        user.nonce = nonce
        user.balance += int(request.args.get('amount', 0))
        db.session.commit()
    except:
        traceback.print_exc()
        return "Something went wrong", 400

    return redirect('home')
```

Here, we notice that the payload is appending the request parameters into a string and then checked that it is valid using the signature. 

My initial idea was to target the signing algorithm. But a quick google tells us that it is not possible. Looking at the code once more, we see how the request parameters used to reconstruct the string that was signed. It simple appends the different parameters together to form a string. 

This means send a request with parameters `user='useramount1'&&amount=0` and `user='user'&&amount=1&&amount=0` would both produce the string  `useruseramount1amount0`. With this, we are able purchase any desired amount of coins. 
We simply create a new account named `x` on the payback site. On the payment site however, we create an account with username `xamount1337`. We then purchase 0 coins with this payment account. We then modify the callback url from  
`/callback?user=xamount1337&amount=0&nonce=1&signature=somesignature`  
to  
`/callback?user=x&amount=1337&amount=0&nonce=1&signature=somesignature`.

We can now purchase the flag as user x.

Flag: `CSR{sometimes_it's_really_hard_to_create_good_flags}`

