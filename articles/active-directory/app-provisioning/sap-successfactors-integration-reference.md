---
title: Referenční informace o integraci Azure Active Directory a SAP SuccessFactors
description: Technický hluboký podrobně do zřizování SAP SuccessFactors – řízení na základě lidských zdrojů
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 805cdc0713afd43502bb224cce60167adbc418ee
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969520"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Jak se Azure Active Directory zřizování integruje s SAP SuccessFactors 

[Azure Active Directory služba zřizování uživatelů](../app-provisioning/user-provisioning.md) integruje s [SAP SuccessFactors zaměstnanci – střed](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) pro správu životního cyklu identit uživatelů. Azure Active Directory nabízí tři předem připravené integrace: 

* [SuccessFactors se na místní zřizování uživatelů Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors pro Azure Active Directory zřizování uživatelů](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [Zpětný zápis SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)

Tento článek vysvětluje, jak integrace funguje a jak můžete přizpůsobit chování zřizování pro různé scénáře týkající se lidských zdrojů. 

## <a name="establishing-connectivity"></a>Navazování připojení 
Služba zřizování Azure AD používá základní ověřování pro připojení k koncovým bodům rozhraní OData API pro zaměstnance. Při nastavování aplikace pro zřizování SuccessFactors použijte parametr *adresy URL klienta* v části *přihlašovací údaje správce* pro konfiguraci [adresy URL datového centra rozhraní API](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

K dalšímu zabezpečení připojení mezi službou zřizování Azure AD a SuccessFactors můžete přidat rozsahy IP adres Azure AD v seznamu povolených IP adres SuccessFactors pomocí kroků popsaných níže:

1. Stáhněte si [nejnovější rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=56519) pro veřejný cloud Azure. 
1. Otevřete soubor a vyhledejte tag **azureactivedirectory selhala** 

   >[!div class="mx-imgBorder"] 
   >![Rozsah IP adres Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Zkopírujte všechny rozsahy IP adres uvedené v rámci elementu *addressPrefixes* a použijte rozsah k sestavení seznamu omezení IP adres.
1. Přeloží hodnoty CIDR na rozsahy IP adres.  
1. Přihlaste se k portálu pro správu SuccessFactors a přidejte rozsahy IP adres do seznamu povolených adres. Podívejte se na [podporu SAP poznámky 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). V tomto nástroji teď můžete [zadat rozsahy IP adres](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) . 

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
| 9  | FOCompany                              | employmentNav/jobInfoNav/companyNav | Pouze v případě `company` , že `companyId` je namapován atribut nebo |
| 10 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Pouze v případě `department` , že `departmentId` je namapován atribut nebo |
| 11 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Pouze v případě `businessUnit` , že `businessUnitId` je namapován atribut nebo |
| 12 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Pouze v případě `costCenter` , že `costCenterId` je namapován atribut nebo |
| 13 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Pouze v případě `division` , že `divisionId` je namapován atribut nebo |
| 14 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Pouze v případě `jobCode` , že `jobCodeId` je namapován atribut nebo |
| 15 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Pouze v případě, že `payGrade` je mapován atribut |
| 16 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Pouze v případě, že `location` je mapován atribut |
| 17 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Pokud mapování obsahuje jeden z následujících atributů: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 18 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Pouze v případě, že `eventReason` je mapován atribut |
| 19 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Pouze v případě, že `assignmentType` je namapován |
| 20 | Rozevírací seznam EmploymentType                | employmentNav/jobInfoNav/employmentTypeNav | Pouze v případě, že `employmentType` je namapován |
| 21 | Rozevírací seznam EmployeeClass                 | employmentNav/jobInfoNav/employeeClassNav | Pouze v případě, že `employeeClass` je namapován |
| 22 | Rozevírací seznam EmplStatus                    | employmentNav/jobInfoNav/emplStatusNav | Pouze v případě, že `emplStatus` je namapován |
| 23 | Rozevírací seznam AssignmentType                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Pouze v případě, že `assignmentType` je namapován |

## <a name="how-full-sync-works"></a>Jak funguje Úplná synchronizace
V závislosti na mapování atributů během úplné synchronizace služby zřizování Azure AD pošle následující dotaz "GET" rozhraní OData API, který načte efektivní data všech aktivních uživatelů. 

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

Chcete-li ověřit data vrácená vaším koncovým bodem rozhraní OData API pro konkrétní `personIdExternal` aktualizaci, aktualizujte `SuccessFactorsAPIEndpoint` v níže uvedeném dotazu rozhraní API adresu URL vašeho serveru datového centra rozhraní API a použijte k vyvolání dotazu nástroj, jako je například [Poster](https://www.postman.com/downloads/) . 

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

Po úplné synchronizaci udržuje služba zřizování Azure AD `LastExecutionTimestamp` a používá ji k vytvoření rozdílových dotazů pro načítání přírůstkových změn. Atributy časového razítka přítomné v každé entitě SuccessFactors, například, `lastModifiedDateTime` `startDate` , `endDate` a `latestTerminationDate` , jsou vyhodnocovány, aby bylo možné zjistit, zda změna spadá mezi `LastExecutionTimestamp` a `CurrentExecutionTime` . Pokud ano, změna položky se považuje za efektivní a zpracovaná pro synchronizaci. 

## <a name="reading-attribute-data"></a>Čtení dat atributu

Když se služba zřizování Azure AD dotazuje SuccessFactors, načte sadu výsledků JSON. Sada výsledků JSON obsahuje několik atributů uložených v centru zaměstnanců. Ve výchozím nastavení je schéma zřizování nakonfigurováno tak, aby načetlo pouze podmnožinu těchto atributů. 

Pokud chcete načíst další atributy, postupujte podle níže uvedených kroků:
    
1. Přejděte k **podnikovým aplikacím**  ->  **SuccessFactors App**  ->  **Provisioning**  ->  **Upravit zřizování**  ->  **atribut – mapování stránky**.
1. Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
1. Klikněte na **Upravit seznam atributů pro SuccessFactors**. 

   > [!NOTE] 
   > Pokud se v Azure Portal nezobrazuje možnost **Upravit seznam atributů pro SuccessFactors** , použijte adresu URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* pro přístup ke stránce. 

1. Sloupec **výrazu rozhraní API** v tomto zobrazení zobrazuje výrazy JSONPath používané konektorem.

   >[!div class="mx-imgBorder"] 
   >![Rozhraní API – výraz](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. Můžete buď upravit existující hodnotu JSONPath, nebo přidat nový atribut s platným výrazem JSONPath do schématu. 

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

Výchozí schéma aplikace Azure AD SuccessFactors zřizování se dodává s [90 + předem definovanými atributy](sap-successfactors-attribute-reference.md). K přidání dalších atributů SuccessFactors do schématu zřizování použijte níže uvedený postup: 

1. Pomocí následujícího dotazu OData načtěte data pro platného testovacího uživatele od středu zaměstnance. 

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

1. Určení centrální entity zaměstnance přidružené k atributu
   * Pokud je atribut součástí entity *EmpEmployment* , vyhledejte atribut pod uzlem *employmentNav* . 
   * Pokud je atribut součástí entity *uživatele* , vyhledejte atribut v uzlu *employmentNav/userNav* .
   * Pokud je atribut součástí entity *EmpJob* , vyhledejte atribut pod uzlem *employmentNav/jobInfoNav* . 
1. Vytvořte cestu JSON přidruženou k atributu a přidejte tento nový atribut do seznamu atributů SuccessFactors. 
   * Příklad 1: řekněme, že chcete přidat atribut *okToRehire*, který je součástí entity *employmentNav* , pak použijte JSONPath  `$.employmentNav.results[0].okToRehire`
   * Příklad 2: řekněme, že chcete přidat *časové pásmo*atributu, který je součástí entity *userNav* , pak použijte JSONPath. `$.employmentNav.results[0].userNav.timeZone`
   * Příklad 3: řekněme, že chcete přidat atribut *flsaStatus*, který je součástí entity *jobInfoNav* , pak použijte JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. Uložte schéma. 
1. Restartujte zřizování.

### <a name="retrieving-custom-attributes"></a>Načítání vlastních atributů

Ve výchozím nastavení jsou následující vlastní atributy předem definované v aplikaci Azure AD SuccessFactors Provisioning: 
* *custom01-Custom15* od entity User (userNav)
* *customString1-customString15* z entity EmpEmployment (employmentNav) s názvem *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* z entity EmpJobInfo (jobInfoNav) s názvem *empJobNavCustomString1-empNavJobCustomString15*

Řekněme, že v centrální instanci zaměstnance *customString35* atribut v *EmpJobInfo* ukládá popis umístění. Tuto hodnotu chcete flowovat do atributu Active Directory *physicalDeliveryOfficeName* . Chcete-li nakonfigurovat mapování atributů pro tento scénář, použijte následující postup: 

1. Upravte seznam atributů SuccessFactors a přidejte nový atribut s názvem *empJobNavCustomString35*.
1. Nastavte výraz rozhraní API JSONPath pro tento atribut jako: `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Uložte a znovu načtěte změnu mapování v Azure Portal.  
1. V okně mapování atributů namapujte *empJobNavCustomString35* na *physicalDeliveryOfficeName*.
1. Uložte mapování.

Rozšíření tohoto scénáře: 
* Pokud chcete mapovat atribut *custom35* z entity *uživatele* , použijte JSONPath. `$.employmentNav.results[0].userNav.custom35`
* Pokud chcete mapovat atribut *customString35* z entity *EmpEmployment* , použijte JSONPath `$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Scénář zpracování převodu pracovního procesu

Převod pracovního procesu je proces převodu stávajícího zaměstnance v plném čase na dodavatele nebo zhotovitele na plný úvazek. V tomto scénáři pracovník pro zaměstnance přidá novou entitu *EmpEmployment* spolu s novou entitou *uživatele* pro stejnou entitu *Person* . Entita *uživatele* vnořená v předchozí entitě *EmpEmployment* je nastavená na hodnotu null. Chcete-li tento scénář zpracovat tak, aby se nová data o zaměstnání zobrazovala při převodu, můžete schéma zřizování aplikace hromadně aktualizovat pomocí níže uvedených kroků:  

1. Otevřete okno mapování atributů vaší aplikace pro zřizování SuccessFactors. 
1. Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
1. Kliknutím na odkaz **Projděte si schéma tady** a otevřete editor schémat. 

   >![Snímek obrazovky s odkazem na toto schéma se zobrazí v editoru schémat.](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. Před úpravou uložte kopii schématu kliknutím na odkaz ke **stažení** . 

   >![Snímek obrazovky znázorňující editor schémat s volbou stáhnout a uložit kopii schématu.](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. V editoru schémat stiskněte klávesovou zkratku CTRL-H a otevřete ovládací prvek najít-nahradit.
1. Do textového pole najít zkopírujte a vložte hodnotu. `$.employmentNav.results[0]`
1. Do textového pole nahradit zkopírujte a vložte hodnotu `$.employmentNav.results[?(@.userNav != null)]` . Všimněte si mezer kolem `!=` operátoru, který je důležitý pro úspěšné zpracování výrazu JSONPath. 
   >![Find-nahrazování a převod](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. Pro aktualizaci schématu klikněte na možnost Nahradit vše. 
1. Uložte schéma. 
1. Výše uvedený proces aktualizuje všechny výrazy JSONPath následujícím způsobem: 
   * Původní JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Nové JSONPath: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. Restartujte zřizování. 

### <a name="handling-rehire-scenario"></a>Zpracování scénáře pro zařazení

K dispozici jsou obvykle dvě možnosti pro zpracování renáborů: 
* Možnost 1: vytvoření nového profilu osoby v centru zaměstnanců
* Možnost 2: opětovné použití existujícího profilu osoby v centru zaměstnanců

Pokud váš personální proces používá možnost 1, nevyžadují se pro zřizovací schéma žádné změny. Pokud váš personální proces používá možnost 2, pak zaměstnanec Central přidá novou entitu *EmpEmployment* spolu s novou entitou *uživatele* pro stejnou entitu *Person* . Na rozdíl od scénáře převodu není entita *uživatele* v předchozí entitě *EmpEmployment* nastavená na hodnotu null. 

Aby bylo možné tento scénář opětovného navýšení zpracovat (možnost 2), aby se nejnovější data o zaměstnání zobrazovala pro profily opětovného zařazení, můžete schéma zřizování aplikace hromadně aktualizovat pomocí níže uvedených kroků:  

1. Otevřete okno mapování atributů vaší aplikace pro zřizování SuccessFactors. 
1. Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
1. Kliknutím na odkaz **Projděte si schéma tady** a otevřete editor schémat.   
1. Před úpravou uložte kopii schématu kliknutím na odkaz ke **stažení** .   
1. V editoru schémat stiskněte klávesovou zkratku CTRL-H a otevřete ovládací prvek najít-nahradit.
1. Do textového pole najít zkopírujte a vložte hodnotu. `$.employmentNav.results[0]`
1. Do textového pole nahradit zkopírujte a vložte hodnotu `$.employmentNav.results[-1:]` . Tento výraz JSONPath vrací nejnovější záznam *EmpEmployment* .   
1. Pro aktualizaci schématu klikněte na možnost Nahradit vše. 
1. Uložte schéma. 
1. Výše uvedený proces aktualizuje všechny výrazy JSONPath následujícím způsobem: 
   * Původní JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Nové JSONPath: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. Restartujte zřizování. 

Tato změna schématu také podporuje scénář převodu pracovních procesů. 

### <a name="handling-global-assignment-scenario"></a>Scénář zpracování globálních přiřazení

Když se pro globální přiřazení zpracuje uživatel v centru zaměstnanců, SuccessFactors přidá novou entitu *EmpEmployment* a nastaví *assignmentClass* na GA. Zároveň vytvoří novou entitu *uživatele* . Proto má uživatel teď:
* Jedna *EmpEmployment*  +  *uživatelská* entita, která odpovídá přiřazení domů s *assignmentClass* nastavenou na "St" a 
* Další *EmpEmployment*  +  entita*uživatele* EmpEmployment, která odpovídá globálnímu přiřazení s *assignmentClass* nastavenou na "GA"

Chcete-li načíst atributy patřící do uživatelského profilu standardního přiřazení a globálního přiřazení, použijte následující postup: 

1. Otevřete okno mapování atributů vaší aplikace pro zřizování SuccessFactors. 
1. Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
1. Kliknutím na odkaz **Projděte si schéma tady** a otevřete editor schémat.   
1. Před úpravou uložte kopii schématu kliknutím na odkaz ke **stažení** .   
1. V editoru schémat stiskněte klávesovou zkratku CTRL-H a otevřete ovládací prvek najít-nahradit.
1. Do textového pole najít zkopírujte a vložte hodnotu. `$.employmentNav.results[0]`
1. Do textového pole nahradit zkopírujte a vložte hodnotu `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
1. Pro aktualizaci schématu klikněte na možnost Nahradit vše. 
1. Uložte schéma. 
1. Výše uvedený proces aktualizuje všechny výrazy JSONPath následujícím způsobem: 
   * Původní JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Nové JSONPath: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. Znovu načtěte okno mapování atributů aplikace. 
1. Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
1. Klikněte na **Upravit seznam atributů pro SuccessFactors**.
1. Přidejte nové atributy pro načtení globálních dat přiřazení. Příklad: Pokud chcete načíst název oddělení přidruženého k globálnímu profilu přiřazení, můžete přidat atribut *globalAssignmentDepartment* s nastavením výrazu JSONPath na `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
1. Nyní můžete buď přesměrovat hodnoty obou oddělení na atributy služby Active Directory, nebo selektivně flowovat hodnotu pomocí mapování výrazů. Příklad: výraz uvedený níže nastaví hodnotu atributu AD *Department* na *globalAssignmentDepartment* , pokud je k dispozici, jinak nastaví hodnotu na *oddělení* přidružené ke standardnímu přiřazení. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. Uložte mapování. 
1. Restartujte zřizování. 

### <a name="handling-concurrent-jobs-scenario"></a>Scénář zpracování souběžných úloh

Pokud má uživatel v centru zaměstnanců v souběžných nebo více úlohách, existují dvě *EmpEmployment* a *uživatelské* entity s *assignmentClass* nastavenou na hodnotu "St". Chcete-li načíst atributy patřící do obou úloh, použijte postup uvedený níže: 

1. Otevřete okno mapování atributů vaší aplikace pro zřizování SuccessFactors. 
1. Přejděte dolů a klikněte na **Zobrazit upřesňující možnosti**.
1. Klikněte na **Upravit seznam atributů pro SuccessFactors**.
1. Řekněme, že chcete získat oddělení přidružené k úloze 1 a úloze 2. Předem definované *oddělení* atributů již načítá hodnotu oddělení pro první úlohu. Můžete definovat nový atribut s názvem *secondJobDepartment* a nastavit výraz JSONPath na `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. Nyní můžete buď přesměrovat hodnoty obou oddělení na atributy služby Active Directory, nebo selektivně flowovat hodnotu pomocí mapování výrazů. 
1. Uložte mapování. 
1. Restartujte zřizování. 

## <a name="writeback-scenarios"></a>Scénáře zpětného zápisu

V této části jsou popsány různé scénáře se zpětným zápisem. Doporučuje konfigurační přístupy na základě toho, jak se v SuccessFactors nastaví e-mail a telefonní číslo.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>Podporované scénáře pro zpětný zápis na telefon a e-mail 

| \# | Požadavek na scénář | E-mail – primární <br> Hodnota příznaku | Telefon do zaměstnání <br> primární hodnota příznaku | Mobilní telefon <br> primární hodnota příznaku | Telefon do zaměstnání <br> mapování | Mobilní telefon <br> mapování |
|--|--|--|--|--|--|--|
| 1 | * Jako primární nastavte jenom obchodní e-mail. <br> * Nenastavuje telefonní čísla. | true | true | false (nepravda) | \[Nenastaveno\] | \[Nenastaveno\] | 
| 2 | * V SuccessFactors, obchodním e-mailu a firemním telefonu je primární <br> * Vždy přesměrujte telefonní číslo Azure AD na firemní telefon a mobilní telefon do mobilního telefonu. | true | true | false (nepravda) | telephoneNumber | mobil | 
| 3 | * V SuccessFactors je primární e-mailová adresa a mobilní telefon <br> * Vždy přesměrovat telefonní číslo Azure AD na firemní telefon a mobilní zařízení do mobilního telefonu | true | false (nepravda) | true |  telephoneNumber | mobil | 
| 4 | * V SuccessFactors Business e-mail je primární. <br> * Ve službě Azure AD zkontrolujte, jestli je přítomné telefonní číslo, pokud je k dispozici, a potom zkontrolujte, jestli je k dispozici i mobilní číslo, označte pracovní telefonní číslo jako primární, jenom když není k dispozici mobilní číslo. | true | Použít mapování výrazů: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | Použít mapování výrazů: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobil | 
| 5 | * V SuccessFactors Business e-mail a firemní telefon je primární. <br> * V Azure AD, pokud je k dispozici mobilní zařízení, nastavte ho jako telefon do zaměstnání, jinak použijte telephoneNumber. | true | true | false (nepravda) | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[Nenastaveno\] | 

* Pokud neexistuje žádné mapování pro telefonní číslo v mapování atributů se zpětným zápisem, bude do zpětného zápisu zahrnut pouze e-mail.
* Během nové registrace zaměstnanců v centru pro zaměstnance je možné, že nebudete mít k dispozici firemní e-mail a telefonní číslo. Pokud je při připojování nastavené jako primární e-mail a firemní telefon jako primární, můžete při vytváření nového náboru nastavit fiktivní hodnotu pro telefon a e-mailovou adresu, kterou bude nakonec aktualizovat aplikace se zpětným zápisem.
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>Nepodporované scénáře pro zpětný zápis na telefon a e-mail

* V centru zaměstnanců je při připojování k osobnímu e-mailu a osobnímu telefonu nastavený jako primární. Aplikace se zpětným zápisem nemůže přepnout toto nastavení a nastavit firemní e-mail a firemní telefon jako primární.
* V centru zaměstnanců je firemní telefon nastavený jako primární. Aplikace se zpětným zápisem nemůže tuto změnu změnit a nastavit jako primární mobilní telefon.
* Aplikace se zpětným zápisem nemůže přečíst aktuální primární nastavení příznaku a použít stejné hodnoty pro operaci zápisu. Hodnoty příznaků nakonfigurované v mapování atributů budou vždy použity. 

## <a name="next-steps"></a>Další kroky

* [Informace o tom, jak nakonfigurovat SuccessFactors na zřizování služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Informace o tom, jak nakonfigurovat zpětný zápis na SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Další informace o podporovaných atributech SuccessFactors pro příchozí zřizování](sap-successfactors-attribute-reference.md)










