<svelte:head>
  <title>Resume</title>
</svelte:head>

<section class="education">
    <h2>Education</h2>
    <article>
        <h3>Massachusetts Institute of Technology (MIT)</h3>
        <div class="info"><h4>Computer Science and Engineering</h4>
            <p>Expected Graduation: <time datetime="2026-05">May 2026</time></p></div>
    </article>
</section>

<hr>

<section class="personal-projects">
    <h2>Projects</h2>
    <article class="personal-project">
        <h3>Software Design 6.104</h3>
        <div class="project-info">
            <img src="images/craftfolio.png" alt="craftfolio">
            <p><b><a href="https://craft-folio.vercel.app/" target="_blank">Craftfolio</a></b> is a web app designed for beginner crafters looking to confidently start and complete creative fiber projects. 
                Our app targets a unique, underserved audience of new crafters who often feel overwhelmed by the complexity of project planning and lack of accessible resources. 
                Craftfolio empowers users by providing an intuitive project tracker and resource management tools tailored specifically to beginners working with different fibers.
                Unlike existing crafting communities that focus mainly on showcasing finished projects, 
                Craftfolio offers a collaborative space for planning, sharing, and discussing project materials.
                <br>
                Key features include personalized project filters based on available sources, ratings to simplify finding useful guides, and sepcial comments fostering a 
                supportive and interactive environment. Craftfolio's value lies in bridging the gap between inspiration and execution, helping users efficiently manage 
                crafting resources and build confidence through shared expertise.</p>
        </div>

    </article>
    <hr class="dotted">
    <article class="personal-project">
        <h3>How to Ship Almost Anything with AI</h3>
        <div class="project-info">
            <img src="images/dishiq.png" alt="dishiq">
            <div>
                <p>
                    <b><a href="https://dish-iq.streamlit.app/" target="_blank"> DishIQ</a></b> is an innovative app that transforms how you navigate menus. Here's what it does:
                    <br>
                    Extract Key Details Using advanced AI, DishIQ scans restaurant menus to extract:
                    <ul>
                        <li>Ingredients: Know exactly what's in each dish.</li>
                        <li>Prices: Stay within your budget effortlessly.</li>
                        <li>Dish Images: If unavailable, it generates mouthwatering, realistic dish visuals to help you decide.</li>
                    </ul>
                    Tailored Recommendations DishIQ takes dietary restrictions seriously. Whether you're gluten-free, vegan, allergic to nuts, or simply avoiding certain ingredients, the app:
                    <ul>
                        <li>Flags potential allergens and cross-contamination risks.</li>
                        <li>Provides questions to ask the waiter for clarity on preparation methods.</li>
                        <li>Highlights dishes that fit your preferences while warning you about risky ones.</li>
                    </ul>
                <!-- </p> -->
            </div>
        </div>
        
    </article>
</section>

<style>
article {
  margin: 0 2em;
}

.project-info {
  display: grid;
  gap: 1em;
  grid-template-columns: 1fr 1fr;
}

.project-info img {
  /* max-width: 200px; */
}

.education > article{
  display: flex;
  gap: 2em;
}

.education > article > h3 {
  flex: 1;
}
</style>