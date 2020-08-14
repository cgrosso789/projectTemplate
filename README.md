# Implementation of Quilt Fabric Estimator

## Intro
In this project, I have designed an estimator to calculate the amount of yardage of fabrics you would need to purchase in order to produce a desired quilt (or portions of a quilt), based on number, size and shape of rectangular or triangular pieces in "blocks" (the typical unit of construction) of a quilt.  For example, here is a diagram of a quilt block: https://www.pinterest.com.mx/pin/543880092498520547/.  If the "finished size" of the block in the diagram is 12 inches, most of the pieces that make up the block are either 2-inch finished squares, or 2" finished "half-square triangles" (HSTs).  If you know you will have, say, 64 of these blocks in your quilt, you can figure out how many of each piece in each color you will need to complete the entire quilt.

## Motivation
Many kinds of electronic quilting software packages are available on the market.  Here is a very popular one for $240: https://electricquilt.com/online-shop/electric-quilt-8/.  (These pricey packages allow you to *design* your quilts, as well as providing fabric calculators.)  This student has found FREE websites that offer some fabric calculation.  Here is one: https://app.prequilt.com/fabric-calculator.  You will notice that the only triangle calculation offered is for the HST, which severely curtails the app's usefulness.

When you cut quilt fabric, you must add 1/4" "seam allowance" to all sides of what you want want your pieces to be when they are combined into a quilt.  For example, if you need squares that have a "finished size" of 2 inches, you simply cut out 2 1/2-inch squares.  42" of usable width of fabric on a bolt, and it's pretty easy to stand in JoAnn fabric and do the mental math as to how much yardage you'd need ("gee, I can get 16 x 2 1/2-inch squares from a 2 1/2-inch wide strip of fabric; I need 160 squares, so I need 10 x 2 1/2-inch strips, which is 25 inches, which is just less than 3/4 yards of fabric, so I'll buy 7/8 yd to be safe--boom").

Not so with triangles.  Even HSTs that finish at 2x2 on the short edges need to be cut as 2 7/8" squares first, because of the additional allowance at the diagonal middle, and the "pointy corners."  Forget 30-60-90, equilateral or scalene triangles.

**The problem is that fabric is expensive.**  Therefore, you want to purchase enough fabric to complete your project, with maybe an extra 1/8 or 1/4 yard in case of mistakes, but you CANNOT have even a 1/4" too little, because you will be unable to complete your project, and then possibly unable to purchase more of the same fabric.  So you need a ball-park estimator.  This estimator would be useful not only for the projects you have designed from scratch, but also to recalculate fabric requirements given in purchased patterns or directions in quilt books.  As a quilter, I have made lots of patterns that have included mistakes in their fabric requirements.  Most call for far too much fabric--one pattern I made cost me nearly double what I would have spent if I had figured out the requirements myself.  I've also made at least two in which they underestimated fabrics (fortunately, the fabrics came out of my "stash" and I had more than enough on hand).

## About the Design
To develop my estimator, I determined 6 main kinds of shapes about which I would want to be able to input information into a system, and for which I would want to get yardage estimation out.  These shapes are:
- HST (half-square triangle)
- QST (quarter-square triangle)
- RightTriangle (a right triangle whose short sides are not the same)
- Square
- Rectangle
- ScaleneTriangle (a triangle whose three sides are unequal, but this will work for any triangle without a right angle, or, in other words, that you cannot "pair up" into a rectangle or square with another).

