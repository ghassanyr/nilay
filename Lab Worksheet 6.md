this is my data structures course worksheet for this week. throughout prev weeks we were given similar worksheets and asked to push code to this repo. this week's worksheet is:

|  2026 – 2027 Academic Year Fall Semester Faculty of Engineering And Natural Sciences Computer Engineering & Software Engineering Department CENG203 – Data Structures Lab Worksheet \#6  |  |  |  |
| ----- | :---- | :---- | :---- |
| **Ders Kimliği /** *Course ID:* **30476** | **Şube /** *Section:* **1 & 2** | **Cevap Kâğıdı Sayısı /** *Number of Answer Sheet:*  |  |
| **Öğrenci Adı Soyadı** / *Student’s* *Name Surname*: |  |  | **Sınav Notu \- Paraf /** Exam Score \- Initials |
| **Öğrenci Numarası** / *Student’s Number:* |  |  |  |
| **Öğrenci İmzası** / *Student’s Signature:* |  |  |  |

|  Deney Tarihi / *Lab Date:* 05.11.2026 |  |  |  |  Deney Süresi / *Lab* *Duration:* 120 minutes |  |  |  |  |  |  |
| :---- | :---: | :---: | :---: | ----- | :---: | :---: | :---: | :---: | :---: | :---: |
|  **Soru Numarası /** *Numbers of the Questions* | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **10** |
|  **Alınan Not /** *Scored Points* |  |  |  |  |  |  |  |  |  |  |

*\*The point for each question must be stated next to the question.*

# **PART A – GET READY (≈ 15 min)**

This week is about trees – the first non-linear structure of the course – and about the binary search tree (BST), where every key is placed so that the left subtree holds smaller keys and the right subtree larger ones. Everything you built so far is reused: the recursion of Week 5 walks the tree, the stack of Week 4 builds an expression tree from postfix notation, and the queue of Week 4 prints the tree level by level. The rules do not change: one solution, one project, one main(), one folder per topic, one test function per week, commit and push at the end.

**A.1  Quick Notes – Mistakes We Keep Seeing**

Read this table before you ask for help.

| What you see | Cause | Fix |
| :---- | :---- | :---- |
| Unable to start program '…\\x64\\Debug\\CENG203\_Lab.exe'. The system cannot find the file specified. | There is no .exe: the last build failed, you never built, or you ran Clean. The dialog “There were build errors. Would you like to continue and run the last successful build?” means the same. | Answer **No**. View → Error List, fix the **first** error, build (Ctrl+Shift+B) until the Output window says 1 succeeded, then run. |
| Toolbar shows Select Startup Item…; no Local Windows Debugger. | Solution Explorer is in **Folder View**. No project is loaded. | Solution Explorer toolbar → Switch Views (⇄) → CENG203\_Lab.sln (.slnx in newer versions). |
| error LNK2019: unresolved external symbol "void \_\_cdecl runWeek06(void)" … – or the same for preorder, BST::insert, buildFromPostfix … referenced in function … | Week06.cpp, TreeUtils.cpp, BST.cpp or ExpressionTree.cpp is on disk but **not in the project**, so it is never compiled; or the body is missing / misspelled. | Show All Files → right-click the greyed file or folder → **Include In Project** → File → **Save All** → build. The Output window must list every .cpp. |
| Build succeeds but your new code never runs, or LNK2019 stays after Include In Project; Solution Explorer shows the file but the Output window never lists it. | The file is missing from the \<ItemGroup\> of CENG203\_Lab.vcxproj – the project was not saved, or the entry sits only in the .filters file. | Section **A.2**: check and repair the project file by hand. |
| error LNK2005: "void \_\_cdecl preorder(struct TreeNode const \*)" already defined in TreeUtils.obj | Two bodies for one function: you typed preorder again in BST.cpp or Week06.cpp, or a body sits inside a header that two .cpp files include. | One body per function, bodies only in .cpp files. BST::preorder() is a different function (a member) – its body only calls ::preorder(root). |
| error C2660: 'BST::preorder': function does not take 1 arguments (inside BST.cpp) | Inside a member function the name preorder means the member BST::preorder(); the free function of TreeUtils.h is hidden by it. | Write ::preorder(root) – the two colons select the global function. Same for inorder, postorder, height, isBalanced. |
| fatal error C1083: Cannot open include file: 'TreeNode.h' – or C2143: syntax error: missing ';' before '\*' with C4430 on a line that uses TreeNode\* | Wrong relative path; or the \#include "TreeNode.h" line is missing, so the type TreeNode is unknown. | From Labs\\Week06.cpp write "../Tree/TreeUtils.h"; inside Tree\\ the files include each other as "TreeNode.h" (same folder); from main.cpp "Labs/Labs.h". |
| New folders appear inside CENG203\_Lab\\CENG203\_Lab\\, next to Debug and x64. | You right-clicked the build folder in Folder View. | Move them with File Explorer next to CENG203\_Lab.vcxproj and main.cpp, then Include In Project. |
| error C2039: 'findMin': is not a member of 'BST' or error C2065: 'insertInto': undeclared identifier | The body is in BST.cpp but the declaration is missing from BST.h – an old copy of the header, or a skipped line in A.4. | Compare BST.h with A.4 line by line; every function needs its prototype inside the class. Save the header (Ctrl+S) before you build. |
| warning C4715: 'BST::find': not all control paths return a value – and the function returns garbage at run time | An if / else chain without a final return, or a loop that can end without one. | Every path must end in a return: add return false; after the loop. Treat C4715 as an error. |
| Exception thrown: read access violation. node was nullptr. – the debugger stops inside preorder, height, evaluate … | The function touched node-\>data (or node-\>left) before testing for the empty tree. | The base case if (node \== nullptr) return …; must be the **first** line of every recursive tree function. |
| Unhandled exception at 0x…: Stack overflow (parameters: 0x0000000000000001, 0x…) – exception code 0xC00000FD | Infinite recursion: the recursive call does not move towards the base case – e.g. height(node) instead of height(node-\>left) – or you built a cycle such as root-\>left \= root. | Check every recursive call: it must pass a **child**, never the same node. Stop the program with Shift+F5. |
| LNK1168: cannot open …\\CENG203\_Lab.exe for writing | The previous run is still open. | Close the console window, build again. |
| The program behaves like an old version. | Stale build, or you edited a copy of the file in another folder. | Build → **Rebuild Solution**. Hover the editor tab to see the full path. |

**A.2  Project File Check – the File Is on Disk but Not in the Build**

Visual Studio compiles only the files listed in CENG203\_Lab.vcxproj. That file is plain XML; Include In Project does nothing more than add one line to it. If a line is missing – because the project was not saved, because you created the file in Folder View, or because a Git merge lost it – the file sits in Solution Explorer or on disk, but it is never compiled and the linker reports LNK2019 for everything inside it.

**Check:** the Output window after a build lists every .cpp that was compiled (TreeUtils.cpp, BST.cpp, ExpressionTree.cpp, Week06.cpp, …). A .cpp that never appears there is not in the project. Or search the file: in PowerShell, inside the project folder:

cd C:\\CENG203\\CENG203\_Lab  
Select-String \-Path CENG203\_Lab.vcxproj \-Pattern 'ClCompile Include\|ClInclude Include'

**Repair by hand** (a few lines of XML):

**Step 1**	Close the solution (File → Close Solution) or right-click the project → **Unload Project**.

**Step 2**	Open CENG203\_Lab.vcxproj with Notepad (right-click → Open with → Notepad). Do not rename it.

**Step 3**	Find the block that contains \<ClCompile Include="main.cpp" /\>. Every .cpp of the project needs one such line; every .h needs a \<ClInclude …/\> line in the next block. Paths are relative to the folder of the .vcxproj and use backslashes. The complete Week 6 list:

  \<ItemGroup\>  
    \<ClCompile Include="main.cpp" /\>  
    \<ClCompile Include="Labs\\Week02.cpp" /\>  
    \<ClCompile Include="Labs\\Week03.cpp" /\>  
    \<ClCompile Include="Labs\\Week04.cpp" /\>  
    \<ClCompile Include="Labs\\Week05.cpp" /\>  
    \<ClCompile Include="Labs\\Week06.cpp" /\>  
    \<ClCompile Include="LinkedList\\LinkedList.cpp" /\>  
    \<ClCompile Include="LinkedList\\CircularList.cpp" /\>  
    \<ClCompile Include="LinkedList\\DoublyLinkedList.cpp" /\>  
    \<ClCompile Include="Stack\\Stack.cpp" /\>  
    \<ClCompile Include="Stack\\ArrayStack.cpp" /\>  
    \<ClCompile Include="Queue\\Queue.cpp" /\>  
    \<ClCompile Include="Queue\\CircularQueue.cpp" /\>  
    \<ClCompile Include="Recursion\\Recursion.cpp" /\>  
    \<ClCompile Include="Tree\\TreeUtils.cpp" /\>  
    \<ClCompile Include="Tree\\BST.cpp" /\>  
    \<ClCompile Include="Tree\\ExpressionTree.cpp" /\>  
  \</ItemGroup\>  
  \<ItemGroup\>  
    \<ClInclude Include="Labs\\Labs.h" /\>  
    \<ClInclude Include="LinkedList\\Node.h" /\>  
    \<ClInclude Include="LinkedList\\LinkedList.h" /\>  
    \<ClInclude Include="LinkedList\\CircularList.h" /\>  
    \<ClInclude Include="LinkedList\\DNode.h" /\>  
    \<ClInclude Include="LinkedList\\DoublyLinkedList.h" /\>  
    \<ClInclude Include="Stack\\Stack.h" /\>  
    \<ClInclude Include="Stack\\ArrayStack.h" /\>  
    \<ClInclude Include="Queue\\Queue.h" /\>  
    \<ClInclude Include="Queue\\CircularQueue.h" /\>  
    \<ClInclude Include="Recursion\\Recursion.h" /\>  
    \<ClInclude Include="Tree\\TreeNode.h" /\>  
    \<ClInclude Include="Tree\\TreeUtils.h" /\>  
    \<ClInclude Include="Tree\\BST.h" /\>  
    \<ClInclude Include="Tree\\ExpressionTree.h" /\>  
  \</ItemGroup\>

**Step 4**	Add the missing lines (or replace both blocks with the list above – only files that really exist on disk). Keep the XML exact: \<ClCompile Include="…" /\> with the closing /\>. Save.

**Step 5**	Reopen CENG203\_Lab.sln (or Reload Project) and build. If Visual Studio says The project file could not be loaded, a bracket or quote is missing – fix the line or undo the edit.

**Note:** CENG203\_Lab.vcxproj.filters only controls the folders shown in Solution Explorer (filters are virtual folders). A file that is in .filters but not in .vcxproj is not compiled; a file in .vcxproj but not in .filters is compiled and simply appears at the top level. If the .filters file is damaged, delete it – Visual Studio recreates it on the next save.

**Tip:** Prevention: after Include In Project always press File → **Save All** (Ctrl+Shift+S). The project file must change on disk – check its modified time in File Explorer.

**A.3  Files This Week**

C:\\CENG203\\CENG203\_Lab\\  
│   main.cpp                 ← ADD  menu line 6 \+ case 6: runWeek06();  
├── Labs\\  
│       Labs.h               ← ADD  void runWeek06();  
│       Week06.cpp           ← NEW  test code (given in A.4)  
├── LinkedList\\  Stack\\  Queue\\  Recursion\\      ← unchanged  
└── Tree\\                    ← NEW folder  
        TreeNode.h           ← given: the node struct  
        TreeUtils.h / .cpp   ← traversals, height, balance (Q2, Q3, Q6)  
        BST.h / .cpp         ← insert, find, remove (Q4, Q5, Q7)  
        ExpressionTree.h/.cpp← postfix build, evaluate (Q8, Q9)

**Step 1**	In Labs.h add void runWeek06();. In main.cpp add cout \<\< " 6\. Week 6 \- Trees and BSTs" \<\< endl; and case 6:  runWeek06(); break;.

**Step 2**	Solution view, Show All Files on: right-click the project → Add → **New Folder** → Tree. Add the seven files to it (Header File / C++ File) and type them from A.4. Every file must appear in the Output window when you build – if not, A.2.

**Step 3**	Add Labs\\Week06.cpp and type the test code of A.4.

**Step 4**	Build (Ctrl+Shift+B) → 1 succeeded → run (Ctrl+F5) → 6. Every \[Qn\] line prints, but the lists are empty and the numbers are 0, \-1 or false – correct for now, the bodies are stubs. Commit (Week 6 \- setup).

**Note:** The solution code of the programming questions is published after the lab in the course repository: https\://github.com/rushout912/CENG203\_Lab. Written answers are discussed in class – write your own first.

**A.4  Code to Type**

The headers are complete – type them exactly, they are the contract between the files. The three .cpp files of Tree\\ contain the parts that are given plus a stub (// TODO) for every function you write in Part B. Week06.cpp is the test code: it prints one \[Qn\] block per question.

***Tree\\TreeNode.h (given)***

\#pragma once  
   
// One node of a binary tree: a value and two child pointers  
struct TreeNode  
{  
    int       data;  
    TreeNode\* left;                 // left subtree  (nullptr \= none)  
    TreeNode\* right;                // right subtree (nullptr \= none)  
};  
   
// Creates a node on the heap with both children set to nullptr  
TreeNode\* makeNode(int value);

***Tree\\TreeUtils.h (given)***

\#pragma once  
\#include "TreeNode.h"  
   
// Week 6: recursive functions that work on ANY binary tree.  
// Every function takes the root of a (sub)tree; nullptr \= empty tree.  
   
// \---- Q2: traversals (print the values, separated by spaces) \----  
void preorder(const TreeNode\* node);        // node \- left \- right  
void inorder(const TreeNode\* node);         // left \- node \- right  
void postorder(const TreeNode\* node);       // left \- right \- node  
int  countNodes(const TreeNode\* node);  
int  countLeaves(const TreeNode\* node);  
   
// \---- Q3: height and level order \----  
int  height(const TreeNode\* node);          // edges on the longest path:  
                                            // empty \= \-1, single node \= 0  
void levelOrder(const TreeNode\* node);      // breadth first, uses a queue  
   
// \---- Q6: balance \----  
bool isBalanced(const TreeNode\* node);      // \|h(left) \- h(right)\| \<= 1  
                                            // at EVERY node  
   
// \---- memory \----  
void destroy(TreeNode\* node);               // deletes the whole subtree

***Tree\\TreeUtils.cpp (given: makeNode and destroy are complete; the rest are stubs)***

\#include "TreeUtils.h"  
\#include \<iostream\>  
\#include \<queue\>                    // std::queue: the library version of  
using namespace std;                // Week 4's Queue, for any type  
   
TreeNode\* makeNode(int value)  
{  
    TreeNode\* node \= new TreeNode;  
    node-\>data \= value;  
    node-\>left \= nullptr;  
    node-\>right \= nullptr;  
    return node;  
}  
   
// \============================ Q2 \============================  
void preorder(const TreeNode\* node)  
{  
    // TODO: base case; print; left; right  
}  
   
void inorder(const TreeNode\* node)  
{  
    // TODO: left; print; right  
}  
   
void postorder(const TreeNode\* node)  
{  
    // TODO: left; right; print  
}  
   
int countNodes(const TreeNode\* node)  
{  
    return 0;                       // TODO: 1 \+ left \+ right  
}  
   
int countLeaves(const TreeNode\* node)  
{  
    return 0;                       // TODO  
}  
   
// \============================ Q3 \============================  
int height(const TreeNode\* node)  
{  
    return \-1;                      // TODO: 1 \+ the taller subtree  
}  
   
void levelOrder(const TreeNode\* node)  
{  
    // TODO: std::queue\<const TreeNode\*\>; push root; pop, print, push kids  
}  
   
// \============================ Q6 \============================  
bool isBalanced(const TreeNode\* node)  
{  
    return false;                   // TODO: compare the heights, recurse  
}  
   
// \============================ memory \============================  
void destroy(TreeNode\* node)  
{  
    if (node \== nullptr)  
        return;  
    destroy(node-\>left);            // children first ...  
    destroy(node-\>right);  
    delete node;                    // ... the node last (postorder)  
}

***Tree\\BST.h (given)***

\#pragma once  
\#include "TreeNode.h"  
   
// Binary Search Tree: left subtree \< node \< right subtree, at every node.  
// The class owns the root; the work is done by private recursive helpers.  
class BST  
{  
private:  
    TreeNode\* root;                 // nullptr \= empty tree  
   
    static TreeNode\* insertInto(TreeNode\* node, int value, bool& inserted);  
    static TreeNode\* removeFrom(TreeNode\* node, int value, bool& removed);  
   
public:  
    BST();  
    \~BST();  
   
    bool insert(int value);         // false \= duplicate, tree unchanged  
    bool find(int value) const;     // O(height)  
    bool findMin(int& value) const; // leftmost node; false \= empty  
    bool findMax(int& value) const; // rightmost node  
    bool remove(int value);         // false \= not found  
   
    void preorder() const;          // TreeUtils applied to root  
    void inorder() const;           // ascending order\!  
    void postorder() const;  
    int  height() const;  
    bool isBalanced() const;  
    bool isEmpty() const;  
};

***Tree\\BST.cpp (given: constructor, destructor, insert / remove wrappers and the traversal wrappers; the helpers are stubs)***

\#include "BST.h"  
\#include "TreeUtils.h"  
\#include \<iostream\>  
using namespace std;  
   
BST::BST()  
{  
    root \= nullptr;  
}  
   
BST::\~BST()  
{  
    destroy(root);                  // postorder delete of every node  
}  
   
// \============================ Q4 \============================  
bool BST::insert(int value)  
{  
    bool inserted \= false;  
    root \= insertInto(root, value, inserted);  
    return inserted;  
}  
   
TreeNode\* BST::insertInto(TreeNode\* node, int value, bool& inserted)  
{  
    // TODO: empty spot \-\> makeNode; smaller \-\> left; larger \-\> right  
    return node;  
}  
   
// \============================ Q5 \============================  
bool BST::find(int value) const  
{  
    return false;                   // TODO: walk down, one side only  
}  
   
bool BST::findMin(int& value) const  
{  
    return false;                   // TODO: leftmost node  
}  
   
bool BST::findMax(int& value) const  
{  
    return false;                   // TODO: rightmost node  
}  
   
// \============================ Q7 \============================  
bool BST::remove(int value)  
{  
    bool removed \= false;  
    root \= removeFrom(root, value, removed);  
    return removed;  
}  
   
TreeNode\* BST::removeFrom(TreeNode\* node, int value, bool& removed)  
{  
    // TODO: find the node; leaf / one child / two children (successor)  
    return node;  
}  
   
// \============================ wrappers \============================  
void BST::preorder() const   { ::preorder(root); }  
void BST::inorder() const    { ::inorder(root); }  
void BST::postorder() const  { ::postorder(root); }  
int  BST::height() const     { return ::height(root); }  
bool BST::isBalanced() const { return ::isBalanced(root); }  
bool BST::isEmpty() const    { return root \== nullptr; }

***Tree\\ExpressionTree.h (given)***

\#pragma once  
\#include \<string\>  
   
// Expression tree: every internal node is an operator (+ \- \* /),  
// every leaf is a number. Built from POSTFIX with a stack of nodes.  
struct ExprNode  
{  
    std::string data;               // "3", "+", "42" ... (text, not int)  
    ExprNode\*   left;  
    ExprNode\*   right;  
};  
   
bool      isOperator(const std::string& token);  
ExprNode\* buildFromPostfix(const std::string& postfix);   // Q8  
void      printPrefix(const ExprNode\* node);              // preorder  
void      printPostfix(const ExprNode\* node);             // postorder  
void      printInfix(const ExprNode\* node);               // inorder \+ ( )  
double    evaluate(const ExprNode\* node);                 // Q9, throws  
void      destroyExpr(ExprNode\* node);                    // delete all

***Tree\\ExpressionTree.cpp (given: isOperator, printPrefix, printPostfix, destroyExpr; the rest are stubs)***

\#include "ExpressionTree.h"  
\#include \<iostream\>  
\#include \<sstream\>                  // stringstream: splits the tokens  
\#include \<stack\>                    // std::stack: Week 4's Stack, any type  
\#include \<stdexcept\>                // runtime\_error  
using namespace std;  
   
bool isOperator(const string& token)  
{  
    return token \== "+" \|\| token \== "-" \|\| token \== "\*" \|\| token \== "/";  
}  
   
// \============================ Q8 \============================  
ExprNode\* buildFromPostfix(const string& postfix)  
{  
    stack\<ExprNode\*\> st;            // holds finished subtrees  
    stringstream ss(postfix);  
    string token;  
    while (ss \>\> token) {           // one token at a time: "3", "4", "+"  
        // TODO: make a node; operator \-\> pop right, pop left; push it  
    }  
    return st.empty() ? nullptr : st.top();  
}  
   
void printPrefix(const ExprNode\* node)  
{  
    if (node \== nullptr)  
        return;  
    cout \<\< node-\>data \<\< " ";      // operator first: Polish notation  
    printPrefix(node-\>left);  
    printPrefix(node-\>right);  
}  
   
void printPostfix(const ExprNode\* node)  
{  
    if (node \== nullptr)  
        return;  
    printPostfix(node-\>left);  
    printPostfix(node-\>right);  
    cout \<\< node-\>data \<\< " ";      // operator last: reverse Polish  
}  
   
// \============================ Q9 \============================  
void printInfix(const ExprNode\* node)  
{  
    // TODO: "(" left " op " right ")" for operators, the number otherwise  
}  
   
double evaluate(const ExprNode\* node)  
{  
    return 0.0;                     // TODO: number \-\> stod; else combine  
}  
   
void destroyExpr(ExprNode\* node)  
{  
    if (node \== nullptr)  
        return;  
    destroyExpr(node-\>left);  
    destroyExpr(node-\>right);  
    delete node;  
}

***Labs\\Week06.cpp (given: the test code – type it completely)***

\#include "Labs.h"  
\#include "../Tree/TreeNode.h"  
\#include "../Tree/TreeUtils.h"  
\#include "../Tree/BST.h"  
\#include "../Tree/ExpressionTree.h"  
\#include \<iostream\>  
\#include \<stdexcept\>  
using namespace std;  
   
void runWeek06()  
{  
    cout \<\< "--- Week 6: Trees and Binary Search Trees \---" \<\< endl;  
    cout \<\< boolalpha;  
   
    // \---------- Q2: a binary tree built by hand \----------  
    TreeNode\* root \= makeNode(1);  
    root-\>left \= makeNode(2);  
    root-\>right \= makeNode(3);  
    root-\>left-\>left \= makeNode(4);  
    root-\>left-\>right \= makeNode(5);  
    root-\>right-\>right \= makeNode(6);  
    cout \<\< "\[Q2\] preorder : ";  preorder(root);   cout \<\< endl;  
    cout \<\< "\[Q2\] inorder  : ";  inorder(root);    cout \<\< endl;  
    cout \<\< "\[Q2\] postorder: ";  postorder(root);  cout \<\< endl;  
    cout \<\< "\[Q2\] nodes \= " \<\< countNodes(root)  
         \<\< "  leaves \= " \<\< countLeaves(root) \<\< endl;  
   
    // \---------- Q3: height and level order \----------  
    cout \<\< "\[Q3\] height(root) \= " \<\< height(root)  
         \<\< "  height(node 4\) \= " \<\< height(root-\>left-\>left)  
         \<\< "  height(empty) \= " \<\< height(nullptr) \<\< endl;  
    cout \<\< "\[Q3\] level order: ";  levelOrder(root);  cout \<\< endl;  
    destroy(root);  
    root \= nullptr;  
   
    // \---------- Q4: BST insert \----------  
    BST bst;  
    int keys\[\] \= { 10, 5, 15, 7, 12, 20, 3 };  
    for (int k : keys)  
        bst.insert(k);  
    cout \<\< "\[Q4\] insert(10) again \= " \<\< bst.insert(10) \<\< endl;  
    cout \<\< "\[Q4\] inorder  : ";  bst.inorder();    cout \<\< endl;  
    cout \<\< "\[Q4\] preorder : ";  bst.preorder();   cout \<\< endl;  
    cout \<\< "\[Q4\] postorder: ";  bst.postorder();  cout \<\< endl;  
   
    // \---------- Q5: find, min, max \----------  
    int v \= 0;  
    cout \<\< "\[Q5\] find(7) \= " \<\< bst.find(7)  
         \<\< "  find(8) \= " \<\< bst.find(8) \<\< endl;  
    bst.findMin(v);  
    cout \<\< "\[Q5\] min \= " \<\< v;  
    bst.findMax(v);  
    cout \<\< "  max \= " \<\< v \<\< endl;  
   
    // \---------- Q6: shape and balance \----------  
    cout \<\< "\[Q6\] height \= " \<\< bst.height()  
         \<\< "  balanced \= " \<\< bst.isBalanced() \<\< endl;  
    BST chain;  
    for (int k \= 1; k \<= 5; k++)  
        chain.insert(k);            // sorted input: the worst case  
    cout \<\< "\[Q6\] chain 1..5: height \= " \<\< chain.height()  
         \<\< "  balanced \= " \<\< chain.isBalanced() \<\< endl;  
    cout \<\< "\[Q6\] chain preorder: ";  chain.preorder();  cout \<\< endl;  
   
    // \---------- Q7: remove \----------  
    bst.remove(3);  
    cout \<\< "\[Q7\] remove(3) leaf       : ";  bst.inorder();  cout \<\< endl;  
    bst.remove(5);  
    cout \<\< "\[Q7\] remove(5) one child  : ";  bst.inorder();  cout \<\< endl;  
    bst.remove(10);  
    cout \<\< "\[Q7\] remove(10) two kids  : ";  bst.inorder();  cout \<\< endl;  
    cout \<\< "\[Q7\] preorder now: ";  bst.preorder();  cout \<\< endl;  
    cout \<\< "\[Q7\] remove(99) \= " \<\< bst.remove(99) \<\< endl;  
   
    // \---------- Q8: expression tree from postfix \----------  
    ExprNode\* expr \= buildFromPostfix("3 4 \+ 5 2 \- \*");  
    cout \<\< "\[Q8\] prefix : ";  printPrefix(expr);   cout \<\< endl;  
    cout \<\< "\[Q8\] postfix: ";  printPostfix(expr);  cout \<\< endl;  
   
    // \---------- Q9: infix and evaluation \----------  
    cout \<\< "\[Q9\] infix  : ";  printInfix(expr);  cout \<\< endl;  
    cout \<\< "\[Q9\] value  \= " \<\< evaluate(expr) \<\< endl;  
    destroyExpr(expr);  
   
    ExprNode\* bad \= buildFromPostfix("8 2 2 \- /");  
    cout \<\< "\[Q9\] infix  : ";  printInfix(bad);  cout \<\< endl;  
    try {  
        double value \= evaluate(bad);           // throws: next line skipped  
        cout \<\< "\[Q9\] value  \= " \<\< value \<\< endl;  
    }  
    catch (const runtime\_error& e) {  
        cout \<\< "\[Q9\] Error: " \<\< e.what() \<\< endl;  
    }  
    destroyExpr(bad);  
}

***Labs\\Labs.h and main.cpp – the lines to add***

// Labs.h: one more prototype at the end  
void runWeek06();  
   
// main.cpp: inside the menu, after the Week 5 line ...  
        cout \<\< " 6\. Week 6 \- Trees and BSTs" \<\< endl;  
// ... and inside the switch, after case 5  
        case 6:  runWeek06(); break;

**A.5  Recap – Tree Vocabulary and the Four Traversals**

A tree is a set of nodes connected by edges, with one node – the **root** – at the top and no cycles. In a **binary tree** every node has at most two children, called left and right; an empty subtree is a nullptr. Because every subtree is itself a tree, almost every tree function is recursive: handle the empty tree (base case), handle this node, call yourself on the two children.

                  1                 the tree of Q1 and Q2  
                /   \\               (Week06.cpp builds it by hand)  
               2     3  
              / \\     \\  
             4   5     6

| Term | Meaning | In the tree above |
| :---- | :---- | :---- |
| root | the only node without a parent | 1 |
| edge | the link from a parent to a child; a tree with n nodes has n − 1 edges | 5 edges |
| parent / child / siblings | the node above / the nodes below / children of the same parent | 2 is the parent of 4 and 5, which are siblings |
| leaf (external node) | a node without children | 4, 5, 6 |
| internal node | a node with at least one child | 1, 2, 3 |
| degree | number of children of a node; the degree of the tree is the largest one | degree(3) \= 1, degree of the tree \= 2 |
| level | the root is at level 0, its children at level 1, … | 6 is at level 2 |
| depth of a node | number of edges from the root down to the node (= its level) | depth(4) \= 2 |
| height of a node | number of edges on the longest path from the node down to a leaf; a leaf has height 0, an empty tree −1 | height(2) \= 1, height(4) \= 0 |
| height of the tree | the height of the root \= the deepest level | 2 |
| path | the sequence of nodes you pass along edges | 1 – 3 – 6 |
| ancestor / descendant | reached by going up to parents / down to children | ancestors of 5: 2, 1; descendants of 2: 4, 5 |
| subtree | a node together with all its descendants | the subtree of 2 is {2, 4, 5} |

| Traversal | Order of the visit | Result above | Typical use |
| :---- | :---- | :---- | :---- |
| preorder | node – left – right | 1 2 4 5 3 6 | copy a tree, prefix notation (Q8) |
| inorder | left – node – right | 4 2 5 1 3 6 | sorted output of a BST (Q4), infix notation (Q9) |
| postorder | left – right – node | 4 5 2 6 3 1 | delete a tree, evaluate an expression (Q9) |
| level order | level by level, left to right – a queue instead of recursion | 1 2 3 4 5 6 | breadth-first search, print by levels (Q3) |

The first three are **depth-first** (DFS): they follow one path to the bottom before they try the next one, and the call stack of Week 5 remembers the way back. Level order is **breadth-first** (BFS): every node of a level is printed before the next level, so a FIFO queue holds the nodes that wait.

A **binary search tree** adds one rule to the binary tree: at every node, all keys in the left subtree are smaller and all keys in the right subtree are larger than the node’s key. The rule turns a search into a walk down a single path (O(h), h \= height) and makes the inorder traversal print the keys in ascending order. The shape of a BST depends on the insertion order: random order gives a bushy tree with h ≈ log₂ n, sorted order gives a chain with h \= n − 1 – the linked list of Week 3 in disguise. The tree above is **not** a BST.

**Tip:** \#include \<stack\> and \#include \<queue\> give you std::stack and std::queue – the library versions of the classes you wrote in Week 4, for any element type: stack\<ExprNode\*\> st;, queue\<const TreeNode\*\> q;. Operations: push, pop (returns **nothing** – read top() / front() first), empty, size.

**Checkpoint before Part B:**

☐	Tree\\ folder with seven files, Week06.cpp, menu option 6 – build succeeds, every \[Qn\] line prints (empty lists, 0, \-1, false).

☐	Committed Week 6 \- setup.

# **PART B – QUESTIONS**

Questions are ordered from easy to hard. Implement each function in the file named in the question, build, run menu option 6 and compare with the expected output. Write the answers to the lettered sub-questions in the boxes. The solution code is published after the lab at https\://github.com/rushout912/CENG203\_Lab – the written answers are yours.

1. **Tree Vocabulary (easy) *(5 pts)***

No code in this question. Use the tree of A.5 (the one Week06.cpp builds for Q2):

                  1  
                /   \\  
               2     3  
              / \\     \\  
             4   5     6

**a)**	Fill in the table.

| Term | Your answer |
| :---- | :---- |
| root |  |
| leaves |  |
| internal nodes |  |
| parent of 5 / children of 3 |  |
| siblings of 4 |  |
| level of node 6 |  |
| depth of node 4 |  |
| height of node 2 |  |
| height of the tree |  |
| degree of node 3 / degree of the tree |  |
| number of edges |  |
| path from 1 to 6 |  |
| ancestors of 5 / descendants of 2 |  |

**b)**	Is the tree **full** (every node has 0 or 2 children), **perfect** (full and all leaves on the same level), **complete** (all levels full except the last, which is filled from the left), **balanced** (at every node the heights of the two subtrees differ by at most 1)? Is it a **binary search tree**? One reason each.

**c)**	A tree with n nodes always has exactly n − 1 edges. Why?

2. **Three Traversals (easy) *(10 pts)***

In Tree\\TreeUtils.cpp implement preorder, inorder, postorder, countNodes and countLeaves. Every function starts with the base case if (node \== nullptr) return …;. The three traversals are the same function with the cout line in a different place; the two counters return a number instead of printing.

***Expected output***

\[Q2\] preorder : 1 2 4 5 3 6  
\[Q2\] inorder  : 4 2 5 1 3 6  
\[Q2\] postorder: 4 5 2 6 3 1  
\[Q2\] nodes \= 6  leaves \= 3

**a)**	Before you run, predict the three traversal lines from the drawing. Then run and compare.

**b)**	“Visit” means executing the cout line. Describe, for the frame of node 2, the moment of its visit in each of the three orders: what has already returned, what is still to come?

**c)**	How many frames of preorder are on the call stack at the deepest point – the moment preorder(nullptr) is called for the left child of node 4? Which calls are they? Give the general formula for a tree of height h.

**d)**	In countLeaves, why must the leaf test come after the nullptr test, and what would the function return without the leaf test at all? Which traversal order does destroy use, and why is it the only correct one?

**Try it:** In preorder move the cout line below the two recursive calls, run, and name the output you get. Put it back.

3. **Height and Level Order (easy–medium) *(10 pts)***

In Tree\\TreeUtils.cpp implement height – the number of edges on the longest path down: an empty tree has height −1, a leaf 0, every other node 1 \+ the larger height of its children – and levelOrder, which prints the tree level by level with a std::queue\<const TreeNode\*\>: push the root; while the queue is not empty, take the front node, print it, push its children.

***Expected output***

\[Q3\] height(root) \= 2  height(node 4\) \= 0  height(empty) \= \-1  
\[Q3\] level order: 1 2 3 4 5 6

**a)**	Trace height(root): write the value every call returns – for the six nodes and for the nullptr calls below the leaves. How many calls are made in total? Why does a leaf get 0?

**b)**	Some books count **nodes** instead of edges: an empty tree has height 0 and a leaf has height 1\. Which single line of height changes, and what does the \[Q3\] height line print then? Does isBalanced (Q6) still work with that convention?

**c)**	Trace levelOrder(root): fill in one row per loop iteration.

| Step | Popped and printed | Pushed | Queue after the step (front → back) |
| :---- | :---- | :---- | :---- |
| start | – |  |  |
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |
| 6 |  |  |  |

**d)**	Which of the four traversals are depth-first and which is breadth-first? Why does level order need a FIFO queue, while the recursive traversals need no queue at all? If you replace the queue by a std::stack (push the right child first, then the left), which order comes out?

4. **BST Insert (medium) *(10 pts)***

In Tree\\BST.cpp implement the recursive helper insertInto(node, value, inserted): an empty spot (nullptr) is where the new node goes – create it with makeNode, set inserted \= true and return it; a smaller value continues in the left subtree, a larger one in the right subtree (node-\>left \= insertInto(…)); an equal value is a duplicate and changes nothing. The function always returns the root of the subtree it was given, so the parent can store it. Week06.cpp inserts 10, 5, 15, 7, 12, 20, 3 in this order.

***Expected output***

\[Q4\] insert(10) again \= false  
\[Q4\] inorder  : 3 5 7 10 12 15 20  
\[Q4\] preorder : 10 5 3 7 15 12 20  
\[Q4\] postorder: 3 7 5 12 20 15 10

**a)**	Draw the BST after the seven insertions. Then list the comparisons made by insert(7) and where the node ends up.

**b)**	Why does inorder print the keys in ascending order – for every BST, not only this one? If you had to store the keys in a file so that re-inserting them rebuilds exactly the same shape, which traversal would you save, and why not inorder?

**c)**	What happens in insertInto when value \== node-\>data? Change else if (value \> node-\>data) to a plain else: what does insert(10) return now, what does inorder print, and which operation of the class becomes ambiguous?

**d)**	Why does insertInto return a TreeNode\*, and why is every recursive call written as node-\>left \= insertInto(node-\>left, …) instead of just insertInto(node-\>left, …)? How does the value of inserted get back to insert()?

**Try it:** Insert the same seven keys in the order 3, 5, 7, 10, 12, 15, 20 into a second BST and print its height. You will meet this shape again in Q6.

5. **Find, Min and Max (medium) *(10 pts)***

In Tree\\BST.cpp implement find (a loop: start at the root, go left if the value is smaller, right if it is larger, stop at a match or at nullptr), findMin (follow left as far as possible) and findMax (follow right). The last two write the key into the reference parameter and return false for an empty tree.

***Expected output***

\[Q5\] find(7) \= true  find(8) \= false  
\[Q5\] min \= 3  max \= 20

**a)**	List the nodes that find(7) and find(8) visit, in order, and the comparison made at each one.

**b)**	How many nodes does find visit at most in a BST of height h? Give the number for the tree of Q4, for the chain of five keys of Q6, and for a balanced tree with 1 000 000 keys. How many nodes would a search in Week 3’s linked list visit in the worst case?

**c)**	Why is the signature bool findMin(int& value) const and not int findMin() const? What would the second version return for an empty tree? (The course rule: class functions report with bool, the caller decides what to print.)

**d)**	Write find recursively in the box (a private static helper findIn(const TreeNode\* node, int value) and a one-line find). Which version uses more stack memory, and by how much?

6. **Shape and Balance (medium) *(10 pts)***

In Tree\\TreeUtils.cpp implement isBalanced: an empty tree is balanced; a node is balanced if the heights of its two subtrees differ by at most 1 **and** both subtrees are balanced. Week06.cpp also builds a second BST from the sorted input 1, 2, 3, 4, 5.

***Expected output***

\[Q6\] height \= 2  balanced \= true  
\[Q6\] chain 1..5: height \= 4  balanced \= false  
\[Q6\] chain preorder: 1 2 3 4 5

**a)**	Draw the tree built from 1, 2, 3, 4, 5. Why does sorted input produce this shape, which structure of Week 3 has it turned into, and how many nodes does find(5) visit?

**b)**	isBalanced compares height(left) − height(right) at every node. Write this difference for every node of the Q4 tree and for every node of the chain. Which is the lowest node of the chain where the rule \|difference\| ≤ 1 breaks, and how does that single node make isBalanced(root) false?

**c)**	Why does balance matter? Compare the cost of find in a balanced tree and in a chain for n \= 1 000 000 keys. The keys 1, 2, 3, 4, 5 arrive in a sorted array: in which order would you insert them so that the tree gets height 2? Give the rule, not only one order.

**d)**	The isBalanced you wrote calls height at every node, and height visits the whole subtree each time. What is the cost for a chain of n nodes? Sketch how one pass could compute the height and the balance together (hint: let the helper return a special value such as −2 for “not balanced”).

7. **Remove: Three Cases (medium–hard) *(10 pts)***

In Tree\\BST.cpp implement removeFrom(node, value, removed). First find the node as in insert. Then: a **leaf** is deleted and nullptr is returned; a node with **one child** is deleted and its child is returned, so the parent links to the child; a node with **two children** keeps its place but takes the key of its **inorder successor** (the smallest key of the right subtree), and that successor is removed from the right subtree with a recursive call. The first two cases share the same code: child is whichever pointer is not nullptr. Week06.cpp removes 3, 5 and 10 from the Q4 tree, in this order.

***Expected output***

\[Q7\] remove(3) leaf       : 5 7 10 12 15 20  
\[Q7\] remove(5) one child  : 7 10 12 15 20  
\[Q7\] remove(10) two kids  : 7 12 15 20  
\[Q7\] preorder now: 12 7 15 20  
\[Q7\] remove(99) \= false

**a)**	Draw the tree after each of the three removals.

**b)**	For remove(10): which node is the inorder successor and how is it found? Why does copying its key into the root keep the BST property? Could the inorder **predecessor** be used instead?

**c)**	The successor is removed by a second, recursive removeFrom on the right subtree. Why can that call never reach the two-children case again? What does this mean for the cost of remove?

**d)**	Trace remove(5) from the root: write every call of removeFrom, the case taken, what is deleted and what is returned to whom. Why does the function end with return node; – what would go wrong with return nullptr;?

**Try it:** Remove 15 from the final tree. Which case is it and what does inorder print? Then remove 12 – the root with two children.

8. **Expression Tree from Postfix (hard) *(10 pts)***

An **expression tree** is a binary tree whose internal nodes are operators and whose leaves are numbers. In Tree\\ExpressionTree.cpp complete the loop of buildFromPostfix: for every token create an ExprNode; if the token is an operator, pop the top of the stack into its **right** child and the next one into its **left** child; push the node. When the input ends, the only node left on the stack is the root. The postfix form of (3 \+ 4\) \* (5 \- 2\) is 3 4 \+ 5 2 \- \*.

                  \*  
                /   \\          prefix  (preorder):  \* \+ 3 4 \- 5 2  
               \+     \-         postfix (postorder): 3 4 \+ 5 2 \- \*  
              / \\   / \\        infix   (inorder):   ((3 \+ 4\) \* (5 \- 2))  
             3   4 5   2

***Expected output***

\[Q8\] prefix : \* \+ 3 4 \- 5 2  
\[Q8\] postfix: 3 4 \+ 5 2 \- \*

**a)**	Why is data a std::string and not an int?

**b)**	Why is the tree built from the **postfix** form and not from the infix form (3 \+ 4\) \* (5 \- 2\) that humans write? What would the builder have to handle for infix?

**c)**	When an operator is read, why is the **first** node popped always the **right** child? Show with 5 2 \- what happens if you pop the other way round.

**d)**	Trace buildFromPostfix("3 4 \+ 5 2 \- \*"): one row per token. Write a subtree as its infix form.

| Token | Action | Stack after the token (bottom → top) |
| :---- | :---- | :---- |
| 3 |  |  |
| 4 |  |  |
| \+ |  |  |
| 5 |  |  |
| 2 |  |  |
| \- |  |  |
| \* |  |  |

**e)**	Why does the preorder traversal print the **prefix** form and the postorder traversal the **postfix** form? What do printPrefix and printPostfix have in common with Q2?

9. **Infix with Parentheses and Evaluation (hard) *(10 pts)***

In Tree\\ExpressionTree.cpp implement printInfix – inorder, but an operator node prints (, its left subtree, the operator, its right subtree and ) – and evaluate: a number node returns stod(node-\>data); an operator node first evaluates both children, then applies the operator. A division whose right value is 0 throws std::runtime\_error("Division by zero"); Week06.cpp catches it.

***Expected output***

\[Q9\] infix  : ((3 \+ 4\) \* (5 \- 2))  
\[Q9\] value  \= 21  
\[Q9\] infix  : (8 / (2 \- 2))  
\[Q9\] Error: Division by zero

**a)**	The tree stores no parentheses, yet the infix output needs them. Why? Show with the tree of (3 \+ 4\) \* 5 what a plain inorder print would say and what a reader would compute from it.

**b)**	Which traversal order is evaluate? Explain why the same evaluate serves the prefix, infix and postfix forms – why is there no “preorder evaluation”?

**c)**	Why does evaluate throw instead of returning 0 or printing a message? What happens to the frames between the throw inside the / node and the catch in Week06.cpp, and why is the line \[Q9\] value \=  never printed for the second tree?

**d)**	What does buildFromPostfix do with the inputs "3 \+" and "3 4"? What goes wrong in each case, and which check would you add?

**Try it:** Build 3 4 5 \* \+ and print its infix form and value: (3 \+ (4 \* 5)) \= 23\. Then try 3 4 \+ 5 \*.

10. **Putting It Together (hard) *(15 pts)***

    **a)**	A tree can be rebuilt from its **inorder** and **preorder** sequences. Rebuild the tree with inorder 4 2 5 1 3 6 and preorder 1 2 4 5 3 6, explain the method step by step, and say whether preorder **and postorder** alone would also determine a tree uniquely.

    

    

    

    

    

    

    

  


    **b)**	Binary tree versus binary search tree – fill in the table.

|  | Binary tree (BT) | Binary search tree (BST) |
| :---- | :---- | :---- |
| Rule for the keys |  |  |
| Inorder traversal prints |  |  |
| Searching a key |  |  |
| Shape is decided by |  |  |
| Example this week |  |  |

    **c)**	Classify the four trees: full, perfect, complete, balanced? Give the height (in edges).

 T1          1              T2          1  
           /   \\                      /   \\  
          2     3                    2     3  
         / \\   / \\                  / \\     \\  
        4   5 6   7                4   5     6  
   
 T3     1                   T4          1  
         \\                            /   \\  
          2                          2     3  
           \\                        / \\   /  
            3                      4   5 6  
             \\  
              4  
               \\  
                5

| Tree | Full? | Perfect? | Complete? | Balanced? | Height |
| :---- | :---- | :---- | :---- | :---- | :---- |
| T1 |  |  |  |  |  |
| T2 |  |  |  |  |  |
| T3 |  |  |  |  |  |
| T4 |  |  |  |  |  |

**d)**	The lecture’s example of a tree is the file system: folders contain files and other folders. Which of the four traversals would you use to (1) print the folder tree with one indentation step per level, (2) compute the size of every folder from the sizes of its files and subfolders, (3) print the keys of a BST in ascending order, (4) find the node closest to the root that satisfies a condition? One reason each.

**e)**	Commit and push (Week 6 \- trees). Write your repository URL here:





Your task is to complete this worksheet's tasks and give me the answers to each question AFTER doing the code part. Propose plan then proceed.
