# Stata-code-for-BMC-Medicine-2019
 Physical activity, multimorbidity, and life expectancy: a UK Biobank longitudinal study

** BASELINE DATA **
* Prevalence
tabout hyp1065-cancer using Septem\prevalence_.xls, oneway ptotal(single) cells(freq col) layout(% No. ) replace
tab multimorbid 
ci  proportions multimorbid, level(95)

* Follow-up time
centile years, centile(50,25,75)
centile years if multimorbid==0, centile(50,25,75)
centile years if multimorbid==1, centile(50,25,75)

* Those that have died
tab dead
tab dead  multimorbid, col chi2

* Baseline table 
sum age, d
centile age, centile(50,25,75)
centile age if multimorbid==0, centile(50,25,75)
centile age if multimorbid==1, centile(50,25,75)
ranksum age, by(multimorbid)
 
tabout sex catage newethnic cat_townsend_depriv smoke bmigrp meat2more redmeat oilyfish nonoilyfish ///
guideline_fruitveg excess_alcohol empcat edu_level cat_sedtime multimorbid using ///
Septem/baseline_multimorbid.xls, cells(freq col) stats(chi2) layout(No. % No. % No %) replace 

tab typePA_pa_status , m
tab typePA_pa_status multimorbid, col chi2 m
sum typePA_METtot, d
centile typePA_METtot, centile(50,25,75)
centile typePA_METtot if multimorbid==0, centile(50,25,75)
centile typePA_METtot if multimorbid==1, centile(50,25,75)
ranksum typePA_METtot, by(multimorbid)

* NB: IPAQ = TPA 
tab IPAQ_pa_status 
tab IPAQ_pa_status multimorbid, col chi2 m
sum IPAQ_METtotal, d
centile IPAQ_METtotal, centile(50,25,75)
centile IPAQ_METtotal if multimorbid==0, centile(50,25,75)
centile IPAQ_METtotal if multimorbid==1, centile(50,25,75)
ranksum IPAQ_METtotal, by(multimorbid)
spearman IPAQ_METtotal typePA_METtot, stats(rho p)

********************************************************************************
** HAZARD RATIOS **
* SELF-REPORTED PHYSICAL ACTIVITY *
stset stime, f(dead==1)

* Multimorbidity *
stcox i.multimorbid
stcox i.multimorbid age sex
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==1 
stcox i.multimorbid i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime

* LTPA *
* With multimorbidity
stcox i.typePA_pa_status if multimorbid==1 
estimate store model1
stcox i.typePA_pa_status age i.sex if multimorbid==1 
estimate store model2
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==1 
estimate store model3
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==1
estimate store model4

coefplot model1 model2 model3 model4, keep(*.typePA_pa_status) yline(1) eform  vertical levels(95) yscale(log range(0.4 1))   offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")  ///
coeflabels(1.typePA_pa_status="M" 2.typePA_pa_status="H")  ///
aseq  eqstrict graphregion(color(white))  legen(off)   ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("With multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}" ) 
     
* Without multimorbidity
stcox i.typePA_pa_status if multimorbid==0
estimate store model10
stcox i.typePA_pa_status age i.sex if multimorbid==0
estimate store model20
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==0
estimate store model30
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==0
estimate store model40

coefplot model10 model20 model30 model40, keep(*.typePA_pa_status) yline(1) eform  vertical levels(95)   yscale(log range(0.4 1))  offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")   ///
coeflabels(1.typePA_pa_status="M" 2.typePA_pa_status="H")  ///
aseq  eqstrict graphregion(color(white))  legen(off)    ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("Without multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}"  "{bf:Model 5}")
     	
* IPAQ *
* With multimorbidity
stcox i.IPAQ_pa_status if multimorbid==1 
estimate store model11
stcox i.IPAQ_pa_status age i.sex if multimorbid==1 
estimate store model21
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==1 
estimate store model31
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==1
estimate store model41

coefplot model11 model21 model31 model41 , keep(*.IPAQ_pa_status) yline(1) eform  vertical levels(95) yscale(log range(0.4 1))    offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")   ///
coeflabels(0.IPAQ_pa_status ="L" 1.IPAQ_pa_status="M" 2.IPAQ_pa_status="H")  ///
aseq  eqstrict graphregion(color(white))  legen(off)    ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("With multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}"  "{bf:Model 5}") 
      
* Without multimorbidity
stcox i.IPAQ_pa_status if multimorbid==0
estimate store model101
stcox i.IPAQ_pa_status age i.sex if multimorbid==0
estimate store model201
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==0
estimate store model301
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==0
estimate store model401	

coefplot model101 model201 model301 model401 , keep(*.IPAQ_pa_status) yline(1) eform  vertical levels(95)   yscale(log range(0.4 1))  offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")   ///
coeflabels(1.IPAQ_pa_status="M" 2.IPAQ_pa_status="H")  ///
aseq  eqstrict graphregion(color(white))  legen(off)    ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("Without multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}"  "{bf:Model 5}") 
 
graph combine mm_ltpa.gph nomm_ltpa.gph, row(1) iscale(0.8) title("Leisure-time physical activity") saving(ltpa, replace) 
graph combine mm_ipaq.gph nomm_ipaq.gph, row(1) iscale(0.8) title("Total physical activity ") saving(ipaq, replace) 
graph combine ltpa.gph ipaq.gph, col(1) iscale(0.8) 

* CONTINOUS PA *
* LTPA * 
sum typePA_METtot
gen typePA_METtot_plus1 = (typePA_METtot) + 1
sum typePA_METtot_plus1
gen log10_typePA_METtot= log10(typePA_METtot_plus1)
gen log10percent = log10(1.1)
gen log10percent_typePA_METtot= log10_typePA_METtot/log10percent
* With multimorbidity
stcox log10percent_typePA_METtot i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==1
* Without multimorbidity
stcox log10percent_typePA_METtot i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==0

* IPAQ *
sum IPAQ_METtotal
gen IPAQ_METtotal_plus1 = (IPAQ_METtotal) + 1
sum IPAQ_METtotal_plus1
gen log10_IPAQ_METtotal= log10(IPAQ_METtotal_plus1)
gen log10percent_IPAQ_METtotal= log10_IPAQ_METtotal/log10percent
* With multimorbidity
stcox log10percent_IPAQ_METtotal i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==1
* Without multimorbidity
stcox log10percent_IPAQ_METtotal i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==0


** SENSITIVITY ANALYSES **
* TERTILES *
tab cat_typemet 
tab cat_ipaq 
tab multimorbid cat_typemet , row chi2
tab multimorbid cat_ipaqmet , row chi2
 
* LTPA * 
* With multimorbidity
stcox i.cat_typemet if multimorbid==1 
estimate store model1t
stcox i.cat_typemet age i.sex if multimorbid==1 
estimate store model2t
stcox i.cat_typemet i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==1 
estimate store model3t
stcox i.cat_typemet i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==1
estimate store model4t

coefplot model1t model2t model3t model4t, keep(*.cat_typemet) yline(1) eform  vertical levels(95) yscale(log range(0.4 1))   offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")  ///
coeflabels(2.cat_typemet="M" 3.cat_typemet="U")  ///
aseq  eqstrict graphregion(color(white))  legen(off)   ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("With multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}"  "{bf:Model 5}") saving(ter_mm_ltpa, replace)
     
* Without multimorbidity
stcox i.cat_typemet if multimorbid==0
estimate store model10t
stcox i.cat_typemet age i.sex if multimorbid==0
estimate store model20t
stcox i.cat_typemet i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==0
estimate store model30t
stcox i.cat_typemet i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==0
estimate store model40t
		
coefplot model10t model20t model30t model40t , keep(*.cat_typemet) yline(1) eform  vertical levels(95)   yscale(log range(0.4 1))  offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")   ///
coeflabels(2.cat_typemet="M" 3.cat_typemet="U")  ///
aseq  eqstrict graphregion(color(white))  legen(off)    ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("Without multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}"  "{bf:Model 5}") saving(ter_nomm_ltpa, replace)
     	 

* IPAQ *
* With multimorbidity
stcox i.cat_ipaqmet if multimorbid==1 
estimate store m1
stcox i.cat_ipaqmet age i.sex if multimorbid==1 
estimate store m2
stcox i.cat_ipaqmet i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==1 
estimate store m3
stcox i.cat_ipaqmet i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==1
estimate store m4
		
coefplot m1 m2 m3 m4, keep(*.cat_ipaqmet) yline(1) eform  vertical levels(95)  yscale(log range(0.4 1))  offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")   ///
coeflabels(2.cat_ipaqmet="M" 3.cat_ipaqmet="U")  ///
aseq  eqstrict graphregion(color(white))  legen(off)    ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("With multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}"  "{bf:Model 5}") saving(ter_mm_ipaq, replace)
     
* Without multimorbidity
stcox i.cat_ipaqmet if multimorbid==0
estimate store model101i
stcox i.cat_ipaqmet age i.sex if multimorbid==0
estimate store model201i
stcox i.cat_ipaqmet i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==0
estimate store model301i
stcox i.cat_ipaqmet i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==0
estimate store model401i	

