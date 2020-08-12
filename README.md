# Implementation of Quilt Fabric Estimator

In this project, I have designed an estimator to calculate the amount of yardage of fabrics you would need to purchase in order to produce a desired quilt (or portions of a quilt), based on number, size and shape of rectangular or triangular pieces in "blocks" (the typical unit of construction) of a quilt.  For example, here is a diagram of a quilt block: https://www.pinterest.com.mx/pin/543880092498520547/.  If the "finished size" of the block is 12 inches, most of the pieces that make up the block are either 2-inch finished squares, or 2" finished "half-square triangles" (HSTs).  If you know you will have, say, 64 of these blocks in your quilt, you can figure out how many of each piece you will need to complete the entire quilt.
     
Many kinds of electronic quilting software packages are available on the market.  Here is a very popular one for $240: https://electricquilt.com/online-shop/electric-quilt-8/.  (These pricey packages allow you to *design* your quilts, as well as providing fabric calculators.)  This student has found FREE websites that offer some fabric calculation.  Here is one: https://app.prequilt.com/fabric-calculator.  You will notice that the only triangle calculation offered is the HST.

When you cut quilt fabric, you must add 1/4" "seam allowance" to all sides of what you want want your pieces to be when they are combined into a quilt.  For example, if you need squares that have a "finished size" of 2 inches, you simply cut out 2 1/2-inch squares.  42" of usable width of fabric on a bolt, and it's pretty easy to stand in JoAnn fabric and do the mental math as to how much yardage you'd need ("gee, I can get 16 x 2 1/2-inch squares from a 2 1/2-inch wide strip of fabric; I need 160 squares, so I need 10 x 2 1/2-inch strips, which is 25 inches, which is just less than 3/4 yards of fabric, so I'll buy 7/8 yd to be safe--boom").

Not so with triangles.  Even HSTs that finish at 2x2 on the short edges need to be cut as 2 7/8" squares first, because of the additional allowance at the diagonal middle, and the "pointy corners."  Forget 30-60-90, equilateral or scalene triangles.

**The problem is that fabric is expensive.**  Therefore, you want to purchase enough fabric to complete your project, with maybe an extra 1/8 or 1/4 yard in case of mistakes, but you CANNOT have even a 1/4" too little, because you will be unable to complete your project, and then possibly unable to purchase more of the same fabric.  So you need a ball-park estimator.  This estimator would be useful not only for the projects you have designed from scratch, but also to recalculate fabric requirements given in purchased patterns or directions in quilt books.  As a quilter, I have made lots of patterns that have included mistakes in their fabric requirements.  Most call for far too much fabric--one pattern I made cost me nearly double what I would have spent if I had figured out the requirements myself.  I've also made at least two in which they underestimated fabrics (fortunately, the fabrics came out of my "stash" and I had more than enough on hand).

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

To build the software for the estimator, I used two Design Patterns: the Builder Pattern and the Template Method.  (A very helpful treatise on the Builder Pattern came to me from https://www.geeksforgeeks.org/builder-design-pattern/.)  The Builder Pattern turned out to be perfect for this project.  I had to not only estimate the fabric, but also give brief cutting directions, so that whomever uses the estimator would have equally-critical information on how the fabric was intended to be cut.  I literally needed to BUILD a set of directions step-by-step.  In this program, then, the client uses the Builder interface (PieceBuilder) to polymorphically instantiate a concrete Builder (HstBuilder, QstBuilder, RightTriangleBuilder, RectangleBuilder, SquareBuilder, or ScaleneTriangleBuilder), and instantiates a QuiltMaker object to which it passes the Builder object.  In between the Builder interface and the concrete Builders, I placed an abstract class: Piecing Assistant.  This abstract class together with the concrete Builder classes provide a Template Method for the Builders, as five of the ten methods for each builder are exactly the same (and are therefore "moved up" as concrete methods in the abstract class), and the other five have hooks from the abstract class into the concrete classes.  Also, the abstract class contains many fields necessary to all the concrete classes.  (The ScaleneTriangleBuilder overrides one concrete method in the abstract class.)  The job of the Builder, then, is to instantiate and build a Fabric object.  This object, which uses the FabricPlan interface, mainly just contains getters, setters and a toString method.  The Fabric object also stores a minCut double value, which it uses after the client is done obtaining user input, to add together and display all cuts of the SAME fabric.

Assumptions and limitations:
- The UI graphic is console-based only at this time, but could be expanded one day.


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


