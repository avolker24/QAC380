# QAC380
// Hudson Dore & Annie Volker
// COVID-19 Data
clear
// Do-File Stata Doc
// Import data
import excel "P:\QAC\qac380\Data and Codebooks 2022\RIPHI\ODH\RADx COVID Data.xlsx", sheet("Sheet1") firstrow

gen gender_identity=.
replace gender_identity = 1 if Currentgenderidentity=="Woman" 
replace gender_identity = 0 if Currentgenderidentity=="Man"
replace gender_identity = 2 if gender_identity==.
replace gender_identity = . if Currentgenderidentity=="Choose not to disclose"

// If gender_identity = 1 then they are a Woman
// If gender_identity = 0 then they are a Man
// If gender_identity = 2 then they are non-binary/genderqueer/other
// If gender_identity = . or missing then they choose not to disclose and 
// therefore should not be in the dataset

gen Vaccination_Status=1 if BE=="Checked"
replace Vaccination_Status = 0 if BE=="Unchecked"

// If Vaccination_Status = 1, then they are vaccinated. 
// If Vaccination_Status = 0, then they are unvaccinated

gen COVID_Pos=0 if RapidTestResults=="Negative"
replace COVID_Pos=0 if PCRTestResults=="Negative"
replace COVID_Pos=1 if RapidTestResults=="Positive" 
replace COVID_Pos=1 if PCRTestResults=="Positive" 

// if COVID_Pos = 1 when patient is positive for covid 
// renaming for Annie's
rename Currentgenderidentity gender
rename Sexassignedatbirth sex
rename RacechoiceWhite white
rename RacechoiceAfricanAmerican africanamerican
rename RacechoiceAsian asian
rename RacechoiceAmericanIndian americanindian
rename RacechoiceOtherNotlisted otherrace
rename Raceothernotlistedpleasesp specifyrace

// race variabels to categorical 
// white
gen white_cat=.
replace white_cat=1 if white=="Checked"
replace white_cat=0 if white=="Unchecked"

// black
gen aa_cat=.
replace aa_cat=1 if africanamerican=="Checked"
replace aa_cat=0 if africanamerican=="Unchecked"

// asian
gen asian_cat=.
replace asian_cat=1 if asian=="Checked"
replace asian_cat=0 if asian=="Unchecked"


// american indian
gen ai_cat=.
replace ai_cat=1 if americanindian=="Checked"
replace ai_cat=0 if americanindian=="Unchecked"

// other race
gen other_cat=.
replace other_cat=1 if otherrace=="Checked"
replace other_cat=0 if otherrace=="Unchecked"

// hispanic/latinx
gen hisp_latinx=.
replace hisp_latinx=1 if Ethnicity=="Hispanic / Latinx"
replace hisp_latinx=0 if Ethnicity== "Not Hispanic/Latinx"

// insurance status 
rename PrimaryInsurancePayerchoice medicaid 
gen insurance=.
replace insurance=0 if V=="Checked"
replace insurance=1 if U=="Checked"
replace insurance=2 if T=="Checked"
replace insurance=3 if medicaid=="Checked" 
// value labels 
label define insurance 0 "Uninsured" 1 "Private" 2 "Medicare" 3 "Medicaid"
lab val insurance insurance

//testing compliance variable 
rename DidthepatientconsenttoCOVID test_compliance
gen testing=.
replace testing=0 if test_compliance=="No"
replace testing=1 if test_compliance=="Yes"

// insurance status 
histogram insurance, discrete frequency addlabel ytitle(Frequency) xtitle(Insurance Status) xmtick(0 "Uninsured" 1 "Private" 2 "Medicare" 3 "Medicaid", labels valuelabel) title(Insurance Status) legend(order(0 "Uninsured" 1 "Private" 2 "Medicare" 3 "Medicaid") symplacement(nwest) all ring(0))
tabulate insurance
by COVID_Pos, sort : tab1 insurance



//Gender Identity Graph
dotplot gender_identity

//Race Graph
graph bar (sum) white_cat (sum) aa_cat (sum) asian_cat (sum) ai_cat (sum) other_cat (sum) hisp_latinx

graph pie, over(COVID_Pos)

gen race_1=.
//White = 0
replace race_1 = 0 if white=="Checked"
//Black = 1
replace race_1 = 1 if africanamerican=="Checked"
//Asian = 2
replace race_1 = 2 if asian=="Checked"
//American Indian = 3
replace race_1 = 3 if americanindian=="Checked"
//Other race = 4
replace race_1 = 4 if otherrace=="Checked"
// Hispanic = 5
/*
replace race_1 = 5 if Ethnicity=="Checked"
*/
label define race_1 0 "White" 1 "Black" 2 "Asian" 3 "American Indian" 4 "Other Race"
lab val race_1 race_1

label define Vaccination_Status 0 "Unvaccinated" 1 "Vaccinated"
lab val Vaccination_Status Vaccination_Status

tab Vaccination_Status race_1, exact


gen race_combined =.
replace race_combined=0 if  white=="Checked"
replace race_combined = 1 if africanamerican=="Checked"
replace race_combined = 2 if asian=="Checked"
replace race_combined = 2 if americanindian=="Checked"
replace race_combined = 2 if otherrace=="Checked"

label define race_combined 0 "White" 1 "Black" 2 "Other Race"
lab val race_combined race_combined
/*
Generate insurance_new=.
replace insurance_new=1 if insurance!=0
Replace insurance_new=0 if insurance==0

logit Vaccination_Status race_combined
logit Vaccination_Status white_cat aa_cat
logit, or

graph bar, over(Vaccination_Status) over(gender_identity) stack asyvars percentage

logit Vaccination_Status testing

/*Combine races → Black, White, and Other*/
/*Bivariate logistic regression → odds ratio “unadjusted” */
/*Don’t need Chi Squared test*/
/*Multivariate later*/

/*choose one as the reference
Logistical regression with one predictor
*/
// Model 1 Logistic Regression
logit Vaccination_Status i.race_combined i.gender_identity i.insurance hisp_latinx testing
logit, or

//Model 2 Logistic Regression 
logit testing i.race_combined i.gender_identity i.insurance hisp_latinx Vaccination_Status
logit, or 


