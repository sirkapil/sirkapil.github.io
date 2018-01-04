---
layout: post
title: Model based test automation - test cases
tags: [test automation, page object]
excerpt_separator: <!--more-->
---

The test cases define the test scenarios. They should be written in the domain language of the target application.
The way of writing test cases can be anything between using a gherkin language (like [cucumber](https://cucumber.io/)) to use plain programmatic functions. Using a custom DSL language is an option as well (see for example [MPS](https://www.jetbrains.com/mps/)). Even if one uses plain functions it is important to pay attention to the code readability. See the following example from the Selenium Getting Started in Java.

<!--more-->

{% highlight java linenos=table%}
public static void main(String[] args) {
    // Create a new instance of the html unit driver
    // Notice that the remainder of the code relies on the interface,
    // not the implementation.
    WebDriver driver = new HtmlUnitDriver();

    // And now use this to visit Google
    driver.get("http://www.google.com");

    // Find the text input element by its name
    WebElement element = driver.findElement(By.name("q"));

    // Enter something to search for
    element.sendKeys("Cheese!");

    // Now submit the form. WebDriver will find the form for us from the element
    element.submit();

    // Check the title of the page
    System.out.println("Page title is: " + driver.getTitle());

    driver.quit();
}
{% endhighlight %}

If we hide the implementation in a model class, the testcase becomes more readable and clear.

{% highlight java linenos=table%}
public static void main(String[] args)
{
    GoogleHomePageObject homePage = new GoogleHomePageObject();
    homePage.OpenPage();
    homePage.SetSearchText("Cheese!");
    homePage.DoSearch();
    System.out.println("Page title is: " + homePage.getTitle());
    homePage.ClosePage();
}

public class GoogleHomePageObject
{
    WebDriver driver = new HtmlUnitDriver();

    public void OpenPage()
    {
        driver.get("http://www.google.com");
    }

    public void ClosePage()
    {
        driver.quit();
    }

    public void SetSearchText(String textToSet)
    {
        WebElement element = driver.findElement(By.name("q"));
        element.sendKeys(textToSet);
    }

    public void DoSearch(String textToSet)
    {
        WebElement element = driver.findElement(By.name("q"));
        element.submit();
    }

    public String GetTitle()
    {
        return driver.getTitle();
    }
}
{% endhighlight %}

We can make a further step and use the page object pattern to its fullest. The public functions of the model will give back the model object itself. In this way we can chain the function calls which improves the readability once again.

{% highlight java linenos=table%}
public static void main(String[] args)
{
    GoogleHomePageObject homePage = new GoogleHomePageObject();

    homePage
        .OpenPage()
        .SetSearchText("Cheese!")
        .DoSearch();

    System.out.println("Page title is: " + homePage.getTitle());

    homePage.ClosePage();
}

public class GoogleHomePageObject
{
    WebDriver driver = new HtmlUnitDriver();

    public GoogleHomePageObject OpenPage()
    {
        driver.get("http://www.google.com");
        return this;
    }

    public GoogleHomePageObject ClosePage()
    {
        driver.quit();
        return this;
    }

    public GoogleHomePageObject SetSearchText(String textToSet)
    {
        WebElement element = driver.findElement(By.name("q"));
        element.sendKeys(textToSet);
        return this;
    }

    public GoogleHomePageObject DoSearch(String textToSet)
    {
        WebElement element = driver.findElement(By.name("q"));
        element.submit();
        return this;
    }

    public String GetTitle()
    {
        return driver.getTitle();
    }
}
{% endhighlight %}

Using an automation framework like JUnit, NUnit etc the function readability improves again.

{% highlight java linenos=table%}

public class TestClass
{
    GoogleHomePageObject homePage;
    String titleToSet = "Cheese!";

    @Before
    public void TestSetup()
    {
        homePage = new GoogleHomePageObject();
    }

    @After
    public void TestTeardown
    {
        homePage.ClosePage();
    }

    @Test
    public void TitleCheckingTestCase()
    {
        Assert.assertEquals(
            titleToSet,
            homePage
                .OpenPage()
                .SetSearchText(titleToSet)
                .DoSearch()
                .getTitle());
    }
}
{% endhighlight %}

The test cases should primarily use the functions which are provided publicly by the model objects of the interaction code layer.

#### How to organize the test cases
The way of organizing the test cases is depending on the target application and the used test case definition solution.
With a .net example a suite of test cases tends to be a class. A suite of test suites is a project, and a solution with its projects covers the target application. In java a suite would be a class, and a package would be a suite of suites.

Within the test class, the usual "unit-test writing techniques" should apply. (see this great book by Roy Osherove: [Art of Unit Testing](https://www.manning.com/books/the-art-of-unit-testing-second-edition))

What test should be in a single suite? This is again highly dependent of the target application, the size and the complexity of the software components. Usually it is a good starting point to have the tests in a suite which are related to a single functionality.