---
title: Automatické SaaS zřizování uživatelů aplikace ve službě Azure AD | Dokumentace Microsoftu
description: Úvod k použití služby Azure AD k automatickému zřízení, zrušení zřízení a průběžně aktualizovat uživatelské účty napříč různými aplikacemi SaaS třetích stran.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8b099f845df66dfe8c43bc6f968fd63b30d09d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186348"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizace zřizování a jeho rušení pro aplikace SaaS ve službě Azure Active Directory

## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Co je automatické zřizování uživatelů pro aplikace SaaS?
Azure Active Directory (Azure AD) umožňuje automatizovat vytváření, údržbu a odebírání uživatelských identit v cloudu ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) aplikace, jako je Dropbox, Salesforce, ServiceNow a další.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Níže je několik příkladů co tato funkce umožňuje, můžete s nimi dělat:**

* Automaticky vytvoří nové účty v pravém systémy pro nové uživatele, když se připojí k váš tým nebo organizace.
* V pravém systémy automaticky deaktivujte účty, když lidé opouštějí tým nebo organizace.
* Ujistěte se, že identit ve vašich aplikacích a systémy jsou tak pořád aktuální na základě změn v adresáři nebo systému lidských zdrojů.
* Zřízení neuživatelských objekty, například skupin, do aplikací, které je podporují.

**Zřizování uživatelů automatizované také zahrnuje následující funkce:**

* Možnost tak, aby odpovídala stávající identit mezi zdrojovým a cílovým systémem.
* Přizpůsobitelný atribut mapování, které definují, jaká data uživatele jakým způsobem se předávají ze zdrojového systému do cílového systému.
* Volitelná e-mailová oznámení pro chyby zřizování.
* Vytváření sestav a protokolů aktivit usnadňující sledování a řešení potíží.

## <a name="why-use-automated-provisioning"></a>Proč používat automatického zřizování?

Některé běžné motivace pro použití této funkce patří:

* Jak se vyhnout náklady, umožňuje zvýšit efektivitu a lidské chyby spojené s ručními procesy pro zřizování.
* Jak se vyhnout náklady spojené s hostitelem a vlastní řešení pro zřizování a skripty.
* Chcete-li zabezpečit svou organizaci odebráním okamžitě identit uživatelů z klíčových aplikací SaaS při opuštění organizace.
* Snadno importovat velký počet uživatelů v určité aplikaci SaaS nebo systému.
* Chcete-li využívat, s jedinou sadu zásady určující, který je zřízený a který můžete přihlásit k aplikaci.

## <a name="how-does-automatic-provisioning-work"></a>Jak funguje automatické zřizování
    
**Služba zřizování Azure AD** zřizuje uživatele aplikací SaaS a dalšími systémy připojením ke koncovým bodům uživatelské rozhraní API pro správu vám poskytne dodavatel každé aplikace. Tyto koncové body rozhraní API management uživatele povolit Azure AD prostřednictvím kódu programu vytvořit, aktualizovat a odebírat uživatele. Pro vybrané aplikace služby zřizování také vytvářet, aktualizovat a odebírat další objekty souvisejícím s identitou, například skupin a rolí. 

![Zřizování](./media/user-provisioning/provisioning0.PNG)
*obrázek 1: Služba zřizování Azure AD*

![Odchozí zřizování](./media/user-provisioning/provisioning1.PNG)
*obrázek 2: "Odchozí" uživatele, zřizování pracovního postupu ze služby Azure AD k oblíbeným aplikacím SaaS*

![Příchozí zřizování](./media/user-provisioning/provisioning2.PNG)
*obrázek 3: "Příchozí" uživatel zřizování pracovního postupu z oblíbených aplikací lidského kapitálu správy (HCM) do Azure Active Directory a Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jaké aplikace a systémy můžete použít s Azure AD automatické zřizování uživatelů?

Funkce Azure AD předem integrovaných podporu pro širokou škálu oblíbených aplikací SaaS a systémy lidských zdrojů, jakož i obecných podporu pro aplikace, které implementují konkrétní části standard SCIM 2.0.

### <a name="pre-integrated-applications"></a>Předem integrovaných aplikací

Seznam všech aplikací, pro který Azure AD podporuje předem integrované zřizování konektoru, najdete v článku [seznam kurzy k aplikacím pro zřizování uživatelů](../saas-apps/tutorial-list.md).

