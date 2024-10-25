#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <cmath>
#include <nlohmann/json.hpp>  // JSON library

using json = nlohmann::json;
using namespace std;

long long decodeBaseToDecimal(const string& value, int base) {
    long long decimalValue = 0;
    for (char digit : value) {
        decimalValue = decimalValue * base + (digit - '0');
    }
    return decimalValue;
}
double findConstantTerm(const vector<pair<int, long long>>& points) {
    double constant = 0.0;
    int k = points.size();
    for (int i = 0; i < k; ++i) {
        double term = points[i].second;
        for (int j = 0; j < k; ++j) {
            if (i != j) {
                term *= static_cast<double>(-points[j].first) / (points[i].first - points[j].first);
            }
        }
        constant += term;
    }
    return constant;
}
int main(int argc, char* argv[]) {
    if (argc < 2) {
        cerr << "Usage: " << argv[0] << " <input_file.json>" << endl;
        return 1;
    }
    ifstream inputFile(argv[1]);
    if (!inputFile.is_open()) {
        cerr << "Error: Could not open file " << argv[1] << endl;
        return 1;
    }
    json testCase;
    inputFile >> testCase;
    inputFile.close();
    int k = testCase["keys"]["k"];
    vector<pair<int, long long>> points;
    for (auto& item : testCase.items()) {
        if (item.key() == "keys") continue;
        int x = stoi(item.key());                 
        int base = item.value()["base"];          
        string encodedValue = item.value()["value"]; 
        long long y = decodeBaseToDecimal(encodedValue, base);  
        points.push_back({x, y});
    }
    points.resize(k);
    double constantTerm = findConstantTerm(points);
    cout << "Constant term (c): " << round(constantTerm) << endl;

    return 0;
}
