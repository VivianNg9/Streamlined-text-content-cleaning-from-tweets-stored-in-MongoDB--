# __<center>Streamlined text content cleaning from tweets stored in MongoDB</center>__

## __<center>Overview</center>__
Using MapReduce paradigms to process and analyze a large movie dataset, revealing trends in movie production by year and company. The workflow incorporates MongoDB for data storage and mrjob for MapReduce implementation, enabling scalable data processing and sorting.


## __<center>Datasets</center>__
- [`movies.zip`](https://github.com/VivianNg9/Data-Mining/blob/main/MapReduce/movies.json): Contains the movie dataset used for analysis. It includes data such as release dates and production companies.
  
![MongoDB](https://github.com/VivianNg9/Streamlined-text-content-cleaning-from-tweets-stored-in-MongoDB--/blob/main/image%20/1.png)


## __<center>Project Environment</center>__
- **MongoDB & Studio 3T:** Used for database creation and dataset importation.
- **pymongo:** To connect to MongoDB and extract document data.
- **mrjob:** Framework for implementing MapReduce programs in Python.


## __<center>Project Workflow</center>__

### Task 1: Movie Count by Production Company and Year  
Calculate the number of movies released by each production company for each year.
  
![Workflow1](https://github.com/VivianNg9/Streamlined-text-content-cleaning-from-tweets-stored-in-MongoDB--/blob/main/image%20/workflow%20task1.png)

**EXTRACTION:** 
Using pymongo to extract <year, company> for each movie.
  1. **Data Retrieval:** Access 'movie -> date' to get release dates and extract the release year.
  2. **Company Identification:** Identify the top three production companies from 'movie -> companies'.
  3. **Data Formatting:** Create `<year, company>` pairs.
  4. **Data Storage:** Store these pairs in [`year_and_company.txt`](https://github.com/VivianNg9/Data-Mining/blob/main/MapReduce/Output%20file%20for%20Task%201/year_and_company.txt).
     
```python
from pymongo import MongoClient                                                                                                   

# Making database connection
client = MongoClient('localhost', 27017)
db = client["assignment1"]
col = db["movies"]
cur = col.find() 

# Write a txt file with <year, company> pairs of all movies 
file = open("year_and_company.txt", "w")
# Loop through each movie document in the cur
for movie in cur: 
    for i in range(min(len(movie['companies']), 3)):
        year_company = movie['date'][-4:] + " , " + movie['companies'][i]['name']+ '\n'
    file.write(year_company)  
file.close()
```

**COUNT:** 
Use mrjob to calculate the frequency of each `<year, company>` pair. Output stored in [`task1_output.txt`](https://github.com/VivianNg9/Data-Mining/blob/main/MapReduce/Output%20file%20for%20Task%201/task1_output.txt)

```python
from mrjob.job import MRJob                                     

class MRWordCount(MRJob):
    def mapper(self, _, line):
        yield line, 1

    def reducer(self, year_company, count):
        yield year_company, sum(count)

if __name__ == "__main__":
    MRWordCount.run()
```


### Task 2: Sorting Algorithms Implementation 
Implement the Merge Sort1 algorithm and the Bucket Sort2 algorithm using two MapReduce programs, to sort results of Task 1.

**MERGE SORT:** 
Sort `<year, company>` pairs in ascending order. 

![Merge sort](https://github.com/VivianNg9/Streamlined-text-content-cleaning-from-tweets-stored-in-MongoDB--/blob/main/image%20/Workflow_Mergesort.png)

Output stored in [`task2_mergesort.txt`](https://github.com/VivianNg9/Data-Mining/blob/main/MapReduce/Output%20file%20for%20Task%202/task2_mergesort_output.txt)

## Example Code
Here is an example of the function used in Task 1:



**BUCKET SORT:** 
Sort `<year, company>` pairs in descending order.
![Bucket sort](https://github.com/VivianNg9/Streamlined-text-content-cleaning-from-tweets-stored-in-MongoDB--/blob/main/image%20/Workflow_Bucketsort.png)

Output stored in [`task2_bucketsort_output.txt`](https://github.com/VivianNg9/Data-Mining/blob/main/MapReduce/Output%20file%20for%20Task%202/task2_bucketsort_output.txt)