(In the design of my estimator, I used trigonometry to calculate the augmented lengths of the sides of triangles.  Most of the trig I used came from the dusty circular files in my mind, placed there back in high school.  Reminders of how SSS and ASA are implemented came from the following sources:
- https://www.dummies.com/education/math/trigonometry/use-the-law-of-cosines-with-sss/
- https://www.mathsisfun.com/algebra/trig-solving-asa-triangles.html)

To build the software for the estimator, I used two Design Patterns: the **Builder Pattern** and the **Template Pattern**.  (A very helpful treatise on the Builder Pattern came to me from https://www.geeksforgeeks.org/builder-design-pattern/).  The Builder Pattern turned out to be perfect for this project.  I had to not only estimate the fabric, but also give brief cutting directions, so that whomever uses the estimator would have equally-critical information on how the fabric was intended to be cut.  I literally needed to BUILD a set of directions step-by-step.  In this program, then, the client uses the Builder interface (PieceBuilder) to polymorphically instantiate a concrete Builder (HstBuilder, QstBuilder, RightTriangleBuilder, RectangleBuilder, SquareBuilder, or ScaleneTriangleBuilder).  It then instantiates a QuiltMaker (Director) object, to which it passes the Builder object.  The QuiltMaker is then periodically called to set its own fields to prepare for the big bang, when all the fields are set and ready; then the QuiltMaker's buyFabric method is called by the client to launch the construction of the Fabric object.

In between the Builder interface and the concrete Builders, I placed an abstract class: Piecing Assistant.  This abstract class, together with the concrete Builder classes, provides a Template Pattern for the Builders, as five of the ten methods for each builder are exactly the same (and are therefore "moved up" as concrete methods into the abstract class), and the other five have hooks from the abstract class into the concrete classes.  Thus, the Template Pattern is excellent for code reduction and reuse.  Also, the abstract class contains several fields necessary to all the concrete classes.  The job of a concrete Builder, then, is to instantiate and build a Fabric object.  This object, which uses the FabricPlan interface, mainly just contains getters, setters and a toString method.  The Fabric object also stores a minCut double value, which it uses after the client is done obtaining user input, to add together and display all cuts of the SAME fabric for each of the DIFFERENT Fabric objects created during the information-collection phase.

In addition to the Builder Pattern and Template Method, I also used the **Factory Method** *in the client class* to easily instantiate the particular concrete Builder class.  (**Iterators** were also used in for-loops in the client class, but these iterators were supplied by Java.)


## Adding New Concrete Builder Classes:
It did not occur to me until I finished the coding and am now writing this README that I forgot all about diamond (parallelogram) shapes--I sure have made plenty of quilts with diamonds in them!!  Because I will most certainly use my estimator, I will definitely add diamonds as another PieceBuilder concrete class.  The only thing I have to change will be to write the class (a copy-paste-edit from another concrete Builder), make sure it extends Piecing Assistant, and update my drivers (QuiltClient will just need its queryFirstSide and pieceFactory methods, and the display of choices updated).  Simple!!

I almost never use fabric pieces that have curved edges; but if I did decide to add a CircleBuilder, the circle's radius would be exactly the same, functionally, as the side of a square.  I would use an **Adapter Pattern** that would call the SquareBuilder.

## How it Works:
If you run the UI Driver (QuiltClient), this is the basic logic:

define ArrayList<Fabric> fabricList (of Fabric objects)
define ArrayList<String> fabricNames (fabric names correspond to Strings in Fabric objects)
    while true:
        if not 1st time though loop:
    
## Terms:

- HST: Half-Square Triangle (hypotenuse of each triangle gets cut on the bias of the fabric)
- QST: Quarter-Square Triangle (hypotenuse of each triangle gets cut on the grain of the fabric)
- WOF: Width of Fabric (generally 42 inches)
- seam allowance: (nearly universally understood to be 1/4")

## Assumptions and Limitations:
- The UI graphic is console-based only at this time, but could be expanded one day.
- The output at the end does not currently get written to a file, but it could easily be, to print out and take to the store.  It is also nearly as easy, however, to cut and paste the console output to a Word document and then take THAT to the store.
- The program will limit the length of a side of a piece to 40-inches.  This is somewhat arbitrary, but if a long, skinny triangle is laid across the WOF, more than 2" could be consumed by the "pointy angles."  This is so unlikely to happen that this programming quilter chose for now not to worry about it.
- The WOF is not alterable at this time, but could easily be by adding an additional user query.  The WOF could then be Fabric-object-specific by only changing the PiecingAssistant abstract class, which would change the *calculations* based on the WOF variable.  On the other hand, if you wanted to log the specific WOF in the Fabric object by building in a field for it, you would have to change the PieceBuilder interface as well as the PiecingAssistant class, the FabricPlan interface as well as the Fabric class, and the Quiltmaker class, as well.
- No diamonds (parallelograms) yet--but they are coming!

## Additional credits:
- For help with Scanner usage and exception-handling: https://stackoverflow.com/questions/24414299/java-scanner-exception-handling


# How to compile the project

We use Apache Maven to compile and run this project. 

You need to install Apache Maven (https://maven.apache.org/)  on your system. 

Type on the command line: 

```bash
mvn clean compile
```

# How to create a binary runnable package 


```bash
mvn clean compile assembly:single
```


# How to run

There are two drivers in the system, and both are launched from Main.  The AlternativeQuiltClient runs the program with pre-set mock objects and outputs information to the console.  The QuiltClient has a console-based UI.  To run your prefered driver, so into Main and comment out the driver you DON'T want, and uncomment the driver you DO want.  The QuiltClient's launcher looks like this:
    QuiltClient qc = new QuiltClient();
    qc.goBuild()
    
And the AlternativeQuiltClient's launcher looks like this:    
    AlternateQuiltClient.doIt();


```bash
mvn -q clean compile exec:java -Dexec.executable="edu.bu.met.cs665.Main" -Dlog4j.configuration="file:log4j.properties"
```

We recommand the above command for running the project. 

Alternativly, you can run the following command. It will generate a single jar file with all of the dependencies. 

```bash
mvn clean compile assembly:single

java -Dlog4j.configuration=file:log4j.properties -classpath ./target/JavaProjectTemplate-1.0-SNAPSHOT-jar-with-dependencies.jar  edu.bu.met.cs665.Main
```


# Run all the unit test classes.


```bash
mvn clean compile test

```

# Using Findbugs 

To see bug detail using the Findbugs GUI, use the following command "mvn findbugs:gui"

Or you can create a XML report by using  


```bash
mvn findbugs:gui 
```

or 


```bash
mvn findbugs:findbugs
```


For more info about FindBugs see 

http://findbugs.sourceforge.net/

And about Maven Findbug plugin see 
https://gleclaire.github.io/findbugs-maven-plugin/index.html


You can install Findbugs Eclipse Plugin 

http://findbugs.sourceforge.net/manual/eclipse.html



SpotBugs https://spotbugs.github.io/ is the spiritual successor of FindBugs.


# Run Checkstyle 

CheckStyle code styling configuration files are in config/ directory. Maven checkstyle plugin is set to use google code style. 
You can change it to other styles like sun checkstyle. 

To analyze this example using CheckStyle run 

```bash
mvn checkstyle:check
```

This will generate a report in XML format


```bash
target/checkstyle-checker.xml
target/checkstyle-result.xml
```

and the following command will generate a report in HTML format that you can open it using a Web browser. 

```bash
mvn checkstyle:checkstyle
```

```bash
target/site/checkstyle.html
```


# Generate  coveralls:report 

You can find more info about coveralls 

https://coveralls.io/

```bash
mvn -DrepoToken=YOUR-REPO-TOCKEN-ON-COVERALLS  cobertura:cobertura coveralls:report
```


