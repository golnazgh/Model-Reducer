# Model Reducer

Model reducer is an automated tool for reducing test cases that exhibit bugs in programs. For instance, large C programs that trigger some bug in GCC or Clang compilers can efficiently be reduced and simplified by model reducer. Model reducer avoids syntactically invalid test variants during reduction by building an Abstract Syntax tree (AST) of the test case and placing syntactically removeable nodes (based on rules of a given grammar) in a queue and visiting them for removal. In addition, model reducer tries to avoid semantically invalid variants during reduction by consultimg models trained using random forest and semantic features extracted from a large corpus of test cases. 

Currently, model reducer supports five models based on the type of the extracted features but it can easily be extended by more types of customized models. These supported five models are the following:

- rf-type: a model trained on GCC torture test cases using random forest and grammar type of the query node (the node in AST to remove when trying reduction) as feature.
- rf-children: a model trained on GCC torture test cases using random forest and grammar type of the children of the query node as features.
- rf-pathtoroot: a model trained on GCC torture test cases using random forest and grammar type of the nodes on the path from the query node to the root of the AST as features.
- rf-type-children: a model trained on GCC torture test cases using random forest and grammar type of the query node and its children as features.
- rf-type-children-pathtoroot: a model trained on GCC torture test cases using random forest and grammar type of the query node, its children and nodes on the path from the query node to the root as features.

A C grammar (cpnf) is provided in this version of the tool.

The benchmarks used in our study is also used in Perses and Pardis tools studies and can be found at the following link: 

https://bitbucket.org/chengniansun/perses_c_benchmarks/src/master/

For more information on Perses and Pardis, you can refer to the citations in the submitted paper.



# How to build

You will need a recent version of C++ compiler in addition to LLVM (version 9 works).
You can build using cmake as follows:
Create a build directory where model-reducer directory is placed. Then use the following commands:

mkdir build && cd build

cmake -DLLVM_DIR=/path/to/LLVM/lib/cmake/llvm ../model-reducer

make 

# How to run

After model-reducer is built, you can see its different options using the following:

bin/model-reducer -help

An example run:

bin/model-reducer -time -cpnf -prefilter=true -ml-fixpoint -model=rf-type -queue-mode=node -source=/path/to/file-to-reduce.c -checker=/path/to/checker.sh -o=/path/to/reduced.c

To write the checker.sh file, you need to return exit code 0 if property of interest is not reproduced. Exit code 1 should be returned if the property of interest is reproduced. In the above example run, the checker won't execute in a removal trial and will be skipped if rf-type model predicts that the removal trial will generate a semantically invalid test case.

