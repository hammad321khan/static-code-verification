# Review Checklist

Static Code Verification: game.cpp

| # | Category | Result (ISSUE / OK) | Defects found (line numbers of game.cpp) |
|---|---|---|---|
| 1 | Initialization | ISSUE | Line 27: monsters[] pointers never initialized. Line 77: local variable sum used without initial value. |
| 2 | Pointers (null / delete) | ISSUE | Lines 54, 71: findMonster() can return nullptr and it is dereferenced. Lines 85-86: monster used after delete. Line 85: deleted pointer stays in array, so the destructor deletes it again (double delete). Line 109: delete used on memory from new[]. Line 121: bonus is never deleted (memory leak). |
| 3 | Arrays / buffers | ISSUE | Line 41 (called at line 119): no bounds check, 6th monster is written past the 5-element array. Line 64: reads monsters[monsterCount], outside valid data. Lines 102 and 105: char[10] buffer can overflow with cin >> name. |
| 4 | Loops | ISSUE | Line 64: condition i <= monsterCount is off by one, it should be i < monsterCount. |
| 5 | Arithmetic | ISSUE | Lines 55-56: unsigned dmg makes (health - dmg) never negative and turns negative damage into a huge number. Line 80: division by zero when monsterCount is 0, and double result is truncated to int. Line 91: integer division before conversion to double loses decimals. |
| 6 | Control flow | ISSUE | Line 47: "=" used instead of "==" in the if condition. Lines 94-98: playerRank() has no return when score <= 100. |
| 7 | Function call | ISSUE | Lines 70-73: getMonsterLabel() returns a reference to a local variable (dangling reference). Line 123 in main: attackMonster(99, 20) calls with an id that does not exist. Line 109/119: spawn and delete calls are used with unsafe arguments. |
| 8 | Classes | ISSUE | Lines 25-39 and 133: Game owns raw pointers but has no copy constructor or assignment operator (Rule of Three), so "Game copy = game;" makes a shallow copy and causes double delete. Lines 11-16: Monster data members are public. |
