---
title: "Personal CV Page"
date: 2021-06-06T12:23:53+02:00
draft: false
---
### &nbsp; 
## Given
While I was working on my macOS app [Exposure Adjustor](https://apps.apple.com/us/app/exposure-adjustor/id1570960511?mt=12). I forgot that in order to upload it to the App Store I have to provide a Support URL, Marketing URL and Privacy Policy URL. Till now it wasn't a problem all of the apps that I've published were done for big companies that already had a website legal notes etc. The Exposure Adjustor allows you to lock the exposure of your camera to make sure that your face is visible even if there is direct sun or window behind you.
### &nbsp; 
I decided to take this opportunity and create a personal CV page you can check it here [rogowski.page](https://rogowski.page), where I can also link and publish my articles (I'm not writing too often but it's nice to have your personal space till now I was only publishing on dev.to). I decided to share my experience because with static site generator frameworks it's cheap and easy to do it in one day.
### &nbsp; 
## When
I bought the domain on www.ovh.com they have this nice plan where you can buy a domain with a **free** hosting included it's called Start10M solution and is described [here](https://docs.ovh.com/gb/en/hosting/activate-start10m/). As they are writing on their page 
### &nbsp; 
*With Start10M, OVHcloud gives you a 10MB Web Hosting plan, and an email account with 5GB storage.*
### &nbsp; 
So the only cost is the cost of the domain itself. 
### &nbsp; 
Because of the hosting plan limitations, I used a static site generator framework. I have some experience with other frameworks like [11ty](https://www.11ty.dev) and integrating it with [GitLab Pages](https://docs.gitlab.com/ee/user/project/pages/) but I like to try new technologies so this time I used [hugo](https://gohugo.io).
### &nbsp; 
I searched through some themes but finally, I decided to use beautiful theme called [almeida-cv](https://themes.gohugo.io/almeida-cv/). It's a clean nice looking CV format page but the thing that it was missing was the possibility to list my articles.
### &nbsp; 
I didn't want to add the navigation header on top because it would destroy the CV paper feeling that I liked, so I decided to list the last 5 articles that I've written. I also did some fixes to the layout especially on mobile devices. I decided that the CV look is less important than readability.
### &nbsp; 
To upload the page to the server I'm using the open-source [cyberduck](https://cyberduck.io) app and it's great and easy to use. 
### &nbsp; 
## Then
I think creating such page is a great way to improve your personal branding because you can put much more information on the web page than in an actual CV. I will update you on statistics in the future, I linked this page in my [LinkedIn account](https://www.linkedin.com/in/rogowski-michal/), [Twitter](https://twitter.com/michrogowski) account and in [dev.to](https://dev.to/michalrogowski).
### &nbsp; 
For sure I still have to improve some UI issues but as something that I was able to do during one weekend because my kid was with his grandparents I think it's worth it, and it doesn't require a lot of maintenance.
### &nbsp; 
I found the problem with the template I had to add crossorigin=anonymous to the theme css links to support CORS type deployment where the baseURL may be different than the URL used to see the web page (origin). I've created the fix - PR available [here](https://github.com/ineesalmeida/almeida-cv/pull/10) and the source code is available [here](https://github.com/MichalRogowski/rogowski), if you'd like to buy the app it's [here](https://apps.apple.com/us/app/exposure-adjustor/id1570960511?mt=12).