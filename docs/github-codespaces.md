# First thoughts on Github Codespaces

I've been looking forward to trying out [Github Codespaces](https://github.com/features/codespaces) since it was announced at Github's yearly developer conference back in May, and was excited to get invited to the beta a few weeks ago. 

Codespaces are an "on-demand" development server that's [customized for each project](https://docs.github.com/en/free-pro-team@latest/github/developing-online-with-codespaces/configuring-codespaces-for-your-project) through a `devcontainer.json` file. The on-demand devservers also integrate tightly with VS Code, which has become the most popular editor and [may stay that way for a long time](https://blog.robenkleene.com/2020/09/21/the-era-of-visual-studio-code/), and is my personal editor of choice.

Using on-demand environments for a development project is very compelling: getting started developing on a project will be much simpler, and the painful "works on my machine" issue is less likely to occur. On-demand devservers are also much cheaper than dedicated devservers. We're switching to on-demand devservers at Facebook for those reasons, and I expect other companies are doing the same.

I've tried out other on-demand environnments, including AWS's Cloud9, but never stuck with them. But since I use VS Code, and most of my projects live on Github, I was keen to see if Codespaces could replace doing development locally on my Macbook.

The short answer is: mostly. I've been doing my blog's development on Codespaces for the last two weeks, and it's gone really well, especially for just editing some CSS and markdown. But another project I've been working on, which involves some datasets that are about 100MB, didn't go as well: opening those files kept causing VS Code to hang and requiring me to restart the devserver. 

Those issues came up because the beta version of their on-demand servers are pretty lite, with just 2 cores, 4GB of ram and 64GB of disk, although [the pricing they've announced for the full release](https://docs.github.com/en/free-pro-team@latest/github/developing-online-with-codespaces/about-billing-for-codespaces) includes machines with up to 8 cores and 16 GB of ram. 

But for app development projects that don't have lots of heavy data, there's already several great features available in the beta version which make Github's on-demand devservers a compelling alternative to developing locally, especially if you're already using VS Code or Github.

First, it's really easy to connect to your on-demand devserver straight from VSCode. Once you've created a Codespace devserver from the web UI, you can just use the command "Open in VS Code," and it opens your VS Code desktop app, prompts you to install all the relevant Codespace extensions for VS Code, and connects to your remote server flawlessly. Using the VS Code desktop app is also completely optional, as the web based editor is still quite good, although it won't have all your local VS Code features like custom keyboard shortcuts.

You can also connect to an existing dev server seamlessly from your VSCode desktop app. From VS Code, just run the command "Connect to Codespaces" and select the environment you want. If your codespace was sleeping, it will wake up automatically and be ready in about a minute.

Second, viewing the web apps you're developing on the remote devserver through your browser is also straightforward. I was nervous about this, because having configured my own remote dev environments on AWS before, it's tricky to get features like live-reloading working correctly.

But I was able to run the VS Code command "Forward a Port", and then opened localhost in my browser. Live reload worked perfectly without any configuration necessary. What a time to be alive!

Third, it's also pretty easy to personalize you devserver environment. You'll need to set up a public `dotfiles` repo on your Github account, which will automatically run an `install.sh` script when you create a new codespace. [I've used this to install a few packages](https://github.com/tjwaterman99/dotfiles) that I use regularly, like changing the terminal's shell and prompt. 

It's a little confusing though that the install script will run after you can access the workspace, so the first time you're on, you'll be be curious why the commands you've installed aren't available. You can also watch the progress of your `install.sh` script in the web page of the Codespace's "creation log".

So VS Code makes it really easy to get a web-app development project up and running, and personalize your environment. The next logical question is to ask about deploying your project: how do devservers handle secrets, like SSH keys for for connecting to a "production" server?

Secret's are currently not supported in Codespaces, but they're [on the roadmap](https://github.community/t/secrets-support-in-github-codespaces/131984). Without secrets, it's not really possible for Codespaces to completely take over a "dev environment," since those environments typically need to connect to production services in some way, and not just modify an application locally. But it's not a big deal to add those credentials to your environment, since the state of the workspace will be saved even when you shut it down.

Another wonderful feature is that the on-demand devserver is auto stopped once it's been idle for 30 minutes. You'll still pay the storage cost associated with your instance while it's alseep, and I'd estimate that their "basic" type will probably run about $15 per month all in, depending on how much development you do. There's more details of their pricing [here](https://docs.github.com/en/free-pro-team@latest/github/developing-online-with-codespaces/about-billing-for-codespaces).

Github hasn't announced an official release date for Codespaces yet, but it's probably going to be some time in 2021. I'm really curious to see how companies move to on-demand devservers, and whether open-source projects start adding those `devcontainer.json` files. These environments will also make teaching programming so much better - I'd wager some schools will start moving their course materials over to Github to take advantage of the simplified environment set up. 

It's really amazing how much developer tools have improved in just the last few years, with free CI/CD, editors like VSCode, and now on-demand devservers. On-demand devservers might just be the biggest improvement yet.