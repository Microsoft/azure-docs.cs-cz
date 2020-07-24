---
title: Referenční informace o integraci Azure Active Directory a SAP SuccessFactors
description: Technický hluboký podrobně do zřizování SAP SuccessFactors – řízení na základě lidských zdrojů
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 3c1d0d05554fafb4b18d8dc7043cca3e8479b35e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097049"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Jak se Azure Active Directory zřizování integruje s SAP SuccessFactors 

[Azure Active Directory služba zřizování uživatelů](../app-provisioning/user-provisioning.md) integruje s [SAP SuccessFactors zaměstnanci – střed](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) pro správu životního cyklu identit uživatelů. Azure Active Directory nabízí tři předem připravené integrace: 

* SuccessFactors se na místní zřizování uživatelů Active Directory
* SuccessFactors pro Azure Active Directory zřizování uživatelů
* Zpětný zápis SuccessFactors

Tento článek vysvětluje, jak integrace funguje a jak můžete přizpůsobit chování zřizování pro různé scénáře týkající se lidských zdrojů. 

## <a name="establishing-connectivity"></a>Navazování připojení 
Modul zřizování Azure AD používá základní ověřování pro připojení k koncovým bodům rozhraní OData API pro zaměstnance. Při nastavování aplikace pro zřizování SuccessFactors použijte parametr *adresy URL klienta* v části *přihlašovací údaje správce* pro konfiguraci [adresy URL datového centra rozhraní API](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

K dalšímu zabezpečení připojení mezi službou zřizování Azure AD a SuccessFactors můžete přidat rozsahy IP adres Azure AD v seznamu povolených IP adres SuccessFactors pomocí kroků popsaných níže:

* Stáhněte si [nejnovější rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=56519) pro veřejný cloud Azure. 
* Otevřete soubor a vyhledejte značky **azureactivedirectory selhala** a **AzureActiveDirectoryDomainServices** . 

  >[!div class="mx-imgBorder"] 
  >![Rozsah IP adres Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

* Zkopírujte všechny rozsahy IP adres uvedené v rámci elementu *addressPrefixes* a použijte rozsah k sestavení seznamu omezení IP adres.
* Přeloží hodnoty CIDR na rozsahy IP adres.  
* Přihlaste se k portálu pro správu SuccessFactors a přidejte rozsahy IP adres do seznamu povolených adres. Podívejte se na [podporu SAP poznámky 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). V tomto nástroji teď můžete [zadat rozsahy IP adres](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) . 

## <a name="supported-entities"></a>Podporované entity
Služba zřizování Azure AD pro každého uživatele v SuccessFactors načítá následující entity. Každá entita se rozbalí pomocí parametru dotazu OData API *$expand* . Přečtěte si níže uvedený sloupec *pravidla načítání* . Některé entity se ve výchozím nastavení rozšiřují, zatímco některé entity se rozšiřují jenom v případě, že je v mapování přítomen konkrétní atribut. 

| \# | SuccessFactors – entita                  | Uzel OData     | Pravidlo načtení |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *kořenový uzel*                  | Vždy           |
| 2  | PerPersonal                            | personalInfoNav              | Vždy           |
| 3  | PerPhone                               | phoneNav                     | Vždy           |
| 4  | PerEmail                               | emailNav                     | Vždy           |
| 5  | EmpEmployment                          | employmentNav                | Vždy           |
| 6  | Uživatel                                   | employmentNav/userNav        | Vždy           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Vždy           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Vždy           |
| 9  | FOCompany                              | employmentNav/jobInfoNav/companyNav | Pouze v případě, že je namapován atribut Company nebo companyId |
| 10 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Pouze pokud je mapována vlastnost oddělení nebo departmentId |
| 11 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Pouze v případě, že je mapován atribut businessUnit nebo businessUnitId |
| 12 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Pouze v případě, že je mapován atribut costCenter nebo costCenterId |
| 13 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Pouze v případě, že je mapován atribut dělení nebo divisionId |
| 14 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Pouze v případě, že je mapován atribut jobCode nebo jobCodeId |
| 15 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Pouze v případě, že je namapován atribut payGrade |
| 16 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Pouze v případě, že je mapován atribut Location |
| 17 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Pokud mapování obsahuje jeden z následujících atributů: officeLocationAddress, officeLocationCity, officeLocationZipCode |
| 18 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Pouze v případě, že je namapován atribut eventReason |
| 19 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Pouze v případě, že je namapován assignmentType |
| 20 | Rozevírací seznam EmploymentType                | employmentNav/jobInfoNav/employmentTypeNav | Pouze v případě, že je namapován employmentType |
| 21 | Rozevírací seznam EmployeeClass                 | employmentNav/jobInfoNav/employeeClassNav | Pouze v případě, že je namapován employeeClass |
| 22 | Rozevírací seznam EmplStatus                    | employmentNav/jobInfoNav/emplStatusNav | Pouze v případě, že je namapován emplStatus |
| 23 | Rozevírací seznam AssignmentType                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Pouze v případě, že je namapován assignmentType |

## <a name="how-full-sync-works"></a>Jak funguje Úplná synchronizace
V závislosti na mapování atributů při úplné synchronizaci služby Azure AD Provisioning pošle následující dotaz "GET" rozhraní OData API, který načte efektivní data všech aktivních uživatelů. 

> [!div class="mx-tdCol2BreakAll"]
>| Parametr | Popis |
>| ----------|-------------|
>| Hostitel rozhraní OData API | Připojí HTTPS k *adrese URL tenanta*. Příklad: `https://api4.successfactors.com` |
>| Koncový bod rozhraní OData API | `/odata/v2/PerPerson` |
>| Parametr dotazu $format OData | `json` |
>| Parametr dotazu $filter OData | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| Parametr dotazu $expand OData | Tato hodnota parametru závisí na namapovaných atributech. Příklad: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| Parametr dotazu customPageSize OData | `100` |

> [!NOTE]
> Při první počáteční úplné synchronizaci služba Azure AD Provisioning nenačte neaktivní/ukončená pracovní data.

Pro každého uživatele SuccessFactors služba zřizování vyhledá účet v cíli (Azure AD/místní Active Directory) pomocí odpovídajícího atributu definovaného v mapování. Například: Pokud se *personIdExternal* mapuje na *ČísloZaměstnance* a je nastaven jako atribut pro spárování, pak služba zřizování používá hodnotu *personIdExternal* pro hledání uživatele s filtrem *ČísloZaměstnance* . Pokud se najde shoda se zadaným uživatelem, aktualizují se cílové atributy. Pokud se nenajde žádná shoda, vytvoří se nová položka v cíli. 

Pokud chcete ověřit data vrácená vaším koncovým bodem rozhraní OData API pro konkrétní *personIdExternal*, aktualizujte *SuccessFactorsAPIEndpoint* v dotazu rozhraní API níže pomocí adresy URL vašeho serveru datového centra API a použijte k vyvolání dotazu nástroj, jako je například [Poster](https://www.postman.com/downloads/) . 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Jak funguje přírůstková synchronizace

Po úplné synchronizaci služba zřizování Azure AD udržuje *LastExecutionTimestamp* a použije ji k vytvoření rozdílových dotazů pro načítání přírůstkových změn. Atributy časového razítka přítomné v každé entitě SuccessFactors, jako je *lastModifiedDateTime*, *StartDate*, *EndDate*a *latestTerminationDate*, jsou vyhodnocovány, aby bylo možné zjistit, zda změna spadá mezi *LastExecutionTimestamp* a *CurrentExecutionTime*. Pokud ano, změna položky se považuje za efektivní a zpracuje se pro synchronizaci. 

## <a name="reading-attribute-data"></a>Čtení dat atributu

Když se služba zřizování Azure AD dotazuje SuccessFactors, načte sadu výsledků JSON. Sada výsledků JSON obsahuje několik atributů uložených v centru zaměstnanců. Ve výchozím nastavení je schéma zřizování nakonfigurováno tak, aby načetlo pouze podmnožinu těchto atributů. 

Pokud chcete načíst další atributy, postupujte podle níže uvedených kroků:
    
* Přejděte k **podnikovým aplikacím**  ->  **SuccessFactors App**  ->  **Provisioning**  ->  **Upravit**  ->  **mapování atributů**zřizování.
* Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
* Klikněte na **Upravit seznam atributů pro SuccessFactors**. 

> [!NOTE] 
> Pokud se v Azure Portal nezobrazuje možnost **Upravit seznam atributů pro SuccessFactors** , použijte adresu URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* pro přístup ke stránce. 

* Sloupec **výrazu rozhraní API** v tomto zobrazení zobrazuje výrazy JSONPath používané konektorem.
  >[!div class="mx-imgBorder"] 
  >![Rozhraní API – výraz](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  
* Můžete buď upravit existující hodnotu JSONPath, nebo přidat nový atribut s platným výrazem JSONPath do schématu. 

V další části najdete seznam běžných scénářů pro úpravu hodnot JSONPath. 

## <a name="handling-different-hr-scenarios"></a>Zpracování různých scénářů pro personální oddělení

JSONPath je dotazovací jazyk pro JSON, který se podobá XPath pro XML. Podobně jako XPath JSONPath umožňuje extrakci a filtraci dat mimo datovou část JSON.
Pomocí transformace JSONPath můžete přizpůsobit chování aplikace zřizování služby Azure AD, aby se načetly vlastní atributy a zpracovávala scénáře, jako je například zařazení, převod pracovního procesu a globální přiřazení. 

V této části se dozvíte, jak můžete aplikaci pro zřizování přizpůsobit pro následující scénáře týkající se lidských zdrojů: 
* [Načítání dalších atributů](#retrieving-additional-attributes)
* [Načítání vlastních atributů](#retrieving-custom-attributes)
* [Scénář zpracování převodu pracovního procesu](#handling-worker-conversion-scenario)
* [Zpracování scénáře pro zařazení](#handling-rehire-scenario)
* [Scénář zpracování globálních přiřazení](#handling-global-assignment-scenario)
* [Scénář zpracování souběžných úloh](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Načítání dalších atributů

Výchozí schéma aplikace Azure AD SuccessFactors zřizování se dodává s [90 + předem definovanými atributy](sap-successfactors-attribute-reference.md). Pokud chcete přidat další připravené atributy SuccessFactors do schématu zřizování, použijte následující postup: 

* Pomocí následujícího dotazu OData načtěte data pro platného testovacího uživatele od středu zaměstnance. 

```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

* Určení centrální entity zaměstnance přidružené k atributu
  * Pokud je atribut součástí entity *EmpEmployment* , vyhledejte atribut pod uzlem *employmentNav* . 
  * Pokud je atribut součástí entity *uživatele* , vyhledejte atribut v uzlu *employmentNav/userNav* .
  * Pokud je atribut součástí entity *EmpJob* , vyhledejte atribut pod uzlem *employmentNav/jobInfoNav* . 
* Vytvořte cestu JSON přidruženou k atributu a přidejte tento nový atribut do seznamu atributů SuccessFactors. 
  * Příklad 1: řekněme, že chcete přidat atribut *okToRehire*, který je součástí entity *employmentNav* , pak použijte JSONPath`$.employmentNav.results[0].okToRehire`
  * Příklad 2: řekněme, že chcete přidat *časové pásmo*atributu, který je součástí entity *userNav* , pak použijte JSONPath.`$.employmentNav.results[0].userNav.timeZone`
  * Příklad 3: řekněme, že chcete přidat atribut *flsaStatus*, který je součástí entity *jobInfoNav* , pak použijte JSONPath`$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
* Uložte schéma. 
* Restartujte zřizování.

### <a name="retrieving-custom-attributes"></a>Načítání vlastních atributů

Ve výchozím nastavení jsou následující vlastní atributy předem definované v aplikaci Azure AD SuccessFactors Provisioning: 
* *custom01-Custom15* od entity User (userNav)
* *customString1-customString15* z entity EmpEmployment (employmentNav) s názvem *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* z entity EmpJobInfo (jobInfoNav) s názvem *empJobNavCustomString1-empNavJobCustomString15*

Řekněme, že v centrální instanci zaměstnance *customString35* atribut v *EmpJobInfo* ukládá popis umístění. Tuto hodnotu chcete flowovat do atributu Active Directory *physicalDeliveryOfficeName* . Chcete-li nakonfigurovat mapování atributů pro tento scénář, použijte následující postup: 

* Upravte seznam atributů SuccessFactors a přidejte nový atribut s názvem *empJobNavCustomString35*.
* Nastavte výraz rozhraní API JSONPath pro tento atribut jako:`$.employmentNav.results[0].jobInfoNav.results[0].customString35`
* Uložte a znovu načtěte změnu mapování v Azure Portal.  
* V okně mapování atributů namapujte *empJobNavCustomString35* na *physicalDeliveryOfficeName*.
* Uložte mapování.

Rozšíření tohoto scénáře: 
* Pokud chcete mapovat atribut *custom35* z entity *uživatele* , použijte JSONPath.`$.employmentNav.results[0].userNav.custom35`
* Pokud chcete mapovat atribut *customString35* z entity *EmpEmployment* , použijte JSONPath`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Scénář zpracování převodu pracovního procesu

Převod pracovního procesu je proces převodu stávajícího zaměstnance v plném čase na smluvní stranu nebo naopak. V tomto scénáři pracovník pro zaměstnance přidá novou entitu *EmpEmployment* spolu s novou entitou *uživatele* pro stejnou entitu *Person* . Entita *uživatele* vnořená v předchozí entitě *EmpEmployment* je nastavená na hodnotu null. Chcete-li tento scénář zpracovat tak, aby se nová data o zaměstnání zobrazovala při převodu, můžete schéma zřizování aplikace hromadně aktualizovat pomocí níže uvedených kroků:  

* Otevřete okno mapování atributů vaší aplikace pro zřizování SuccessFactors. 
* Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
* Kliknutím na odkaz **Projděte si schéma tady** a otevřete editor schémat. 
  >![Kontrola – schéma](media/sap-successfactors-integration-reference/review-schema.png#lightbox)
* Před úpravou uložte kopii schématu kliknutím na odkaz ke **stažení** . 
  >![Stáhnout – schéma](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
* V editoru schémat stiskněte klávesovou zkratku CTRL-H a otevřete ovládací prvek najít-nahradit.
* Do textového pole najít zkopírujte a vložte hodnotu.`$.employmentNav.results[0]`
* Do textového pole nahradit zkopírujte a vložte hodnotu `$.employmentNav.results[?(@.userNav != null)]` . Všimněte si mezer kolem `!=` operátoru, který je důležitý pro úspěšné zpracování výrazu JSONPath. 
  >![Find-nahrazování a převod](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
* Pro aktualizaci schématu klikněte na možnost Nahradit vše. 
* Uložte schéma. 
* Výše uvedený proces aktualizuje všechny výrazy JSONPath následujícím způsobem: 
  * Původní JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Nové JSONPath:`$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
* Restartujte zřizování. 

### <a name="handling-rehire-scenario"></a>Zpracování scénáře pro zařazení

K dispozici jsou obvykle dvě možnosti pro zpracování renáborů: 
* Možnost 1: vytvoření nového profilu osoby v centru zaměstnanců
* Možnost 2: opětovné použití existujícího profilu osoby v centru zaměstnanců

Pokud váš personální proces používá možnost 1, nevyžadují se pro zřizovací schéma žádné změny. Pokud váš personální proces používá možnost 2, pak zaměstnanec Central přidá novou entitu *EmpEmployment* spolu s novou entitou *uživatele* pro stejnou entitu *Person* . Na rozdíl od scénáře převodu si předchozí entita *EmpEmployment* zachová entitu *uživatele* a ta není nastavená na hodnotu null. 

Aby bylo možné tento scénář opětovného navýšení zpracovat (možnost 2), aby se nejnovější data o zaměstnání zobrazovala pro profily opětovného zařazení, můžete schéma zřizování aplikace hromadně aktualizovat pomocí níže uvedených kroků:  

* Otevřete okno mapování atributů vaší aplikace pro zřizování SuccessFactors. 
* Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
* Kliknutím na odkaz **Projděte si schéma tady** a otevřete editor schémat.   
* Před úpravou uložte kopii schématu kliknutím na odkaz ke **stažení** .   
* V editoru schémat stiskněte klávesovou zkratku CTRL-H a otevřete ovládací prvek najít-nahradit.
* Do textového pole najít zkopírujte a vložte hodnotu.`$.employmentNav.results[0]`
* Do textového pole nahradit zkopírujte a vložte hodnotu `$.employmentNav.results[-1:]` . Tento výraz JSONPath vrací nejnovější záznam *EmpEmployment* .   
* Pro aktualizaci schématu klikněte na možnost Nahradit vše. 
* Uložte schéma. 
* Výše uvedený proces aktualizuje všechny výrazy JSONPath následujícím způsobem: 
  * Původní JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Nové JSONPath:`$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
* Restartujte zřizování. 

### <a name="handling-global-assignment-scenario"></a>Scénář zpracování globálních přiřazení

Když se pro globální přiřazení zpracuje uživatel v centru zaměstnanců, SuccessFactors přidá novou entitu *EmpEmployment* a nastaví *assignmentClass* na GA. Zároveň vytvoří novou entitu *uživatele* . Proto má uživatel teď:
* Jedna *EmpEmployment*  +  *uživatelská* entita, která odpovídá přiřazení domů s *assignmentClass* nastavenou na "St" a 
* Další *EmpEmployment*  +  entita*uživatele* EmpEmployment, která odpovídá globálnímu přiřazení s *assignmentClass* nastavenou na "GA"

Chcete-li načíst atributy patřící do uživatelského profilu standardního přiřazení a globálního přiřazení, použijte následující postup: 

* Otevřete okno mapování atributů vaší aplikace pro zřizování SuccessFactors. 
* Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
* Kliknutím na odkaz **Projděte si schéma tady** a otevřete editor schémat.   
* Před úpravou uložte kopii schématu kliknutím na odkaz ke **stažení** .   
* V editoru schémat stiskněte klávesovou zkratku CTRL-H a otevřete ovládací prvek najít-nahradit.
* Do textového pole najít zkopírujte a vložte hodnotu.`$.employmentNav.results[0]`
* Do textového pole nahradit zkopírujte a vložte hodnotu `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
* Pro aktualizaci schématu klikněte na možnost Nahradit vše. 
* Uložte schéma. 
* Výše uvedený proces aktualizuje všechny výrazy JSONPath následujícím způsobem: 
  * Původní JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Nové JSONPath:`$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
* Znovu načtěte okno mapování atributů aplikace. 
* Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
* Klikněte na **Upravit seznam atributů pro SuccessFactors**.
* Přidejte nové atributy pro načtení globálních dat přiřazení. Příklad: Pokud chcete načíst název oddělení přidruženého k globálnímu profilu přiřazení, můžete přidat atribut **globalAssignmentDepartment** s nastavením výrazu JSONPath na `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
* Nyní můžete buď přesměrovat hodnoty obou oddělení na atributy služby Active Directory, nebo selektivně flowovat hodnotu pomocí mapování výrazů. Příklad: následující výraz nastaví hodnotu atributu AD *Department* na *globalAssignmentDepartment* , pokud je přítomen, jinak nastaví hodnotu na *oddělení* přidružené ke standardnímu přiřazení. 
  * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`
* Uložte mapování. 
* Restartujte zřizování. 

### <a name="handling-concurrent-jobs-scenario"></a>Scénář zpracování souběžných úloh

Pokud má uživatel v centru zaměstnanců v souběžných nebo více úlohách, existují dvě *EmpEmployment* a *uživatelské* entity s *assignmentClass* nastavenou na hodnotu "St". Chcete-li načíst atributy patřící do obou úloh, použijte postup uvedený níže: 

* Otevřete okno mapování atributů vaší aplikace pro zřizování SuccessFactors. 
* Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
* Klikněte na **Upravit seznam atributů pro SuccessFactors**.
* Řekněme, že chcete získat oddělení přidružené k úloze 1 a úloze 2. Předem definované *oddělení* atributů již načítá hodnotu oddělení pro první úlohu. Můžete definovat nový atribut s názvem *secondJobDepartment* a nastavit výraz JSONPath na`$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
* Nyní můžete buď přesměrovat hodnoty obou oddělení na atributy služby Active Directory, nebo selektivně flowovat hodnotu pomocí mapování výrazů. 
* Uložte mapování. 
* Restartujte zřizování. 

## <a name="next-steps"></a>Další kroky

* [Informace o tom, jak nakonfigurovat SuccessFactors na zřizování služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Informace o tom, jak nakonfigurovat zpětný zápis na SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Další informace o podporovaných atributech SuccessFactors pro příchozí zřizování](sap-successfactors-attribute-reference.md)










