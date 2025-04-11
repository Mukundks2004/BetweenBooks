---
title: Lessons Learnt at Roseworthy
date: 2025-03-31 14:20:00 +1030

categories: [University]
tags: [robotics,uni,rover,adelaide,story,stories,reflection,recount]
image:
    path: /assets/img/image_with_rover.png
---

My whole life I have always wanted to be a real engineer[^footnote] (sounds wicked) but simultaneously wondered what engineers do. Mid 2024 I had the chance to adjudicate a highschool robotics competition as part of an outreach program my company was doing. The passion and technical depth of the challenge is what finally pushed me over the edge and in August I joined the UTS Rover Team. Here I sit in Adelaide airport on March 31st 2025, having just finished competing at the Australian Rover Challenge- and I want to write down, for my and my team's sake, some of the bigger picture lessons and takeaways from the last 8 days. In all areas, technical, non technical, high level, low level, all of it. Hopefully I come back to this next year and actually learn from and improve on my and my team's mistakes.

[^footnote]: As in, working on hardware 🙃. Not that software engineers aren't real engineers, and I am a software engineer...

## Hofstadter's Law

Popularised in Douglas Hostadter's masterpiece, *Godel, Escher, Bach* (a topic for another day), Hofstadter's Law states that the time taken to complete something will always take longer than the expected duration, even when taking into account Hofstadter's Law. The trickiness and recusion in the definition does not make this law any less true! Mechanical development similar to software development, but it is different in many ways with a key distinction being time taken for assembly is significantly longer. Writing software might take anywhere from a few minutes to a few hours especially in the prototyping stages, which is fantastic, but mechanical engineers have no such luck- to acquire materials, to carefully construct, potentailly redesign and reconstruct takes hours at best, likely days or weeks even for a simple component. Numerous members in our team at several points in time pointed out that the manipulator (the arm) was weeks away from being finished, but to my untrained eyes it looked ready to assembly. It turns out I was right and wrong, the assembly itself was the part that would take weeks, the most difficult part of which was the electronics. 

The electrical lead and one of the more talented mechatronics students stayed up overnight soldering and heat shrinking and crimping- and I was so excited to see the finished product. I could not come to understand the team's morose expressions, even when they explained to me the manipulator was a lost cause. Come morning, the wiring was complete! Why was it then, that the mood in the room did not improve? It turns out none of the team expected it to work. From experience, they knew that something was bound to go wrong since it was untested and lo and behold, something did. In our hurried mounting and lack of testing we did not notice that the arm, unless locked, would swing around with a lot of inertia leading to the rover potentially losing balance. During the first task, the arm heaved over as the rover descended downhill and we toppled. Due to a cascade of problems we secured a total of 4.5/100 for that task.

![Us Doing Repairs](/assets/img/rover_repairs.png)

## Don't Drink on a Uni Trip

Uni trips are unbelievably flexible compared to highschool trips. While yes, we did have to go through the uni for payments and there were restrictions on accommodation and flights. But once we got there it was hands off, very different to my experience at the WSC. We got to control our transport, (kind of) our food, and how- or where- we spent our time. It was wildly fun, and I loved hanging out with what was a really different crowd to what I'm used to. We did some light drinks on Saturday night, after our last competitive day at the comp. Only a couple of the team attended. On Sunday though- the night before the morning out- that's when we really let loose. "We" being this one guy in the team. He was having a blast and getting along with everyone- and smashing beers non stop. One. Boom. Two. Boom. A jug! Boom! Another!! Boom!! By 12- normally past closing, but they made an exception cause there were so many of us- he'd had something like 18 standard drinks. There were empty beer jugs everywhere. I don't understand how a person can hold that much liquid in them. Now's probably also a good time to mention I don't drink basically at all- I had had 2 drinks and a bowl of pasta the whole night- so this was making my eyes wide.

