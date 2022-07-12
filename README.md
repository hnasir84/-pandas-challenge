# -pandas-challenge
Assignment_4

PyCity Schools Analysis
Charter schools outperfomred district schools which could be atributed to charter schools having lesser students comparted to district schools and hence, teacher can alocate more time per student compared to district schools

charter schools recevied lesser budget per student compared to district schools which eleminate the budget alocated as a factor in performance. This analysis is proven on the Scores by School Size data frame

District schools are doing well in reading scores. however, the gap get bigger when it comes to math and overall passing.

more school with maximum number of student set to 2000 could yield good results as shown in the below analysis

# Dependencies
import pandas as pd 

# load files from the source
school_data = "Resources/schools_complete.csv"
students_data = "Resources/students_complete.csv"

# Read both files

school_data_df = pd.read_csv(school_data)


students_data_df = pd.read_csv(students_data)


# merge both files in 1 datasheet based on school name colum
school_data_combined = pd.merge(students_data_df, school_data_df, on =["school_name", "school_name"], how= "left" )
school_data_combined .head()

# check that the dataframe is complete

school_data_combined.count()


# District Summary #



# Calculate Total schools

Total_number_of_schools = len(school_data_combined["school_name"].unique())

# Calculate Total students

Total_number_students = school_data_combined["student_name"].count()

# Calculate Total budget
Total_budget = school_data_df["budget"].sum()

# Calculate the Average Scores for math and reading

Average_math_score = school_data_combined["math_score"].mean()

Average_reading_score = school_data_combined["reading_score"].mean()


# Find % passing math (the percentage of students who passed math)

Math_passing_percentage = ((len(school_data_combined.loc[school_data_combined["math_score"]>=70]))/Total_number_students)*100

# Calculate % passing reading (the percentage of students who passed reading)
Reading_passing_percentage =  ((len(school_data_combined.loc[school_data_combined["reading_score"]>=70]))/Total_number_students)*100

# Calculate % overall passing (the percentage of students who passed math AND reading)
overall_passing_count= len(school_data_combined.loc[(school_data_combined["reading_score"]>=70) & (school_data_combined["math_score"]>=70)])
overall_passing_percentage = (overall_passing_count/Total_number_students)*100

# Create and Display the DataFrame of the district summary 

district_summary ={"Total Schools":Total_number_of_schools,
                   "Total Students":Total_number_students, 
                   "Total Budget":Total_budget,
                   "Average Math Score":Average_math_score,
                   "Average Reading Score":Average_reading_score,
                   "% Passing Math":Math_passing_percentage,
                   "% Passing Reading":Reading_passing_percentage,
                   "% Overall Passing":overall_passing_percentage}
                           

  
    
district_summary_DF = pd.DataFrame([district_summary]) 

# Format the data frame

#district_summary_DF["Total Students"] = district_summary_DF["Total Students"].map("${:,}".format)
#district_summary_DF["Total Budget"] = district_summary_DF["Total Budget"].map("${:,.2f}".format) 

district_summary_DF


# School Summary #

# Determine the School Type

schools = school_data_combined[["school_name", "type"]]


School_Type = schools[schools.duplicated()== False]


School_Types = School_Type.set_index(["school_name"])["type"]

# Calculate the total student count

Students_count = school_data_combined["school_name"].value_counts()

# Calculate the total school budget and per capita spending

per_school_budget = school_data_combined.groupby(["school_name"]).mean()["budget"]


#  per capita spending
per_school_capita = per_school_budget/Students_count

# Calculate the average test scores math and reading

average_school_math_score = school_data_combined.groupby(["school_name"]).mean()["math_score"]

average_school_reading_score = school_data_combined.groupby(["school_name"]).mean()["reading_score"]


# Get the students who passed math and passed reading by creating separate filtered DataFrames.

#  filter df by number of students who passed math
student_passing_math = school_data_combined.loc[school_data_combined["math_score"]>=70]

# group the student who passed math by school and use count to know the number of sutdents passing math per school

student_passing_math_per_school = student_passing_math.groupby(["school_name"]).count()["student_name"]

