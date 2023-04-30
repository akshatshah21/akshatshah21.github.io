# Project Pandemonium


> This blog was originally published by me as part of a [blog series](https://medium.com/dscvjti) by [Google Student Developer Clubs, VJTI](https://gdsc.community.dev/veermata-jijabai-technological-institute-vjti-mumbai/).

A few days ago, during a lazy afternoon, while rummaging through my muddle of a cupboard, I found a notebook that had, unsurprisingly, my name scrawled on it. I opened the book somewhere in the middle, but those pages were blank. Then, opening the first page, I smiled, sheepish and nostalgic. Written on the first twenty or so pages of this notebook, was the account of one of my first projects as an engineering student.

I immediately took a trip down memory lane. I perused those logs, consisting of long paragraphs of technical details, diagrams and outlines of activities carried out each day. But what stood out were the number of questions and exclamations. Occasionally, a few lines lamented on the roadblocks and failures that were faced, the uncertainty of whether things will work out and whether we were getting anywhere with the project.

> ## About the project
> _Along with three friends, I was part of a project that aimed to develop a [WiFi Positioning System](https://en.wikipedia.org/wiki/Wi-Fi_positioning_system), or more specifically, an [Indoor Positioning System](https://en.wikipedia.org/wiki/Indoor_positioning_system). Fundamentally, this system would determine the location of a device using wireless access points that are in its vicinity. This project was under Eklavya, a mentorship program of the [Society of Robotics and Automation, VJTI](https://sravjti.in/)._

I would have loved to tell you that despite the misgivings in my memoirs, we managed to accomplish our goals. We didn't. And that's what this blog is about.

---

A project can be very chaotic. A number of things can go wrong, and every action to correct those mistakes can cascade into a wave of defects. In the end (if there is one), you are left with a good solution, a bad solution, a good solution that is not required, or several other variants. I'm no software engineering expert or a project management expert. I'm a final year undergraduate and I want to share some things I've learned after doing some projects in the last four years. In no way are these things guaranteed to be relevant or useful or even correct. But they're food for thought!

# But what's the problem?
<iframe src="https://giphy.com/embed/l1Et0eHN0m9daH7ZS" style="max-width:100%; width:100%;" height="360px" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/spongebob-spongebob-squarepants-season-5-l1Et0eHN0m9daH7ZS"></a></p>
Define the problem. We hear this all the time but seldom do we really put it into effect, or at least with the required level of sincerity, specificity and clarity. *"I want to make a chatting application"* might seem self-explanatory as the definition of the required solution, but it's not. The necessity for a clear understanding of the requirements, the scope and priorities, and the assumptions of a solution cannot be emphasized enough. Indeed, this is one of the major reasons why my WiFi Positioning System project was, in subtle terms, a letdown. I vividly remember *not* being sure about what we were making. To quote the very first line in my logs:

> *"After days of ambiguity, our group was able to decide firmly about the project idea - Localization using Triangulation."*

Except of course, the ambiguity wasn't gone, and to the keen reader, it must be apparent that the topic mentioned in this excerpt is not what we ended up doing.

# Is that what we had decided?

<div style="width:100%;height:0;padding-bottom:53%;position:relative;"><iframe src="https://giphy.com/embed/DrO4Bm325pjhc0BRM0" width="100%" height="100%" style="position:absolute" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div><p><a href="https://giphy.com/gifs/theoffice-DrO4Bm325pjhc0BRM0"></a></p>

It's important to get most requirements or goals clear and sorted out in the beginning. I say "most" because getting the requirements perfect the first time is extremely difficult, if at all possible. If there are stakeholders of the project, it will indubitably prove difficult to solicit and analyze specific and clear requirements from them at the very beginning of the project - one of the biggest drawbacks of the [Waterfall model](https://en.wikipedia.org/wiki/Waterfall_model) of software development. Invariably, some form of an iterative process involving prototyping and feedback is part of the trade. But the sooner these requirements get clear, the sooner a project will reach completion, and lesser time will be spent pursuing features or improvements that are not required.

It's common knowledge that brainstorming in a group can help a lot with this part, because more aspects of the problem and solution will be explored, uncovering more and more questions that will hopefully be answered later on. What's not common knowledge is that all these (likely diverging) lines of thought should be combined to a single, coherent one early enough, otherwise the benefits of collaboration may turn futile.

# Yes, we can!

<div class="tenor-gif-embed" data-postid="22328595" data-share-method="host" data-aspect-ratio="1.20301" data-width="90%"><a href="https://tenor.com/view/thor-really-it-is-what-it-is-gif-22328595"></a><a href="https://tenor.com/search/thor-gifs"></a></div> <script type="text/javascript" async src="https://tenor.com/embed.js"></script>
Can we though?

Another important aspect of planning is feasibility. Gauging the feasibility of a solution is, admittedly, difficult. It requires experience, which I definitely didn't have then, and no one can have enough. However, research can help here. If it's a research project or maybe a data science project, a literature review is helpful, and in most cases, expected. Published work like papers, blogs, source code repositories can help one understand the work that others pursuing similar goals have done, as well as an understanding of the technical difficulty, skills and resources required, and therefore, the feasibility of the project. Did we do a literature review for WiFi Positioning? Yes! But it was very, very haphazard and half-hearted. To make a literature review worthwhile, document it while doing it (and not when you're typing out the final report). Not only does this make researching an active task, but it helps down the line. What's better, going through a 20-page research paper *again* when you need to, or going through a summary that you have written yourself?

# Divide and Conquer

{{<figure src="scrum.png" alt="Divide and Conquer Scrum">}}

Okay, very trite. But it's only logical that you break down the project into large functional pieces, then break those pieces down further, and refine these pieces until they're granular enough for someone to work on. But if you're working in a team, it's equally important to break down development into pieces that can be worked on *independently*, in parallel. How often have you worked on something where you had to wait for your friend to push that commit so that you can continue your work? This is not only a waste of time but a sign of an ineffective breakdown of tasks. With experience, this becomes easier and natural to prevent.

With great division of work, comes the great requirement of reproducibility. Hopefully, you'll use a version control system, like [git](https://git-scm.com/). For those who don't know, [version control](https://www.atlassian.com/git/tutorials/what-is-version-control) is the practice of tracking and managing changes to software code. Also use virtual environments and containers wherever appropriate. The focus of team members should be on the task at hand, not on things such as incompatibilities and installations. Use files for configuration (and check the non-confidential parts into version control) instead of typing out those command-line arguments (or worse, redefining constants in source code; been there, done that) every time. Additionally, combine work *nicely*. That means using a standard workflow to introduce changes to the project, like using pull requests, running tests, and other principles of [Continuous Integration](https://www.atlassian.com/continuous-delivery/continuous-integration).

# Write it down, write it down, write it down
<div style="width:100%;height:0;padding-bottom:56%;position:relative;"><iframe src="https://giphy.com/embed/qlnqDO6nkw8iae2G2s" width="100%" height="100%" style="position:absolute" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div><p><a href="https://giphy.com/gifs/cbs-bob-hearts-abishola-bobheartsabishola-bayo-akinfemi-qlnqDO6nkw8iae2G2s"></a></p>

Again, it's a no-brainer that you should document stuff. Just like you shouldn't stay up too late and get up early. And just like waking up early, very few take this seriously.

*So, one should document stuff? What a drag… Why? We're probably going to write up a final report anyway!*

The point of documenting stuff (and humans writing anything in general) is so that you or anyone else can come back to it later if needed. It's communication: between you, your teammates and the same people in the future. It's important to document technicalities and design decisions since they will be revisited. It is important to document tasks, activities and progress so that as a team (or even individually), you know where the project is headed.

If your project involves running many experiments with different conditions or parameters, consider using a dedicated tool to track them (instead of random TXT files, or notes on your phone, or the worst: messages in your group chat). This can be as simple as using a spreadsheet, but if the experiments are more complex, use specialized tools, such as [MLflow](https://mlflow.org/) for machine learning models. The point here is to document and facilitate easy review, search and filtering of all the experiments you do.

A pro tip is to document issues that you have faced, and how you solved them (this is literally how [Stack Overflow](https://stackoverflow.com/) works; does it sound useful now?). This habit is like an investment. Spend some time now, dig into the problem, and document it. Next time, you're just a Ctrl+F away from solving that problem. This habit also pushes you to actually understand what the problem is and how to solve it, instead of copying something from the documentation or changing parameters randomly (duct tape solutions). To quote my logs:

> *"In the morning, I was able to solve the GPIO errors. But I still don't know how."*


# Success: 0 out of 0 tests passed
<div style="width:100%;height:0;padding-bottom:42%;position:relative;"><iframe src="https://giphy.com/embed/njYrp176NQsHS" width="100%" height="100%" style="position:absolute" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div><p><a href="https://giphy.com/gifs/lotr-gandalf-lord-of-the-rings-njYrp176NQsHS"></a></p>

Iterative and collaborative development *needs* automated testing, possibly on different levels: unit, integration and end-to-end. Let's face it, regressions suck. They're a headache and a very annoying side-effect of iteration. Tests can seem laborious, but for a large or long-term project, they're worth it, and provide reliability and confidence in code. And they're likely to be mandatory if you're working for an organization. Also, consider using a code quality checker.

Along with tests, technical reviews are of enormous benefit. They fuel discussion and brainstorming, and things like bad design or code smells can be detected. Reviews have a great side-effect: knowledge sharing. We hardly did this for our WiFi Positioning project, and it ended up with team members oblivious of how parts developed by others were working. Indeed, grave bugs and opportunities for improvement stay undetected to date.

# Meetings, meetings everywhere

<div class="tenor-gif-embed" data-postid="12712215" data-share-method="host" data-aspect-ratio="1.73723" data-width="100%"><a href="https://tenor.com/view/meeting-yesterdays-meeting-todays-meeting-long-meeting-another-meeting-gif-12712215"></a><a href="https://tenor.com/search/meeting-gifs"></a></div> <script type="text/javascript" async src="https://tenor.com/embed.js"></script>

Here's an example of how a college project group generally gets a meeting scheduled: "Let's meet for project TVA at 10 am". That's usually it, followed by a series of messages negotiating the time of the meeting. This is very likely to be an ineffective, diverging and chaotic meeting. Meetings should be *limited*:
* In terms of **agenda**. There are different kinds of meetings, for example, stand-up meetings for daily updates or task-specific meetings, e.g., discussion on requirements, or an API design, or design considerations of an ML system. A meeting without a preset agenda will likely be an unfruitful one. Since the agenda is undecided, everyone might not be prepared with all areas of discussion
* In terms of **duration**: long meetings can reduce their effectiveness, and in some cases also end up wasting time and proxying for productivity rather than adding value
* In terms of **frequency**; although regularity must be ensured

# Connecting People

<div class="tenor-gif-embed" data-postid="13534843" data-share-method="host" data-aspect-ratio="1.77778" data-width="100%"><a href="https://tenor.com/view/how-about-now-ready-yet-bout-gif-13534843"></a><a href="https://tenor.com/search/how+about+now-gifs"></a></div> <script type="text/javascript" async src="https://tenor.com/embed.js"></script>

Constant collaboration or being in a state of perpetual connectivity is the trend, using an array of tools like email, Discord, Slack et cetera. But this habit of staying online and open to communication all the time can be counterproductive. One major reason is attention fragmentation. Constant notifications disrupting your work and intermittent switches to replying to a message break concentration on the task at hand, leading to lower quality work than you would be doing in a state of focus.

According to Cal Newport in his book *[Deep Work](https://www.calnewport.com/books/deep-work/)*, at work, it's common for people to develop the habit of running their day from their inbox: doing tasks as and when you receive emails about it, and using up much of their time replying to emails rather than actually working. This also prevents them from planning activities beforehand and taking a more proactive approach, since it's easier to use the inbox as a to-do list.

For a project amongst friends, being connected to them constantly for work can easily turn into a hangout. I've lost count of how many times my friends and I have switched from working on a project to playing Skribble on virtual meets.

All these reasons further justify the constraints that I mentioned that you should keep on meetings. Outside of these meetings, try to hold sessions where you actually work in a focused manner.

---

Finally, always remember, software development is hard. Don't beat yourself up if you find things tough, it's part of the journey. And hey, some pages of that book are still blank!
