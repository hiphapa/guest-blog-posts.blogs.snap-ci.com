---
layout: post
title:  "Hack Scala/Play build on Snap CI"
date:   2013-09-18
author: Shishir Das
categories: java scala play deployments
---

Typically, we do not claim to offer first class support for a language/framework on Snap unless we provide some basic heuristics to help users set up their builds. Based on this defintion, Snap currently supports Ruby, Rails & Java builds. However, Snap can run builds for any JVM based language. In this post, I will talk about how to set up a build for simple Scala/Play project.

If you have your own project on GitHub you can use that. You would need to add sbt-laucher to your project if not present and a script to invoke it. This might look something like
{% highlight bash %}
>> curl -LO http://repo.typesafe.com/typesafe/ivy-releases/org.scala-sbt/sbt-launch//0.12.4/sbt-launch.jar
>> echo 'java -Xms512M -Xmx1536M -Xss1M -XX:+CMSClassUnloadingEnabled -XX:MaxPermSize=384M -jar `dirname $0`/sbt-launch.jar "$@"' > sbt
>> chmod u+x sbt
{% endhighlight %}

For those you don't have a scala/play project you can follow the following instructions.

##Project Set Up.

You can find the instructions to setup a Play Todolist project [here](http://www.playframework.com/documentation/2.1.x/ScalaTodoList). After you have successfully created the project, you need to add sbt-launcher(v0.12.4) to the root of the project project.

{% highlight bash %}
>> cd ~/projects/todolist
(todolist) >> curl -LO http://repo.typesafe.com/typesafe/ivy-releases/org.scala-sbt/sbt-launch/0.12.4/sbt-launch.jar
{% endhighlight %}

Create script to invoke sbt-laucher in PROJECT_ROOT.

{% highlight bash %}
(todolist) >> echo 'java -Xms512M -Xmx1536M -Xss1M -XX:+CMSClassUnloadingEnabled -XX:MaxPermSize=384M -jar `dirname $0`/sbt-launch.jar "$@"' > sbt
(todolist) >> chmod u+x sbt
{% endhighlight %}

Run test on local machine.

{% highlight bash %}
(todolist) >> ./sbt clean test
{% endhighlight %}

##Add the project to github

Create a [repository](https://help.github.com/articles/create-a-repo) on Github named todolist and push local todolist repository to github

{% highlight bash %}
(todolist) >> git init .
(todolist) >> git add .
(todolist) >> git commit -m "first commit"
(todolist) >> git remote add origin git@github.com:<<your_github_id>>/todolist.git
(todolist) >> git push -u origin master
{% endhighlight %}

##Setup build for todolist project on [Snap](https://snap-ci.com/)

Login to Snap and add "todolist" project. Snap will try to detect the framework and fail, you would see a warning stating "Manually Configure a project"

<img src="/assets/images/screenshots/scala/build-plan-detection.png" class="screenshot"/>

Click "+ADD NEW". And select "Custom Stage" (Ignore the Ruby version drop down ;))

Enter "Build" in "STAGE NAME" and "./sbt clean test" in "Tasks to be executed".

<img src="/assets/images/screenshots/scala/manual-stage.png" class="screenshot"/>


Click "Create Project"

That’s all there is to it. Snap will now monitor your repository and run builds for all commits. Snap has a lot more to offer from CCTray, HipChat and Campfire build notifications to manual gated deployments to production environments. Also, we’re just getting warmed up. If there’s a feature you’d like to see or if you just want to say hi feel free to [contact us]({{ site.link.contact_us }}).