# divide the number of student passing math per school by the the total number of student per school to obtain the percentage
student_passing_math_per_school_percentage =(student_passing_math_per_school/Students_count )*100


#  filter df by number of students who passed reading
# group the student who passed reading by school and use count to know the number of sutdents passing math per school
# divide the number of student passing math per school by the the total number of student per school to obtain the percentage
student_passing_reading = school_data_combined.loc[school_data_combined["reading_score"]>=70]
student_passing_reading_per_school =student_passing_reading.groupby(["school_name"]).count()["student_name"]
student_passing_reading_per_school_percentage =(student_passing_reading_per_school/Students_count )*100


# Get the the students who passed both reading and math in a separate DataFrame.
#  filter df by number of students who passed both reading and math
# group the student who passed both reading and math by school and use count to know the number of sutdents passing math per school
# divide the number of student passing both reading and math per school by the the total number of student per school to obtain the percentage
student_passing_math_and_reading = (school_data_combined.loc[(school_data_combined["reading_score"]>=70) & (school_data_combined["math_score"]>=70)])

student_passing_reading_and_math_per_school = student_passing_math_and_reading.groupby(["school_name"]).count()["student_name"]

student_passing_reading_and_math_per_school_percentage = (student_passing_reading_and_math_per_school/Students_count)*100


# Create and Display the per school DataFrame
per_school_summary = pd.DataFrame({ 
     "School Type" :School_Types,
    "Total Students":Students_count, 
    "Total School Budget":per_school_budget,
    "Per Student Budget":per_school_capita,
    "Average Math Score":average_school_math_score,
   "Average Reading Score":average_school_reading_score,
   "% Passing Math":student_passing_math_per_school_percentage,
   "% Passing Reading":student_passing_reading_per_school_percentage,
   "% Overall Passing":student_passing_reading_and_math_per_school_percentage
    
  })

# Format the data frame

per_school_summary ["Total School Budget"] = per_school_summary ["Total School Budget"].map("${:,.2f}".format)
per_school_summary ["Per Student Budget"] = per_school_summary ["Per Student Budget"].map("${:,.2f}".format)

per_school_summary



Top Performing Schools (By % Overall Passing)

# Sort and show top five schools

Top_five_schools = per_school_summary.sort_values("% Overall Passing", ascending =False)



Top_five_schools.head()



# Bottom Performing Schools (By % Overall Passing) #



# Sort and show bottom five schools

Bottom_five_schools = per_school_summary.sort_values("% Overall Passing")

Bottom_five_schools.head()


# Math & Reading Scores by Grade #


# Filter the 9th students across all schools
nineth_grade_students = school_data_combined.loc[school_data_combined["grade"] == "9th"]

#average math score for 9th grade per school
average_math_score_nineth_grade_student = nineth_grade_students.groupby(["school_name"]).mean()["math_score"]
#average reading score for 9th grade per school
average_reading_score_nineth_grade_student = nineth_grade_students.groupby(["school_name"]).mean()["reading_score"]



# Filter the 10th students across all schools
Tenth_grade_students = school_data_combined.loc[school_data_combined["grade"] == "10th"]

# average math score for 10th grade per school
average_math_score_tenth_grade_student = Tenth_grade_students.groupby(["school_name"]).mean()["math_score"]
# average reading score for 10th grade per school
average_reading_score_tenth_grade_student = Tenth_grade_students.groupby(["school_name"]).mean()["reading_score"]


# Filter the 11th students across all schools
Eleventh_grade_students = school_data_combined.loc[school_data_combined["grade"] == "11th"]

#average math score for 11th grade per school
average_math_score_eleventh_grade_student = Eleventh_grade_students.groupby(["school_name"]).mean()["math_score"]
#average reading score for 11th grade per school
average_reading_score_eleventh_grade_student = Eleventh_grade_students.groupby(["school_name"]).mean()["reading_score"]




# Filter the 12th students across all schools
Twelfth_grade_students = school_data_combined.loc[school_data_combined["grade"] == "12th"]

