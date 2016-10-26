---
layout: post
title: Be Explicit
---

When naming things, decide on a name that accurately reflects the privilege. Name things for what they are. In MS Windows, the Administrators group is very well named. It’s equivalent `root` is also well named. There is no user who has more access rights than those two. Naming can go very wrong.

In June, 2016, Google’s project zero found a [bug][[task_t considered harmful] in Apple’s kernel. The bug allowed any application, whether from the app store, or anywhere else, to bypass all security checks and run any code as root. All versions of iPhone, and OS X were affected. Billions of devices.

The root cause was a variable named `ownerTask` on an object. The implication is the object tracked which task owned it and handled the authorization, and etc. Even the sample code distributed showed improper use of this variable.

Maybe the original developer knew each process needed to manage ownership, but subsequent developers did not.

Three areas to be more explicit in.

1. vague variables, group names, or user names

Which is more clear to it’s function:

    a. `developers`
    b. `all_repos_read_write_access`


2. using data structures that require lookups vs flat lists

Which function is more clear; this one:

{% highlight javascript %}
     app.get("/loan_application/(?loan_app_id)",
         // do authorization first
         isBorrowerOrCoBorrower(req, res, next),
           (req, res) => {
           // do work
     };
{% endhighlight %}

or this:

{% highlight javascript %}
         app.use(doAuthorization);
         app.get("/loan_application/(?loan_app_id)",
         (req, res) => {
             // do work
         }
         
         function doAuthorization(req, res, next) {
             // lookup some roles from the db
             // map roles to req.path
             // some if/then logic to handle special cases
             // a couple of parallels and waterfalls
             // a few years worth of customizations
             // refactoring
             // etc
         }
{% endhighlight %}

3. url endpoint names that don’t reflect their purpose

{% highlight javascript %}
    app.put(“/admin/user/(?userID)”,
            isUserAdmin(req, res, next),
            (req, res) => {});
    app.get(“/user/(?userID)”, 
            isUserEqualToUserID(req, res, next),
            (req, res) => {/*get user*/});
    app.put(“/user/(?userID)”, 
            isUserEqualToUserID(req, res, next),
            (req, res) => {/*update user*/});
{% endhighlight %}

vs

{% highlight javascript %}
app.put(“/user/(?userID)”,
    isUserLoggedIn(req, res, next),
    (req, res) => {
                if (_.contains(req.session.user.groups, ‘admin’) {
                    // update user
                }
                else if (_.contains(req.user.userID == req.params.userID) {
                    // update user
                } 
                else {
                  // return error
              }
                    
        });
{% endhighlight %}


This information is especially important in security controls. How can you audit whether a control functions if you either cannot understand the control, or there are so many places to lookup variables or functions that it’s impossible to quickly grasp what’s expected. Being explicit means defining what’s expected and making sure that meaning is retained based on the words you choose.


[task_t considered harmful]: https://googleprojectzero.blogspot.com/2016/10/taskt-considered-harmful.html
