---
title: Reference k atributu SAP SuccessFactors
description: Informace o tom, které atributy z SuccessFactors jsou podporované SuccessFactors zřizováním na základě lidských zdrojů
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 12/06/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: 25541b76dda55db1ec26f4d8e3ec63573a47e7b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781544"
---
# <a name="sap-successfactors-attribute-reference"></a>Reference k atributu SAP SuccessFactors

V tomto článku najdete informace o:

- [Podporované entity a atributy SuccessFactors](#supported-successfactors-entities-and-attributes)
- [Výchozí mapování atributů](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Podporované entity a atributy SuccessFactors

Následující tabulka zachycuje seznam atributů SuccessFactors podporovaných následujícími dvěma zřizovacími aplikacemi:

- [SuccessFactors se zřizování uživatelů služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors se zřizování uživatelů Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)


| \# | SuccessFactors – entita                  | SuccessFactors – atribut     | Typ operace |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Čtení           |
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
| 16 | Uživatel                                   | country                      | Čtení           |
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
| 32 | Uživatel                                   | homePhone                    | Čtení           |
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
| 54 | EmpJob                                 | jobTitle                     | Čtení           |
| 55 | EmpJob                                 | position                     | Čtení           |
| 65 | EmpJob                                 | customString13               | Čtení           |
| 56 | EmpJob                                 | managerId                    | Čtení           |
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | Čtení           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | Čtení           |
| 59 | \.Společnost EmpJob                        | company                      | Čtení           |
| 60 | \.Společnost EmpJob                        | companyId                    | Čtení           |
| 61 | \.CountryOfRegistration společnosti \. EmpJob | twoCharCountryCode           | Čtení           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Čtení           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Čtení           |
| 64 | EmpJob \. CostCenter                     | costCenterDescription        | Čtení           |
| 65 | EmpJob \. oddělení                     | Oddělení                   | Čtení           |
| 66 | EmpJob \. oddělení                     | departmentId                 | Čtení           |
| 67 | EmpJob \. rozdělení                       | dělení                     | Čtení           |
| 68 | EmpJob \. rozdělení                       | divisionId                   | Čtení           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Čtení           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Čtení           |
| 71 | \.Umístění EmpJob                       | Umístění                 | Čtení           |
| 72 | \.Umístění EmpJob                       | officeLocationAddress        | Čtení           |
| 73 | \.Umístění EmpJob                       | officeLocationCity           | Čtení           |
| 74 | \.Umístění EmpJob                       | officeLocationCustomString4  | Čtení           |
| 75 | \.Umístění EmpJob                       | officeLocationZipCode        | Čtení           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Čtení           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Čtení           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Čtení           |

## <a name="default-attribute-mapping"></a>Výchozí mapování atributů

Následující tabulka uvádí výchozí mapování atributů mezi SuccessFactors atributy uvedenými výše a atributy AD/Azure AD. V okně mapování aplikace zřizování Azure AD můžete toto výchozí mapování upravit tak, aby obsahovalo atributy ze seznamu výše. 

| \# | SuccessFactors – entita                  | SuccessFactors – atribut | Výchozí mapování atributů AD nebo Azure AD   | Zpracování přeznačení                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | Zaměstnance                              | Použito jako shodný atribut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Nenamapováno se \- používá jako zdrojové ukotvení.\] | Při počáteční synchronizaci propojuje služba zřizování personUuid existující objectGuid\..  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | Uživatel                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | Uživatel                                   | city                     | l                                       | NA                                                                                           |
| 8  | Uživatel                                   | country                  | co                                      | NA                                                                                           |
| 9  | Uživatel                                   | state                    | st                                      | NA                                                                                           |
| 10 | Uživatel                                   | uživatelské jméno                 | samAccountName                          | NA                                                                                           |
| 11 | Uživatel                                   | PSČ                  | Ovládacím                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | pošta                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | \.CountryOfRegistration společnosti \. EmpJob | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob \. oddělení                     | Oddělení               | Oddělení                              | NA                                                                                           |
| 17 | EmpJob \. rozdělení                       | dělení                 | company                                 | NA                                                                                           |
| 18 | \.Umístění EmpJob                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | \.Umístění EmpJob                       | officeLocationZipCode    | Ovládacím                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Pokud activeEmploymentsCount = 0, zakažte Account\..                                           |
