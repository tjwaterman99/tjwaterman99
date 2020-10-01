# First thoughts on Github Codespaces (WIP)

I've been looking forward to Codespaces since it was announced at Github's yearly developer conference back in May, and was excited to get invited to the beta a few weeks ago. 

Github's "on-demand" development environments [could be a pretty big deal](https://blog.robenkleene.com/2020/09/21/the-era-of-visual-studio-code/), since they make getting started with a project much easier.

Here are my first thoughts on using Github codespaces:

- Can you connect to the codespace from the vscode desktop app? Yep, pretty easy. Just use the command "Open in VS Code" and it opens your VS Code desktop app, prompts you to install the relevant Codespaces extension, and opens your new codespace. You can't create a new Codespace though - you can only connect to the ones you've already created.

- Viewing apps in my browser was also straightforward. I just ran the command "Forward a Port", typed in 8000, and then opened localhost in my browser. Live reload worked perfectly without any configuration necessary. Using the command "Open port in browser" wasn't working for me though.

- The state of the workspace was saved between runs, and auto stopped after it was closed.

- Also pretty easy to set up a public `dotfiles` repo on your Github account, which will automatically run an `install.sh` script when you create a new codespace. I've used this to install a few packages that I use regularly, like changing the terminal's shell and prompt. 

It's little confusing though that the install script will run after you can access the workspace - so the first time you're on, you'll be confused why the commands you've installed aren't available. 

You can also watch the progress of your `install.sh` script in the web page of the Codespace's "creation log".

To use the custom shell I had set up in my dotfiles, I also had to edit my local VSCode's settings, which took me a couple minutes to figure out but was easy enough.

Now that my project was running, my personal config was set up, and I had deployed a simple change to my blog, I wanted to test out managing my "production" servers that run on AWS and Heroku.

- Secret's are currently not supported, but [on the roadmap](https://github.community/t/secrets-support-in-github-codespaces/131984). Without secrets, it's not really possible for Codespaces to take over a "dev environment," since those environments typically need to connect to production services and not just modify an application locally. 

- It would be nice if Github added keyboard shortcuts for viewing your codespaces, the same way they have shortcuts for visiting your notifications tabs.

- I could find the pricing and here's how it compares to running my own dev server. 

- 64g of space available, not huge. I imagine they'll offer the option of more if you ever need it.

- Currently the beta versions of codespace with 2 CPUs and limited RAM just isn't great for any data processing work.