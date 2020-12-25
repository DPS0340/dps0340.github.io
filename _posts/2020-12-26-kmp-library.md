---
title: KMP 라이브러리
excerpt: 다들 알지만 짜기 귀찮은 그것
---

옛날에 객체지향으로 만들어서 보관 중이었는데, 이렇게라도 블로그에 올려본다.

```c++
#include <algorithm>
#include <cassert>
#include <iostream>
#include <memory>
#include <string>
#include <vector>

using namespace std;

class Loss;
class StringWrapper;
class Solve;

class Loss {
  private:
    vector<int> table;
    int len;
    void construct(string f);

  public:
    Loss(string f);
    int operator[](int idx) const;
};

class Solve {
  private:
    string s, f;
    int n, k;
    vector<string> sv;
    vector<string> table;
    int kmp(const StringWrapper &s, const StringWrapper &f);
    int magic(string &s1, int n);
    void make_table(vector<bool> &visited, vector<string> &cache, int k);

  public:
    Solve();
    void run();
};

class StringWrapper {
  private:
    string s;
    int i;
    int len;
    Loss l;

  public:
    StringWrapper(string s);
    int get_loss_index(int idx) const;
    char operator[](int idx) const;
    int get_len() const;
    void twice();
};
StringWrapper::StringWrapper(string s) : l{s} {
    this->s = s;
    this->len = s.length();
}

int StringWrapper::get_loss_index(int idx) const {
    assert(0 <= idx && idx < len);
    return l[idx];
}
char StringWrapper::operator[](int idx) const {
    assert(0 <= idx && idx < len);
    return s[idx];
}
int StringWrapper::get_len() const { return len; }

void Solve::make_table(vector<bool> &visited, vector<string> &cache, int k) {
    if (k == n) {
        string s1;
        for (int i = 0; i < n; i++) {
            s1.append(cache[i]);
        }
        table.push_back(s1);
        return;
    }
    if (k == 0) {
        visited[0] = 1;
        cache.push_back(sv[0]);
        make_table(visited, cache, k + 1);
        cache.pop_back();
        visited[0] = 0;
        return;
    }
    for (int i = 1; i < n; i++) {
        if (!visited[i]) {
            visited[i] = 1;
            cache.push_back(sv[i]);
            make_table(visited, cache, k + 1);
            cache.pop_back();
            visited[i] = 0;
        }
    }
}

Loss::Loss(string f) {
    len = f.length();
    table.resize(len);
    fill(table.begin(), table.end(), 0);
    construct(f);
}

Solve::Solve() {
    cin >> n;
    for (int i = 0; i < n; i++) {
        string s1;
        cin >> s1;
        sv.push_back(s1);
    }
    cin >> k;
}

int Solve::magic(string &s1, int n) {
    string s2{s1};
    s2.append(s2);
    StringWrapper wrap{s2};
    StringWrapper f{s1};
    int res = kmp(wrap, f);
    if (res == k) {
        return n;
    }
    return 0;
}

int Solve::kmp(const StringWrapper &s, const StringWrapper &f) {
    int cnt = 0;
    int len = s.get_len();
    int len_f = f.get_len();
    for (int i = 0, j = 0; i < len; i++) {
        while (j > 0 && s[i] != f[j]) {
            j = f.get_loss_index(j - 1);
        }
        if (s[i] == f[j]) {
            if (j == len_f - 1) {
                j = f.get_loss_index(j);
                if (i != len - 1) {
                    cnt++;
                }
            } else {
                j++;
            }
        }
    }
    return cnt;
}

void Solve::run() {
    vector<bool> visited(n);
    vector<string> cache;
    make_table(visited, cache, 0);
    // 수행 부분 추가

    // 수행 부분 끝
}

void Loss::construct(string f) {
    for (int i = 1, j = 0, len = f.length(); i < len; i++) {
        while (j > 0 && f[i] != f[j]) {
            j = table[j - 1];
        }
        if (f[i] == f[j]) {
            table[i] = ++j;
        }
    }
}

int Loss::operator[](int idx) const {
    assert(0 <= idx && idx < len);
    return table[idx];
}

int main(void) {
    cin.tie(NULL);
    ios_base::sync_with_stdio(false);
    Solve solve;
    solve.run();
    return 0;
```