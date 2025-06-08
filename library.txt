#include <iostream>
#include <string> 
#include <fstream> //برای نوشتن و خواندن در فایل
#include <sstream> //برای پردازش در متن

using namespace std;

class parent {
protected:
    virtual void add() = 0;
    virtual void show() = 0;
};

class person {
protected:
    int id;
    string name;
};

class book : public person, public parent {
public:
    void add() override {
        string author;
        double price;
        cout << "Enter book ID: ";
        cin >> id;
        cout << "Enter book name: ";
        cin >> name;
        cout << "Enter author: ";
        cin >> author;
        cout << "Enter price: ";
        cin >> price;

        ofstream file("books.txt", ios::app);
        file << id << "|" << name << "|" << author << "|" << price << "\n";
        file.close();
    }

    void show() override {
        ifstream file("books.txt");
        if (!file) {
            cout << "list is empty." << endl;
            return;
        }

        string line;
        while (getline(file, line)) {
            stringstream obj(line);
            string idStr, name, author, priceStr;
            if (getline(obj, idStr, '|') &&
                getline(obj, name, '|') &&
                getline(obj, author, '|') &&
                getline(obj, priceStr)) {
                try {
                    int id = stoi(idStr);
                    double price = stod(priceStr);
                    cout << "ID: " << id << ", Name: " << name << ", Author: " << author << ", Price: $" << price << endl;
                }
                catch (...) {
                    cout << "Invalid data in line: " << endl;
                }
            }
        }

        file.close();
    }

    bool exists(int id) {
        ifstream file("books.txt");
        if (!file) {
            return false;};

        string line;
        while (getline(file, line)) {
            stringstream obj(line);
            string idStr;
            if (getline(obj, idStr, '|')){
                if (stoi(idStr) == id) {
                    return true;
                }
            }
        }

        return false;
    }
};

class client : public person, public parent {
public:
    void add() override {
        cout << "Enter client ID: ";
        cin >> id;
        cout << "Enter client name: ";
        cin >> name;

        ofstream file("clients.txt", ios::app);
        file << id << "|" << name << "\n";
        file.close();
    }

    void show() override {
        ifstream file("clients.txt");
        if (!file) {
            cout << "list is empty." << endl;
            return;
        }

        string line;
        while (getline(file, line)) {
            stringstream obj(line);
            string idStr, name;
            if (getline(obj, idStr, '|') && getline(obj, name)) {
                try {
                    int id = stoi(idStr);
                    cout << "Client ID: " << id << ", Name: " << name << endl;
                }
                catch (...) {
                    cout << "Invalid data in line: " << line << endl;
                }
            }
        }

        file.close();
    }

    bool exists(int id) {
        ifstream file("clients.txt");
        if (!file) {
            return false;};

        string line;
        while (getline(file, line)) {
            stringstream ss(line);
            string idStr;
            if (getline(ss, idStr, '|')) {
                if (stoi(idStr) == id) {
                    return true;
                }
            }
        }

        return false;
    }
};

class author : public person, public parent {
public:
    void add() override {
        cout << "Enter author ID: ";
        cin >> id;
        cout << "Enter author name: ";
        cin >> name;

        ofstream file("authors.txt", ios::app);

        file << id << "|" << name << "\n";
        file.close();
    }

    void show() override {
        ifstream file("authors.txt");
        if (!file) {
            cout << "list is empty" << endl;
            return;
        }

        string line;
        while (getline(file, line)) {
            stringstream obj(line);
            string idStr, name;
            if (getline(obj, idStr, '|') && getline(obj, name)) {
                try {
                    int id = stoi(idStr);
                    cout << "Author ID: " << id << ", Name: " << name << endl;
                }
                catch (...) {
                    cerr << "Invalid data in line: " << line << endl;
                }
            }
        }

        file.close();
    }
};

class sale{
public:
    sale(int cId = 0, int bId = 0, client* cObject = nullptr, book* bObject = nullptr) {

        if (!cObject->exists(cId)) {
            cout << "Client not found. Please register first.\n";
            return;
        }

        if (!bObject->exists(bId)) {
            cout << "Book not found.\n";
            return;
        }

        ofstream file("sales.txt", ios::app);
        file << cId << "|" << bId << "\n";
        file.close();

        cout << "Sale is successful!\n";
    }

    static void showsale() {
        ifstream file("sales.txt");
        if (!file) {
            cout << "list is empty\n";
            return;
        }

        string line;
        while (getline(file, line)) {
            stringstream ss(line);
            string clientId, bookId;

            if (getline(ss, clientId, '|') &&
                getline(ss, bookId)) {
                cout << "Client ID: " << clientId
                    << ", Book ID: " << bookId << endl;
            }
        }

        file.close();
    }
};


int main() {
    client c;
    book b;
    author a;

    while (true) {
        cout << "\nBook Store Menu:\n";
        cout << "1. Add Book\n";
        cout << "2. Show Books\n";
        cout << "3. Add Client\n";
        cout << "4. Show Clients\n";
        cout << "5. Add Author\n";
        cout << "6. Show Authors\n";
        cout << "7. Buy Book\n";
        cout << "8. Show Sales\n";
        cout << "9. Exit\n";
        cout << "Choose an option: ";

        int choice;
        cin >> choice;

        switch (choice) {
        case 1:
            b.add();
            break;
        case 2:
            b.show();
            break;
        case 3:
            c.add();
            break;
        case 4:
            c.show();
            break;
        case 5:
            a.add();
            break;
        case 6:
            a.show();
            break;
        case 7: {
            int cid, bid;
            cout << "Enter client ID: ";
            cin >> cid;
            cout << "Enter book ID: ";
            cin >> bid;
            sale s(cid, bid, &c, &b);
            break;
        }
        case 8:
            sale::showsale();
            break;
        case 9:
            cout << "Exiting...\n";
            return 0;
        default:
            cout << "Invalid choice. Try again.\n";
        }
    }

    return 0;
}
