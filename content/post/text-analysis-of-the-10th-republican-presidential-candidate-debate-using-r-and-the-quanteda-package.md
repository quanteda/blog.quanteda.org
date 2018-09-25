On 25 February 2016, the tenth debate among the Republican candidates
for the 2016 Presidential election took place in Houston, Texas,
moderated by CNN. In this demonstration of the
[quanteda](https://github.com/kbenoit/quanteda) package, I will show how
to download, import, clean, parse by speaker, and analyze the debate by
speaker.

The first step involves loading the debate into R. To read this text, we
can use the **rvest** package, which will automatically extract text
from the [US Presidency Project
website](http://www.presidency.ucsb.edu/ws/index.php?pid=111634).

    library("rvest")

    ## Loading required package: xml2

    scraping <- read_html("http://www.presidency.ucsb.edu/ws/index.php?pid=111634")
    transcriptText <- scraping %>% html_nodes("p") %>% html_text()

To get only the text spoken by each candidate, we still need to remove
the non-text markers for events such as applause. We can do this using a
substitution of the text we wish to remove for the null string "", using
`gsub()`.

    # removes interjections
    transcriptText <- gsub("\\s*\\[(APPLAUSE|BELL RINGS|BELL RINGING|THE STAR-SPANGLED BANNER|COMMERCIAL BREAK|CROSSTALK|inaudible|LAUGHTER|CHEERING)\\]\\s*", 
        "", ignore.case = TRUE, transcriptText)

    cat(paste(substring(transcriptText, 1, 1000), substring(transcriptText, 5001, 
        5655), collapse = "\n"))

    ## MODERATOR:Wolf Blitzer (CNN); withPANELISTS:Maria Celeste Arrarás (Telemundo);Dana Bash (CNN); and Hugh Hewitt (Salem Radio Network) 
    ## BLITZER: We're live here at the University of Houston for the 10th Republican presidential debate. 
    ## An enthusiastic crowd is on hand here in the beautiful opera house at the Moore School of Music. Texas is the biggest prize next Tuesday, Super Tuesday, when 11 states vote, a day that will go a long way towards deciding who wins the Republican nomination. 
    ## We want to welcome our viewers in the United States and around the world. I'm Wolf Blitzer. This debate is airing on CNN, CNN International, and CNN in Espanol. It's also being seen on Telemundo and heard on the Salem Radio Network. Telemundo and Salem are our partners in this debate, along with the Republican National Committee. 
    ## We'd also like to welcome a very special guest with us here tonight. Ladies and gentlemen, the 41st president of the United States, George Herbert Walker Bush and former first lady Barbara Bush. 
    ## Everyone here is looking forward to a lively debate. I'll be your moderator tonight. Joining me in the questioning, Telemundo host Maria Celesta Arrasas, CNN Chief Political Correspondent Dana Bash; and Salem Radio Network's Hugh Hewitt, who worked in the Reagan administration for six years. 
    ## Tonight, there are five Republican candidates and they're ready to join us right now. 
    ## Please welcome Ohio Governor John Kasich. 
    ## Senator Ted Cruz of Texas. 
    ## Businessman and real estate developer Donald Trump. 
    ## Senator Marco Rubio of Florida. 
    ## And retired neurosurgeon, Dr. Ben Carson. 
    ## Ladies and gentlemen, welcome the Republican candidates for president of the United States. 
    ## BLITZER: Now please rise for our national anthem, performed by country music artist, Deana Carter. 
    ## [the national anthem is sung] 
    ## BLITZER: Thank you very much, Deana Carter. A beautiful, beautiful national anthem. 
    ## The final debate before Super Tuesday begins right after this quick break.  
    ##  
    ## BLITZER: Welcome back to the University of Houston and the Republican presidential debate. 
    ## The candidates, they are now in place. Their positions were selected based on their standing in the delegate race through Nevada, with the top candidate in the center and the others extending outward. 
    ## I want to tell you how tonight's debate will work. As moderator, I will guide the discussion, asking questions and follow-ups, as will Maria Celeste, Dana Bash and Hugh Hewitt. Candidates, you'll have a minute and 15 seconds to answer and 30 seconds for follow-ups and rebuttals. We have timing lights that are visible to the candidates. Those lights will warn you when your time is up, and as the candidates requested, a bell will sound like this. 
    ##  
    ## We know you all want to jump and debate these critically important issues, but please wait until you're called on. These are the rules all of the candidates have agreed to. 
    ## It's time for the candidates to introduce themselves right now. You'll each have 30 seconds. Dr. Carson, you're first. 
    ## CARSON: If someone had tried to describe today's America to you 30 years ago, you would have listened in disbelief. Americans know that our nation is heading off the abyss of destruction, secondary to divisiveness, fiscal irresponsibility, and failure to lead. 
    ## Marco, Donald, Ted, John, we will not solve any of these problems by trying to destroy each other. What we need to do is be looking for solutions tonight. It's not about us, it's about the American people. 
    ## BLITZER: Governor Kasich? 
    ## KASICH: Well, you know, on the way over here, even getting ready earlier and sitting in the green room and watching the early coverage, you know, my father carried mail on his back and his father was a coal miner and my mother's mother was an immigrant, could barely speak English. And I'm standing on this stage. It's pretty remarkable. But I want to tell you, there's a lot of young people watching tonight. You can do whatever you want to do in your life. America is an amazing country, where a kid like me can grow up to run for president of the United States and be on this stage tonight. So to all the young people that are out there, your hopes, your dreams, pursue them. Shoot for the stars. America's great, and you can do it. Thank you, Wolf. 
    ## BLITZER: Senator Rubio? 
    ## RUBIO: Well, thank you. This election, we have to decide the identity of America in the 2ist century, but as part of this primary, we have to find out our identity as a party and as a movement. 
    ## Thirty-six years ago, Ronald Reagan and George H.W. Bush began the Reagan Revolution. For a generation, they defined conservatism as limited government and free enterprise and a strong national defense. But they also appealed to our hopes and our dreams. Now we have to decide if we are still that kind of party and still that kind of movement, or if we're simply going to become a party that preys on people's angers and fears. 
    ## I hope we remain that conservative movement that appeals to our hopes and our dreams and the belief that America will always be better in its future than it's been in its story history. 
    ## BLITZER: Senator Cruz? 
    ## CRUZ: Welcome to Texas. 
    ## Here, Texas provided my family with hope. Here, my mom became the first in her family ever to go to college. Here, my dad fled Cuba and washed dishes, making 50 cents an hour to pay his way through the University of Texas. I graduated from high school at Second Baptist not too far away from here.  
    ## When I ran for Senate, I promised 27 million Texans I would fight for you every day, and not for the Washington bosses. 
    ## And, I'll tell you, as I travel the state, Democrats tell me I didn't vote for you, but you're doing what you said you would do. And, as president, I will do the same. 
    ## BLITZER: Mr. Trump. 
    ## TRUMP: Thank you. My whole theme is make America great again. We don't win anymore as a country. We don't win with trade, we don't win with the military. ISIS, we can't even knock out ISIS, and we will, believe me. We will. 
    ## We don't win in any capacity with healthcare. We have terrible health care, Obamacare is going to be repealed and replaced. We just don't win. 
    ## You look at our borders, they're like swiss cheese, everybody pours in. 
    ## We're going to make a great country again. We're going to start winning again. We're going to win a lot, it's going to be a big difference, believe me. It's going to be a big difference. 
    ## BLITZER: Thank you very much. It's now time to begin questions. Voters in the first four states have spoken, and Mr. Trump has emerged as the frontrunner, but in five days the candidates will face their biggest test yet, Super Tuesday. When nearly half of the delegates needed to win the Republican nomination will be awarded, and the biggest prize of the night is Texas. 
    ## Immigration is a key issue in this state, for all voters nationwide, including the many people watching us on Telemundo. So, that's where we begin. 
    ## Mr. Trump, you've called for a deportation force to remove the 11 million undocumented immigrants from the United States. You've also promised to let what you call, "the good ones", come back in. Your words, "the good ones", after they've been deported. 
    ## Senator Cruz would not allow them to come back in. He says that's the biggest difference between the two of you. He calls your plan amnesty. Is it? 
    ## TRUMP: First of all, he was in charge of amnesty, he was the leader, and you can ask Marco because they've been debating this every debate that we've had. 
    ## As far as coming back in, number one, you wouldn't even be talking, and you wouldn't have asked that as the first question if it weren't for me when my opening when I talked about illegals immigration. It wouldn't even be a big subject. 
    ## But, we either have a country, or we don't have a country. We have at least 11 million people in this country that came in illegally. They will go out. They will come back — some will come back, the best, through a process. They have to come back legally. They have to come back through a process, and it may not be a very quick process, but I think that's very fair, and very fine. 
    ## They're going to get in line with other people. The best of them will come back, but they're going to come back through a process. 
    ## BLITZER: Senator Cruz, what's wrong with letting what Mr. Trump calls, "the good ones" come back to the United States? 
    ## CRUZ: You know, the people that get forgotten in this debate over immigration are the hardworking men and women of this country — our millions of Americans who are losing their jobs. Millions of legal immigrants who are losing their jobs are seeing their wages driven down. 
    ## You know, in the past couple of weeks the Wall Street Journal had a very interesting article about the state of Arizona. Arizona put in very tough laws on illegal immigration, and the result was illegal immigrants fled the state, and what's happened there — it was a very interesting article. 
    ## Some of the business owners complained that the wages they had to pay workers went up, and from their perspective that was a bad thing. But, what the state of Arizona has seen is the dollars they're spending on welfare, on prisons, and education, all of those have dropped by hundreds of millions of dollars. And, the Americans, and for that matter, the legal immigrants who are in Arizona, are seeing unemployment drop are seeing wages rise. That's who we need to be fighting for. 
    ## Listen, we have always welcomed legal immigrants, but I think it is a mistake to forgive those who break the law to allow them to become U.S. citizens, and that's why I've led the fight against granting citizenship to those here illegally, and that's why I will do the same thing as president. 
    ## BLITZER: Mr. Trump, do you want to respond to that?  
    ## TRUMP: Well, I'm very glad that Ted mentioned Arizona because probably the toughest man on borders is Sheriff Joe Arpaio, and two days ago he totally endorsed me, so, thank you. 
    ## BLITZER: Senator Rubio? 
    ## RUBIO: Senator Cruz has called your immigration plan amnesty, and has an add out there comparing it to President Obama's. He says both of you support allowing undocumented immigrants legal status here in the United States after a background check, paying a fine, and paying taxes. 
    ## Are those claims correct? 
    ## RUBIO: Well, first of all, and before we do anything, I've been abundantly clear on this. When I'm president of the United States, before we do anything on immigration, we are going to secure the border. And, that's not just the physical border with Mexico, it's Visa overstays. That's 45 percent of the problem right there. 
    ## It also has to do — that's why we need e-verify, and entry-exit tracking system, and so-forth. And, until that happens, we're not doing anything else. And then we'll see what the American people are willing to support. 
    ## And Donald mentioned, because he mentioned me in his answer, that his position on immigration is what has driven this debate. Well, the truth is, though, that a lot of these positions that he's now taking are new to him. 
    ## In 2011, he talked about the need for a pathway to citizenship. In 2012, Donald criticized Mitt Romney, saying that Mitt lost his election because of self-deportation. 
    ## And so even today, we saw a report in one of the newspapers that Donald, you've hired a significant number of people from other countries to take jobs that Americans could have filled. 
    ## My mom and dad — my mom was a maid at a hotel, and instead of hiring an American like her, you have brought in over a thousand people from all over the world to fill those jobs instead. 
    ## So I think this is an important issue. And I think we are realizing increasingly that it's an important issue for the country that has been debated for 30 years, but finally needs to be solved once and for all. 
    ## BLITZER: Mr. Trump? 
    ## TRUMP: Well, first of all, self-deportation is people are going to leave as soon as they see others going out. If you look at Dwight Eisenhower in the 1950s, they started moving people out and the rest of them left. 
    ## Self-deportation, as I really define it, and that's the way I define it, is you're going to get some to go, and the rest are going to go out. 
    ## As far as the people that I've hired in various parts of Florida during the absolute prime season, like Palm Beach and other locations, you could not get help. It's the up season. People didn't want to have part-time jobs. There were part-time jobs, very seasonal, 90-day jobs, 120-day jobs, and you couldn't get. 
    ## Everybody agrees with me on that. They were part-time jobs. You needed them, or we just might as well close the doors, because you couldn't get help in those hot, hot sections of Florida. 
    ## RUBIO: That — my point that I made was you had criticized Mitt Romney for self-deportation. You said that his strategy of self- deportation is why he lost the election. 
    ## And I think people in Florida would be surprised, because, in fact, the article that was today, they interviewed a number of people that would have been willing to do those jobs, if you would have been willing to hire them to do it. 
    ## TRUMP: I criticized Mitt Romney for losing the election. He should have won that election. He had a failed president. He ran a terrible campaign. He was a terrible candidate. That's what I criticize Mitt Romney — I mean, ran... 
    ## RUBIO: No, he... 
    ## TRUMP: Excuse me. He ran one terrible campaign. That's an election that should have been won. 
    ## RUBIO: Well, in fact, I agree we should have won and I wished we would have, but, in fact, you did criticize him for using the term "self-deportation." I mean, that's on the record and people can look it up right now online. 
    ## But, again, I just want to reiterate, I think it's really important, this point. I think it's fine, it's an important point that you raise and we discuss on immigration. This is a big issue for Texas, a huge issue for the country. 
    ## But I also think that if you're going to claim that you're the only one that lifted this into the campaign, that you acknowledge that, for example, you're only person on this stage that has ever been fined for hiring people to work on your projects illegally. 
    ## You hired some workers from Poland... 
    ## TRUMP: No, no, I'm the only one on the stage that's hired people. You haven't hired anybody. 
    ## RUBIO: In fact, some of the people... 
    ##  
    ## TRUMP: And by the way, I've hired — and by the way, I've hired tens of thousands of people over at my job. You've hired nobody. 
    ## RUBIO: Yes, you've hired a thousand from another country... 
    ## TRUMP: You've had nothing but problems with your credit cards, et cetera. So don't tell me about that. 
    ## RUBIO: Let me just say — let me finish the statement. This is important. 
    ## TRUMP: You haven't hired one person, you liar. 
    ## RUBIO: He hired workers from Poland. And he had to pay a million dollars or so in a judgment from... 
    ## TRUMP: That's wrong. That's wrong. Totally wrong. 
    ## RUBIO: That's a fact. People can look it up. I'm sure people are Googling it right now. Look it up. "Trump Polish workers," you'll see a million dollars for hiring illegal workers on one of his projects. He did it.That happened. 
    ## TRUMP: I've hired tens of thousands of people over my lifetime. Tens of thousands... 
    ## RUBIO: Many from other countries instead of hiring Americans. 
    ## TRUMP: Be quiet. Just be quiet.Let me talk. I've hired tens of thousands of people. He brings up something from 30 years ago, it worked out very well. Everybody was happy. 
    ## RUBIO: You paid a million dollars. 
    ## TRUMP: And by the way, the laws were totally different. That was a whole different world. 
    ## BLITZER: Thank you. 
    ## TRUMP: But I've hired people. Nobody up here has hired anybody. 
    ## BLITZER: Senator Cruz, you say you want to deport the 11 million undocumented immigrants, but you never want to allow them to come back to the United States. What would happen to the children who are U.S.- born citizens whose parent will be deported under your plan? 
    ## CRUZ: Well, existing law provides that those who are deported cannot come back here legally. U.S. citizens can come back. That's existing law. 
    ## But let me say, Wolf, I really find it amazing that Donald believes that he is the one who discovered the issue of illegal immigration. I can tell you, when I ran for Senate here in the state of Texas, I ran promising to lead the fight against amnesty, promising to fight to build a wall. And in 2013, when I was fight against the "gang of eight" amnesty bill, where was Donald? He was firing Dennis Rodman on "Celebrity Apprentice." 
    ## And indeed, if you look at the "gang of eight," one individual on this stage broke his promise to the men and women who elected him and wrote the amnesty bill. 
    ## If you look at the eight members of the Gang of Eight, Donald gave over $50,000 to three Democrats and two Republicans. And when you're funding open border politicians, you shouldn't be surprised when they fight for open borders. 
    ## And I think if you want to know who actually will secure the borders and follow through, you ought to ask who has a record before they were a candidate for president of fighting to secure the borders and stop amnesty. And I'm the only one on this stage that has that record. And by the way, Marco is exactly right that a federal court found Donald guilty of being part of a conspiracy to hire people illegally and entered a $1 million judgment against him. 
    ## BLITZER: Mr. Trump? 
    ## TRUMP: I can only say this, and I've said it loud and clear and I've said it for years. And many of these people are sitting right in the audience right now — your lobbyist and your special interest and your donors, because the audience is packed with them, and they're packed with you. 
    ## I've had an amazing relationship with politicians — with politicians both Democrat, Republican, because I was a businessman. As one magazine said, he's a world-class businessman; he was friendly with everybody. I got along with everybody. 
    ## You get along with nobody. You don't have one Republican — you don't have one Republican senator, and you work with them every day of your life, although you skipped a lot of time. These are minor details. But you don't have one Republican senator backing you; not one. You don't have the endorsement of one Republican senator and you work with these people. You should be ashamed of yourself. 
    ## BLITZER: Senator Cruz? 
    ## CRUZ: You know, I actually think Donald is right. He is promising if he's elected he will go and cut deals in Washington. And he's right. He has supported — he has given hundreds of thousands of dollars to Democrats. Anyone who really cared about illegal immigration wouldn't be hiring illegal immigrants. Anyone who really cared about illegal immigration wouldn't be funding Harry Reid and Nancy Pelosi; wouldn't be funding the Gang of Eight. And, you know, he is right. When you stand up to Washington, when you honor the promise you made to the men and women who elected you and say enough with the corruption, enough with the cronyism, let's actually stand for the working men and women of this country, Washington doesn't like it. 
    ## And Donald, if you want to be liked in Washington, that's not a good attribute for a president. 
    ##  
    ## TRUMP: Here's a man — Robin Hood. This is Robin Hood over here. He talks about corruption. On his financial disclosure form, he didn't even put that he's borrowed money from Citibank and from Goldman Sachs, which is a total violation. He didn't talk about the fact that he pays almost no interest. He just left it off, and now he's going to protect the people from the big bad banks. 
    ## Give me a break. 
    ## BLITZER: All right. We're going to move on to Governor Kasich. 
    ## Governor Kasich... 
    ##  
    ## CRUZ: Wolf, can I respond to that attack? 
    ## BLITZER: You can respond, but let me get Governor Kasich in. He's been waiting patiently. 
    ## Governor Kasich, the idea — you've said this, and I want to quote you now: "The idea that we're going to deport all these people is ludicrous and everybody knows it." Those are your words. Should people be allowed to break the law just because it's not feasible to stop them? 
    ## KASICH: Look, we have a great president here, George Bush, the 41st president of the United States. He worked with Ronald Reagan to pass an effort to try to solve this problem — a path to legalization. You see, that was a time when things worked. It was a time when President Reagan and George Bush decided that we needed to make the country work. 
    ## Look, I think there is an answer here. The answer is you complete the border. You let people know that once it's done, you don't have a right to come in. If you come in, we don't want any excuse. You're going to go back. But I favor a guest worker program. I think it's practical. And I think for the 11 million or 11.5 million Americans — the illegals that are here, if they have not committed a crime since they've been here, I'd make them pay a fine, some back taxes, maybe some community service. And at the end, I'd give them a path to legalization, but not a path to citizenship. I don't think we're going to tear families apart. I don't think we're going to ride around in people's neighborhoods and grab people out of their homes. I don't think — first of all, I don't think it's practical and I don't think it reflects America. 
    ## You know what happened? The problem with President Reagan is we didn't get in there and actually finish the border. And I think it was probably business interests that affected it. But at the end of the day, let's be practical. Let's start solving problems in this country instead of kicking them upstairs. With President Reagan and George Bush, it was a bipartisan coalition to address the issue, and I think we can and should do it again. And I will have a plan in the first 100 days to get it done and get this issue behind us. 
    ## BLITZER: Thank you, Governor. 
    ## Dr. Carson, you've been critical of mass deportation. You said back in November you don't think Mr. Trump's plan necessarily represents the Republican Party. Given how well Mr. Trump has been doing with the Republican primary voters, do you still believe that? 
    ## CARSON: I believe in liberty and justice for all. I think everything that we do should be fair. And I've already described — you know, how we can secure the border. 
    ## We need to secure all the borders, because it's not just people coming in from South America and Mexico, but there are terrorists who want to destroy us, who are getting across our borders fairly easily. And we have to stop that. 
    ## But in terms of the people who are here already, after we — after we stop the illegal immigration, we need to be reasonable. And I would give them a six-month period in which to get registered as a guest worker, assuming that they have an acceptable record. 
    ## They have to pay a back-tax penalty, have to pay taxes going forward, but they don't have to live underground anymore. And I think they do not become American citizens, they do not vote. 
    ## If they want to become an American citizen, they go through exactly the same process that anybody else goes through. I think that's the kind of situation that is actually fair to people. 
    ## And we have other ways of — of utilizing our facilities and our talents as foreign aid: doing things in South America and Central America and Mexico that improve the economy there, so that they don't feel the need to come over here. That would cost us a lot less than borrowing money from China, paying interest on it. 
    ## BLITZER: Thank you, Dr. Carson. 
    ## Mr. Trump, your campaign, as you well remember, began with the idea of building a wall along the southern border. 
    ## TRUMP:. 
    ## BLITZER: It's about 315 miles southwest of where we are right now. You've said the Mexican government will pay for it. 
    ## TRUMP: Correct. 
    ## BLITZER: The spokesperson for the current president of Mexico says that will never happen. The last two presidents of Mexico say that will never happen. In fact, the former president of Mexico, Vicente Fox — he said today, and I'm quoting him — he said, "I'm not going to pay for that," quote, "effing wall." 
    ## So if you don't get an actual check from the Mexican government for $8 billion or $10 billion or $12 billion, whatever it will cost, how are you going to make them pay for the wall? 
    ## TRUMP: I will, and the wall just got 10 feet taller, believe me. 
    ## It just got 10 feet taller. I saw him make that — I saw him make the statement. I saw him use the word that he used. I can only tell you, if I would have used even half of that word, it would have been national scandal. 
    ## This guy used a filthy, disgusting word on television, and he should be ashamed of himself, and he should apologize, OK? Number one. Number two, we have a trade deficit with Mexico of $58 billion a year. And that doesn't include all the drugs that are pouring across and destroying our country. 
    ## We're going to make them pay for that wall. Now, the wall is $10 billion to $12 billion, if I do it. If these guys do it, it'll end up costing $200 billion. 
    ## But the wall is $10 billion to $12 billion. You need 1,000 — you need 1,000 miles. The Great Wall of China, built 2,000 years ago — 2,000, is 13,000 miles. We need 1,000, because we have a lot of natural barriers. 
    ## We can do it for $10 billion to $12 billion, and it's a real wall. This is a wall that's a heck of a lot higher than the ceiling you're looking at. This is a wall that's going to work. 
    ## Mexico will pay for it, because they are not doing us any favors. They could stop all of this illegal trade if they wanted to...... immediately. Mexico will pay for the wall. It's a small portion of the kind of money that we lose and the deficits that we have with Mexico. 
    ## BLITZER: If the — if the Mexicans don't pay for the wall, will you start a trade war with Mexico? 
    ## TRUMP: Well, you know, I don't mind trade wars when we're losing $58 billion a year, you want to know the truth. We're losing so much. 
    ## We're losing so much with Mexico and China — with China, we're losing $500 billion a year. And then people say, "don't we want to trade?" I don't mind trading, but I don't want to lose $500 billion. I don't want to lose $58 billion. 
    ## Mexico just took Carrier Corporation, maker of air conditioners. They just took Ford. They're building a $2.5 billion plant. They just took Nabisco out of Chicago. 
    ## And I always say I'm not having Oreos anymore, which is true, by the way. But they just took a big plant from Nabisco into Mexico. They're taking our businesses. I don't mind. 
    ## BLITZER: Thank you. Senator Rubio? 
    ## RUBIO: Yeah, a couple points. If he builds the wall the way he built Trump Towers, he'll be using illegal immigrant labor to do it. The second... 
    ## TRUMP: Such a cute sound bite. 
    ## RUBIO: But it — no, it's not a sound bite. It's a fact. Again, go online and Google it. Donald Trump, Polish workers. You'll see it. 
    ## The second thing, about the trade war — I don't understand, because your ties and the clothes you make is made in Mexico and in China. So you're gonna be starting a trade war against your own ties and your own suits. 
    ## TRUMP: All right, you know what? 
    ## RUBIO: Why don't you make them in America? 
    ## TRUMP: Because they devalue their currency — they devalue their currencies... 
    ## RUBIO: Well, then make them in America. 
    ## TRUMP: ... that makes it — well, you don't know a thing about business. You lose on everything... 
    ## RUBIO: Well, make them in America.  
    ## TRUMP: Let me just tell you — they de-value their currency. They de-value their currencies. 
    ## RUBIO: Well then, make them in America. 
    ## TRUMP: That makes it — well, you don't know a thing about business. You lose on everything you do. 
    ## RUBIO: Well, make them in America. 
    ## TRUMP: Let me just tell you, they de-value their currencies. China, Mexico, everybody. Japan with the cars. They de-value their currencies to such an extent that our businesses cannot compete with them, our workers lose their jobs... 
    ## RUBIO: And so you make them in China and in Russia. 
    ## TRUMP: But you wouldn't know anything about it because you're a lousy businessman. 
    ## RUBIO: Well, I don't know anything about bankrupting four companies. You've bankrupted.. 
    ## TRUMP: No, I — and you know why? You know why? 
    ## RUBIO: I don't know anything about... 
    ## TRUMP: You know why? 
    ## RUBIO: ... starting a university, and that was a fake university. 
    ## BLITZER: One at a time. 
    ## TRUMP: First of all... 
    ## BLITZER: One at a time. 
    ## TRUMP: ... first of all, that's called a... 
    ## RUBIO: There are people who borrowed $36,000... 
    ## BLITZER: Hold on. One at a time, Mr. Trump. 
    ## RUBIO: ... to go to Trump University, and they're suing now — $36,000 to go to a university... 
    ## TRUMP: And by the way — and by the way... 
    ## RUBIO: ... that's a fake school. 
    ## TRUMP: ... and by the way... 
    ## RUBIO: And you know what they got? They got to take a picture with a cardboard cutout of Donald Trump... 
    ## TRUMP: ... I've won most of the lawsuits. 
    ## RUBIO: That's what they got for $36,000. 
    ## BLITZER: All right, I want to move on. 
    ## TRUMP: And they actually did a very good job, but I've won most of the lawsuits. 
    ## BLITZER: Mr. Trump, Senator, I want to bring in... 
    ## RUBIO: Most of the lawsuits. 
    ## BLITZER: ... I want to bring in my colleague Maria Celeste. 
    ## TRUMP: Excuse me. Hey Wolf, let me ask you. Am I allowed to respond to this? 
    ## BLITZER: You're allowed — you've been responding. 
    ## TRUMP: OK. Well let — no, I haven't. I really haven't. 
    ## RUBIO: He's talked through the whole thing. 
    ## TRUMP: Here's a guy — here's a guy that buys a house for $179,000, he sells it to a lobbyist who's probably here for $380,000 and then legislation is passed. You tell me about this guy. This is what we're going to have as president. 
    ## RUBIO: Here's a guy that inherited $200 million. If he hadn't inherited $200 million, you know where Donald Trump would be right now? 
    ## TRUMP: No, no, no. 
    ## RUBIO: Selling watches in Manhattan. 
    ## TRUMP:I took... 
    ## RUBIO: That's where he would be.  
    ## TRUMP: That is so wrong. We'll work on that. I took $1 million and I turned into $10 billion. 
    ## RUBIO: Oh, OK. One million. 
    ## TRUMP: I borrowed $1 million... 
    ## RUBIO: Better release your tax returns so we can see how much money he made. 
    ## TRUMP: I borrowed $1 million, I turned it into $10 billion... 
    ## RUBIO: Oh, he doesn't make that money. 
    ## TRUMP: ... more than $10 billion. 
    ## BLITZER: Thank you. Thank you. I want to bring in Maria Celeste Arrarás of Telemundo. Maria? 
    ## ARRARÁS: Senator Rubio, last week, you said that on your first day in office, you will get rid of President Obama's executive action known as Deferred Action for Childhood Arrivals, DACA for short. 
    ## RUBIO: Correct. 
    ## ARRARÁS: It is a program that has protected hundreds of thousands of young people that came here when they were children, brought to the U.S. by undocumented immigrants. This is the only home they know, and that is a dramatic change from last April when you said in Spanish, and I'm going to quote you [in Spanish] which translates to DACA is going to have to end at some point, but it wouldn't be fair to cancel it immediately. 
    ## So Senator Rubio, what changed? 
    ## RUBIO: It didn't change. 
    ## ARRARÁS: Why is it now fair to cancel it on Day One? 
    ## RUBIO: No, it's the same policy. It will have to end at some moment, and as I said, we will — we will eliminate that executive order. The people that are on it now will not be allowed to renew it, and new applicants will not be allowed to apply to it. And it's not because we're not compassionate to the plight of a 2 — someone who came here when they were 2 years old. I understand. I know people that are personally impacted by this. 
    ## The problem with the executive order is it is unconstitutional. The president doesn't have the power to do that. 
    ## And he himself admitted that. 
    ## ARRARÁS: Senator, Senator... 
    ## RUBIO: I'm sorry, but let me finish my... 
    ## ARRARÁS: ... but you went — you went from saying that it was deeply disruptive to deport them immediately to deport them on Day One. 
    ## RUBIO: No, but this is not about deportation. Everybody always goes immediately to the issue of deportation. This is about DACA. DACA is an executive order that is unconstitutional. I will cancel it on my first day in office, which means people who currently hold those permits will not be allowed to renew them when they expire, and new people will not be allowed to apply for them. 
    ## Now, I am sympathetic to the plight of someone who came here when they were 2 or 3 years old through no fault of their own, but you can't solve it doing something that is unconstitutional. No matter how sympathetic we may be to a cause, we cannot violate the Constitution of the United States the way this president now does on a regular basis. 
    ## ARRARÁS: Senator Rubio, you accused Senator Cruz in a previous debate of lying when he said that you said one thing in Spanish and another one in English. So in what sense did he lie? 
    ## RUBIO: Because it is not true that I'm not going to get rid of DACA. I am going to get rid of DACA. In the Spanish interview, you just read out the transcript in Spanish, I said, it will have to end at some point. That point will be when I eliminate the executive order and the people who have those permits when they expire will not be allowed to renew it. And new people will not be able to apply. In fact, I don't even think we should be taking new enrollees in the program now. 
    ## That is how the program ends and how you wind it down is you allow the people who are on it, when the program expires, they cannot renew it, and it goes away. But I will cancel the executive order as soon as I take — as soon as I step foot into the Oval Office. 
    ## TRUMP: I have to say, he lied this time. He lied. 100 percent. 100 percent. 
    ## RUBIO: You lied about the Polish workers. 
    ## TRUMP: Yes, yes, yes. 38 years ago. 
    ## RUBIO: You lied to the students at Trump University. 
    ## ARRARÁS: Let Senator Cruz jump in. 
    ## RUBIO: Oh, he lied 38 years ago. All right, I guess there's a statute of limitation on lies. [laughter and applause]  
    ## CRUZ: Well Maria, I would note you made the exact same point here that I made at the last debate, and you're right that Senator Rubio called me a liar for saying that.  
    ## You know, we've both seen at home when Washington politicians say about an illegal, or unconstitutional program. Well, it'll have to end some day, not immediately, but someday in the future. 
    ## That, inevitably, is when a politician doesn't plan to end it at all. 
    ## You know, I'm reminded of that that is the same position that Marco took in Iowa on ethanol subsidies. When I campaigned in Iowa, I took on the lobbyists, took on the corporate welfare and said we should have no ethanol subsidies. 
    ## Marco's position was the same as it is to illegal amnesty. Well, someday it should end, just not now. And, frankly, I think we need a president who knows what he believes in, is willing to say it on day one, not at the end of his term when it's somebody else's problem. 
    ## RUBIO: That's not an accurate assessment of what I said about ethanol. What I said is that ethanol will phase out, it is phasing out now. By 2022 that program expires by virtue of the existing law, and at that point it will go away. I don't agree with the mandate and the program that's in place, but I think it's unfair that these people have gone out and invested all this money into this program and we're just going to yank it away from them. 
    ## And, again, you read the statement in Spanish. I said very clearly on Spanish television, DACA will have to end at some point, and that point is — at that time I was not a candidate for president. I said it will end in my first day in office as president, and the people who have it now will not be able to renew it. New applicants will not be able to apply. That is the end of DACA. 
    ## I am sympathetic to this cause, but once again, it cannot supersede the Constitution of the United States which this president habitually and routinely every single day ignores and violates. [cheering and applause] 
    ## ARRARÁS: Senator Cruz, you and Senator Rubio are the two candidates of hispanic descent on this stage. As a matter of fact, you are the first hispanic candidate ever to win a caucus or primary. 
    ## And yet, there is the perception in the Latino community that instead of trying to prove to Latinos who has the best plan, the best platform to help them, that you two are spending the time arguing with each other. Trying to figure out which one is tougher on immigration in order to appeal to the majority of Republicans. 
    ## So, my question to you is are you missing a huge opportunity to expand the Republican base? 
    ## CRUZ: Well, Maria, you are right. It is extraordinary that of five people standing on this stage that two of us are the children of Cuban immigrants. It really is the embodiment of the incredible opportunity and promise this nation provides. 
    ## You know, I would note that a lot of folks in the media have a definition of hispanics that you can only be hispanic if you're liberal. That makes sense in the media, but I gotta tell you, one of the things I was most proud of when I ran for Senate here in Texas, I earned 40 percent of the hispanic vote here in Texas. 
    ## At the same time, Mitt Romney was getting clobbered with 27 percent of the hispanic vote nationwide. And, the reason is, as you know, you look at the value sin the hispanic community. The values in our community are faith, family, patriotism. 
    ## You know, we've got the highest rate of military enlistment among hispanics in any demographic in this country. And, when I campaigned, and I campaigned the same here in Houston or Dallas as I did in the Rio Grande Valley, defending conservative principles, defending judeo- Christian principals, telling my father's story. 
    ## Telling my Dad's story of coming to America with $100 dollars in his underwear, not speaking English, washing dishes, having hopes and dreams for the American dream. And, the truth is the Obama-Clinton economy has done enormous damage to the hispanic community. It is not working in the hispanic community, and I......fighting so that everyone who is struggling in the hispanic community and beyond will have a fair and even shake at the American dream. 
    ## RUBIO: I'm sorry I was mentioned... 
    ## ARRARÁS: ... Governor Kasich. 
    ## RUBIO: Maria I was mentioned in that. I was mentioned in that statement. 
    ## ARRARÁS: Governor Kasich, after the... 
    ## RUBIO: ... OK. I was mentioned — just because of the hispanic — and I'll be brief. 
    ## A couple points, number one, I do think it's amazing that on this stage tonight there are two descendants of Cuban origin, and an African American. We are the party of diversity, not the Democratic party. [cheering and applause]  
    ## And, the second point I would make is that we have to move past this idea that somehow the hispanic community only cares about immigration. 
    ## Yes, it's an important issue because we know and love people that have been impacted by it. But, I'm going to tell you that the most powerful sentiment in the hispanic community, as it is in every immigrant community, is the burning desire to leave your children better off than yourself......and, you can only do that through free enterprise. That's what we stand for, not socialism like Bernie Sanders, and increasingly Hillary Clinton. 
    ## ARRARÁS: Governor Kasich, after the last presidential election the Republican party realized that in order to win the presidency it needed the support of latinos. Guidelines as to how to accomplish that were spelled out in an autopsy report that concluded, and I'm going to quote it, "if hispanic Americans hear that the GOP doesn't want them in the United States they won't pay attention to our next sentence." 
    ## So, do you think that your fellow Republican candidates get it? 
    ## KASICH: Well, I'm not going to talk about that. I mean, I've got to tell you, I was with this little 12-year-old girl, was at a town hall meeting, and she said, you know, I don't like all this yelling and screaming at the debates. My mother's thinking I might not be able to watch the thing anymore. 
    ## I think we ought to move beyond that, about what they think. I'm going to tell you what I think. My position on this whole immigration issue has been clear from the beginning. I haven't changed anything with it. 
    ## And, look, my view is, we need economic growth. Everything starts with economic growth. And how do you get it? Common sense regulations, lower taxes for both business and individuals, and, of course, a fiscal plan that balances the budget. 
    ## That gives you economic growth. I did it when I was in Washington, as the Budget Committee chairman, negotiating actually with Democrats, that gave us surpluses, economic growth, and the same thing in Ohio. 
    ## But here's the thing that I believe. Economic growth is not an end unto itself. We have to make sure that everybody has a sense that they can rise. 
    ## Of course, our friends in the Hispanic community, our friends in the African-American community, the promise of America is that our system, when we follow the right formula, is going to give opportunity for everyone. 
    ## It's what Jack Kemp used to say. A rising tide lifts all boats, not just some boats, but all boats. And you know what? With me and the Hispanic community, I think they like me. And I appreciate that, because I want them to have the same opportunity that I and my children and my wife and the people we love have had in this country. 
    ## It's time to solve problems. 
    ## ARRARÁS: Dr. Carson, concerning this recommendation of the report, are you, as a candidate, getting it? 
    ## CARSON: I didn't hear the first part of the question? 
    ## ARRARÁS: The first part of the question is, there was a report that recommended that in order to approach Hispanics and bring them to vote for the Republican Party, certain things needed to happen. 
    ## And one of them was that they shouldn't feel like they were going to get kicked out of the United States, otherwise they wouldn't pay attention to one more sentence from candidates. 
    ## CARSON: OK, well, first of all, let me just mention that last year at the NALEO, the National Association for Latino Elected Officials, I was the only one of 17 Republican candidates to go there. 
    ## And the reason that I don't fear going to an organization like that is because the message that I give is the same message to every group. You know, this is America. And we need to have policies that are — that give liberty and justice to all people. 
    ## And that's the way that I have fashioned virtually every policy, looking at that. And I think that's the way the Republican Party generally thinks. We don't pick and choose winners and losers. We are compassionate. 
    ## But real compassion is providing people with a ladder of opportunity to climb up from a state of dependence and become part of the fabric of America. When we begin to emphasize that, I think we will attract everybody. 
    ## ARRARÁS: Mr. Trump, it is common knowledge that the Hispanic vote is very important in this race. You keep saying that Hispanics love you. 
    ## TRUMP: True. 
    ## ARRARÁS: And, yes, you won the Hispanic vote in Nevada. 
    ## TRUMP: True. 
    ## ARRARÁS: But a brand new Telemundo poll says that three out of four Hispanics that vote nationwide have a negative opinion of you. They don't like you. Wouldn't that make you an unelectable... 
    ## TRUMP: No. 
    ## ARRARÁS: ... candidate in a general election? 
    ## TRUMP: First of all, I don't believe anything Telemundo says. 
    ## ARRARÁS: You used to say that you love... 
    ## TRUMP: Number one. Number two, I currently employ thousands of Hispanics, and over the years, I've employed tens of thousands of Hispanics. They're incredible people. They know, and the reason I won in Nevada, not only won the big one, but I also won subs, like, as an example, I won with women. 
    ## I won with every single category. I won with men, I won with high-income, low-income, I won with Hispanics. And I got 46 percent. Nobody else was close. Because they know I'm going to bring jobs back from China, from Japan, from so many other places. 
    ## They get it. They're incredible people. They're incredible workers. They get it. And I've won many of the polls with Hispanics. I didn't maybe win the Telemundo poll. 
    ## But one thing I'm also going to do, I'm going to be getting — bringing a lot of people in who are Democrats, who are independents, and you're seeing that with the polls, because if you look at anywhere, look at any of the elections, every single election, it has been record-setting. 
    ## And the good news is, for the Republican Party, the Democrats are getting very poor numbers in terms of bringing them in. We're getting record-setting numbers. I think I have something to do with that. 
    ## We're getting record-setting numbers. And I won every one — the three of them that I won, I won with record-setting numbers. 
    ## TRUMP: New people are coming into the Republican Party. We are building a new Republican Party, a lot of new people are coming in. 
    ## ARRARÁS: For the record, you have said publicly that you loved Telemundo in the past. But it is not just a Telemundo poll. We have... 
    ## TRUMP: I love them. I love them. 
    ## ARRARÁS: All right. Well, it's not the only poll. 
    ## TRUMP: They're fine. Do you know what? They're fine. 
    ## ARRARÁS: Just last night — let me — let me finish, please. 
    ## Just last night, The Washington Post showed that 80 percent of Hispanic voters in their polls have a negative view of you. And concerning the Nevada victory, allow me to explain that the poll in Nevada was based on a tiny sample, statistically insignificant of only about 100 — let me finish please — of 100 Hispanic Republicans in the state of Nevada. 
    ## TRUMP: Why did they take the poll? Why did they... 
    ## ARRARÁS: I am making reference — I am making reference to Hispanic voters nationwide in a general election. 
    ## TRUMP: I'm just telling you, I'm doing very well with Hispanics. And by the way, I settled my suit, as you know, with Univision. It was settled. We're good friends now. It was all settled up. 
    ## Very happy, very happy. Very good people. 
    ## I'm just telling you — I'm just telling you that I will do really well with Hispanics. I will do better than anybody on this stage. I have respect for the people on the stage, but I will do very well with Hispanics. But I'm telling you also, I'm bringing people, Democrats over and I'm bringing independents over, and we're building a much bigger, much stronger Republican Party. 
    ## BLITZER: Mr. Trump, thank you. 
    ## I want to turn our attention now to another critically important issue for the American people, the United States Supreme Court, where filling the vacancy left by the late Justice Antonin Scalia has become a major campaign issue. I want to bring in Salem Radio Network host, Hugh Hewitt. 
    ## Hugh? 
    ## HEWITT: Thank you, Wolf. 
    ## To me, it's the most important issue. I'll start with you, Senator Cruz. Do you trust Mr. Trump to nominate conservative justices? 
    ## CRUZ: Well, Hugh, I agree with you that it — Justice Scalia's passing underscores the enormous gravity of this election. Justice Scalia was someone I knew personally for 20 years; was privileged to be at his funeral this weekend. And with his passing, the court is now hanging in the balance. We are one liberal justice away from a five-justice radical leftist majority that would undermine our religious liberty; that would undermine the right to life; and that would fundamentally erase the Second Amendment right to keep and bear arms from the Constitution. 
    ## Now, I think the voters of Texas, the voters across Super Tuesday are assessing everyone standing on this — this stage. In the past, Republican presidents always promise to nominate strict constitutionalists. So I'm certain if you took a survey, everyone would say they would do that. 
    ## But the reality is, Democrats bat about 1,000. Just about everyone they put on the court votes exactly as they want. Republicans have batted worse than 500, more than half of the people we put on the court have been a disaster. 
    ## I've spent my whole life fighting to defend the Bill of Rights and the Constitution. I can tell you, for voters that care about life or marriage or religious liberty or the Second Amendment, they're asking the question: Who do you know, beyond a shadow of a doubt, who do you know will nominate principled constitutionalists to the court? I give you my word, every justice I nominate will vigorously defend the Bill of Rights for my children and for yours. 
    ## HEWITT: Mr. Trump, Senator Cruz mentioned the issue that keeps me up at night, which is religious liberty. Churches, Catholic and Christian colleges, Catholic adoption agencies — all sorts of religious institutions fear that Hobby Lobby, if it's repealed, it was a five-four decision, they're going to have to bend their knee and provide morning-after pills. They fear that if Bob Jones is expanded, they will lose their tax exemption. 
    ## Will you commit to voters tonight that religious liberty will be an absolute litmus test for anyone you appoint, not just to the Supreme Court, but to all courts? 
    ## TRUMP: Yes, I would. And I've been there. And I've been there very strongly. I do have to say something, and this is interesting and it's not anybody's fault. It's not Ted's fault. Justice Roberts was strongly recommended and pushed by Ted. Justice Roberts gave us Obamacare. Might as well be called Roberts-care. Two times of the Supreme Court, Justice Roberts approved something that he should have never raised his hand to approve. And we ended up with Obamacare. 
    ## That is a rough thing. And I know Ted feels badly about it. And I think he probably still respects the judge. But that judge has been a disaster in terms of everything we stand for because there is no way — no way that he should have approved Obamacare. 
    ## Now, with that being said, these are the things that happen. But Ted very, very strongly pushed Judge Roberts, and Justice Roberts gave us something that we don't want. 
    ## HEWITT: Ted Cruz, Senator, the chief justice got Hobby Lobby right, but what do you make of Mr. Cruz's criticism? 
    ## CRUZ: Well, listen — Donald knows that it was George W. Bush who appointed John Roberts. Yes, it's true, I supported the Republican nominee once he was made. 
    ## But I would not have nominated John Roberts. I would have nominated my former boss, Mike Luttig, who was the strongest proven conservative on the court of appeals. And I'll tell you, Hugh......you know, it's interesting now that Donald promises that he will appoint justices who — who will defend religious liberty, but this is a man who, for 40 years, has given money to Jimmy Carter, to Joe Biden, to Hillary Clinton, to Chuck Schumer, to Harry Reid. 
    ## Nobody who supports far-left liberal Democrats who are fighting for judicial activists can possibly care about having principled constitutionalists on the court. 
    ## And what Donald has told us is he will go to Washington......and cut a deal. 
    ## HEWITT: Mr. Trump... 
    ## CRUZ: So that means on Supreme Court... 
    ## HEWITT: ... can I... 
    ## CRUZ: ... he's going to look to cut a deal, rather than fight for someone who won't cut a deal on the Constitution, but will defend it faithfully. 
    ## HEWITT: Can I trust you on religious liberty? 
    ## TRUMP: Well, let — let me — let me just say — let me just say this. Look, I watched Ted — and I respected it, but he gets nowhere — stand on the Senate floor for a day or two days, and talk and talk and talk. 
    ## I watched the other senators laughing and smiling. And when Ted was totally exhausted, he left the Senate floor, and they went back to work. OK? We have to have somebody that's going to make deals. 
    ## It's wonderful to stand up for two days and do that. Now, Ted's been very critical — I have a sister who's a brilliant... 
    ## HEWITT: Mr. Cruz, will you make a deal about religious liberty? 
    ## TRUMP: ... excuse me. She's a brilliant judge. He's been criticizing — he's been criticizing my sister for signing a certain bill. You know who else signed that bill? Justice Samuel Alito, a very conservative member of the Supreme Court, with my sister, signed that bill. 
    ## So I think that maybe we should get a little bit of an apology from Ted. What do you think? 
    ## HEWITT: Let me — Senator. 
    ## CRUZ: Let me tell you right now, Donald, I will not apologize for a minute for defending the Constitution. I will not apologize for defending the Bill of Rights. 
    ## And I find it amazing that your answer to Hugh and to the American people is, on religious liberty, you can't have one of the these crazy zealots that actually believes in it. You've got to be willing to cut a deal. 
    ## And you know, there is a reason why, when Harry Reid was asked, of all the people on this stage, who does he want the most, who does he like the most, Harry Reid said Donald — Donald Trump. 
    ## Why? Because Donald has supported him in the past, and he knows he can cut a deal with him. 
    ## You know what, Donald... 
    ## HEWITT: Senator Rubio. 
    ## CRUZ: ... I don't want a Supreme Court justice that you cut a deal with Harry Reid to undermine religious liberty, because that same justice will also erase the Second Amendment from the Bill of Rights. 
    ## TRUMP: When you say crazy zealot, are you talking about you? Crazy zealot — give me a break. 
    ## HEWITT: Senator Rubio, you've heard this exchange on religious liberty. You have said that religious liberty will trump even the ability of people to stay away from same-sex marriages, not provide flowers, not provide baked goods, et cetera. Are you satisfied with this exchange on religious liberty? 
    ## RUBIO: Well, I think you ask a very important question, because the issue here — the next president of the United States has to fill this vacancy. 
    ## Justice Scalia — in the history of the republic, there has never been anyone better than him at standing for the principle that the Constitution is not a living and breathing document — it is supposed to be applied as originally meant. 
    ## And the next president of the United States has to be someone that you can trust and believe in to appoint someone just as good as Scalia — plus there may be at least two other vacancies. 
    ## So you ask Mr. Trump to respond and say that he would, and he says that he would. But the bottom line is, if you look at his record over the last 25 or 30 years, on issue after issue, he has not been on our side. 
    ## Now, if he's changed, we're always looking for converts into the conservative movement. But the bottom line is that, if you don't have a record there to look at and say, "I feel at peace that when Donald Trump is president of the United States, he's going to be firmly on our side on these issues." 
    ## In fact, very recently, he was still defending Planned Parenthood. He says he's not going to take sides in the Palestinians versus Israel. These are concerning things. 
    ## And so, yes, I have a doubt about whether Donald Trump, if he becomes president, will replace Justice Scalia with someone just like Justice Scalia. 
    ## HEWITT: Mr. Trump? 
    ## TRUMP: Well, let — let me just say — let me just say, first of all, I have great respect for Justice Scalia. I thought he was terrific. And if you talk about evolving, Ronald Reagan was a somewhat liberal Democrat. Ronald Reagan evolved into a somewhat strong conservative — more importantly, he was a great president. A great president. 
    ## As far as Planned Parenthood is concerned, I'm pro-life. I'm totally against abortion, having to do with Planned Parenthood. But millions and millions of women — cervical cancer, breast cancer — are helped by Planned Parenthood. 
    ## So you can say whatever you want, but they have millions of women going through Planned Parenthood that are helped greatly. And I wouldn't fund it. 
    ## I would defund it because of the abortion factor, which they say is 3 percent. I don't know what percentage it is. They say it's 3 percent. But I would defund it, because I'm pro-life. But millions of women are helped by Planned Parenthood. 
    ## HEWITT: Governor Kasich, back to religious liberty. You've been a little bit less emphatic. You've said, same-sex couple approaches a cupcake maker, sell them a cupcake. Can we trust you as much on religious liberty as the rest of these people?  
    ## KASICH: Well, you know, , of course. I mean, if — look, I was involved in just being a pioneer in a new church. Religious institutions should be able to practice the religion that they believe in. No question and no doubt about it. 
    ## Now, in regard to same-sex marriage, I don't favor it. I've always favored traditional marriage, but, look, the court has ruled and I've moved on. And what I've said, Hugh, is that, look, where does it end? 
    ## If you're in the business of selling things, if you're not going to sell to somebody you don't agree with, OK, today I'm not going to sell to somebody who's gay, and tomorrow maybe I won't sell to somebody who's divorced. 
    ## I mean, if you're in the business of commerce, conduct commerce. That's my view. And if you don't agree with their lifestyle, say a prayer for them when they leave and hope they change their behavior. 
    ## But when it comes to the religious institutions, they are in inviolate in my mind, and I would fight for those religious institutions. And look, I've appointed over a hundred judges as governor. I even appointed adjudge to the Ohio Supreme Court. 
    ## And you know what they are? They're conservatives. Go check it out. They are conservatives. They don't make the law. They interpret the law. That's all they do. And they stick by the Constitution. So I will do that. 
    ## But let's just not get so narrow here as to gotcha this or that. I think my position is clear. 
    ## HEWITT: Dr. Carson, let me wrap it up with you. Are their positions clear? 
    ## Are the positions you've heard clear about the First Amendment and the first freedom? 
    ## CARSON: Well, first of all, let me just add my praise to Justice Scalia. I first met him when we got an honorary degree together a long time ago. A tremendous wit and intellect. 
    ## As far as religious freedom is concerned, one of the basic tenets of this nation, and I believe that the Constitution protects all of our rights. And it gives people who believe in same-sex marriage the same rights as everybody else. 
    ## But what we have to remember is even though everybody has the same rights, nobody get extra rights. So nobody gets to redefine things for everybody else and then have them have to conform to it. That's unfair. 
    ## And this is the responsibility of Congress to come back and correct what the Supreme Court has done. That's why we have divided government. And we're going to have to encourage them to act in an appropriate way, or we will lose our religious freedom. 
    ## And as president, I would go through and I would look at what a person's life has been. What have they done in the past? What kind of judgments have they made? What kind of associations do they have? That will tell you a lot more than an interview will tell you. 
    ## The fruit salad of their life is what I will look at. 
    ## BLITZER: Thank you, Dr. Carson. 
    ## All of you want to repeal and replace Obamacare, so let's talk about your plans, specific plans to replace it. I want to bring in our chief political correspondent, Dana Bash. 
    ## BASH: Senator Rubio, you said yesterday, right here in Houston, that Mr. Trump thinks part of Obamacare is pretty good. So, he says he is going to repeal Obamacare. Are you saying that you're worried he won't? 
    ## RUBIO: The individual mandate. He said he likes the individual mandate portion of it, which I don't believe that should be part of it. That should not remain there. I think here's what we need to replace it with. 
    ## We need to repeal Obamacare completely and replace it with a system that puts Americans in charge of their health care money again. If your employer wants to buy health insurance for you, they can continue to do so from any company in America they want to buy it from. 
    ## Otherwise, your employers can provide you health care money, tax- free, not treated as income, and you can use that money only for health care, but you can use it to fund health care any way you want, fully fund a health savings account, the combination of a health savings account or a private plan from any company in any state in the country. 
    ## And if you don't have that, then you will have a refundable tax credit that provides you health care money to buy your own health care coverage. And that, I think, is a much better approach than Obamacare, which, by the way, isn't just bad for health care, it's bad for our economy. It is a health care law that is basically forcing companies to lay people off, cut people's hours, move people to part-time. It is not just a bad health care law, it is a job-killing law. And I will repeal it as president and we will replace it with something substantially better for all Americans. 
    ## BASH: Mr. Trump, Senator Rubio just said that you support the individual mandate. Would you respond? 
    ## TRUMP: I just want to say, I agree with that 100 percent, except pre-existing conditions, I would absolutely get rid of Obamacare. We're going to have something much better, but pre-existing conditions, when I'm referring to that, and I was referring to that very strongly on the show with Anderson Cooper, I want to keep pre- existing conditions. 
    ## I think we need it. I think it's a modern age. And I think we have to have it. 
    ## BASH: OK, so let's talk about pre-existing conditions. What the insurance companies say is that the only way that they can cover people is to have a mandate requiring everybody purchase health insurance. Are they wrong? 
    ## TRUMP: I think they're wrong 100 percent. What we need — look, the insurance companies take care of the politicians. The insurance companies get what they want. We should have gotten rid of the lines around each state so we can have real competition. 
    ## We thought that was gone, we thought those lines were going to be gone, so something happened at the last moment where Obamacare got approved, and all of that was thrown out the window. 
    ## The reason is some of the people in the audience are insurance people, and insurance lobbyists, and special interests. They got — I'm not going to point to these gentlemen, of course, they're part of the problem, other than Ben, in all fairness. 
    ## And, actually, the Governor too, let's just talk about these too, OK? 
    ## Because I don't think the Governor had too much to do with this. 
    ## But, we should have gotten rid of the borders, we should have gotten rid of the lines around the state so there's great competition. The insurance companies are making a fortune on every single thing they do. 
    ## I'm self-funding my campaign. I'm the only one in either party self-funding my campaign. I'm going to do what's right. We have to get rid of the lines around the states so that there's serious, serious competition. 
    ## BASH: But, Mr. Trump... 
    ## TRUMP: ... And, you're going to see — excuse me. You're going to see preexisting conditions and everything else be part of it, but the price will be done, and the insurance companies can pay. Right now they're making a fortune. 
    ## BASH: But, just to be specific here, what you're saying is getting rid of the barriers between states, that is going to solve the problem... 
    ## TRUMP: That's going to solve the problem. And, the insurance companies aren't going to say that, they want to keep it. They want to say — they say whatever they have to say to keep it the way it is. I know the insurance companies, they're friends of mine. The top guys, they're friends of mine. I shouldn't tell you guys, you'll say it's terrible, I have a conflict of interest. They're friends of mine, there's some right in the audience. One of them was just waving to me, he was laughing and smiling. He's not laughing so much anymore. 
    ## Hi. 
    ## Look, the insurance companies are making an absolute fortune. Yes, they will keep preexisting conditions, and that would be a great thing. Get rid of Obamacare, we'll come up with new plans. But, we should keep preexisting conditions. 
    ## RUBIO: Dana, I was mentioned in his response, so if I may about the insurance companies... 
    ## BASH: ... Go ahead. 
    ## RUBIO: You may not be aware of this, Donald, because you don't follow this stuff very closely, but here's what happened. When they passed Obamacare they put a bailout fund in Obamacare. All these lobbyists you keep talking about, they put a bailout fund in the law that would allow public money to be used, taxpayer money, to bail out companies when they lost money. 
    ## And, we led the effort and wiped out that bailout fund. The insurance companies are not in favor of me, they hate that. They're suing that now to get that bailout money put back in. 
    ## Here's what you didn't hear in that answer, and this is important guys, this is an important thing. What is your plan? I understand the lines around the state, whatever that means. This is not a game where you draw maps... 
    ## TRUMP: ... And, you don't know what it means... 
    ## RUBIO: ... What is your plan, Mr. Trump?What is your plan on healthcare? 
    ## TRUMP: You don't know. 
    ## BASH: 
    ## TRUMP: ... The biggest problem... 
    ## RUBIO: ... What's your plan... 
    ## TRUMP: ... The biggest problem, I'll have you know... 
    ## RUBIO: ... What's your plan...  
    ## TRUMP: ... You know, I watched him meltdown two weeks ago with Chris Christie. I got to tell you, the biggest problem he's got is he really doesn't know about the lines. The biggest thing we've got, and the reason we've got no competition, is because we have lines around the state, and you have essentially.... 
    ## RUBIO: ... We already mentioned thatplan, I know what that is, but what else is part of your plan... 
    ## TRUMP: ... You don't know much... 
    ## RUBIO: ... So, you're only thing is to get rid of the lines around the states. What else is part of your healthcare plan... 
    ## TRUMP: ... The lines around the states... 
    ## RUBIO: ... That's your only plan... 
    ## TRUMP: ... and, it was almost done — not now... 
    ## RUBIO: ... Alright,... 
    ## TRUMP: ... Excuse me. Excuse me. 
    ## RUBIO: ... His plan. That was the plan... 
    ## TRUMP: ... You get rid of the lines, it brings in competition. So, instead of having one insurance company taking care of New York, or Texas, you'll have many. They'll compete, and it'll be a beautiful thing. 
    ## RUBIO: Alright...So, that's the only part of the plan? Just the lines? 
    ## BASH: 
    ## TRUMP: The nice part of the plan — you'll have many different plans. You'll have competition, you'll have so many different plans. 
    ## RUBIO: Now he's repeating himself. 
    ## TRUMP: No, no, no. [laughter, applause, and cheering] 
    ## TRUMP: 
    ## RUBIO: 
    ##  
    ## TRUMP:I watched him repeat himself five times four weeks ago...  
    ## RUBIO: ... I just watched you repeat yourself five times five seconds ago... 
    ## TRUMP: I watched him meltdown on the stage like that, I've never seen it in anybody... 
    ## BASH: ... Let's stay focused on the subject... 
    ## TRUMP: ... I thought he came out of the swimming pool... 
    ## RUBIO: ... I see him repeat himself every night, he says five things, everyone's dumb, he's gonna make America great again... 
    ## BASH: ... Senator Rubio... 
    ## RUBIO: ... We're going to win, win win, he's winning in the polls... 
    ## BASH: ... Senator Rubio, please. 
    ## RUBIO: ... And the lines around the state.... Every night. 
    ## BASH: Senator Rubio. 
    ##  
    ## TRUMP: I tell the truth, I tell the truth. 
    ## BASH: Senator Rubio, you will have time to respond if you would just let Mr. Trump respond to what you've just posed to him... 
    ## RUBIO: ... Yeah, he's going to give us his plan now, right? OK... 
    ## BASH: ... If you could talk a little bit more about your plan. I know you talked about... 
    ## TRUMP: ... We're going to have many different plans because... 
    ## BASH: ... Can you be a little specific... 
    ## TRUMP: ... competition... 
    ## RUBIO: ... He's done it again. [cheering and applause] 
    ## TRUMP: There is going to be competition among all of the states, and the insurance companies. They're going to have many, many different plans.  
    ## BASH: Is there anything else you would like to add to that... 
    ## TRUMP: No, there's nothing to add. [cheering and applause] What is to add? 
    ## BASH: Thank you. Thank you both. 
    ## RUBIO: Alright. 
    ## BASH: Governor Kasich, you've said it is, quote, "Un American to deny someone health insurance if they have a preexisting condition." 
    ## Would you leave the individual mandate in place requiring all Americans to purchase insurance? 
    ## KASICH: No, I wouldn't. And — but that doesn't matter when it comes to the issue of preexisting conditions. You don't want any American to lose their house, everything they've saved, because they get sick. Now, I think it is more complicated than what we've heard here tonight. We're actually running significant health reform in my state. 
    ## I would repeal Obamacare for a variety of reasons. I would take some of the federal resources, combine it with the freed-up Medicaid program, which I would send back to the states, and cover the people who are currently the working poor because we don't want to have tens of millions of Americans losing their health insurance. 
    ## And then we're driving towards total transparency. If any of you here ever get a hospital bill, it's easier to interpret the Dead Sea scrolls than to understand your hospital bill. The fact is what we need is transparency with hospitals and with the providers. 
    ## And I'll tell you what we will do. We are actually going to make payments to physicians and to hospitals who actually deliver healthcare with great quality at low prices. We actually are going to make the market work. 
    ## BASH: Governor, let me just go back to the original question about the individual mandate. In 1994 when you were in Congress, you proposed a plan requiring an individual mandate. So what changed? 
    ## KASICH: Well, Dana, the Heritage Foundation had this position as well. And when I look at it, I don't think it's tenable. And we don't need to do that. Again, I'm telling you that we are going to — we have a proposal, a plan that we're enacting now that says if you are a hospital or a doctor and you're providing very high quality at lower prices, below the midpoint — some charge high, some charge low. If you are below the midpoint, we are going to give you a financial reward for allowing you to provide services that result in high quality for our people at lower pricess. 
    ## That is the way in which we are going to damp down the rising costs of healthcare. Because if you think about your own deductibles today, they're going higher, higher and higher. And you know what? At some point, people can't afford it. Our plan will work. It uses the market. It uses transparency. It gets the patient in the middle. And guess what? We're actually doing it in my state, the seventh-largest state in the country. And if this will go — this will go national, we will get our hands on healthcare where you will know what's going on. We will pay for quality, lower prices, and we will begin to see healthcare become affordable in America and where people will also be able to have health insurance, even if they have a preexisting condition. 
    ## We don't want to throw millions of people out into the cold and not have the health insurance, Dana. So that's really what we're doing. This is not a theory. This is what we are actually doing in our state. We will begin payments next year based on episodes that we have in our lives. If our primary care physicians keep us healthy for a year, with really high quality, guess what? They will get a financial reward. 
    ## Our primary care physicians need help. They need support. We're losing them. This will allow them to get a reward for doing a great job. 
    ## BASH: Governor Kasich, thank you. 
    ## KASICH: Thank you. 
    ## BASH: Dr. Carson, you have dealt with the sickest of patients. You support covering preexisting conditions. How would you change Obamacare, but maintain that coverage? 
    ## CARSON: Well, first of all, healthcare is not a right. But I do believe it is a responsibility for a responsible society, and we are that. We spend almost twice as much per capita on healthcare as many other nations who have actually much better access than we do. 
    ## I propose a system in which we use health empowerment accounts, which are like a health savings account with no bureaucrats. And we give it to everybody from birth until death. They can pass it on when they die. We pay for it with the same dollars that we pay for traditional healthcare with. We give people the ability to shift money within their health empowerment account within their family. So dad's $500 short, mom can give it to him or a cousin or uncle. 
    ## And it makes every family their own insurance carrier with no middle man. It gives you enormous flexibility. And also, you know, if Uncle Joe is smoking like a chimney, everybody's going to hide his cigarettes because they're all interested in what's going on there. 
    ## Also, the — your catastrophic healthcare is going to cost a lot less money now because the only thing coming out of that is catastrophic healthcare. So, it's like a homeowners policy with a large deductible, versus a homeowners policy where you want every scratch covered. One costs $1,500 a year; one costs $10,000 a year. You can buy the $1,500 one. That will take care of 75 percent of the people. The people who are indigent, how do we take care of them now? Medicaid. What's the Medicaid budget? Almost $500 billion; almost 80 million people participate, which is way too many, and that will get a lot better when we fix the economy, which I hope we get a chance to talk about. 
    ## But do the math. Over $5,000 for each man, woman and child, and all — they will have a lot more flexibility. What could you buy with that? A concierge practice. 
    ## BLITZER: Thank you. 
    ## CARSON: And you could still have thousands of dollars left over. And let me just finish, because I don't get to talk that much. And, you know, let's......you can have the money that's left over to buy your catastrophic insurance. But most importantly, we give them a menu, just like we do in Medicare Part C, and they have the choices that will allow them not only to have catastrophic health care, but drug care and everything else. 
    ## It will be such a good program that nobody will want Obamacare after that, and that's probably the best way do it, although if anybody still did, I would still de-fund it. 
    ## BLITZER: Thank you. 
    ## Thank you, Dr. Carson. Let's talk about the economy. Let's talk about... 
    ## CRUZ: Wolf, Wolf, Wolf. Does everyone get to address Obamacare but me? 
    ## BLITZER: I want to move on, but there'll be plenty of opportunities for you to address... 
    ## CRUZ: It's kind of an issue I have a long history with. 
    ## BLITZER: I know you do. And — all right, go ahead. 
    ## CRUZ: Thank you, Wolf. 
    ## KASICH: How do you — how do you get that extra time, Cruz? You're very good at...  
    ## CRUZ: You know, this is another issue on which Donald and I have sharp disagreements. On Planned Parenthood, he thinks Planned Parenthood is wonderful. I would instruct the Department of Justice to investigate them and prosecute any and all criminal violations. 
    ## On Obamacare, both Donald and I say we want to end it, but for very different reasons. I want to end it because it goes too far, it's killed millions of jobs, and it's hurting people's health care. Donald wants to end it because he says it doesn't go nearly far enough. And what was amazing in that exchange that was missing is for decades Donald has been advocating socialized medicine. 
    ## What he's said is government should pay for everyone's health care, and in fact, a couple of debates ago, he said, if you don't support socialized health care, you're heartless. Now, liberal Democrats have been saying that for years. Now let me tell you if you're a small business owner, Donald Trump's socialized medicine, putting the government in charge of your health care would kill more jobs than Obamacare, and if you're elderly, the results of socialized medicine in every country on earth where it's been implemented has been rationing, has been the government saying, no, you don't get that hip replacement, you don't get that knee replacement, the government is in charge of your health care. 
    ## I'll tell you this. As president... 
    ## BLITZER: Senator... 
    ## CRUZ: ... I will repeal every word of Obamacare. 
    ## BLITZER: Thank you, thank you. Mr. Trump? 
    ## TRUMP: I do not want socialized medicine, just so you understand. He goes around saying oh, he wants it. I do not want socialized medicine. I do agree with him that it's going to be a disaster, Obamacare, for the economy. 
    ## In 2017, it will be impossible for us to pay for it if you look at what's going on. That's why it has to be repealed, for a lot of reasons, Number one, it doesn't work, number two, premium. You look at premiums going up, 25, 35, even 45 percent, and more. We have to get rid of Obamacare. It is going to destroy our economy completely. Our economy is not doing well. It is going to destroy our economy greatly. And on that, I agree. 
    ## CRUZ: Donald, true or false, you've said the government should pay for everyone's health care. 
    ## TRUMP: That's false. 
    ## CRUZ: You've never said that? 
    ## TRUMP: No, I said it worked in a couple of countries... 
    ## CRUZ: But you've never stood on this debate stage and says it works great in Canada and Scotland and we should do it here. 
    ## TRUMP: No, I did not. No I did not. 
    ## CRUZ: Did you say if you want people to die on the streets, if you don't support socialized health care, you have no heart. 
    ## TRUMP: Correct. I will not let people die on the streets if I'm president. 
    ## CRUZ: Have you said you're a liberal on health care? 
    ## TRUMP: Excuse me. Let me talk. If people... 
    ## CRUZ: Talk away. Explain your plan, please. 
    ## TRUMP: If people — my plan is very simple. I will not — we're going to have private — we are going to have health care, but I will not allow people to die on the sidewalks and the streets of our country if I'm president. You may let it and you may be fine with it... 
    ## CRUZ: So does the government pay for everyone's health care? 
    ## TRUMP: ... I'm not fine with it. We are going to take those people... 
    ## CRUZ: Yes or no. Just answer the question. 
    ## TRUMP: Excuse me. We are going to take those people and those people are going to be serviced by doctors and hospitals. We're going to make great deals on it, but we're not going to let them die in the streets. 
    ## CRUZ: Who pays for it? 
    ## RUBIO: Well, can I just clarify something? 
    ## BLITZER: Gentleman, please. 
    ## RUBIO: Wolf, no. I want to clarify something. 
    ## BLITZER: Gentlemen please. I want to move on. 
    ## RUBIO: This is a Republican debate, right? Because that attack about letting people die in the streets... 
    ## BLITZER: I want to talk about the economy. 
    ##  
    ## BLITZER: Gentleman, gentleman. All of you have agreed — Senator Cruz... 
    ## TRUMP: You know what? Call it what you want. 
    ## CRUZ: It's a yes or no. 
    ## TRUMP: Call it what you want, people are not going to be dying on the sidewalk. 
    ## BLITZER: All of you have agreed — all of you have agreed to the rules. I want to move on. We're talking about the economy right now. Mr. Trump, you want to cut taxes more than President Ronald Reagan did, more than President George W. Bush did. The Independent Tax Foundation says the cost to the country of your proposal would be about $10 trillion, and that takes into account the economic growth that would emerge from your proposed tax cuts. 
    ## How would you cut $10 trillion over 10 years, but make sure the country isn't saddled with even more debt?  
    ## TRUMP: Because the country will become a dynamic economy. We'll be dynamic again. If you look at what's going on, we have the highest taxes anywhere in the world. We pay more business tax, we pay more personal tax. We have the highest taxes in the world. 
    ## It's shutting off our economy. It's shutting off our country. We have trillions of dollars outside that we can't get in. Yes, we will do my tax plan, and it will be great. We will have a dynamic economy again. 
    ## BLITZER: What specific cuts will you make to pay for that tax cut? 
    ## TRUMP: We're going to make many cuts in business. We're getting rid of — we're going to get rid of so many different things. Department of Education — Common Core is out. We're going local. Have to go local. 
    ## Environmental protection — we waste all of this money. We're going to bring that back to the states. And we're going to have othermany things. 
    ## We are going to cut many of the agencies, we will balance our budget, and we will be dynamic again. 
    ## BLITZER: Mr. Trump — Mr. Trump. If you eliminate completely the Department of Education, as you have proposed, that's about $68 billion. If you eliminate the Environmental Protection Agency, that's about $8 billion. That's about $76 billion for those two agencies. 
    ## The current deficit this year is $544 billion. Where are you going to come up with the money? 
    ## TRUMP: Waste, fraud and abuse all over the place. Waste, fraud and abuse. 
    ## You look at what's happening with Social Security, you look — look at what's happening with every agency — waste, fraud and abuse. We will cut so much, your head will spin. 
    ## BLITZER: Governor Kasich. 
    ## When you were in Congress, you were chairman of the Budget Committee. You helped craft the last balanced budget the United States had. Can Mr. Trump's plan work? 
    ## KASICH: Well, I think it takes three things, Wolf. And I've done it. I mean, I — we got the budget balanced. We cut the capital gains tax. You see, in order to get this economy moving again, you have to grow the economy, and you have to restrain the spending. 
    ## And when I was chairman, we cut that capital gains tax and we instituted a significant program to get to balance. We had a balanced budget four years in a row, had to take on every interest group in Washington — every single one of them — and we paid down a half a trillion of the national debt. 
    ## And why do you do it? Because you want job growth. If you don't have regulatory reform, common-sense regulations, reasonable tax cuts, which I have, and a fiscal plan, you won't get there. You will never be able to do it. 
    ## Now, I — I inherited an an $8 billion hole in Ohio, I have common-sense regulations, I have tax cuts — the biggest of any governor in the country — and we have a fiscal plan. 
    ## And it's not all — it's not always cutting. It's innovating — it's producing a better product at, frankly, a lower price. Now we have a $2 billion surplus. Our credit is strong, our pensions are strong. 
    ## And, look — I've got a plan to take to Washington, and I will have it there in the first hundred days, and it will include shifting welfare, education, transportation, Medicaid and job training back to us, so we can begin, in the states, to be the laboratories of innovation. 
    ## I've done it — I did it in Washington — four years of balanced budgets. No one could even believe it happened.I've done it in Ohio, we're growing, the jobs are up and people are having opportunity. And I will go back to Washington and do it again for the American people. I promise you that. 
    ## Within the first hundred days, we will have the plan to get this done. 
    ## BLITZER: Thank you. Thank you, governor. 
    ## Speaking of taxes... 
    ## TRUMP: I just want to say — and I'm a big fan of the governor, but they also struck oil, OK, so that helped Iowa a lot. 
    ## KASICH: OK, let me — let me — let me just talk about that, because I know that — that Donald believes the energy industry is important. So do I. But of the over 400,000 jobs that we've created in the state, we think maybe 15,000 are connected to this industry, because it's early-stage. 
    ## See, what we've done in Ohio, and what a president needs to do, is to have a cabinet and a whole operation that's jobs-friendly. We have diversified our economy. 
    ## We — we do have energy, we have medical devices, we have financial services, we have I.T., we just got Amazon — their Cloud computing in the Midwest. You know why it's happening? 
    ## Because we're balanced budgets, we're strong, we're job-friendly, we don't raise their taxes, and if we have a president that does that in America, we will get the economic growth, and that is what this country needs. Jobs, jobs and jobs, period. 
    ## BLITZER: Mr. Trump, yesterday, the last Republican presidential nominee, Mitt Romney, called on you to release your back tax returns, and said, and I'm quoting him now, "there is good reason to believe there is a bombshell in them." 
    ## Romney said either you're not as wealthy as you say you are, said maybe you haven't paid the kind of taxes we would expect you to pay, or you haven't been giving the money to veterans or disabled people. Are any of those accusations that he has leveled true? 
    ## TRUMP: All right. First of all, let me just explain. I was the first one to file a financial disclosure form — almost 100 pages. You don't learn anything about somebody's wealth with a tax return. You learn it from statements. 
    ## I filed — which shows that I'm worth over $10 billion. I built a great company with very little debt. People were shocked, the people in the back, the reporters, they were shocked when they went down. And I filed it on time. I didn't ask for five 45-day extensions, which I would have been entitled to. 
    ## So as far as that's concerned, I filed it. And that's where you find out what kind of a company. You don't learn anything from a tax return. 
    ## I will say this. Mitt Romney looked like a fool when he delayed and delayed and delayed. And Harry Reid baited him so beautifully. And Mitt Romney didn't file his return until a September 21st of 2012, about a month-and-a-half before the election. And it cost him big league. 
    ## As far as my return, I want to file it, except for many years, I've been audited every year. Twelve years, or something like that. Every year they audit me, audit me, audit me. 
    ## Nobody gets audited — I have friends that are very wealthy people. They never get audited. I get audited every year. I will absolutely give my return, but I'm being audited now for two or three years, so I can't do it until the audit is finished, obviously. And I think people would understand that. 
    ## BLITZER: Hugh, go ahead. 
    ## HEWITT: Mr. Trump. You told me... 
    ## TRUMP: Are you going to ask anybody else that question? 
    ## CARSON: Yes, amen, amen. 
    ## TRUMP: Every single question comes to me? 
    ## HEWITT: Mr. Trump... 
    ## TRUMP: I know I'm here for the ratings, but it's a little bit ridiculous. 
    ## HEWITT: Mr. Trump, a year ago you told me on my radio show, the audio and the transcript are out there on YouTube, that you would release your tax returns. 
    ## TRUMP: True. 
    ## HEWITT: Are you going back on your commitment? 
    ## TRUMP: No, I'm not. First of all, very few people listen to your radio show. That's the good news. 
    ## Let me just tell you, let me just — which happens to be true. Check out the ratings. 
    ## Look, let me just tell you something. Let me just tell you something. I want to release my tax returns but I can't release it while I'm under an audit. We're under a routine audit. I've had it for years, I get audited. 
    ## And obviously if I'm being audited, I'm not going to release a return. As soon as the audit is done, I love it. 
    ## HEWITT: So, Senator Rubio, Mitt Romney also called upon to you release your tax returns. Your campaign said last spring that you would release your returns that you had not previously released. And you said, coming out any day momentarily. When are we going to see your returns? 
    ## RUBIO: Yes, tomorrow or Saturday, in fact, is our plan to release them. And there's nothing really that interesting in them. So I have no problem releasing them. And luckily I'm not being audited this year, or last year, for that matter. 
    ##  
    ## RUBIO: But this is my time. I want to go back to this question you asked about the debt. This is an important issue. It's a huge issue, OK? In less than five years, 83 percent of our entire budget will be made up of Social Security, Medicare, Medicaid, and the interest on the debt. 
    ## That means only 17 percent of our budget will be for things like the military or the Department of Education or environmental protection issues. 
    ## You cannot balance our budget unless you deal with that 83 percent, which is why I've been repeatedly talking about since my time running for the Senate in Florida, where there are a lot of people like my mother that depend on Social Security and Medicare, on the need to save those programs, by reforming the way they work for future generations. 
    ## And I think if we — the longer we take to do this, the closer we are going to get to a debt crisis. And, Wolf, you did not get an answer to your question. This debt issue is — the next president of the United States will not be able to serve four to eight years without dealing with the national debt. 
    ## It is not a question of if, it is a question of when we have a debt crisis. And we should not leave the stage here tonight without hearing a serious answer from every single one of us about how we are going to deal bring the national debt under control once and for all. 
    ## HEWITT: Thank you, Senator Rubio. But I am......I'm being fair to all of the candidates. 
    ## Senator Cruz, Tuesday is five days away. Why haven't voters seen your 2012, 2013, and 2014 returns? 
    ## CRUZ: So, I've released five years of tax returns already. We will have two more years available tomorrow. And I would note that this question really goes — you know, Donald says he's being audited. 
    ## Well, I would think that would underscore the need to release those returns. If he has said something that was false and that an audit is going to find was fraudulent, the voters need to know. 
    ## And listen, people across this country, we recognize our country is in crisis. The most important question is how do we win the general election in November, 2016. And roughly 65 percent of Republicans think Donald is not the right candidate to go against Hillary Clinton. 
    ## Now, part of the reason in the last 10 polls... 
    ## TRUMP: Eighty-five percent say you, big difference. 
    ## CRUZ: ... RealClearPolitics he has lost to Hillary on eight of them. In the last 10 polls on RealClearPolitics, I either tied or beat Hillary. And this is an example. 
    ## You know, the mainstream media is laying off Donald now. They're going to pick apart his taxes. They're going to pick apart his business deals. 
    ## And let's take, for example, one of Hillary's great vulnerabilities, the corruption at the Clinton Foundation, the fact that she had CEOs and foreign companies giving her money while she was secretary of state. 
    ## The next Republican nominee needs to be able to make that case against Hillary. And if Donald tried to did it, Hillary would turn to Donald and say, "but gosh, Donald, you gave $100,000 to the Clinton foundation. I even went to your wedding." 
    ## He can't prosecute the case against Hillary, and we can't risk another four years of these failed Obama policies by nominating someone who loses to Hillary Clinton in November. 
    ##  
    ## TRUMP: So at the beginning, I said openly to everybody that I contribute to many, many politicians, both Republican and Democrat. And I have, over the years. I'm a businessman. I have, over the years. 
    ## And I sort of have to laugh when Ted makes a big deal out of the fact that he's doing well in the polls. Well, I'm beating him in virtually every poll. I'm tied in Texas, by the way, which I shouldn't be. But I think I'll do very well. 
    ## But a poll just came out — a Bloomberg poll — where I am beating him so badly that it's, like, embarrassing even for me to say I'm beating him that badly. 
    ## And — and here's the thing — it was sort of funny — 65 percent of the people don't like you — I just got 36 percent of the vote, right? I just got 46 percent on another one. I got 38 percent......on another one. That means — and he got 20 and 22, and he lost in South Carolina so badly — that was going to be his stronghold. He said a year ago, "I can't lose South Carolina." I beat him in a landslide. 
    ## Last week in Nevada, I beat him in a landslide, and he sank about the polls. One other thing — Hillary Clinton — take a look at USA Today, take a look at the Q poll. I beat her, and I beat her badly. And I — and I haven't even started at her. I only had one little interchange......I only had one little interchange, and that was......four weeks ago, when she said I was sexist. And believe me, they had a rough weekend that weekend, between Bill and Hillary. They had a rough weekend. 
    ## BLITZER: Gentlemen. Gentlemen. Gentlemen. 
    ## CRUZ: Hold on. He — he attacked me, Wolf. I get a response. 
    ## BLITZER: I was about to say — Senator Cruz, respond. 
    ## CRUZ: Thank you. Thank — thank — thank you very much. 
    ## You know, it's interesting — Donald went — went on — on an extended tirade about the polls, but he didn't respond to any of the substance. He has yet to say — he can release past year's tax returns. He can do it tomorrow. 
    ## He doesn't want to do it, because presumably there's something in there... 
    ## TRUMP: Nothing. 
    ## CRUZ: ... that is bad. If there's nothing, release them tomorrow. 
    ##  
    ## CRUZ: They're already prepared. The only reason he's not releasing them... 
    ## TRUMP: You — you don't... 
    ## CRUZ: ... is because he's afraid that he will get hit. 
    ## TRUMP: I'm not afraid. 
    ## CRUZ: You know, Marco made reference earlier to the litigation against Trump University. It's a fraud case. His lawyers have scheduled the trial for July. 
    ## I want you to think about, if this man is the nominee, having the Republican nominee......on the stand in court, being cross-examined about whether he committed fraud. You don't think the mainstream media will go crazy on that? 
    ## And on substance, how do we nominate a candidate who has said Hillary Clinton was the best secretary of state of modern times, who agreed with her on foreign policy, who agrees with Bernie Sanders on health care, who agreed with Barack Obama on the Wall Street bailout? 
    ## BLITZER: All right... 
    ## CRUZ: If — we've got to win this election, and we can't do it with a candidate who agrees with Hillary Clinton and can't take it to her and beat her on the debate stage and at the polls. 
    ## BLITZER: Mr. Trump. Mr, — hold on. Mr. Trump — Mr. Trump... 
    ## TRUMP: ... first of all, he's talking about the polls. I'm beating him awfully badly in the polls. 
    ##  
    ## CRUZ: But you're not beating Hillary. You're not beating Hillary. 
    ## TRUMP: Well, then, if I can't — if — hey, if I can't beat her, you're really going to get killed, aren't you? 
    ## So — so let me ask you this, because you're really getting beaten badly. I know you're embarrassed — I know you're embarrassed, but keep fighting — keep swinging, man. Swing for the fences. 
    ## Let me just tell you — let me just tell you, the Trump University case is a civil case. Not a — it's a civil case. It's a case where people want to try and get — it's a case that is nonsense. 
    ## It's something I could have settled many times. I could settle it right now for very little money, but I don't want to do it out of principle. The people that took the course all signed — most — many — many signed report cards saying it was fantastic, it was wonderful, it was beautiful. 
    ## As — and believe me, I'll win that case. That's an easy case. Civil case. Number two, as far as the taxes are concerned, I'm being audited. It's a very routine audit, and it's very unfair, because I've been audited for, I think, over 12 years. 
    ## Every year, because of the size of my company, which is very, very large, I'm being audited — which is a very large company. 
    ##  
    ## BLITZER: Thank you. 
    ## TRUMP: I'm being audited 12 years in a row, at least. 
    ## Now, until that audit's done, and I don't think anybody would blame me, I'm not giving it... 
    ## CRUZ: ... the years you're not being audited? Will you release those years? 
    ## BLITZER: Gentlemen, gentlemen, thank you. 
    ## TRUMP:audited for those years. 
    ## CRUZ: Which years? Which years are you being audited? 
    ## BLITZER: Gentlemen......we actually have rules — we're trying to obey these rules that all of you agreed. We're going to take a quick break. We have a lot more — many more critically important issues to discuss. 
    ## Our coverage of this tenth Republican presidential debate from the University of Houston continues in a moment. 
    ##  
    ## BLITZER: Welcome back to the Republican presidential debate here at the University of Houston. 
    ## Gentlemen, I want to turn our attention right now to key issues involving foreign policy and national security. And Mr. Trump, I'll begin with you. 
    ## TRUMP: Shocking. 
    ## BLITZER: You said this about the ongoing conflict between the Israelis and the Palestinians — I'm quoting you now: "Let me be sort of a neutral guy. I don't want to say whose fault it is, I don't think it helps." 
    ## TRUMP: Right. 
    ## BLITZER: Here's the question. How do you remain neutral when the U.S. considers Israel to be America's closest ally in the Middle East? 
    ## TRUMP: Well, first of all, I don't think they do under President Obama because I think he's treated Israel horribly, all right? I think he's treated Israel horribly. 
    ## I was the grand marshall down 5th Avenue a number of years ago for the Israeli Day Parade, I have very close ties to Israel. I've received the Tree of Life Award and many of the greatest awards given by Israel. 
    ## As president, however, there's nothing that I would rather do to bring peace to Israel and its neighbors generally. And I think it serves no purpose to say that you have a good guy and a bad guy. 
    ## Now, I may not be successful in doing it. It's probably the toughest negotiation anywhere in the world of any kind. OK? But it doesn't help if I start saying, "I am very pro-Israel, very pro, more than anybody on this stage." But it doesn't do any good to start demeaning the neighbors, because I would love to do something with regard to negotiating peace, finally, for Israel and for their neighbors. 
    ## And I can't do that as well — as a negotiator, I cannot do that as well if I'm taking big, big sides. With that being said, I am totally pro-Israel. 
    ## BLITZER: Senator Cruz? 
    ## CRUZ: Well, this is another area on which Donald agrees with Hillary Clinton and on which I disagree with them both strongly. Both Donald and Hillary Clinton want to be neutral, to use Donald's word, between Israel and the Palestinians. 
    ## Let me be clear. If I'm president, America will stand unapologetically with the nation of Israel. 
    ## And the notion of neutrality is based upon the left buying into this moral relativism that is often pitched in the media. Listen, it is not equivalent. When you have terrorist strapping dynamite around their chest, exploding and murdering innocent women and children, they are not equivalent to the IDF officers protecting Israel. And I will not pretend that they are. 
    ## Just today, Iran announced they're going to pay $7,000 to each suicide bomber. And I would note, missing from Donald's answer was anything he has done in his nearly 70 years of living defending Israel. I have over and over again led the fight to defend Israel, to fight for Israel. And this — if you want to know who will stand with Israel, we ought to start with who has stood with Israel when the heat was on. 
    ## BLITZER: Mr. Trump? 
    ## TRUMP: Well, I can only say — look, I can only say I've been a big contributor to Israel over the years. I've received many, many awards from Israel, as I've said before. I have a great relationship with Israel. And I'm going to keep it that way. And if I could bring peace, that would be a fantastic thing. It would be one of my greatest achievements as president. 
    ## BLITZER: Governor Kasich, I want you to weigh in. 
    ## KASICH: Well, I mean, well, I was in Congress for 18 years on the Defense Committee. And then, you know, after 9/11, the secretary of defense called me in to help out with some things. And I've been a supporter of Israel — a strong supporter of Israel longer than anybody on this stage. I didn't give as much money as Donald gave, but I've been standing with the Israelis for a very long time. 
    ## And frankly, I think the problem we have in foreign policy right now, Wolf, is that we are not certain with who we stand with. Our allies are not sure what to make of us, and our enemies are moving. And one — are moving because they're not sure what we will do. 
    ## It's a very interesting development here within the 24 hours. We said to the South Koreans that we would give them the high altitude defense system. It really rattled the Chinese, and for the first time since we took positive action, the Chinese are beginning to take action against North Korea. 
    ## When we stand firm and we let the world know who we're with, who we stand for, and we bring our allies together, that is the road forward. 
    ##  
    ## BLITZER: We're going to get to North Korea in a moment. But Senator Rubio, what's wrong with the U.S. being an honest broker in a negotiation, as Mr. Trump is proposing? 
    ## RUBIO: Because — and I don't know if Donald realizes this. I'm sure it's not his intent perhaps. But the position you've taken is an anti-Israel position. And here's why. Because you cannot be an honest broker in a dispute between two sides in which one of the sides is constantly acting in bad faith. The Palestinian Authority has walked away from multiple efforts to make peace, very generous offers from the Israels. Instead, here's what the Palestinians do. They teach their four- year-old children that killing Jews is a glorious thing. Here's what Hamas does. They launch rockets and terrorist attacks again Israel on an ongoing basis. The bottom line is, a deal between Israel and the Palestinians, given the current makeup of the Palestinians, is not possible. 
    ## And so the next president of the United States needs to be someone like me who will stand firmly on the side of Israel. I'm not — I'm not going to sit here and say, "Oh, I'm not on either side." I will be on a side. I will be on Israel's side every single day because they are the only pro-American, free enterprise democracy in the entire Middle East. 
    ## BLITZER: Mr. Trump? 
    ## TRUMP: I'm a negotiator. I've done very well over the years through negotiation. It's very important that we do that. In all fairness, Marco is not a negotiator. I watched him melt down and I'll tell you, it was one of the saddest things I've ever seen. He's not going down — excuse me... 
    ## RUBIO: He thinks a Palestinian is a real estate deal. 
    ## TRUMP: ... wait a minute, and these people may even be tougher than Chris Christie. OK? 
    ## RUBIO: The Palestinians are not a real estate deal, Donald. 
    ## TRUMP: OK, no, no, no — a deal is a deal. Let me tell you that. I learned a long time ago. 
    ## RUBIO: A deal is not a deal when you're dealing with terrorists. Have you ever negotiated with terrorists? 
    ## TRUMP: You are not a negotiator. You are not a negotiator. 
    ## And, with your thinking, you will never bring peace. You will never bring peace... 
    ## RUBIO: ... Donald, might be able toPalestinians and Arabs, but it's not a real estate deal... 
    ## TRUMP: ... Excuse me, I want to be able to bring peace... 
    ## BLITZER: ... Senator. 
    ## TRUMP: He will never be able to do it. I think I may be able to do it, although I will say this. Probably the toughest deal of any kind is that particular deal. 
    ## BLITZER: Let's move on to talk about North Korea. You raised it, Governor Kasich. The threat posed by North Korea to the United States and its sallies, the commander of American forces in South Korea said that North Korean leader Kim Jong Un would use a weapon of mass destruction if he thought his regime was being threatened. You have said the United States should start examining a strategy of regime change in North Korea. 
    ## Let's be clear. Are you talking about getting rid of Kim Jong Un? 
    ## KASICH: When you talk about regime change, Wolf, it means regime change. That's what it means. Even though there's so much chaos in North Korea right now, there's a lot of reports of uncertainty, and instability in that government. 
    ## But, look, here's what I think we ought to do — like, immediately. And, we've been kicking the can down the road on this for, I don't know, 15 years. We should be intercepting the ships that are leaving North Korea so they're not selling this material, or this technology, or giving it to someone else. 
    ## Secondly, the same goes with the aircraft. 
    ## Thirdly, we need to slap even tougher sanctions on North Korea because we really don't have the toughest sanctions on North Korea. We ought to talk about arming South Korea with ballistic missile technology. And, of course, also Japan with ballistic missile technology. Because we're now starting to take a firm position. We have the attention of the Chinese. The Chinese are the best way to calm that regime down and get them in a position of where they back off. 
    ## But, when I say regime change, I don't have to talk exactly what that means. Look, I've been involved in national security for a long time. You don't have to spell everything out, but what I'm telling you is you look for any means you can to be able to solve that problem in North Korea, and in the meantime put the pressure on the Chinese. And, what we're doing is beginning to work against them. 
    ## They are the key to being able to settle this situation. 
    ## BLITZER: I just want to be precise, Governor Kasich, this is critically important. There are a million North Korean troops North of the DMZ... 
    ## KASICH: ... I'm very well aware of that. 
    ## BLITZER: A million South Korean troops, 28,000 U.S. troops along the DMZ, right in between. Would you risk war for a regime change? 
    ## KASICH: Wolf, again, it would depend exactly what, you know, what was happening. What the situation was. But, if there was an opportunity to remove the leader of North Korea and create stability? Because, I'll tell you, you keep kicking the can down the road we're going to face this sooner or later. 
    ## But, in the meantime, I'm also aware of the fact that there's 10 million people living in Seoul. So, you don't just run around making charges. I have put it on the table that I would leave to see regime change in North Korea.  
    ## Now, perhaps the Chinese can actually accomplish that with this man who is now currently the leader, but the fact is we have to bring everything to bear. We have to be firm, and we've got to unite those people in that part of the world to stand firmly against North Korea, and make sure we have the ballistic......ballistic missile technology to defend ourselves. 
    ## BLITZER: Mr. Trump. 
    ## TRUMP: One thing I'd like to add to what the Governor's saying, I think that we are now in a position — are $19 trillion dollars because of the horrible omnibus budget that was approved six weeks ago, it's going to be $21 trillion dollars. We can no longer defend all of these countries, Japan, Germany, South Korea. 
    ## You order televisions, you order almost anything, you're getting it from these countries. Whether it's a Mercedes-Benz, or whether it's an air conditioning unit. They're coming out of these countries. They are making a fortune. Saudi Arabia, we are defending Saudi Arabia. Before the oil went down, now they're making less, but they're making plenty. They were making $1 billion dollars a day. 
    ## We defend all of these countries for peanuts. You talk about budgets. We have to start getting reimbursed for taking care of the military services for all of these countries. 
    ## KASICH: Hey, Wolf, Wolf... 
    ## BLITZER: Dr. Carson. 
    ## KASICH: Hey, Wolf, let me just say this because he mentioned this. Look, we're all in agreement that the Japanese need to do more. We're all in agreement that the Europeans need to do more, but I hate to just tell everybody we are the leader of the world and we should put the pressure on them to do their job. There is no question about it. 
    ## But, at the same time, we also have to rebuild the military. Look, I have a balanced budget plan that cuts taxes, reforms regulations, but also builds the military, puts a $100 billion dollars more in defense. We need to rebuild our defenses, 
    ## But, I must also tell you, a long time reformer of the Pentagon, we must reform that building. 
    ## We can't have a weapon system take 22 and a half years. We have 800,000 bureaucrats working for DOD, performing bureaucratic functions when we ought to be putting... 
    ## BLITZER: ... Thank you... 
    ## KASICH: ... these resources into strengthening the military. So, we can do it all... 
    ## BLITZER: Dr. Carson, how would you deal with North Korea? 
    ## CARSON: OK. Well first of all, people say that I whine a lot because I don't get time. I'm going to whine because I didn't get asked about taxes, I didn't get asked about Israel. Hugh, you said you're going to be fair to everybody, you didn't ask me about taxes. I had something to say about that. 
    ## Now... 
    ## BLITZER: Go ahead. This is your moment. 
    ## CARSON: OK. We have a system of taxation in this country that is horribly wrong. You know, I never had an audit until I spoke at the National Prayer Breakfast, and then all of a sudden, they came in, they said we just want to look at your real estate dealings. And then they didn't find anything, so they said let's look at the whole year. And they didn't find anything, so they said let's look at the next year and the next year. They didn't find anything and they won't find anything because I'm a very honest person. 
    ## But he fact of the matter is the IRS is not honest and we need to get rid of them. 
    ## And as far as Israel is concerned, you know, when I was there several months ago, I talked to a lot of people. I couldn't find a single one who didn't think that we had turned our backs on Israel. You know, they are a strategic partner for us but also recognize that we have a Judeo Christian foundation, and the last thing we need to do is to reject Israel. It doesn't mean that we can't be fair to other people. We can always be fair to other people, but, you know, it's like when you have a child, you know, you want to be fair to all the children around but you have a special attention for your own child. 
    ## And now, as far as North Korea is concerned, you know, Kim Jung Un is an unstable person, but he does understand strength. And I think we have to present strength to him. We should be encouraging the alliance with Japan and South Korea. We should be encouraging the placement of the THAAD, the Terminal High-Altitude Area Defense, that seems to disturb not only the North Koreans but the Chinese as well. 
    ## And we also need to have a much more robust naval presence in that area, and I think we need to be developing strategic defense initiative because this man is going to have long-range missiles, he is going to have nuclear capabilities. We need to be able to defend ourselves. And lastly, we should make sure that he knows that if he ever shoots a missile at us, it will be the last thing he ever does. 
    ## BLITZER: Thank you. Thank you. We're going to continue with national security. Go ahead, Hugh. 
    ## HEWITT: Thank you, Wolf. Mr. Trump, we are less than 24 hours away from a ceasefire in Syria that has been brokered between the U.S. and Russia. Do you support this ceasefire? 
    ## TRUMP: I really don't because it not working and the countries aren't agreeing to it and the rebels aren't agreeing and Syria is not agreeing. So It's a meaningless ceasefire. 
    ## I love the idea of a ceasefire. I love the idea of — with a total cessation. But it's not working, as you know very well. It's not working. If — we can do what we want with Russia but nobody else is adhering to it. 
    ## So I certainly support it, I would certainly love it, but all parties have to be part of it. 
    ## HEWITT: Senator Cruz, your opinion on the ceasefire. 
    ## CRUZ: Well look. We're certainly hopeful that the violence will cease, but there's reason to be highly skeptical. Russia has enhanced its position because of Obama's weakness in the Middle East, weakness in Syria. And you know, as we're headed to November, we need no nominate a Republican candidate that can lay out a clear difference with both Barack Obama and Hillary Clinton on foreign policy. 
    ## One of the real challenges with both Donald and Senator Rubio is that they have agreed over and over again with both Hillary Clinton and Barack Obama. So for example, in Libya, both of them agreed with the Obama/Clinton policy of toppling the government in Libya. That was a disaster. It gave the country over to radical Islamic terrorism and it endangered America. 
    ## Another example is John Kerry. John Kerry — Senator Rubio voted to confirm John Kerry as secretary of State. I voted against him. And Donald Trump supported John Kerry against George W. Bush in 2004, gave him a check. And John Kerry has been the most anti-Israel secretary of State this country has ever seen. His diplomacy has been a disaster. And if we nominate someone who agreed with John Kerry, Barack Obama and Hillary Clinton on foreign policy, we're not in a strong position to win the general election. 
    ## HEWITT: A response, Mr. Trump, then Mr. Rubio. 
    ## TRUMP: Again, I think I gave them both checks to be exactly honest. I think they both liked me very much. But the fact is that ... 
    ## CRUZ: But you called for Bush to be impeached. 
    ## TRUMP: Well, I think Bush did a hell of a bad as far as that's concerned. You know it and so do I. 
    ## CRUZ: But you gave him a check and called for him to be impeached. 
    ## TRUMP: Be honest. Be honest. No, this was before. The check came early.  
    ## But let me just tell you, Syria, he's saying that I was in favor of Syria. He said I was in favor of Libya? I never discussed that subject. I was in favor of Libya? We would be so much better off if Gadhafi were in charge right now. 
    ## If these politicians went to the beach and didn't do a thing, and we had Saddam Hussein and if we had Gadhafi in charge, instead of having terrorism all over the place, we'd be — at least they killed terrorists, all right? 
    ## And I'm not saying they were good because they were bad, they were really bad, but we don't know what we're getting. You look at Libya right now, ISIS, as we speak, is taking over their oil. As we speak, it's a total mess. 
    ## We would have been better off if the politicians took a day off instead of going into war. 
    ## HEWITT: Senator Rubio. 
    ## RUBIO: Yes, a couple of points. Number one, on the Libya situation, we didn't topple Gadhafi, the Libyan people toppled Gadhafi. The only choice before America that this president had to make is, does it happen quickly or does it take a long time? 
    ## And I argued if it takes a long time, you're going to have rebel forces emerge like these radical Islamists to take advantage of the vacuum. And that's what happened. That's where the term "lead from behind" came. And that's the foreign policy that apparently Senator Cruz appears to agree with. 
    ## On John Kerry, yes, you know why, because every day John Kerry wasn't appointed was another day Hillary Clinton was still in charge of the State Department. And she was absolutely horrible. 
    ## I couldn't imagine that they were going to find somebody even worse than her, but this president never ceases to amaze. [laughter amd applause] 
    ## And the last point I would make on South Korea, now this is important, because we're asking to be commander-in-chief. Donald is asking to be commander-in-chief. And he's saying these guys need to do more. 
    ## South Korea contributes $800 million a year to that effort. And Japan contributes as well. And here's why our commitment to that regional security is so critical, Donald, because if we walk away from them, both Japan and South Korea will become nuclear weapons powers. 
    ## They can do that very quickly. And that's what they will do if the American defense agreements wither away, which is why we have to rebuild the military, but why we can't walk away from our Asia-Pacific defense status. 
    ## HEWITT: Mr. Trump. 
    ## TRUMP: I never said walk away. I wouldn't want to walk away. I want them to pay us much more money. We cannot afford to subsidize... 
    ## RUBIO: How much? 
    ## TRUMP: A lot. I'll negotiate a lot more money than you'll ever get. 
    ## As far as John Kerry is concerned, there has been no tougher critic of this man, I think he negotiated one of the worst deals in the history of our country, the Iran deal, where they get their $150 billion and all of the other things that take place. 
    ## It is a disaster for this country, and speaking of Israel, it's a disaster for Israel. I'm no fan of John Kerry. 
    ##  
    ## BLITZER: Hold on, hold on, Governor. 
    ## Senator Cruz. 
    ## CRUZ: You know, it's interesting, Donald just said that he never came out in favor of toppling Gadhafi in Libya. Well, he stated that in an interview that will be on our Web site, tedcruz.org. 
    ## You can see and hear the exact words from Donald's mouth. And I assume when he sees that interview, maybe he forgot about it, but I assume Donald will apologize where he sees that he said exactly that. 
    ## With regard to John Kerry, I will say John Kerry's foreign policy has been a disaster for decades. That's why I voted against him when he came up. And the fact that Donald Trump would write him a check and support him against George W. Bush shows exceptionally poor foreign policy judgment. 
    ## And I'll give one more example on Israel. When the Obama administration canceled civilian air flights into the national of Israel, when Hamas was raining rockets down on them, I publicly asked, is this an economic boycott against Israel? 
    ## The next day Michael Bloomberg, another New York billionaire, got on a plane, a commercial flight, and flew to Israel from London. Together the heat and light that was put on the State Department was so great that within 36 hours they lifted the ban on air flights into Israel. 
    ## During that entire battle, and indeed during every battle on Israel the natural question is, where was Donald? If this is something he cares about, why has he supported anti-Israel politicians from Jimmy Carter to Hillary Clinton to John Kerry for four decades? 
    ## If you care about Israel, you don't write checks to politicians who are undermining Israel. Instead you stand and support the national security of America and the alliance with Israel. 
    ##  
    ## KASICH: There's a critical point that needs to be made here. 
    ## BLITZER: Governor, Governor, Governor, he attacked Mr. Trump. 
    ## Mr. Trump has a right to respond. 
    ## TRUMP: Well, look, my response is very simple. There is nobody on this stage that has done more for Israel than I have. Nobody. You might say, you might talk, you're politicians, all talk, no action. 
    ## I've been watching it all my life. You are all talk and no action. 
    ## CRUZ: Then name one specific thing you've done. 
    ## TRUMP: What I've seen up here — I mean, first of all, this guy is a choke artist, and this guy is a liar. You have a combination... 
    ## RUBIO: This guy always goes for... 
    ## TRUMP: You have a combination of factors. He can't do it... 
    ## RUBIO: This is so typical. 
    ## TRUMP: ... for the obvious reason, and he can't do it because he doesn't know how to tell the truth. Other than that, I rest my case. 
    ##  
    ## BLITZER: One at a time, gentlemen. 
    ## Governor Kasich, you have the floor. Governor... 
    ## BLITZER: You will have a response. But I promised Governor Kasich he could respond. 
    ## CARSON: Can somebody attack me, please? [laughter and applause] 
    ## KASICH: There's something — I want to — I want to point out something here today that is — it's so critically important — about how the Obama administration has really done such a ridiculous, feckless job here in foreign policy. 
    ## First of all, we should have been supporting the rebels long ago. They could have taken Assad out, and because we did nothing, the Russians are in, and they're sitting in the catbird seat. 
    ## We should have been helping them. I'm thankful that the aid trucks are finally getting into Syria. But the fact is, had we had acted, we would have solved that problem. 
    ## Now, let's talk about Libya. Libya didn't go down because there was some people revolution. Hillary Clinton, Samantha Power and all these other people convinced the president to undermine Gadhafi. They undermined him, and now they have created a cesspool in Libya. 
    ## And let me just say to you — we have ISIS beginning get — get a foothold in Libya. We're gonna have to deal with it. There are not many major cities in Libya. They're on the coast, which — mostly, it's desert, but it's a problem. 
    ## Then we have ISIS in — in Syria, and we have ISIS in Iraq. Because this administration has not had a strong and firm foreign policy, we are going to inherit — one of us here is going to inherit a total mess... 
    ## BLITZER: All right... 
    ## KASICH: ... and we're going to have to work our way out of it, including... 
    ## BLITZER: Let's continue. 
    ## KASICH: ... the need to arm the Ukrainians. They have been ignored, and we need to help them as well... 
    ## BLITZER: Let us continue. 
    ## KASICH: ... and assert ourselves as America. 
    ## BLITZER: Let's continue the questioning on ISIS. Maria. 
    ## CRUZ: Hold on, Wolf. You said I got a response. 
    ## BLITZER: You'll have a chance. Maria will pick up... 
    ## CRUZ: Hold on. He called me a liar. You're saying I can't respond to being called a liar? 
    ## BLITZER: Go ahead and respond. 
    ## CRUZ: You know, what we're seeing with Donald is actually the pattern of Washington — the pattern of Washington deal makers, which is they make promises, they break their words, and then when anyone calls them on it, they call you a liar. 
    ## And so that's Donald's pattern over and over again. He said, for example, seven months ago — this is Donald speaking, quote — "I, Donald Trump, was a member of the establishment." 
    ## There's a reason Harry Reid thinks he's the best Republican up here. There's a reason Jimmy Carter said he would support Donald Trump over me, because he said Donald Trump is malleable, he has no fixed set of beliefs......whereas Ted Cruz is not malleable. And every time anyone points at Donald's actual record... 
    ## BLITZER: Thank you. 
    ## CRUZ: ... what he said on national television, Donald yells "liar." Let me tell you something — falsely accusing someone of lying is itself a lie... 
    ## BLITZER: Go ahead, Mr. Trump. 
    ## CRUZ: ... and it's something Donald does daily. 
    ## BLITZER: Go ahead, Mr. Trump. 
    ## TRUMP: I watched — I watched......the lobbyists. I watched what this man did to Dr. Ben Carson, who I respect, in Iowa, where he said that Ben Carson is out of the race — he has left Iowa and he's out of the race. And I thought it was disgraceful. 
    ## And got a lot of votes because of that — a lot of votes. Took them away from Ben Carson. I watched that. Probably took them away from me, too. But I watched it. 
    ## I also watched where he did a forum that looked like it came right out of a government agency, and it said on top, "Voter Violation," and then it graded you......and it scared the hell out of people, and it said the only way you clear up the violation, essentially, is to go and vote for Ted Cruz. I watched that fraudulent document, and I said it's the worst thing I've ever seen in politics. 
    ## To me, that was even worse than what he did to Ben. 
    ## BLITZER: Senator Cruz... 
    ## TRUMP: I know politicians — I know politicians, believe it or not, better than you do. And it's not good. 
    ## CRUZ: I believe it. No, no. I believe you know politicians much better than I do, because for 40 years, you've been funding liberal Democratic politicians. And by the way... 
    ## TRUMP: I funded you. I funded him. Can you believe it? 
    ## CRUZ: ... the reason is — you're welcome to have the check back. 
    ## TRUMP: I funded this guy. I gave him a check. 
    ## CRUZ: Yeah, you gave me $5,000. 
    ## TRUMP: I gave him a check. He never funded me. 
    ## CRUZ: And — and by the way, let's be clear. 
    ## Donald claims — Donald claims to care about... 
    ## TRUMP: You know why? I didn't want to, but he sent me his book with his autograph... 
    ## CRUZ: Donald. Donald. Donald. I understand rules are very hard for you. They're very confusing. 
    ## TRUMP: Mr. Trump, you're doing a great job. I have his book. 
    ##  
    ## TRUMP: Thank you — thank you for the book. Go ahead. 
    ## CRUZ: Donald, you can get back on your meds now. 
    ## TRUMP: This is a lot of fun up here tonight, I have to tell you. 
    ## Thank — thank you for the book. I really appreciate it. 
    ## CRUZ: Donald — Donald, relax. 
    ## TRUMP: Go ahead. I'm relaxed. You're the basket case.Go ahead. 
    ## CRUZ: Donald... 
    ## TRUMP: Go ahead. Don't get nervous. 
    ## CRUZ:... 
    ## TRUMP: Go ahead. 
    ## CRUZ: I promise you, Donald, there's nothing about you... 
    ## TRUMP: I've seen you. 
    ## CRUZ: ... that makes anyone nervous. 
    ## TRUMP: You're losing so badly you — I want to... 
    ## CRUZ: You know, people are actually watching this at home. 
    ## TRUMP: ... I — you don't know what's happening. 
    ## BLITZER: Gentlemen, gentlemen. 
    ## CRUZ: Wolf, I'm going to ask my time not be deducted when he's yelling at me. 
    ## BLITZER: You've gotta stop this.The latest debate — gentlemen, please. 
    ## CRUZ: Hold on, I'm going to get my answer. He doesn't get to yell the whole time.  
    ## BLITZER: I want to move — I want to move on. These are the rules. 
    ## CRUZ: Excuse me, he called me a liar, then interrupted the whole time. Am I allowed to... 
    ## Wolf, do I not get a response? Do I not get a response without being interrupted? 
    ## BLITZER: You'll get — you'll get plenty of response, so stand by. 
    ## CARSON: My name was mentioned. 
    ## BLITZER: I want to talk — I want to talk about ISIS right now, and the federal government — how much best to keep Americans safe from ISIS. 
    ## There's a huge battle underway right now between the tech giant Apple and the federal government. The federal government wants Apple to unlock the phone used by that San Bernardino terrorist to prevent future attacks. Apple has refused, saying it would compromise the security of all of its customers. And just this afternoon, they went to court to block the judge's order. 
    ## Dana Bash, pick up the questioning. 
    ## BASH: Senator Rubio, you say it's complicated, and that, quote, "Apple isn't necessarily wrong to refuse the court order." Why shouldn't investigators have everything at their disposal? 
    ## RUBIO: No, in fact what I have said is the only thing — the FBI made this very clear 48 hours ago — the only thing they are asking of Apple is that Apple allow them to use their own systems in the FBI to try to guess the password of the San Bernardino killer. Apple initially came out saying, "We're being ordered to create a back door to an encryption device." That is not accurate. 
    ## The only thing they're being asked to do, and the FBI made this very clear about 48 hours ago, is allow us to disable the self- destruct mode that's in the Apple phone so that we can try to guess using our own systems what the password of this killer was. 
    ## And I think they should comply with that. If that's all they're asking for, they are not asking for Apple to create a back door to encryption. 
    ## BASH: So just to be clear, you did say on CNN a couple of weeks ago this is a complicated issue; Apple is not necessarily wrong here. 
    ## RUBIO: Because at the time, Apple was portraying that the court order was to create a back door to an encryption device. 
    ## BASH: But just to be clear — just to be clear, if you are president, would you instruct your Justice Department to force Apple to comply or not? 
    ## RUBIO: To comply with an order that says that they have to allow the FBI the opportunity to try to guess the password? 
    ## BASH: Correct. 
    ## RUBIO: Absolutely. That Apple phone didn't even belong to the killer. It belonged to the killer's employer who have agreed to allow him to try to do this. That is all they're asking them to do is to disable the self-destruct mode or the auto-erase mode on one phone in the entire world. But Apple doesn't want to do it because they think it hurts their brand. 
    ## Well, let me tell you, their brand is not superior to the national security of the United States of America. 
    ## BASH: Senator Cruz, Apple CEO Tim Cook says this would be bad for America. Where do you stand: national security or personal privacy? 
    ## CRUZ: Well, as you know, at that same CNN forum, both Marco and I were asked this question. His answer, he was on both sides of the fence. He's now agreeing with me. And so I'm glad. 
    ## What I said is yes, Apple should be forced to comply with this court order. Why? Because under the Fourth Amendment, a search and seizure is reasonable if it has judicial authorization and probable cause. In this instance, the order is not put a back door in everyone's cell phone. If that was the order, that order would be problematic because it would compromise security and safety for everyone. 
    ## I would agree with Apple on that broad policy question. But on the question of unlocking this cell phone of a terrorist, we should enforce the court order and find out everyone that terrorist at San Bernardino talked to on the phone, texted with, e-mailed. And absolutely, Apple doesn't have a right to defy a valid court order in a terrorism investigation. 
    ## BASH: Dr. Carson, Tim Cook, again, the CEO of Apple, says that this would be bad for America. What do you think? 
    ## CARSON: I think allowing terrorist to get away with things is bad for America. 
    ## You know, we have the — we have a Constitution. We have a Fourth Amendment. It guards us against illegal and unreasonable search and seizure. But we have mechanisms in place with the judicial system that will allow us to gain material that is necessary to benefit the nation as a whole or the community as a whole. And that's why we have FISA courts and things of that nature. 
    ## So absolutely, I would — I would expect Apple to comply with the court order. If they don't comply with that, you're encouraging chaos in our system. 
    ## BASH: Mr. Trump... 
    ## KASICH: I want to weigh in on this please. I want to just tell you that the problem is not right now between the administration and Apple. You know what the problem is? Where's the president been? You sit down in a back room and you sit down with the parties and you get this worked out. You don't litigate this on the front page of the New York Times, where everybody in the world is reading about their dirty laundry out here. 
    ## The president of the United States should be convening a meeting, should have convened a meeting with Apple and our security forces. And then you know what you do when you're the president? You lock the door and you say you're not coming out until you reach an agreement that both gives the security people what they need and protects the rights of Americans. This is a failure of his leadership to get this done as an executive should be doing it. 
    ## And I'll tell you, that's why you want a governor. I do this all the time. And we reach agreements all the time. Because as an executive, you've got to solve problems instead of fighting on the front page of the newspaper. 
    ## ARRARÁS: Thank you, Governor. 
    ## KASICH: Thank you. 
    ## ARRARÁS: Mr. Trump, you have been very vocal about securing the Mexican border, but ISIS has called upon its supporters to conduct attacks on our neighbor to the North, Canada. 
    ## As a matter of fact, U.S. officials have warned that it is the Canadian border which is the most significant threat. You have said that you will not build a wall in Canada. When it comes to national security, and the threat of terrorism, why does Mexico need a wall, and Canada doesn't? Isn't that, like, closing the front door, and leaving the back door open? 
    ## TRUMP: First of all, you're talking about a border that's many, many times longer. You're talking about a massive border. 
    ## We have far less problem with that border than we do with our Southern border, and tremendous amounts — you know, I won, I had the privilege of winning by a landslide, by the way, New Hampshire. 
    ## You go to New Hampshire, the first thing they talk about is heroin and drugs pouring in. And, you wouldn't think this beautiful place — it's beautiful. With the trees and the roads, and the countryside. Their biggest problem is heroin, and it's such a shame to see it. 
    ## They're pouring in from the Southern border, so I'm talking about great security. I'm talking about a wall that can absolutely be built, and I'll build it on time, on budget. It'll be a very high wall, a great wall. It's going to be built, it's going to be built. It's going to be paid for by Canada, by the way — maybe I'll get Canada to pay? Got to be paid for by Mexico. 
    ## The problem with Canada, you're talking about a massively long piece. You're talking about a border that would be about four times longer. It would be very, very hard to do, and we — it is not our biggest problem. I don't care what anyone says. It is not our big problem. Our big problem is not only people coming in, and in many cases the wrong people, it's the tremendous amount of drugs that are coming in. 
    ## ARRARÁS: I want to talk to you, Senator Rubio, about Puerto Rico. As you know, Puerto Rico's in the midst of financial collapse, unable to pay it's debt of $72 billion dollars. Puerto Rico is asking for bankruptcy protection which would give Puerto Rico, and Puerto Ricans, which are U.S. citizens, you know that — the tools to restructure the debt. That is the same debt the other 50 states have. 
    ## You oppose granting Puerto Rico that bankruptcy protection. You say that it is only a last resort measure, but the government of Puerto Rico has said that bankruptcy is it's last resort. That that's where they are now. How do you explain this very strong stance to the hundreds of thousands of Puerto Ricans that vote across the U.S. , and particularly in your state of Florida? 
    ## RUBIO: Sure, because bankruptcy doesn't work unless you change the way you're operating, or you're going to be bankrupt again. And, the problem with Puerto Rico is it's economy is not growing. It has a massive exodus of professionals and others that are leaving to my home state of Florida, and all over the country. 
    ## They're coming to the mainland from Puerto Rico because the economy there is not growing, it's too expensive to do business there. The tax rate is too high. The government regulations are too extensive. 
    ## This year alone, with all the problems they're having, they barely cut their budget from one year to the next. So, I think the leadership on the island has to show their willingness to get their house in order and put in place measures allow the economy there to grow again. If the economy of Puerto Rico does not grow they will never generate the revenue to pay this debt, or the billions of dollars in unfunded liabilities that they have on their books of promises they've made to future generations to make payments. 
    ## So, yes, if they do all of those things then we can explore the use of bankruptcy protection, but not as the first resort, which is what they're asking for, because it will not solve the problems on the island and you're going to continue to see hundreds of thousands of people leave that beautiful place, and coming to the mainland. 
    ## They're United States citizens, they're obviously entitled to do so, and we welcome them, but we would also prefer to see a Puerto Rico that once again is growing economically, and is robust. And, the leaders in charge there now are doing a terrible job. 
    ## Their previous governor, Louis Fortuno was doing a great job until he barely lost that election to......to someone who has taken a big government stance once again... 
    ## BLITZER: ... Senator, thank you very much. 
    ## I want our viewers to stay with us right now, including the last pitch in the final debate before Super Tuesday. [applause and cheering] 
    ##  
    ## BLITZER: Welcome back to the University of Houston. It's time now for closing statements. All of you will have 30 seconds. Dr. Carson, we'll start with you. 
    ## CARSON: Well first of all, I want people to think about what kind of leader do you want and what kind of person do you want your kids to emulate. Think about that. 
    ## Secondly, several years ago, a movie was made about these hands. These hands by the grace of God have saved many lives and healed many families. And I'm asking you tonight, America, to join hands with me to heal, inspire and revive America. If not us, who? And if not now, when? 
    ## BLITZER: Governor Kasich. 
    ## KASICH: Well, the last USA Today poll had me beating Hillary Clinton by 11 points, more than anybody on this stage. Secondly, I hope you saw tonight that executive experience really matters. It matters in terms of growing our economy, balancing budgets, cutting taxes, reforming regulations. I've done it in Washington, I've done it in Ohio, and I can go back to Washington and do it again. 
    ## But I hope you also noticed tonight that I do have the foreign policy experience, not just a few years, but a lot of years in working with some of the great, great minds in this country to develop the expertise, the confidence, the firmness, the toughness and the ability to bring people together. 
    ## I hope you all think about giving me your vote. I would appreciate it very much. And I tell you, we won't have to spend time figuring what we're going to do. I will hit the ground running and we will get America moving again. Thank you all very much. 
    ## BLITZER: Senator Rubio.  
    ## RUBIO: Well, thank you for having us tonight. You know, this campaign has come a long way. It was just a few months ago there were 15 or 11 us on the stage and now it's narrowed and the votes are starting to count. And we have an incredible decision to make, not just about the direction of America, but the identity of our party and of the conservative movement. 
    ## The time for games is over. 
    ## I know you've had a lot of choices to make, but now it's time to narrow it down. And I'm asking you to get behind me, go on our Web site and join you our effort, marcorubio.com, so we can bring an end to this silliness, this looniness, and once again re-embrace all the things that made America and the Republican Party the bearer of the conservative movement in this country. 
    ## BLITZER: Senator Cruz. 
    ## CRUZ: Washington deals are bankrupting this country. There are several deal-makers on this stage but there is only one person who has consistently stood up to both parties, fighting for the American people against the Washington deals. 
    ## If I'm elected president, on the first day in office I will rescind every single illegal and unconstitutional executive action. I will instruct the Department of Justice to open an investigation into Planned Parenthood and prosecute any criminal violations. 
    ## I will instruct every federal agency that the persecution of religious liberty ends today. I will rip to shreds the Iranian — catastrophic Iranian nuclear deal. And I will begin the process of moving the American embassy in Israel to Jerusalem. 
    ## We will repeal Obamacare, abolish of IRS, secure the border, and bring back jobs. 
    ## BLITZER: Mr. Trump. 
    ## TRUMP: Thank you. 
    ## Nobody knows politicians better than I do. They're all talk, they're no action, nothing gets done. I've watched it for years. Take a look at what's happening to our country. 
    ## All of the things that I've been talking about, whether it's trade, whether it's building up our depleted military, whether it's taking care of our vets, whether it's getting rid of Common Core, which is a disaster, or knocking out Obamacare and coming up with something so much better, I will get it done. Politicians will never, ever get it done. And we will make America great again. Thank you. 
    ## BLITZER: Mr. Trump, thank you. 
    ## And thanks to each of the candidates, on behalf of everyone here at CNN and Telemundo. We also want to thank the Republican National Committee and the University of Houston. My thanks also to Hugh Hewitt, Maria Celeste, and Dana Bash. 
    ## Super Tuesday is only five days away.

Now that we have this as one “document”, we need to load it into the
**quanteda** package for processing and analysis.

    require(quanteda, warn.conflicts = FALSE, quietly = TRUE)

    ## Warning: package 'quanteda' was built under R version 3.4.4

    ## Package version: 1.3.4

    ## Parallel computing: 2 of 8 threads used.

    ## See https://quanteda.io for tutorials and examples.

    transcriptCorpus <- corpus(transcriptText, metacorpus = list(source = "http://www.presidency.ucsb.edu/ws/index.php?pid=111634", 
        notes = "10th Republican candidate debate, Houston TX 2016-02-25"))

    summary(transcriptCorpus)

    ## Corpus consisting of 906 documents, showing 100 documents:
    ## 
    ##     Text Types Tokens Sentences
    ##    text1    18     27         1
    ##    text2    16     17         1
    ##    text3    42     51         2
    ##    text4    43     65         5
    ##    text5    32     37         2
    ##    text6    46     52         3
    ##    text7    16     16         1
    ##    text8     7      7         1
    ##    text9     6      6         1
    ##   text10     8      8         1
    ##   text11     6      6         1
    ##   text12     8      9         1
    ##   text13    14     15         1
    ##   text14    18     19         1
    ##   text15     7      7         1
    ##   text16    14     17         2
    ##   text17    13     13         1
    ##   text18     0      0         0
    ##   text19    14     15         1
    ##   text20    30     37         2
    ##   text21    58     90         5
    ##   text22     0      0         0
    ##   text23    30     34         2
    ##   text24    21     24         3
    ##   text25    40     48         2
    ##   text26    36     46         3
    ##   text27     5      5         1
    ##   text28   100    166        10
    ##   text29     5      5         1
    ##   text30    32     45         2
    ##   text31    57     83         5
    ##   text32    29     35         1
    ##   text33     5      5         1
    ##   text34     6      6         1
    ##   text35    49     65         4
    ##   text36    21     26         1
    ##   text37    26     42         2
    ##   text38     5      6         1
    ##   text39    35     54         6
    ##   text40    23     29         3
    ##   text41    14     15         1
    ##   text42    21     42         4
    ##   text43    56     75         4
    ##   text44    27     30         2
    ##   text45    39     55         3
    ##   text46    28     32         4
    ##   text47    29     34         1
    ##   text48    39     49         2
    ##   text49    48     89         6
    ##   text50    23     28         2
    ##   text51    24     27         1
    ##   text52    38     51         2
    ##   text53    37     56         2
    ##   text54    64     98         4
    ##   text55    45     59         1
    ##   text56    13     14         1
    ##   text57    34     37         1
    ##   text58     5      5         1
    ##   text59    45     52         2
    ##   text60     5      5         1
    ##   text61    47     68         4
    ##   text62    37     45         3
    ##   text63    37     47         2
    ##   text64    27     32         2
    ##   text65    32     36         1
    ##   text66    35     42         1
    ##   text67    34     43         2
    ##   text68     6      6         1
    ##   text69    36     46         2
    ##   text70    22     34         1
    ##   text71    46     64         4
    ##   text72    35     40         3
    ##   text73    31     34         2
    ##   text74    35     50         1
    ##   text75    31     50         6
    ##   text76     6      8         1
    ##   text77    18     20         3
    ##   text78    39     54         2
    ##   text79    35     52         3
    ##   text80    36     51         1
    ##   text81     7      9         1
    ##   text82    18     22         2
    ##   text83    10     12         1
    ##   text84     0      0         0
    ##   text85    22     31         2
    ##   text86    12     14         1
    ##   text87    21     22         2
    ##   text88    15     17         2
    ##   text89    11     11         1
    ##   text90    20     25         2
    ##   text91     6     11         3
    ##   text92    40     51         6
    ##   text93    13     19         2
    ##   text94    11     11         1
    ##   text95    33     38         5
    ##   text96     8      8         1
    ##   text97    17     20         2
    ##   text98     5      5         1
    ##   text99    12     14         2
    ##  text100    43     54         2
    ## 
    ## Source: http://www.presidency.ucsb.edu/ws/index.php?pid=111634
    ## Created: Tue Sep 25 14:36:27 2018
    ## Notes: 10th Republican candidate debate, Houston TX 2016-02-25

Our goal in order to analyze this by speaker, is to redefine the corpus
as a set of documents defined as a single speech acts, with a document
variable identifying the speaker. We accomplish this through the
`corpus_segment()` method:

    transcriptCorpus <- corpus_segment(transcriptCorpus, pattern = "\\s*[[:upper:]]+:\\s+", 
        valuetype = "regex")

    summary(transcriptCorpus, 10)

    ## Corpus consisting of 531 documents, showing 10 documents:
    ## 
    ##      Text Types Tokens Sentences   pattern
    ##   text2.1    14     15         1 BLITZER: 
    ##  text14.1    16     17         1 BLITZER: 
    ##  text16.1    12     15         2 BLITZER: 
    ##  text19.1    12     13         1 BLITZER: 
    ##  text25.1    38     46         2  CARSON: 
    ##  text27.1     3      3         1 BLITZER: 
    ##  text28.1    98    164        10  KASICH: 
    ##  text29.1     3      3         1 BLITZER: 
    ##  text30.1    30     43         2   RUBIO: 
    ##  text33.1     3      3         1 BLITZER: 
    ## 
    ## Source: http://www.presidency.ucsb.edu/ws/index.php?pid=111634
    ## Created: Tue Sep 25 14:36:28 2018
    ## Notes: corpus_segment.corpus(transcriptCorpus, pattern = "\\s*[[:upper:]]+:\\s+", valuetype = "regex")

We can clean up the patterns:

    docvars(transcriptCorpus, "pattern") <- stringi::stri_trim_both(docvars(transcriptCorpus, 
        "pattern"))
    docvars(transcriptCorpus, "pattern") <- gsub(":", "", docvars(transcriptCorpus, 
        "pattern"))
    docvars(transcriptCorpus, "pattern") <- gsub("ARRARAS", "ARRASAS", docvars(transcriptCorpus, 
        "pattern"))

    transcriptCorpus <- corpus_subset(transcriptCorpus, !(pattern %in% c("MALE", 
        "COOPER")))

    summary(transcriptCorpus, 10)

    ## Corpus consisting of 531 documents, showing 10 documents:
    ## 
    ##      Text Types Tokens Sentences pattern
    ##   text2.1    14     15         1 BLITZER
    ##  text14.1    16     17         1 BLITZER
    ##  text16.1    12     15         2 BLITZER
    ##  text19.1    12     13         1 BLITZER
    ##  text25.1    38     46         2  CARSON
    ##  text27.1     3      3         1 BLITZER
    ##  text28.1    98    164        10  KASICH
    ##  text29.1     3      3         1 BLITZER
    ##  text30.1    30     43         2   RUBIO
    ##  text33.1     3      3         1 BLITZER
    ## 
    ## Source: http://www.presidency.ucsb.edu/ws/index.php?pid=111634
    ## Created: Tue Sep 25 14:36:28 2018
    ## Notes: corpus_segment.corpus(transcriptCorpus, pattern = "\\s*[[:upper:]]+:\\s+", valuetype = "regex")

    table(docvars(transcriptCorpus, "pattern"))

    ## 
    ##      aid  ARRARÁS     BASH  BLITZER   CARSON     CRUZ   HEWITT   KASICH 
    ##        1       25       26      103       14       67       24       25 
    ##      now question    RUBIO    stand    TRUMP 
    ##        2        1       91        1      151

Now we can start to perfom some analysis on the text. Who spoke the most
in the debate, in words?

    par(mar = c(5, 6, 0.5, 1))
    barplot(sort(ntoken(texts(transcriptCorpus, groups = "pattern"), removePunct = TRUE)), 
        horiz = TRUE, las = 1, xlab = "Total Words Spoken")

![](text-analysis-of-the-10th-republican-presidential-candidate-debate-using-r-and-the-quanteda-package_files/figure-markdown_strict/barplot-1.png)

The `ntoken()` function does the work here of counting the tokens in the
vector of texts returned by the call to
`transcriptCorpus, groups = "pattern"`, which extracts the texts from
our segmented corpus and concatenates all texts by speaker. This results
in a vector of the same 10 speakers as in our tabulation above. Passing
through the `removePunct = TRUE` option in the `ntoken()` call sends
this argument through to `tokenize()`, meaning we will not count
punctutation characters as tokens. (See `?quanteda::tokenize` for
details.)

If we wanted to go further, we convert the segmented corpus into a
*document-feature matrix* and apply one of many available psychological
dictionaries to analyze the tone of each candidate’s remarks. Here I
will demonstrate using the Regressive Imagery Dictionary, from
Martindale, C. (1975) *Romantic progression: The psychology of literary
history.* Washington, D.C.: Hemisphere. The code below automatically
downloads a version of this dictionary in a format prepared for the
WordStat software by Provalis, available from
<http://www.provalisresearch.com/Download/RID.ZIP>. **quanteda** can
import dictionaries formatted for WordStat, using the `dictionary()`
function.

Here, we will apply the RID dictionary to find out who used what degree
of “glory”-oriented language. (You might be able to guess the results
already.)

    # get the RID from the Provalis website
    RIDzipfile <- download.file("http://provalisresearch.com/Download/RID.ZIP", 
        "RID.zip")
    unzip("RID.zip")
    data_dictionary_RID <- dictionary(file = "RID.CAT", format = "wordstat")
    file.remove("RID.zip", "RID.CAT", "RID.exc")

    ## [1] TRUE TRUE TRUE

This is a nested dictionary object with three primary keys:

    names(data_dictionary_RID)

    ## [1] "PRIMARY"   "SECONDARY" "EMOTIONS"

We can inspect the category we will use (“Glory”) by looking at the last
sub-key of the third key, “Emotions”.

    tail(data_dictionary_RID[["EMOTIONS"]], 1)

    ## Dictionary object with 1 key entry.
    ## - [GLORY]:
    ##   - admir*, admirabl*, adventur*, applaud*, applaus*, arroganc*, arrogant*, audacity*, awe*, boast*, boastful*, brillianc*, brilliant*, caesar*, castl*, conque*, crown*, dazzl*, eagl*, elit*, emperor*, empir*, exalt*, exhibit*, exquisit*, extraordinary*, extrem*, fame, famed, famou*, foremost*, geniu*, glor*, gold*, golden*, grandeur*, great*, haughty*, hero*, homag*, illustriou*, kingdom*, magestic*, magnificent*, majestic*, majesty*, nobl*, outstand*, palac*, pomp*, prestig*, prid*, princ*, proud*, renown*, resplendent*, rich*, royal*, royalty*, sceptr*, scorn*, splendid*, splendor*, strut*, sublim*, superior*, superiority*, suprem*, thron*, triump*, victor*, victoriou*, victory*, wealth*, wonder*, wonderful*

Now we will extract just the candidates, using the `subset()` method for
a corpus class object, and then create a document-feature matrix from
this corpus, grouping the documents by speaker as we did before.

    transcriptCorpusCands <- corpus_subset(transcriptCorpus, pattern %in% c("TRUMP", 
        "CRUZ", "RUBIO", "KASICH", "CARSON"))
    canddfm <- dfm(transcriptCorpusCands, groups = "pattern")

Because the texts are of different lengths, we want to normalize them
(by converting the feature counts into vectors of relative frequencies
within document):

    canddfmRel <- dfm_weight(canddfm, "prop")

Now we are in a position to apply the RID to the dfm, which matches on
the “glob” formatted wildcard expressions that form the values of the
RID in our `data_dictionary_RID` object.

    canddfmRelRID <- dfm_lookup(canddfmRel, data_dictionary_RID)
    head(canddfmRelRID)

    ## Document-feature matrix of: 5 documents, 43 features (35.8% sparse).

    topfeatures(canddfmRelRID, n = 20)

    ##          SECONDARY.SOCIAL_BEHAVIOR          SECONDARY.ABSTRACT_TOUGHT 
    ##                        0.157500708                        0.147910985 
    ##          SECONDARY.TEMPORAL_REPERE          SECONDARY.INSTRU_BEHAVIOR 
    ##                        0.114819561                        0.096903091 
    ##     PRIMARY.REGR_KNOL.CONCRETENESS         SECONDARY.MORAL_IMPERATIVE 
    ##                        0.093807007                        0.047455687 
    ##                EMOTIONS.AGGRESSION           PRIMARY.SENSATION.VISION 
    ##                        0.035784142                        0.027675943 
    ##    PRIMARY.REGR_KNOL.BRINK-PASSAGE                SECONDARY.RESTRAINT 
    ##                        0.016433574                        0.015484508 
    ##                    SECONDARY.ORDER                 EMOTIONS.AFFECTION 
    ##                        0.015023172                        0.014702072 
    ##          PRIMARY.ICARIAN_IM.HEIGHT                     EMOTIONS.GLORY 
    ##                        0.014527536                        0.012315041 
    ## PRIMARY.DEFENSIVE_SYMBOL.PASSIVITY               PRIMARY.NEED.ORALITY 
    ##                        0.009243941                        0.007875402 
    ##            PRIMARY.SENSATION.SOUND    PRIMARY.SENSATION.GEN_SENSATION 
    ##                        0.007381209                        0.007078389 
    ##    PRIMARY.DEFENSIVE_SYMBOL.VOYAGE           PRIMARY.ICARIAN_IM.WATER 
    ##                        0.006837386                        0.006468089

We could probably spend a whole day analyzing this information, but
here, let’s simply compare candidates on their relative use of language
in the “Emotions: Glory” category of the RID. We do this by slicing out
the feature with this label, converting this to a vector (as there is no
`drop = TRUE` option for dfm indexing), and then reattaching the
document labels to this vector so that it will be named vector. We then
send it to the `dotchart()` for a simple plot, showing that Trump was by
far the highest user of this type of language.

    canddfmRelRID[, "EMOTIONS.GLORY"]

    ## Document-feature matrix of: 5 documents, 1 feature (0% sparse).
    ## 5 x 1 sparse Matrix of class "dfm"
    ##         features
    ## docs     EMOTIONS.GLORY
    ##   CARSON    0.001769912
    ##   CRUZ      0.003099814
    ##   KASICH    0.001573564
    ##   RUBIO     0.001710864
    ##   TRUMP     0.004160888

    glory <- as.vector(canddfmRelRID[, "EMOTIONS.GLORY"])
    names(glory) <- docnames(canddfmRelRID)
    dotchart(sort(glory), xlab = "RID \"Glory\" terms used as a proportion of all terms", 
        pch = 19, xlim = c(0, 0.005))

![](text-analysis-of-the-10th-republican-presidential-candidate-debate-using-r-and-the-quanteda-package_files/figure-markdown_strict/dotchart-1.png)