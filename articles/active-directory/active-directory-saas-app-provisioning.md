---
title: Automatizované SaaS zřizování uživatelů aplikace ve službě Azure AD | Microsoft Docs
description: Úvod do Azure AD můžete použít jak pro automatické zřizování, zrušit a průběžně aktualizovat uživatelské účty napříč různými aplikacemi SaaS jiných výrobců.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/07/2018
ms.author: asmalser
ms.openlocfilehash: 6189038a338a9151b23dbdad11d86e43709a96a0
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247940"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizovat uživatele zajišťování a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Co je automatické zřizování uživatelů pro aplikace SaaS?
Azure Active Directory (Azure AD) umožňuje automatizovat vytváření, údržbu a odebírání uživatelských identit v cloudu ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) aplikace, jako je Dropbox, Salesforce, ServiceNow a další.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Tady jsou některé příklady co tato funkce umožňuje, abyste mohli provádět:**

* Automaticky vytvořte nové účty v pravém systémy pro nové osoby, při připojí váš tým nebo organizace.
* Automaticky deaktivujte účtů v pravém systémech při osoby nechte tým nebo organizace.
* Zajišťují, aby identity aplikace a systémy aktuální na základě změn v adresáři nebo systému lidských zdrojů.
* Zřídit neuživatelských objekty, například skupin, k aplikacím, které je podporují.

**Zřizování uživatelů automatizované také zahrnuje následující funkce:**

* Možnost tak, aby odpovídala stávající identit mezi zdrojovým a cílovým systémem.
* Mapování přizpůsobitelný atribut, které definují, jaká data uživatel by měl toku ze zdrojového systému do cílového systému.
* Volitelné e-mailové výstrahy pro zřizování chyby
* Vytváření sestav a aktivity protokolů, které pomáhají s monitorování a řešení potíží.

## <a name="why-use-automated-provisioning"></a>Proč používat automatického zřizování?
Některé běžné motivace pro použití této funkce patří:

* Zamezení náklady, umožňuje zvýšit efektivitu a lidské chyby související s procesy ručního zřizování.
* Zamezení nákladů spojených s hostování a údržbu zákaznických zřizování řešení a skripty
* Chcete-li okamžitě odebrání identity uživatelů z klíče aplikace SaaS, když opustí organizaci zabezpečit vaši organizaci.
* Snadno importovat velký počet uživatelů do určité aplikaci SaaS nebo systému.
* Abyste mohli využívat s jedinou sadu zásady určující, který je zřízený a který můžete přihlásit k aplikaci.


## <a name="how-does-automatic-provisioning-work"></a>Jak funguje automatické zřizování
    
**Zřizování služby Azure AD** zřídí uživatelům aplikace SaaS a dalšími systémy připojením ke koncovým bodům uživatelské rozhraní API pro správu poskytnutý dodavatelem každé aplikace. Rozhraní API koncových bodů pro tyto uživatele správu povolit službě Azure AD prostřednictvím kódu programu vytvářet, aktualizovat a odebírat uživatele. Pro vybrané aplikace, zřizování služby mohou také vytvářet aktualizovat a odebírat další spojené s objekty, například skupin a rolí. 

