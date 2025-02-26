# CS_210_Midterm_Project
# Ryan Svezia
#include <iostream>
#include <sstream>
#include <fstream>
#include <vector>
using namespace std;

struct School {
  string name;
  string address;
  string city;
  string state;
  string county;
  School* next;

  School(string n, string a, string c, string s, string co) : name(n), address(a), city(c), state(s), county(co) {}
};

class SchoolList {
  private: School* head;
    public: SchoolList() : head(nullptr) {}
    void insertFirst(const School& school) {
      School* newSchool = new School(school);
      newSchool->next = head;
      head = newSchool;
    }
    void insertLast(const School& school) {
      School* newSchool = new School(school);
      if (!head) {
        head = newSchool;
        return;
      }
      School* temp = head;
      while (temp->next) {
        temp = temp->next;
      }
      temp->next = newSchool;
    }
    void deleteByName(const string& name) {
      if (!head) return;
      if (head->name == name) {
        School* temp = head;
        head = head->next;
        delete temp;
        return;
      }
      School* current = head;
      while (current->next && current->next->name != name) {
        current = current->next;
      }
      if (current->next) {
        School* temp = current->next;
        current->next = current->next->next;
        delete temp;
      }
    }
    School* findByName(const string& name) {
      School* current = head;
      while (current) {
        if (current->name == name) {
          return current;
        }
        current = current->next;
      }
      return nullptr;
    }
    void display() {
      School* current = head;
      while (current) {
        cout << "Name: " << current->name << ", Address: " << current->address << ", City: " << current->city
             << ", State: " << current->state << ", County: " << current->county << endl;
        current = current->next;
      }
    }
    ~SchoolList() {
      while (head) {
        School* temp = head;
        head = head->next;
        delete temp;
      }
    }
};

class CSVReader {
  public: static vector<vector<string>> readCSV(const string& filename) {
      ifstream file(filename);
      vector<vector<string>> data;
      string line, word;
      if (!file.is_open()) {
        cerr << "Error: Could not open file " << filename << endl;
        return data;
      }

    while (getline(file, line)) {
      stringstream ss(line);
      vector<string> row;
      while (getline(ss, word, ',')) {
        row.push_back(word);
      }
      data.push_back(row);
    }
    file.close();
    return data;
  }
};

int main() {
  SchoolList schoolList;
  string filename = "schools.csv";
  vector<vector<string>> data = CSVReader::readCSV(filename);
  for (const auto& row : data) {
    if (row.size() >= 5) {
      School school(row[0], row[1], row[2], row[3], row[4]);
      schoolList.insertLast(school);
    }
  }
  cout << "All schools in list:\n";
  schoolList.display();

  cout << "\nEnter school name to find: ";
  string searchName;
  getline(cin, searchName);
  School* found = schoolList.findByName(searchName);
  if (found) {
    cout << "Found school - Name: " << found->name << ", Address: " << found->address << endl;
  } else {
    cout << "School not found" << endl;
  }
  cout << "\nEnter school name to delete: ";
  string deleteName;
  getline(cin, deleteName);
  schoolList.deleteByName(deleteName);
  cout << "Updated School List:\n";
  schoolList.display();

  return 0;
}
