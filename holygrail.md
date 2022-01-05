solution

```html
<header>
  <h1>Welcome to Hipster Haven</h1>
</header>
<div class="grid">
  <aside class="specials">
    <h3>Today's Specials</h3>
    <ul>
      <li>Gochujang Green Juice</li>
      <li>La Croix Tacos</li>
      <li>Banjo Brunch</li>
      <li>Ennui Affogato</li>
    </ul>
  </aside>
  <main>
    <h2>We were inspired by...</h2>
    <p>
      I'm baby fanny pack unicorn hammock chambray salvia venmo banjo listicle.
      IPhone stumptown keffiyeh marfa. Tofu bespoke paleo cardigan. Put a bird
      on it master cleanse post-ironic thundercats +1 air plant, jean shorts
      ennui truffaut biodiesel vegan tousled mlkshk distillery sartorial. Cloud
      bread bitters farm-to-table trust fund health goth, offal +1 succulents.
      Thundercats butcher slow-carb meditation.
    </p>
    <p class="text--faded">
      Jean shorts schlitz lomo affogato vice pitchfork. Wayfarers drinking
      vinegar meggings live-edge scenester gluten-free meh lumbersexual viral
      small batch forage. Vaporware typewriter ugh asymmetrical gastropub yr
      occupy. Gluten-free squid migas, snackwave master cleanse try-hard palo
      santo chillwave skateboard affogato plaid paleo fashion axe ugh. Direct
      trade blue bottle hoodie meditation pickled pok pok viral leggings
      post-ironic paleo sustainable squid fingerstache ramps.
    </p>
  </main>
  <aside class="about">
    <h3 class="about__header">About the Chef</h3>
    <p>
      Skateboard aesthetic venmo polaroid mlkshk disrupt irony scenester vinyl
      keffiyeh four dollar toast chia. Letterpress ramps echo park raw denim
      sustainable. Blue bottle jean shorts pop-up viral coloring book trust fund
      vape activated charcoal pork belly craft beer direct trade. 8-bit wolf
      crucifix typewriter taiyaki.
    </p>
    <p class="text--faded">Pork Belly Vegan Fingerstache</p>
  </aside>
</div>
<footer>All Rights Reserved Hipster Haven 2020</footer>
```

```css
/* You will be given some HTML for a restaurant website. Your job is to improve the existing code to meet your best practices and style it to look like the "holy grail" of web design layouts: full width header and footer, with a 3 column layout for the main content. The two outer columns of the main content ('Daily Specials' and 'About' sections) should be a fixed width, with the main content filling in the rest of the space.

After you've completed the layout, you will need to add styles to make it look like the provided [mock](https://imgur.com/a/fZvcWoh). You have complete liberty with the HTML markup, feel free to add and remove classes, change HTML tags, syntax, etc. The idea is to leave this code better than you found it, like a pull request review.

The end result should look like this (https://imgur.com/a/z8Zt65B) */

html {
  font-family: "Helvetica Neue", Arial, sans-serif;
  padding: 50px 20px;
}

h1 {
  text-align: center;
  letter-spacing: 2px;
}

h1,
h3 {
  text-transform: uppercase;
}

h2 {
  font-weight: normal;
}

ul {
  list-style: none;
  padding-inline-start: 0px;
  line-height: 1.8em;
}

/* grid solution */
/* .grid {
    display: grid;
    grid-template-columns: 200px 1fr 200px;
    grid-gap: 20px;
} */

/* flexbox solution */
.grid {
  display: flex;
}

header,
footer {
  flex-grow: 1;
  text-align: center;
}

aside {
  min-width: 200px;
  flex-basis: 200px;
  font-size: 0.9em;
}

aside p {
  color: #666;
}

main {
  margin: 0 50px;
}

footer {
  font-size: 0.5em;
  text-transform: uppercase;
}

/* .text--faded {
  color: grey;
} */

/* bonus points for pseudo selector */
p:last-of-type {
  color: grey;
}

/* .profile-pic {
	height: 50px;
	width: 50px;
	border-radius: 50px;
	background: grey;
	border: 5px solid #999;
	margin: auto;
} */

.about {
  text-align: center;
}

/* profile pic as pseudo element */
.about__header:after {
  content: "";
  display: block;
  height: 50px;
  width: 50px;
  border-radius: 50px;
  background: grey;
  border: 5px solid #333;
  margin: auto;
  margin-top: 1em;
}

@media (max-width: 776px) {
  .grid {
    flex-wrap: wrap;
  }

  aside {
    flex-basis: 100%;
    text-align: center;
  }

  main {
    margin: 0;
  }
}
```