coefplot model101i model201i model301i model401i , keep(*.cat_ipaqmet) yline(1) eform  vertical levels(95)  yscale(log range(0.4 1))  offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")   ///
coeflabels(2.cat_ipaqmet="M" 3.cat_ipaqmet="U")  ///
aseq  eqstrict graphregion(color(white))  legen(off)    ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("Without multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}"  "{bf:Model 5}") saving(ter_nomm_ipaq, replace)
       	
graph combine ter_mm_ltpa.gph ter_nomm_ltpa.gph, row(1) iscale(0.8) title("Leisure-time physical activity")  saving(ter_ltpa, replace) 
graph combine ter_mm_ipaq.gph ter_nomm_ipaq.gph, row(1) iscale(0.8) title("Total physical activity")  saving(ter_ipaq, replace) 
graph combine ter_ltpa.gph ter_ipaq.gph , col(1) iscale(0.8)


********************************************************************************

** OBJECTIVE PHYSICAL ACTIVITY **
tab objective_data 

* cut points
* >250 mg
gen ac_250mg =  n_90133_0_0 if objective_data==1 
sum ac_250mg
gen  ac250mg_weartime = ac_250mg*168
gen  ac250mg_weartime_subtract = 168 - ac250mg_weartime
gen ac250mg_weartime_subtract_60 =  ac250mg_weartime_subtract*60
gen ac250_mins = ac250mg_weartime_subtract_60 /  7
bys cat_objective : sum ac250_mins, d

sum ac250_mins if objective_data==1 & cat_objective==1, d
sum ac250_mins if objective_data==1 & cat_objective==2, d
sum ac250_mins if objective_data==1 & cat_objective==3, d
pwcorr ac250_mins overalaccel, sig

sum currentobject_age
centile currentobject_age if objective_data==1, centile(50,25,75)
centile age if objective_data==0, centile(50,25,75)
tab current_catage if objective_data==1
tab catage if objective_data==0

* Baseline table
tabout sex  newethnic cat_townsend_depriv smoke bmigrp meat2more redmeat oilyfish nonoilyfish guideline_fruitveg excess_alcohol empcat edu_level cat_sedtime ///
objective_data using Septem/baseline_objective_data.xls, cells(freq col) stats(chi2) layout(No. % No. % No %) replace 

tab typePA_pa_status  if objective_data==1, m
tab typePA_pa_status  if objective_data==0, m
centile typePA_METtot if objective_data==1, centile(50,25,75)
centile typePA_METtot if objective_data==0, centile(50,25,75)

tab IPAQ_pa_status    if objective_data==1, m
tab IPAQ_pa_status    if objective_data==0, m
centile IPAQ_METtotal if objective_data==1, centile(50,25,75)
centile IPAQ_METtotal if objective_data==0, centile(50,25,75)


* Baseline table if have objective data
tabout sex  newethnic cat_townsend_depriv edu_level empcat  bmigrp smoke excess_alcohol guideline_fruitveg oilyfish nonoilyfish meat2more redmeat cat_sedtime ///
multimorbid  if objective_data==1 using Septem/baseline_multimorbid_objective_data.xls, cells(freq col) stats(chi2) layout(No. % No. % No %) replace 

centile typePA_METtot if  multimorbid==1 & objective_data==1, centile(50,25,75)
centile typePA_METtot if  multimorbid==0 & objective_data==1, centile(50,25,75)
tab typePA_pa_status  multimorbid if objective_data==1, m col

centile IPAQ_METtotal if  multimorbid==1 & objective_data==1, centile(50,25,75)
centile IPAQ_METtotal if  multimorbid==0 & objective_data==1, centile(50,25,75)
tab IPAQ_pa_status  multimorbid if objective_data==1, m col

centile objective if multimorbid==1 & objective_data==1, centile(50,25,75)
centile objective if multimorbid==0 & objective_data==1, centile(50,25,75)
tab  cat_objective multimorbid if objective_data==1 , col chi2 m

centile currentobject_age if multimorbid==1 & objective_data==1, centile(50,25,75)
centile currentobject_age if multimorbid==0 & objective_data==1, centile(50,25,75)

tab current_catage if multimorbid==1 & objective_data==1
tab current_catage if multimorbid==0 & objective_data==1

* correlation overall, and by multimorbidity
spearman typePA_METtot objective if objective_data==1, stats(rho p)
spearman IPAQ_METtotal objective if objective_data==1, stats(rho p)
spearman typePA_METtot objective if objective_data==1 & multimorbid==1, stats(rho p)
spearman typePA_METtot objective if objective_data==1 & multimorbid==0, stats(rho p)
spearman IPAQ_METtotal objective if objective_data==1 & multimorbid==1, stats(rho p)
spearman IPAQ_METtotal objective if objective_data==1 & multimorbid==0, stats(rho p)

* overall acceleration  	   
sum objective, d
centile objective if objective_data==1, centile(50,25,75)
centile objective if multimorbid==1 & objective_data==1, centile(50,25,75)
centile objective if multimorbid==0 & objective_data==1, centile(50,25,75)

tab cat_objective 
tab multimorbid cat_objective , row chi2

** HAZARD RATIOS **
stset stime, f(dead==1)

* With multimorbidity
stcox i.cat_objective if multimorbid==1 & objective_data==1 
estimate store model1ob
stcox i.cat_objective currentobject_age i.sex if multimorbid==1  & objective_data==1
estimate store model2ob
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==1 & objective_data==1
estimate store model3ob
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==1 & objective_data==1
estimate store model4ob

coefplot model1ob model2ob model3ob model4ob, keep(*.cat_objective) yscale(log range(0.1 1)) yline(1) eform  vertical levels(95) offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")  ///
coeflabels(2.cat_objective="M" 3.cat_objective="U")  ///
aseq  eqstrict graphregion(color(white))  legen(off)   ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("With multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}") saving(mm_obj, replace)
     
* Without multimorbidity
stcox i.cat_objective if multimorbid==0 & objective_data==1
estimate store model10ob
stcox i.cat_objective currentobject_age i.sex if multimorbid==0 & objective_data==1
estimate store model20ob
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level if multimorbid==0 & objective_data==1
estimate store model30ob
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==0 & objective_data==1
estimate store model40ob

coefplot model10ob model20ob model30ob model40ob , keep(*.cat_objective)  yline(1) yscale(log range(0.1 1)) eform   vertical levels(95)   offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")  ///
coeflabels(2.cat_objective="M" 3.cat_objective="U")  ///
aseq  eqstrict graphregion(color(white))  legen(off)   ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("Without multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}"  "{bf:Model 4}" )  saving(nomm_obj, replace)
     	
graph combine mm_obj.gph nomm_obj.gph, row(1) iscale(0.8) title("Objective physical activity") xsize(7) ysize(3)
graph combine obj_one.gph two.gph, col(1) iscale(0.8) ycommon
	
	
* SENSITIVITY ANALYSES *
* Continuously measured *
sum objective
gen obj_plus1 = (objective) + 1  if objective_data==1
sum obj_plus1
gen log10_obj_plus1= log10(obj_plus1)
gen log10percent = log10(1.1)
gen log10_obj_tot= log10_obj_plus1/log10percent
* With multimorbidity
stcox log10_obj_tot i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==1
* Without multimorbidity
stcox log10_obj_tot i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==0

* Time 0 at objective measure *
stset obj_stime if objective_data==1, f(dead==1)
gen objyears = obj_stime/365.25
centile objyears, centile(50,25,75)
tab dead multimorbid if objective_data==1

* With multimorbidity
stcox i.cat_objective if multimorbid==1 & objective_data==1 
estimate store model1_t0obj
stcox i.cat_objective currentobject_age i.sex if multimorbid==1  & objective_data==1
estimate store model2_t0obj
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv  if multimorbid==1 & objective_data==1
estimate store model3_t0obj
*estat phtest
coefplot model1_t0obj model2_t0obj model3_t0obj , keep(*.cat_objective) yscale(log range(0.1 1)) yline(1) eform  vertical levels(95) offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")  ///
coeflabels(2.cat_objective="M" 3.cat_objective="U")  ///
aseq  eqstrict graphregion(color(white))  legen(off)   ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("With multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}" ) saving(t0_mm_obj, replace)
     
 * Without multimorbidity 
stcox i.cat_objective if multimorbid==0 & objective_data==1 
estimate store model10_t0obj
stcox i.cat_objective currentobject_age i.sex if multimorbid==0  & objective_data==1
estimate store model20_t0obj
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv  if multimorbid==0 & objective_data==1
estimate store model30_t0obj
*estat phtest

