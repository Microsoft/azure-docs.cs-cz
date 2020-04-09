---
title: Zjistěte, jak zřizování Azure AD funguje | Dokumenty společnosti Microsoft
description: Zjistěte, jak funguje zřizování Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 241d90981ed9ba54d253e6c22c00f9e5a9197863
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884881"
---
# <a name="how-provisioning-works"></a>Jak funguje zřizování

Automatické zřizování označuje vytváření identit uživatelů a rolí v cloudových aplikacích, ke kterým uživatelé potřebují přístup. Kromě vytváření identit uživatelů zahrnuje automatické zřizování údržbu a odebrání identit uživatelů při změně stavu nebo rolí. Než začnete nasazení, můžete si projít tento článek se dozvíte, jak funguje zřízení Azure AD a získat doporučení konfigurace. 

**Služba Azure AD Provisioning Service** zřazuje uživatele do aplikací SaaS a dalších systémů připojením k koncovému bodu rozhraní API pro správu uživatelů system for-Domain Identity Management (SCIM) 2.0, který poskytuje dodavatel aplikace. Tento koncový bod SCIM umožňuje Azure AD programově vytvářet, aktualizovat a odebírat uživatele. U vybraných aplikací může služba zřizování také vytvářet, aktualizovat a odebírat další objekty související s identitou, jako jsou skupiny a role. Kanál používaný pro zřizování mezi Azure AD a aplikací je šifrovaný pomocí šifrování HTTPS TLS.


![Azure AD zřizování služby](./media/how-provisioning-works/provisioning0.PNG)
*Obrázek 1: Služba zřizování Azure AD*

![Pracovní postup](./media/how-provisioning-works/provisioning1.PNG)
zřizování odchozích uživatelů*Obrázek 2: Pracovní postup zřizování uživatelů z Azure AD do oblíbených aplikací SaaS*

![Pracovní postup zřizování příchozích uživatelů](./media/how-provisioning-works/provisioning2.PNG)
*Obrázek 3: Pracovní postup zřizování uživatelů z oblíbených aplikací správy lidského kapitálu (HCM) do služby Azure Active Directory a služby Active Directory systému Windows Server*

## <a name="provisioning-using-scim-20"></a>Zřizování pomocí SCIM 2.0

