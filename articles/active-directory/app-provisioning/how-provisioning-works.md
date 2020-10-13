---
title: Pochopte, jak zřizování Azure AD funguje | Microsoft Docs
description: Pochopení způsobu, jakým funguje zřizování Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/20/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5fdce791ba8848b93a8457f3738392b1f5f15508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91801796"
---
# <a name="how-provisioning-works"></a>Jak funguje zřizování

Automatické zřizování se týká vytváření identit uživatelů a rolí v cloudových aplikacích, ke kterým uživatelé potřebují přístup. Kromě vytváření identit uživatelů zahrnuje Automatické zřizování také údržbu a odebírání identit uživatelů při změně stavu nebo rolí. Než začnete s nasazením, přečtěte si tento článek, kde najdete informace o tom, jak zřizování Azure AD funguje a jak získat doporučení pro konfiguraci. 

**Služba zřizování Azure AD** zřídí uživatele k SaaS aplikací a dalších systémů připojením k systému pro správu identit mezi doménami (SCIM) 2,0 koncovým bodem rozhraní API pro správu uživatelů poskytovaným dodavatelem aplikace. Tento koncový bod SCIM umožňuje službě Azure AD programově vytvářet, aktualizovat a odebírat uživatele. Pro vybrané aplikace může služba zřizování také vytvářet, aktualizovat a odebírat další objekty související s identitou, jako jsou například skupiny a role. Kanál použitý ke zřízení služby Azure AD a aplikace je zašifrovaný pomocí šifrování HTTPS TLS 1,2.


![Azure AD Provisioning Service ](./media/how-provisioning-works/provisioning0.PNG)
 *– Obrázek 1: služba zřizování Azure AD*

![Obrázek pracovní postup odchozího zřizování uživatelů ](./media/how-provisioning-works/provisioning1.PNG)
 *2: odchozí pracovní postup zřizování uživatelů z Azure AD do oblíbených aplikací SaaS*

![Obrázek 3 příchozí pracovní postup zřizování uživatelů ](./media/how-provisioning-works/provisioning2.PNG)
 *: "příchozí" pracovní postup zřizování uživatelů z oblíbených aplikací pro správu lidských kapitálu (HCM) pro Azure Active Directory a Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Zřizování pomocí SCIM 2,0

