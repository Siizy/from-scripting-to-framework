# From Scripting to Framework course on TestAutomationU.com

## Chapter 1 - Machine Setup

1. Requirements
    - .NET Core version 2.2 (latest at time of recording) or greater
    - VS Code

2. Make a new project called `scripting-to-framework` and open it in VS Code

3. Install Extensions in VS Code
    - C# by Microsoft
    - PackSharp by Carlos Kidman

4. Open the Command Palette for each of these commands
    - `PackSharp: Create New Project` > select `Class Library` > call the Project "Framework"
    - `PackSharp: Create New Project` > select `Class Library` > call the Project "Royale"
    - `PackSharp: Create New Project` > select `NUnit 3 Test Project` > call the Project "Royale.Tests"
    - `PackSharp: Create New Project` > select `Solution File` > call the Project "StatsRoyale"

> NOTE: The Solution (.sln) file will manage the Project (.csproj) files while the Project files handle their own packages and dependencies. As you add things, this is all handled for you! Very cool.

5. Add the Projects to the Solution. Run these commands in the Terminal:
    - `$ dotnet sln add Framework`
    - `$ dotnet sln add Royale`
    - `$ dotnet sln add Royale.Tests`

6. Build the Solution so we know everything is working
    - `$ dotnet build`

7. Open the `UnitTest1.cs` file in the Royale.Tests project
    - C# will "spin up" so you can start coding in C# and get helpful completions, hints, etc.
    - VS Code will ask if you want "Required assets to build and debug". Add them by clicking the "Yes" button.
        - If you do not get this immediately, try closing VS Code and reopening the project.
        - This will add a `.vscode` folder to your solution, but this is required to run and debug the tests.

8. Run the Tests
    - `$ dotnet test`
    - This will run all the tests, but you only have one right now. It should pass.


## Chapter 2 - Script Some Tests

> NOTE: Our application under test (website) is https://statsroyale.com

1. Change the name of the Test Class and Test Method so they make more sense
    - Test Class from `Tests` to `CardTests`
    - File name from `Tests.cs` to `CardTests.cs`
    - Test Method from `Test1()` to `Ice_Spirit_is_on_Cards_Page()`

2. Install Selenium NuGet (package) with PackSharp
    - Open Command Palette > select `PackSharp: Bootstrap Selenium` > add to `Royale.Tests`, our Test Project
    - If you open the `Royale.Tests.csproj` file, you will see that Selenium packages have been added
    - You will also see a `_drivers` directory is added at the Workspace root

> NOTE: This command installs the **latest** versions of chromedriver and the Selenium packages.

3. Use Selenium in our CardTests.cs file
    - Within the CardTests class, add the `IWebDriver driver;` field to the top
    - Resolve the error by hovering the red line and click on the lightbulb
        - The first option will want you to add the `using OpenQA.Selenium;` statement. Select that one
        - The error will go away and you will see that using statement is added automatically

4. SetUp and TearDown methods
    - The [SetUp] method is run *before each* test. Change the method name from `Setup()` to `BeforeEach()`
    - Add a [TearDown] method which runs *after each* test. Call this method `AfterEach()`

5. Within AfterEach(), add:
    - `driver.Quit();`
    - This will close the driver after each test is finished

6. Within BeforeEach(), add:
    - `driver = new ChromeDriver(<path-to-chromedriver>);`
    - This will open a new Chrome browser for every test
    - "Point" the ChromeDriver() to wherever you store your `chromedriver` or `chromedriver.exe`.

> NOTE: Everyone manages their drivers (like `chromedriver`, `geckodriver`, etc.) differently. Use your preferred method.

7. Write the first test. The steps are:
    1. Go to https://statsroyale.com
    2. Click on Cards link
    3. Assert Ice Spirit is displayed

8. For the second test, the steps are:
    1. Go to https://statsroyale.com
    2. Click on Cards link
    3. Click on Ice Spirit card
    4. Assert the basic headers are correct. These headers are:
        - Name ("Ice Spirit")
        - Type ("Troop")
        - Arena ("Arena 8")
        - Rarity ("Common")

9. There's a lot of code in this one, so make sure to pause and replay as much as you need :)


## Chapter 3 - Page Object Model

Follow the video to for an explanation on the `Page Object Model` and `Page Map Pattern`.

1. Within the Royale project, create a `Pages` directory. This is where all of our Page objects will live.

2. Move the `Class1.cs` file into `Pages` and rename it to `HeaderNav.cs`

3. Within the file, rename `Class1` to `HeaderNav` and then make another class called `HeaderNavMap`

4. Use PackSharp to restructure our packages and dependencies so we leverage Framework and Royale projects
    1. Move Selenium to the `Framework` project
        - Open Command Palette > `PackSharp: Bootstrap Selenium` > select `Framework`
    2. Remove Selenium from `Royale.Tests` project
        - Open Command Palette > `PackSharp: Remove Package` > select `Royale.Tests` > select `Selenium.Support`
        - Also remove `Selenium.WebDriver`

5. Framework is our base, so we want the projects to reference each other in a linear way.

    Framework -> Royale -> Royale.Tests

    `Royale.Tests` will reference `Royale` which references `Framework`

    - Open Command Palette > `PackSharp: Add Project Reference` > select `Royale.Tests` > select `Royale`
    - Open Command Palette > `PackSharp: Add Project Reference` > select `Royale` > select `Framework`

6. Now we can bring in `using OpenQA.Selenium` in our `HeaderNav.cs` file

> NOTE: The rest of this video is very "code-heavy", so make sure to follow along there

7. The naming convention for Pages and Page Maps is very simple. If you have a Home page, then you would do this:
    - Page => `HomePage`
    - Map => `HomePageMap`

8. In the video, there is a "jump" from `11:22` to `11:25` where I am able to use the `Map.Card()` immediately. At this point, you will have an error. All you need to do is:
    - Add the `public readonly CardPageMap Map;` field at the top of the `CardsPage` class and the Constructor as well
    - You will see the needed code at `11:43`. Sorry about that!

9. Card Details Page and Map
    - Take a moment to pause the video and copy the code to move forward

10. At the end of the video, run your second test. It should fail! Your challenge is to solve this error so the test passes.
