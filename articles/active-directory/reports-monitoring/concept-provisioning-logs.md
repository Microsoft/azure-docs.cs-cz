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
ms.date: 09/02/2020
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a15024362b31d49e51b291c10401bbf2965f1d82
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469860"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Sestavy zřizování na portálu Azure Active Directory (Preview)

Architektura vytváření sestav ve službě Azure Active Directory (Azure AD) se skládá z následujících součástí:

- **Aktivita** 
    - **Přihlášení** – informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - **Protokoly auditu**  -  [Protokoly auditu](concept-audit-logs.md) poskytují informace o činnostech systému o uživatelích a správě skupin, spravovaných aplikacích a aktivitách adresářů.
    - **Zřizování protokolů** – poskytněte systémové aktivity o uživatelích, skupinách a rolích, které zřídí služba zřizování Azure AD. 

- **Zabezpečení** 
    - **Riziková přihlášení** – [rizikové přihlášení](../identity-protection/overview-identity-protection.md) je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
    - **Uživatelé označení příznakem rizika** – [rizikové uživatel](../identity-protection/overview-identity-protection.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

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

![Protokoly zřizování](./media/concept-provisioning-logs/access-provisioning-logs.png "Protokoly zřizování")


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

Můžete filtrovat data zřizování. Některé hodnoty filtru se dynamicky naplní na základě vašeho tenanta. Pokud například ve vašem tenantovi nemáte žádné události vytvoření, nebude pro vytvoření k dispozici možnost filtrování.
Ve výchozím zobrazení můžete vybrat následující filtry:

- Identita
- Datum
- Stav
- Akce


![Přidání filtrů](./media/concept-provisioning-logs/default-filter.png "Filtr")

Filtr **identit** umožňuje zadat název nebo identitu, o které se zajímáte. Tato identita by mohla být uživatel, skupina, role nebo jiný objekt. Můžete hledat podle názvu nebo ID objektu. ID se liší podle scénáře. Například při zřizování objektu ze služby Azure AD do SalesForce je ID zdroje ID objektu uživatele ve službě Azure AD, zatímco TargetID je ID uživatele v Salesforce. Při zřizování z Workday do služby Active Directory je zdrojem ID ID zaměstnance pracovního procesu Workday. Všimněte si, že jméno uživatele nemusí být vždy k dispozici ve sloupci identita. Vždy bude existovat jedno ID. 


Filtr **Datum** umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty:

- 1 měsíc
- 7 dní
- 30 dní
- 24 hodin
- Vlastní časový interval

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncové datum.


Filtr **stavu** umožňuje vybrat:

- Vše
- Success
- Selhání
- Přeskočeno



Filtr **akcí** umožňuje filtrovat:

- Vytvořit 
- Aktualizace
- Odstranit
- Zakázat
- Jiné

Kromě toho můžete také nastavit následující filtry pro filtry výchozího zobrazení:

- ID úlohy
- ID cyklu
- ID změny
- ID zdroje
- ID cíle
- Aplikace


![Vybrat pole](./media/concept-provisioning-logs/add-filter.png "Vybrat pole")


- **ID úlohy** – jedinečné ID úlohy je přidruženo ke každé aplikaci, pro kterou jste povolili zřizování.   

- **ID cyklu** – jedinečně identifikuje cyklus zřizování. Toto ID můžete sdílet tak, aby podporovalo hledání cyklu, ve kterém k této události došlo.

- **ID změny** – jedinečný identifikátor pro událost zřizování. Toto ID můžete sdílet tak, aby podporovalo vyhledávání události zřizování.   


- **Zdrojový systém** – umožňuje určit, odkud se identita získává. Například při zřizování objektu ze služby Azure AD do ServiceNow je zdrojový systém Azure AD. 

- **Cílový systém** – umožňuje určit, kde se identita má zřídit. Například při zřizování objektu ze služby Azure AD do ServiceNow je cílový systém ServiceNow. 

- **Aplikace** – umožňuje zobrazit pouze záznamy aplikací se zobrazeným názvem, který obsahuje konkrétní řetězec.

 

## <a name="provisioning-details"></a>Podrobnosti zřizování 

Když vyberete položku v zobrazení seznamu zřizování, získáte další podrobnosti o této položce.
Podrobnosti jsou seskupené podle následujících kategorií:

- Postup

- Řešení potíží a doporučení

- Upravené vlastnosti

- Shrnutí


![Podrobnosti zřizování](./media/concept-provisioning-logs/provisioning-tabs.png "Karty")



### <a name="steps"></a>Postup

Karta **kroky** popisuje kroky podniknuté při zřizování objektu. Zřizování objektu se může skládat ze čtyř kroků: 

- Importovat objekt
- Zjistit, jestli je objekt v oboru
- Porovnat objekt mezi zdrojem a cílem
- Zřídit objekt (provést akci – může to být vytvoření, aktualizace, odstranění nebo zakázání)



![Postup](./media/concept-provisioning-logs/steps.png "Filtr")


### <a name="troubleshoot-and-recommendations"></a>Řešení potíží a doporučení


Karta **Poradce při potížích a doporučeních** poskytuje kód chyby a důvod. Informace o chybě jsou k dispozici pouze v případě selhání. 


### <a name="modified-properties"></a>Upravené vlastnosti

**Změněné vlastnosti** zobrazují starou hodnotu a novou hodnotu. V případech, kdy není k dispozici stará hodnota sloupce stará hodnota je prázdná. 


### <a name="summary"></a>Shrnutí

Karta **Souhrn** poskytuje přehled o tom, co se stalo, a identifikátory pro objekt ve zdrojovém a cílovém systému. 

## <a name="what-you-should-know"></a>Co byste měli vědět

- Azure Portal ukládá nahlášená data zřizování po dobu 30 dnů, pokud máte edici Premium a 7 dní, pokud máte bezplatnou edici.

- Atribut Change ID můžete použít jako jedinečný identifikátor. To je například užitečné při interakci s produktovou podporou.

- V tuto chvíli není k dispozici možnost stahovat data zřizování jako soubor CSV, ale data můžete exportovat pomocí [Microsoft Graph](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http).

- Služba Log Analytics momentálně není podporovaná.

- Pro uživatele, kteří nejsou v oboru, se můžou zobrazit vynechané události. To je očekáváno, zejména v případě, že je rozsah synchronizace nastaven na všechny uživatele a skupiny. Naše služba vyhodnotí všechny objekty v tenantovi, a to i ty, které jsou mimo rozsah. 

- Protokoly zřizování nejsou aktuálně k dispozici v cloudu pro státní správu. Pokud nemůžete získat přístup k protokolům zřizování, použijte prosím jako dočasné řešení protokoly auditu.  

## <a name="error-codes"></a>Kódy chyb

Pomocí následující tabulky můžete lépe pochopit, jak vyřešit chyby, které můžete najít v protokolech zřizování. U všech chybových kódů, které chybí, poskytněte zpětnou vazbu pomocí odkazu v dolní části této stránky. 

|Kód chyby|Popis|
|---|---|
|Konflikt, EntryConflict|Opravte konfliktní hodnoty atributů buď v rámci služby Azure AD, nebo v aplikaci, nebo zkontrolujte shodnou konfiguraci atributu, pokud by byl konfliktní uživatelský účet shodný a převzatý z něj. Další informace o konfiguraci atributů odpovídajícího atributu najdete v následující [dokumentaci](../app-provisioning/customize-application-attributes.md) .|
|TooManyRequests|Cílová aplikace odmítla tento pokus o aktualizaci uživatele, protože je přetížena a přijímá příliš mnoho požadavků. Žádná akce není k dispozici. Tento pokus bude automaticky vyřazen. Společnost Microsoft si také oznámila tento problém.|
|InternalServerError |Cílová aplikace vrátila neočekávanou chybu. Může se jednat o problém se službou cílové aplikace, který brání v práci. Tento pokus bude automaticky vyřazen za 40 minut.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Neautorizováno| Služba Azure AD se dokázala ověřit u cílové aplikace, ale nemá autorizaci k provedení této aktualizace. Projděte si pokyny, které poskytuje cílová aplikace, a také [kurz](../saas-apps/tutorial-list.md)příslušné aplikace.|
|UnprocessableEntity|Cílová aplikace vrátila neočekávanou odpověď. Konfigurace cílové aplikace nemusí být správná nebo může dojít k potížím se službou cílové aplikace, která brání jejímu fungování.|
|WebExceptionProtocolError |Při připojování k cílové aplikaci došlo k chybě protokolu HTTP. Žádná akce není k dispozici. Tento pokus bude automaticky vyřazen za 40 minut.|
|InvalidAnchor|Uživatel, který byl dříve vytvořen nebo spárován službou zřizování, již neexistuje. Zkontrolujte, jestli uživatel existuje. Pokud chcete vynutit přesouhlasení všech uživatelů, použijte k [restartování úlohy](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)MS Graph API. Všimněte si, že restartování zřizování spustí počáteční cyklus, který může nějakou dobu trvat. Odstraní také mezipaměť, kterou služba zřizování používá k provozu, což znamená, že všechny uživatele a skupiny v tenantovi budou muset být vyhodnoceny znovu a určité události zřizování by mohly být vyhozeny.|
|NotImplemented | Cílová aplikace vrátila neočekávanou odpověď. Konfigurace aplikace nemusí být správná nebo může být problém služby s cílovou aplikací, což brání v práci. Projděte si pokyny, které poskytuje cílová aplikace, a také [kurz](../saas-apps/tutorial-list.md)příslušné aplikace. |
|MandatoryFieldsMissing, MissingValues |Uživatele nelze vytvořit, protože chybí požadované hodnoty. Opravte chybějící hodnoty atributů ve zdrojovém záznamu, nebo zkontrolujte shodnou konfiguraci atributu, abyste zajistili, že požadovaná pole nebudou vynechána. [Přečtěte si další informace](../app-provisioning/customize-application-attributes.md) o konfiguraci atributů, které se shodují.|
|SchemaAttributeNotFound |Operaci nelze provést, protože byl zadán atribut, který v cílové aplikaci neexistuje. Přečtěte si [dokumentaci](../app-provisioning/customize-application-attributes.md) k přizpůsobení atributů a ujistěte se, že je konfigurace správná.|
|InternalError |Došlo k vnitřní chybě služby ve službě Azure AD Provisioning. Žádná akce není k dispozici. Tento pokus se automaticky zopakuje za 40 minut.|
|InvalidDomain |Operaci nelze provést, protože hodnota atributu obsahuje neplatný název domény. Aktualizujte název domény na uživateli nebo ho přidejte do seznamu povolených aplikací v cílové aplikaci. |
|Časový limit |Operaci nelze dokončit, protože odpověď trvala příliš dlouho. Žádná akce není k dispozici. Tento pokus se automaticky zopakuje za 40 minut.|
|LicenseLimitExceeded|V cílové aplikaci nelze vytvořit uživatele, protože pro tohoto uživatele nejsou k dispozici žádné licence. Buď si zajistěte další licence pro cílovou aplikaci, nebo zkontrolujte přiřazení uživatelů a konfiguraci mapování atributů, abyste se ujistili, že správným uživatelům jsou přiřazeny správné atributy.|
|DuplicateTargetEntries  |Operaci nelze dokončit, protože v cílové aplikaci bylo nalezeno více než jeden uživatel s nakonfigurovanými shodnými atributy. Buď odeberte duplicitního uživatele z cílové aplikace, nebo znovu nakonfigurujte mapování atributů, jak je popsáno [zde](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Operaci nelze dokončit, protože byl nalezen více než jeden uživatel s nakonfigurovanými shodnými atributy. Odeberte duplicitního uživatele nebo znovu nakonfigurujte mapování atributů, jak je popsáno [zde](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Při vyhodnocování každého uživatele se pokusíme importovat uživatele ze zdrojového systému. K této chybě obvykle dochází v případě, že uživatel, který naimportoval, nemá vlastnost Matching definovanou v mapování atributů. Bez hodnoty přítomné v objektu User pro atribut Matching nemůžeme vyhodnotit rozsahy, shodující se ani exportovat změny. Všimněte si, že přítomnost této chyby neindikuje, že se uživatel nachází v oboru, protože pro uživatele zatím nehodnotili rozsah.|
|EntrySynchronizationSkipped | Služba zřizování úspěšně provedla dotaz na zdrojový systém a identifikovala uživatele. U uživatele se neuskutečnila žádná další akce, která se přeskočila. Přeskočení může být způsobeno tím, že uživatel je mimo rozsah nebo uživatel, který už v cílovém systému existuje, a nevyžaduje žádné další změny.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Při provádění požadavku GET k načtení uživatele nebo skupiny přijali v odpovědi více uživatelů nebo skupin. Očekávali jsme, že odpověď bude v odpovědi dostávat jenom jeden uživatel nebo skupina. Pokud [například](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#get-group)provedeme požadavek GET na načtení skupiny a poskytneme filtr pro vyloučení členů a váš koncový bod SCIM vrátí členy, vygenerujeme tuto chybu.|

## <a name="next-steps"></a>Další kroky

* [Ověřit stav zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problém s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD](../app-provisioning/application-provisioning-config-problem.md)
