# codewars-challenges
A gathering of my solution to challenges on codewars.com

The number of kyus correspond to estimated difficulty levels in decreasing order (smaller = more difficult)

# [Alphametics Solver](https://www.codewars.com/kata/5b5fe164b88263ad3d00250b) - 3 kyu
#### Completed: Friday, October 30, 2020
### Python
```python
def alphametics(puzzle):
    (left,right) = puzzle.split(" = ")
    terms = left.split(" + ")
    vars = list(set(("".join(terms)+right)))
    leadingLetters = set([term[0] for term in terms] + [right[0]])

    # transforming the problem into one linear equation with each letter value as the variable
    varsCoeff = dict()
    for c in vars:
        coeff = 0
        for term in terms:
            if c in term:
                coeff += sum([10**(len(term) - i - 1)\
                              for i, ltr in enumerate(term) if ltr == c])  
        if c in right:
            coeff -= sum([10**(len(right) - i - 1)\
                           for i, ltr in enumerate(right) if ltr == c])
        varsCoeff[c] = coeff
    varsCoeff =  dict(sorted(varsCoeff.items(), key=lambda item: -abs(item[1])))
    
    
    
    # exploring the solutions and transforming the chains with the result
    res = recursiveExplore(0, varsCoeff, list(range(0,10)), leadingLetters)[1]
    vars = list(varsCoeff.keys())
    return ("".join([str(res[(vars.index(c))]) if (c in vars) else c for c in puzzle]))


# checks if a given solution can be excluded because even with  
# an optimal letter-digit mapping you'll be too far from zero
def checkPossible(currSum, varsCoeff, availableDigits):
    pos = [coeff for coeff in varsCoeff.values() if coeff > 0]
    neg = [coeff for coeff in varsCoeff.values() if coeff < 0]
    # largest possible combination
    largest = sum([coeff*availableDigits[-1-i] for i,coeff in enumerate(pos)])
    + sum([coeff*availableDigits[i] for i,coeff in enumerate(neg)])
    # smallest possible combination
    smallest = sum([coeff*availableDigits[-1-i] for i,coeff in enumerate(neg)])
    + sum([coeff*availableDigits[i] for i,coeff in enumerate(pos)])
    return largest >= -currSum and smallest <= - currSum


# solves the equation by recursively exploring possible solutions
def recursiveExplore(currSum, varsCoeff, availableDigits, leadingLetters):
    if not(checkPossible(currSum, varsCoeff, availableDigits)):
        return (False, [])
    if not(varsCoeff):
        if currSum == 0:
            return (True, [])
        else:
            return (False, [])
    varsCoeff = dict(varsCoeff)
    currentKey = next(iter(varsCoeff.keys())) 
    currentCoeff = varsCoeff.pop(next(iter(varsCoeff)))
    rangeCosts = [(i,currSum + i*currentCoeff) for i in availableDigits]
    sortedCosts = list(sorted(rangeCosts, key = lambda e : abs(e[1])))

    if currentKey in leadingLetters and 0 in availableDigits: # no leading zero
        rangeCosts.pop(0)
    for i, cost in rangeCosts:
        newDigits = list(availableDigits)
        newDigits.remove(i)
        res = recursiveExplore(cost, varsCoeff, newDigits, leadingLetters)
        if(res[0]):
            return (True, [i] + res[1])
    return (False, [])
        
```

# [Sum by Factors](https://www.codewars.com/kata/54d496788776e49e6b00052f) - 4 kyu
#### Completed: Thursday, October 29, 2020
### C++
```c++
#include <string>
#include <vector>

class SumOfDivided
{
public:
    static std::string sumOfDivided(std::vector<int> &lst);
};

static bool abs_compare(int a, int b)
{
    return (std::abs(a) < std::abs(b));
}
 

std::string SumOfDivided::sumOfDivided(std::vector<int> &lst)
{
  std::string res;
  
  if(lst.empty())
    return res;

  std::vector<int> lstCopy(lst);
  std::vector<int>::iterator it, itCopy;
  int i,sum;
  bool hasOccured;
  int nbIter = std::abs(*(max_element(lst.begin(), lst.end(), abs_compare)));
  
  for(i = 2; i <= nbIter; ++i)
  {
    sum = 0;
    hasOccured = false;
    for(it = lst.begin(), itCopy = lstCopy.begin();
        it < lst.end() && itCopy < lstCopy.end(); ++it, ++itCopy)
    {
      if(*itCopy % i == 0)
      {
        sum += *it;
        for(;*itCopy % i == 0;*itCopy /= i);
        hasOccured = true;
      }
    }
    if(hasOccured)
      res += "(" + std::to_string(i) + " " + std::to_string(sum) + ")";
  }
  return res;
}
```

