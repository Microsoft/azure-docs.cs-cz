---
title: Reference k atributu SAP SuccessFactors | Microsoft Docs
description: Informace o tom, které atributy z SuccessFactors jsou podporované SuccessFactors zřizováním na základě lidských zdrojů
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522352"
---
# <a name="sap-successfactors-attribute-reference"></a>Reference k atributu SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Podporované entity a atributy SuccessFactors

Následující tabulka zachycuje seznam atributů SuccessFactors podporovaných následujícími dvěma zřizovacími aplikacemi: 
* [SuccessFactors se zřizování uživatelů služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors se zřizování uživatelů Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | SuccessFactors – entita                  | SuccessFactors – atribut     | Typ operace |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Číst           |
| 2  | PerPerson                              | personId                     | Číst           |
| 3  | PerPerson                              | perPersonUuid                | Číst           |
| 4  | PerPersonal                            | displayName                  | Číst           |
| 5  | PerPersonal                            | firstName                    | Číst           |
| 6  | PerPersonal                            | gender                       | Číst           |
| 7  | PerPersonal                            | lastName                     | Číst           |
| 8  | PerPersonal                            | middleName                   | Číst           |
| 9  | PerPersonal                            | preferované                | Číst           |
| 10 | Uživatel                                   | addressLine1                 | Číst           |
| 11 | Uživatel                                   | addressLine2                 | Číst           |
| 12 | Uživatel                                   | addressLIne3                 | Číst           |
| 13 | Uživatel                                   | businessPhone                | Číst           |
| 14 | Uživatel                                   | cellPhone                    | Číst           |
| 15 | Uživatel                                   | city                         | Číst           |
| 16 | Uživatel                                   | krajin                      | Číst           |
| 17 | Uživatel                                   | custom01                     | Číst           |
| 18 | Uživatel                                   | custom02                     | Číst           |
| 19 | Uživatel                                   | custom03                     | Číst           |
| 20 | Uživatel                                   | custom04                     | Číst           |
| 21 | Uživatel                                   | custom05                     | Číst           |
| 22 | Uživatel                                   | custom06                     | Číst           |
| 23 | Uživatel                                   | custom07                     | Číst           |
| 24 | Uživatel                                   | custom08                     | Číst           |
| 25 | Uživatel                                   | custom09                     | Číst           |
| 26 | Uživatel                                   | vlastní10                     | Číst           |
| 27 | Uživatel                                   | custom11                     | Číst           |
| 28 | Uživatel                                   | custom12                     | Číst           |
| 29 | Uživatel                                   | custom13                     | Číst           |
| 30 | Uživatel                                   | custom14                     | Číst           |
| 31 | Uživatel                                   | empId                        | Číst           |
| 32 | Uživatel                                   | Telefon domů                    | Číst           |
| 33 | Uživatel                                   | jobFamily                    | Číst           |
| 34 | Uživatel                                   | zdívek                     | Číst           |
| 35 | Uživatel                                   | state                        | Číst           |
| 36 | Uživatel                                   | timeZone                     | Číst           |
| 37 | Uživatel                                   | username jméno                     | Číst           |
| 38 | Uživatel                                   | PSČ                      | Číst           |
| 39 | PerPhone                               | areaCode                     | Číst           |
| 40 | PerPhone                               | countryCode                  | Číst           |
| 41 | PerPhone                               | klapk                    | Číst           |
| 42 | PerPhone                               | phoneNumber                  | Číst           |
| 43 | PerPhone                               | phoneType                    | Číst           |
| 44 | PerEmail                               | emailAddress                 | Čtení, zápis    |
| 45 | PerEmail                               | emailType                    | Číst           |
| 46 | EmpEmployment                          | firstDateWorked              | Číst           |
| 47 | EmpEmployment                          | lastDateWorked               | Číst           |
| 48 | EmpEmployment                          | userId                       | Číst           |
| 49 | EmpEmployment                          | isContingentWorker           | Číst           |
| 50 | EmpJob                                 | countryOfCompany             | Číst           |
| 51 | EmpJob                                 | emplStatus                   | Číst           |
| 52 | EmpJob                                 | endDate                      | Číst           |
| 53 | EmpJob                                 | startDate                    | Číst           |
| 54 | EmpJob                                 | pracovní funkce                     | Číst           |
| 55 | EmpJob                                 | umístění                     | Číst           |
| 65 | EmpJob                                 | customString13               | Číst           |
| 56 | EmpJob                                 | managerId                    | Číst           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Číst           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Číst           |
| 59 | EmpJob\.společnosti                        | Společnosti                      | Číst           |
| 60 | EmpJob\.společnosti                        | companyId                    | Číst           |
| 61 | EmpJob\.společnosti\.CountryOfRegistration | twoCharCountryCode           | Číst           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Číst           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Číst           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Číst           |
| 65 | EmpJob\.oddělení                     | Oddělení                   | Číst           |
| 66 | EmpJob\.oddělení                     | departmentId                 | Číst           |
| 67 | EmpJob\.divize                       | dělení                     | Číst           |
| 68 | EmpJob\.divize                       | divisionId                   | Číst           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Číst           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Číst           |
| 71 | EmpJob\.umístění                       | Umístění                 | Číst           |
| 72 | EmpJob\.umístění                       | officeLocationAddress        | Číst           |
| 73 | EmpJob\.umístění                       | officeLocationCity           | Číst           |
| 74 | EmpJob\.umístění                       | officeLocationCustomString4  | Číst           |
| 75 | EmpJob\.umístění                       | officeLocationZipCode        | Číst           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Číst           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Číst           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Číst           |


## <a name="default-attribute-mapping"></a>Výchozí mapování atributů

Následující tabulka uvádí výchozí mapování atributů mezi SuccessFactors atributy uvedenými výše a atributy AD/Azure AD. V okně mapování aplikace zřizování Azure AD můžete toto výchozí mapování upravit tak, aby obsahovalo atributy ze seznamu výše. 

| \# | SuccessFactors – entita                  | SuccessFactors – atribut | Výchozí mapování atributů AD nebo Azure AD   | Zpracování přeznačení                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Použito jako shodný atribut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[není namapovaný \- použit jako zdrojový kotva\] | Při počáteční synchronizaci propojuje služba zřizování personUuid existující objectGuid\..  |
| 3  | PerPersonal                            | displayName              | displayName                             | Není k dispozici                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | Není k dispozici                                                                                           |
| 5  | PerPersonal                            | lastName                 | sériové číslo                                      | Není k dispozici                                                                                           |
| 6  | Uživatel                                   | addressLine1             | streetAddress                           | Není k dispozici                                                                                           |
| 7  | Uživatel                                   | city                     | l                                       | Není k dispozici                                                                                           |
| 8  | Uživatel                                   | krajin                  | co                                      | Není k dispozici                                                                                           |
| 9  | Uživatel                                   | state                    | St                                      | Není k dispozici                                                                                           |
| 10 | Uživatel                                   | username jméno                 | samAccountName                          | Není k dispozici                                                                                           |
| 11 | Uživatel                                   | PSČ                  | PSČ                              | Není k dispozici                                                                                           |
| 12 | PerEmail                               | emailAddress             | pošta                                    | Není k dispozici                                                                                           |
| 13 | EmpJob                                 | pracovní funkce                 | Název                                   | Není k dispozici                                                                                           |
| 14 | EmpJob                                 | managerId                | Správce                                 | Není k dispozici                                                                                           |
| 15 | EmpJob\.společnosti\.CountryOfRegistration | twoCharCountryCode       | c                                       | Není k dispozici                                                                                           |
| 16 | EmpJob\.oddělení                     | Oddělení               | Oddělení                              | Není k dispozici                                                                                           |
| 17 | EmpJob\.divize                       | dělení                 | Společnosti                                 | Není k dispozici                                                                                           |
| 18 | EmpJob\.umístění                       | officeLocationAddress    | streetAddress                           | Není k dispozici                                                                                           |
| 19 | EmpJob\.umístění                       | officeLocationZipCode    | PSČ                              | Není k dispozici                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Pokud activeEmploymentsCount = 0, zakažte Account\..                                           |

