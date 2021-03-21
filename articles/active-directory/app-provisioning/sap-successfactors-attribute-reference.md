---
title: Reference k atributu SAP SuccessFactors
description: Informace o tom, které atributy z SuccessFactors jsou podporované SuccessFactors zřizováním na základě lidských zdrojů
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: 0d1a25f67d3550e79b2339ee060d37acaea0c1c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255538"
---
# <a name="sap-successfactors-attribute-reference"></a>Reference k atributu SAP SuccessFactors

V tomto článku najdete informace o:

- [SuccessFactors entit a atributů](#supported-successfactors-entities-and-attributes)
- [Výchozí mapování atributů](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Podporované entity a atributy SuccessFactors

Následující tabulka zachycuje seznam atributů SuccessFactors, které jsou ve výchozím nastavení zahrnuty, v následujících dvou zřizovacích aplikacích:

- [SuccessFactors se zřizování uživatelů služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors se zřizování uživatelů Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

Další atributy najdete v [referenčních informacích k integraci SAP SuccessFactors](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) . 

| \# | SuccessFactors – entita                  | SuccessFactors – atribut     | Typ operace |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Číst           |
| 2  | PerPerson                              | personId                     | Číst           |
| 3  | PerPerson                              | perPersonUuid                | Číst           |
| 4  | PerPersonal                            | displayName                  | Číst           |
| 5  | PerPersonal                            | firstName                    | Číst           |
| 6  | PerPersonal                            | gender (pohlaví)                       | Číst           |
| 7  | PerPersonal                            | lastName                     | Číst           |
| 8  | PerPersonal                            | middleName                   | Číst           |
| 9  | PerPersonal                            | preferované                | Číst           |
| 10 | User                                   | addressLine1                 | Číst           |
| 11 | User                                   | addressLine2                 | Číst           |
| 12 | User                                   | addressLIne3                 | Číst           |
| 13 | User                                   | businessPhone                | Číst           |
| 14 | User                                   | cellPhone                    | Číst           |
| 15 | User                                   | city                         | Číst           |
| 16 | User                                   | country                      | Číst           |
| 17 | User                                   | custom01                     | Číst           |
| 18 | User                                   | custom02                     | Číst           |
| 19 | User                                   | custom03                     | Číst           |
| 20 | User                                   | custom04                     | Číst           |
| 21 | User                                   | custom05                     | Číst           |
| 22 | User                                   | custom06                     | Číst           |
| 23 | User                                   | custom07                     | Číst           |
| 24 | User                                   | custom08                     | Číst           |
| 25 | User                                   | custom09                     | Číst           |
| 26 | User                                   | vlastní10                     | Číst           |
| 27 | User                                   | custom11                     | Číst           |
| 28 | User                                   | custom12                     | Číst           |
| 29 | User                                   | custom13                     | Číst           |
| 30 | User                                   | custom14                     | Číst           |
| 31 | User                                   | empId                        | Číst           |
| 32 | User                                   | homePhone                    | Číst           |
| 33 | User                                   | jobFamily                    | Číst           |
| 34 | User                                   | zdívek                     | Číst           |
| 35 | User                                   | state                        | Číst           |
| 36 | User                                   | timeZone                     | Číst           |
| 37 | User                                   | username                     | Číst           |
| 38 | User                                   | PSČ                      | Číst           |
| 39 | PerPhone                               | areaCode                     | Číst           |
| 40 | PerPhone                               | countryCode                  | Číst           |
| 41 | PerPhone                               | přípona                    | Číst           |
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
| 54 | EmpJob                                 | jobTitle                     | Číst           |
| 55 | EmpJob                                 | position                     | Číst           |
| 65 | EmpJob                                 | customString13               | Číst           |
| 56 | EmpJob                                 | managerId                    | Číst           |
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | Číst           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | Číst           |
| 59 | \.Společnost EmpJob                        | company                      | Číst           |
| 60 | \.Společnost EmpJob                        | companyId                    | Číst           |
| 61 | \.CountryOfRegistration společnosti \. EmpJob | twoCharCountryCode           | Číst           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Číst           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Číst           |
| 64 | EmpJob \. CostCenter                     | costCenterDescription        | Číst           |
| 65 | EmpJob \. oddělení                     | Oddělení                   | Číst           |
| 66 | EmpJob \. oddělení                     | departmentId                 | Číst           |
| 67 | EmpJob \. rozdělení                       | dělení                     | Číst           |
| 68 | EmpJob \. rozdělení                       | divisionId                   | Číst           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Číst           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Číst           |
| 71 | \.Umístění EmpJob                       | Umístění                 | Číst           |
| 72 | \.Umístění EmpJob                       | officeLocationAddress        | Číst           |
| 73 | \.Umístění EmpJob                       | officeLocationCity           | Číst           |
| 74 | \.Umístění EmpJob                       | officeLocationCustomString4  | Číst           |
| 75 | \.Umístění EmpJob                       | officeLocationZipCode        | Číst           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Číst           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Číst           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Číst           |

## <a name="default-attribute-mapping"></a>Výchozí mapování atributů

Následující tabulka uvádí výchozí mapování atributů mezi SuccessFactors atributy uvedenými výše a atributy AD/Azure AD. V okně mapování aplikace zřizování Azure AD můžete toto výchozí mapování upravit tak, aby obsahovalo atributy ze seznamu výše. 

| \# | SuccessFactors – entita                  | SuccessFactors – atribut | Výchozí mapování atributů AD nebo Azure AD   | Zpracování přeznačení                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | Zaměstnance                              | Použito jako shodný atribut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Nenamapováno se \- používá jako zdrojové ukotvení.\] | Při počáteční synchronizaci propojuje služba zřizování personUuid existující objectGuid\..  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | User                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | User                                   | city                     | l                                       | NA                                                                                           |
| 8  | User                                   | country                  | co                                      | NA                                                                                           |
| 9  | User                                   | state                    | st                                      | NA                                                                                           |
| 10 | User                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | User                                   | PSČ                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | pošta                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | \.CountryOfRegistration společnosti \. EmpJob | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob \. oddělení                     | Oddělení               | Oddělení                              | NA                                                                                           |
| 17 | EmpJob \. rozdělení                       | dělení                 | company                                 | NA                                                                                           |
| 18 | \.Umístění EmpJob                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | \.Umístění EmpJob                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Pokud activeEmploymentsCount = 0, zakažte Account\..                                           |
