---
title: Zřizování protokolů na portálu Azure Active Directory (Preview) | Microsoft Docs
description: Úvod do sestav aktivit zřizování na portálu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d48aa3ead28ab0b0a22478a0c4183995483058a
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983507"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Sestavy zřizování na portálu Azure Active Directory (Preview)

Architektura vytváření sestav ve službě Azure Active Directory (Azure AD) se skládá z následujících součástí:

- **Aktivita** 
    - **Přihlášení** – informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - **Protokoly auditu protokoly**auditu poskytují informace o činnosti systému týkající se správy uživatelů a skupin, spravovaných aplikací a aktivit adresáře.[](concept-audit-logs.md)  - 
    - **Zřizování protokolů** – poskytněte systémové aktivity o uživatelích, skupinách a rolích, které zřídí služba zřizování Azure AD. 

- **Zabezpečení** 
    - **Riziková přihlášení** – [rizikové přihlášení](concept-risky-sign-ins.md) je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
    - **Uživatelé označení příznakem rizika** – [rizikové uživatel](concept-user-at-risk.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

Toto téma vám poskytne přehled o zřizovacích sestavách.

## <a name="prerequisites"></a>Požadavky

### <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
* Uživatelé v rolích správce zabezpečení, čtenář zabezpečení, čtenář sestav, Správce aplikací a role správce cloudové aplikace
* Globální správci


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Jaká licence Azure AD potřebujete pro přístup k aktivitám zřizování?

Aby se váš tenant mohl podívat na veškerou sestavu aktivity zřizování, musí mít přidruženou licenci Azure AD Premium. Pokud chcete upgradovat edici Azure Active Directory, přečtěte si téma [Začínáme se Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) . 

## <a name="provisioning-logs"></a>Protokoly zřizování

Protokoly zřizování poskytují odpovědi na následující otázky:

* Které skupiny byly úspěšně vytvořeny v ServiceNow?
* Jak se role naimportovaly z Amazon Web Services?
* Kteří uživatelé nebyli úspěšně vytvořeni v DropBoxu?

Přístup k protokolům zřizování můžete získat tak, že v části **monitorování** v okně **Azure Active Directory** v [Azure Portal](https://portal.azure.com)vyberete **protokoly zřizování** . Může trvat až dvě hodiny, než se některé záznamy zřizování zobrazí na portálu.

![Zřizování protokolů](./media/concept-provisioning-logs/access-provisioning-logs.png "Zřizování protokolů")


Protokol zřizování má výchozí zobrazení seznamu, které obsahuje:

- Identita
- Akce
- Zdrojový systém
- Cílový systém
- Stav
- Datum


![Výchozí sloupce](./media/concept-provisioning-logs/default-columns.png "Výchozí sloupce")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Výběr sloupců](./media/concept-provisioning-logs/column-chooser.png "Výběr sloupců")

To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.

![Dostupné sloupce](./media/concept-provisioning-logs/available-columns.png "Dostupné sloupce")

Chcete-li získat podrobnější informace, vyberte položku v zobrazení seznamu.

![Podrobné informace](./media/concept-provisioning-logs/steps.png "Filtr")


## <a name="filter-provisioning-activities"></a>Filtrovat aktivity zřizování

Chcete-li zúžit uvedená data na úroveň, která vám vyhovuje, můžete data zřizování filtrovat pomocí následujících výchozích polí. Všimněte si, že hodnoty v filtrech se dynamicky naplní na základě vašeho tenanta. Pokud například ve vašem tenantovi nemáte žádné události vytvoření, nebude pro vytvoření k dispozici možnost filtrování.

- Identita
- Action
- Zdrojový systém
- Cílový systém
- Stav
- Date


![Filtr](./media/concept-provisioning-logs/filter.png "Filtr")

Filtr **identit** umožňuje zadat název nebo identitu, o které se zajímáte. Tato identita by mohla být uživatel, skupina, role nebo jiný objekt. Můžete hledat podle názvu nebo ID objektu. ID se liší podle scénáře. Například při zřizování objektu ze služby Azure AD do SalesForce je ID zdroje ID objektu uživatele ve službě Azure AD, zatímco TargetID je ID uživatele v Salesforce. Při zřizování z Workday do služby Active Directory je zdrojem ID ID zaměstnance pracovního procesu Workday. Všimněte si, že jméno uživatele nemusí být vždy k dispozici ve sloupci identita. Vždy bude existovat jedno ID. 

Filtr **zdrojového systému** vám umožní určit, odkud se identita získává. Například při zřizování objektu ze služby Azure AD do ServiceNow je zdrojový systém Azure AD. 

Filtr **cílový systém** vám umožní určit, kam se identita získává. Například při zřizování objektu ze služby Azure AD do ServiceNow je cílový systém ServiceNow. 

Filtr **stavu** umožňuje vybrat:

- Vše
- Úspěch
- Chyba
- Přeskočeno

Filtr **akcí** umožňuje filtrovat:

- Create 
- Aktualizace
- Odstranění
- Zakázat
- Ostatní

Filtr **Datum** umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty jsou:

- 1 měsíc
- 7 dní
- 30 dní
- 24 hodin
- Vlastní časový interval

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncové datum.


Kromě výchozích polí můžete při výběru do filtru zahrnout taky následující pole:

- **ID úlohy** – jedinečné ID úlohy je přidruženo ke každé aplikaci, pro kterou jste povolili zřizování.   

- **ID cyklu** – jedinečně identifikuje cyklus zřizování. Toto ID můžete sdílet tak, aby podporovalo hledání cyklu, ve kterém k této události došlo.

- **ID změny** – jedinečný identifikátor pro událost zřizování. Toto ID můžete sdílet tak, aby podporovalo vyhledávání události zřizování.   



  

## <a name="provisioning-details"></a>Podrobnosti zřizování 

Když vyberete položku v zobrazení seznamu zřizování, získáte další podrobnosti o této položce.
Podrobnosti jsou seskupené podle následujících kategorií:

- Kroky

- Řešení potíží a doporučení

- Upravené vlastnosti

- Souhrn


![Filtr](./media/concept-provisioning-logs/provisioning-tabs.png "Karty")



### <a name="steps"></a>Kroky

Karta **kroky** popisuje kroky podniknuté při zřizování objektu. Zřizování objektu se může skládat ze čtyř kroků: 

- Importovat objekt
- Zjistit, jestli je objekt v oboru
- Porovnat objekt mezi zdrojem a cílem
- Zřídit objekt (provést akci – může to být vytvoření, aktualizace, odstranění nebo zakázání)



![Filtr](./media/concept-provisioning-logs/steps.png "Filtr")


### <a name="troubleshoot-and-recommendations"></a>Řešení potíží a doporučení


Karta **Poradce při potížích a doporučeních** poskytuje kód chyby a důvod. Informace o chybě jsou k dispozici pouze v případě selhání. 


### <a name="modified-properties"></a>Upravené vlastnosti

**Změněné vlastnosti** zobrazují starou hodnotu a novou hodnotu. V případech, kdy není k dispozici stará hodnota sloupce stará hodnota je prázdná. 


### <a name="summary"></a>Souhrn

Karta **Souhrn** poskytuje přehled o tom, co se stalo, a identifikátory pro objekt ve zdrojovém a cílovém systému. 

## <a name="what-you-should-know"></a>Co byste měli vědět

- Azure Portal ukládá nahlášená data zřizování po dobu 30 dnů, pokud máte edici Premium a 7 dní, pokud máte bezplatnou edici.

- Atribut Change ID můžete použít jako jedinečný identifikátor. To je například užitečné při interakci s produktovou podporou.

- V tuto chvíli není k dispozici možnost stahovat data zřizování.

- Služba Log Analytics momentálně není podporovaná.

- Když přistupujete k protokolům zřizování z kontextu aplikace, nefiltrují automaticky události na konkrétní aplikaci podle způsobu, jakým protokoly auditují.

## <a name="next-steps"></a>Další postup

* [Ověřit stav zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problém s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


