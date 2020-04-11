---
title: Zřizování protokolů na portálu Azure Active Directory (preview) | Dokumenty společnosti Microsoft
description: Úvod do zřizování sestav aktivit na portálu Azure Active Directory
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
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30cc8be6ad9ebffcad58c5b2412ae15ff3f26fa5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113317"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Zřizování sestav na portálu Azure Active Directory (preview)

Architektura sestav ve službě Azure Active Directory (Azure AD) se skládá z následujících součástí:

- **Činnosti** 
    - **Přihlášení** – informace o využití spravovaných aplikací a aktivity přihlášení uživatelů.
    - **Protokoly auditu** - [Protokoly auditu](concept-audit-logs.md) poskytují informace o aktivitách systému uživatelů a správy skupin, spravovaných aplikací a aktivit adresářů.
    - **Protokoly zřizování** – poskytují systémové aktivity o uživatele, skupiny a role, které jsou zřizované služby zřizování Azure AD. 

- **Zabezpečení** 
    - **Riskantní přihlášení** – riskantní [přihlášení](concept-risky-sign-ins.md) je indikátorem pro pokus o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
    - **Uživatelé označeni jako riziko** – [rizikový uživatel](concept-user-at-risk.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

Toto téma poskytuje přehled sestavy zřizování.

## <a name="prerequisites"></a>Požadavky

### <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
* Uživatelé v rolích Správce zabezpečení, Čtečka zabezpečení, Čtečka sestav, Správce aplikací a Správce cloudových aplikací
* Globální správci


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Jakou licenci Azure AD potřebujete pro přístup k aktivitám zřizování?

Váš tenant musí mít licenci Azure AD Premium k němu spojené zobrazit všechny sestavy zřizování aktivit. Podívejte [se na téma Začínáme s Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) a upgradujte edici Azure Active Directory. 

## <a name="provisioning-logs"></a>Protokoly zřizování

Protokoly zřizování poskytují odpovědi na následující otázky:

* Jaké skupiny byly úspěšně vytvořeny v ServiceNow?
* Jak byly role importovány z Amazon Web Services?
* Co uživatelé byli neúspěšně vytvořeni v DropBoxu?

K protokolům zřizování se dostanete výběrem **protokolů zřizování** v části **Monitorování** v okně **Azure Active Directory** na webu Azure [Portal](https://portal.azure.com). Může trvat až dvě hodiny, než se některé záznamy zřizování zobrazí na portálu.

![Protokoly zřizování](./media/concept-provisioning-logs/access-provisioning-logs.png "Protokoly zřizování")


Protokol zřizování má výchozí zobrazení seznamu, které zobrazuje:

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

Vyberte položku v zobrazení seznamu, abyste získali podrobnější informace.

![Podrobné informace](./media/concept-provisioning-logs/steps.png "Filtr")


## <a name="filter-provisioning-activities"></a>Filtrování zřizovacích aktivit

Můžete filtrovat data zřizování. Některé hodnoty filtru jsou dynamicky naplněny na základě vašeho tenanta. Pokud například nemáte žádné vytvořit události ve vašem tenantovi, nebude filtr možnost pro vytvoření.
Ve výchozím zobrazení můžete vybrat následující filtry:

- Identita
- Datum
- Status
- Akce


![Filtr](./media/concept-provisioning-logs/default-filter.png "Filtr")

Filtr **Identita** umožňuje zadat název nebo identitu, která vás zajímá. Tato identita může být uživatel, skupina, role nebo jiný objekt. Můžete hledat podle názvu nebo ID objektu. ID se liší podle scénáře. Například při zřizování objektu z Azure AD na SalesForce, ID zdroje je ID objektu uživatele ve službě Azure AD, zatímco TargetID je ID uživatele v Salesforce. Při zřizování z Pracovního dne do služby Active Directory je ID zdroje ID ID pracovníka pracovního dne. Všimněte si, že jméno uživatele nemusí být vždy k dispozici ve sloupci Identita. Vždy bude existovat jeden ID. 


Filtr **Datum** umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty:

- 1 měsíc
- 7 dní
- 30 dní
- 24 hodin
- Vlastní časový interval

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncové datum.


**Stavový** filtr umožňuje vybrat:

- Všechny
- Úspěch
- Selhání
- Přeskočen



Filtr **Akce** umožňuje filtrovat:

- Vytvořit 
- Aktualizace
- Odstranit
- Zakázat
- Ostatní

Kromě filtrů výchozího zobrazení můžete také nastavit následující filtry:

- ID úlohy
- ID cyklu
- ID změny
- ID zdroje
- ID cíle
- Aplikace


![Výběr pole](./media/concept-provisioning-logs/add-filter.png "Výběr pole")


- **ID úlohy** – ke každé aplikaci, pro kterou jste povolili zřizování, je přidruženo jedinečné ID úlohy.   

- **ID cyklu** – jednoznačně identifikuje cyklus zřizování. Toto ID můžete sdílet pro podporu pro vyhledat cyklus, ve kterém k této události došlo.

- **ID změny** – jedinečný identifikátor události zřizování. Toto ID můžete sdílet pro podporu pro vyvyhledávání události zřizování.   


- **Zdrojový systém** – umožňuje určit, odkud se identita zmiňuje. Například při zřizování objektu z Azure AD do ServiceNow, zdrojový systém je Azure AD. 

- **Cílový systém** – umožňuje určit, kde je identita stále zmiňována. Například při zřizování objektu z Azure AD do ServiceNow, cílový systém je ServiceNow. 

- **Aplikace** – umožňuje zobrazit pouze záznamy aplikací se zobrazovaným názvem, který obsahuje určitý řetězec.

 

## <a name="provisioning-details"></a>Podrobnosti zřizování 

Když vyberete položku v zobrazení seznamu zřizování, získáte další podrobnosti o této položce.
Podrobnosti jsou seskupeny podle následujících kategorií:

- Kroky

- Poradce při potížích a doporučení

- Změněné vlastnosti

- Souhrn


![Filtr](./media/concept-provisioning-logs/provisioning-tabs.png "Karty")



### <a name="steps"></a>Kroky

Karta **Kroky** popisuje kroky, které byly podniknuty při zřizování objektu. Zřizování objektu se může skládat ze čtyř kroků: 

- Importovat objekt
- Zjištění, zda je objekt v oboru
- Shoda objektu mezi zdrojem a cílem
- Zřizovací objekt (provést akci – může se jednat o vytvoření, aktualizaci, odstranění nebo zakázání)



![Filtr](./media/concept-provisioning-logs/steps.png "Filtr")


### <a name="troubleshoot-and-recommendations"></a>Poradce při potížích a doporučení


Karta **Poradce při potížích a doporučení** obsahuje kód chyby a důvod. Informace o chybě jsou k dispozici pouze v případě selhání. 


### <a name="modified-properties"></a>Změněné vlastnosti

**Upravené vlastnosti** zobrazí starou hodnotu a novou hodnotu. V případech, kdy neexistuje žádná stará hodnota, je starý sloupec hodnoty prázdný. 


### <a name="summary"></a>Souhrn

Karta **Souhrn** poskytuje přehled o tom, co se stalo, a identifikátory objektu ve zdrojovém a cílovém systému. 

## <a name="what-you-should-know"></a>Co byste měli vědět

- Azure portal ukládá hlášeny zřizování dat po dobu 30 dnů, pokud máte prémiovou edici a 7 dní, pokud máte bezplatnou edici..

- Atribut Změnit ID můžete použít jako jedinečný identifikátor. To je například užitečné při interakci s podporou produktu.

- V současné době neexistuje žádná možnost stáhnout zřizovací data.

- V současné době neexistuje žádná podpora pro analýzu protokolu.

- Když přistupujete k protokolům zřizování z kontextu aplikace, automaticky nefiltruje události do konkrétní aplikace způsobem, jakým to dělají protokoly auditování.

## <a name="error-codes"></a>Kódy chyb

Pomocí následující tabulky lépe pochopit, jak vyřešit chyby, které můžete najít v protokolech zřizování. Pro všechny chybové kódy, které chybí, zadejte zpětnou vazbu pomocí odkazu v dolní části této stránky. 

|Kód chyby|Popis|
|---|---|
|Konflikt, EntryConflict|Opravte konfliktní hodnoty atributů v Azure AD nebo v aplikaci nebo zkontrolujte konfiguraci odpovídajícího atributu, pokud měl být konfliktní uživatelský účet spárován a převzat. Další informace o konfiguraci odpovídajících atributů naleznete v následující [dokumentaci.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)|
|TooManyRequests|Cílová aplikace odmítla tento pokus o aktualizaci uživatele, protože je přetížený a přijímá příliš mnoho požadavků. Nedá se nic dělat. Tento pokus bude automaticky vyřazen. Společnost Microsoft byla na tento problém také upozorněna.|
|InternalServerError |Cílová aplikace vrátila neočekávanou chybu. Může být problém služby s cílovou aplikací, která brání tomu, aby to fungovalo. Tento pokus bude automaticky vyřazen za 40 minut.|
|InsufficientRights, MethodNotAllowed, NotAllowed, NotAllowed, Unauthorized Insufficient Rights, MethodNotAllowed, NotAllowed, Unauthorized InsufficientRights, MethodNotAllowed, NotAllowed, Unauthorized Insufficient Rights| Azure AD bylo možné ověřit pomocí cílové aplikace, ale nebyl oprávněn k provedení aktualizace. Přečtěte si prosím všechny pokyny poskytnuté cílovou aplikací, stejně jako příslušné [aplikace tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list).|
|Nezpracovatelná entita|Cílová aplikace vrátila neočekávanou odpověď. Konfigurace cílové aplikace nemusí být správná nebo může být problém služby s cílovou aplikací, která brání tomu z práce.|
|Chyba protokolu WebExceptionProtocolError |Při připojování k cílové aplikaci došlo k chybě protokolu HTTP. Nedá se nic dělat. Tento pokus bude automaticky vyřazen za 40 minut.|
|Neplatná kotva|Uživatel, který byl dříve vytvořen nebo spárován službou zřizování již neexistuje. Zkontrolujte, zda uživatel existuje. Chcete-li vynutit opětovné shodě všech uživatelů, použijte k [restartování úlohy](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)rozhraní MS Graph API . Všimněte si, že restartování zřizování aktivuje počáteční cyklus, jehož dokončení může nějakou dobu trvat. Také odstraní mezipaměti zřizovací služba používá k provozu, což znamená, že všichni uživatelé a skupiny v tenantovi bude muset být vyhodnocena znovu a některé zřizování události by mohly být vynechány.|
|Není implementováno | Cílová aplikace vrátila neočekávanou odpověď. Konfigurace aplikace nemusí být správná nebo může být problém se službou s cílovou aplikací, která tomu brání v práci. Přečtěte si prosím všechny pokyny poskytnuté cílovou aplikací, stejně jako příslušné [aplikace tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). |
|MandatoryFieldsMissing, MissingValues |Uživatele nelze vytvořit, protože chybí požadované hodnoty. Opravte chybějící hodnoty atributů ve zdrojovém záznamu nebo zkontrolujte konfiguraci odpovídajícího atributu, abyste zajistili, že požadovaná pole nebudou vynechána. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) o konfiguraci odpovídajících atributů.|
|Atribut schématu nebyl nalezen. |Operaci nelze provést, protože byl zadán atribut, který v cílové aplikaci neexistuje. Podívejte se do [dokumentace](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) o přizpůsobení atributů a ujistěte se, že vaše konfigurace je správná.|
|InternalError |V rámci zřizovací služby Azure AD došlo k vnitřní chybě služby. Nedá se nic dělat. Tento pokus bude automaticky opakován za 40 minut.|
|Neplatná doména |Operaci nelze provést z důvodu hodnoty atributu obsahující neplatný název domény. Aktualizujte název domény uživatele nebo jej přidejte do povoleného seznamu v cílové aplikaci. |
|Časový limit |Operaci nelze dokončit, protože odpověď na cílovou aplikaci trvala příliš dlouho. Nedá se nic dělat. Tento pokus bude automaticky opakován za 40 minut.|
|Překročil limit licence.|Uživatele nelze vytvořit v cílové aplikaci, protože pro tohoto uživatele nejsou k dispozici žádné licence. Buď obkupte další licence pro cílovou aplikaci, nebo zkontrolujte přiřazení uživatelů a konfiguraci mapování atributů, abyste zajistili, že budou správným uživatelům přiřazeni správné atributy.|
|Duplicitní cílové položky  |Operaci nelze dokončit, protože bylo nalezeno více uživatelů v cílové aplikaci s nakonfigurovanými odpovídajícími atributy. Odeberte duplicitního uživatele z cílové aplikace nebo změňte konfiguraci mapování atributů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|
|DuplikátSourceEntries | Operaci nelze dokončit, protože bylo nalezeno více uživatelů s nakonfigurovanými odpovídajícími atributy. Odeberte duplicitního uživatele nebo změňte konfiguraci mapování atributů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|

## <a name="next-steps"></a>Další kroky

* [Kontrola stavu zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problém s konfigurací zřizování uživatelů do aplikace Azure AD Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