coefplot model10_t0obj  model20_t0obj model30_t0obj   , keep(*.cat_objective)  yline(1) yscale(log range(0.1 1)) eform   vertical levels(95)   offset(0.3) ///
ytitle("Hazard Ratio (95% CI)")  ///
coeflabels(2.cat_objective="M" 3.cat_objective="U")  ///
aseq  eqstrict graphregion(color(white))  legen(off)   ///
ciopts(recast(rcap) lwidth(*2.5) lcolor(*.8) )  ///
msymbol(S) mlcolor(black)  mlstyle(forground) msize(med )  title("Without multimorbidity")  ///
eqlabels("{bf:Model 1}" "{bf:Model 2}"  "{bf:Model 3}" )  saving(t0_nomm_obj, replace)
     
graph combine t0_mm_obj.gph t0_nomm_obj.gph, row(1) iscale(0.8) title("Objective physical activity") xsize(7) ysize(3)
graph combine obj_one.gph two.gph, col(1) iscale(0.8) ycommon

	
* Sensitivity analysis - excluding the 1 year of follow up *
gen obj_stime_exconeyear=obj_stime
replace obj_stime_exconeyear=. if obj_years<=1 & objective_data==1 
gen obj_stime_exconeyear_years = obj_stime_exconeyear/365.25

stset obj_stime_exconeyear_years if objective_data==1, f(dead==1)
* With multimorbidity
stcox i.cat_objective if multimorbid==1 & objective_data==1 
stcox i.cat_objective currentobject_age i.sex if multimorbid==1  & objective_data==1
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv  if multimorbid==1 & objective_data==1
estat phtest

* Without multimorbidity 
stcox i.cat_objective if multimorbid==0 & objective_data==1 
stcox i.cat_objective currentobject_age i.sex if multimorbid==0  & objective_data==1
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv  if multimorbid==0 & objective_data==1
estat phtest
* meets PH test

* Tertiles of 250 *
_pctile ac250_mins if objective_data==1, nq(3)
 return list
 *scalars:
                * r(r1) =  7.199968814849854
                * r(r2) =  15.83993053436279

xtile cat_ac250_mins = ac250_mins if objective_data==1, nq(3) 
label define carr 1 "Low" 2 "Middle" 3 "Upper"
label values cat_ac250_mins carr
bys cat_ac250_mins : sum ac250_mins, d

sum ac250_mins if objective_data==1 & cat_ac250_mins==1, d
sum ac250_mins if objective_data==1 & cat_ac250_mins==2, d
sum ac250_mins if objective_data==1 & cat_ac250_mins==3, d
tab cat_ac250_mins multimorbid, col chi2

stset stime, f(dead==1)
stcox i.cat_ac250_mins i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp ///
i.smoke i.excess_alcohol i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==1 & objective_data==1
stcox i.cat_ac250_mins i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp ///
i.smoke i.excess_alcohol i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if multimorbid==0 & objective_data==1

stset obj_stime if objective_data==1, f(dead==1)
stcox i.cat_ac250_mins i.sex currentobject_age i.newethnic i.cat_townsend_depriv  if multimorbid==1 & objective_data==1
stcox i.cat_ac250_mins i.sex currentobject_age i.newethnic i.cat_townsend_depriv  if multimorbid==0 & objective_data==1

********************************************************************************

** SEVERITY **

* OVERALL HEALTH RATING *

* Good and bad health 
*good=0, bad=1
gen good_bad1=0
replace good_bad1=1 if overall_health==3 | overall_health==4
replace good_bad1=. if overall_health==. | overall_health==-3 | overall_health==-1

* Status
gen healthmm=0
replace healthmm=. if good_bad1==. 
replace healthmm=1 if good_bad1==1 & multimorbid==0
replace healthmm=2 if good_bad1==0 & multimorbid==1
replace healthmm=3 if good_bad1==1 & multimorbid==1
tab healthmm

* Baseline table
tabout sex catage newethnic cat_townsend_depriv smoke bmigrp meat2more redmeat oilyfish nonoilyfish guideline_fruitveg ///
excess_alcohol empcat edu_level cat_sedtime healthmm using Septem/baseline_overallhealth_multimorbid.xls, cells(freq col) ///
stats(chi2) layout(No. % No. % No %) replace 

centile age if healthmm==0, centile(50,25,75)
centile age if healthmm==1, centile(50,25,75)
centile age if healthmm==2, centile(50,25,75)
centile age if healthmm==3, centile(50,25,75)

centile typePA_METtot if healthmm==0, centile(50,25,75)
centile typePA_METtot if healthmm==1, centile(50,25,75)
centile typePA_METtot if healthmm==2, centile(50,25,75)
centile typePA_METtot if healthmm==3, centile(50,25,75)
tab typePA_pa_status healthmm, col chi2 m

centile IPAQ_METtotal if healthmm==0, centile(50,25,75)
centile IPAQ_METtotal if healthmm==1, centile(50,25,75)
centile IPAQ_METtotal if healthmm==2, centile(50,25,75)
centile IPAQ_METtotal if healthmm==3, centile(50,25,75)
tab IPAQ_pa_status healthmm, col chi2 m

centile objective if healthmm==0 & objective_data==1, centile(50,25,75)
centile objective if healthmm==1 & objective_data==1, centile(50,25,75)
centile objective if healthmm==2 & objective_data==1, centile(50,25,75)
centile objective if healthmm==3 & objective_data==1, centile(50,25,75)
tab  cat_objective healthmm if objective_data==1 , col chi2 m

* Prevalence diagram
tabout hyp1065-cancer if healthmm==0 using Septem\prevalence_healthmm0_.xls, oneway ptotal(single) cells(freq col) layout(% No.) replace
tabout hyp1065-cancer if healthmm==1 using Septem\prevalence_healthmm1_.xls, oneway ptotal(single) cells(freq col) layout(% No.) replace
tabout hyp1065-cancer if healthmm==2 using Septem\prevalence_healthmm2_.xls, oneway ptotal(single) cells(freq col) layout(% No.) replace
tabout hyp1065-cancer if healthmm==3 using Septem\prevalence_healthmm3_.xls, oneway ptotal(single) cells(freq col) layout(% No.) replace

* Hazard ratios 
stset stime, f(dead==1)
stcox i.healthmm
stcox i.healthmm age sex

* Only include the fully adjusted model
* LTPA
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==0
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==1
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==2
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==3

* IPAQ
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==0
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==1
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==2
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==3
		
* Objective 
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==0 & objective_data==1
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==1 & objective_data==1
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==2 & objective_data==1
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if healthmm==3 & objective_data==1

** TOP-10 COMORBIDITY **
tab comorbid_10

* Baseline table
tabout sex catage newethnic cat_townsend_depriv edu_level empcat  bmigrp smoke excess_alcohol guideline_fruitveg oilyfish nonoilyfish meat2more redmeat     cat_sedtime ///
comorbid_10  using Septem/baseline_comorbidity10_data.xls, cells(freq col) stats(chi2) layout(No. % No. % No %) replace 

centile age if comorbid_10==1, centile(50,25,75)
centile age if comorbid_10==0, centile(50,25,75)

centile typePA_METtot if comorbid_10==1, centile(50,25,75)
centile typePA_METtot if comorbid_10==0, centile(50,25,75)
tab typePA_pa_status comorbid_10, col chi2 m

centile IPAQ_METtotal if comorbid_10==1, centile(50,25,75)
centile IPAQ_METtotal if comorbid_10==0, centile(50,25,75)
tab IPAQ_pa_status comorbid_10, col chi2 m

centile objective if comorbid_10==1 & objective_data==1, centile(50,25,75)
centile objective if comorbid_10==0 & objective_data==1, centile(50,25,75)
tab  cat_objective comorbid_10 if objective_data==1 , col chi2 m

* Hazard ratios 
stcox i.comorbid_10
stcox i.comorbid_10 age sex

* LTPA
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if comorbid_10==1
stcox i.typePA_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if comorbid_10==0
		
* IPAQ
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if comorbid_10==1
stcox i.IPAQ_pa_status i.sex age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if comorbid_10==0

* Objective 
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if comorbid_10==1 & objective_data==1
stcox i.cat_objective i.sex currentobject_age i.newethnic i.cat_townsend_depriv i.empcat i.edu_level i.bmigrp i.smoke i.excess_alcohol ///
i.guideline_fruitveg i.nonoilyfish i.oilyfish i.meat2more i.redmeat  i.cat_sedtime if comorbid_10==0 & objective_data==1

********************************************************************************		
	
** LIFE EXPECTANCY CALCULATIONS **

* Run each programme in separate do files

********************************************************************************	
	
* With and without multimorbidity *
set more off 
clear
cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Data"
use "UKBIOBANK 11092017.dta", clear
count
stset currentage, failure(dead == 1) enter(age)  
stpm2 multimorbid , df(4) scale(hazard) eform nolog   
			
