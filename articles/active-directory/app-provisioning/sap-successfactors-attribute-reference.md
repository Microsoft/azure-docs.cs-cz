---
title: SAP SuccessFactors Reference atribut | Dokumenty společnosti Microsoft
description: Zjistěte, které atributy z SuccessFactors jsou podporovány SuccessFactors-HR řízené zřizování
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522352"
---
# <a name="sap-successfactors-attribute-reference"></a>Odkaz na atribut SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Podporované entity a atributy SuccessFactors

V následující tabulce je uveden seznam atributů SuccessFactors podporovaných následujícími dvěma zřizovacími aplikacemi: 
* [Faktory úspěchu zřizování uživatelů služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Faktory úspěchu pro zřizování uživatelů Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Entita SuccessFactors                  | Atribut SuccessFactors     | Typ operace |
|----|----------------------------------------|------------------------------|----------------|
| 1  | Na osobu                              | personIdExterní             | Čtení           |
| 2  | Na osobu                              | personId                     | Čtení           |
| 3  | Na osobu                              | perPersonUuid                | Čtení           |
| 4  | PerPersonal                            | displayName                  | Čtení           |
| 5  | PerPersonal                            | firstName                    | Čtení           |
| 6  | PerPersonal                            | gender (pohlaví)                       | Čtení           |
| 7  | PerPersonal                            | lastName                     | Čtení           |
| 8  | PerPersonal                            | Middlename                   | Čtení           |
| 9  | PerPersonal                            | preferredName                | Čtení           |
| 10 | Uživatel                                   | adresaLine1                 | Čtení           |
| 11 | Uživatel                                   | adresaLine2                 | Čtení           |
| 12 | Uživatel                                   | adresaLIne3                 | Čtení           |
| 13 | Uživatel                                   | businessPhone                | Čtení           |
| 14 | Uživatel                                   | Mobil                    | Čtení           |
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
| 27 | Uživatel                                   | vlastní11                     | Čtení           |
| 28 | Uživatel                                   | vlastní12                     | Čtení           |
| 29 | Uživatel                                   | vlastní13                     | Čtení           |
| 30 | Uživatel                                   | vlastní14                     | Čtení           |
| 31 | Uživatel                                   | Empid                        | Čtení           |
| 32 | Uživatel                                   | domácí telefon                    | Čtení           |
| 33 | Uživatel                                   | jobFamily                    | Čtení           |
| 34 | Uživatel                                   | Přezdívka                     | Čtení           |
| 35 | Uživatel                                   | state                        | Čtení           |
| 36 | Uživatel                                   | timeZone                     | Čtení           |
| 37 | Uživatel                                   | uživatelské jméno                     | Čtení           |
| 38 | Uživatel                                   | Psč                      | Čtení           |
| 39 | PerPhone                               | areaCode                     | Čtení           |
| 40 | PerPhone                               | kód země                  | Čtení           |
| 41 | PerPhone                               | přípona                    | Čtení           |
| 42 | PerPhone                               | Phonenumber                  | Čtení           |
| 43 | PerPhone                               | phoneType                    | Čtení           |
| 44 | PerEmail                               | Emailaddress                 | Číst, psát    |
| 45 | PerEmail                               | emailType                    | Čtení           |
| 46 | EmpZaměstnanost                          | firstDateWorked              | Čtení           |
| 47 | EmpZaměstnanost                          | lastDateWorked               | Čtení           |
| 48 | EmpZaměstnanost                          | userId                       | Čtení           |
| 49 | EmpZaměstnanost                          | isContingentWorker           | Čtení           |
| 50 | EmpJob                                 | zeměOfCompany             | Čtení           |
| 51 | EmpJob                                 | emplStatus                   | Čtení           |
| 52 | EmpJob                                 | Enddate                      | Čtení           |
| 53 | EmpJob                                 | Datum_spuštění                    | Čtení           |
| 54 | EmpJob                                 | název úlohy                     | Čtení           |
| 55 | EmpJob                                 | position                     | Čtení           |
| 65 | EmpJob                                 | vlastní String13               | Čtení           |
| 56 | EmpJob                                 | Managerid                    | Čtení           |
| 57 | EmpJob\.BusinessUnit                   | obchodní jednotka                 | Čtení           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Čtení           |
| 59 | Společnost EmpJob\.                        | company                      | Čtení           |
| 60 | Společnost EmpJob\.                        | společnostId                    | Čtení           |
| 61 | EmpJob\.\.společnost ZeměRegistrace | twoCharCountryCode           | Čtení           |
| 62 | Centrum nákladů\.empJob                     | costCenter                   | Čtení           |
| 63 | Centrum nákladů\.empJob                     | costCenterId                 | Čtení           |
| 64 | Centrum nákladů\.empJob                     | costCenterDescription        | Čtení           |
| 65 | EmpJob\.oddělení                     | Oddělení                   | Čtení           |
| 66 | EmpJob\.oddělení                     | departmentId                 | Čtení           |
| 67 | Divize EmpJob\.                       | Divize                     | Čtení           |
| 68 | Divize EmpJob\.                       | divisionId                   | Čtení           |
| 69 | EmpJob\.JobKód                        | jobCode                      | Čtení           |
| 70 | EmpJob\.JobKód                        | jobCodeId                    | Čtení           |
| 71 | Umístění empjob\.                       | Název_umístění                 | Čtení           |
| 72 | Umístění empjob\.                       | officeLocationAddress        | Čtení           |
| 73 | Umístění empjob\.                       | officeLocationCity           | Čtení           |
| 74 | Umístění empjob\.                       | officeLocationCustomString4  | Čtení           |
| 75 | Umístění empjob\.                       | officeLocationZipCode        | Čtení           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Čtení           |
| 77 | EmpZaměstnanostUkončení               | activeEmploymentsCount       | Čtení           |
| 78 | EmpZaměstnanostUkončení               | datum posledního ukončení        | Čtení           |


## <a name="default-attribute-mapping"></a>Výchozí mapování atributů

Níže uvedená tabulka obsahuje výchozí mapování atributů mezi atributy SuccessFactors uvedenými výše a atributy AD/Azure AD. V okně Zřizování Azure AD aplikace "Mapování" můžete upravit toto výchozí mapování tak, aby zahrnovalo atributy z výše uvedeného seznamu. 

| \# | Entita SuccessFactors                  | Atribut SuccessFactors | Výchozí mapování atributů služby AD/Azure AD   | Zpracování poznámky                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | Na osobu                              | personIdExterní         | Employeeid                              | Používá se jako odpovídající atribut                                                                   |
| 2  | Na osobu                              | perPersonUuid            | \[Není mapováno \- jako zdrojová kotva\] | Během počáteční synchronizace služba zřizování propojí personUid s existujícím objektemGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | Není k dispozici                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | Není k dispozici                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | Není k dispozici                                                                                           |
| 6  | Uživatel                                   | adresaLine1             | Streetaddress                           | Není k dispozici                                                                                           |
| 7  | Uživatel                                   | city                     | l                                       | Není k dispozici                                                                                           |
| 8  | Uživatel                                   | country                  | Co                                      | Není k dispozici                                                                                           |
| 9  | Uživatel                                   | state                    | st                                      | Není k dispozici                                                                                           |
| 10 | Uživatel                                   | uživatelské jméno                 | samAccountName                          | Není k dispozici                                                                                           |
| 11 | Uživatel                                   | Psč                  | Postalcode                              | Není k dispozici                                                                                           |
| 12 | PerEmail                               | Emailaddress             | pošta                                    | Není k dispozici                                                                                           |
| 13 | EmpJob                                 | název úlohy                 | title                                   | Není k dispozici                                                                                           |
| 14 | EmpJob                                 | Managerid                | manager                                 | Není k dispozici                                                                                           |
| 15 | EmpJob\.\.společnost ZeměRegistrace | twoCharCountryCode       | c                                       | Není k dispozici                                                                                           |
| 16 | EmpJob\.oddělení                     | Oddělení               | Oddělení                              | Není k dispozici                                                                                           |
| 17 | Divize EmpJob\.                       | Divize                 | company                                 | Není k dispozici                                                                                           |
| 18 | Umístění empjob\.                       | officeLocationAddress    | Streetaddress                           | Není k dispozici                                                                                           |
| 19 | Umístění empjob\.                       | officeLocationZipCode    | Postalcode                              | Není k dispozici                                                                                           |
| 20 | EmpZaměstnanostUkončení               | activeEmploymentsCount   | účetPovolený                          | pokud activeEmploymentsCount=0, zakažte účet\.                                           |

