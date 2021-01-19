---
title: Referenční informace o integraci Azure Active Directory a Workday
description: Technická rozsáhlá podrobněa v Workday – zřizování řízené hodinou
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: 251e1d4249373ec52afb3d7edaa2325c992b66f1
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570157"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Jak se Azure Active Directory zřizování integruje s Workday

[Azure Active Directory služba zřizování uživatelů](../app-provisioning/user-provisioning.md) integruje s [Workday HCM](https://www.workday.com) ke správě životního cyklu identity uživatelů. Azure Active Directory nabízí tři předem připravené integrace: 

* [Zřizování uživatelů z Workday do místního prostředí Active Directory](../saas-apps/workday-inbound-tutorial.md)
* [Pracovní den pro Azure Active Directory zřizování uživatelů](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Zpětný zápis do Workday](../saas-apps/workday-writeback-tutorial.md)

Tento článek vysvětluje, jak integrace funguje a jak můžete přizpůsobit chování zřizování pro různé scénáře týkající se lidských zdrojů. 

## <a name="establishing-connectivity"></a>Navazování připojení 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Omezení přístupu k koncovým bodům rozhraní API Workday do Azure AD
Služba zřizování Azure AD používá základní ověřování pro připojení k koncovým bodům rozhraní API webových služeb Workday.  

Aby bylo možné lépe zabezpečit připojení mezi službou zřizování služby Azure AD a Workday, můžete omezit přístup tak, aby určený uživatel systému Integration System přistupuje pouze k rozhraním API Workday z povolených rozsahů IP adres služby Azure AD. Obraťte se prosím na správce Workday, aby ve vašem tenantovi Workday dokončil následující konfiguraci. 

1. Stáhněte si [nejnovější rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=56519) pro veřejný cloud Azure. 
1. Otevřete soubor a vyhledejte tag **azureactivedirectory selhala** 

   >[!div class="mx-imgBorder"] 
   >![Rozsah IP adres Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Zkopírujte všechny rozsahy IP adres uvedené v rámci elementu *addressPrefixes* a použijte rozsah k sestavení seznamu IP adres.
1. Přihlaste se k portálu pro správu Workday. 
1. Přejděte na úlohu **udržovat rozsahy IP** adres a vytvořte nový rozsah IP adres pro datová centra Azure. Zadejte rozsahy IP adres (pomocí zápisu CIDR) jako seznam oddělený čárkami.  
1. Přejděte na úlohu **Spravovat zásady ověřování** a vytvořte nové zásady ověřování. V zásadách ověřování použijte seznam **povolených ověření** k určení rozsahu IP adres Azure AD a skupiny zabezpečení, které budou mít přístup z tohoto rozsahu IP adres. Uložte změny. 
1. Pro potvrzení změn přejděte na úlohu **aktivovat všechny změny zásad** , které čekají na ověření.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Omezení přístupu k datům pracovních procesů v Workday pomocí omezených skupin zabezpečení

Výchozí postup [Konfigurace uživatele Integration System](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) pro pracovní den uděluje přístup k načtení všech uživatelů v tenantovi pracovního dne. V některých scénářích integrace můžete chtít přístup omezit tak, aby uživatelé patřící pouze k některým dozorčím organizacím Get_Workers volání rozhraní API a zpracovali ho pomocí konektoru služby Azure AD pro Workday. 

Tento požadavek můžete splnit tím, že pracujete s vaším správcem Workday a konfigurujete skupiny zabezpečení omezeného integračního systému. Další informace o tom, jak to udělat, najdete v [tomto článku komunity Workday](https://community.workday.com/forums/customer-questions/620393) (*přihlašovací údaje komunity Workday se vyžadují pro přístup k tomuto článku*).

Tato strategie omezení přístupu pomocí omezeného ISSG (skupiny zabezpečení Integration System) je zvláště užitečná v následujících scénářích: 
* **Scénář zavedení fází**: máte rozsáhlý tenant v Workday a plánujete provést postupné zavedení Workday do automatizovaného zřizování Azure AD. V tomto scénáři doporučujeme místo toho, aby uživatelé, kteří nejsou v dosahu aktuální fáze s filtry oborů Azure AD, nakonfigurovat omezené ISSG tak, aby se do Azure AD zobrazovaly jenom pracovní procesy v oboru.
* **Scénář více úloh zřizování**: máte rozsáhlého tenanta Workday a několik domén služby AD, které každá podporují jinou organizační jednotku, divizi nebo společnost. Pro podporu této topologie byste chtěli spustit více úloh zřizování služby Azure AD s více pracovními procesy s jednotlivými úlohami zřizování konkrétní sady pracovníků. Pokud v tomto scénáři nepoužíváte filtry oborů Azure AD k vyloučení dat pracovního procesu, doporučujeme nakonfigurovat omezené ISSG tak, aby se v Azure AD zobrazovaly jenom relevantní údaje pracovního procesu. 

### <a name="workday-test-connection-query"></a>Dotaz na test připojení Workday

K otestování připojení k Workday pošle služba Azure AD následující *Get_Workers* požadavek webové služby Workday. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Jak funguje Úplná synchronizace

**Úplná synchronizace** v kontextu zřizování založeného na Workday odkazuje na proces načtení všech identit z Workday a určení pravidel zřizování, která se mají použít u každého objektu pracovního procesu. Úplná synchronizace nastane při prvním zapnutí zřizování a také při *restartování zřizování* z Azure Portal nebo pomocí rozhraní Graph API. 

Služba Azure AD pošle následující žádost o načtení dat pracovního procesu do *Get_Workers* žádosti o webovou službu Workday. Dotaz vyhledá v protokolu transakcí Workday všechny platné položky pracovního procesu v čase odpovídajícím úplnému spuštění synchronizace. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
Uzel *Response_Group* slouží k určení atributů pracovního procesu, které mají být načteny z pracovního dne. Popis každého příznaku v uzlu *Response_Group* najdete v [dokumentaci k rozhraní API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)pro Workday Get_Workers. 

Některé hodnoty příznaků zadané v uzlu *Response_Group* se počítají na základě atributů nakonfigurovaných v rámci aplikace zřizování služby Azure AD v Workday. Kritéria používaná k nastavení hodnot příznaků najdete v části *podporovaných entit* . 

*Get_Workers* odpověď z pracovního dne pro výše uvedený dotaz zahrnuje počet záznamů pracovních procesů a počet stránek.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Chcete-li načíst další stránku sady výsledků, dotaz Next *Get_Workers* Určuje číslo stránky jako parametr v *Response_Filter*.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Služba zřizování Azure AD zpracuje každou stránku a projde všemi efektivními pracovními procesy během úplné synchronizace. Pro každou položku pracovního procesu naimportovanou z pracovního dne:
* [Výraz XPath](workday-attribute-reference.md) se použije pro načtení hodnot atributů z pracovního dne.
* Použije se mapování atributů a pravidla pro porovnání. 
* Služba určuje, jakou operaci má v cíli (Azure AD/AD) provést. 

Po dokončení zpracování uloží časové razítko přidružené k začátku úplné synchronizace jako meze. Tento vodoznak slouží jako výchozí bod pro přírůstkový cyklus synchronizace. 

## <a name="how-incremental-sync-works"></a>Jak funguje přírůstková synchronizace

Po úplné synchronizaci udržuje služba zřizování Azure AD `LastExecutionTimestamp` a používá ji k vytvoření rozdílových dotazů pro načtení přírůstkových změn. Při přírůstkové synchronizaci Azure AD odesílá do Workday následující typy dotazů: 

* [Dotaz na ruční aktualizace](#query-for-manual-updates)
* [Dotaz na efektivní aktualizace a ukončení s datem platnosti](#query-for-effective-dated-updates-and-terminations)
* [Dotaz na budoucí pronájem s datem](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>Dotaz na ruční aktualizace

Následující *Get_Workers* vyžádat dotaz na ruční aktualizace, ke kterým došlo mezi posledním spuštěním a aktuálním časem spuštění. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Dotaz na efektivní aktualizace a ukončení s datem platnosti

Následující *Get_Workers* vyžádá dotaz na účinné aktualizace, ke kterým došlo mezi posledním spuštěním a aktuálním časem spuštění. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Dotaz na budoucí pronájem s datem

Pokud některý z výše uvedených dotazů vrátí budoucí pronájem, pak se k získání informací o novém zařazení nového pronájmu používá následující *Get_Workers* požadavek. Atribut *WID* nového zařazení se používá k provedení vyhledávání a datum účinnosti je nastaveno na datum a čas zařazení. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="retrieving-worker-data-attributes"></a>Načítání atributů dat pracovního procesu

Rozhraní *Get_Workers* API může vracet různé datové sady, které jsou přidružené k pracovnímu procesu. V závislosti na [výrazech rozhraní API XPath](workday-attribute-reference.md) nakonfigurovaných ve schématu zřizování určí služba Azure AD Provisioning, které datové sady se mají načíst z Workday. Proto jsou příznaky *Response_Group* nastaveny v žádosti *Get_Workers* . 

Následující tabulka uvádí pokyny k mapování konfigurace, která se má použít k načtení konkrétní sady dat. 

| \# | Entita Workday                       | Zahrnuto ve výchozím nastavení | Vzor XPATH pro určení v mapování pro načtení jiných než výchozích entit             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Osobní údaje                        | Ano                 | nepřenosná data pracovního procesu/podprocesu \_ : osobní \_ data                                             |
| 2  | Data o zaměstnání                      | Ano                 | nepracovní data pracovního procesu/přenosová data \_ : data o zaměstnání \_                                           |
| 3  | Další data úlohy                  | Ano                 | nečinnost: data pracovního procesu/přenosová data/meziprocesy \_ \_ : \_ úloha dat úlohy pracovního procesu \_ \[ @wd:Primary \_ = 0\]|
| 4  | Data organizace                    | Ano                 | nepřenosová data pracovního procesu/podprocesu \_ : \_ data organizace                                         |
| 5  | Data řetězu pro správu                | Ano                 | nepřenosová data pracovního procesu/meziprocesu \_ : \_ data řetězu správy \_                                    |
| 6  | Dozorčí organizace             | Ano                 | NAD                                                                 |
| 7  | Společnost                              | Ano                 | PODNIKOVÝ                                                                     |
| 8  | Organizační jednotka                        | Ne                  | ' obchodní \_ jednotka '                                                              |
| 9  | Hierarchie organizačních jednotek              | Ne                  | hierarchie BUSINESS \_ Unit \_                                                   |
| 10 | Hierarchie společnosti                    | Ne                  | hierarchie společnosti \_                                                          |
| 11 | Cost Center                          | No                  | NÁKLADové \_ středisko                                                                |
| 12 | Hierarchie nákladového centra                | Ne                  | ' hierarchie NÁKLADového \_ centra \_ '                                                     |
| 13 | Obrat                                 | Ne                  | OBRAT                                                                        |
| 14 | Hierarchie fondů                       | Ne                  | ' \_ hierarchie fondů '                                                             |
| 15 | Dárkový                                 | Ne                  | Dárkový                                                                        |
| 16 | Hierarchie dárku                       | Ne                  | ' hierarchie DÁRKů \_ '                                                             |
| 17 | Oprávnění                                | Ne                  | UDĚLIT                                                                       |
| 18 | Udělit hierarchii                      | Ne                  | ' udělit \_ hierarchii '                                                            |
| 19 | Hierarchie firemních webů              | Ne                  | hierarchie obchodních \_ lokalit \_                                                   |
| 20 | Maticová organizace                  | Ne                  | SLUŽBU                                                                      |
| 21 | Skupina plateb                            | Ne                  | ' PAY – \_ Skupina '                                                                  |
| 22 | Programy                             | Ne                  | SPUŠTĚN                                                                    |
| 23 | Hierarchie programu                    | Ne                  | ' \_ hierarchie programu '                                                          |
| 24 | Oblast                               | Ne                  | hierarchie REGION \_                                                           |
| 25 | Hierarchie umístění                   | Ne                  | ' LOCATION \_ HIERARCHY '                                                         |
| 26 | Data zřizování účtů            | Ne                  | meziprocesový: data pracovního procesu/přenosová data \_ : \_ zřizování účtů \_                                |
| 27 | Data kontroly na pozadí                | Ne                  | nepřenosová data/podprocesy: data \_ \_ kontroly pozadí \_                                    |
| 28 | Data nároku na zvýhodnění             | Ne                  | nevyužitý: data pracovního procesu \_ / \_ přenosová data: zaměstnanecký nárok \_                                 |
| 29 | Data registrace výhod              | Ne                  | nevyužitý: data pracovního procesu/podprocesy \_ : \_ data registrace výhod \_                                  |
| 30 | Data kariéry                          | Ne                  | meziprocesový: data pracovního procesu/přenosová data \_ : kariéry \_                                               |
| 31 | Data kompenzace                    | Ne                  | nečinnost: data pracovního procesu/přenosová \_ data: data kompenzace \_                                         |
| 32 | Podmíněná data finančního úřadu pracovního procesu | Ne                  | meziforma: data pracovního procesu/podprocesu \_ : \_ \_ \_ \_ \_ data typu \_ podmíněných pracovních autorit pro daňové úřady       |
| 33 | Data vývojových položek                | Ne                  | nepřenosová data pracovního procesu/podprocesu \_ : \_ data vývoje položky \_                                    |
| 34 | Data o kontraktech zaměstnanců              | Ne                  | nepřenosová data pracovního procesu/meziprocesu \_ : \_ data smluv zaměstnanců \_                                  |
| 35 | Data kontroly zaměstnanců                 | Ne                  | nepřenosová data pracovního procesu/přenosová \_ data: zaměstnanci – \_ Revize \_ dat                                     |
| 36 | Data přijatá zpětná vazba               | Ne                  | nedoručení: data pracovního procesu/podprocesy \_ : \_ data přijatá zpětná vazba \_                                   |
| 37 | Data cíle pracovního procesu                     | Ne                  | přenos dat pracovního procesu/meziprocesu \_ : \_ data cíle pracovního procesu \_                                         |
| 38 | Data fotky                           | Ne                  | MS: data pracovního procesu/přenosová data \_ : \_ data fotky                                                |
| 39 | Data kvalifikace                   | Ne                  | nepřenosová data pracovního procesu/meziprocesový: \_ \_ data kvalifikace                                        |
| 40 | Data souvisejících osob                 | Ne                  | nepřenosová data pracovního procesu/meziprocesu \_ : související \_ \_ data osob                                     |
| 41 | Data role                            | Ne                  | nepřenosová data pracovního procesu/podprocesu \_ : \_ data role                                                 |
| 42 | Data dovedností                           | Ne                  | nepřenosová data pracovních procesů: data o \_ dovednostech \_                                                |
| 43 | Data profilu úspěšnosti              | Ne                  | meziprocesový: data pracovního procesu \_ / \_ přenosová data: data profilu pro úspěch \_                                  |
| 44 | Data posouzení talentů               | Ne                  | nepřenosová data pracovního procesu/podprocesu \_ : talentů \_ \_ data posouzení                                   |
| 45 | Data uživatelského účtu                    | Ne                  | nepřenosová data pracovního procesu/podprocesu \_ : \_ data uživatelského účtu \_                                        |
| 46 | Data dokumentů pracovního procesu                 | Ne                  | nepřenosová data pracovního procesu/podprocesu \_ : \_ data pracovního dokumentu \_                                     |

Tady je několik příkladů, jak můžete tuto integraci v rámci pracovní doby rozšíří, aby splňovala konkrétní požadavky. 

**Příklad 1**

Řekněme, že chcete z Workday načíst následující datové sady a použít je v pravidlech zřizování:

* Nákladové středisko
* Hierarchie nákladového centra
* Skupina plateb

Výše uvedené datové sady nejsou ve výchozím nastavení zahrnuty. Načtení těchto datových sad:
1. Přihlaste se k Azure Portal a otevřete pracovní den v rámci aplikace zřizování uživatelů služby AD nebo Azure AD. 
1. V okně zřizování upravte mapování a otevřete seznam atributů Workday z části Upřesnit. 
1. Přidejte následující definice atributů a označte je jako povinné. Tyto atributy nebudou namapovány na žádný atribut ve službě AD ani v Azure AD. Jenom slouží jako signály konektoru pro načtení nákladového centra, hierarchie nákladového centra a informací o skupině plateb. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Název atributu | Výraz rozhraní API XPATH |
     >|---|---|
     >| CostCenterHierarchyFlag  |  /WD: Work/FORMED: Worker_Data: Organization_Data/WD: Worker_Organization_Data [/WD: Organization_Data/WD: Organization_Type_Reference: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  /WD: Work/FORMED: Worker_Data: Organization_Data/WD: Worker_Organization_Data [/WD: Organization_Data/WD: Organization_Type_Reference: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER ']/WD: Organization_Data/WD: Organization_Code/text () |
     >| PayGroupFlag  |  /WD: Work/FORMED: Worker_Data: Organization_Data/WD: Worker_Organization_Data [/WD: Organization_Data/WD: Organization_Type_Reference: ID [ @wd:type = ' Organization_Type_ID '] = ' PAY_GROUP ']/WD: Organization_Data/WD: Organization_Reference_ID/text () |

1. Jakmile jsou nákladové středisko a sada dat skupiny plateb dostupné v *Get_Workers* reakci, můžete k získání názvu nákladového centra, kódu nákladového centra a skupiny plateb použít následující hodnoty XPath. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Název atributu | Výraz rozhraní API XPATH |
     >|---|---|
     >| CostCenterName  | Levné: Work/Form: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' cost Center ']/WD: Organization_Name/text () |
     >| CostCenterCode | Levné: Work/Form: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' cost Center ']/WD: Organization_Code/text () |
     >| PayGroup | /WD: Work/Form: Worker_Data: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' Pay Group ']/WD: Organization_Name/text () |

**Příklad 2**

Řekněme, že chcete načíst certifikace spojené s uživatelem. Tyto informace jsou k dispozici jako součást sady *dat kvalifikace* . Pokud chcete tuto datovou sadu získat jako součást odpovědi *Get_Workers* , použijte následující cestu XPath: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Příklad 3**

Řekněme, že chcete načíst *zřizovací skupiny* přiřazené pracovnímu procesu. Tyto informace jsou k dispozici jako součást sady *dat zřizování účtu* . Pokud chcete tuto datovou sadu získat jako součást odpovědi *Get_Workers* , použijte následující cestu XPath: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>Další kroky

* [Informace o tom, jak nakonfigurovat zřizování z Workday na Active Directory](../saas-apps/workday-inbound-tutorial.md)
* [Přečtěte si, jak nakonfigurovat zpětný zápis do Workday.](../saas-apps/workday-writeback-tutorial.md)
* [Další informace o podporovaných atributech Workday pro příchozí zřizování](workday-attribute-reference.md)

