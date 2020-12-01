---
date: 2020-05-09
---

# Github announces Codespaces, a web-based IDE

On May 8th at Github Satellite, the company announced the release of its new product Codespaces, a web-based IDE that integrates with Github repositories. Since Microsoft acquired Github in late 2018, Codespaces is the third major new product that Github has announced, along with their continuous integration service Github Actions and their package management service Github Packages.

Web-based IDE's like Codespaces can have significant benefits for developers. By standardizing the development environment for a project, along with enabling creation of those environments with a single click, a web-based IDE can save a significant amount of time getting started with a new development project, and reduce errors during development that are caused by different environments. In short, they help solve the "works on my machine" problem that's frequently encountered during development.

Unlike non web-based IDEs such as Eclipse, Pycharm, or Intellij, or Github's own Atom, which run on a developer's laptop, a web-based IDE runs in a web browser and is connected to a remote virtual machine, similar to a remote desktop. The configuration of the remote virtual machine is managed in the same repository as the application's code itself, so two developers that use a web-based IDE can be guaranteed their virtual machines have the same configuration. If they used a non web-based IDE, they could never be certain that their two different laptops have the exact same configuration, leading to the "works on my machine" problem.

It's likely that Github's Codespaces borrows significantly from Microsoft's Visual Studio Codespaces, and in fact the company renamed Visual Studio Online to Visual Studio Codespaces on the same day that Github announced their new product. However Github Codespaces has a different look and feel from Microsoft's Visual Studio, and will likely offer tighter integrations with Github repositories.

The invention of web-based IDEs is nothing new, with AWS's Cloud 9 released in late 2017, and open-source "IDE's" like the Jupyter Notebook have been available since the early 2010's.

But the announcement of Github Codespaces is still significant, since it means that open-source projects which rely on selling a web-based development environment will become less differentiated from their zero-cost self-hosted versions.

As Github is by far the largest host of open-source projects in the world, their ability to offer "1-click" provisioning of a web-based development environment for projects like DBT, Airflow, and even Anaconda, will likely make it cheaper for companies to in-source the for-profit SaaS products provided by vendors funding the developers of those open-source projects.