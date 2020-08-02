# Implementation of Quilt Fabric Estimator

In this project, I have designed a calculator to estimate the amount of yardage of fabrics you would need to purchase to produce a desired quilt (or portions of a quilt), based on number, size and shape of rectangular or triangular pieces.
     
Many kinds of electronic quilting software packages are available on the market.  Here is a very popular one for $240: https://electricquilt.com/online-shop/electric-quilt-8/.  (These pricey packages allow you to *design* your quilts, as well as providing fabric calculators.)  This student has found FREE websites that offer some fabric calculation.  Here is one: https://app.prequilt.com/fabric-calculator.  You will notice that the only triangle calculation offered is the "half-square triangle," or HST.

When you cut quilt fabric, you must add 1/4" "seam allowance" to all sides of what you want want your pieces to be when they are combined into a quilt.  For example, if you need squares that have a "finished size" of 2 inches, you simply cut out 2 1/2-inch squares.  42" of usable width of fabric on a bolt, and it's pretty easy to stand in JoAnn fabric and do the mental math as to how much yardage you'd need ("gee, I can get 16 x 2 1/2 squares from a 2 1/2 wide strip of fabric; I need 160 squares, so I need 10 x 2 1/2-inch strips, which is 25 inches, which is just less than 3/4 yards of fabric, so I'll buy 7/8 yd to be safe--boom").

Not so with triangles.  Even HSTs that finish at 2x2 on the short edges need to be cut as 2 7/8" squares first, because of the additional allowance in the middle, and the pointy edges.  Forget 30-60-90 or equilateral triangles.

**The problem is that fabric is expensive.**  So you want to purchase enough fabric to complete your project, with maybe an extra 1/8 or 1/4 yard in case of mistakes, but you CANNOT have even a 1/2" too little, because you will ruin your project.  So you need a ball-park estimator.

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