# [Detect Pangram](https://www.codewars.com/kata/545cedaa9943f7fe7b000048) - 6 kyu
#### Completed: Thursday, October 29, 2020
### C
```c
#include <stdbool.h>

bool is_pangram(const char *str_in) {
  bool containsLetter[26];
  for(int i = 0; i<26; containsLetter[i++] = false);
  for(const char* curr = str_in; *curr; curr++)
  {
    if(*curr >= 'a' && *curr <= 'z')
      containsLetter[*curr - 'a'] = true;
    if(*curr >= 'A' && *curr <= 'Z')
      containsLetter[*curr - 'A'] = true;
  }
  for(int i = 0; i<26; ++i)
  {
    if(!containsLetter[i])
      return false;
  }
  return true;
    //  <----  hajime!

}
```

# [Does my number look big in this?](https://www.codewars.com/kata/5287e858c6b5a9678200083c) - 6 kyu
#### Completed: Wednesday, October 28, 2020
### C
```c
#include <stdbool.h>
#include <math.h>

bool narcissistic(int num)
{
  int i,j,sum=0;
  for(i = 0; pow(10,i) < num; i++);
  for(j = num; j > 0; j/=10)
    sum += pow(j%10, i);
  return sum==num;
}
```

# [Bit Counting](https://www.codewars.com/kata/526571aae218b8ee490006f4) - 6 kyu
#### Completed: Wednesday, October 28, 2020
### C
```c
#include <stddef.h>

size_t countBits(unsigned value)
{
  unsigned int res = 0;
  for(unsigned int i = value; i > 0; i /= 2)
    res += (i%2);
  return res;
}
```

# [Is a number prime?](https://www.codewars.com/kata/5262119038c0985a5b00029f) - 6 kyu
#### Completed: Wednesday, October 28, 2020
### C
```c
#include <stdbool.h>
#include <math.h>

bool is_prime(int num)
{
  if(num < 2) return false;
  for(int i = 2; i < sqrt(num + 1); ++i)
  {
    if(num%i == 0) return false;
  }
  return true;
}
```

# [Sum of a sequence](https://www.codewars.com/kata/586f6741c66d18c22800010a) - 7 kyu
#### Completed: Wednesday, October 28, 2020
### C
```c
unsigned sequence_sum(unsigned start, unsigned end, unsigned step)
{
  unsigned sum = 0;
  for(unsigned i=start;i<=end;i+=step)
    sum+=i;
  return sum;
}
```

# [Complementary DNA](https://www.codewars.com/kata/554e4a2f232cdd87d9000038) - 7 kyu
#### Completed: Wednesday, October 28, 2020
### C
```c
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

char *dna_strand(const char *dna)
{
  char *ret = (char*)malloc(strlen(dna)+1), *retIt = ret;
  while (*dna)
  {
    printf("%c", *dna);
    switch(*(dna++))
    {
      case 'G':
        *(retIt++) = 'C';
        break;
      case 'T':
        *(retIt++) = 'A';
        break;
      case 'C':
        *(retIt++) = 'G';
        break;
      case 'A':
        *(retIt++) = 'T';
        break;
      default:
        *(retIt++) = *dna;
        break;   
    }
  }
  *retIt = '\0';
  return ret;
}
```

# [Reverse words](https://www.codewars.com/kata/5259b20d6021e9e14c0010d4) - 7 kyu
#### Completed: Wednesday, October 28, 2020
### C
```c
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

char* reverseWords(const char* text) {
  char* newText = (char*) malloc(strlen(text)+1);
  strcpy(newText, text);
  unsigned int i, j, wordStart = 0;
  for(i = 0; i < strlen(newText) + 1; ++i)
  {
    if(newText[i] == ' ' || i == strlen(newText))
    {
      for(j = wordStart; j < i; ++j)
        newText[i - 1 + (wordStart - j)] = text[j];
      wordStart = i + 1;
    }
  }
  return newText;

}
```

# [String ends with?](https://www.codewars.com/kata/51f2d1cafc9c0f745c00037d) - 7 kyu
#### Completed: Wednesday, October 28, 2020
### C
```c
#include <stdbool.h>
#include <string.h>
#include <stdio.h>


bool solution(const char *string, const char *ending)
{
  if(*string != '\0' || *ending == '\0')
    return strcmp(string, ending)==0 || solution(string+1, ending);
  return false;
}
```

