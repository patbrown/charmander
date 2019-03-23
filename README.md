# Charmander

A clojure library to make working with firebase easier

[![Build Status](https://travis-ci.org/alekcz/charmander.svg?branch=master)](https://travis-ci.org/alekcz/charmander)

[![Clojars Project](https://img.shields.io/clojars/v/alekcz/charmander.svg)](https://clojars.org/alekcz/charmander)


## Why?

- Authentication, email verification and password resets are tedious and hard. 
- Auth0 gets pricey as you scale
- Firebase auth has a fixed cost of 0 (for now)

## Usage

```clojure

[alekcz/charmander "0.2.0"]

;; In your ns statement:
(ns my.ns
  (:require [charmander.core :as charm]
            [charmander.admin :as charm-admin]))

```

## API
- Token API
  * `validate-token`
- Admin API
  * `init`
  * `create-user`
  * `delete-user`
  * `get-user`
  * `get-user-by-email`
  * `get-user-by-phone-number`
  * `set-user-email`
  * `set-user-password`
  * `set-user-phone-number`
  * `set-user-display-name`
  * `set-user-photo-url`

### Validating tokens

Validates firebase tokens. Validating tokens doesn't require an admin or service account.

```clojure

(charm/validate-token "firebase-project-id" fresh-token)  
;;{
;;	:projectid "firebase-project-id", 
;;	:uid "uid", 
;;	:email "name@domain.com", 
;;	:email_verified false, 
;;	:sign_in_provider "password", 
;;	:exp 0000000000, 
;;	:auth_time 0000000000
;;}

(charm/validate-token "firebase-project-id" stale-token)
;; nil

(charm/validate-token "wrong-firebase-project-id" fresh-token)
;; nil

(charm/validate-token "(.*)-project-ids" fresh-token)
;;{
;;	:projectid "multiple-project-ids", 
;;	:uid "uid", 
;;	:email "name@domain.com", 
;;	:email_verified false, 
;;	:sign_in_provider "password", 
;;	:exp 0000000000, 
;;	:auth_time 0000000000
;;}

```

## Admin 

Manages user data and authentication using your firebase admin or service account credentials using the email/password sign-in provider. At the moment only one firebase app can be used at a time.   

The Admin API is based on the Java Firebase Admin SDK v6.8.0



### Initializing your admin account

1. Get the `json` file containing your creditials by following the instruction here [https://firebase.google.com/docs/admin/setup](https://firebase.google.com/docs/admin/setup)  

2. Enable the email/password sign-in provider.

3. initialize the admin functions in `charmander`

```clojure
(charm-admin/init "/path/to/firebase/key.json" "database-name")  ;; you may need to create a database first
```

### Managing user accounts
The admin api only allows the creating of users using the email/password sign-in provider.

```clojure

(charm-admin/create-user "email@domain.com" "superstrong6characterpassword")  
;;{   
;;    :email email@domain.com
;;    :email-verified false
;;    :uid vMnMJvS28kWr5pb6sByHULMLelJ3
;;    :provider-id firebase
;;    :photo-url nil
;;    :phone-number nil
;;    :display-name nil
;;    :disabled false
;;}

(charm-admin/get-user "vMnMJvS28kWr5pb6sByHULMLelJ3")  
;;{   
;;    :email email@domain.com
;;    :email-verified true
;;    :uid vMnMJvS28kWr5pb6sByHULMLelJ3
;;    :provider-id firebase
;;    :photo-url https://avatars0.githubusercontent.com/u/11717556?s=460&v=4
;;    :phone-number nil
;;    :display-name emailer
;;    :disabled false
;;}

(charm-admin/get-user-by-email "email@domain.com")  
;;{   
;;    :email email@domain.com
;;    :email-verified true
;;    :uid vMnMJvS28kWr5pb6sByHULMLelJ3
;;    :provider-id firebase
;;    :photo-url https://avatars0.githubusercontent.com/u/11717556?s=460&v=4
;;    :phone-number nil
;;    :display-name emailer
;;    :disabled false
;;}

(charm-admin/set-user-email "vMnMJvS28kWr5pb6sByHULMLelJ3" "tony@hawk.cool")  
;;{   
;;    :email tony@hawk.cool
;;    :email-verified false
;;    :uid vMnMJvS28kWr5pb6sByHULMLelJ3
;;    :provider-id firebase
;;    :photo-url https://avatars0.githubusercontent.com/u/11717556?s=460&v=4
;;    :phone-number nil
;;    :display-name emailer
;;    :disabled false
;;}

(charm-admin/set-user-password "vMnMJvS28kWr5pb6sByHULMLelJ3" "5tr0ngp455w0rd")  
;;{   
;;    :email tony@hawk.cool
;;    :email-verified false
;;    :uid vMnMJvS28kWr5pb6sByHULMLelJ3
;;    :provider-id firebase
;;    :photo-url https://avatars0.githubusercontent.com/u/11717556?s=460&v=4
;;    :phone-number nil
;;    :display-name emailer
;;    :disabled false
;;}

(charm-admin/set-user-phone-number "vMnMJvS28kWr5pb6sByHULMLelJ3" "0800123123")  
;;{   
;;    :email tony@hawk.cool
;;    :email-verified false
;;    :uid vMnMJvS28kWr5pb6sByHULMLelJ3
;;    :provider-id firebase
;;    :photo-url https://avatars0.githubusercontent.com/u/11717556?s=460&v=4
;;    :phone-number 0800123123
;;    :display-name emailer
;;    :disabled false
;;}

(charm-admin/set-user-display-name "vMnMJvS28kWr5pb6sByHULMLelJ3" "Tony Hawk")  
;;{   
;;    :email tony@hawk.cool
;;    :email-verified false
;;    :uid vMnMJvS28kWr5pb6sByHULMLelJ3
;;    :provider-id firebase
;;    :photo-url https://avatars0.githubusercontent.com/u/11717556?s=460&v=4
;;    :phone-number nil
;;    :display-name Tony Hawk
;;    :disabled false
;;}

(charm-admin/set-user-photo-url "vMnMJvS28kWr5pb6sByHULMLelJ3" "https://en.wikipedia.org/wiki/Tony_Hawk#/media/File:Skater_Tony_Hawk.jpg")  
;;{   
;;    :email tony@hawk.cool
;;    :email-verified false
;;    :uid vMnMJvS28kWr5pb6sByHULMLelJ3
;;    :provider-id firebase
;;    :photo-url https://en.wikipedia.org/wiki/Tony_Hawk#/media/File:Skater_Tony_Hawk.jpg
;;    :phone-number nil
;;    :display-name Tony Hawk
;;    :disabled false
;;}

```

## Next steps

- Increase efficiency
- Look into core.async for public key refresh
- Build up API 

## License

Copyright © 2019 Alexander Oloo

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