Zřizovací služba Azure AD používá [protokol SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) pro automatické zřizování. Služba se připojuje ke koncovému bodu SCIM pro aplikaci a používá schéma objektů uživatele SCIM a rozhraní REST API k automatizaci zřizování a zrušení zřizování uživatelů a skupin. Konektor zřizování založený na SCIM je k dispozici pro většinu aplikací v galerii Azure AD. Při vytváření aplikací pro Azure AD můžou vývojáři pomocí rozhraní API pro správu uživatelů SCIM 2.0 vytvořit koncový bod SCIM, který integruje Azure AD pro zřizování. Podrobnosti naleznete [v tématu Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Pokud chcete požádat o automatický konektor zřizování Azure AD pro aplikaci, která ho momentálně nemá, vyplňte [žádost o aplikaci Azure Active Directory](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autorizace

Přihlašovací údaje jsou vyžadovány pro Azure AD pro připojení k rozhraní API pro správu uživatelů aplikace. Při konfiguraci automatického zřizování uživatelů pro aplikaci budete muset zadat platná pověření. Typy přihlašovacích údajů a požadavky pro aplikaci můžete najít odkazem na kurz aplikace. Na webu Azure Portal budete moct otestovat přihlašovací údaje tak, že se Azure AD pokusí připojit k zřizovací aplikaci aplikace pomocí zadaných přihlašovacích údajů.

Pokud saml založené jednotné přihlašování je také nakonfigurovaný pro aplikaci, Azure AD interní, na aplikaci limit úložiště je 1024 bajtů. Toto omezení zahrnuje všechny certifikáty, tajné tokeny, přihlašovací údaje a související konfigurační data přidružená k jedné instanci aplikace (označované také jako záznam instančního objektu ve službě Azure AD). Při konfiguraci jednotného přihlašování založenéna SAML, certifikát používaný k podepsání tokenů SAML často spotřebovává více než 50 % % místa. Všechny další položky (tajné tokeny, identifikátory URI, e-mailové adresy oznámení, uživatelská jména a hesla), které zadáte během nastavení zřizování uživatelů, mohou překročit limit úložiště. Další informace naleznete [v tématu Problém s ukládáním pověření správce při konfiguraci zřizování uživatelů](../manage-apps/application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Atributy mapování

Když povolíte zřizování uživatelů pro aplikaci SaaS jiného výrobce, portál Azure řídí jeho hodnoty atributů prostřednictvím mapování atributů. Mapování určují atributy uživatele, které toku mezi Azure AD a cílové aplikace při zřizování nebo aktualizaci uživatelských účtů.

Existuje předem nakonfigurovaná sada atributů a mapování atributů mezi uživatelskými objekty Azure AD a uživatelskými objekty každé aplikace SaaS. Některé aplikace spravují jiné typy objektů spolu s uživateli, například Skupiny.

Při nastavování zřizování je důležité zkontrolovat a nakonfigurovat mapování atributů a pracovní postupy, které definují, které vlastnosti uživatele (nebo skupiny) toku z Azure AD do aplikace. Zkontrolujte a nakonfigurujte odpovídající vlastnost **(Match objekty pomocí tohoto atributu),** který se používá k jednoznačné identifikaci a sladění uživatelů nebo skupin mezi dvěma systémy.

Výchozí mapování atributů můžete přizpůsobit podle vašich obchodních potřeb. Takže můžete změnit nebo odstranit existující mapování atributů nebo vytvořit nové mapování atributů. Podrobnosti naleznete v [tématu Přizpůsobení mapování atributů pro aplikace SaaS uživatelem](../manage-apps/customize-application-attributes.md).

Při konfiguraci zřizování do aplikace SaaS je jedním z typů mapování atributů, které můžete zadat, mapování výrazů. Pro tato mapování je nutné napsat výraz podobný skriptu, který umožňuje transformovat data uživatelů do formátů, které jsou přijatelnější pro aplikaci SaaS. Podrobnosti naleznete v [tématu Psaní výrazů pro mapování atributů](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Oboru 
### <a name="assignment-based-scoping"></a>Obory založené na přiřazení

Pro odchozí zřizování z Azure AD do aplikace SaaS, spoléhání se na [přiřazení uživatele nebo skupiny](../manage-apps/assign-user-or-group-access-portal.md) je nejběžnější způsob, jak určit, kteří uživatelé jsou v oboru pro zřizování. Vzhledem k tomu, že přiřazení uživatelů se také používají pro povolení jednotného přihlašování, stejnou metodu lze použít pro správu přístupu i zřizování. Obory založené na přiřazení se nevztahuje na příchozí zřizování scénáře, jako je Workday a Successfactors.

* **Skupiny.** S licenčním plánem Azure AD Premium můžete pomocí skupin přiřadit přístup k aplikaci SaaS. Potom, když je nastaven rozsah zřizování na **synchronizaci pouze přiřazené uživatele a skupiny**, služba zřizování Azure AD zřídí nebo deprovisioning uživatelů na základě toho, zda jsou členy skupiny, která je přiřazena k aplikaci. Samotný objekt skupiny není zřízen, pokud aplikace nepodporuje objekty skupiny. Ujistěte se, že skupiny přiřazené k vaší aplikaci mají vlastnost SecurityEnabled nastavenou na hodnotu False.

* **Dynamické skupiny.** Služba zřizování uživatelů Azure AD může číst a zřazovat uživatele v [dynamických skupinách](../users-groups-roles/groups-create-rule.md). Mějte na paměti tyto upozornění a doporučení:

  * Dynamické skupiny mohou ovlivnit výkon zřizování od začátku do konce z aplikací Azure AD do SaaS.

  * Jak rychle se uživatel v dynamické skupině zřídí nebo odloučí v aplikaci SaaS, závisí na tom, jak rychle dynamická skupina může vyhodnotit změny členství. Informace o kontrole stavu zpracování dynamické skupiny naleznete v tématu [Kontrola stavu zpracování pravidla členství](../users-groups-roles/groups-create-rule.md).

  * Když uživatel ztratí členství v dynamické skupině, je považován za událost zrušení zřizování. Zvažte tento scénář při vytváření pravidel pro dynamické skupiny.

* **Vnořené skupiny.** Služba zřizování uživatelů Azure AD nemůže číst nebo zřazovat uživatele ve vnořených skupinách. Služba může číst pouze a zřazovat uživatele, kteří jsou bezprostředními členy explicitně přiřazené skupiny. Toto omezení "přiřazení na základě skupiny k aplikacím" také ovlivňuje jednotné přihlašování (viz [Použití skupiny ke správě přístupu k aplikacím SaaS](../users-groups-roles/groups-saasapps.md)). Místo toho přímo přiřadit nebo jinak [oboru ve](define-conditional-rules-for-provisioning-user-accounts.md) skupinách, které obsahují uživatele, kteří potřebují být zřízena.

### <a name="attribute-based-scoping"></a>Obory založené na atributech 

Filtry oborů můžete použít k definování pravidel založených na atributech, která určují, kteří uživatelé jsou zřízeni do aplikace. Tato metoda se běžně používá pro příchozí zřizování z aplikací HCM do Azure AD a Active Directory. Obory filtry jsou nakonfigurované jako součást mapování atributů pro každý konektor zřizování uživatelů Azure AD. Podrobnosti o konfiguraci filtrů oborů založených na atributech naleznete v [tématu Zřizování aplikací založených na atributech pomocí filtrů oborů](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Uživatelé B2B (host)

Je možné použít službu zřizování uživatelů Azure AD pro zřizování uživatelů B2B (nebo host) uživatelů ve službě Azure AD do aplikací SaaS. Pro uživatele B2B k přihlášení k aplikaci SaaS pomocí Azure AD, aplikace SaaS musí mít své saml založené funkce jednotného přihlašování nakonfigurován určitým způsobem. Další informace o konfiguraci aplikací SaaS pro podporu přihlášení od uživatelů B2B najdete v [tématu Konfigurace aplikací SaaS pro spolupráci B2B](../b2b/configure-saas-apps.md).

Všimněte si, že userPrincipalName pro uživatele typu Host@domain.comje často uložen jako "alias#EXT# ". Pokud je userPrincipalName zahrnuta do mapování atributů jako zdrojový atribut, #EXT# je odebrán z userPrincipalName. Pokud požadujete, aby byl přítomen #EXT#, nahraďte userPrincipalName názvem originalUserPrincipalName jako zdrojový atribut. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Zřizovací cykly: Počáteční a přírůstkové

Když Azure AD je zdrojový systém, zřizovací služba používá [použít rozdílový dotaz ke sledování změn v datech Microsoft Graph](https://docs.microsoft.com/graph/delta-query-overview) u monitorovat uživatele a skupiny. Zřizovací služba spustí počáteční cyklus proti zdrojovému systému a cílovému systému, následovaný periodickými přírůstkovými cykly.

### <a name="initial-cycle"></a>Počáteční cyklus

Při spuštění zřizovací služby bude první cyklus:

1. Dotaz na všechny uživatele a skupiny ze zdrojového systému a načtení všech atributů definovaných v [mapování atributů](customize-application-attributes.md).

2. Umožňuje filtrovat vrácené uživatele a skupiny pomocí všech nakonfigurovaných [přiřazení](../manage-apps/assign-user-or-group-access-portal.md) nebo [filtrů oborů založených na atributech](define-conditional-rules-for-provisioning-user-accounts.md).

3. Pokud je uživateli přiřazen nebo v oboru pro zřizování, služba se dotazuje cílového systému pro odpovídajícího uživatele pomocí zadaných [odpovídajících atributů](customize-application-attributes.md#understanding-attribute-mapping-properties). Příklad: Pokud je jméno userPrincipal ve zdrojovém systému odpovídající atribut a mapuje se na userName v cílovém systému, pak služba zřizování dotazuje cílový systém pro userNames, které odpovídají hodnotám názvu userPrincipal ve zdrojovém systému.

4. Pokud odpovídající uživatel není nalezen v cílovém systému, je vytvořen pomocí atributů vrácených ze zdrojového systému. Po vytvoření uživatelského účtu služba zřizování detekuje a ukládá ID cílového systému pro nového uživatele. Toto ID se používá ke spuštění všech budoucích operací s tímto uživatelem.

5. Pokud je nalezen odpovídající uživatel, je aktualizován pomocí atributů poskytovaných zdrojovým systémem. Po spárování uživatelského účtu služba zřizování detekuje a ukládá ID cílového systému pro nového uživatele. Toto ID se používá ke spuštění všech budoucích operací s tímto uživatelem.

6. Pokud mapování atributů obsahuje atributy "reference", služba provádí další aktualizace v cílovém systému k vytvoření a propojení odkazovaných objektů. Uživatel může mít například atribut "Manager" v cílovém systému, který je propojen s jiným uživatelem vytvořeným v cílovém systému.

7. Zachovat vodoznak na konci počátečního cyklu, který poskytuje počáteční bod pro pozdější přírůstkové cykly.

Některé aplikace, jako je ServiceNow, G Suite a Box podporují nejen zřizování uživatelů, ale také zřizování skupin a jejich členů. V těchto případech pokud zřizování skupin [je povolena](customize-application-attributes.md)v mapování , zřizovací služba synchronizuje uživatele a skupiny a později synchronizuje členství ve skupinách.

### <a name="incremental-cycles"></a>Přírůstkové cykly

Po počátečním cyklu budou všechny ostatní cykly:

1. Dotaz zdrojového systému pro všechny uživatele a skupiny, které byly aktualizovány od posledního vodoznaku byl uložen.

2. Umožňuje filtrovat vrácené uživatele a skupiny pomocí všech nakonfigurovaných [přiřazení](../manage-apps/assign-user-or-group-access-portal.md) nebo [filtrů oborů založených na atributech](define-conditional-rules-for-provisioning-user-accounts.md).

3. Pokud je uživateli přiřazen nebo v oboru pro zřizování, služba se dotazuje cílového systému pro odpovídajícího uživatele pomocí zadaných [odpovídajících atributů](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Pokud odpovídající uživatel není nalezen v cílovém systému, je vytvořen pomocí atributů vrácených ze zdrojového systému. Po vytvoření uživatelského účtu služba zřizování detekuje a ukládá ID cílového systému pro nového uživatele. Toto ID se používá ke spuštění všech budoucích operací s tímto uživatelem.

5. Pokud je nalezen odpovídající uživatel, je aktualizován pomocí atributů poskytovaných zdrojovým systémem. Pokud se jedná o nově přiřazený účet, který je spárován, služba zřizování detekuje a ukládá ID cílového systému pro nového uživatele. Toto ID se používá ke spuštění všech budoucích operací s tímto uživatelem.

6. Pokud mapování atributů obsahuje atributy "reference", služba provádí další aktualizace v cílovém systému k vytvoření a propojení odkazovaných objektů. Uživatel může mít například atribut "Manager" v cílovém systému, který je propojen s jiným uživatelem vytvořeným v cílovém systému.

7. Pokud uživatel, který byl dříve v oboru pro zřizování je odebrán z oboru, včetně nepřiřazené, služba zakáže uživatele v cílovém systému prostřednictvím aktualizace.

8. Pokud uživatel, který byl dříve v oboru pro zřizování je zakázáno nebo obnovitelné odstranění ve zdrojovém systému, služba zakáže uživatele v cílovém systému prostřednictvím aktualizace.

9. Pokud uživatel, který byl dříve v oboru pro zřizování je pevně odstraněn ve zdrojovém systému, služba odstraní uživatele v cílovém systému. Ve službě Azure AD jsou uživatelé pevně odstraněni 30 dní po softwarových odstraněních.

10. Zachovat nový vodoznak na konci přírůstkového cyklu, který poskytuje počáteční bod pro pozdější přírůstkové cykly.

> [!NOTE]
> Volitelně můžete zakázat operace **Vytvořit**, **Aktualizovat**nebo **Odstranit** pomocí zaškrtávacích políček **Akce cílovýobjekt** v části [Mapování.](customize-application-attributes.md) Logika zakázat uživatele během aktualizace je také řízena pomocí mapování atributů z pole, jako je například "accountEnabled".

Služba zřizování pokračuje v běhu přírůstkové cykly zády k sobě po neomezenou dobu, v intervalech definovaných v [kurzu specifické pro každou aplikaci](../saas-apps/tutorial-list.md). Přírůstkové cykly pokračovat, dokud nedojde k jedné z následujících událostí:

- Služba se ručně zastaví pomocí portálu Azure nebo pomocí příslušného příkazu rozhraní MICROSOFT Graph API.
- Nový počáteční cyklus se aktivuje pomocí možnosti **Vymazat stav a restartování** na webu Azure Portal nebo pomocí příslušného příkazu rozhraní MICROSOFT Graph API. Tato akce vymaže všechny uložené vodoznak a způsobí, že všechny zdrojové objekty, které mají být vyhodnoceny znovu.
- Nový počáteční cyklus se aktivuje z důvodu změny mapování atributů nebo filtrů oborů. Tato akce také vymaže všechny uložené vodoznak a způsobí, že všechny zdrojové objekty, které mají být vyhodnoceny znovu.
- Proces zřizování přejde do karantény (viz níže) z důvodu vysoké míry chyb a zůstane v karanténě po dobu delší než čtyři týdny. V takovém případě bude služba automaticky zakázána.

### <a name="errors-and-retries"></a>Chyby a opakování

Pokud chyba v cílovém systému zabrání přidání, aktualizaci nebo odstranění jednotlivého uživatele v cílovém systému, bude operace zopakována v dalším cyklu synchronizace. Pokud uživatel nadále selhat, pak opakování začne dochází se sníženou frekvencí, postupně škálování zpět na jeden pokus za den. Chcete-li vyřešit selhání, správci musí zkontrolovat [protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) k určení hlavní příčiny a přijmout příslušnou akci. Běžné poruchy mohou zahrnovat:

- Uživatelé, kteří nemají ve zdrojovém systému vyplněný atribut, který je vyžadován v cílovém systému
- Uživatelé, kteří mají hodnotu atributu ve zdrojovém systému, pro které je v cílovém systému jedinečné omezení, a stejná hodnota je přítomna v jiném uživatelském záznamu

Vyřešte tyto chyby úpravou hodnot atributů pro postiženého uživatele ve zdrojovém systému nebo úpravou mapování atributů tak, aby nezpůsobovaly konflikty.

### <a name="quarantine"></a>Karanténa

Pokud většina nebo všechna volání, která jsou provedena proti cílovému systému, konzistentně selžou z důvodu chyby (například neplatná pověření správce), úloha zřizování přejde do stavu "karantény". Tento stav je uveden v [souhrnné zprávě zřizování](../manage-apps/check-status-user-account-provisioning.md) a e-mailem, pokud e-mailová oznámení byla nakonfigurovaná na webu Azure Portal.

V karanténě se frekvence přírůstkových cyklů postupně snižuje na jednou denně.

Úloha zřizování ukončí karanténu poté, co jsou opraveny všechny problematické chyby a spustí se další cyklus synchronizace. Pokud úloha zřizování zůstane v karanténě déle než čtyři týdny, úloha zřizování je zakázána. Další informace o stavu [karantény](../manage-apps/application-provisioning-quarantine-status.md)naleznete zde .

### <a name="how-long-provisioning-takes"></a>Jak dlouho trvá zřizování

Výkon závisí na tom, zda úloha zřizování běží počáteční cyklus zřizování nebo přírůstkový cyklus. Podrobnosti o tom, jak dlouho zřizování trvá a jak sledovat stav zřizovací služby, najdete [v tématu Kontrola stavu zřizování uživatelů](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Jak zjistit, zda jsou uživatelé zřizováni správně

Všechny operace spuštěné službou zřizování uživatelů se zaznamenávají do [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)Azure AD (preview). Protokoly zahrnují všechny operace čtení a zápisu provedené do zdrojového a cílového systému a uživatelská data, která byla přečtena nebo zapsána během každé operace. Informace o tom, jak číst protokoly zřizování na webu Azure Portal, najdete v tématu [zřizování vykazování průvodce](../manage-apps/check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Zrušení zřizování

Zřizovací služba Azure AD udržuje zdrojové a cílové systémy synchronizované zrušením zřizování účtů, když uživatelé už nemají přístup. 

Služba zřizování Azure AD bude obnovitelné odstranění uživatele v aplikaci, když aplikace suupports obnovitelné odstranění (požadavek na aktualizaci s active = false) a dojde k některé z následujících událostí:

* Uživatelský účet se odstraní ve službě Azure AD
*   Uživatel je nepřiřazený z aplikace.
*   Uživatel se již nesetká s filtrem oboru a přejde mimo rozsah.
    * Ve výchozím nastavení služba zřizování Azure AD obnovitelné odstraní nebo zakáže uživatele, kteří přejdou mimo rozsah. Pokud chcete toto výchozí chování přepsat, můžete nastavit příznak [pro přeskočení odstranění mimo rozsah](../app-provisioning/skip-out-of-scope-deletions.md).
*   Vlastnost AccountEnabled je nastavena na hodnotu False.

Pokud dojde k jedné z výše uvedených čtyř událostí a cílová aplikace nepodporuje obnovitelné odstranění, služba zřizování odešle požadavek DELETE trvale odstranit uživatele z aplikace. 

30 dní po odstranění uživatele ve službě Azure AD se trvale odstraní z tenanta. V tomto okamžiku služba zřizování odešle požadavek DELETE trvale odstranit uživatele v aplikaci. Kdykoli během 30denního okna můžete [ručně odstranit uživatele trvale](../fundamentals/active-directory-users-restore.md), který odešle žádost o odstranění do aplikace.

Pokud se zobrazí atribut IsSoftDeleted v mapování atributů, slouží k určení stavu uživatele a zda chcete odeslat požadavek na aktualizaci s active = false na obnovitelné odstranění uživatele. 

## <a name="next-steps"></a>Další kroky

[Naplánování nasazení automatického zřizování uživatelů](../app-provisioning/plan-auto-user-provisioning.md)

[Konfigurace zřizování pro aplikaci v galerii](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Vytvoření koncového bodu SCIM a konfigurace zřizování při vytváření vlastní aplikace](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Poradce při potížích s konfigurací a zřizováním uživatelů do aplikace](../manage-apps/application-provisioning-config-problem.md).