# [Vowel Count](https://www.codewars.com/kata/54ff3102c1bad923760001f3) - 7 kyu
#### Completed: Wednesday, October 28, 2020
### C
```c
#include <stddef.h>


size_t get_count(const char *s)
{
    if (*s == '\0')
      return 0;
    if(*s == 'a' || *s == 'e' ||  *s == 'i' ||  *s == 'o' ||  *s == 'u')
      return 1 + get_count(s+1);
    return get_count(s+1);
}
```

# [Sum of Digits / Digital Root](https://www.codewars.com/kata/541c8630095125aba6000c00) - 6 kyu
#### Completed: Wednesday, October 28, 2020
### C
```c
int digital_root(int n) {
  if(n < 10)
    return n;
  int sum = 0;
  int currentDigit;
  int currentReminder = n;
  while(currentReminder > 0)
  {
    currentDigit = currentReminder % 10;
    currentReminder /= 10;
    sum += currentDigit;
  }
  
  return digital_root(sum);
  // .....
}
```

# [Sudoku Solution Validator](https://www.codewars.com/kata/529bf0e9bdf7657179000008) - 4 kyu
#### Completed: Saturday, October 24, 2020
### Python
```python
def valid_solution(board):
    rowsOk = not(sum([len(set(row)) != 9 for row in board]))
    colsOk = not(sum([len(set([board[i][j] for i in range(9)])) != 9 for j in range(9)]))
    gridsOk = not(sum([len(set([board[i%3+3*(j%3)][i//3+3*(j//3)] for i in range(9)])) != 9  for j in range(9)]))
    return rowsOk and colsOk and gridsOk
```

# [Human Readable Time](https://www.codewars.com/kata/52685f7382004e774f0001f7) - 5 kyu
#### Completed: Saturday, October 24, 2020
### Python
```python
def make_readable(seconds):
    return "{:02d}:{:02d}:{:02d}".format(seconds // 3600, seconds % 3600 // 60, seconds % 60)

```

# [Calculating with Functions](https://www.codewars.com/kata/525f3eda17c7cd9f9e000b39) - 5 kyu
#### Completed: Friday, October 23, 2020
### Python
```python
import operator

def zero(arg=[]): return arg[0](0,arg[1]) if arg else 0
def one(arg=[]): return arg[0](1,arg[1]) if arg else 1
def two(arg=[]): return arg[0](2,arg[1]) if arg else 2
def three(arg=[]): return arg[0](3,arg[1]) if arg else 3
def four(arg=[]): return arg[0](4,arg[1]) if arg else 4
def five(arg=[]): return arg[0](5,arg[1]) if arg else 5
def six(arg=[]):  return arg[0](6,arg[1]) if arg else 6
def seven(arg=[]): return arg[0](7,arg[1]) if arg else 7
def eight(arg=[]):  return arg[0](8,arg[1]) if arg else 8
def nine(arg=[]): return arg[0](9,arg[1]) if arg else 9

def plus(num):  return (operator.add,num)
def minus(num): return (operator.sub,num)
def times(num): return (operator.mul,num)
def divided_by(num): return (operator.floordiv,num)
    
```

# [Highest Scoring Word](https://www.codewars.com/kata/57eb8fcdf670e99d9b000272) - 6 kyu
#### Completed: Friday, October 23, 2020
### Python
```python
def high(x):
    return max(x.split(" "), key = lambda w : sum([ord(c) - ord('a') for c in w]))

```

# [Your order,  please](https://www.codewars.com/kata/55c45be3b2079eccff00010f) - 6 kyu
#### Completed: Friday, October 23, 2020
### Python
```python
def order(sentence):
    if len(sentence) == 0:
        return ""
    return " ".join(sorted(sentence.split(" "),\
                 key = lambda w : int(next(filter(lambda c: c.isdigit(),w),None))))
```

# [Growth of a Population](https://www.codewars.com/kata/563b662a59afc2b5120000c6) - 7 kyu
#### Completed: Friday, October 23, 2020
### Python
```python
import math 
def nb_year(p0, percent, aug, p):
    if percent == 0:
        return math.ceil((p-p0)/aug)
    return math.ceil(math.log((p+aug/(percent/100)) /  (p0+aug/(percent/100)),1+percent/100))
```

# [Are they the "same"?](https://www.codewars.com/kata/550498447451fbbd7600041c) - 6 kyu
#### Completed: Friday, October 23, 2020
### C++
```c++
#include <vector>

bool absComp(int i,int j)
{
   return abs(i)<abs(j);
}

class Same {
public :
    static bool comp(std::vector<int>&a, std::vector<int>&b) {
      if(a.size() != b.size())
        return false;
      std::sort(a.begin(), a.end(), absComp);
      std::sort(b.begin(), b.end(), absComp);
      for(unsigned int i = 0; i < a.size(); ++i)
      {
        if(a[i]*a[i] != b[i])
          return false;
      }
      return true;
    }
};

```

