---
title: Reference k atributu SAP SuccessFactors | Microsoft Docs
description: Informace o tom, které atributy z SuccessFactors jsou podporované SuccessFactors zřizováním na základě lidských zdrojů
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 6f7497e62be0036c13d5c33fa82301469df16f26
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971107"
---
# <a name="sap-successfactors-attribute-reference"></a>Reference k atributu SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Podporované entity a atributy SuccessFactors

Následující tabulka zachycuje seznam atributů SuccessFactors podporovaných následujícími dvěma zřizovacími aplikacemi: 
* [SuccessFactors se zřizování uživatelů služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors se zřizování uživatelů Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | SuccessFactors – entita                  | SuccessFactors – atribut     | Typ operace |
|----|----------------------------------------|------------------------------|----------------|
| 1\. místo  | PerPerson                              | personIdExternal             | Čtení           |
| 2  | PerPerson                              | personId                     | Čtení           |
| 3  | PerPerson                              | perPersonUuid                | Čtení           |
| 4  | PerPersonal                            | displayName                  | Čtení           |
| 5  | PerPersonal                            | firstName                    | Čtení           |
| 6  | PerPersonal                            | gender (pohlaví)                       | Čtení           |
| 7  | PerPersonal                            | lastName                     | Čtení           |
| 8  | PerPersonal                            | middleName                   | Čtení           |
| 9  | PerPersonal                            | preferované                | Čtení           |
| 10 | Uživatel                                   | addressLine1                 | Čtení           |
| 11 | Uživatel                                   | addressLine2                 | Čtení           |
| 12 | Uživatel                                   | addressLIne3                 | Čtení           |
| 13 | Uživatel                                   | businessPhone                | Čtení           |
| 14 | Uživatel                                   | cellPhone                    | Čtení           |
| 15 | Uživatel                                   | city                         | Čtení           |
| 16 | Uživatel                                   | země                      | Čtení           |
| 17 | Uživatel                                   | custom01                     | Čtení           |
| 18 | Uživatel                                   | custom02                     | Čtení           |
| 19 | Uživatel                                   | custom03                     | Čtení           |
| 20 | Uživatel                                   | custom04                     | Čtení           |
| 21 | Uživatel                                   | custom05                     | Čtení           |
| 22 | Uživatel                                   | custom06                     | Čtení           |
| 23 | Uživatel                                   | custom07                     | Čtení           |
| 24 | Uživatel                                   | custom08                     | Čtení           |
| 25 | Uživatel                                   | custom09                     | Čtení           |
| 26 | Uživatel                                   | vlastní10                     | Čtení           |
| 27 | Uživatel                                   | custom11                     | Čtení           |
| 28 | Uživatel                                   | custom12                     | Čtení           |
| 29 | Uživatel                                   | custom13                     | Čtení           |
| 30 | Uživatel                                   | custom14                     | Čtení           |
| 31 | Uživatel                                   | empId                        | Čtení           |
| 32 | Uživatel                                   | Telefon domů                    | Čtení           |
| 33 | Uživatel                                   | jobFamily                    | Čtení           |
| 34 | Uživatel                                   | zdívek                     | Čtení           |
| 35 | Uživatel                                   | state                        | Čtení           |
| 36 | Uživatel                                   | timeZone                     | Čtení           |
| 37 | Uživatel                                   | uživatelské jméno                     | Čtení           |
| 38 | Uživatel                                   | PSČ                      | Čtení           |
| 39 | PerPhone                               | areaCode                     | Čtení           |
| 40 | PerPhone                               | countryCode                  | Čtení           |
| 41 | PerPhone                               | přípona                    | Čtení           |
| 42 | PerPhone                               | phoneNumber                  | Čtení           |
| 43 | PerPhone                               | phoneType                    | Čtení           |
| 44 | PerEmail                               | emailAddress                 | Čtení, zápis    |
| 45 | PerEmail                               | emailType                    | Čtení           |
| 46 | EmpEmployment                          | firstDateWorked              | Čtení           |
| 47 | EmpEmployment                          | lastDateWorked               | Čtení           |
| 48 | EmpEmployment                          | userId                       | Čtení           |
| 49 | EmpEmployment                          | isContingentWorker           | Čtení           |
| 50 | EmpJob                                 | countryOfCompany             | Čtení           |
| 51 | EmpJob                                 | emplStatus                   | Čtení           |
| 52 | EmpJob                                 | endDate                      | Čtení           |
| 53 | EmpJob                                 | startDate                    | Čtení           |
| 54 | EmpJob                                 | pracovní funkce                     | Čtení           |
| 55 | EmpJob                                 | pozice                     | Čtení           |
| 65 | EmpJob                                 | customString13               | Čtení           |
| 56 | EmpJob                                 | managerId                    | Čtení           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Čtení           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Čtení           |
| 59 | EmpJob\.společnosti                        | Společnosti                      | Čtení           |
| 60 | EmpJob\.společnosti                        | companyId                    | Čtení           |
| 61 | EmpJob\.společnosti\.CountryOfRegistration | twoCharCountryCode           | Čtení           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Čtení           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Čtení           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Čtení           |
| 65 | EmpJob\.oddělení                     | Oddělení                   | Čtení           |
| 66 | EmpJob\.oddělení                     | departmentId                 | Čtení           |
| 67 | EmpJob\.divize                       | dělení                     | Čtení           |
| 68 | EmpJob\.divize                       | divisionId                   | Čtení           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Čtení           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Čtení           |
| 71 | EmpJob\.umístění                       | Umístění                 | Čtení           |
| 72 | EmpJob\.umístění                       | officeLocationAddress        | Čtení           |
| 73 | EmpJob\.umístění                       | officeLocationCity           | Čtení           |
| 74 | EmpJob\.umístění                       | officeLocationCustomString4  | Čtení           |
| 75 | EmpJob\.umístění                       | officeLocationZipCode        | Čtení           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Čtení           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Čtení           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Čtení           |


## <a name="default-attribute-mapping"></a>Výchozí mapování atributů

Následující tabulka uvádí výchozí mapování atributů mezi SuccessFactors atributy uvedenými výše a atributy AD/Azure AD. V okně mapování aplikace zřizování Azure AD můžete toto výchozí mapování upravit tak, aby obsahovalo atributy ze seznamu výše. 

| \# | SuccessFactors – entita                  | SuccessFactors – atribut | Výchozí mapování atributů AD nebo Azure AD   | Zpracování přeznačení                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1\. místo  | PerPerson                              | personIdExternal         | employeeId                              | Použito jako shodný atribut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[není namapovaný \- použit jako zdrojový kotva\] | Při počáteční synchronizaci propojuje služba zřizování personUuid existující objectGuid\..  |
| 3  | PerPersonal                            | displayName              | displayName                             | není k dispozici                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | není k dispozici                                                                                           |
| 5  | PerPersonal                            | lastName                 | sériové číslo                                      | není k dispozici                                                                                           |
| 6  | Uživatel                                   | addressLine1             | streetAddress                           | není k dispozici                                                                                           |
| 7  | Uživatel                                   | city                     | l                                       | není k dispozici                                                                                           |
| 8  | Uživatel                                   | země                  | co                                      | není k dispozici                                                                                           |
| 9  | Uživatel                                   | state                    | St                                      | není k dispozici                                                                                           |
| 10 | Uživatel                                   | uživatelské jméno                 | samAccountName                          | není k dispozici                                                                                           |
| 11 | Uživatel                                   | PSČ                  | PSČ                              | není k dispozici                                                                                           |
| 12 | PerEmail                               | emailAddress             | e-mailu                                    | není k dispozici                                                                                           |
| 13 | EmpJob                                 | pracovní funkce                 | title                                   | není k dispozici                                                                                           |
| 14 | EmpJob                                 | managerId                | manažer                                 | není k dispozici                                                                                           |
| 15 | EmpJob\.společnosti\.CountryOfRegistration | twoCharCountryCode       | c                                       | není k dispozici                                                                                           |
| 16 | EmpJob\.oddělení                     | Oddělení               | Oddělení                              | není k dispozici                                                                                           |
| 17 | EmpJob\.divize                       | dělení                 | Společnosti                                 | není k dispozici                                                                                           |
| 18 | EmpJob\.umístění                       | officeLocationAddress    | streetAddress                           | není k dispozici                                                                                           |
| 19 | EmpJob\.umístění                       | officeLocationZipCode    | PSČ                              | není k dispozici                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Pokud activeEmploymentsCount = 0, zakažte Account\..                                           |