![Zřizování](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*obrázek 1: Zřizování služby Azure AD*

![Odchozí zřizování](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*obrázek 2: "Odchozí" uživatele zřizování pracovního postupu z Azure AD oblíbených aplikací SaaS.*

![Příchozí zřizování](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*obrázek 3: zřizování pracovního postupu z oblíbených aplikací lidského kapitálové správy (HCM) do Azure Active Directory a Windows Server Active Directory uživatelů "Příchozí"*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jaké aplikace a systémy lze použít při zřizování automatické uživatele Azure AD?

Funkce Azure AD předem integrované podpory pro různé oblíbených aplikací SaaS a systémy lidských zdrojů, jakož i obecné podporu pro aplikace, které implementují konkrétní části standardní SCIM 2.0.

Seznam všech aplikací, pro kterou Azure AD podporuje předem integrovaných zřizování konektor, naleznete [seznam kurzů aplikace pro zřizování uživatelů](active-directory-saas-tutorial-list.md).

Informace o tom, jak přidat podporu pro Azure AD zřizování uživatelů k aplikaci najdete v tématu [pomocí SCIM pro automatické zřizování uživatelů a skupin ze služby Azure Active Directory k aplikacím](active-directory-scim-provisioning.md).

Kontaktujte Azure AD technickému týmu požádat o zřizování podporu dalších aplikací, odeslání zprávy prostřednictvím [fóru pro zpětnou vazbu Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).    

> [!NOTE]
> Aby aplikace pro podporu zřizování automatizované uživatelů se musí nejprve poskytování správy potřebné uživatelské rozhraní API umožňující pro externí programy automatizovat vytváření, údržbu a odebírání uživatelů. Ne všechny aplikace SaaS jsou proto kompatibilní s touto funkcí. Pro aplikace, které podporují rozhraní API správy uživatelů technický tým služby Azure AD bude moct vytvářet zřizování konektor pro tyto aplikace a činnost prioritu podle potřeb aktuální a potenciální zákazníky. 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Jak je nastavit automatické zřizování pro aplikace?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Konfigurace služby Azure AD pro vybrané aplikace se spustí v zřizování služby  **[portál Azure](https://portal.azure.com)**. V **Azure Active Directory > podnikové aplikace, které** vyberte **přidat**, pak **všechny**a poté přidejte následující v závislosti na vašem scénáři:

* Všechny aplikace v **vybrané aplikace** části Podpora automatické zřizování. Najdete v článku [seznam kurzů aplikace pro zřizování uživatelů](active-directory-saas-tutorial-list.md) pro další pravidla.

* Pomocí možnosti "bez Galerie aplikace" pro zákaznických SCIM integrace

![Galerie](./media/active-directory-saas-app-provisioning/gallery.png)

Na obrazovce správy aplikací, zřizování je nakonfigurovaný v **zřizování** kartě.

![Nastavení](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **Přihlašovací údaje správce** je třeba zadat do služby Azure AD zřizování služby, který vám umožní připojení k rozhraní API poskytuje aplikace pro správu uživatelů. Tato část také umožňuje povolit e-mailová oznámení, pokud přihlašovací údaje selhat nebo zřizování úloha přejde do [karantény](#quarantine).

* **Mapování atributů** lze nakonfigurovat, zadejte, který polí ve zdrojovém systému (Příklad: Azure AD) bude jejich obsah synchronizují do pole, která v cílovém systému (Příklad: ServiceNow). Pokud cílová aplikace podporuje, v této části vám umožní Volitelně lze konfigurovat zřizování skupin kromě uživatelských účtů. "Odpovídající vlastnosti" umožňují vyberte pole, která se používají tak, aby odpovídaly účty mezi systémy. "[Výrazy](active-directory-saas-writing-expressions-for-attribute-mappings.md)" vám umožní změnit a transformace hodnot načten ze zdrojového systému, než se zapisují do cílového systému. Další informace najdete v tématu [přizpůsobení mapování atributů](active-directory-saas-customizing-attribute-mappings.md).

![Nastavení](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **Filtry oborů** říct zřizování služby, kteří uživatelé a skupiny ve zdrojovém systému by měl zřídit nebo zrušit do cílového systému. Existují dva aspekty oborů filtry, které jsou vyhodnocovány společně určující, který je v oboru pro zřizování:

    * **Filtr pro hodnoty atributu** -nabídky "Zdrojového objektu obor" v mapování atributů umožňuje filtrování na konkrétní atribut hodnoty. Například můžete zadat, že by měl být jenom uživatelé s atributem "Oddělení" "Prodeje" v oboru pro zřizování. Další informace najdete v tématu [pomocí oboru filtrů](active-directory-saas-scoping-filters.md).

    * **Filtr pro přiřazení** -nabídky "Obor" v funkce zřizování > portálu v oddílu nastavení umožňuje určit, jestli by měl být pouze "přiřazené" Uživatelé a skupiny v oboru pro zřizování, nebo pokud by měly být všechny uživatele v adresáři služby Azure AD zřízení. Informace o "přiřazení" uživatelů a skupin najdete v tématu [přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* **Nastavení** řízení operaci zřizování služby pro aplikaci, včetně toho, jestli je aktuálně spuštěna, nebo ne.

* **Protokoly auditu** zadejte záznamy každé operace provádí zřizování služby Azure AD. Další podrobnosti najdete v tématu [zřizování průvodce vytvářením sestav](active-directory-saas-provisioning-reporting.md).

![Nastavení](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> Zřizování služby uživatelů Azure AD je také možné nakonfigurovat a spravovat pomocí [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Co se stane při zřizování?

Pokud Azure AD je zdrojovém systému, služba zřizování používá [rozdílové dotazu funkce Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) monitorování uživatelů a skupin. Zřizování služby spustí počáteční synchronizaci před systém zdrojového a cílového systému, za nímž následuje pravidelné přírůstkové synchronizace. 

### <a name="initial-sync"></a>Počáteční synchronizace
Při spuštění zřizování služby, bude první synchronizace někdy provést:

1. Dotaz na všechny uživatele a skupiny ze zdrojového systému, všechny atributy, které jsou definované v načítání [mapování atributů](active-directory-saas-customizing-attribute-mappings.md).
2. Filtrování uživatelů a skupin, vrátí, pomocí kteréhokoli nakonfigurované [přiřazení](active-directory-coreapps-assign-user-azure-portal.md) nebo [na základě atributů filtry oborů](active-directory-saas-scoping-filters.md).
3. Pokud uživatel je nalezen přiřazení nebo v oboru pro zřizování, dotazuje službu cílovém systému pro odpovídající uživatele s využitím určené [odpovídající atributy](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties). Příklad: Pokud je odpovídající atribut název userPrincipal ve zdrojovém systému který mapuje uživatelské jméno v cílovém systému a pak službu zřizování dotazuje cílovém systému pro uživatelská jména, které se shodují s hodnotami název userPrincipal ve zdrojovém systému.
4. Pokud odpovídající uživatel nebyl nalezen v cílovém systému, je vytvořen pomocí atributů vrácených ze zdrojového systému.
5. Pokud je nalezen odpovídající uživatele, jsou aktualizovány pomocí atributů poskytované zdrojovém systému.
6. Pokud mapování atributů obsahovat atributy typu "odkaz", služba provede další aktualizace v cílovém systému vytvořit a propojit odkazované objekty. Uživatel například může mít atribut "Manager" v cílovém systému, který je propojený s jiným uživatelem vytvořené v cílovém systému.
7. Zachovat vodoznak na konci počáteční synchronizace, která poskytuje výchozí bod pro následné přírůstkové synchronizace.

Některé aplikace, jako je podpora ServiceNow, Google Apps a pole pouze zřizování uživatelů, ale také zřizování skupiny a jejich členové. V těchto případech, pokud v zřizování skupiny je povolené [mapování](active-directory-saas-customizing-attribute-mappings.md), zřizování služby synchronizuje uživatelé a skupiny a následně synchronizuje členství ve skupinách. 

### <a name="incremental-syncs"></a>Přírůstkové synchronizace
Po počáteční synchronizaci budou všechny následné synchronizace:

1. Dotaz na zdrojovém systému pro všechny uživatele a skupiny, které byly aktualizovány od posledního vodoznak uložil.
2. Filtrování uživatelů a skupin, vrátí, pomocí kteréhokoli nakonfigurované [přiřazení](active-directory-coreapps-assign-user-azure-portal.md) nebo [na základě atributů filtry oborů](active-directory-saas-scoping-filters.md).
3. Pokud uživatel je nalezen přiřazení nebo v oboru pro zřizování, dotazuje službu cílovém systému pro odpovídající uživatele s využitím určené [odpovídající atributy](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties).
4. Pokud odpovídající uživatel nebyl nalezen v cílovém systému, je vytvořen pomocí atributů vrácených ze zdrojového systému.
5. Pokud je nalezen odpovídající uživatele, jsou aktualizovány pomocí atributů poskytované zdrojovém systému.
6. Pokud mapování atributů obsahovat atributy typu "odkaz", služba provede další aktualizace v cílovém systému vytvořit a propojit odkazované objekty. Uživatel například může mít atribut "Manager" v cílovém systému, který je propojený s jiným uživatelem vytvořené v cílovém systému.
7. Pokud uživatel, který byl dříve v oboru pro zřizování je odebrán z oboru (včetně se nepřiřazené), služba zakáže uživatele v cílovém systému prostřednictvím aktualizace.
8. Pokud uživatel, který byl dříve v oboru pro zřizování je zakázaná nebo obnovitelné odstranění ve zdrojovém systému, služby zakáže uživatele v cílovém systému prostřednictvím aktualizace.
9. Pokud je uživatel, která byla dříve v oboru pro zřizování pevný odstranit ve zdrojovém systému, služba odstraní uživatele v cílovém systému. Ve službě Azure AD Uživatelé jsou pevné odstranit 30 dní po jsou dočasně odstraněné.
10. Zachovat nové vodoznak na konci přírůstkové synchronizace, která poskytuje výchozí bod pro následné přírůstkové synchronizace.

>[!NOTE]
> Volitelně můžete zakázat vytvoření, aktualizace a operacemi odstranění pomocí **cíle akce objektu** zaškrtávací políčka v [mapování atributů](active-directory-saas-customizing-attribute-mappings.md) části. Logika pro zakázání uživatele během aktualizace se také řídí prostřednictvím mapování atributů z pole, jako je například "accountEnabled".

Zřizování služby bude dál běžet back-to-back přírůstkové synchronizace dobu neurčitou, v intervalech definovaných v [kurzu specifické pro každou aplikaci](active-directory-saas-tutorial-list.md), dokud nenastane některá z následujících událostí:

* Je služba ručně zastavená pomocí webu Azure portal nebo použitím příkazu příslušné rozhraní Graph API 
* Nové počáteční synchronizace se aktivuje pomocí **Vymazat stav a restartujte** možnost na webu Azure portal nebo použitím příkazu příslušné rozhraní Graph API. To vymaže všechny uložené vodoznak a způsobí, že všem objektům zdrojové vyhodnocované znovu.
* Nové počáteční synchronizace se aktivuje z důvodu změn v mapování atributů nebo oboru filtrů. To také vymaže všechny uložené vodoznak a způsobí, že všem objektům zdrojové vyhodnocované znovu.
* Proces zajišťování přejde do karantény (viz níže) z důvodu míra vysoké chyb a zůstane v umístění do karantény pro více než čtyři týdny. V takovém případě bude služba automaticky zakázána.

### <a name="errors-and-retries"></a>Chyby a opakovaných pokusů 
Pokud jednotlivého uživatele nelze přidat, aktualizovat nebo odstranit v cílovém systému z důvodu chyby v cílovém systému, bude operaci opakovat v příštím synchronizačním cyklu. Pokud uživatel dále nedaří, opakované pokusy zahájíte proběhnout snížené frekvencí, postupně škálování zpět na právě jeden pokus za den. Chcete-li tento problém vyřešen, musí správci zkontrolovat [protokoly auditu](active-directory-saas-provisioning-reporting.md) pro "proces úschově" událostí k určení kořenové způsobit a proveďte příslušnou akci. Běžné chyby mohou zahrnovat:

* Uživatelé, které nemají atribut vyplní ve zdrojovém systému, který je požadován v cílovém systému
* Uživatelé s hodnota atributu ve zdrojovém systému, pro které je jedinečné omezení v cílovém systému a stejnou hodnotu nachází v jiné uživatelský záznam

Tyto chyby lze vyřešit nastavením hodnoty atributu pro ovlivněného uživatele ve zdrojovém systému, nebo úpravě mapování atributů není způsobit konflikty.   

### <a name="quarantine"></a>Umístění do karantény
Pokud většinu nebo všechny z volání směřovaný na cílovém systému konzistentně nezdaří z důvodu chyby (například jako v případě přihlašovací údaje správce neplatný), pak úlohy zřizování přejde do stavu "umístit do karantény". To je uvedené v [zřizování souhrnnou sestavu](active-directory-saas-provisioning-reporting.md)a prostřednictvím e-mailu, pokud e-mailová oznámení byly nakonfigurované na webu Azure portal. 

Když do karantény, četnost přírůstkové synchronizace postupně snížena jednou za den. 

Úlohy zřizování se odeberou z karantény po všech problematické chyb opravována a spustí příštím synchronizačním cyklu. Je-li úlohu zřizování do karantény pro více než čtyři týdny, zřizování úloha je zakázána.


## <a name="how-long-will-it-take-to-provision-users"></a>Jak dlouho bude trvat zřízení uživatelů?

Výkon závisí na tom, jestli je vaše úlohy zřizování provádění počáteční synchronizace nebo přírůstkové synchronizace, jak je popsáno v předchozí části.

Pro **počáteční synchronizace**, čas úlohy závisí na různých faktorech, například počet uživatelů a skupin v oboru pro zřizování a celkový počet uživatelů a skupin ve zdrojovém systému. Úplný seznam faktorů, které mají vliv na výkon počáteční synchronizace jsou shrnuté později v této části.

Pro **přírůstkové synchronizace**, čas úlohy závisí na počtu změn v tomto synchronizačním cyklu. Pokud jsou méně než 5 000 uživatelů nebo změn členství ve skupinách, můžete dokončit úlohy v rámci jedné přírůstkové synchronizace cyklu. 

Následující tabulka shrnuje časů synchronizace pro běžné scénáře zřizování. V těchto scénářích zdrojovém systému je Azure AD a cílovém systému je k aplikaci SaaS. Časy synchronizace jsou odvozeny od statistické analýzy úloh synchronizace pro aplikace SaaS ServiceNow, síti na pracovišti, Salesforce a Google Apps.


| Konfigurace oboru | Uživatelům, skupinám a členům v oboru | Počáteční synchronizaci času | Čas přírůstkové synchronizace |
| -------- | -------- | -------- | -------- |
| Přiřadit uživatele a skupiny pouze synchronizace |  < 1 000 |  < 30 minut | < 30 minut |
| Přiřadit uživatele a skupiny pouze synchronizace |  1,000 - 10,000 | 142 - 708 minut | < 30 minut |
| Přiřadit uživatele a skupiny pouze synchronizace |   10 000 - 100 000 | 1,170 - 2,340 minut | < 30 minut |
| Synchronizaci všech uživatelů a skupin ve službě Azure AD |  < 1 000 | < 30 minut  | < 30 minut |
| Synchronizaci všech uživatelů a skupin ve službě Azure AD |  1,000 - 10,000 | < 30 120 minut | < 30 minut |
| Synchronizaci všech uživatelů a skupin ve službě Azure AD |  10 000 - 100 000  | 713 - 1,425 minut | < 30 minut |
| Synchronizace všech uživatelů ve službě Azure AD|  < 1 000  | < 30 minut | < 30 minut |
| Synchronizace všech uživatelů ve službě Azure AD | 1,000 - 10,000  | 43 - 86 minut | < 30 minut |


Pro konfiguraci **synchronizaci přiřadit uživatele a skupiny pouze**, následující vzorce slouží k určení přibližnou minimální a maximální očekává **počáteční synchronizace** časy:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Souhrn faktorů, které ovlivňují doby potřebné k dokončení **počáteční synchronizace**:

* Celkový počet uživatelů a skupin v oboru pro zřizování

* Celkový počet uživatelů, skupin a členy skupiny nachází ve zdrojovém systému (Azure AD)

* Uživatelé v oboru pro zřizování, jestli se shodují se stávající uživatelé v cílové aplikaci nebo třeba vytvořit první. Úloh synchronizace, pro které všichni uživatelé jsou vytvořeni poprvé trvat přibližně *dvakrát stejně dlouho* jako synchronizovat úlohy, pro které se shodují všichni uživatelé se stávající uživatele.

* Počet chyb v [protokoly auditu](active-directory-saas-provisioning-reporting.md). Výkon je nižší, pokud je mnoho chyb a zřizování služby přešel do stavu umístění do karantény   

* Požádat o omezení přenosové rychlosti a omezování implementována cílovým systémem. Některé cílové systémy implementují omezení míry požadavků a omezení, což může ovlivnit výkon při velkých synchronizační operace. Za těchto podmínek aplikaci, která obdrží příliš rychlé příliš mnoho žádostí o mohou zpomalit jeho rychlost odezvy nebo ukončení připojení. Pokud chcete zvýšit výkon, je potřeba upravit pomocí není rychleji, než aplikace dokáže zpracovat zasílání požadavků aplikace konektor. Zřizování konektory vytvořené Microsoft učinit této úpravy. 

* Počet a velikost přiřazených skupin. Synchronizuje se přiřazeny skupiny trvá déle než synchronizaci uživatelů. Počet a velikost přiřazené skupiny dopad na výkon. Pokud má aplikace [mapování povolena pro skupinu objekt synchronizace](active-directory-saas-customizing-attribute-mappings.md#editing-group-attribute-mappings), vlastnosti skupiny jako názvy skupin a členství ve skupinách se synchronizují kromě uživatelů. Tyto další synchronizace bude trvat déle než jenom synchronizaci uživatelských objektů.
 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Jak můžete sledovat průběh úlohy aktuální zřizování?**

Najdete v článku [zřizování průvodce vytvářením sestav](active-directory-saas-provisioning-reporting.md).

**Jak poznám, že pokud uživatelům nedaří získat zřízený správně?**

Všechny chyby se zaznamenávají do Azure AD protokoly auditu. Další informace najdete v tématu [zřizování průvodce vytvářením sestav](active-directory-saas-provisioning-reporting.md).

**Tom, jak můžete sestavit aplikace, která spolupracuje se službou zřizování?**

V tématu [pomocí SCIM pro automatické zřizování uživatelů a skupin ze služby Azure Active Directory k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

**Jak mohu odeslat zpětnou vazbu technickému týmu?**

Kontaktujte nás prostřednictvím [fóru pro zpětnou vazbu Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Související články
* [Seznam kurzů k integraci aplikací SaaS](active-directory-saas-tutorial-list.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](active-directory-saas-customizing-attribute-mappings.md)
* [Zapisují se výrazy pro mapování atributů](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtry pro zřizování uživatelů oborů](active-directory-saas-scoping-filters.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](active-directory-scim-provisioning.md)
* [Přehled synchronizace rozhraní API služby Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
* [Podrobný postup nasazení plán pro zřizování odchozí uživatelů aplikace](https://aka.ms/userprovisioningdeploymentplan)