# [Create Phone Number](https://www.codewars.com/kata/525f50e3b73515a6db000b83) - 6 kyu
#### Completed: Friday, October 23, 2020
### C++
```c++
#include <string>

std::string createPhoneNumber(const int arr [10]){
  std::string res = "(";
  int i;
  for(i = 0; i < 3; res+=std::to_string(arr[i++]));
  res += ") ";
  for(; i < 6; res+=std::to_string(arr[i++]));
  res += "-";
  for(; i < 10; res+=std::to_string(arr[i++]));
  return res;
}
```

# [Playing with digits](https://www.codewars.com/kata/5552101f47fc5178b1000050) - 6 kyu
#### Completed: Friday, October 23, 2020
### C
```c
#include <stdlib.h>
#include <stdio.h>
#include <math.h>

int digPow(int n, int p) {
  int sum = 0, initialN=n;
  p += log10(n);
  while(n>0)
  {
    sum += pow((n - 10*(n/10)),p--);
    n /= 10;
  }
  if(sum%initialN == 0)
    return (sum/initialN);
  return -1;
}
```

# [Take a Ten Minute Walk](https://www.codewars.com/kata/54da539698b8a2ad76000228) - 6 kyu
#### Completed: Friday, October 23, 2020
### Python
```python
def is_valid_walk(walk):
    #determine if walk is valid
    return len(walk) == 10 and walk.count('n') == walk.count('s') and walk.count('w') == walk.count('e')
    pass
```

# [Valid Braces](https://www.codewars.com/kata/5277c8a221e209d3f6000b56) - 6 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
def validBraces(string):
    bracketList = []
    for c in string:
        if c in  [']', ')', '}']:
            if not bracketList or (abs(ord(bracketList[-1]) - ord(c))) > 2:
                return False
            bracketList.pop()
        else:
            bracketList.append(c)
    return not bracketList

```

# [Moving Zeros To The End](https://www.codewars.com/kata/52597aa56021e91c93000cb0) - 5 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
def isZero(x):
    return (x is not False) and x == 0
def move_zeros(array):
    return [x for x in array if not(isZero(x))] + list(filter(isZero,array))
```

# [Array.diff](https://www.codewars.com/kata/523f5d21c841566fde000009) - 6 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
def array_diff(a, b):
    return [elem for elem in a if not (elem in b)]
```

# [Sums of Parts](https://www.codewars.com/kata/5ce399e0047a45001c853c2b) - 6 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
def parts_sums(ls):
    res = [sum(ls)]
    [res.append(res[-1]-elem) for elem in ls]
        
    return res
```

# [L1: Set Alarm](https://www.codewars.com/kata/568dcc3c7f12767a62000038) - 8 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
def set_alarm(employed, vacation):
    return True if employed and not(vacation) else False
```

# [Beginner Series #3 Sum of Numbers](https://www.codewars.com/kata/55f2b110f61eb01779000053) - 7 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
from functools import reduce
import operator
def get_sum(a,b):
    return (reduce(operator.add,list(range(min(a,b),max(a,b)+1))))
```

# [Persistent Bugger.](https://www.codewars.com/kata/55bf01e5a717a0d57e0000ec) - 6 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
from functools import reduce
import operator

def persistence(n):
    string = str(n)
    i = 0
    while len(string) > 1:
        i += 1
        string = str(reduce(operator.mul,[int(c) for c in string]))
    return i
```

# [Find the odd int](https://www.codewars.com/kata/54da5a58ea159efa38000836) - 6 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
def find_it(seq):
    seqCount = dict()
    for i in seq:
        if not (i in seqCount.keys()):
            seqCount[i] = 1
        else:
            seqCount[i] += 1
    print(seqCount)
    oddIndex = [count%2 == 1 for (i, count) in seqCount.items()].index(True)
    return list(seqCount.keys())[oddIndex]


```

# [Stop gninnipS My sdroW!](https://www.codewars.com/kata/5264d2b162488dc400000001) - 6 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
def spin_words(sentence):
    res = ""
    words = sentence.split(" ")
    for word in words:
        if len(word) >= 5:
            res += word[::-1] + " "
        else:
            res += word + " "
    # Your code goes here
    return res[:-1]
```

# [Multiply](https://www.codewars.com/kata/50654ddff44f800200000004) - 8 kyu
#### Completed: Thursday, October 22, 2020
### Python
```python
def multiply(a, b):
  return (a * b)
```