Služba zřizování Azure AD používá [protokol SCIM 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) pro Automatické zřizování. Služba se připojí ke koncovému bodu SCIM pro aplikaci a pomocí schématu uživatelského objektu SCIM a rozhraní REST API automatizuje zřizování a zrušení zřizování uživatelů a skupin. Zřizovací konektor založený na SCIM se poskytuje pro většinu aplikací v galerii Azure AD. Při sestavování aplikací pro Azure AD můžou vývojáři pomocí rozhraní API pro správu uživatelů SCIM 2,0 vytvořit koncový bod SCIM, který integruje Azure AD pro zřizování. Podrobnosti najdete v tématu [Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Pokud chcete požádat o automatický konektor zřizování Azure AD pro aplikaci, která ji momentálně neobsahuje, vyplňte [žádost o Azure Active Directory aplikace](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autorizace

Aby se služba Azure AD mohla připojit k rozhraní API pro správu uživatelů aplikace, vyžadují přihlašovací údaje. Při konfiguraci automatického zřizování uživatelů pro aplikaci musíte zadat platné přihlašovací údaje. Typy a požadavky na přihlašovací údaje pro aplikaci najdete tak, že se odkazujete na kurz aplikace. V Azure Portal budete moct otestovat přihlašovací údaje tím, že se Azure AD pokusí připojit k aplikaci zřizování aplikace pomocí zadaných přihlašovacích údajů.

Pokud je pro aplikaci nakonfigurovaná taky jednotné přihlašování založené na SAML, je limit úložiště interní služby Azure AD pro jednotlivé aplikace 1024 bajtů. Tento limit zahrnuje všechny certifikáty, tajné tokeny, přihlašovací údaje a související konfigurační data přidružená k jedné instanci aplikace (označované také jako záznam instančního objektu ve službě Azure AD). Pokud je nakonfigurováno jednotné přihlašování založené na SAML, certifikát použitý k podepisování tokenů SAML často spotřebovává více než 50% procent místa. Všechny další položky (tajné tokeny, identifikátory URI, e-mailové adresy, uživatelská jména a hesla), které zadáte během nastavení zřizování uživatelů, by mohly překročit limit úložiště. Další informace najdete v tématu [problém při ukládání přihlašovacích údajů správce při konfiguraci zřizování uživatelů](./application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Mapování atributů

Pokud povolíte zřizování uživatelů pro aplikaci SaaS třetí strany, Azure Portal řídí její hodnoty atributu prostřednictvím mapování atributů. Mapování určují atributy uživatele, které se budou přesměrovat mezi Azure AD a cílovou aplikací při zřizování nebo aktualizaci uživatelských účtů.

K dispozici je předem nakonfigurovaná sada atributů a mapování atributů mezi uživatelskými objekty Azure AD a všemi uživatelskými objekty aplikace v SaaS. Některé aplikace spravují jiné typy objektů spolu s uživateli, jako jsou skupiny.

Při nastavování zřizování je důležité zkontrolovat a nakonfigurovat mapování atributů a pracovní postupy, které definují, které vlastnosti uživatele (nebo skupiny) se z Azure AD do aplikace přesměrují. Zkontrolujte a nakonfigurujte odpovídající vlastnost (**Porovnat objekty pomocí tohoto atributu**), která se používá k jednoznačné identifikaci uživatelů nebo skupin mezi oběma systémy a jejich porovnání.

Výchozí mapování atributů můžete přizpůsobit podle potřeb vaší firmy. Takže můžete změnit nebo odstranit existující mapování atributů nebo vytvořit nová mapování atributů. Podrobnosti najdete v tématu [přizpůsobení atributů zřizování uživatelů – mapování pro aplikace SaaS](./customize-application-attributes.md).

Při konfiguraci zřizování pro aplikaci SaaS je jedním z typů mapování atributů, které lze zadat, mapování výrazu. Pro tato mapování musíte napsat výraz podobný skriptům, který umožňuje transformovat data uživatelů na formáty, které jsou pro aplikaci SaaS přijatelné. Podrobnosti najdete v tématu [zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Rozsah 
### <a name="assignment-based-scoping"></a>Rozsah založený na přiřazení

Pro odchozí zřizování z Azure AD do aplikace SaaS, která se spoléhá na [přiřazení uživatele nebo skupiny](../manage-apps/assign-user-or-group-access-portal.md) , je nejběžnější způsob, jak určit, kteří uživatelé jsou v oboru pro zřizování. Vzhledem k tomu, že přiřazení uživatelů slouží také k povolení jednotného přihlašování, lze stejnou metodu použít ke správě přístupu i zřizování. Obory založené na přiřazení se nevztahují na vstupní scénáře zřizování, například Workday a SuccessFactors.

* **Skupiny.** Pomocí Azure AD Premiumho licenčního plánu můžete pomocí skupin přiřadit přístup k aplikaci SaaS. Až se pak obor zřizování nastaví na **synchronizovat jenom přiřazené uživatele a skupiny**, služba zřizování Azure AD zřídí nebo zruší zřízení uživatelů na základě toho, jestli jsou členy skupiny, která je přiřazená k dané aplikaci. Samotný objekt skupiny není zřízený, pokud aplikace nepodporuje skupinové objekty. Zajistěte, aby skupiny přiřazené k vaší aplikaci měly vlastnost "SecurityEnabled" nastavenou na hodnotu "true".

* **Dynamické skupiny.** Služba zřizování uživatelů Azure AD může číst a zřizovat uživatele v [dynamických skupinách](../users-groups-roles/groups-create-rule.md). Mějte na paměti tato upozornění a doporučení:

  * Dynamické skupiny můžou ovlivnit výkon komplexního zřizování z Azure AD až po SaaS aplikace.

  * Způsob zřízení a zrušení zřizování uživatele v dynamické skupině v aplikaci SaaS závisí na tom, jak rychlá dynamická skupina dokáže vyhodnotit změny členství ve skupinách. Informace o tom, jak kontrolovat stav zpracování dynamické skupiny, najdete v tématu [Zkontrolujte stav zpracování pravidla členství](../users-groups-roles/groups-create-rule.md).

  * Když uživatel ztratí členství v dynamické skupině, považuje se za událost zrušení zřízení. Při vytváření pravidel pro dynamické skupiny Vezměte v úvahu tento scénář.

* **Vnořené skupiny.** Služba zřizování uživatelů Azure AD nemůže číst ani zřizovat uživatele ve vnořených skupinách. Služba může číst a zřizovat pouze uživatele, kteří jsou bezprostředními členy explicitně přiřazené skupiny. Toto omezení "přiřazení na základě skupin na aplikace" ovlivňuje také jednotné přihlašování (viz téma [použití skupiny pro správu přístupu k aplikacím SaaS](../users-groups-roles/groups-saasapps.md)). Místo toho přímo přiřaďte nebo jinak [obor ve](define-conditional-rules-for-provisioning-user-accounts.md) skupinách, které obsahují uživatele, kteří se musí zřídit.

### <a name="attribute-based-scoping"></a>Rozsah založený na atributech 

Filtry oborů lze použít k definování pravidel založených na atributech, která určují, kteří uživatelé jsou zřízeni v aplikaci. Tato metoda se běžně používá pro příchozí zřizování z aplikací HCM do služby Azure AD a služby Active Directory. Filtry oborů se konfigurují jako součást mapování atributů pro jednotlivé konektory zřizování uživatelů Azure AD. Podrobnosti o konfiguraci filtrů oboru založeného na atributech najdete v tématu [zřizování aplikací na základě atributů s filtry oborů](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Uživatelé B2B (host)

Je možné použít službu Azure AD Provisioning Service ke zřízení uživatelů B2B (nebo hostů) ve službě Azure AD a SaaS aplikace. Aby se však uživatelé B2B přihlásili k aplikaci SaaS pomocí služby Azure AD, musí mít aplikace SaaS možnost jednotného přihlašování založené na SAML nakonfigurovanou určitým způsobem. Další informace o tom, jak nakonfigurovat aplikace SaaS tak, aby podporovaly přihlášení uživatelů B2B, najdete v tématu [Konfigurace aplikací SaaS pro spolupráci B2B](../external-identities/configure-saas-apps.md).

Všimněte si, že userPrincipalName pro uživatele typu Host je často uložen jako "alias # EXT # @domain.com ". Pokud je hodnota userPrincipalName obsažena v mapování atributů jako zdrojový atribut, #EXT # je odstraněn z třídy userPrincipalName. Pokud požadujete, aby byla k dispozici #EXT #, nahraďte atribut userPrincipalName parametrem originalUserPrincipalName jako zdrojový atribut. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Cykly zřizování: počáteční a přírůstkové

Když je Azure AD zdrojovým systémem, služba zřizování používá [dotaz použít rozdíl ke sledování změn v Microsoft Graph dat](/graph/delta-query-overview) pro monitorování uživatelů a skupin. Služba zřizování spouští počáteční cyklus proti zdrojovému systému a cílovému systému, po kterém následují pravidelné přírůstkové cykly.

### <a name="initial-cycle"></a>Počáteční cyklus

Po spuštění služby zřizování se první cyklus provede:

1. Dotazujte všechny uživatele a skupiny ze zdrojového systému a načtěte všechny atributy definované v [mapování atributů](customize-application-attributes.md).

2. Filtrování vrácených uživatelů a skupin pomocí všech nakonfigurovaných [přiřazení](../manage-apps/assign-user-or-group-access-portal.md) nebo [filtrů rozsahů založených na atributech](define-conditional-rules-for-provisioning-user-accounts.md).

3. Když je uživatel přiřazen nebo je v oboru pro zřizování, služba se dotazuje cílového systému pro odpovídajícího uživatele pomocí zadaných [atributů odpovídajícího atributu](customize-application-attributes.md#understanding-attribute-mapping-properties). Příklad: Pokud je název userPrincipal ve zdrojovém systému odpovídajícím atributem a mapuje se na uživatelské jméno v cílovém systému, pak služba zřizování dotazuje cílový systém pro uživatelská jména, která se shodují s hodnotami userPrincipal názvu ve zdrojovém systému.

4. Pokud se v cílovém systému nenajde shodný uživatel, bude vytvořen pomocí atributů vrácených ze zdrojového systému. Po vytvoření uživatelského účtu služba zřizování detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele. Toto ID se používá ke spuštění všech budoucích operací s tímto uživatelem.

5. Pokud se najde shodný uživatel, aktualizuje se pomocí atributů poskytovaných zdrojovým systémem. Po porovnání uživatelského účtu služba zřizování detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele. Toto ID se používá ke spuštění všech budoucích operací s tímto uživatelem.

6. Pokud mapování atributů obsahuje "referenční" atributy, služba provede další aktualizace v cílovém systému pro vytvoření a propojení odkazovaných objektů. Uživatel může například mít v cílovém systému atribut "nadřízený", který je propojený s jiným uživatelem vytvořeným v cílovém systému.

7. Na konci počátečního cyklu můžete zachovat vodoznak, který poskytuje výchozí bod pro pozdější přírůstkové cykly.

Některé aplikace, jako je ServiceNow, G Suite a box, podporují nejen zřizování uživatelů, ale také zřizování skupin a jejich členů. V takových případech, pokud je zřizování skupin povoleno v [mapování](customize-application-attributes.md), služba zřizování synchronizuje uživatele a skupiny a potom později synchronizuje členství ve skupině.

### <a name="incremental-cycles"></a>Přírůstkové cykly

Po úvodním cyklu budou všechny ostatní cykly:

1. Dotaz na zdrojový systém pro všechny uživatele a skupiny, které byly aktualizovány od posledního uložení vodoznaku.

2. Filtrování vrácených uživatelů a skupin pomocí všech nakonfigurovaných [přiřazení](../manage-apps/assign-user-or-group-access-portal.md) nebo [filtrů rozsahů založených na atributech](define-conditional-rules-for-provisioning-user-accounts.md).

3. Když je uživatel přiřazen nebo je v oboru pro zřizování, služba se dotazuje cílového systému pro odpovídajícího uživatele pomocí zadaných [atributů odpovídajícího atributu](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Pokud se v cílovém systému nenajde shodný uživatel, bude vytvořen pomocí atributů vrácených ze zdrojového systému. Po vytvoření uživatelského účtu služba zřizování detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele. Toto ID se používá ke spuštění všech budoucích operací s tímto uživatelem.

5. Pokud se najde shodný uživatel, aktualizuje se pomocí atributů poskytovaných zdrojovým systémem. Pokud se jedná o nově přiřazený účet, služba zřizování detekuje a ukládá ID cílového systému pro nového uživatele do mezipaměti. Toto ID se používá ke spuštění všech budoucích operací s tímto uživatelem.

6. Pokud mapování atributů obsahuje "referenční" atributy, služba provede další aktualizace v cílovém systému pro vytvoření a propojení odkazovaných objektů. Uživatel může například mít v cílovém systému atribut "nadřízený", který je propojený s jiným uživatelem vytvořeným v cílovém systému.

7. Pokud je uživatel, který byl dříve v oboru pro zřizování, odebrán z oboru, včetně Nepřiřazeno, služba zakáže uživatele v cílovém systému prostřednictvím aktualizace.

8. Pokud je uživatel, který byl dříve v oboru pro zřizování, zakázán nebo odstraněn ze zdrojového systému, služba zakáže uživatele v cílovém systému prostřednictvím aktualizace.

9. Pokud je uživatel, který byl dříve v oboru pro zřizování, ve zdrojovém systému pevným smazán, služba odstraní uživatele v cílovém systému. Ve službě Azure AD se uživatelé po odinstalaci nezávazně odstraní po uplynutí 30 dnů.

10. Zachovejte nový vodoznak na konci přírůstkového cyklu, který poskytuje výchozí bod pro pozdější přírůstkové cykly.

> [!NOTE]
> Volitelně můžete zakázat operace **vytvořit**, **aktualizovat**nebo **Odstranit** pomocí **akcí cílového objektu** v sekci [mapování](customize-application-attributes.md) . Logika zakázání uživatele během aktualizace je také řízena prostřednictvím mapování atributů z pole, jako je například "accountEnabled".

Zřizovací služba pokračuje v provádění přírůstkových cyklů back-to-back v intervalech definovaných v tomto [kurzu, které jsou specifické pro jednotlivé aplikace](../saas-apps/tutorial-list.md). Přírůstkové cykly pokračují, dokud nedojde k jedné z následujících událostí:

- Služba se ručně zastavila pomocí Azure Portal nebo pomocí příslušného příkazu Microsoft Graph API.
- Nový počáteční cyklus se aktivuje pomocí možnosti **Vymazat stav a restartovat** v Azure Portal nebo pomocí příslušného příkazu Microsoft Graph API. Tato akce vymaže libovolný uložený vodoznak a způsobí, že všechny zdrojové objekty budou znovu vyhodnoceny.
- Spustí se nový počáteční cyklus, protože se změní mapování atributů nebo filtry oborů. Tato akce také vymaže všechny uložené meze a způsobí, že všechny zdrojové objekty budou znovu vyhodnoceny.
- Proces zřizování přejde do karantény (viz níže) z důvodu vysoké míry chyb a zůstane v karanténě po dobu více než čtyř týdnů. V takovém případě se služba automaticky zakáže.

### <a name="errors-and-retries"></a>Chyby a opakování

Pokud chyba v cílovém systému brání přidání, aktualizaci nebo odstranění jednotlivého uživatele v cílovém systému, operace se zopakuje v dalším cyklu synchronizace. Pokud uživatel i nadále selže, začnou se opakované pokusy vyskytnout s omezenou frekvencí a postupně škálovat zpátky na jeden pokus za den. Aby bylo možné tuto chybu vyřešit, musí správci v [protokolech zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) zjistit hlavní příčinu a provést příslušnou akci. Běžné chyby můžou zahrnovat:

- Uživatelé nemají ve zdrojovém systému naplněný atribut, který je požadován v cílovém systému.
- Uživatelé mají ve zdrojovém systému hodnotu atributu, pro kterou je v cílovém systému jedinečné omezení a v jiném záznamu uživatele se nachází stejná hodnota.

Vyřešte tyto chyby úpravou hodnot atributu pro ovlivněného uživatele ve zdrojovém systému nebo úpravou mapování atributů tak, aby nezpůsobily konflikty.

### <a name="quarantine"></a>Karanténa

Pokud se většina nebo všechna volání prováděná na cílovém systému konzistentně nezdařila kvůli chybě (například neplatné přihlašovací údaje správce), úloha zřizování přejde do stavu "karanténa". Tento stav je uveden v [sestavě souhrn zřizování](./check-status-user-account-provisioning.md) a prostřednictvím e-mailu, pokud byly e-mailová oznámení konfigurována v Azure Portal.

Když je v karanténě, frekvence přírůstkových cyklů se postupně zkracuje na jeden den.

Úloha zřizování se ukončí do karantény po opravení všech problematických chyb a spuštění dalšího cyklu synchronizace. Pokud úloha zřizování zůstane v karanténě po dobu delší než čtyři týdny, úloha zřizování je zakázaná. Tady najdete další informace [o stavu](./application-provisioning-quarantine-status.md)karantény.

### <a name="how-long-provisioning-takes"></a>Jak dlouho trvá zřizování

Výkon závisí na tom, jestli vaše úloha zřizování spouští počáteční cyklus zřizování nebo přírůstkový cyklus. Podrobnosti o tom, jak dlouho trvá zřizování a jak monitorovat stav služby zřizování, najdete v tématu [Zkontrolujte stav zřizování uživatelů](application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Jak zjistit, jestli se uživatelé správně zřídí

Všechny operace spouštěné službou zřizování uživatelů se zaznamenávají v [protokolech zřizování Azure AD (Preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Protokoly zahrnují všechny operace čtení a zápisu provedené ve zdrojovém a cílovém systému a uživatelská data, která byla během každé operace načtena nebo zapsána. Informace o tom, jak číst protokoly zřizování v Azure Portal, najdete v [průvodci zřizováním sestav](./check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Zrušení zřizování
Služba zřizování Azure AD udržuje zdrojový a cílový systém v synchronizaci s účty zrušení zřizování při odebrání přístupu uživatele.

Služba zřizování podporuje odstranění i zakázání (někdy označované jako obnovitelné odstraňování) uživatelů. Přesná definice zakázaného a odstranění se liší v závislosti na implementaci cílové aplikace, ale obecně se tím znamená, že se uživatel nemůže přihlásit. Odstranění indikuje, že uživatel byl z aplikace zcela odebrán. V případě aplikací SCIM je zakázán požadavek na nastavení *aktivní* vlastnosti na hodnotu false pro uživatele. 

**Konfigurace aplikace pro zakázání uživatele**

Ujistěte se, že jste zaškrtli políčko pro aktualizace.

Ujistěte se, že máte mapování pro *aktivní* pro vaši aplikaci. Pokud používáte aplikaci z Galerie aplikací, mapování může být mírně odlišné. Ujistěte se prosím, že pro aplikace Galerie použijete výchozí/vycházející mapování polí.


**Konfigurace aplikace pro odstranění uživatele**

Pomocí následujících scénářů se aktivuje operace zakázat nebo odstranit: 
* Ve službě Azure AD se neodstranil uživatel (odesílá se do vlastnosti koš/AccountEnabled nastaven na hodnotu false).
    30 dní po odstranění uživatele ve službě Azure AD se trvale odstraní z tenanta. V tuto chvíli služba zřizování pošle žádost o odstranění, aby uživatele v aplikaci trvale odstranil. Kdykoli během 30denního okna můžete [trvale odstranit uživatele](../fundamentals/active-directory-users-restore.md), který odešle žádost o odstranění do aplikace.
* Uživatel je trvale odstraněn nebo odebrán z koše ve službě Azure AD.
* Uživatel není přiřazený k aplikaci.
* Uživatel přejde z oboru do rozsahu mimo rozsah (již neprojde filtr oboru).
    
Ve výchozím nastavení služba zřizování Azure AD dočasná odstraní nebo zakáže uživatele, kteří se přestanou přidělovat mimo rozsah. Pokud chcete přepsat toto výchozí chování, můžete nastavit příznak pro [přeskočení odstranění mimo rozsah.](skip-out-of-scope-deletions.md)

Pokud dojde k jedné z výše uvedených čtyř událostí a cílová aplikace nepodporuje obnovitelné odstranění, služba zřizování odešle požadavek na odstranění, který uživatele trvale odstraní z aplikace.

Pokud se ve svých mapováních atributů zobrazí atribut IsSoftDeleted, použije se k určení stavu uživatele a zda má být odeslán požadavek na aktualizaci s hodnotou aktivní = false pro obnovitelné odstranění uživatele.

**Známá omezení**

* Pokud uživatel, který byl dřív spravovaný službou zřizování, není přiřazený z aplikace nebo ze skupiny přiřazené k aplikaci, pošleme žádost o zákaz. Od tohoto okamžiku uživatel není spravován službou a nepošle se žádost o odstranění, když se odstraní z adresáře.
* Zřizování uživatele, který je zakázaný ve službě Azure AD, se nepodporuje. Musí být ve službě Azure AD aktivní předtím, než se zřídí.
* Když uživatel přejde z tichého odstranění na aktivní, služba zřizování Azure AD aktivuje uživatele v cílové aplikaci, ale neobnoví automaticky členství ve skupině. Cílová aplikace by měla udržovat členství ve skupině pro uživatele v neaktivním stavu. Pokud cílová aplikace tuto možnost nepodporuje, můžete restartovat zřizování a aktualizovat členství ve skupině. 

**Doporučení**

Při vývoji aplikace vždy podporují obnovitelné odstranění i pevné odstranění. Umožňuje zákazníkům obnovení, když je uživatel omylem zakázán.


## <a name="next-steps"></a>Další kroky

[Naplánování nasazení automatického zřizování uživatelů](../app-provisioning/plan-auto-user-provisioning.md)

[Konfigurace zřizování pro aplikaci v galerii](./configure-automatic-user-provisioning-portal.md)

[Vytvoření koncového bodu SCIM a konfigurace zřizování při vytváření vlastní aplikace](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Řešení potíží s konfigurací a zřizováním uživatelů do aplikace](./application-provisioning-config-problem.md).