I was kind of itching to leave at the start- I'm not a super outgoing person to begin with, and when we showed up at 7 it was hardly even a party. The others could probably tell, so I made sure to tell them I would look after myself and left to mingle. With enough nerds in one place, I was sure I would find someone I could tolerate. And I did! The crew I ended up chatting with were cool in a humble kind of way, and I enjoyed getting to know them a bit. They were a tech heavy team, which was better for me than mechanical or electrical. At around 11:50 my super awesome team lead tapped me on the shoulder and told me she was rounding everyone up (6 of us between 2 cards, along with all our cargo) and I told her I was ready. 10 minutes later, we're standing in a circle waiting for the last guy when she (again) beckons at me to come closer. When I do she hisses one word into my ear- "RUN!" and takes off for the car, sprinting. I waste no time and follow her, with the others close behind us. We jump into the car- this one only has 2 seats, the rest are covered with cargo- and lock the doors. The others arrive, panting, furious, banging on the door. She's laughing her head off, I'm so confused. It turns out noone wanted to be in the car with the drunk guy and people were fighting to drive in this one so they wouldn't have to deal with him. Honestly I couldn't care less about the guy, but my TL's awesome and I wanted to drive with her anyway. As the others grumpily trudge to the other car, Phillip (who is also super cool- everyone in the team is cool and I love them) stays behind, knocks on the glass and begs us to make space to let him sit in the back. Reluctantly we do, and it turned out to be a wise decision...

20 minutes into the 60 minute drive back to the accommo, music is blasting, we're going 120 or something on the motorway, we're all laughing (Phillip is squished) and life is good. Then we get a call from the other car and just like that, silence. You could feel the temperature in the car go down. We all know what it is. Turns out, drunk dude got sick in the car and when we had a look at it later it was EVERYWHERE, even on the windshield somehow. I don't think I've ever laughed so hard in my life, although I was pretty sleep deprived. I don't know how much it costed (also we crashed the car the next morning too but that's another thing) but it must have been hefty. Ideally noone should have to have that conversation but I can think of no better person to have it than the TL, she just seems to be a natural at dealing with real people. Moral of the story is no more than 2 drinks, especially not at a uni trip.

## Operations Are Important!

The software lead for the team is a truly brilliant guy, and I have learnt a lot from him. The most important thing I think is how important operations are, and how great it is to have them correct and efficient. I say operations to mean things like: bumping in, conducting a field test, setting up the base station, testing drive control. Everything, EVERYTHING must be recorded. You must take photos. If there is quantitative data, it must be recorded. There must be qualitative audio and video logs of us reflecting on the event afterwards. Even the videos of us interacting with the staff, us being briefed, maybe the most pedantic was us recording ourselves when we entered the tent for the first time, to see how we went about the initial scouting and setup process. And for all this data, there must be backups. If there is even a single spare hand and something is not recorded, it is the end of the world for this guy. And he's right! It makes sense, both common sense-wise and from the perspective of someone who wished they had recordings of these events beforehand to study and learn from. And that's just the start.

We have a project management software, and everything MUST go on it, down to the project, subproject, module, submodule, issue, task, everything. This one was harder to keep running because you have to rely on the culture of the team to get it done, and our team just hasn't adopted it completely yet. I myself am guilty of neglecting it a bit. But this man was committed to keeping it working! And I understand how useful it would/could/should be when everyone knows exactly what everyone is doing, it saves so much time not having endless meetings to update people on progress, that just waste everyone's time. Time is a resource- a constrained resource (I learnt about the theory of constaints at work) and using team time to do team-level organization is much smarter than using it for updates. The software lead reaaaally pushed for that, and rightfully so.

This guy is graduating soon and I hope I have absorbed enough to take over as software lead. Big boots to fill, but I'll do my best. Also: his constantly talking about efficiency and operations has inspired me to build a project management software/dashboard for my family, will updated the blog based on how it goes.

## Noone Expects Even Close to 100

## Mechatronics Software Is... Different to Normal Software

Mechatronics engineering is famously a combination of three major disciplines of engineering- mechanical, electrical and software. As a comp sci/software engineering student and software engineer, I thought I would be well suited to meeting programming requirements of the rover. So what if it's all in MATLAB? I can always learn it... is what I thought.