# average math score for 12th grade per school
average_math_score_twelfth_grade_student = Twelfth_grade_students.groupby(["school_name"]).mean()["math_score"]
# average reading score for 12th grade per school
average_reading_score_twelfth_grade_student = Twelfth_grade_students.groupby(["school_name"]).mean()["reading_score"] 


# Math Scores by Grade #

# Create and Display the DataFrame containing average math score per grade

average_Math_scores_by_grade = pd.DataFrame({"9th":average_math_score_nineth_grade_student,
                                            "10th":average_math_score_tenth_grade_student,
                                            "11th" :average_math_score_eleventh_grade_student,
                                            "12th":average_math_score_twelfth_grade_student})

average_Math_scores_by_grade


# Reading Score by Grade #

# Create and Display the DataFrame containing average math score per grade

average_Reading_scores_by_grade = pd.DataFrame({"9th":average_reading_score_nineth_grade_student,
                                            "10th":average_reading_score_tenth_grade_student,
                                            "11th" :average_reading_score_eleventh_grade_student,
                                            "12th":average_reading_score_twelfth_grade_student})

average_Reading_scores_by_grade


# Scores by School Spending #

# Create a table that breaks down school performance based on average spending ranges (per student). Use your judgment to create four bins with reasonable cutoff values to group school spending. Include the following metrics in the table:

# Average math score
# Average reading score
# % passing math (the percentage of students who passed math)
# % passing reading (the percentage of students who passed reading)
# % overall passing (the percentage of students who passed math AND reading)

# we have per_school_summary data frame created.
# Establish Bins
# find the max and minimum in per student Budget to esablish the bins (max = 655 & min 578)

school_spending = per_school_summary

bins = [555,580,605,630,655]

spending_range = [ "$ 555-579", "$ 580-604", "$ 605-629", "$ 630-655"]

# add a column with the bins to the data frame
school_spending["Spending Ranges (Per Student)"] = pd.cut(school_spending["Per Student Budget"], bins, labels =spending_range, include_lowest=True)


# school_spending #

# create data frame to displae the spending ranges in reltion to performance
spending_range = school_spending[["Spending Ranges (Per Student)", "Average Math Score", "Average Reading Score", "% Passing Math",
                                 "% Passing Reading", "% Overall Passing"]]
spending_range_grouped = spending_range.groupby(["Spending Ranges (Per Student)"]).mean()

#Format the data fram to 2 decimals
#spending_range_grouped ["Average Math Score"] = spending_range_grouped ["Average Math Score"] .map("${:,.2f}".format)
#spending_range_grouped ["Average Reading Score"] = spending_range_grouped ["Average Reading Score"] .map("${:,.2f}".format)
#spending_range_grouped ["% Passing Math"] = spending_range_grouped ["% Passing Math"] .map("${:,.2f}".format)
#spending_range_grouped ["% Passing Reading"] = spending_range_grouped ["% Passing Reading"] .map("${:,.2f}".format)
#spending_range_grouped ["% Overall Passing"] = spending_range_grouped ["% Overall Passing"] .map("${:,.2f}".format)

spending_range_grouped


# Scores by School Size #

# Create a table that breaks down school performance based on school size (small, medium, or large).
# Establish the bins.
size_bins = [0, 1000, 2000, 5000]
group_names = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

school_size =per_school_summary

# add a new column called school size uitlzing bins

school_size["School Size"] = pd.cut(school_size["Total Students"], size_bins, labels =group_names, include_lowest=True)


# school_size #

# calcualte the average based on school size
#create a new data frame
spending_based_on_size = school_size[["School Size", "Average Math Score", "Average Reading Score", "% Passing Math",
                                 "% Passing Reading", "% Overall Passing"]]


spending_based_on_size_DF = spending_based_on_size.groupby(["School Size"]).mean()

spending_based_on_size_DF


# Scores by School Type # 


# Create a table that breaks down school performance based on type of school (district or charter).
# create a data frame
school_Type_DF = school_spending[["School Type", "Average Math Score", "Average Reading Score", "% Passing Math",
                                 "% Passing Reading", "% Overall Passing"]]
score_by_school_type = school_Type_DF.groupby(["School Type"]).mean()
score_by_school_type

# The end_Hassan