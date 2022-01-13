# 900 - Authentication with Magic Links

A while back, I tweeted some words that I'm eating...

Here are the only reasonable options for authentication in apps:
1. Use a cloud provider for auth
2. Have a team dedicated to auth for the company's apps
3. Use HTTP basic auth because security clearly doesn't matter that much for you anyway 🤷‍♂️

Yup, that's right... I hand-rolled my own authentication on this site. But I had a good reason! Remember all the talk above about making things super fast by collocating the node servers and databases close to users? Well, I'd kinda undo all that work if I used an authentication service. Every request would have to go to the region that provider supported to verify the user's logged in state. How disappointing would that be?

Around the time I was working on the authentication problem, Ryan Florence did some [live](https://www.youtube.com/watch?v=f1OFqldJ9FE) [streams](https://www.youtube.com/watch?v=rU_C2pg_P40) where he implemented authentication for his own Remix app. It didn't look all that complicated. And he was kind enough to give me an outline of the things that are required and I got most of it done in just one day!

Something that helped a great deal was using magic links for authentication. Doing this means I don't need to worry about storing passwords or handling password resets/changes. But this wasn't just a selfish/lazy decision. I feel strongly that magic links are the best authentication system for an app like mine. Keep in mind that pretty much every other app has a "magic link"-like auth system even if it's implicit because of the "reset password" flow which emails you a link to reset your password. So it's certainly not any less secure. In fact, actually more secure because there's no password to lose.

Oh, and before you say:

"But if there's no password, I can't use my password manager and I'll forget which of my 30 email addresses I used to sign up on your site!"

Your password manager can definitely store login information that has only an email address and no password. Do that.

Ok, let's take a look at a diagram of the authentication flow:

![auth-flow-dark](https://user-images.githubusercontent.com/12828104/149367688-fb446026-8207-4c53-8ffd-eb92fbab3775.png)

Authentication Flow

Some things I want to point out with this flow is that there's no interaction with the database until the user has actually signed up. Also, the flow for sign up and login are the same. This simplifies things a fair amount for me.

Now, let's take a look at what happens when a user navigates to an authenticated page.








== WE ARE HERE ==