// create an empty file to save the yll results - ru altogether 
preserve
clear
tempfile results2
save `results2', emptyok replace
restore
timer clear 1
// create the conditional age and the time variable
foreach i  of num 45(1)100 {
		timer on 1
		preserve
		gen t`i'= `i' in 1/100  
		range tt`i' `i' 100 100
	    predictnl mm`i' = [predict( surv timevar(tt`i') at( multimorbid 0) ) / predict( surv timevar(t`i') at(multimorbid 0))] ///
				    	- [predict( surv timevar(tt`i') at( multimorbid 1) ) / predict( surv timevar(t`i') at(multimorbid 1) ) ],  ci(lci`i' uci`i') level(95) 
					
		integ mm`i' tt`i'
	    scalar area_mm`i' = r(integral)
		
		integ lci`i' tt`i'
	    scalar area_lci`i' = r(integral)
		
		integ uci`i' tt`i'
	    scalar area_uci`i' = r(integral)
		
		
		clear
		set obs 1
		gen cond = `i'
		gen erl_mm = area_mm`i'
		gen erl_lci = area_lci`i'
		gen erl_uci = area_uci`i'
		append using `results2'
		save `results2', replace
		restore
		timer off 1
	  } 
timer list 1    
use `results2', clear
sort cond

********************************************************************************

* LTPA with multimorbidity * 

set more off 
clear
cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Data"
use "UKBIOBANK 11092017.dta", clear
count
stset currentage, failure(dead == 1) enter(age)  
stpm2 i.typePA_pa_status if multimorbid==1 , df(4) scale(hazard) eform nolog   
// create an empty file to save the yll results - ru altogether 
preserve
clear
tempfile ltpamm
save `ltpamm', emptyok replace
restore
timer clear 1
// create the conditional age and the time variable
foreach i  of num 45(1)100 {
		timer on 1
		preserve
		gen t`i'= `i' in 1/100  
		range tt`i' `i' 100 100
	    predictnl lowmod`i' = [predict( surv timevar(tt`i') at( typePA_pa_status 1) ) / predict( surv timevar(t`i') at(typePA_pa_status 1))] ///
				    	    - [predict( surv timevar(tt`i') at( typePA_pa_status 0) ) / predict( surv timevar(t`i') at(typePA_pa_status 0) ) ],  ci(lowmod_lci`i' lowmod_uci`i') level(95) 
			
		predictnl highmod`i' = [predict( surv timevar(tt`i') at( typePA_pa_status 2) ) / predict( surv timevar(t`i') at(typePA_pa_status 2))] ///
				    	     - [predict( surv timevar(tt`i') at( typePA_pa_status 0) ) / predict( surv timevar(t`i') at(typePA_pa_status 0) ) ],  ci(highmod_lci`i' highmod_uci`i') level(95) 
				
		integ lowmod`i' tt`i'
	    scalar area_lowmod`i' = r(integral)
		
		integ lowmod_lci`i' tt`i'
	    scalar area_lowmod_lci`i' = r(integral)
		
		integ lowmod_uci`i' tt`i'
	    scalar area_lowmod_uci`i' = r(integral)
		
		integ highmod`i' tt`i'
	    scalar area_highmod`i' = r(integral)
		
		integ highmod_lci`i' tt`i'
	    scalar area_highmod_lci`i' = r(integral)
		
		integ highmod_uci`i' tt`i'
	    scalar area_highmod_uci`i' = r(integral)
		
		clear
		set obs 1
		gen cond = `i'
		gen erl_lowmod = area_lowmod`i'
		gen erl_lowmod_lci = area_lowmod_lci`i'
		gen erl_lowmod_uci = area_lowmod_uci`i'
		
		gen erl_highmod = area_highmod`i'
		gen erl_highmod_lci = area_highmod_lci`i'
		gen erl_highmod_uci = area_highmod_uci`i'
			
		append using `ltpamm'
		save `ltpamm', replace
		restore
		timer off 1
	  } 
timer list 1     
use `ltpamm', clear
save "ltpa_ylg_mm.dta", replace

********************************************************************************

* LTPA without multimorbidity * 
set more off 
clear
cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Data"
use "UKBIOBANK 11092017.dta", clear
count
stset currentage, failure(dead == 1) enter(age)  
stpm2 i.typePA_pa_status if multimorbid==0 , df(4) scale(hazard) eform nolog   	
// create an empty file to save the yll results - ru altogether 
preserve
clear
tempfile ltpa_nomm
save `ltpa_nomm', emptyok replace
restore
timer clear 1
// create the conditional age and the time variable
foreach i  of num 45(1)100 {
		timer on 1
		preserve
		gen t`i'= `i' in 1/100  
		range tt`i' `i' 100 100
	    predictnl lowmod`i' = [predict( surv timevar(tt`i') at( typePA_pa_status 1) ) / predict( surv timevar(t`i') at(typePA_pa_status 1))] ///
				    	    - [predict( surv timevar(tt`i') at( typePA_pa_status 0) ) / predict( surv timevar(t`i') at(typePA_pa_status 0) ) ],  ci(lowmod_lci`i' lowmod_uci`i') level(95) 
			
		predictnl highmod`i' = [predict( surv timevar(tt`i') at( typePA_pa_status 2) ) / predict( surv timevar(t`i') at(typePA_pa_status 2))] ///
				    	     - [predict( surv timevar(tt`i') at( typePA_pa_status 0) ) / predict( surv timevar(t`i') at(typePA_pa_status 0) ) ],  ci(highmod_lci`i' highmod_uci`i') level(95) 
				
			
		integ lowmod`i' tt`i'
	    scalar area_lowmod`i' = r(integral)
		
		integ lowmod_lci`i' tt`i'
	    scalar area_lowmod_lci`i' = r(integral)
		
		integ lowmod_uci`i' tt`i'
	    scalar area_lowmod_uci`i' = r(integral)
		
		integ highmod`i' tt`i'
	    scalar area_highmod`i' = r(integral)
		
		integ highmod_lci`i' tt`i'
	    scalar area_highmod_lci`i' = r(integral)
		
		integ highmod_uci`i' tt`i'
	    scalar area_highmod_uci`i' = r(integral)
		
		clear
		set obs 1
		gen cond = `i'
		gen erl_lowmod = area_lowmod`i'
		gen erl_lowmod_lci = area_lowmod_lci`i'
		gen erl_lowmod_uci = area_lowmod_uci`i'
		
		gen erl_highmod = area_highmod`i'
		gen erl_highmod_lci = area_highmod_lci`i'
		gen erl_highmod_uci = area_highmod_uci`i'
			
		append using `ltpa_nomm'
		save `ltpa_nomm', replace
		restore
		timer off 1
	  }
timer list 1    
use `ltpa_nomm', clear
save "ltpa_ylg_no_mm.dta", replace

********************************************************************************

* IPAQ with multimorbidity * 
set more off 
clear
cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Data"
use "UKBIOBANK 11092017.dta", clear
count
stset currentage, failure(dead == 1) enter(age)  
stpm2 i.IPAQ_pa_status if multimorbid==1 , df(4) scale(hazard) eform nolog   	
preserve
clear
tempfile ipaqmm
save `ipaqmm', emptyok replace
restore
timer clear 1
// create the conditional age and the time variable
foreach i  of num 45(1)100 {
		timer on 1
		preserve
		gen t`i'= `i' in 1/100  
		range tt`i' `i' 100 100
	    predictnl lowmod`i' = [predict( surv timevar(tt`i') at( IPAQ_pa_status 1) ) / predict( surv timevar(t`i') at(IPAQ_pa_status 1))] ///
				    	    - [predict( surv timevar(tt`i') at( IPAQ_pa_status 0) ) / predict( surv timevar(t`i') at(IPAQ_pa_status 0) ) ],  ci(lowmod_lci`i' lowmod_uci`i') level(95) 
			
		predictnl highmod`i' = [predict( surv timevar(tt`i') at( IPAQ_pa_status 2) ) / predict( surv timevar(t`i') at(IPAQ_pa_status 2))] ///
				    	     - [predict( surv timevar(tt`i') at( IPAQ_pa_status 0) ) / predict( surv timevar(t`i') at(IPAQ_pa_status 0) ) ],  ci(highmod_lci`i' highmod_uci`i') level(95) 
				
			
		integ lowmod`i' tt`i'
	    scalar area_lowmod`i' = r(integral)
		
		integ lowmod_lci`i' tt`i'
	    scalar area_lowmod_lci`i' = r(integral)
		
		integ lowmod_uci`i' tt`i'
	    scalar area_lowmod_uci`i' = r(integral)
		
		integ highmod`i' tt`i'
	    scalar area_highmod`i' = r(integral)
		
		integ highmod_lci`i' tt`i'
	    scalar area_highmod_lci`i' = r(integral)
		
		integ highmod_uci`i' tt`i'
	    scalar area_highmod_uci`i' = r(integral)
		
		clear
		set obs 1
		gen cond = `i'
		gen erl_lowmod = area_lowmod`i'
		gen erl_lowmod_lci = area_lowmod_lci`i'
		gen erl_lowmod_uci = area_lowmod_uci`i'
		
		gen erl_highmod = area_highmod`i'
		gen erl_highmod_lci = area_highmod_lci`i'
		gen erl_highmod_uci = area_highmod_uci`i'
			
		append using `ipaqmm'
		save `ipaqmm', replace
		restore
		timer off 1
	  }
