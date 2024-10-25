#include <iostream>
#include <fstream>
#include <cmath>
#include <vector>
#include <string>
#include <nlohmann/json.hpp>

using json = nlohmann::json;
using namespace std;

long long decodeBaseToDecimal(const string& value, int base) {
    long long decimalValue = 0;
    for (char digit : value) {
        decimalValue = decimalValue * base + (digit - '0');
    }
    return decimalValue;
}

double lagrangeInterpolation(const vector<pair<int, long long>>& points) {
    double constantTerm = 0.0;
    int k = points.size();
    for (int i = 0; i < k; ++i) {
        double term = points[i].second;
        for (int j = 0; j < k; ++j) {
            if (i != j) {
                term *= static_cast<double>(-points[j].first) / (points[i].first - points[j].first);
            }
        }
        constantTerm += term;
    }
    return constantTerm;
}

int main() {
    std::ifstream inputFile("testcase.json");
    if (!inputFile.is_open()) {
        cerr << "Error: Could not open input file." << endl;
        return 1;
    }
    json testCase;
    inputFile >> testCase;
    inputFile.close();
    int n = testCase["keys"]["n"];
    int k = testCase["keys"]["k"];
    vector<pair<int, long long>> points;
    for (json::iterator it = testCase.begin(); it != testCase.end(); ++it) {
        if (it.key() == "keys") continue;
        int x = stoi(it.key());
        int base = it.value()["base"];
        string encodedValue = it.value()["value"];
        long long y = decodeBaseToDecimal(encodedValue, base);
        points.push_back({x, y});
    }
    points.resize(k);
    double secretConstant = lagrangeInterpolation(points);
    cout << "Secret constant term (c): " << static_cast<long long>(round(secretConstant)) << endl;
    return 0;
}
