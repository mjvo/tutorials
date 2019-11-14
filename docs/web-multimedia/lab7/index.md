---
title: Lab 7
description: Mad Libs in Javascript
authors:  Mark Olson
updated:  2019-11-11
---

!!! danger ""
    Due:  _**_First_ Lab Section meeting of the week of November 18<sup>th</sup>**_

## **Mad Libs in Javascript**

### **Overview**

In this lab exercise, you will create a simple ["Mad Libs"-style game](https://en.wikipedia.org/wiki/Mad_Libs) using Javascript. If you are not familiar with Mad Libs, the idea is that you ask a player to give you a collection of words (nouns, adjectives, verbs, adverbs) without knowledge of how they will be used.  They are then plugged into a pre-determined narrative, often with hilarious or nonsensical results.

[Example of a Mad Lib](https://www.thespruce.com/thmb/FZj6HAcBY9XCG-GhcPAWSRILxX4=/960x0/filters:no_upscale():max_bytes(150000):strip_icc()/d03ab930f0f65a3b58aa6770095b1fef-574e443f5f9b58516598688c.jpg)

The game play should proceed as follows:

1. First, the user should be presented with an HTML form asking for a certain number of generic nouns, adjectives, verbs, adverbs as well as some specific elements:  part of the body, favorite song title, article of clothing, type of food, etc.  Be creative.

2.  Second, the user clicks the "Submit" button, which hides the form and displays the Mad Lib.

3.  Finally, hilarity ensues.  Or not.  

### **Requirements for a &#10003;**

1. Create a new folder in your `labs` folder called `lab07` and a new `index.html` file in that folder.

1. Do what is necessary to bring the page's aesthetic into alignment with your midterm website (link to your midterm.css; add the navigation / footers as necessary, etc.).

1.  On paper or in a separate text document, write an innocuous short story, indicating in parentheses where the Mad Libs words will go.  For example,

    <blockquote>On a (adjective) November day, I woke up to the (adjective) smell of (liquid) percolating in the (room of a house) downstairs.  I wrapped my (body part) in a warm (article of clothing) and sat down to (verb) my Javascript homework.  I typed (adverb) as thoughts of Thanksgiving (verb - past tense) in my head.</blockquote>
    
    Our process, in essence, is to create an HTML form to collect the nouns, verbs, adverbs, adjectives, both general and specific, which we will then use javascript to insert into our narrative.  [You'll of course need to come up with an original story of your own.]

1. Return to your HTML document.  Inside the `<body>` element, add the following, which will lay the groundwork for an HTML form for collecting nouns, verbs, adverbs, adjectives, both general and specific:

    ```html
    <form id="formData">
        <fieldset>
            <legend>Javascript Mad Libs</legend>
            
        </fieldset>
    </form>
    ```

1. Beneath the `<legend>` element, create labelled input fields for each of the words that your form will collect to insert into the narrative.  Your Mad Lib should collect at least eight (8) words.  For example:

    ```html hl_lines="4 5 6 7"
     <form id="formData">
        <fieldset>
            <legend>Javascript Mad Libs</legend>
            <label>Adjective: </label><input type="text" name="adjective1"><br>
            <label>Article of Clothing: </label><input type="text" name="clothing"><br>
            <label>Liquid: </label><input type="text" name="liquid"><br>
            <label>Part of the Body: </label><input type="text" name="bodypart"><br>
            <!-- etc etc etc -->
        </fieldset>
    </form>
    ```

1. Finally, before the closing `</fieldset>` tag, add a button for submitting the Mad Libs form.  In a future step, we will add an event listener to that button to process the form fields' contents:

    ```html hl_lines="2"
            <!-- etc etc etc -->
            <button id="submit">Submit</button> 
        </fieldset>
    </form>
    ```

1.  Next, add a `<div>` element with an id of "content" below the closing `</form>` element.

    ```html hl_lines="5"
            <!-- etc etc etc -->
            <button id="submit">Submit</button> 
        </fieldset>
    </form>
    <div id = "content"></div>
    ```

1. Now it's time to turn your attention to the javascript that will render this dynamic.  All of your javascript should be written _before_ the closing `</body>` tag and enclosed within a `<script>` tag:

    ```html
    <script type="text/javascript">
        // javascript goes here
    </script>
    ```

1. Let's first create a function that will, once fully implemented, grab all of the form input values and insert them into your MadLib narrative.    

    A function is declared with the keyword `function`, followed by an arbitrary (but hopefully semantically informative) function name, followed by a pair of parenthesis (which optionally contain the function's arguments as variables).  The function itself follows those parenthesis, enclosed in curly brackets.  In this case, we'll create a function called "goMadLib" in betweeen the opening and closing `<script>` tags:
    
    ```html hl_lines="3 4 5"
    <script type="text/javascript">
        // javascript goes here
        function goMadLib() {
        // function javascript goes here
        }
    </script>
    ```

1.  Now let's populate the function with javascript that selects each form input and assign its contents to its own variable.  You _could_ do this by assigning each `<input>` an ID and then make use of the `getElementById` method.  But we already have assigned each `<input>` element a "name" atttribute, so rather than create redundant IDs, we can instead grab the entire form by its ID and then make use of the [form elements collection](https://www.w3schools.com/jsref/coll_form_elements.asp) from the DOM.  [But if you prefer to stick with the `getElementByID()` method, that's fine too!  There are several valid ways to accomplish most things in javascript.]

    First, declare a variable to hold the form object.  Recall that the form was assigned an ID of "formData" (using proper camelcase):

    ```javascript hl_lines="2"
    function goMadLib() { 
        let formData = document.getElementById("formData");
    }
    ```

    Now that we have the form object, we can access each form element and its value by using the  the `.namedItem()` method on the [form elements collection](https://www.w3schools.com/jsref/coll_form_elements.asp):

    ```javascript hl_lines="4 5"
    function goMadLib() { 
        let formData = document.body.getElementById("formData");
   
        let adj1 = formData.elements.namedItem("adjective1").value;
        let clothing = formData.elements.namedItem("clothing").value;
        // continue with your remaining inputs
    }
    ```

    You'll of course modify the above with your own form input names and make sure you assign all form inputs to their own respective values
    
    !!! note
        The `.item()` method works the same as the `.namedItem()` method
        
        ```javascript
        let variable = formData.elements.item("input_name").value;
        ```

1. Now that each form input's value is stored in its own variable, we can use those variables and some string operations to generate our MadLib text:

    ```javascript hl_lines="8 9 10 11 12"
    function goMadLib() { 
        let formData = document.getElementById("formData");
   
        let adj1 = formData.elements.namedItem("adjective1").value;
        let clothing = formData.elements.namedItem("clothing").value;
        // continue with your remaining inputs (not all listed above)

        let newContent = "On a " + adj1 + " November day, ";
        
        newContent += "I woke up to the " + adj2 + " smell of " + liquid + " percolating in the " + room + " downstairs.";
        
        newContent += "I wrapped my " + bodypart + " in a warm " + clothing + " and sat down to ...";
    }
    ```

1. As a final step in our `goMadLib()` function, we'll use the `innerHTML()` method to insert the MadLib into the "content" DIV:

    ```javascript hl_lines="5"
        newContent += "I wrapped my " + bodypart + " in a warm " + clothing + " and sat down to ...";
   
        // this sets the innerHTML of our content DIV to the concatenated 
        // string contained in the variable newContent 
        document.getElementById("content").innerHTML = newContent;
    }
    ```

1.  Finally, you can add an event listener to the "submit" button to call the `goMadLib()` function on the click event.  Importantly, this needs to be **outside** of the `goMadLib()` function.   So add the following _after_ the closing curly bracket of your function:

    ```javascript hl_lines="3 4"
        document.getElementById("content").innerHTML = newContent;
    }
    let submitBtn = document.getElementById("submit");
    submitBtn.addEventListener("click", goMadLib);
    ```

    !!! warning "SUPER IMPORTANT"

        By default, clicking button within a `<form>` element will force a page reload, as if you are submitting your form data to a remote database.  We need to override that behavior for this exercise, as we don't want the page to reload.   Add the following `onsubmit` attribute to the `<form>` element in your HTML:

        ```html
        <form id="formData" onsubmit="event.preventDefault();">
        ```

If you encounter errors, use the Javascript Console in Chrome's Developer tools to troubleshoot.



### **Reach Goals**

Some suggestions to extend your work in this lab:

* Instead of storing each verb, adjective, noun in its own variable, store them in an array using the array `push()` method and then use array indexes to display them in the Mad Lib.

* Create a "reset" button that clears out all data, clears the story, and returns the input form to its empty state.

* Use Javascript for basic form validation, ensuring that no fields are left blank.  Hint: this can be added to form inputs using the "onblur" event through the `addEventListener()` method.
