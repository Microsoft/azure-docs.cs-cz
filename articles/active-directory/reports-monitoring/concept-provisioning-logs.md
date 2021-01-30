---
title: Přehled zřizování protokolů v Azure Portal (Preview) | Microsoft Docs
description: Seznámení se sestavou se zřizováním sestav protokolu v Azure Active Directory prostřednictvím Azure Portal.
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
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad69df37d2635156873dc59d6fbf700a67ade548
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091928"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Přehled zřizování protokolů v Azure Portal (Preview)

Architektura vytváření sestav ve službě Azure Active Directory (Azure AD) se skládá z následujících součástí:

- Aktivita: 
    - **Přihlášení**: informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - [Protokoly auditu](concept-audit-logs.md): informace o činnosti systému týkající se správy uživatelů a skupin, spravovaných aplikací a aktivit adresáře.
    - **Protokoly zřizování**: systémová aktivita o uživatelích, skupinách a rolích, které jsou zřízené službou zřizování Azure AD. 

- Zabezpečení: 
    - **Riziková přihlášení**: [rizikové přihlášení](../identity-protection/overview-identity-protection.md) je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
    - **Uživatelé označení příznakem rizika**: [rizikový uživatel](../identity-protection/overview-identity-protection.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

Toto téma poskytuje přehled protokolů zřizování. Protokoly poskytují odpovědi na otázky, jako jsou: 

* Které skupiny byly úspěšně vytvořeny v ServiceNow?
* Které uživatele byly úspěšně odebrány z Adobe?
* Které uživatele z Workday byly úspěšně vytvořeny ve službě Active Directory? 

## <a name="prerequisites"></a>Požadavky

Tito uživatelé mají přístup k datům v protokolech zřizování:

* Vlastníci aplikací (protokoly pro vlastní aplikace)
* Uživatelé v roli správce zabezpečení, čtenář zabezpečení, čtenář sestav, operátor zabezpečení, správce aplikace a role správce cloudové aplikace
* Uživatelé ve vlastní roli s [oprávněním provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Globální správci


Pokud chcete zobrazit sestavu aktivity zřizování, ke svému klientovi musí být přidružená licence Azure AD Premium. Pokud chcete upgradovat edici Azure AD, přečtěte si téma [Začínáme s Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md). 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Způsoby interakce s protokoly zřizování 
Zákazníci můžou s protokoly zřizování spolupracovat čtyřmi způsoby:

- Přístup k protokolům z Azure Portal, jak je popsáno v následující části.
- Streamování zřizovacích protokolů do [Azure monitor](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-log-analytics). Tato metoda umožňuje rozšířené uchovávání dat a sestavování vlastních řídicích panelů, výstrah a dotazů.
- Dotazování [rozhraní API pro Microsoft Graph](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) pro protokoly zřizování
- Stahují se protokoly zřizování jako soubor CSV nebo JSON.

## <a name="access-the-logs-from-the-azure-portal"></a>Přístup k protokolům z Azure Portal
Přístup k protokolům zřizování můžete získat tak, že v části **monitorování** v podokně **Azure Active Directory** v [Azure Portal](https://portal.azure.com)vyberete **protokoly zřizování** . Může trvat až dvě hodiny, než se některé záznamy zřizování zobrazí na portálu.

![Snímek obrazovky zobrazující výběry pro přístup k protokolům zřizování.](./media/concept-provisioning-logs/access-provisioning-logs.png "Protokoly zřizování")


Protokol zřizování má výchozí zobrazení seznamu, které obsahuje:

- Identita
- Akce
- Zdrojový systém
- Cílový systém
- Stav
- Datum


![Snímek obrazovky, který zobrazuje výchozí sloupce v protokolu zřizování.](./media/concept-provisioning-logs/default-columns.png "Výchozí sloupce")

Zobrazení seznamu můžete přizpůsobit tak, že na panelu nástrojů vyberete **sloupce** .

![Snímek obrazovky, který zobrazuje tlačítko pro přizpůsobení sloupců](./media/concept-provisioning-logs/column-chooser.png "Výběr sloupců")

Tato oblast vám umožní zobrazit další pole nebo odebrat pole, která jsou už zobrazená.

![Snímek obrazovky, který zobrazuje dostupné sloupce s vybranými sloupci](./media/concept-provisioning-logs/available-columns.png "Dostupné sloupce")

Chcete-li získat podrobnější informace, vyberte položku v zobrazení seznamu.

![Snímek obrazovky, který zobrazuje podrobné informace.](./media/concept-provisioning-logs/steps.png "Filtrovat")


## <a name="filter-provisioning-activities"></a>Filtrovat aktivity zřizování

Můžete filtrovat data zřizování. Některé hodnoty filtru se dynamicky naplní na základě vašeho tenanta. Pokud například nemáte ve svém tenantovi žádné události "vytvořit", nebudete mít možnost **vytvořit** filtr.

Ve výchozím zobrazení můžete vybrat následující filtry:

- **Identita**
- **Date** (Datum)
- **Stav**
- **Akce**


![Snímek obrazovky zobrazující hodnoty filtru](./media/concept-provisioning-logs/default-filter.png "Filtrovat")

Filtr **identit** umožňuje zadat název nebo identitu, o které se zajímáte. Tato identita může být uživatel, skupina, role nebo jiný objekt. 

Můžete hledat podle názvu nebo ID objektu. ID se liší podle scénáře. Například při zřizování objektu ze služby Azure AD do Salesforce je ID zdroje ID objektu uživatele ve službě Azure AD. ID cíle je ID uživatele v Salesforce. Při zřizování z Workday do služby Active Directory je zdrojem ID ID zaměstnance pracovního procesu Workday. 

> [!NOTE]
> Název uživatele nemusí být vždy přítomen ve sloupci **identity** . Vždy bude existovat jedno ID. 


Filtr **Datum** umožňuje definovat časový rámec pro vracená data. Možné hodnoty:

- 1 měsíc
- 7 dní
- 30 dní
- 24 hodin
- Vlastní časový interval

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncové datum.

Filtr **stavu** umožňuje vybrat:

- **Vše**
- **Success**
- **Poruše**
- **Přeskočeno**

Filtr **akcí** umožňuje filtrovat tyto akce:

- **Vytvořit** 
- **Aktualizace**
- **Odstranit**
- **Zakázat**
- **Další**

Kromě filtrů výchozího zobrazení můžete nastavit následující filtry.

![Snímek obrazovky, který zobrazuje pole, která můžete přidat jako filtry.](./media/concept-provisioning-logs/add-filter.png "Vybrat pole")

- **ID úlohy**: jedinečné ID úlohy je přidruženo ke každé aplikaci, pro kterou jste povolili zřizování.   

- **ID cyklu**: ID cyklu jednoznačně identifikuje cyklus zřizování. Toto ID můžete sdílet s podporou produktu a vyhledat tak cyklus, ve kterém k této události došlo.

- **ID změny**: ID změny je jedinečný identifikátor pro událost zřizování. Pokud chcete vyhledat událost zřizování, můžete toto ID sdílet s podporou produktu.   

- **Zdrojový systém**: můžete určit, odkud se identita získává. Například při zřizování objektu ze služby Azure AD do ServiceNow je zdrojový systém Azure AD. 

- **Cílový systém**: můžete určit, kde se identita má zřídit. Například při zřizování objektu ze služby Azure AD do ServiceNow je cílový systém ServiceNow. 

- **Aplikace**: můžete zobrazit pouze záznamy aplikací se zobrazeným názvem, který obsahuje konkrétní řetězec.

## <a name="provisioning-details"></a>Podrobnosti zřizování 

Když vyberete položku v zobrazení seznamu zřizování, získáte další podrobnosti o této položce. Podrobnosti jsou seskupeny na následující karty.

![Snímek obrazovky zobrazující čtyři karty, které obsahují podrobné informace o zřizování.](./media/concept-provisioning-logs/provisioning-tabs.png "Karty")

- **Postup**: popisuje kroky podniknuté při zřizování objektu. Zřizování objektu se může skládat ze čtyř kroků:
  
  1. Importujte objekt.
  1. Určí, zda je objekt v oboru.
  1. Porovnává objekt mezi zdrojem a cílem.
  1. Zřídit objekt (vytvořit, aktualizovat, odstranit nebo zakázat).

  ![Snímek obrazovky ukazuje kroky zřizování na kartě kroky.](./media/concept-provisioning-logs/steps.png "Filtrovat")

- **Řešení potíží s & doporučeními**: poskytuje kód chyby a důvod. Informace o chybě jsou k dispozici pouze v případě, že dojde k chybě.

- **Změněné vlastnosti**: zobrazuje starou hodnotu a novou hodnotu. Pokud neexistuje žádná stará hodnota, tento sloupec je prázdný.

- **Summary**: poskytuje přehled o tom, co se stalo, a identifikátory pro objekt ve zdrojovém a cílovém systému.

## <a name="download-logs-as-csv-or-json"></a>Stáhnout protokoly jako CSV nebo JSON

Protokoly zřizování pro pozdější použití si můžete stáhnout tak, že do protokolů v Azure Portal kliknete a vyberete **Stáhnout**. Soubor se vyfiltruje na základě kritérií filtru, která jste vybrali. Zmenšete filtry tak, aby byly co konkrétní, aby se snížila velikost a doba stahování. 

Stahování CSV obsahuje tři soubory:

* **ProvisioningLogs**: stáhne všechny protokoly s výjimkou kroků zřizování a upravených vlastností.
* **ProvisioningLogs_ProvisioningSteps**: obsahuje kroky zřizování a ID změny. Můžete použít ID změny k připojení události k ostatním dvěma souborům.
* **ProvisioningLogs_ModifiedProperties**: obsahuje atributy, které byly změněny, a ID změny. Můžete použít ID změny k připojení události k ostatním dvěma souborům.

#### <a name="open-the-json-file"></a>Otevřít soubor JSON
K otevření souboru JSON použijte textový editor, jako je [Microsoft Visual Studio kód](https://aka.ms/vscode). Visual Studio Code usnadňuje čtení souboru tím, že poskytuje zvýrazňování syntaxe. Soubor JSON můžete otevřít také pomocí prohlížečů ve formátu bez úprav, jako je například [Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Prettify soubor JSON
Soubor JSON se stáhne ve formátu minifikovaného, aby se snížila velikost souboru ke stažení. V tomto formátu může být obtížné číst datovou část. Pokud chcete soubor prettify, podívejte se na dvě možnosti:

- [K formátování formátu JSON použijte Visual Studio Code](https://code.visualstudio.com/docs/languages/json#_formatting).

- Použijte PowerShell k formátování formátu JSON. Tento skript vytvoří výstup JSON ve formátu, který obsahuje karty a mezery: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Analyzovat soubor JSON

Tady jsou některé ukázkové příkazy pro práci se souborem JSON pomocí PowerShellu. Můžete použít libovolný programovací jazyk, se kterým jste spokojeni.  

Nejprve [si přečtěte soubor JSON](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json?view=powershell-7.1) spuštěním tohoto příkazu:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Teď můžete data analyzovat podle vašeho scénáře. Tady je několik příkladů: 

- Výstup všech ID úloh v souboru JSON:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Výstup všech identifikátorů změny pro události, u kterých byla akce "vytvořit":

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Co byste měli vědět

Zde jsou některé tipy a doporučení pro sestavy zřizování:

- Azure Portal ukládá nahlášená data zřizování po dobu 30 dnů, pokud máte edici Premium a 7 dní, pokud máte bezplatnou edici. Protokoly zřizování můžete publikovat, aby se [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) pro uchování delší než 30 dní. 

- Atribut Change ID můžete použít jako jedinečný identifikátor. To je užitečné, když pracujete s podporou produktu, například.

- Pro uživatele, kteří nejsou v oboru, se můžou zobrazit vynechané události. To je očekáváno, zejména v případě, že je rozsah synchronizace nastaven na všechny uživatele a skupiny. Služba vyhodnotí všechny objekty v tenantovi, a to i ty, které jsou mimo rozsah. 

- Protokoly zřizování nejsou aktuálně k dispozici v cloudu pro státní správu. Pokud nemůžete získat přístup k protokolům zřizování, použijte jako dočasné řešení protokoly auditu. 

- Protokoly zřizování nezobrazuje importy rolí (platí pro AWS, Salesforce a Zendesk). Protokoly pro import rolí najdete v protokolech auditu. 

## <a name="error-codes"></a>Kódy chyb

Pomocí následující tabulky můžete lépe pochopit, jak vyřešit chyby, které najdete v protokolech zřizování. U všech chybových kódů, které chybí, poskytněte zpětnou vazbu pomocí odkazu v dolní části této stránky. 

|Kód chyby|Description|
|---|---|
|Konflikt, EntryConflict|Opravte konfliktní hodnoty atributu buď v Azure AD, nebo v aplikaci. Nebo si přečtěte odpovídající konfiguraci atributu, pokud by byl konfliktní uživatelský účet shodný a převzatý z něj. Další informace o konfiguraci atributů odpovídajícího atributu najdete v [dokumentaci](../app-provisioning/customize-application-attributes.md) .|
|TooManyRequests|Cílová aplikace odmítla tento pokus o aktualizaci uživatele, protože je přetížený a přijímá příliš mnoho požadavků. Žádná akce není k dispozici. Tento pokus bude automaticky vyřazen. Společnost Microsoft si také oznámila tento problém.|
|InternalServerError |Cílová aplikace vrátila neočekávanou chybu. Je možné, že problém služby s cílovou aplikací brání v práci. Tento pokus bude automaticky vyřazen za 40 minut.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Neautorizováno| Služba Azure AD byla ověřena pomocí cílové aplikace, ale nebyla autorizována k provedení aktualizace. Projděte si pokyny, které poskytuje cílová aplikace, spolu s příslušným [kurzem](../saas-apps/tutorial-list.md)aplikace.|
|UnprocessableEntity|Cílová aplikace vrátila neočekávanou odpověď. Konfigurace cílové aplikace nemusí být správná nebo může být problém služby s cílovou aplikací znemožněný tím, že nebude fungovat.|
|WebExceptionProtocolError |Při připojování k cílové aplikaci došlo k chybě protokolu HTTP. Žádná akce není k dispozici. Tento pokus bude automaticky vyřazen za 40 minut.|
|InvalidAnchor|Uživatel, který byl dříve vytvořen nebo spárován službou zřizování, již neexistuje. Ujistěte se, že uživatel existuje. Pokud chcete vynutit nové porovnání všech uživatelů, [restartujte úlohu](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)pomocí rozhraní Microsoft Graph API. <br><br>Restartováním zřizování se aktivuje počáteční cyklus, což může trvat nějakou dobu. Restartováním zřizování se odstraní také mezipaměť, kterou služba zřizování používá k provozu. To znamená, že všechny uživatele a skupiny v tenantovi se budou muset vyhodnotit znovu a můžou se vynechat určité události zřizování.|
|NotImplemented | Cílová aplikace vrátila neočekávanou odpověď. Je možné, že konfigurace aplikace není správná nebo pokud je problém služby s cílovou aplikací znemožněný pracovat. Projděte si pokyny, které poskytuje cílová aplikace, spolu s příslušným [kurzem](../saas-apps/tutorial-list.md)aplikace. |
|MandatoryFieldsMissing, MissingValues |Uživatele nelze vytvořit, protože chybí požadované hodnoty. Opravte chybějící hodnoty atributu ve zdrojovém záznamu, nebo zkontrolujte shodnou konfiguraci atributu, abyste měli jistotu, že požadovaná pole nejsou vynechána. [Přečtěte si další informace](../app-provisioning/customize-application-attributes.md) o konfiguraci atributů, které se shodují.|
|SchemaAttributeNotFound |Operaci se nepodařilo provést, protože byl zadán atribut, který v cílové aplikaci neexistuje. Přečtěte si [dokumentaci](../app-provisioning/customize-application-attributes.md) k přizpůsobení atributů a ujistěte se, že je vaše konfigurace správná.|
|InternalError |Došlo k vnitřní chybě služby ve službě Azure AD Provisioning. Žádná akce není k dispozici. Tento pokus se automaticky zopakuje za 40 minut.|
|InvalidDomain |Operaci se nepovedlo provést, protože hodnota atributu obsahuje neplatný název domény. Aktualizujte název domény na uživateli nebo ho přidejte do seznamu povolených aplikací v cílové aplikaci. |
|Časový limit |Operaci se nepovedlo dokončit, protože na odpověď trvala moc dlouho aplikace. Žádná akce není k dispozici. Tento pokus se automaticky zopakuje za 40 minut.|
|LicenseLimitExceeded|Uživatele se v cílové aplikaci nepodařilo vytvořit, protože pro tohoto uživatele nejsou k dispozici žádné licence. Zajištění dalších licencí pro cílovou aplikaci. Nebo zkontrolujte přiřazení uživatelů a konfiguraci mapování atributů, abyste měli jistotu, že se správným uživatelům přiřadí správné atributy.|
|DuplicateTargetEntries  |Operaci se nepovedlo dokončit, protože se našlo víc než jednoho uživatele v cílové aplikaci s nakonfigurovanými shodnými atributy. Odeberte duplicitního uživatele z cílové aplikace nebo [znovu nakonfigurujte mapování atributů](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Operaci se nepodařilo dokončit, protože byl nalezen více než jeden uživatel s nakonfigurovanými shodnými atributy. Odeberte duplicitního uživatele nebo [překonfigurujte mapování atributů](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Při vyhodnocení každého uživatele se systém pokusí importovat uživatele ze zdrojového systému. K této chybě obvykle dochází v případě, že uživatel, který se právě importuje, nemá stejnou vlastnost definovanou v mapování atributů. Bez hodnoty přítomné v uživatelském objektu pro atribut Matching nemůže systém vyhodnotit rozsahy, shodující se ani exportovat změny. Všimněte si, že přítomnost této chyby neindikuje, že uživatel je v oboru, protože jste zatím nehodnotili rozsah pro uživatele.|
|EntrySynchronizationSkipped | Služba zřizování úspěšně provedla dotaz na zdrojový systém a identifikovala uživatele. U uživatele se neuskutečnila žádná další akce, která se přeskočila. Je možné, že uživatel byl mimo rozsah nebo uživatel již mohl existovat v cílovém systému, aniž by se musely provádět žádné další změny.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Požadavek GET na načtení uživatele nebo skupiny přijal v odpovědi více uživatelů nebo skupin. Systém očekává, že odpověď v odpovědi obdrží jenom jednoho uživatele nebo skupinu. Pokud [například](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)uděláte požadavek GET na načtení skupiny a poskytnete filtr pro vyloučení členů a váš systém pro koncový bod pro správu identit mezi doménami (SCIM) vrací členy, zobrazí se tato chyba.|

## <a name="next-steps"></a>Další kroky

* [Ověřit stav zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problém s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD](../app-provisioning/application-provisioning-config-problem.md)
* [Graph API pro protokoly zřizování](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)
