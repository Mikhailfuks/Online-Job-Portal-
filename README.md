#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <fstream>
#include <sstream>

using namespace std;

// Job Posting Class
class JobPosting {
public:
    int jobId;
    string title;
    string company;
    string location;
    string description;
    string skills;
    bool isFilled;

    JobPosting(int jobId, string title, string company, string location, string description, string skills) {
        this->jobId = jobId;
        this->title = title;
        this->company = company;
        this->location = location;
        this->description = description;
        this->skills = skills;
        this->isFilled = false;
    }
};

// User Class (for Job Seekers and Recruiters)
class User {
public:
    int userId;
    string name;
    string email;
    string password;
    string userType; // "jobseeker" or "recruiter"

    User(int userId, string name, string email, string password, string userType) {
        this->userId = userId;
        this->name = name;
        this->email = email;
        this->password = password;
        this->userType = userType;
    }
};

// Online Job Portal Class
class OnlineJobPortal {
private:
    vector<JobPosting> jobPostings;
    map<int, User*> users;
    int nextJobId = 1;
    int nextUserId = 1;

public:
    // Load job postings from a file
    void loadJobPostings(const string& filename) {
        ifstream file(filename);
        if (file.is_open()) {
            int jobId;
            string title, company, location, description, skills;
            while (getline(file, title)) {
                getline(file, company);
                getline(file, location);
                getline(file, description);
                getline(file, skills);
                jobId = nextJobId++;
                jobPostings.push_back(JobPosting(jobId, title, company, location, description, skills));
            }
            file.close();
        } else {
            cout << "Error opening job postings file!" << endl;
        }
    }

    // Save job postings to a file
    void saveJobPostings(const string& filename) {
        ofstream file(filename);
        if (file.is_open()) {
            for (JobPosting& job : jobPostings) {
                file << job.title << endl;
                file << job.company << endl;
                file << job.location << endl;
                file << job.description << endl;
                file << job.skills << endl;
            }
            file.close();

        } else {
            cout << "Error saving job postings file!" << endl;
        }
    }

    // Add a new job posting
    void addJobPosting(const string& title, const string& company, const string& location, const string& description, const string& skills) {
        jobPostings.push_back(JobPosting(nextJobId++, title, company, location, description, skills));
        cout << "Job posting added successfully!" << endl;
    }

    // Register a new user
    void registerUser(const string& name, const string& email, const string& password, const string& userType) {
        users[nextUserId++] = new User(nextUserId - 1, name, email, password, userType);
        cout << "User registered successfully!" << endl;
    }

    // Display available job postings
    void displayJobPostings() {
        cout << "Available Job Postings:" << endl;
        for (int i = 0; i < jobPostings.size(); i++) {
            if (!jobPostings[i].isFilled) {
                cout << i + 1 << ". " << jobPostings[i].title << " (" << jobPostings[i].company << ")" << endl;
                cout << "   Location: " << jobPostings[i].location << endl;
                cout << "   Description: " << jobPostings[i].description << endl;
                cout << "   Skills: " << jobPostings[i].skills << endl;
                cout << "------------------------------" << endl;
            }
        }
    }

    // Search for job postings by keywords
    void searchJobPostings(const string& keywords) {
        cout << "Search Results:" << endl;
        bool found = false;
        for (const JobPosting& job : jobPostings) {
            if (!job.isFilled && (job.title.find(keywords) != string::npos || job.description.find(keywords) != string::npos || job.skills.find(keywords) != string::npos)) {
                cout << "  " << job.title << " (" << job.company << ")" << endl;
                cout << "    Location: " << job.location << endl;
                cout << "    Description: " << job.description << endl;
                cout << "    Skills: " << job.skills << endl;
                cout << "  ------------------------------" << endl;
                found = true;
            }
        }
        if (!found) {

            cout << "  No job postings found matching your search criteria." << endl;
        }
    }

    // Apply for a job
    void applyForJob(int jobId, int userId) {
        User* user = users[userId];
        if (user != nullptr && user->userType == "jobseeker") {
            for (JobPosting& job : jobPostings) {
                if (job.jobId == jobId && !job.isFilled) {
                    cout << "Application for job '" << job.title << "' submitted." << endl;
                    return;
                }
            }
            cout << "Invalid job ID or job is already filled." << endl;
        } else {
            cout << "Invalid user ID or user is not a job seeker." << endl;
        }
    }

    // Fill a job posting
    void fillJobPosting(int jobId, int userId) {
        User* user = users[userId];
        if (user != nullptr && user->userType == "recruiter") {
            for (JobPosting& job : jobPostings) {
                if (job.jobId == jobId && !job.isFilled) {
                    job.isFilled = true;
                    cout << "Job posting '" << job.title << "' has been filled." << endl;
                    return;
                }
            }
            cout << "Invalid job ID or job is already filled." << endl;
        } else {
            cout << "Invalid user ID or user is not a recruiter." << endl;
        }
    }

    // Main job portal menu
    void startPortal() {
        cout << "Welcome to the
