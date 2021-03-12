# CRNRepair
CRNRepair is a framework for Automated Repair of Chemical Reaction Networks. This page contains 
supplementatry data from our paper:

I. Mesecan, M.C. Gerten, J.I. Lathrop, M.B. Cohen, T. H. Caldas, CRNRepair: Automated Program Repair 
of Chemical Reaction Networks, International Workshop on Genetic Improvement, May 2021. 

`bibtex reference:`<br />
`@inproceedings{Mesecan:GI21,`<br />
`author= {Ibrahim Mesecan and Michael C. Gerten and James I. Lathrop and Myra B. Cohen and Tomas Haddad Caldas},`<br />
`title= {CRNRepair: Automated Program Repair of Chemical Reaction Networks},`<br />
`booktitle={International Workshop on Genetic Improvement},`<br />
`year={2021},`<br />
`pages={1-8}`<br />
`}`

## Input file format
The framewok uses the Systems Biology Markup Language [SBML](http://sbml.org/) format which 
is based on XML. SBML is used for describing Chemical and Biological processes, 
[e.g.](https://github.com/LavaOps/CRNRepair/blob/master/InputFiles/Subtraction/S6.sbml) 
Similar to XML, SBML is composed of tags and attributes. Every tag can have 0 or more 
attributes and 0 or more subtags. 

There are two subjects in this repository: 
[Hailstone](https://github.com/LavaOps/CRNRepair/tree/master/InputFiles/Hailstone) 
and [Subtraction](https://github.com/LavaOps/CRNRepair/tree/master/InputFiles/Subtraction). 
For each set, subject 0 (H0 or S0) is the correct CRN. And, subjects from 1 through 
10 are the mutants that we are trying to repair. 

SBML **model** tag contains listOfUnitDefinitions, listOfCompartments, listOfSpecies, 
(possible list of species in the pool) and listOfReactions. Then, reactions are given 
as a subtag in the listOfReactions. Every Reaction must contain at least one of 
(listOfReactants and listOfProducts) and the kineticLaw.

## Reading log files and patch information
Log files contain 3 sections:

1) **The AST tree enumeration**: This section is at the top of log files. The lines 
   start with a number, followed by a colon, space and a tagname. For 
   example, [the following](https://github.com/LavaOps/CRNRepair/blob/61a03eeb335e66991f351365f9162af87f2c8866/RQ1/Subtraction/Random/Logs/outrsu6_9380.txt#L8): <br />
   `7: species`<br />
   is the 7th node in the AST (the first species in the pool of listOfSpecies) and it 
   refers to the first species, [X1](https://github.com/LavaOps/CRNRepair/blob/61a03eeb335e66991f351365f9162af87f2c8866/InputFiles/Subtraction/S6.sbml#L13), 
   given in the input sbml file. 

2) **Text format of the input CRN**: After AST tree enumaration, the text format of the 
   input CRN is given, [e.g.](https://github.com/LavaOps/CRNRepair/blob/61a03eeb335e66991f351365f9162af87f2c8866/RQ1/Subtraction/Random/Logs/outrsu6_9380.txt#L40) <br />
   `List of given reactions in plain text:`<br />
   `reaction_1: X1 -> Y`<br />
   `mutant-addreactant: X2 Y X1 -> null`<br />
This is the human readable equivalent of the 
   [input sbml file](https://github.com/LavaOps/CRNRepair/blob/master/InputFiles/Subtraction/S6.sbml). 

3) **Result information for each epoch**: This section 
   [e.g.](https://github.com/LavaOps/CRNRepair/blob/61a03eeb335e66991f351365f9162af87f2c8866/RQ1/Subtraction/Random/Logs/outrsu6_9380.txt#L43) 
   starts with dashed line containing "RESULT" word in the center and 
   contains result information for each epoch. For example, the following is the result of the second epoch used in 
   [RQ1 Subtraction test subject 6](https://github.com/LavaOps/CRNRepair/blob/61a03eeb335e66991f351365f9162af87f2c8866/RQ1/Subtraction/Random/Logs/outrsu6_9380.txt#L49) :<br />
  `Epoch: 1 Fit.Eval: 70 Time elapsed: 1786.6 BestFitness: 0.0`<br />
  `BestPatch: StmtInsertion({'target': ('Input/Subtraction/CRN/S6.sbml', 24), 'ingredient': ('Input/Subtraction/CRN/S6.sbml', 11)})`<br />
  `StmtDeletion({'target': ('Input/Subtraction/CRN/S6.sbml', 28)})`

  There are 4 items reported for each epoch:
  - `Fitness evaluation:` The number of fitness evaluations in this epoch: 70. And, on 70th evaluation the target fitness is reached. 
  - `Time elapsed:` Time elapsed in seconds: 1786 seconds.
  - `Best fitness:` Best fitness found in this epoch: 0.
  - `Best Patch:` Best patch may contain 1 or more mutations. In the above example, there are two mutations there: 
    1) Insert a copy of AST [node 11](https://github.com/LavaOps/CRNRepair/blob/61a03eeb335e66991f351365f9162af87f2c8866/RQ1/Subtraction/Random/Logs/outrsu6_9380.txt#L12) 
       (the first reaction in [the sbml file](https://github.com/LavaOps/CRNRepair/blob/61a03eeb335e66991f351365f9162af87f2c8866/InputFiles/Subtraction/S6.sbml#L18)) 
       after the AST node 24 ([reaction 2](https://github.com/LavaOps/CRNRepair/blob/61a03eeb335e66991f351365f9162af87f2c8866/InputFiles/Subtraction/S6.sbml#L38))
    2) Delete [AST node 28](https://github.com/LavaOps/CRNRepair/blob/61a03eeb335e66991f351365f9162af87f2c8866/InputFiles/Subtraction/S6.sbml#L42) 
       (the last species from the listOfReactants in reaction 2, X1).

If any epoch is successful (if the target fitness is found), the corresponding 
patched [sbml](https://github.com/LavaOps/CRNRepair/blob/master/RQ1/Subtraction/Random/SBML%20Projects/Speedup/S6_2.sbml) 
and [text](https://github.com/LavaOps/CRNRepair/blob/master/RQ1/Subtraction/Random/SBML%20Projects/Speedup/S6_2.txt) 
files are reported.

**Acknowledgments**:
This work is supported in part by NSF Grant CCF-1909688, CCF-1901543, and  FET-1900716. 
Any opinions, findings, and conclusions or recommendations expressed in this material 
are those of the author(s) and do not necessarily reflect the views of the National Science Foundation.
