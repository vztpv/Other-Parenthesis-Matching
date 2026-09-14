# Other-Parenthesis-Matching

# General??
```c++ 
Pos* solve(const char* str, int64_t n) {
	Pos* vec = g_vecPoolSerial.acquire(n); // memory? pool!
	std::vector<Pos> _stack; _stack.reserve(n / 2);

	for (int64_t i = 0; i < n; ++i) {
		if (str[i] == '(') {
			_stack.push_back(i);
		}
		else {
			if (!_stack.empty()) {
				Pos pos = _stack.back(); _stack.pop_back();
				vec[i] = pos;
				vec[pos] = i;
			}
		}
	}
	return vec; // 주의: 이 포인터는 풀 소유이므로 free() 하지 말 것
}
```
# My Own?
```c++
Pos* solve_other(const char* str, int64_t n) {
	Pos* mate = g_vecPoolSerial2.acquire(n);
	std::vector<Pos> _stack; _stack.reserve(n / 2);
	int64_t now = -1;

	for (int64_t i = 0; i < n; i += 2) {
		mate[i] = i + 2;
		mate[i + 1] = i + 1;

		if (str[i] == ')') { // ...) ( ) ( -> ...) ) ) (
			const int64_t before = -now - 1;
			const int64_t next_now = mate[before];
			mate[before] = i + 1;
			mate[i] = before + 1;
			now = next_now;
		}

		if (str[i + 1] == '(') { // ..( ) ( -> ..( ( )
			if (str[i] == '(') {
				mate[i] = now;
				mate[i + 1] = -(i + 1);
			}
			else {
				mate[i + 1] = now;
			}
			now = -(i + 1 + 1);
		}
		else if (str[i] == ')' && now < 0) {
			const int64_t before = -now - 1;

			const int64_t next_now = mate[before];

			mate[before] = i + 1 + 1;
			mate[i + 1] = before + 1;

			now = next_now;
		}
	}
	return mate; // 주의: 이 포인터는 풀 소유이므로 free() 하지 말 것
}
// maybe has bug..?
```
