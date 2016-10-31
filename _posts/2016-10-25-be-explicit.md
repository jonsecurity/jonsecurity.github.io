---
layout: post
title: Be Explicit
---

When naming things, decide on a name that reflects the privilege. Name things for what they are. In MS Windows, the Administrators group is well named. It’s equivalent `root` is also well named. There is no user who has more access rights than those two. Naming can go wrong.

In June, 2016, Google’s project zero found a [bug][[task_t considered harmful] in Apple’s kernel. This bug affects any application. Any app could bypass all security checks and run malicious code as root. The bug affected all versions of iPhone, and OS X. Billions of devices.



It all came down to a single object created to allow communication between processes. Communication of the form "Hey processes with root privileges, run this code." The entire system broke down because of a poor name choice for a variable.

The name of the problem field is `ownerTask`.  A developer wrote the object into existence. Years later other developers used the object. Year over year, more developers used the object. At some point, people started to misuse the `ownerTask` variable. They assumed the kernel was authorizing it. They stopped verifying the value of `ownerTask` matched. Even sample code distributed showed improper use of this variable.

The original developer knew each process needed to manage ownership. Later developers did not.

Three areas to be more explicit in.

1. vague variables, group names, or user names

Which is more clear to it’s function:

    a. `developers`
    b. `all_repos_read_write_access`


2. using data structures that must perform lookups vs flat lists

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


This information is especially important in security controls. Can you audit a control functions if you either cannot understand the control? Many places to lookup variables or functions make it difficult to grasp what’s allowed. Be explicit means retaining what’s expected by choosing words that matter.


[task_t considered harmful]: https://googleprojectzero.blogspot.com/2016/10/taskt-considered-harmful.html
