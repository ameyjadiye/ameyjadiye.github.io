---
layout: page
title: About Amey
---
<img src="https://avatars.githubusercontent.com/u/2662046" class="align-right" style="border-radius: 50%;" width="300" alt="">


Hello and welcome! My name is **Amey Jadiye**, and I'm thrilled to have you here.

I am a Director at [UBS](https://en.wikipedia.org/wiki/UBS){:target="_blank"} with a passion for Software Engineering and Electronics :heart: . With <span id="experience-years"></span>+ years of experience in Technology Industry, I have honed my skills in Java, Cloud, Multithreading, Low Latency stuff and AI.


What truly drives me is the love and attraction towards the machines. its fascinating to see how we write the code or create small electronics circuit or jot down few mechanical components and collectively they work to acomplish trivial to extramly complex task!

Throughout [My career](https://in.linkedin.com/in/ameyjadiye){:target="_blank"}, I have had the opportunity to work with startups to very big organizations. These experiences have not only helped me develop a strong foundation in the Software Industry, but also taught me the importance of Collbration, Communication, Networking and Innovations.

Beyond my professional life, I like playing with my <span id="son-age"></span> year old son Prakhar! I also truly enjoy spending time on personal software and electronics projects which include AI, embedded circuits, home automation and IoT.

My goal with this website is to share my experiences with life and some work I do in silo. Feel free to explore my blog and subscribe to a newsletter or contact me. If you have any questions or would like to connect, please don't hesitate to mail me on <a href="mailto:amey@jadiye.in"> amey@jadiye.in </a> – [Public key](http://amey.jadiye.in/files/ameyjadiye_gpg_key.asc){:target="_blank"} (1403E215).

Thank you for taking the time to learn more about me. I look forward to connecting with you!

> _I have no special talent. I am only passionately curious. – Albert Einstein_

<script>
  // Calculate years of experience (started November 2010)
  const experienceStartDate = new Date(2010, 11, 20); // November 2010 (months are 0-indexed)
  const currentDate = new Date();
  let experienceYears = currentDate.getFullYear() - experienceStartDate.getFullYear();
  if (currentDate < new Date(currentDate.getFullYear(), experienceStartDate.getMonth(), experienceStartDate.getDate())) {
    experienceYears--;
  }
  document.getElementById('experience-years').textContent = experienceYears;

  // Calculate son's age (born July 2020)
  const sonBirthDate = new Date(2020, 7, 7); // July 2020 (months are 0-indexed)
  let sonAge = currentDate.getFullYear() - sonBirthDate.getFullYear();
  if (currentDate < new Date(currentDate.getFullYear(), sonBirthDate.getMonth(), sonBirthDate.getDate())) {
    sonAge--;
  }
  document.getElementById('son-age').textContent = sonAge;
</script>
