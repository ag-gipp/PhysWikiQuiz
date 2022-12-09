## PhysWikiQuiz

We present [**PhysWikiQuiz**](https://physwikiquiz.wmflabs.org), a Physics Exam Question Generation and Test System. The system can generate physics questions from [Wikidata](https://wikidata.org) items, given a Formula Concept Name or QID (e.g., 'speed' or 'Q11376') user input. Each question contains comprehensive details of the involved physical quantities, and randomly generated numerical values. In a consecutive step, a student can input an answer. The system then checks the correctness of the user input in terms of quantity value and unit respectively. In the last stage, the system finally generates a correct explanation of the solution, including its calculation path.

[**PhysWikiQuiz**](https://physwikiquiz.wmflabs.org) is a web-based system, implemented with Flask, a micro web-framework written in Python. The required metadata for formulae is retrieved from [Wikidata](https://wikidata.org) by means of [SPARQL](https://www.w3.org/TR/rdf-sparql-query/) queries or [Pywikibot](https://www.mediawiki.org/wiki/Manual:Pywikibot).
The system is hosted by Wikimedia at [https://physwikiquiz.wmflabs.org](https://physwikiquiz.wmflabs.org).

## Motivation

Examination is the essential part in every student’s academic life. A significant portion of any physics examination is based upon formula based numerical examples. This system accelerates the process of examination preparation by generating large numbers of novel questions from the open and continuously evolving semantic knowledge-base [Wikidata](https://wikidata.org). The question generation helps students to have study material to train for exams and teachers, tutors, or professors to automatically prepare exam sheets, which significantly reduces their time efforts. Moreover, generating different questions and values for each student separately makes student cheating more difficult.

## Demo Examples

Demo of example formula concept **"speed"**:

![Demo speed](images/PhysWikiQuiz_demo_speed.gif)

Demo of example formula concept **"acceleration"**:

![Demo acceleration](images/PhysWikiQuiz_demo_acceleration.gif)

You can quickly check the system hosted at https://physwikiquiz.wmflabs.org.

You also find a video demonstration of the PhysWikiQuiz system and its evaluation [here](https://purl.org/physwikiquiz) (you will be redirected to YouTube).

## System Workflow

PhysWikiQuiz employs the open access semantic knowledge-base [Wikidata](https://wikidata.org) to retrieve Wikimedia community-curated physics formulae with identifier properties and units using their concept name as input. A given formula is then rearranged, i.e., solved for each occurring identifier by a Computer Algebra System (CAS) to create several question families. For each rearrangement, random identifier values are generated (in a specified range). Finally, the system compares the student's answer input to a CAS computed solution for both value and unit separately and generates an explanation text.

**The following diagram illustrates the fundamental workflow of the system.**

![Fundamental Workflow](images/PhysWikiQuiz-Workflow_white.png)

In module 1, formula and identifier data is retrieved from [Wikidata](https://wikidata.org). In module 2, the formula is rearranged using the python CAS [Sympy](https://www.sympy.org). In module 3, random values are generated for the formula identifiers. In module 4, the question text is generated from the available information. In module 5, the student's answer is compared to the system's solution. Finally, module 6 generates an explanation text for the student. In case some step or module can not be successfully executed, the user is notified, e.g., 'No Wikidata item with formula found'.

**Let us learn the workflow of the system with the example formula concept "acceleration"**.

The following figure shows an example expression tree for "acceleration". Below each of the identifier symbols a, v, and t, information about its properties (Wikidata item name and QID, unit dimension) is displayed.

![Workflow diagram for acceleration](images/PhysWikiQuiz_workflow_acceleration.png)

We now describe the module tasks using the example.

**Module 1** retrieves **formula and identifier information** from Wikidata properties.

 In our example case:
 * 'defining formula' (P2534): 'a=\frac{dv}{dt}'
 * 'in defining formula' (P7235): 'a'
 * 'has part' (P527) or 'calculated from' (P4934):
    * 'velocity' (Q11465)
    * 'duration' (Q2199864)

**Module 2** generates **various possible rearrangements** of the retrieved formula.

 In our example case:
 * <img src="https://render.githubusercontent.com/render/math?math=a = \frac{v}{t}">
 * <img src="https://render.githubusercontent.com/render/math?math=t = \frac{v}{a}">
 * <img src="https://render.githubusercontent.com/render/math?math=v = a \times t">

**Module 3** generates **random numerical values** for the unknown identifier variables and performs the required mathematical operations in order to calculate the **numerical value of the desired identifier**.

 In our example case:
   For the two available formula rearrangements <img src="https://render.githubusercontent.com/render/math?math=a = \frac{v}{t}">, <img src="https://render.githubusercontent.com/render/math?math=t = \frac{v}{a}">, and <img src="https://render.githubusercontent.com/render/math?math=v = a \times t">, the system calculates the respective right-hand side identifier values by performing the required multiplication or division operation. The result is later compared to the user input to check its correctness.​

**Module 4** generates a **well-structured question in natural language** by using the available names, symbols, and values for the occurring formula identifiers.

 In our example case:
   "What is the acceleration a, given velocity v = 4 m s^-1, duration t = 5 s?"

**Module 5 checks the solution value and unit** entered by the user and displays a correctness assessment.

 In our example case:
   If the user entered "4/5 m s^-2" value and unit are correct. If it was "4 m s^-2", only the unit is correct. If it was "4/5 m", only the value is correct.

**Module 6 generates an explanation text** with the formula (including source) and a calculation path for the student's understanding.

 In our example case:
   "Solution from www.wikidata.org/wiki/Q11376 formula a = v/t with 4/5 m s^-2 = 4 m s^-1 / 5 s ."

In the following, you can see the **final stage of the system**, after finishing all tasks:
   ![final stage](images/PhysWikiQuiz_acceleration.png)

## Number of Questions

In principle, PhysWikiQuiz does not depend on formula rearrangements and the workflow would be complete without them. However, they enhance the availability of additional question variations. In the case of our example "speed", using Sympy rearrangements also the other variables "distance" and "durations" can be queried, providing additional concept questions.

The number of questions generated by >>PhysWikiQuiz<< can be calculated as N_generated =  N_identifiers * R_values ^ (N_identifiers - 1)
with
N_identifiers: Number of identifiers in Formula Concept,
R_values = 10: Range for random identifier value generation (here from 1 to 10).

This leads to the following table:

| N_identifiers | N_generated   |
| ------------- |---------------|
| 1             | 1             |
| 2             | 20            |
| 3             | 300           |
| 4             | 4000          |
| 5             | 50000         |
| 6             | 600000        |
| 7             | 7000000       |
| 8             | 80000000      |
| 9             | 900000000     |
| 10            | 10000000000   |

It is evident that for large formulae, PhysWikiQuiz can generate a tremendous amount of question variations. But even for a small formula with 2 identifiers there are already 20 possibilities. On average, the formulae in the testset contain 3 identifiers, which leads to 300 potential questions per Formula Concept. Without rearrangements the possibilities need to be divided by the number of formula identifiers.

## Evaluation on Benchmark

We made a detailed PhysWikiQuiz system evaluation at each individual stage of its workflow. We carried out unit tests for the different modules and an integration test to assess the overall performance on a Formula Concept benchmark dataset.

### Benchmark Dataset

The open-access platform [MathMLben](https://mathmlben.wmflabs.org) stores and displays a benchmark of semantically annotated mathematical formulae. They were extracted from [Wikipedia](https://en.wikipedia.org), the [arXiv](https://arxiv.org) and the Digital Library of Mathematical Functions [DLMF](https://dlmf.nist.gov) and augmented by Wikidata markup. The benchmark can be used to evaluate a variety of MathIR tasks, such as the automatic conversion between different CAS or Mathematical Question Answering [MathQA](https://mathqa.wmflabs.org). In our PhysWikiQuiz evaluation, we employ a selection of 66 formulae (GoldID 310-375) from the MathMLben goldstandard. The Formula Concepts were extracted from Wikipedia articles using the formula and identifier annotation recommendation system [AnnoMathTeX](https://annomathtex.wmflabs.org).

The following table shows the PhysWikiQuiz system performance on the selected Formula Concept benchmark (MathMLben GoldID 310-375). For each concept, e.g., 'speed', the results of the different modules is displayed. At the bottom the total percentage of the available functionality is provided.

[Unit Test Module Workflow](images/unit_test_module_workflow.png)

All result tables and evaluation logs can be found [here](evaluation/module_workflow).

## Installation Dependencies

A deployed version of the system is available online, hosted by Wikimedia at https://physwikiquiz.wmflabs.org. Installing locally on your machine, the system mainly depends on the following python packages (for a full list see `requirements.txt`).

### Flask
The web framework Flask `version 0.12.2` is used as web framework middleware used as an interface between the frontend and the backend.
```
pip3 install Flask
```
### Requests
Requests `version 2.26.0` is an HTTP library designed to make HTTP requests simpler and more human-friendly.
```
pip3 install requests
```
### Pywikibot
Pywikibot `version 5.6.0`is used to extract the formula concept data from Wikidata: [https://tools.wmflabs.org/pywikibot](https://tools.wmflabs.org/pywikibot)
```
pip3 install pywikibot
```
### SPARQLWrapper
SPARQLWrapper `version 1.8.2` is a simple Python wrapper around a [SPARQL](https://www.w3.org/TR/sparql11-overview) service to remotely execute queries. It helps to create the query invokation and convert the result into a more manageable format.
```
pip3 install sparqlwrapper
```
### Sympy
The Computer Algebra System (CAS) Sympy `version 1.7.1` is used for the calculation module to get result values given a retrieved formula and inputs for the variables. 
```
apt-get install python3-sympy
```

### Latex2Sympy 
LaTeX2Sympy `version 1.6.2` is used to convert variants of LaTeX formula strings to a Sympy equivalent form.

1) ANTLR is used to generate the parser:
```
sudo apt-get install antlr4
```
2) Download latex2sympy from [https://github.com/augustt198/latex2sympy](https://github.com/augustt198/latex2sympy)

### **To clone or download the repository**
```
git clone https://github.com/ag-gipp/PhysWikiQuiz.git
```

## PhysWikiQuiz API for question generation
Example query using Wikidata item name ('speed'):
```
https://physwikiquiz.wmflabs.org/api/v1?name=speed
```
Example query using Wikidata item QID ('Q124164'):
```
https://physwikiquiz.wmflabs.org/api/v1?qid=Q124164
```

## API references

 * [Wikidata](https://wikidata.org): A SPARQL query to the [Wikidata Query Services API](https://query.wikidata.org) retrieves lists or properties of [Wikidata items](https://en.wikipedia.org/wiki/Wikidata#Items)
 * [VMEXT](https://vmext-demo.formulasearchengine.com/swagger-ui.html): LaTeX to SymPy formula conversion is done via the 'LaCASt' translator

<!---## Authors

* Philipp Scharpf
* Moritz Schubotz --->

## License

This project is licensed under the Apache License 2.0.

## Acknowledgments

We thank the Wikimedia foundation for [hosting](https://physwikiquiz.wmflabs.org) our web-based system.
