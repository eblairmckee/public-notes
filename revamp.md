- async call
  - fetch, axios
  - lifecycle method
    - useEffect or componentDidMount
- data sanitizing
  - filter/sort
- basic state management
  - hooks vs classes
- layout
  - flexbox vs grid
- css
  - psuedo elements and selectors
- need an opportunity for DRY

One idea I’ve had for FE interview questions that combined HTML and JS with maybe a splash of CSS is to implement the interface for commonly known things in the physical world as code: a radio dial (might be aged out), elevator call system, and vehicle window wiper controls to name a few. The nice thing is that there are some nuanced differences in some of the implementations. Take the window wipers, the basics are on and off with a visual representation of when the wiper blades are in motion vs idle. Then you add on the one-off wipe control, the ability to control the idle time, and double-speed. In an interview, you could start with implementing on/off and one of the other modes. Then depending on how quickly the candidate gets through that, add on another mode, and another. Questions like these provide a familiar anchor for how something works from the user perspective (in many cases) and provide some obvious next steps and test on building an interactive interface rather than just come JS algorithm code or some pure layout HTML/CSS. It’s been on my todo list to design one like this, but since I haven’t gotten to it yet, I figured I’ll put it out here for someone else to run with if it sounds good.

Hi everybody.
My apologies for the very long delay in following up here. The big Core LwR release (and post-release cleanup) took over my work (and non-work) life for many months. But things are finally looking more normal-ish.
To that end, I would like to get back to the alternative interview option if folks are still interested. And no hard feelings if you want to drop off. I know it's been like half a year since I first floated the idea.
I figure work in earnest will have to wait until 2021 given the holidays are upon us. But in case anyone is working (or suffering from poor work-life balance and checking Slack while on vacation), here are some random ideas for potential projects I'd be curious to get people's thoughts on.
My thought was to leave the tech stack, etc. either up to the candidates or to the area doing the interview if they want to specify a framework for their hiring needs or something like that.

1. Build out a UI that mimics being at an ATM and entering how much money you want, with the option to choose the types of bills. I thought of this a little while ago when doing this myself in real life and being annoyed at the UI/UX experience :smile:
   It would be nice to require some sort of API call and handling as part of this project -- so maybe we could rig some kind of fake backend call that does pin authentication or something like that, and make candidates call it?
2. Use one of NASA's many free APIs to build something. https://api.nasa.gov/#:~:text=NeoWs%20(Near%20Earth%20Object%20Web,browse%20the%20overall%20data%2Dset.
   For example, we could give them the relevant APIs and ask them to build a site that takes a user-entered date (or date range maybe) and shows details about the asteroids that will be closest to Earth. Maybe an Asteroid Wedding Planner app, make sure your special date isn't going to be threatened by a near-earth object. :smart:
3. Use the free sites thedogapi.com and thecatapi.com (the former is blocked by Rally's VPN as an adult site, fyi, while the latter is not :thinking_face:) to build a site that presents a random dog picture vs. a random cat picture and asks users to choose a winner. The site should collect and display some specified data on the "matches".
   In an ideal interview world, we could have a set of projects for a candidate to choose from. But I figure to start, we probably want to pick just one and go from there.
   For all of the above, my thought is we'd need to build out some sort of initial project in more specificity that we would want say, 3-4 hours of time spent on. And then come up with a handful of possible follow-up questions or tasks that we could ask the candidate in person to do as a pair programming exercise or something like that. To serve as a gauge of how well they understand their work, defense check against cheating, etc.
   We could give some mocks to design to, or just describe the desired functionality and leave it all up to them (probably my preference).
   Any thoughts on the above? Or any alternate ideas?
   Hope you all are staying safe and having as enjoyable a holiday season as possible in the current circumstances!
