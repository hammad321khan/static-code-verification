# Code Review Report

Static Code Verification: game.cpp

| Line number | Defect type | Why it is a problem | Proposed solution | Severity |
|---|---|---|---|---|
| 27 | Initialization | The array of Monster* is never initialized. Unused slots hold garbage addresses, so any read or delete of them is undefined. | Initialize: Monster* monsters[MAX_MONSTERS] = {nullptr}; | Low |
| 41 (called at 119) | Array bounds / buffer overflow | spawnMonster() never checks monsterCount < MAX_MONSTERS. main() spawns a 6th monster, writing past the end of the array (memory corruption). | Add if (monsterCount >= MAX_MONSTERS) return false; before storing and report the failure. | High |
| 47 | Control flow (= instead of ==) | The condition assigns id to monsters[i]->id and is always true, so the first monster is returned and its id is overwritten. | Use comparison: if (monsters[i]->id == id). | High |
| 54-56 | Null pointer dereference | findMonster() returns nullptr for an unknown id (main passes 99 at line 123). m->health then crashes the program. | Add if (m == nullptr) return -1; right after findMonster(). | High |
| 55-56 | Arithmetic (signed / unsigned) | dmg is unsigned, so (m->health - dmg) is unsigned and "< 0" is never true. A negative damage becomes a huge positive number. | Use int: if (m->health - damage < 0) { ... } and reject negative damage. | High |
| 64 | Loop off-by-one | i <= monsterCount reads monsters[monsterCount], an uninitialized slot, and dereferences it (crash or garbage). | Change the condition to i < monsterCount. | High |
| 70-73 | Dangling reference (function return) | getMonsterLabel() returns a reference to the local string label, which is destroyed when the function ends. Using the result is undefined behavior. | Return by value: std::string getMonsterLabel(int id). | High |
| 71-72 | Null pointer dereference | m may be nullptr and m->id / m->name are used without a check. | Check m == nullptr and return an empty or "Unknown" label. | Medium |
| 77 | Initialization | double sum is not initialized, so sum += ... starts from garbage. | double sum = 0.0; | High |
| 80 | Arithmetic (divide by zero / truncation) | If monsterCount is 0 the division is by zero. The double result is also silently truncated to int. | Check monsterCount == 0 and return 0; return double or cast explicitly. | Medium |
| 85-86 | Use after delete | m->name is read after delete m, which is undefined behavior. | Print the name first, then delete. Also check m for nullptr. | High |
| 85 | Dangling pointer / double delete | The deleted pointer stays in monsters[], and the destructor (line 37) deletes it again. | Remove it from the array (shift the remaining items, monsterCount--) before or when deleting. | High |
| 91 | Arithmetic (integer division) | attack * armorPercent / 100 is integer division, so 80*25/100 loses decimals before it becomes double. The product can also overflow for large values. | Use attack * armorPercent / 100.0. | Medium |
| 94-98 | Control flow (missing return) | playerRank() returns nothing when score <= 100 (main calls it with 50 at line 128). Undefined return value. | Add a final else return 3; (or a default return after the if chain). | High |
| 102 / 109 | Pointers (mismatched delete) | Memory from new char[10] must be freed with delete[], not delete. This is undefined behavior. | Use delete[] name; or better, use std::string name. | Medium |
| 105 | Buffer overflow | std::cin >> name can write more than 9 characters into char[10] and overwrite memory. | Use std::string name; std::cin >> name; (or std::setw(10)). | High |
| 121 | Memory leak | bonus is allocated with new and never deleted. | Add delete bonus; or use a stack object / std::unique_ptr. | Medium |
| 133 | Class design (shallow copy) | Game owns raw pointers but has no copy constructor or operator=. "Game copy = game;" copies the pointers, and both objects delete the same monsters (double delete). | Implement deep copy (Rule of Three) or delete the copy operations; better, use std::vector<std::unique_ptr<Monster>>. | High |
| 25-39 | Class design (Rule of Three) | The destructor frees memory but the copy constructor and assignment operator are missing. | Define or delete: Game(const Game&) = delete; Game& operator=(const Game&) = delete; | Medium |
| 11-16 | Class design (encapsulation) | All Monster data members are public, so any code can corrupt health or id. | Make them private and add getters and setters; use an initializer list in the constructor. | Low |
| 123 | Function call (invalid argument) | attackMonster(99, 20) is called with an id that does not exist and the return value is not validated. | Validate the id and handle the error result in main(). | Medium |
| 133 (end of file) | Incomplete code | The file ends after "Game copy = game;" with no return 0; and no closing brace for main(). | Add return 0; and the closing }. | Low |