Kontaktování Azure AD inženýrský tým žádost o podporu dalších aplikací, zřizování odeslat zprávu přes [fóru pro zpětnou vazbu služby Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Aby aplikace k podpoře zřizování automatizované uživatelů musí nejprve poskytovat správu potřebné uživatelské rozhraní API, která umožňují externích programů k automatizaci vytváření, údržbu a odebírání uživatelů. Proto se některé aplikace SaaS jsou kompatibilní s touto funkcí. Pro aplikace, které podporují rozhraní API správy uživatelů technický tým služby Azure AD pak bude možné sestavit konektor těchto aplikací, zřizování a tuto práci je nastaveno jako prioritní podle potřeb aktuální a pomáhala potenciálním zákazníkům. 

### <a name="connecting-applications-that-support-scim-20"></a>Připojení aplikace s podporou SCIM 2.0

Informace o tom, jak se obecně připojit aplikace, které implementují SCIM 2.0 – Správa na základě uživatelského rozhraní API, najdete v článku [pomocí SCIM k automatickému zřízení uživatelů a skupin ze služby Azure Active Directory do aplikací](use-scim-to-provision-users-and-groups.md).

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Jak nastavit automatické zřizování pro aplikace

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Konfigurace služby zřizování pro vybrané aplikace se spustí v Azure AD  **[webu Azure portal](https://portal.azure.com)**. V **Azure Active Directory > podnikové aplikace** vyberte **přidat**, pak **všechny**a poté přidejte jednu z následujících v závislosti na vašem scénáři:

* Všechny aplikace v **vybrané aplikace** části podporu automatické zřizování. Zobrazit [seznam kurzy k aplikacím pro zřizování uživatelů](../saas-apps/tutorial-list.md) pro další pravidla.

* Pomocí možnosti "aplikaci mimo Galerii" pro vlastní integrace SCIM

![Galerie](./media/user-provisioning/gallery.png)

Na obrazovce správy aplikací, zřizování je nakonfigurovaný v **zřizování** kartu.

![Nastavení](./media/user-provisioning/provisioning_settings0.PNG)


* **Přihlašovací údaje správce** musí být zadaná do služby Azure AD, zřizovací služba, která umožní připojení k rozhraní API poskytuje aplikace pro správu uživatelů. Tato část také umožňuje povolit e-mailová oznámení, pokud selhání přihlašovací údaje nebo zřizování úloha přejde do [karantény](#quarantine).

* **Mapování atributů** lze nakonfigurovat, které určují, která pole ve zdrojovém systému (Příklad: Azure AD) budou mít jejich obsah synchronizovány do pole, která v cílovém systému (Příklad: ServiceNow). Pokud je cílová aplikace podporuje, v této části vám umožní volitelně konfigurace zřizování skupin kromě uživatelských účtů. "Odpovídající vlastnosti" umožňuje zvolit pole, která se používají tak, aby odpovídaly účty mezi systémy. "[Výrazy](functions-for-customizing-application-data.md)" umožňují upravovat a transformovat hodnoty získané z zdrojového systému, než jsou zapsány do cílového systému. Další informace najdete v tématu [přizpůsobení mapování atributů](customize-application-attributes.md).

![Nastavení](./media/user-provisioning/provisioning_settings1.PNG)

* **Filtry oborů** zjistit službu zřizování, kteří uživatelé a skupiny ve zdrojovém systému musí zřízení nebo zrušení zřízení cílového systému. Existují dva aspekty filtry, které jsou vyhodnocovány společně oborů, které definují, kdo je v oboru pro zřizování:

    * **Filtrováním podle hodnot atributů** -nabídky "Obor zdrojového objektu" v mapování atributů umožňuje filtrování podle hodnoty konkrétního atributu. Například můžete určit, že pouze uživatelé s atributem "Oddělení", "Prodeje" by měla být v oboru pro zřizování. Další informace najdete v tématu [pomocí filtrů oborů](define-conditional-rules-for-provisioning-user-accounts.md).

    * **Filtrováním podle přiřazení** -zajištěním z nabídky "Scope" > portálu v oddílu nastavení umožňuje určit, jestli by měl být pouze "přiřazení" Uživatelé a skupiny v oboru pro zřizování, nebo pokud by měl být všichni uživatelé v adresáři Azure AD zřízené. Informace o "přiřazení" uživatelů a skupin najdete v tématu [přiřadit podnikové aplikace v Azure Active Directory uživatele nebo skupinu](assign-user-or-group-access-portal.md).
    
* **Nastavení** řídí provoz služby zřizování pro aplikace, včetně toho, jestli je aktuálně spuštěna, nebo ne.

* **Protokoly auditu** obsahují záznamy každá operace provádí služba zřizování Azure AD. Další podrobnosti najdete v tématu [zřizování průvodce vytvářením sestav](check-status-user-account-provisioning.md).

![Nastavení](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Služba zřizování uživatelů Azure AD je také možné nakonfigurovat a spravovat pomocí [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Co se stane při zřizování?

Pokud Azure AD je systém pro zdrojový, zřizovací služba používá [rozdílový dotaz funkce služby Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) monitorování uživatelů a skupin. Zřizovací služba spustí počáteční synchronizace systému zdrojového a cílového systému, za nímž následuje pravidelné přírůstkové synchronizace. 

### <a name="initial-sync"></a>Počáteční synchronizace

Při spuštění služby zřizování, bude první synchronizace nikdy neprováděl:

1. Všichni uživatelé a skupiny ze zdrojového systému načtení všech atributů definovaných v dotazu [mapování atributů](customize-application-attributes.md).
2. Filtrovat uživatele a skupiny vrátila, některou nakonfigurované [přiřazení](assign-user-or-group-access-portal.md) nebo [založených na atributech filtry oborů](define-conditional-rules-for-provisioning-user-accounts.md).
3. Při přiřazení je najít uživatele nebo v oboru pro zřizování, dotazuje službu cílový systém, hledá odpovídající uživatele s využitím určené [shodné atributy](customize-application-attributes.md#understanding-attribute-mapping-properties). Příklad: Pokud je název userPrincipal ve zdrojovém systému odpovídajícím atributem a mapuje na uživatelské jméno v cílovém systému a pak službu zřizování dotazuje cílovém systému pro uživatelská jména, které odpovídají názvu hodnoty userPrincipal ve zdrojovém systému.
4. Pokud odpovídající uživatel nebyl nalezen v cílovém systému, je vytvořen s využitím atributů vrácených ze zdrojového systému. Po vytvoření uživatelského účtu službu zřizování detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele, který se používá k provádění všech budoucích operací na tohoto uživatele.
5. Pokud je nalezen odpovídající uživatele, je aktualizován pomocí atributů ve zdrojovém systému k dispozici. Po uživatelský účet je nalezena shoda, zřizovací služba detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele, který se používá k provádění všech budoucích operací na tohoto uživatele.
6. Pokud mapování atributů obsahovat atributy typu "odkaz", služba provede další aktualizace v cílovém systému k vytvoření a propojení odkazované objekty. Uživatel například může mít atribut "Správce" v cílovém systému, který je propojený s jiným uživatelem vytvořené v cílovém systému.
7. Zachovat horní meze na konci počáteční synchronizace, která poskytuje výchozí bod pro následné přírůstkové synchronizace.

Některé aplikace, jako je Box, ServiceNow a Google Apps podpora nejen zřizování uživatelů, ale také zřizování skupin a jejich členy. V těchto případech, pokud skupinové zřizování je povoleno v nástroji [mapování](customize-application-attributes.md), služba zřizování synchronizuje uživatele a skupiny a následně synchronizuje členství ve skupinách. 

### <a name="incremental-syncs"></a>Přírůstková synchronizace

Po počáteční synchronizaci budou všechny následné synchronizace:

1. Dotaz na zdrojový systém pro všechny uživatele a skupiny, které byly aktualizovány od posledního meze byla uložena.
2. Filtrovat uživatele a skupiny vrátila, některou nakonfigurované [přiřazení](assign-user-or-group-access-portal.md) nebo [založených na atributech filtry oborů](define-conditional-rules-for-provisioning-user-accounts.md).
3. Při přiřazení je najít uživatele nebo v oboru pro zřizování, dotazuje službu cílový systém, hledá odpovídající uživatele s využitím určené [shodné atributy](customize-application-attributes.md#understanding-attribute-mapping-properties).
4. Pokud odpovídající uživatel nebyl nalezen v cílovém systému, je vytvořen s využitím atributů vrácených ze zdrojového systému. Po vytvoření uživatelského účtu službu zřizování detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele, který se používá k provádění všech budoucích operací na tohoto uživatele.
5. Pokud je nalezen odpovídající uživatele, je aktualizován pomocí atributů ve zdrojovém systému k dispozici. Pokud se jedná o nově přiřazeným účet, který je nalezena shoda, zřizovací služba detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele, který se používá k provádění všech budoucích operací na tohoto uživatele.
6. Pokud mapování atributů obsahovat atributy typu "odkaz", služba provede další aktualizace v cílovém systému k vytvoření a propojení odkazované objekty. Uživatel například může mít atribut "Správce" v cílovém systému, který je propojený s jiným uživatelem vytvořené v cílovém systému.
7. Pokud uživatel, který byl dříve v oboru pro zřizování je odebrán z oboru (včetně se nepřiřazené), služba zakáže uživatele v cílovém systému prostřednictvím aktualizace.
8. Pokud uživatel, který byl dříve v oboru pro zřizování je zakázán nebo obnovitelně odstraněný ve zdrojovém systému, služba zakáže uživatele v cílovém systému prostřednictvím aktualizace.
9. Pokud je uživatel, který byl dříve v oboru pro zřizování pevné odstranit ve zdrojovém systému, služba odstraní uživatele v cílovém systému. Ve službě Azure AD Uživatelé jsou pevné odstranit 30 dnů po jejich obnovitelně odstraněný.
10. Zachovat novou mezí na konci Přírůstková synchronizace, která poskytuje výchozí bod pro následné přírůstkové synchronizace.

>[!NOTE]
> Volitelně můžete zakázat vytvoření, aktualizace nebo odstranění operace s použitím **cílové objektů akce** zaškrtávací políčka v [mapování atributů](customize-application-attributes.md) oddílu. Logika pro zakázání uživatele během aktualizace se také řídí mapování atribut z pole, jako je například "accountEnabled".

Služba zřizování budou i nadále po neomezenou dobu, spusťte back-to-back přírůstkové synchronizace v intervalech definovaných v [kurzu specifické pro danou aplikaci](../saas-apps/tutorial-list.md), dokud nenastane některá z následujících událostí:

* Tato služba ručně zastavená pomocí webu Azure portal nebo pomocí příkazu příslušné rozhraní Graph API 
* Nové počáteční synchronizace se aktivuje pomocí **Vymazat stav a restartovat** možnost na webu Azure Portal nebo pomocí příkazu příslušné rozhraní Graph API. To vymaže všechny uložené vodoznak a způsobí, že všem objektům zdrojové k vyhodnocení znovu.
* Nové počáteční synchronizace se aktivuje z důvodu změny mapování atributů nebo filtry oborů. To také vymaže všechny uložené vodoznak a způsobí, že všem objektům zdrojové k vyhodnocení znovu.
* Během procesu zřizování přejde do karantény (viz níže) z důvodu vysoké chybovost a zůstane v karanténě za více než čtyři týdny. V tomto případě služba se automaticky deaktivuje.

### <a name="errors-and-retries"></a>Chyby a opakované pokusy

Pokud jednotlivého uživatele nelze přidat, aktualizovat nebo odstranit v cílovém systému kvůli chybě v cílovém systému, se operaci opakovat v příštím cyklu synchronizace. Pokud uživatel ani potom nedaří, opakované pokusy začnou na nižší četnost, postupně škálování zpět do stačí jeden pokus za den. Chcete-li vyřešit chybu, musí správci zkontrolovat [protokoly auditu](check-status-user-account-provisioning.md) "podmíněné zpracování" událostí k určení kořenové způsobit a proveďte příslušnou akci. Běžné problémy mohou zahrnovat:

* Uživatelé nemají atribut naplnit ve zdrojovém systému, které je nutné v cílovém systému
* Uživatelé mají hodnotu atributu ve zdrojovém systému, pro které je jedinečné omezení v cílovém systému a stejnou hodnotu se nachází v jiném záznamu uživatele

Nastavením hodnoty atributů pro ovlivněného uživatele ve zdrojovém systému nebo úpravou mapování atributů není způsobit konflikty lze vyřešit tyto chyby.   

### <a name="quarantine"></a>Karanténa

Pokud většinu nebo všechny z volání směřovaný na cílovém systému konzistentně nezdaří z důvodu chyby (například v případě přihlašovacích údajů správce neplatný), pak zřizování úloha přejde do stavu "umístit do karantény". To je uvedeno v [zřizování souhrnnou sestavu](check-status-user-account-provisioning.md)a prostřednictvím e-mailu, pokud e-mailová oznámení nakonfigurované na webu Azure Portal. 

Když do karantény, frekvence přírůstkových synchronizací postupně sníží jednou za den. 

Úlohy zřizování se odebere z karantény po všechny problematické chyby, chybách a spustí další cyklus synchronizace. Je-li Úloha zřizování v karanténě za více než čtyři týdny, zřizování úloha je zakázána.


## <a name="how-long-will-it-take-to-provision-users"></a>Jak dlouho bude trvat nezajistíte zřízení uživatelů?

Výkon závisí na tom, jestli vaše úlohy zřizování provádí počáteční synchronizace nebo přírůstková synchronizace, jak je popsáno v předchozí části.

Pro **počáteční synchronizace**, čas úlohy závisí na celé řadě faktorů, včetně počtu uživatelů a skupin v oboru pro zřizování a celkový počet uživatelů a skupin ve zdrojovém systému. Úplný seznam faktorů, které ovlivňují výkon počáteční replikace jsou shrnuté později v této části.

Pro **přírůstkové synchronizace**, čas úlohy závisí na počtu změn v tento synchronizační cyklus. Pokud méně než 5 000 uživatelů nebo změn členství ve skupinách, můžete dokončit úlohy v rámci jednoho Přírůstková synchronizace cyklu. 

Následující tabulka shrnuje časů synchronizace pro běžné scénáře zřizování. V těchto scénářích zdrojového systému je Azure AD a cílovém systému je aplikace SaaS. Doba synchronizace jsou odvozeny z statistické analýzy úloh synchronizace pro aplikace SaaS, ServiceNow, síti na pracovišti, Salesforce a Google Apps.


| Konfigurace oboru | Uživatelé, skupiny a členové v oboru | Čas počáteční synchronizace | Přírůstková synchronizace |
| -------- | -------- | -------- | -------- |
| Přiřazení uživatelé a skupiny pouze synchronizovat |  < 1,000 |  < 30 minut | < 30 minut |
| Přiřazení uživatelé a skupiny pouze synchronizovat |  1,000 - 10,000 | 142 - minut 708 | < 30 minut |
| Přiřazení uživatelé a skupiny pouze synchronizovat |   10,000 - 100,000 | 1,170 - 2,340 minut | < 30 minut |
| Synchronizovat všechny uživatele a skupiny ve službě Azure AD |  < 1,000 | < 30 minut  | < 30 minut |
| Synchronizovat všechny uživatele a skupiny ve službě Azure AD |  1,000 - 10,000 | < 30-120 minut | < 30 minut |
| Synchronizovat všechny uživatele a skupiny ve službě Azure AD |  10,000 - 100,000  | 713 - 1,425 minut | < 30 minut |
| Synchronizovat všechny uživatele ve službě Azure AD|  < 1,000  | < 30 minut | < 30 minut |
| Synchronizovat všechny uživatele ve službě Azure AD | 1,000 - 10,000  | 43 - 86 minut | < 30 minut |


Pro konfiguraci **synchronizace přiřazené uživatele a skupiny pouze**, těchto vzorců můžete použít k určení přibližné minimální a maximální očekávané **počáteční synchronizace** časy:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Seznam faktorů, které ovlivňují čas potřebný k dokončení **počáteční synchronizace**:

* Celkový počet uživatelů a skupin v oboru pro zřizování.

* Celkový počet uživatelů, skupin a členů skupiny, které jsou k dispozici ve zdrojovém systému (Azure AD).

* Určuje, jestli uživatelé v oboru pro zřizování odpovídají existujícím uživatelům v cílové aplikaci nebo pokud potřebujete vytvořit první. Úlohy synchronizace pro které všichni uživatelé jsou vytvořeni poprvé trvat přibližně *dvakrát tak dlouho,* tak, jak synchronizovat úlohy, pro které všichni uživatelé budou odpovídat existujícím uživatelům.

* Počet chyb v [protokoly auditu](check-status-user-account-provisioning.md). Pokud je mnoho chyb a služby zřizování přešel do stavu karantény je pomalejší výkon.    

* Požádat o šířku pásma a omezení šířky pásma implementovány cílovým systémem. Některé cílové systémy implementovat omezení frekvence požadavků a omezení, což může ovlivnit výkon během operací velký synchronizace. Aplikace, která přijímá příliš mnoho požadavků příliš rychle za těchto podmínek může zpomalit jeho míry odezvy nebo uzavřením připojení. Kvůli zvýšení výkonu se konektoru je potřeba upravit odesláním žádosti o aplikace rychleji, než dokáže zpracovat aplikace. Zřizování konektory vytvořené microsoftem provést tuto úpravu. 

* Počet a velikost přiřazených skupin. Přiřazené skupiny synchronizace trvá déle než synchronizaci uživatelů. Počet a velikost přiřazených skupin dopad na výkon. Pokud má aplikace [mapování povolena pro skupinu synchronizace objektů](customize-application-attributes.md#editing-group-attribute-mappings)vlastnosti skupiny jako například názvy skupin a členství ve skupinách se synchronizují kromě uživatelů. Tyto další synchronizace bude trvat déle než pouze synchronizaci uživatelských objektů.


## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Jak poznám, že pokud uživatelé se nezřizují správně?

Všechny operace prováděné uživatelem služby zřizování se zaznamenávají ve službě Azure AD protokoly auditu. To zahrnuje všechny operace provedené na zdrojové a cílové systémy, jaká data uživatele se čteným nebo zapsaným během každé operace čtení a zápisu.

Informace o tom, jak čtení protokoly auditu na portálu Azure portal, najdete v článku [zřizování průvodce vytvářením sestav](check-status-user-account-provisioning.md).


## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Jak řešit potíže s zřizování uživatelů

Založené na scénářích informace o odstraňování potíží automatické zřizování uživatelů najdete v tématu [problémy konfigurace a zřizování uživatelů pro aplikaci](application-provisioning-config-problem.md).


## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Co jsou doporučené postupy pro zavádění automatické zřizování uživatelů?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Plán služby příklad podrobný postup nasazení pro zřizování odchozí uživatelů pro aplikace, najdete v článku [Identity – Průvodce nasazením pro zřizování uživatelů](https://aka.ms/userprovisioningdeploymentplan).

## <a name="more-frequently-asked-questions"></a>Další časté dotazy

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Podporuje automatické zřizování uživatelů pro SaaS aplikace pracují s uživatele B2B ve službě Azure AD?

Ano, je možné použít uživatele Azure AD zřizování uživatelů služby zřizování B2B (nebo hostovaný) ve službě Azure AD k aplikacím SaaS.

Pro uživatele B2B bude moct přihlásit k aplikaci SaaS pomocí Azure AD, ale SaaS aplikace musí mít jeho založené na SAML jednotné přihlašování nakonfigurovanou schopnost určitým způsobem. Další informace o tom, jak nakonfigurovat aplikace SaaS s podporou přihlášení od uživatele B2B najdete v tématu [konfigurace SaaS aplikace pro spolupráci B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Podporuje automatické zřizování uživatelů pro SaaS aplikace pracují s dynamickými skupinami ve službě Azure AD?

Ano. Když nakonfigurovaný "synchronizovat jenom přiřazené uživatelům a skupinám", služba zřizování uživatelů Azure AD můžete zřizovat nebo rušit přístup uživatelů v aplikaci SaaS v závislosti na Určuje, jestli jsou členové [dynamická skupina](../users-groups-roles/groups-create-rule.md). Dynamické skupiny pracovat i s možností "se synchronizují všichni uživatelé a skupiny".

Použití dynamických skupin může mít vliv na celkový výkon začátku do konce zřizování uživatelů z Azure AD pro aplikace SaaS. Při použití dynamické skupiny, prosím mějte tyto upozornění a doporučení:

* Jak je rychle uživatele v dynamické skupině zřízení nebo zrušení zřízení v aplikaci SaaS, závisí na jak rychle vyhodnotit změny členství dynamické skupiny. Informace o tom, jak zkontrolovat stav zpracování dynamickou skupinu najdete v tématu [zkontrolovat stav zpracování pravidla členství](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule#check-processing-status-for-a-membership-rule).

* Při použití dynamické skupiny pravidel je třeba pečlivě zvážit s uživatelem, zřizování a zrušení zřizování v úvahu, zrušení zřízení události dojde ke ztrátě členství.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Podporuje automatické zřizování uživatelů pro SaaS aplikace pracují s vnořené skupiny ve službě Azure AD?

Ne. Při konfiguraci "synchronizovat jenom přiřazené uživatelům a skupinám", služba zřizování uživatelů Azure AD není schopná přečíst nebo zřídit uživatele, kteří jsou ve vnořené skupiny. Je moct číst a zřizování uživatelů, které jsou okamžitě členy skupiny explicitně přiřazeny.

Jedná se o omezení "na základě skupin přiřazení aplikací", což také ovlivní jednotného přihlašování a je popsána v [pomocí skupiny pro správu přístupu k aplikacím SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Jako alternativní řešení můžete by měly explicitně přiřadit (nebo jinak [oboru v](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) skupiny, které obsahují uživatele, kteří musí být zřízená.

## <a name="related-articles"></a>Související články

* [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
* [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](use-scim-to-provision-users-and-groups.md)
* [Přehled synchronizace rozhraní API služby Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
