# QAC380
import excel "P:\QAC\qac380\Data and Codebooks 2022\RIPHI\ODH\Deident.RoutineCOVID19Testin_DATA_Jen class.xlsx", sheet("Deident.RoutineCOVID19Testin_DA") firstrow

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
replace insurance=0 if U=="Checked"
replace insurance=1 if T=="Checked"
replace insurance=2 if S=="Checked"
replace insurance=3 if medicaid=="Checked" 
// value labels 
label define insurance 0 "Uninsured" 1 "Private" 2 "Medicare" 3 "Medicaid"
lab val insurance insurance

// insurance status 
histogram insurance, discrete frequency addlabel ytitle(Frequency) xtitle(Insurance Status) xmtick(0 "Uninsured" 1 "Private" 2 "Medicare" 3 "Medicaid", labels valuelabel) title(Insurance Status) legend(order(0 "Uninsured" 1 "Private" 2 "Medicare" 3 "Medicaid") symplacement(nwest) all ring(0))
tabulate insurance