I feel like of the three disciplines, software for robotics- and I mean the software that sits on robots and not peripheral software for telemetry or the team's website- is the most different for a mechatronics engineer. I heard from all of them that they don't claim to be software experts and that their code is probably shit, but that was far from the case. More scarily, the more I learnt about robotics the more I learnt that *I* was actually the one with insufficient software knowledge, as mechatronics engineers (at least these ones) happened to use a suite of tools (a tech stack) I was not familiar with, and I had to work hard to "catch up".

Mechatronics software started out with just Python and C++- both basic programming languages that I am no stranger to. But it quickly evolved to programming on STM boards, which was in C and assembly and involved flashing the board with firmware manually and looking at device specs and pinouts. Then I had to dual boot Ubuntu and refamiliarize myself with the Linux environment to set up ROS, the robot OS. And I spent most of my time here- learning how to use workspaces and nodes, how ROS is selective between client-server and publisher-subscriber models, how ros2-control works, moveit and URDFS and the inverse kinematics of manipulators.

That's just the basics. From there, you can either specialize- which involves getting familiar with Rviz for viualization, Gazebo for simulation, MATLAB's simulink, and various SLAM (simultaneous mapping and localization) tools. Or generalizing in any number of fields/subsystems. For example, using low level programming knowledge to branch into working on the electronics, schematics and circuit design. Or networking/comms knowledge to set up and configure the radios and the networks, fixing the IPs, setting up wifi on the jetson, etc. Or CAD knowledge to start designing parts for manufacture, for 3D printing or for using in the URDF for better visualization. And if nothing else, spend the time learning Linux better because you will 100% have at least one computer issue that Linux familiarity can fix. Everything is adjacent to everything in robotics.

## The Various Ways In Which Chat-GPT Is Used

There's this guy, Charles, a mech engineer. The way he and others have used Chat-GPT has just completely changed the way I think about GPT- specifically for coding. At work as a junior engineer I am mostly reading and writing code and working on defects. While I do do design, I feel like the value I provide comes from my code. As a result, I take pride in my code and refrain from using Chat-GPT even when it might have been able to do things in a more succinct way.

Charles, though... The way he sees it is totally different[^footnote2]. He's an engineer and responsible for bringing the system together. To him, GPT is a tool just like a screwdriver and he is going to use every edge he's got. The value comes from him understanding the code and using it, not from writing it That's not to say he can't code, he even seems like a well above average coder for a mechatronics engineer. But he is not afraid to "ask for help". For example, he's attached the dynamixel servomotor to the manipulator and he needs a ROS node to test it? "Just ask chat!" Sure he could do the work of writing the code himself, he's more than capable of it. But it isn't worth even using it to debug (and saving a morsel of your dignity by writing the base yourself), don't kid yourself thinking it is beneath you. Ask it for the whole node. And he does. And it works. And he saves his energy for the electronics. The same goes for linux-based errors too. Broken udev rules? Just ask chat! Need to stop the screen from switching off when the lid goes down? Just ask chat! While I can't/won't start using Chat GPT super liberally, it was definitely interesting looking at different philosophies of AI for coding.

[^footnote2]: Obviously I'm making assumptions here lol, this was what I got from watching him

## How Much Maths There Is in Everything

As a maths guy, I can't help but gush over how much math there is in everything. Even if it is abstracted away by libraries or into firmware. Let me list some of the cool things where having mathematical knowledge about the low-level implementation could help explain weird behaiour:
- Modelling orientation of a robot's end effector using Euler Angles/Roll, Pitch, Yaw 
- Modelling orientation of a robot's end effector using quaternion, the second iteration of the Cayley-Dickson construction
- Describing the distribution of mass of a mechanical component using the inertia tensor (and in IMUs)
- Standard dynamics to describe forces acting on components of the rover, and other things acting on the rover like torque, moments, second moments etc
- Using Lie groups such as SO(3) and SE(3) to model smooth movements of the manipulator in space
- Analyzing the bandwidth based of the frequency space a signal occupies using the Fourier Transform
    - The design of stable feedback systems in radio recievers uses the Laplace Transform