timer list 1     
use `ipaqmm', clear
save "ipaq_ylg_mm.dta", replace

********************************************************************************

* IPAQ without multimorbidity * 
set more off 
clear
cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Data"
use "UKBIOBANK 11092017.dta", clear
count
stset currentage, failure(dead == 1) enter(age)  
stpm2 i.IPAQ_pa_status if multimorbid==0 , df(4) scale(hazard) eform nolog   	
// create an empty file to save the yll results - ru altogether 
preserve
clear
tempfile ipaq_nomm
save `ipaq_nomm', emptyok replace
restore
timer clear 1
// create the conditional age and the time variable
foreach i  of num 45(1)100 {
		timer on 1
		preserve
		gen t`i'= `i' in 1/100  
		range tt`i' `i' 100 100
	    predictnl lowmod`i' = [predict( surv timevar(tt`i') at( IPAQ_pa_status 1) ) / predict( surv timevar(t`i') at(IPAQ_pa_status 1))] ///
				    	    - [predict( surv timevar(tt`i') at( IPAQ_pa_status 0) ) / predict( surv timevar(t`i') at(IPAQ_pa_status 0) ) ],  ci(lowmod_lci`i' lowmod_uci`i') level(95) 
			
		predictnl highmod`i' = [predict( surv timevar(tt`i') at( IPAQ_pa_status 2) ) / predict( surv timevar(t`i') at(IPAQ_pa_status 2))] ///
				    	     - [predict( surv timevar(tt`i') at( IPAQ_pa_status 0) ) / predict( surv timevar(t`i') at(IPAQ_pa_status 0) ) ],  ci(highmod_lci`i' highmod_uci`i') level(95) 
				
			
		integ lowmod`i' tt`i'
	    scalar area_lowmod`i' = r(integral)
		
		integ lowmod_lci`i' tt`i'
	    scalar area_lowmod_lci`i' = r(integral)
		
		integ lowmod_uci`i' tt`i'
	    scalar area_lowmod_uci`i' = r(integral)
		
		integ highmod`i' tt`i'
	    scalar area_highmod`i' = r(integral)
		
		integ highmod_lci`i' tt`i'
	    scalar area_highmod_lci`i' = r(integral)
		
		integ highmod_uci`i' tt`i'
	    scalar area_highmod_uci`i' = r(integral)
		
		clear
		set obs 1
		gen cond = `i'
		gen erl_lowmod = area_lowmod`i'
		gen erl_lowmod_lci = area_lowmod_lci`i'
		gen erl_lowmod_uci = area_lowmod_uci`i'
		
		gen erl_highmod = area_highmod`i'
		gen erl_highmod_lci = area_highmod_lci`i'
		gen erl_highmod_uci = area_highmod_uci`i'
			
		append using `ipaq_nomm'
		save `ipaq_nomm', replace
		restore
		timer off 1
	  }
timer list 1   
use `ipaq_nomm', clear
save "ipaq_ylg_no_mm.dta", replace

********************************************************************************

* Objective with multimorbidity *
set more off 
clear
cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Data"
use "UKBIOBANK 11092017.dta", clear
count
stset currentage, failure(dead == 1) enter(age)  
stpm2 i.cat_objective if multimorbid==1 , df(4) scale(hazard) eform nolog   
// create an empty file to save the yll results - ru altogether 
preserve
clear
tempfile objmm
save `objmm', emptyok replace
restore
timer clear 1
// create the conditional age and the time variable
foreach i  of num 45(1)100 {
		timer on 1
		preserve
		gen t`i'= `i' in 1/100  
		range tt`i' `i' 100 100
	    predictnl lowmod`i' = [predict( surv timevar(tt`i') at( cat_objective 2) ) / predict( surv timevar(t`i') at(cat_objective 2))] ///
				    	    - [predict( surv timevar(tt`i') at( cat_objective 1) ) / predict( surv timevar(t`i') at(cat_objective 1) ) ],  ci(lowmod_lci`i' lowmod_uci`i') level(95) 
			
		predictnl highmod`i' = [predict( surv timevar(tt`i') at( cat_objective 3) ) / predict( surv timevar(t`i') at(cat_objective 3))] ///
				    	     - [predict( surv timevar(tt`i') at( cat_objective 1) ) / predict( surv timevar(t`i') at(cat_objective 1) ) ],  ci(highmod_lci`i' highmod_uci`i') level(95) 
				
			
		integ lowmod`i' tt`i'
	    scalar area_lowmod`i' = r(integral)
		
		integ lowmod_lci`i' tt`i'
	    scalar area_lowmod_lci`i' = r(integral)
		
		integ lowmod_uci`i' tt`i'
	    scalar area_lowmod_uci`i' = r(integral)
		
		integ highmod`i' tt`i'
	    scalar area_highmod`i' = r(integral)
		
		integ highmod_lci`i' tt`i'
	    scalar area_highmod_lci`i' = r(integral)
		
		integ highmod_uci`i' tt`i'
	    scalar area_highmod_uci`i' = r(integral)
		
		clear
		set obs 1
		gen cond = `i'
		gen erl_lowmod = area_lowmod`i'
		gen erl_lowmod_lci = area_lowmod_lci`i'
		gen erl_lowmod_uci = area_lowmod_uci`i'
		
		gen erl_highmod = area_highmod`i'
		gen erl_highmod_lci = area_highmod_lci`i'
		gen erl_highmod_uci = area_highmod_uci`i'
			
		append using `objmm'
		save `objmm', replace
		restore
		timer off 1
	  }
timer list 1     
use `objmm', clear
save "obj_ylg_mm.dta", replace

********************************************************************************

* Objective without multimorbidity *
set more off 
clear
cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Data"
use "UKBIOBANK 11092017.dta", clear
count
stset currentage, failure(dead == 1) enter(age)  
stpm2 i.cat_objective if multimorbid==0 , df(4) scale(hazard) eform nolog   	
// create an empty file to save the yll results - ru altogether 
preserve
clear
tempfile obj_nomm
save `obj_nomm', emptyok replace
restore
timer clear 1
// create the conditional age and the time variable
foreach i  of num 45(1)100 {
		timer on 1
		preserve
		gen t`i'= `i' in 1/100  
		range tt`i' `i' 100 100
	    predictnl lowmod`i' = [predict( surv timevar(tt`i') at( cat_objective 2) ) / predict( surv timevar(t`i') at(cat_objective 2))] ///
				    	    - [predict( surv timevar(tt`i') at( cat_objective 1) ) / predict( surv timevar(t`i') at(cat_objective 1) ) ],  ci(lowmod_lci`i' lowmod_uci`i') level(95) 
			
		predictnl highmod`i' = [predict( surv timevar(tt`i') at( cat_objective 3) ) / predict( surv timevar(t`i') at(cat_objective 3))] ///
				    	     - [predict( surv timevar(tt`i') at( cat_objective 1) ) / predict( surv timevar(t`i') at(cat_objective 1) ) ],  ci(highmod_lci`i' highmod_uci`i') level(95) 
				
			
		integ lowmod`i' tt`i'
	    scalar area_lowmod`i' = r(integral)
		
		integ lowmod_lci`i' tt`i'
	    scalar area_lowmod_lci`i' = r(integral)
		
		integ lowmod_uci`i' tt`i'
	    scalar area_lowmod_uci`i' = r(integral)
		
		integ highmod`i' tt`i'
	    scalar area_highmod`i' = r(integral)
		
		integ highmod_lci`i' tt`i'
	    scalar area_highmod_lci`i' = r(integral)
		
		integ highmod_uci`i' tt`i'
	    scalar area_highmod_uci`i' = r(integral)
		
		clear
		set obs 1
		gen cond = `i'
		gen erl_lowmod = area_lowmod`i'
		gen erl_lowmod_lci = area_lowmod_lci`i'
		gen erl_lowmod_uci = area_lowmod_uci`i'
		
		gen erl_highmod = area_highmod`i'
		gen erl_highmod_lci = area_highmod_lci`i'
		gen erl_highmod_uci = area_highmod_uci`i'
			
		append using `obj_nomm'
		save `obj_nomm', replace
		restore
		timer off 1
	  }   
