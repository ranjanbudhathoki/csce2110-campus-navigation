# Campus Navigation and Meeting Point Search

CSCE 2110 - Foundations of Data Structures - Final Project

Team: Rahul BC, Sushant KC, Ranjan Budhathoki

## What this is

This program treats the UNT campus like a graph - each building is a node, and each walking connection between two buildings is an edge. We load the graph from a text file (college_map.txt) and the program can do 5 things:

1. Show every building you can reach from a starting building within a certain number of connections (BFS)
2. Find the shortest route between two buildings if it exists under a connection limit (BFS)
3. Find the shortest route from a start to a destination that also passes through two required stops, in whichever order ends up shorter
4. Find the shortest round trip that starts somewhere, visits every reachable building, and comes back (this is basically the Traveling Salesman Problem, solved with bitmask DP)
5. Given 3 people starting at different buildings, find the best building for them to meet at (lowest total connections for all 3) and print each person's route there

One thing to note: we originally built this using unordered_map for the building lookups and priority_queue for Dijkstra's algorithm, but later realized those aren't allowed for this project. So we switched the lookups over to our own hash map (separate chaining) and swapped Dijkstra out for BFS instead - since every connection counts as just "1 connection" with no weights, Dijkstra and BFS give the exact same answer anyway, so there was no reason to build a custom priority queue just to get the same result we were already getting.

## Compiler / requirements

- Any compiler that supports C++17 works fine (we only use stuff from the standard library - iostream, fstream, vector, queue, algorithm, utility, string. No outside libraries.)
- We compiled and tested this with Visual Studio (MSVC) on Windows. It should also build fine with g++ or clang++ on Linux/Mac since we're only using standard C++17 - no Windows-specific or compiler-specific stuff.

## How to compile and run it

If you're using Visual Studio (what we used): create a new Empty C++ Project, add main.cpp and college_map.txt to the project, make sure the project's C++ language standard is set to C++17 or later (Project Properties > C/C++ > Language > C++ Language Standard), then build and run (Ctrl+F5). Visual Studio runs the program from the project folder, so college_map.txt needs to be there for it to find it.

If you'd rather use the command line with MSVC's compiler:

```
cl /EHsc /std:c++17 main.cpp /Fe:campus_navigation.exe
campus_navigation.exe
```

On Linux/Mac with g++ instead:

```
g++ -std=c++17 -O2 -o campus_navigation main.cpp
./campus_navigation
```

Everything just prints straight to the console, there's no output file being created.

## About the input file

- File name: college_map.txt
- It needs to be sitting in the same folder as the compiled program when you run it, since we open it with a relative path.
- Format looks like this:

```
BUILDINGS
Building_Name_1
Building_Name_2
...
EDGES
Building_Name_1 Building_Name_2
Building_Name_2 Building_Name_3
...
```

Buildings go under BUILDINGS, one per line, with underscores instead of spaces (like Willis_Library). Then under EDGES, each line is a pair of buildings that are directly connected. All connections are undirected and all count the same (just "1 connection," we don't factor in actual walking distance).

Our college_map.txt has 20 UNT buildings and 40 connections based on the official UNT campus map.

## How to test it

Right now all the test cases are just hardcoded at the bottom of main() - there's no menu or command line input. If you want to try a different building or route, just change the arguments being passed into the show... functions and recompile.

main() currently runs:
- Reachability from Willis_Library within 2 connections
- Shortest route from Willis_Library to Science_Research_Building, must be under 5 connections
- Two different examples of the two-stop routing question
- Shortest round trip starting at University_Union
- Best meeting point for people starting at Willis_Library, Chemistry_Building, and Art_Building

To test the "building doesn't exist" or "no route exists" cases, just pass in a building name that's misspelled or isn't in the map, or buildings that aren't actually connected to each other within whatever limit you set - the program catches these and prints a message instead of crashing.

## Limitations we know about

- The round trip function (showShortestRoundTrip) uses bitmask DP which gets exponentially slower the more reachable buildings there are, so we capped it - it'll refuse to run and print a message if there are more than 20 reachable buildings instead of freezing.
- No command line args or separate test file, everything's hardcoded in main() right now.
- We're treating every connection as equal (1 connection), not actual distance or walking time.