- Image and edge detection in computer vision uses a discrete convolution- which is different but related to the convolution theorem in signal processing
- Programming IK is a whole thing on its own- let $$p(x): \mathbb{R}^m \to \mathbb{R}^3$$, let $$p_0 = p(x_0)$$, and $$p_1 = p(x_0 + \Delta x)$$ be the goal position of the system. The goal of the system is to reduce the error given by $$\Vert p(x_0 + \Delta x_{estimate}) \Vert$$
- Transforms from joint to joint in a manipulator- are all just rigid body transforms (rotation and translation). These are mathematically affine transformations given by 4x4 homogenous transformation matrices
- Anytime anything moves, its velocity is a derivative, its acceleration is a second derivative and its accumulated movement is an integral- for example, in a PID controller
- SLAM uses a lot of probabilistic stuff, like the Kalman filter, and can use fuzzy logic for estimating where an object is (odometry validation)
- Lots more stuff I'm sure I'll remember and regret not writing later

## Looking After Yourself

There are some fundamental rules you must follow to ensure you have a good time and can actually be productive on the trip. This includes (this should be obvious) sleeping, eating, drinking and bathroom. Next in the hierarchy is showering and brushing your teeth (yeah I put it a tier below, I know what I'm doing) and third is psychological wellbeing and being happy. Try to get at least 8 hours per day. I just happen to be really high maintenance and need 9-10 per day, and had to make do with 3-5. Don't think I ever got more than 6. Don't be like that, especially if you are high maintenance. This means you cannot afford to stay up late scrolling and you cannot waste time on social media, delete those apps off your phone. If you have a roomamate like I did for Perthilaide, great they can keep you accountable. Otherwise (I was in my own room for this trip) it is on you.

This trip was so intense we basically had no time for grocery shopping on any day other than day 1. So make sure you have a list prepared beforehand of foods to stock up in the room and stuff you can take with you. Here are three lists of foods I bought that I either finished (or would have finished), didn't finish but should have and didn't open because it was dumb. I recommend getting these again.

#### Finished:
- Cup Noodles x3
- Oat Milk x1
- Uncle Toby's Oat Sachets x12
- Cans of Beans x2
- Cans of Beetroot x1
- Uncle Toby's Choc Chip Oat Bars x15
- 2L Bottled Water x2
- Bananas x8
- Pack of Shapes x1
- Plastic Bowl

#### Should Have Finished:
- Bag of Salad

#### Didn't Open:
- Chia Seeds
- Bottle of Salsa

#### Recommendations:
- Pita Bread Bag x1 (So you don't have to cut the bread)
- Tzatziki Dip x1
- Ready to eat Jasmine Rice x1 (although not sure what to eat this with)

If Brave:
- Pasta x1 (You will have to boil it in the kettle haha)
- Pasta Sauce x1
- Small Cheese x1
- Spanish Olives Jar x1

<br />

Last thing to remember: If you can go to the toilet please do.

### Don't Stay Up- It Isn't Worth It

While it is true you should be prepared to stay up and get very little sleep, please don't go out of your way to do so. Day #1 I literally stayed up until 2 finishing the robot visulization (fixing URDF) and I knew what I was getting myself into, even got a call from software TL saying "Don't stay up in case you were planning to!" and I literally did, despite promising not to. Don't do it, especially not on day #1. Maybe day #8 when everyone is getting ready to go. It ended up not being worth it (it never is!), it never got used because the manipulator failed. So there's that.

The metric for staying up shouldn't be how important it is- because everything is important. It should be: can I gurantee this will be needed tomorrow. If the answer is no, don't do it.

## Monash's Nova Rover

The Monash Nova Rover is the gold standard for all other teams. They didn't win this year- in fact, they didn't even win last year. But their team is huge, well established, organized and generally successful, so I wanted to talk a little about it. The Nova Rover is old- it is older than the ARC itself. Here are some points about what they do that is different to other teams.

#### They Don't Innovate

The Monash Nova Rover arm is (supposedly) the same arm that they have been using for the last 7 years, without any major changes. The rover competitions are not designed to have teams construct rovers from scratch, finish the comp and then discard them. The team has over a period of many years honed in on a design that is clearly successful at a lot of generic rover tasks, and they have kept (for the most part) subsystems as they are, because why fix something that's not broken? These systems took a lot of time to design and construct, and the Monash team simply does not risk making an untested change for a quick couple of points.

#### They Have a Monster Budget

Supposedly one of the things Monash prides itself on is its student teams, and this is reflected in their budgets. The motorsports comp has no budget caps on parts/construction and as a result, rumour has it their car is worth over 250k. While the rover itself has to be worth less than a certain amount (35k, I think) Monash's budget is clearly in the 6 figures as evidenced by the fact literally 60 of them booked flights on the uni's dime out to Adelaide for a week for the comp. It doesn't matter if they win or not, they clearly just have a different mindset and treat the competition very differently to us. For Monash, the ARC is a cultural event. Every year mechatronics students come together to study the rover as part of their degree. Not everyone actually gets the chance to make a difference, you are bound to have organizational inefficiencies with a team that big. But it doesn't matter, they use the competition as a teaching tool and it does a good job. The massive amounts of engagement (which are mandated btw) are just an excuse for the uni to funnel more into their student teams. Some people resent that. I personally just find it interesting, how various unis look at a comp like this.

#### They Have Hyperspecialized Teams

Having so many people grants a team certain luxuries. For instance, after the induction the entire 60-strong team never again made an appearance together. Each day of the comp a dedicated task-force of 10-15 people set up camp in the tent. This subteam would have consisted of a few key mechanical, electrical and software all-rounders, the subsystem TL and the team responsible for that day's task. This clears clutter and ensures that everyone on the field that day has a dedicated role and a way to help. In addition to the task subteams, such a huge number of members also allows for the creation of teams such as the:
- Comms team, that manages things such as networking, radio, bandwidth, camera feeds, latency, wifi etc.
- Business team, that manages budget, talking to the uni, acquiring sponsors, travel logisitcs, paperwork
- Media & Outreach team, that manages recruitment, promotional contnet, social media, celebrating wins

Maybe those subteams aren't real, but with a team that big you can start to see the use of allocating people those sorts of roles, and it is definitely something I am slightly jealous of.

## And Some Miscellaneous Other Things
- UTS doesn't spend much on tech societies, especially compared to other unis. Our entire budget for the year is about 15k, while Monash is 
- The uni is kind of broke- but they still spent about $1500 on just my accommo, flights and food
- UTS specifically is really careful around tech, they won't let us custom manufacture our own drivers for example (while other teams can), and they won't let us custom build our own batteries (while other teams can) and they won't let us drive to Adelaide unless we fill out a bunch of forms (while other teams can)
- Anyone can learn anything, especially young people (don't feel "locked in" to your degree, have a look around)
    - On this note -> the transition from software to embedded is a particularly easy one, and basically my entire team at work has transitioned from embedded to software which I would guess would be the harder of the two
- Try to decide where you are going to assemble the parts before you land
- Make sure everyone- everyone- in the team has read the ARC rules, especially the task descriptions
- Have a plan beforehand for who is in the base station and who is on the field, and if the rover is finished (well, ideally alongside development) have a plan for how the time will be split. This should be tested for reasonableness during DFT
- Bambulab A1 is the best beginner 3D printer
- Try to keep inventory, at least for your stuff (eg for me the base station)
- Keep walkie talkies for the base and field teams to communicate, and have charged batteries for them
- Have ethernet adapters on hand for laptops without ethernet ports

## Conclusion

I learn plenty from my time in Adelaide, these are some of the more outstanding/memorable things in my head right now, and I can't write everything (as much as I want to). I hope future UTS Rover students (myself included) read this and walk away knowing something that might make life easier for them down the track.