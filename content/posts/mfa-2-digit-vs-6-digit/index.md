---
title: "Can a 2-Digit MFA Code Really Be More Secure Than 6?"
date: 2026-04-15T09:00:00+10:30
description: Microsoft moved to a 2-digit MFA prompt. That sounds like a downgrade — but the mechanism behind it is very different.
hero: mfa-digits.png
menu:
  sidebar:
    name: 2-Digit MFA vs 6
    identifier: mfa-2-digit-vs-6-digit
    weight: 30
tags: ["Security", "MFA", "Enterprise"]
categories: ["Security"]
---

I had an interesting chat with a co-worker a couple of weeks ago about Microsoft's move to use a 2-digit MFA prompt instead of the traditional 6-digit time-based code. At first glance, that sounds like a downgrade; fewer digits must mean weaker security, right? But the truth is, the mechanism behind it is very different.

## Email codes and "magic links"

Many applications send a one-time code or magic link to your email. While convenient, this is only as secure as your email account. If an attacker already has access to your inbox, they can trivially intercept those links and reset your password.

Magic links are fine for low-risk consumer apps, but not for administrative or corporate logins. This is also a good reminder to ensure MFA is enabled and up to date for your primary personal email. It is enabled, right? Read Mat Honan's [horrifying story](https://www.wired.com/2012/08/apple-amazon-mat-honan-hacking/) from 2012 if it's not.

## SMS codes

The most common, and least secure, form of MFA is SMS-based codes. While better than nothing, these are vulnerable to [SIM swapping](https://informationsecurity.wustl.edu/the-sim-swap-scam/), where an attacker convinces your carrier to port your number to their SIM card and intercepts your messages. Once that happens, they can receive your MFA codes and take over your accounts.

## Time-based codes (TOTP)

A more secure option is [TOTP](https://datatracker.ietf.org/doc/html/rfc6238) (Time-based One-Time Passwords), used by apps like Google Authenticator or Authy. These generate a 6-digit code that changes every 30 seconds, based on a shared secret and the current time.

It's much harder to intercept than SMS, but still vulnerable to phishing. Attackers can trick users into entering the code on a fake site and reuse it instantly. There's also a grace period to allow for device clock drift, which can [introduce its own vulnerabilities](https://workos.com/blog/authquake-microsofts-mfa-system-vulnerable-to-totp-brute-force-attack).

## Push MFA and number matching

Push-based MFA, used by Microsoft Authenticator and GitHub Mobile, works differently. When you sign in, your phone receives a push notification asking you to approve the sign-in.

Originally this was just an Approve / Deny button, but that led to push fatigue — users blindly tapping Approve to clear notifications.

Microsoft addressed this by [adding number matching](https://learn.microsoft.com/en-us/entra/identity/authentication/how-to-mfa-additional-context): a 2-digit code displayed on your login screen that must be entered in the Authenticator app. This ensures you can't accidentally approve someone else's sign-in, and even if your password is stolen, attackers can't complete MFA without the correct session code.

The two digits aren't the secret — they're proof that your device and login session are in sync.

## Passkeys: where MFA is heading

The next evolution is [passkeys](https://fidoalliance.org/passkeys/) — cryptographic credentials that remove passwords entirely. They use public/private key pairs stored securely on your device and verified by biometrics or a local PIN.

A few things to know:
- You can create a passkey that's local only (stored on one device), or one that syncs via a cloud service like iCloud Keychain or Google Password Manager.
- Local passkeys are the most secure, but less convenient.
- Synced passkeys depend on the strength of your cloud account's protection.

Microsoft is already taking the next step, letting users go completely passwordless. Each method relies on strong cryptographic authentication, not a shared secret — phishing-resistant and without the need to remember, store, or rotate passwords.

## Closing thoughts

Passkeys, push MFA, and one-time codes sit on the same spectrum, balancing security, usability, and human behaviour. Sometimes, fewer digits — or no password at all — can actually mean stronger protection. But, as with most IT security, the human is still the weakest link in the chain.
