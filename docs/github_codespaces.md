# First thoughts on Github Codespaces (WIP)

I wrote four months ago about [Github's announcement of Codespaces](https://www.corbettanalytics.com/github-announces-codespaces-a-web-based-ide/), and I was just invited to the public beta of the product.

I've been really excited about Codespaces, and I think [the github's browser-based editors is a pretty big deal](https://blog.robenkleene.com/2020/09/21/the-era-of-visual-studio-code/).

Here are my first thoughts on using Github codespaces:

- Can you connect to the codespace from the vscode desktop app? Yep, pretty easy. Just use the command "Open in VS Code" and it opens your VS Code desktop app, prompts you to install the relevant Codespaces extension, and opens your new codespace.

- Viewing apps in your browser was straightforward. I just ran the command "Forward a Port", typed in 8000, and then opened localhost in my browser. Live reload worked perfectly without any configuration necessary.

- The state of the workspace was saved between runs, and auto stopped after it was closed.

- Also pretty easy to set up a public `dotfiles` repo on your Github account, which will automatically run an `install.sh` script when you create a new codespace. I've used this to install a few packages that I use regularly and swap my shell and prompt. 

It's little confusing though that the install script will run after you can access the workspace - so the first time you're on, you'll be confused why the commands you've installed aren't available. 

You can also watch the progress of your `install.sh` script in the Codespace's "creation log".

To use the custom shell, I also had to edit my local VSCode's settings, which took me a couple minutes to figure out but was easy enough.

Now that my project was running, my personal config was set up, and I had deployed a simple change to my blog, I wanted to test out managing my "production" servers that run on AWS and Heroku.

- Secret's are currently not supported, but [on the roadmap](https://github.community/t/secrets-support-in-github-codespaces/131984). Without secrets, it's not really possible for Codespaces to take over a "dev environment," since those environments typically need to connect to production services and not just modify an application locally. 

- It would be nice if Github added keyboard shortcuts for viewing your codespaces, the same way they have shortcuts for visiting your notifications tabs.

- You can use VScode's command prompt to connect to existing codespaces. 

- I could find the pricing and here's how it compares to running my own dev server. 

- 64g of space available, not huge. I imagine they'll offer the option of more if you ever need it.