timer list 1    
use `obj_nomm', clear
save "obj_ylg_no_mm.dta", replace

********************************************************************************

* Creating the graph for the PA measure *

cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Graphs_study\Study 1 and 2\Version 2"

twoway  (rarea erl_lowmod_lci erl_lowmod_uci cond, fcolor(orange_red%20)  lwidth(vvthin) lcolor(black%25) ) ///
                (rarea erl_highmod_lci erl_highmod_uci cond, fcolor(midblue%20) lwidth(vvthin) lcolor(black%25)) ///
                (line erl_lowmod cond, sort lcolor(orange_red) lwidth(medthick)) ///
                (line erl_highmod  cond, sort lcolor(midblue) lwidth(medthick)) ///
                  , legend(order(3 "Moderate PA (600-3000 MET-min/week)" 1 "Area indicates 95% CI" 4 "High PA (≥3000 MET-min/week)" 2 "Area indicates 95% CI") ring(0) cols(2) pos(2) symys(*.5) symxs(*.5) size(2) bm(tiny) lwidth( vvthin ) region(c(none)) ) ///
                  ylabel(0(1)5,angle(h) grid gmin gmax) ymtick(##2)  xmtick(##5) xlabel(45(5)100,angle(h) grid) ///
                  ytitle("Years of Life Gained") ///
                  xtitle("Age, y") graphregion(color(white))  title("With multimorbidity")  ysize(4)  xsize(4)
		  				  
twoway  (rarea erl_lowmod_lci erl_lowmod_uci cond, fcolor(orange_red%20)  lwidth(vvthin) lcolor(black%25) ) ///
                (rarea erl_highmod_lci erl_highmod_uci cond, fcolor(midblue%20) lwidth(vvthin) lcolor(black%25)) ///
                (line erl_lowmod cond, sort lcolor(orange_red) lwidth(medthick)) ///
                (line erl_highmod  cond, sort lcolor(midblue) lwidth(medthick) ) ///
                  , legend(order(3 "Moderate PA (600-3000 MET-min/week)" 1 "95% CI" 4 "High PA (≥3000 MET-min/week)" 2 "95% CI") ring(0) cols(2) pos(2) symys(*.5) symxs(*.5) size(2) bm(tiny) lwidth( vvthin ) region(c(none)) ) ///
                  ylabel(0(1)5,angle(h) grid gmin gmax) ymtick(##2) xmtick(##5) xlabel(45(5)100,angle(h) grid gmin gmax) ///
                  ytitle("Years of Life Gained") ///
                  xtitle("Age, y") graphregion(color(white))  title("Without multimorbidity") ysize(4)  xsize(4)				
				
* Objective PA graphs 

twoway  (rarea erl_lowmod_lci erl_lowmod_uci cond, fcolor(orange_red%20)  lwidth(vvthin) lcolor(black%25) ) ///
                (rarea erl_highmod_lci erl_highmod_uci cond, fcolor(midblue%20)  lwidth(vvthin) lcolor(black%25)) ///
                (line erl_lowmod cond, sort lcolor(orange_red) lwidth(medthick)) ///
                (line erl_highmod  cond, sort lcolor(midblue) lwidth(medthick) ) ///
                  , legend(order(3 "Moderate PA (10 mins/day brisk walking)" 1 "95% CI" 4 "High PA (22 mins/day brisk walking)" 2 "95% CI") ring(0) cols(2) pos(2) symys(*.5) symxs(*.5) size(2) bm(tiny) lwidth( vvthin ) region(c(none)) ) ///
                  ylabel(-1(1)12,angle(h) grid gmin gmax) ymtick(##2) xmtick(##1) xlabel(45(5)100,angle(h) grid gmin gmax) ///
                  ytitle("Years of Life Gained") ///
                  xtitle("Age, y") graphregion(color(white))  title("With multimorbidity") ysize(4)  xsize(4) 
				  
twoway  (rarea erl_lowmod_lci erl_lowmod_uci cond, fcolor(orange_red%20)  lwidth(vvthin) lcolor(black%25) ) ///
                (rarea erl_highmod_lci erl_highmod_uci cond, fcolor(midblue%20) lwidth(vvthin) lcolor(black%25)) ///
                (line erl_lowmod cond, sort lcolor(orange_red) lwidth(medthick)) ///
                (line erl_highmod  cond, sort lcolor(midblue) lwidth(medthick)) ///
                  ,legend(order(3 "Moderate PA (10 mins/day brisk walking)" 1 "95% CI" 4 "High PA (22 mins/day brisk walking)" 2 "95% CI") ring(0) cols(2) pos(2) symys(*.5) symxs(*.5) size(2) bm(tiny) lwidth( vvthin ) region(c(none)) ) ///
                  ylabel(-1(1)12,angle(h) grid gmin gmax) ymtick(##2) xmtick(##5) xlabel(45(5)100,angle(h) grid gmin gmax) ///
                  ytitle("Years of Life Gained") ///
                  xtitle("Age, y") graphregion(color(white))  title("Without multimorbidity") ysize(4)  xsize(4)	

graph combine ltpa_mm.gph ltpa_nomm.gph, row(1) iscale(0.8) title("Leisure-time physical activity", box bexpand)  saving(ltpa_both, replace) 
graph combine ipaq_mm.gph ipaq_nomm.gph, row(1) iscale(0.8) title("Total physical activity", box bexpand)  saving(ter_ipaq, replace)   
graph combine obj_mm.gph obj_nomm.gph, row(1) iscale(0.8) title("Objective physical activity", box bexpand)  saving(obj_comb, replace) 

*********************************************************************************

* At the age of 45 *

set more off 
clear
cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Data"
use "UKBIOBANK 11092017.dta", clear
count
stset currentage, failure(dead == 1) enter(age)  

** At age 45 
range tt45 45 100 100
gen t45 = 45 in 1/100

* With and without multimorbidity 
stpm2 i.multimorbid , df(4) scale(hazard) eform nolog   

predictnl mm45 = [predict( surv timevar(tt45) at( multimorbid 0) ) / predict( surv timevar(t45) at(multimorbid 0))] ///
				    	- [predict( surv timevar(tt45) at( multimorbid 1) ) / predict( surv timevar(t45) at(multimorbid 1) ) ],  ci(mm_lci45 mm_uci45) level(95) 
										
integ mm45 tt45  
integ mm_lci45 tt45  
integ mm_uci45 tt45  

range tt65 65 100 100
gen t65 = 65 in 1/100

predictnl mm65 = [predict( surv timevar(tt65) at( multimorbid 0) ) / predict( surv timevar(t65) at(multimorbid 0))] ///
				    	- [predict( surv timevar(tt65) at( multimorbid 1) ) / predict( surv timevar(t65) at(multimorbid 1) ) ],  ci(mm_lci65 mm_uci65) level(95) 								
integ mm65 tt65  
integ mm_lci65 tt65  
integ mm_uci65 tt65  
 
***********************************************************************************
* Serverity 

* Overall health
tab healthmm
stpm2 i.healthmm , df(4) scale(hazard) eform nolog   

predictnl hmm45 = [predict( surv timevar(tt45) at( healthmm 0) ) / predict( surv timevar(t45) at(healthmm 0))] ///
				    	- [predict( surv timevar(tt45) at( healthmm 3) ) / predict( surv timevar(t45) at(healthmm 3) ) ],  ci(hmm_lci45 hmm_uci45) level(95) 							
integ hmm45 tt45  
integ hmm_lci45 tt45  
integ hmm_uci45 tt45  

***********************************************************************************
* Top 10 comorbidity
tab comorbid_10
stpm2 i.comorbid_10 , df(4) scale(hazard) eform nolog   

predictnl cmm45 = [predict( surv timevar(tt45) at( comorbid_10 0) ) / predict( surv timevar(t45) at(comorbid_10 0))] ///
				    	- [predict( surv timevar(tt45) at( comorbid_10 1) ) / predict( surv timevar(t45) at(comorbid_10 1) ) ],  ci(cmm_lci45 cmm_uci45) level(95) 							
integ cmm45 tt45  
integ cmm_lci45 tt45  
integ cmm_uci45 tt45  

********************************************************************************************
* Self reported PA
tab cat_typemet 
tab cat_ipaq 
tab multimorbid cat_typemet , row chi2
tab multimorbid cat_ipaqmet , row chi2
 
*LTPA
stpm2 i.cat_typemet if multimorbid==0 , df(4) scale(hazard) eform nolog   

predictnl ctlowmod = [predict( surv timevar(tt45) at( cat_typemet 2) ) / predict( surv timevar(t45) at(cat_typemet 2))] ///
				    	    - [predict( surv timevar(tt45) at( cat_typemet 1) ) / predict( surv timevar(t45) at(cat_typemet 1) ) ],  ci(ctlowmod_lci ctlowmod_uci) level(95) 
			
predictnl cthighmod = [predict( surv timevar(tt45) at( cat_typemet 3) ) / predict( surv timevar(t45) at(cat_typemet 3))] ///
				    	     - [predict( surv timevar(tt45) at( cat_typemet 1) ) / predict( surv timevar(t45) at(cat_typemet 1) ) ],  ci(cthighmod_lci cthighmod_uci) level(95) 		
integ ctlowmod tt45  
integ ctlowmod_lci tt45  
integ ctlowmod_uci tt45 

integ cthighmod tt45  
integ cthighmod_lci tt45  
integ cthighmod_uci tt45 

stpm2 i.cat_typemet if multimorbid==1 , df(4) scale(hazard) eform nolog   

predictnl mctlowmod = [predict( surv timevar(tt45) at( cat_typemet 2) ) / predict( surv timevar(t45) at(cat_typemet 2))] ///
				    	    - [predict( surv timevar(tt45) at( cat_typemet 1) ) / predict( surv timevar(t45) at(cat_typemet 1) ) ],  ci(mctlowmod_lci mctlowmod_uci) level(95) 
			
predictnl mcthighmod = [predict( surv timevar(tt45) at( cat_typemet 3) ) / predict( surv timevar(t45) at(cat_typemet 3))] ///
				    	     - [predict( surv timevar(tt45) at( cat_typemet 1) ) / predict( surv timevar(t45) at(cat_typemet 1) ) ],  ci(mcthighmod_lci mcthighmod_uci) level(95) 
				
integ mctlowmod tt45  
integ mctlowmod_lci tt45  
integ mctlowmod_uci tt45 

integ mcthighmod tt45  
integ mcthighmod_lci tt45  
integ mcthighmod_uci tt45 

* IPAQ
stpm2 i.cat_ipaqmet if multimorbid==0 , df(4) scale(hazard) eform nolog   

predictnl iplowmod = [predict( surv timevar(tt45) at( cat_ipaqmet 2) ) / predict( surv timevar(t45) at(cat_ipaqmet 2))] ///
				    	    - [predict( surv timevar(tt45) at( cat_ipaqmet 1) ) / predict( surv timevar(t45) at(cat_ipaqmet 1) ) ],  ci(iplowmod_lci iplowmod_uci) level(95) 
			
predictnl iphighmod = [predict( surv timevar(tt45) at( cat_ipaqmet 3) ) / predict( surv timevar(t45) at(cat_ipaqmet 3))] ///
				    	     - [predict( surv timevar(tt45) at( cat_ipaqmet 1) ) / predict( surv timevar(t45) at(cat_ipaqmet 1) ) ],  ci(iphighmod_lci iphighmod_uci) level(95) 
			
integ iplowmod tt45  
integ iplowmod_lci tt45  
integ iplowmod_uci tt45 

integ iphighmod tt45  
integ iphighmod_lci tt45  
integ iphighmod_uci tt45 

**********************
stpm2 i.cat_ipaqmet if multimorbid==1 , df(4) scale(hazard) eform nolog   

predictnl miplowmod = [predict( surv timevar(tt45) at( cat_ipaqmet 2) ) / predict( surv timevar(t45) at(cat_ipaqmet 2))] ///
				    	    - [predict( surv timevar(tt45) at( cat_ipaqmet 1) ) / predict( surv timevar(t45) at(cat_ipaqmet 1) ) ],  ci(miplowmod_lci miplowmod_uci) level(95) 
			
predictnl miphighmod = [predict( surv timevar(tt45) at( cat_ipaqmet 3) ) / predict( surv timevar(t45) at(cat_ipaqmet 3))] ///
				    	     - [predict( surv timevar(tt45) at( cat_ipaqmet 1) ) / predict( surv timevar(t45) at(cat_ipaqmet 1) ) ],  ci(miphighmod_lci miphighmod_uci) level(95) 		
integ miplowmod tt45  
integ miplowmod_lci tt45  
integ miplowmod_uci tt45 

integ miphighmod tt45  
integ miphighmod_lci tt45  
integ miphighmod_uci tt45 


********************************************************************************
* SEVERITY *

set more off 
clear
cd "\\uol.le.ac.uk\root\staff\home\y\yc244\My Documents\1 PhD Folder\7 Data\Data"
use "UKBIOBANK 11092017.dta", clear
stset currentage, failure(dead == 1) enter(age)  

** At age 45 
range tt45 45 100 100
gen t45 = 45 in 1/100

* OVERALL HEALTH 
tab healthmm
stpm2 i.typePA_pa_status if healthmm==0, df(4) scale(hazard) eform nolog   

predictnl lowmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 1) ) / predict( surv timevar(t45) at(typePA_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(lowmod_lci45 lowmod45_uci45) level(95) 

predictnl highmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 2) ) / predict( surv timevar(t45) at(typePA_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(highmod_lci45 highmod_uci45) level(95) 
integ lowmod45 tt45  
integ lowmod_lci45 tt45  
integ lowmod45_uci45 tt45  

integ highmod45 tt45  
integ highmod_lci45 tt45  
integ highmod_uci45 tt45  

*********************
stpm2 i.typePA_pa_status if healthmm==1, df(4) scale(hazard) eform nolog   

predictnl mlowmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 1) ) / predict( surv timevar(t45) at(typePA_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(mlowmod_lci45 mlowmod45_uci45) level(95) 

predictnl mhighmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 2) ) / predict( surv timevar(t45) at(typePA_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(mhighmod_lci45 mhighmod_uci45) level(95) 
integ mlowmod45 tt45  
integ mlowmod_lci45 tt45  
integ mlowmod45_uci45 tt45  

integ mhighmod45 tt45  
integ mhighmod_lci45 tt45  
integ mhighmod_uci45 tt45  

*********************
stpm2 i.typePA_pa_status if healthmm==2, df(4) scale(hazard) eform nolog   

predictnl gmlowmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 1) ) / predict( surv timevar(t45) at(typePA_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(gmlowmod_lci45 gmlowmod45_uci45) level(95) 

predictnl gmhighmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 2) ) / predict( surv timevar(t45) at(typePA_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(gmhighmod_lci45 gmhighmod_uci45) level(95) 
integ gmlowmod45 tt45  
integ gmlowmod_lci45 tt45  
integ gmlowmod45_uci45 tt45  

integ gmhighmod45 tt45  
integ gmhighmod_lci45 tt45  
integ gmhighmod_uci45 tt45  


*********************
stpm2 i.typePA_pa_status if healthmm==3, df(4) scale(hazard) eform nolog   

predictnl pmlowmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 1) ) / predict( surv timevar(t45) at(typePA_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(pmlowmod_lci45 pmlowmod45_uci45) level(95) 

predictnl pmhighmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 2) ) / predict( surv timevar(t45) at(typePA_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(pmhighmod_lci45 pmhighmod_uci45) level(95) 
	
integ pmlowmod45 tt45  
integ pmlowmod_lci45 tt45  
integ pmlowmod45_uci45 tt45  

integ pmhighmod45 tt45  
integ pmhighmod_lci45 tt45  
integ pmhighmod_uci45 tt45  

***************************************************
* IPAQ

stpm2 i.IPAQ_pa_status if healthmm==0, df(4) scale(hazard) eform nolog   

predictnl ilowmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 1) ) / predict( surv timevar(t45) at(IPAQ_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(ilowmod_lci45 ilowmod45_uci45) level(95) 

predictnl ihighmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 2) ) / predict( surv timevar(t45) at(IPAQ_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(ihighmod_lci45 ihighmod_uci45) level(95) 
	
integ ilowmod45 tt45  
integ ilowmod_lci45 tt45  
integ ilowmod45_uci45 tt45  

integ ihighmod45 tt45  
integ ihighmod_lci45 tt45  
integ ihighmod_uci45 tt45  

*********************
stpm2 i.IPAQ_pa_status if healthmm==1, df(4) scale(hazard) eform nolog   

predictnl imlowmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 1) ) / predict( surv timevar(t45) at(IPAQ_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(imlowmod_lci45 imlowmod45_uci45) level(95) 

predictnl imhighmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 2) ) / predict( surv timevar(t45) at(IPAQ_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(imhighmod_lci45 imhighmod_uci45) level(95) 
	
integ imlowmod45 tt45  
integ imlowmod_lci45 tt45  
integ imlowmod45_uci45 tt45  

integ imhighmod45 tt45  
integ imhighmod_lci45 tt45  
integ imhighmod_uci45 tt45  

*********************
stpm2 i.IPAQ_pa_status if healthmm==2, df(4) scale(hazard) eform nolog   

predictnl igmlowmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 1) ) / predict( surv timevar(t45) at(IPAQ_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(igmlowmod_lci45 igmlowmod45_uci45) level(95) 

predictnl igmhighmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 2) ) / predict( surv timevar(t45) at(IPAQ_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(igmhighmod_lci45 igmhighmod_uci45) level(95) 
	
integ igmlowmod45 tt45  
integ igmlowmod_lci45 tt45  
integ igmlowmod45_uci45 tt45  

integ igmhighmod45 tt45  
integ igmhighmod_lci45 tt45  
integ igmhighmod_uci45 tt45  

*********************
stpm2 i.IPAQ_pa_status if healthmm==3, df(4) scale(hazard) eform nolog   

predictnl ipmlowmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 1) ) / predict( surv timevar(t45) at(IPAQ_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(ipmlowmod_lci45 ipmlowmod45_uci45) level(95) 

predictnl ipmhighmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 2) ) / predict( surv timevar(t45) at(IPAQ_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(ipmhighmod_lci45 ipmhighmod_uci45) level(95) 
	
integ ipmlowmod45 tt45  
integ ipmlowmod_lci45 tt45  
integ ipmlowmod45_uci45 tt45  

integ ipmhighmod45 tt45  
integ ipmhighmod_lci45 tt45  
integ ipmhighmod_uci45 tt45 

**********************

* Objective

stpm2 i.cat_objective if healthmm==0, df(4) scale(hazard) eform nolog   

 predictnl lowmod45 = [predict( surv timevar(tt45) at( cat_objective 2) ) / predict( surv timevar(t45) at(cat_objective 2))] ///
  - [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1) ) ],  ci(lowmod_lci45 lowmod45_uci45) level(95) 

predictnl highmod45 = [predict( surv timevar(tt45) at( cat_objective 3) ) / predict( surv timevar(t45) at(cat_objective 3))] ///
 - [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1) ) ],  ci(highmod_lci45 highmod_uci45) level(95) 
     
integ lowmod45 tt45  
integ lowmod_lci45 tt45  
integ lowmod45_uci45 tt45  

integ highmod45 tt45  
integ highmod_lci45 tt45  
integ highmod_uci45 tt45  

**********************
stpm2 i.cat_objective if healthmm==1, df(4) scale(hazard) eform nolog   

predictnl mlowmod45 = [predict( surv timevar(tt45) at( cat_objective 2) ) / predict( surv timevar(t45) at(cat_objective 2))] ///
 - [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1) ) ],  ci(mlowmod_lci45 mlowmod45_uci45) level(95) 

predictnl mhighmod45 = [predict( surv timevar(tt45) at( cat_objective 3) ) / predict( surv timevar(t45) at(cat_objective 3))] ///
- [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1) ) ],  ci(mhighmod_lci45 mhighmod_uci45) level(95) 

integ mlowmod45 tt45  
integ mlowmod_lci45 tt45  
integ mlowmod45_uci45 tt45  

integ mhighmod45 tt45  
integ mhighmod_lci45 tt45  
integ mhighmod_uci45 tt45  

**********************
stpm2 i.cat_objective if healthmm==2, df(4) scale(hazard) eform nolog   

predictnl gmlowmod45 = [predict( surv timevar(tt45) at( cat_objective 2) ) / predict( surv timevar(t45) at(cat_objective 2))] ///
- [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1) ) ],  ci(gmlowmod_lci45 gmlowmod45_uci45) level(95) 

predictnl gmhighmod45 = [predict( surv timevar(tt45) at( cat_objective 3) ) / predict( surv timevar(t45) at(cat_objective 3))] ///
- [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1) ) ],  ci(gmhighmod_lci45 gmhighmod_uci45) level(95) 
(491,839 missing values generated)
     
integ gmlowmod45 tt45  
integ gmlowmod_lci45 tt45  

integ gmlowmod45_uci45 tt45  
integ gmhighmod45 tt45  

integ gmhighmod_lci45 tt45  
integ gmhighmod_uci45 tt45  

**********************
stpm2 i.cat_objective if healthmm==3, df(4) scale(hazard) eform nolog   

predictnl pmlowmod45 = [predict( surv timevar(tt45) at( cat_objective 2) ) / predict( surv timevar(t45) at(cat_objective 2))] ///
- [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1) ) ],  ci(pmlowmod_lci45 pmlowmod45_uci45) level(95) 

predictnl pmhighmod45 = [predict( surv timevar(tt45) at( cat_objective 3) ) / predict( surv timevar(t45) at(cat_objective 3))] ///
- [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1) ) ],  ci(pmhighmod_lci45 pmhighmod_uci45) level(95) 

integ pmlowmod45 tt45  
integ pmlowmod_lci45 tt45  

integ pmlowmod45_uci45 tt45  
integ pmhighmod45 tt45  

integ pmhighmod_lci45 tt45  
integ pmhighmod_uci45 tt45  

******************************

* TOP-10 COMORBIDITY
tab comorbid_10

stpm2 i.typePA_pa_status if comorbid_10==0, df(4) scale(hazard) eform nolog   

predictnl clowmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 1) ) / predict( surv timevar(t45) at(typePA_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(clowmod_lci45 clowmod45_uci45) level(95) 

predictnl chighmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 2) ) / predict( surv timevar(t45) at(typePA_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(chighmod_lci45 chighmod_uci45) level(95) 
	
integ clowmod45 tt45  
integ clowmod_lci45 tt45  
integ clowmod45_uci45 tt45  

integ chighmod45 tt45  
integ chighmod_lci45 tt45  
integ chighmod_uci45 tt45  


**********************
stpm2 i.typePA_pa_status if comorbid_10==1, df(4) scale(hazard) eform nolog   

predictnl mclowmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 1) ) / predict( surv timevar(t45) at(typePA_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(mclowmod_lci45 mclowmod45_uci45) level(95) 

predictnl mchighmod45 = [predict( surv timevar(tt45) at( typePA_pa_status 2) ) / predict( surv timevar(t45) at(typePA_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( typePA_pa_status 0) ) / predict( surv timevar(t45) at(typePA_pa_status 0) ) ],  ci(mchighmod_lci45 mchighmod_uci45) level(95) 
	
integ mclowmod45 tt45  
integ mclowmod_lci45 tt45  
integ mclowmod45_uci45 tt45  

integ mchighmod45 tt45  
integ mchighmod_lci45 tt45  
integ mchighmod_uci45 tt45  

**********************
* IPAQ
stpm2 i.IPAQ_pa_status if comorbid_10==0, df(4) scale(hazard) eform nolog   

predictnl imclowmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 1) ) / predict( surv timevar(t45) at(IPAQ_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(imclowmod_lci45 imclowmod45_uci45) level(95) 

predictnl imchighmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 2) ) / predict( surv timevar(t45) at(IPAQ_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(imchighmod_lci45 imchighmod_uci45) level(95) 
	
integ imclowmod45 tt45  
integ imclowmod_lci45 tt45  
integ imclowmod45_uci45 tt45  

integ imchighmod45 tt45  
integ imchighmod_lci45 tt45  
integ imchighmod_uci45 tt45  

**********************
stpm2 i.IPAQ_pa_status if comorbid_10==1, df(4) scale(hazard) eform nolog   

predictnl mimclowmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 1) ) / predict( surv timevar(t45) at(IPAQ_pa_status 1))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(mimclowmod_lci45 mimclowmod45_uci45) level(95) 

predictnl mimchighmod45 = [predict( surv timevar(tt45) at( IPAQ_pa_status 2) ) / predict( surv timevar(t45) at(IPAQ_pa_status 2))] ///
				    	- [predict( surv timevar(tt45) at( IPAQ_pa_status 0) ) / predict( surv timevar(t45) at(IPAQ_pa_status 0) ) ],  ci(mimchighmod_lci45 mimchighmod_uci45) level(95) 
	
integ mimclowmod45 tt45  
integ mimclowmod_lci45 tt45  
integ mimclowmod45_uci45 tt45  

integ mimchighmod45 tt45  
integ mimchighmod_lci45 tt45  
integ mimchighmod_uci45 tt45  

**********************
* Objetcive PA

stpm2 i.cat_objective if comorbid_10==0, df(4) scale(hazard) eform nolog   

predictnl cmod45 = [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1))] ///
				    	- [predict( surv timevar(tt45) at( cat_objective 0) ) / predict( surv timevar(t45) at(cat_objective 0) ) ],  ci(cmod45_lci45 cmod45_uci45) level(95) 

predictnl chigh45 = [predict( surv timevar(tt45) at( cat_objective 2) ) / predict( surv timevar(t45) at(cat_objective 2))] ///
				    	- [predict( surv timevar(tt45) at( cat_objective 0) ) / predict( surv timevar(t45) at(cat_objective 0) ) ],  ci(chigh45_lci45 chigh45_uci45) level(95) 

integ cmod45 tt45  
integ cmod45_lci45 tt45  
integ cmod45_uci45 tt45  

integ chigh45 tt45  
integ chigh45_lci45 tt45  
integ chigh45_uci45 tt45  

**********************
stpm2 i.cat_objective if comorbid_10==1, df(4) scale(hazard) eform nolog   

predictnl mcmod45 = [predict( surv timevar(tt45) at( cat_objective 1) ) / predict( surv timevar(t45) at(cat_objective 1))] ///
				    	- [predict( surv timevar(tt45) at( cat_objective 0) ) / predict( surv timevar(t45) at(cat_objective 0) ) ],  ci(mcmod45_lci45 mcmod45_uci45) level(95) 

predictnl mchigh45 = [predict( surv timevar(tt45) at( cat_objective 2) ) / predict( surv timevar(t45) at(cat_objective 2))] ///
				    	- [predict( surv timevar(tt45) at( cat_objective 0) ) / predict( surv timevar(t45) at(cat_objective 0) ) ],  ci(mchigh45_lci45 mchigh45_uci45) level(95) 
integ mcmod45 tt45  
integ mcmod45_lci45 tt45  
integ mcmod45_uci45 tt45  

integ mchigh45 tt45  
integ mchigh45_lci45 tt45  
integ mchigh45_uci45 tt45  

*********************************************************************************
