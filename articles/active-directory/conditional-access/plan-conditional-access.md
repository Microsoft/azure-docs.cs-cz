---
title: Plánování nasazení podmíněného přístupu Azure Active Directory
description: Naučte se navrhovat zásady podmíněného přístupu a efektivně je nasadit ve vaší organizaci.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13460fad0ed106f5c0590df961ceca54cb04cd7b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427177"
---
# <a name="plan-a-conditional-access-deployment"></a>Plánování nasazení podmíněného přístupu

Plánování nasazení podmíněného přístupu je důležité pro zajištění strategie přístupu vaší organizace pro aplikace a prostředky.

V rámci cloudového a cloudového světa budou vaši uživatelé přistupovat k prostředkům vaší organizace odkudkoli pomocí různých zařízení a aplikací. Výsledkem je, že se zaměříte na to, kdo má přístup k prostředku, již není dostatek. Musíte také zvážit, kde je uživatel, používané zařízení, přidaný prostředek a další. 

Analýzy podmíněného přístupu Azure Active Directory (Azure AD) signalizují, jako je například uživatel, zařízení a umístění, pro automatizaci rozhodnutí a vymáhání zásad přístupu k organizaci pro prostředky. Pomocí zásad podmíněného přístupu můžete použít řízení přístupu, jako je Multi-Factor Authentication (MFA). Zásady podmíněného přístupu vám umožňují vyzvat uživatele k MFA, pokud jsou potřeba k zabezpečení, a zůstat v případě potřeby mimo uživatele.

![Přehled podmíněného přístupu](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

Společnost Microsoft poskytuje standardní podmíněné zásady nazývané [výchozí hodnoty zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) , které zajišťují základní úroveň zabezpečení. Vaše organizace ale může potřebovat větší flexibilitu než nabídka výchozích hodnot zabezpečení. Podmíněný přístup můžete použít k přizpůsobení výchozích hodnot zabezpečení s větší členitosti a ke konfiguraci nových zásad, které splňují vaše požadavky.

## <a name="learn"></a>Learn

Než začnete, ujistěte se, že rozumíte tomu, jak [podmíněný přístup](overview.md) funguje a kdy byste ho měli použít.

### <a name="benefits"></a>Výhody

Výhody nasazení podmíněného přístupu:

* Zvyšte produktivitu. Přerušit pouze uživatele s podmínkou přihlášení, jako je MFA, pokud je jeden nebo více signálů opravňuje. Zásady podmíněného přístupu vám umožňují řídit, kdy se uživatelům zobrazí výzva k MFA, když se zablokuje přístup a že musí používat důvěryhodné zařízení.

* Řízení rizik. Automatizace hodnocení rizik pomocí podmínek zásad znamená, že jsou rizikové přihlašování zjištěné a napravované nebo blokované. Přístup k podmíněnému přístupu pomocí [Identity Protection](../identity-protection/overview-identity-protection.md), který detekuje anomálie a podezřelé události, vám umožní cílit na to, kdy je přístup k prostředkům blokovaný nebo ověřovaný. 

* Vyřešte dodržování předpisů a zásady správného řízení. Podmíněný přístup umožňuje auditovat přístup k aplikacím, prezentovat podmínky použití pro vyjádření souhlasu a omezovat přístup na základě zásad dodržování předpisů.

* Spravujte náklady. Přesunutí zásad přístupu do Azure AD snižuje závislosti na vlastních nebo místních řešeních pro podmíněný přístup a na jejich náklady na infrastrukturu.

### <a name="license-requirements"></a>Licenční požadavky

Viz [licenční požadavky na podmíněný přístup](overview.md).

Pokud potřebujete další funkce, budete možná potřebovat i související licence. Další informace najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Požadavky

* Funkční tenant Azure AD s povoleným Azure AD Premium nebo zkušební licencí. V případě potřeby [ho vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Účet s oprávněním správce podmíněného přístupu.

* Uživatel bez oprávnění správce s heslem, které znáte, například testuser. Pokud potřebujete vytvořit uživatele, přečtěte si téma [rychlý Start: přidání nových uživatelů do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).

* Skupina, jejíž je uživatel bez oprávnění správce členem. Pokud potřebujete vytvořit skupinu, přečtěte si téma [Vytvoření skupiny a přidání členů v Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

### <a name="training-resources"></a>Školicí materiály

V případě, že se seznámíte s podmíněným přístupem, můžou být užitečné následující zdroje:


#### <a name="videos"></a>Videa

* [Co je podmíněný přístup?](https://youtu.be/ffMAw2IVO7A)
* [Jak nasadit podmíněný přístup?](https://youtu.be/c_izIRNJNuk)
* [Jak zavést zásady podmíněného přístupu pro koncové uživatele?](https://youtu.be/0_Fze7Zpyvc)
* [Jak zahrnout nebo vyloučit uživatele ze zásad podmíněného přístupu](https://youtu.be/5DsW1hB3Jqs)
* [Podmíněný přístup pomocí ovládacích prvků zařízení](https://youtu.be/NcONUf-jeS4)
* [Podmíněný přístup s Azure AD MFA](https://youtu.be/Tbc-SU97G-w)
* [Podmíněný přístup v Enterprise Mobility + Security](https://youtu.be/A7IrxAH87wc)


#### <a name="online-courses-on-pluralsight"></a>Online kurzy na PluralSight

* [Návrh správy identit v Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Návrh ověřování pro Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Autorizace návrhu pro Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

## <a name="plan-the-deployment-project"></a>Plánování projektu nasazení

Při určování strategie pro toto nasazení v prostředí zvažte potřeby vaší organizace.

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne z důvodu neshodných očekávání na dopad, výsledky a zodpovědnosti. Chcete-li se těmto nástrah vyhnout, [Ujistěte se, že jste připravují správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md) a že role projektu jsou jasné.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikujte s vašimi uživateli, jak se změní, když se změní, a jak získat podporu, pokud se jim setkávají problémy.

### <a name="plan-a-pilot"></a>Plánování pilotního projektu

Až budou nové zásady připravené pro vaše prostředí, nasaďte je v produkčním prostředí do fází. Nejdřív použijte zásadu na malou skupinu uživatelů v testovacím prostředí a ověřte, jestli se zásada chová podle očekávání. Podívejte [se na osvědčené postupy pro pilotní nasazení](../fundamentals/active-directory-deployment-plans.md).

> [!NOTE]
> Pro zavedení nových zásad, které nejsou specifické pro správce, vylučte všechny správce. Tím zajistíte, že správci budou mít stále přístup k těmto zásadám, a pokud dojde k významnému dopadu, může to udělat nebo odvolat. Před použitím pro všechny uživatele vždy ověřte zásadu s menšími skupinami uživatelů.

## <a name="understand-conditional-access-policy-components"></a>Principy součástí zásad podmíněného přístupu
Zásady podmíněného přístupu jsou if a then: Pokud je splněné přiřazení, použijte tyto ovládací prvky přístupu.

Při konfiguraci zásad podmíněného přístupu se podmínky nazývají *přiřazení*. Zásady podmíněného přístupu vám umožňují vynutilit řízení přístupu v aplikacích vaší organizace na základě určitých přiřazení.


Další informace najdete v tématu [Vytvoření zásady podmíněného přístupu](concept-conditional-access-policies.md).

![obrazovka pro vytvoření zásady](media/plan-conditional-access/create-policy.png)

[Přiřazení](concept-conditional-access-policies.md#assignments) definují

* [Uživatelé a skupiny](concept-conditional-access-users-groups.md) , na které má zásada vliv

* [Cloudové aplikace nebo akce](concept-conditional-access-cloud-apps.md) , na které se zásady vztahují 

* [Podmínky](concept-conditional-access-conditions.md) , za kterých se zásada uplatní

Nastavení [řízení přístupu](concept-conditional-access-policies.md) určují, jak se má zásada vyhovět:

* [Udělení nebo blokování](concept-conditional-access-grant.md) přístupu ke cloudovým aplikacím

* [Ovládací prvky relace](concept-conditional-access-session.md) umožňují omezená prostředí v rámci konkrétních cloudových aplikací.

### <a name="ask-the-right-questions-to-build-your-policies"></a>Zeptejte se pravých otázek na sestavení zásad

Zásady odpovídají na dotazy týkající se toho, kdo by měl mít přístup k vašim prostředkům, k jakým prostředkům mají přístup a za jakých podmínek. Zásady lze navrhovat pro udělení přístupu nebo k blokování přístupu. Nezapomeňte klást správné otázky ohledně toho, co se vaše zásada snaží dosáhnout. 

Zdokumentujte odpovědi na otázky pro jednotlivé zásady před jejich vytvořením. 

#### <a name="common-questions-about-assignments"></a>Běžné dotazy týkající se přiřazení

[Uživatelé a skupiny](concept-conditional-access-users-groups.md)

* Kteří uživatelé a skupiny budou zahrnuti do zásad nebo z nich budou vyloučeni?

* Zahrnuje tato zásada všechny uživatele, konkrétní skupinu uživatelů, role adresáře nebo externí uživatele?

[Cloudové aplikace nebo akce](concept-conditional-access-cloud-apps.md)

* Jaké aplikace budou zásady platit?

* K jakým akcím uživatelů budou platit tyto zásady?

[Podmínky](concept-conditional-access-conditions.md)

* Které platformy zařízení budou zahrnuty nebo vyloučeny ze zásad?

* Jaká jsou důvěryhodná umístění organizace?

* Jaká umístění budou zahrnuta nebo vyloučena ze zásad?

* Jaké typy klientských aplikací (prohlížeče, mobilní zařízení, klienti klasické pracovní plochy, aplikace s dřívějšími metodami ověřování) budou zahrnuté do zásad nebo z nich budou vyloučené?

* Máte zásady, které by měly z zásad vyloučit zařízení připojená k Azure AD nebo zařízení připojená k hybridní službě Azure AD? 

* Pokud používáte [Identity Protection](../identity-protection/concept-identity-protection-risks.md), chcete začlenit ochranu před riziky přihlašování?

#### <a name="common-questions-about-access-controls"></a>Běžné dotazy týkající se řízení přístupu

[Udělit nebo blokovat ](concept-conditional-access-grant.md) 

Chcete udělit přístup k prostředkům tím, že vyžadujete jednu nebo více následujících akcí?

* Vyžadování MFA

* Vyžadovat, aby zařízení bylo označené jako vyhovující

* Vyžadovat zařízení připojené k hybridní službě Azure AD

* Vyžadovat klientskou aplikaci schválenou

* Vyžadování zásad ochrany aplikací

[Řízení relace](concept-conditional-access-session.md)

Chcete vynutilit některé z následujících řízení přístupu pro cloudové aplikace?

* Použít oprávnění pro vymáhání aplikace

* Použít Řízení podmíněného přístupu k aplikacím

* Vyhovět četnosti přihlašování

* Použít trvalé relace prohlížeče

### <a name="access-token-issuance"></a>Vystavení přístupového tokenu

Je důležité pochopit, jak se vydávají přístupové tokeny. 

![Diagram vystavení přístupového tokenu](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> Pokud není vyžadováno žádné přiřazení a žádná zásada podmíněného přístupu není platná, je výchozím chováním vydání přístupového tokenu. 

Představte si třeba zásadu, kde:

Pokud je uživatel ve skupině 1, vynutí MFA přístup k aplikaci 1.

Pokud se uživatel, který není ve skupině 1, pokusí o přístup k aplikaci, bude splněna podmínka IF a token se vydá. Vyloučení uživatelů mimo skupinu 1 vyžaduje samostatnou zásadu pro blokování všech ostatních uživatelů.

## <a name="follow-best-practices"></a>Dodržujte osvědčené postupy

Rozhraní podmíněného přístupu poskytuje skvělou flexibilitu konfigurace. Ale skvělá flexibilita také znamená, že před vydáním pečlivě zkontrolovat každou zásadu konfigurace, aby nedocházelo k nežádoucím výsledkům.

### <a name="apply-conditional-access-policies-to-every-app"></a>Použití zásad podmíněného přístupu pro každou aplikaci

Přístupové tokeny se ve výchozím nastavení vydávají, pokud podmínka zásad podmíněného přístupu neaktivuje řízení přístupu. Ujistěte se, že každá aplikace má použitou aspoň jednu zásadu podmíněného přístupu.

> [!IMPORTANT]
> Buďte velmi opatrní v používání blokování a všech aplikací v jedné zásadě. To může uzamknout správce z portálu pro správu Azure a vyloučení nelze nakonfigurovat pro důležité koncové body, například Microsoft Graph.

### <a name="minimize-the-number-of-conditional-access-policies"></a>Minimalizace počtu zásad podmíněného přístupu

Vytváření zásad pro jednotlivé aplikace nebude efektivní a vede k obtížné správě. Podmíněný přístup použije jenom prvních 195 zásad na uživatele. Doporučujeme analyzovat své aplikace a seskupit je do aplikací, které mají stejné požadavky na prostředky pro stejné uživatele. Pokud například všechny Microsoft 365 aplikace nebo všechny aplikace pro personální oddělení mají stejné požadavky pro stejné uživatele, vytvořte jednu zásadu a zahrňte všechny aplikace, na které se vztahuje. 

### <a name="set-up-emergency-access-accounts"></a>Nastavení účtů pro nouzový přístup

Pokud zásadu nakonfigurujete, může se organizacím z Azure Portal uzamknout. Omezte dopad náhodného správce tak, že vytvoříte dva nebo víc [účtů pro nouzový přístup](../roles/security-emergency-access.md) ve vaší organizaci.

* Vytvořte uživatelský účet vyhrazený pro správu zásad a vyloučíte ho ze všech vašich zásad.

### <a name="set-up-report-only-mode"></a>Nastavení režimu pouze sestavy

Může být obtížné předpovědět počet a jména uživatelů ovlivněných běžnými iniciativami nasazení, jako například:

* blokování starších verzí ověřování
* vyžadování MFA
* Implementace zásad rizik přihlašování

[Režim pouze pro sestavy ](concept-conditional-access-report-only.md) umožňuje správcům vyhodnotit dopad zásad podmíněného přístupu předtím, než je povolí ve svém prostředí.

Naučte se [Konfigurovat režim pouze pro sestavy na základě zásad podmíněného přístupu](howto-conditional-access-insights-reporting.md).

### <a name="plan-for-disruption"></a>Plánování přerušení

Pokud pro zabezpečení vašich IT systémů spoléháte na jedno řízení přístupu, jako je MFA nebo síťové umístění, budete mít přístup k chybám, pokud dojde k nedostupnosti nebo selhání konfigurace jednoho řízení přístupu. Chcete-li snížit riziko uzamčení během nepředvídatelného rušení, [strategie plánování](../authentication/concept-resilient-controls.md) , které se mají pro vaši organizaci přijmout.

### <a name="set-naming-standards-for-your-policies"></a>Nastavení standardů pojmenování pro vaše zásady

Standard pojmenování vám pomůže najít zásady a pochopit jejich účel, aniž byste je museli otevírat na portálu pro správu Azure. Doporučujeme, abyste pojmenovat zásadu, která bude zobrazovat:

* Pořadové číslo

* Cloudové aplikace, na které se vztahuje

* Odpověď

* Komu se vztahuje

* Když se použije (Pokud je k dispozici)

![Snímek obrazovky, který zobrazuje standardy pojmenovávání pro zásady.](media/plan-conditional-access/11.png)

**Příklad**: Zásada, která vyžaduje MFA pro uživatele marketingu, kteří přistupují k aplikaci Dynamics CRP z externích sítí, může být:

![Standardní pojmenování](media/plan-conditional-access/naming-example.png)

Popisný název vám pomůže zajistit přehled implementace podmíněného přístupu. Pořadové číslo je užitečné, pokud potřebujete odkazovat na zásadu v konverzaci. Když například hovoříte o správce telefonu, můžete požádat, aby otevřeli zásady CA01 a problém vyřešili.

#### <a name="naming-standards-for-emergency-access-controls"></a>Standardy pojmenování pro řízení nouzového přístupu

Kromě aktivních zásad implementujte zakázané zásady, které fungují jako sekundární [ovládací prvky odolného přístupu v případě výpadků nebo nouzových scénářů](../authentication/concept-resilient-controls.md). Vaše standardní pojmenování zásad pro nepředvídané pracovní postupy by měly zahrnovat:
* Umožněte v nouzi na začátku, aby se název vypnul mezi ostatními zásadami.

* Může se jednat o název přerušení, na který se má vztahovat.

* Pořadové číslo objednávání, které správci pomůžou zjistit, v jakém pořadí mají být povolené zásady pořadí.

**Příklad**

Následující název označuje, že tato zásada je první ze čtyř zásad, které je možné povolit, pokud dojde k přerušení MFA:

EM01-ENABLE v nouzi: přerušení MFA [1/4]-Exchange SharePoint: vyžaduje pro uživatele VIP službu hybridního připojení k Azure AD.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>Vylučte země, ze kterých nikdy neočekáváte přihlášení.

Azure Active Directory umožňuje vytvářet [pojmenovaná umístění](location-condition.md). Vytvořte pojmenované umístění, které obsahuje všechny země, ze kterých nikdy neočekáváte, že přihlášení proběhne. Pak vytvořte zásadu pro všechny aplikace, které blokují přihlášení z tohoto pojmenovaného umístění. **Nezapomeňte z této zásady vyloučit správce**.

### <a name="plan-your-policy-deployment"></a>Plánování nasazení zásad

Až budou nové zásady připravené pro vaše prostředí, ujistěte se, že jste před vydáním provedli kontrolu všech zásad, aby nedocházelo k nežádoucím výsledkům.

## <a name="common-policies"></a>Běžné zásady

Při plánování řešení zásad podmíněného přístupu posuďte, jestli potřebujete vytvořit zásady, abyste dosáhli následujících výsledků.

* [Vyžadování MFA](#require-mfa)
* [Reakce na potenciálně ohrožené účty](#respond-to-potentially-compromised-accounts)
* [Vyžadování spravovaných zařízení](#require-managed-devices)
* [Vyžadovat schválené klientské aplikace](#require-approved-client-apps)
* [Blokovat přístup](#block-access)

### <a name="require-mfa"></a>Vyžadování MFA

Běžné případy použití pro vyžadování přístupu MFA:

* [Podle správců](howto-conditional-access-policy-admin-mfa.md)

* [Na konkrétní aplikace](../authentication/tutorial-enable-azure-mfa.md)

* [Pro všechny uživatele](howto-conditional-access-policy-all-users-mfa.md)

* [Z umístění v síti nedůvěřujete](untrusted-networks.md)

* [Pro správu Azure](howto-conditional-access-policy-azure-management.md)

### <a name="respond-to-potentially-compromised-accounts"></a>Reakce na potenciálně ohrožené účty

Pomocí zásad podmíněného přístupu můžete implementovat automatizované reakce na přihlášení pomocí potenciálně ohrožených identit. Pravděpodobnost, že dojde k ohrožení bezpečnosti účtu, je vyjádřena ve formě úrovní rizika. Služba Identity Protection počítá dvě úrovně rizik: riziko přihlášení a riziko pro uživatele. Následující tři výchozí zásady lze povolit.

* [Vyžadovat registraci všech uživatelů pro MFA](howto-conditional-access-policy-risk.md)

* [Vyžadovat změnu hesla pro uživatele s vysokým rizikem](howto-conditional-access-policy-risk.md)

* [Vyžadovat MFA pro uživatele, kteří mají střední nebo vysoké riziko přihlašování](howto-conditional-access-policy-risk.md)

### <a name="require-managed-devices"></a>Vyžadování spravovaných zařízení

Rozšíření podporovaných zařízení pro přístup k vašim cloudovým prostředkům pomáhá zlepšit produktivitu vašich uživatelů. U zařízení s neznámou úrovní ochrany pravděpodobně nechcete mít k některým prostředkům ve vašem prostředí. U těchto prostředků [vyžadují, aby k nim uživatelé měli přístup jenom pomocí spravovaného zařízení](require-managed-devices.md).

### <a name="require-approved-client-apps"></a>Vyžadování klientem schválených aplikací

Zaměstnanci používají mobilní zařízení pro osobní i pracovní úkoly. V případě scénářů BYOD se musíte rozhodnout, jestli chcete spravovat celé zařízení, nebo jenom data. Pokud spravujete jenom data a přístup, můžete [vyžadovat schválené cloudové aplikace](app-based-conditional-access.md) , které můžou chránit vaše firemní data. Můžete například vyžadovat, aby k e-mailům byl přistup jenom přes Outlook Mobile, a ne prostřednictvím obecného e-mailového programu.

### <a name="block-access"></a>Blokování přístupu

Možnost [blokování veškerého přístupu](howto-conditional-access-policy-block-access.md) je výkonná. Dá se použít například při migraci aplikace do služby Azure AD, ale nejsou připravené k tomu, aby se k nim ještě nikdo přihlásil. Blokovat přístup: 

* Přepíše všechna ostatní přiřazení pro uživatele.

* Má zablokovat, aby se celá organizace mohla přihlásit ke svému tenantovi.

> [!IMPORTANT]
> Pokud vytvoříte zásadu pro blokování přístupu pro všechny uživatele, nezapomeňte vyloučit účty pro nouzový přístup a zvážit vyloučení všech správců ze zásad.

K dalším běžným scénářům, kde můžete zablokovat přístup pro uživatele:

* [Blokuje určitá síťová umístění](howto-conditional-access-policy-location.md) pro přístup k vašim cloudovým aplikacím. Pomocí této zásady můžete blokovat určité země, ze kterých víte, že by provoz neměl přijít.

* Azure AD podporuje starší verze ověřování. Starší verze ověřování však nepodporují MFA a mnoho prostředí vyžaduje, aby se zabezpečení identity vyvyžadovalo. V takovém případě můžete pro přístup k prostředkům tenanta [blokovat aplikace pomocí staršího ověřování ](block-legacy-authentication.md) .

## <a name="build-and-test-policies"></a>Zásady sestavení a testování

V každé fázi nasazení se ujistěte, že budete vyhodnocovat, že výsledky jsou očekávané. 

Až budou nové zásady připravené, nasaďte je v produkčním prostředí do fází:

* Poskytněte koncovým uživatelům interní změnu komunikace.

* Začněte s malou sadou uživatelů a ověřte, jestli se zásada chová podle očekávání.

* Když rozbalíte zásadu, aby zahrnovala více uživatelů, pokračujte v vyloučení všech správců. Vyloučení správců zajistí, že někdo má stále přístup k zásadám, pokud je potřeba změnit.

* Zásady můžete použít pro všechny uživatele až po důkladném otestování. Ujistěte se, že máte aspoň jeden účet správce, na který se zásady nevztahují.

### <a name="create-test-users"></a>Vytvoření testovacích uživatelů

Vytvořte sadu testovacích uživatelů, kteří odrážejí uživatele v produkčním prostředí. Vytváření testovacích uživatelů vám umožní ověřit, jestli zásady fungují podle očekávání, a pak ovlivnit reálné uživatele a potenciálně rušit přístup k aplikacím a prostředkům.

Některé organizace mají pro tento účel testovací klienty. Může však být obtížné znovu vytvořit všechny podmínky a aplikace v testovacím tenantovi pro kompletní testování výsledku zásad.

### <a name="create-a-test-plan"></a>Vytvoření testovacího plánu

Testovací plán je důležitý pro porovnání očekávaných výsledků a skutečných výsledků. Před testováním byste měli vždycky očekávat. Následující tabulka popisuje příklady testovacích případů. Upravte scénáře a očekávané výsledky na základě toho, jak jsou nakonfigurované zásady podmíněného přístupu.

| Zásady| Scenario| Očekávaný výsledek |
| - | - | - |
| [Vyžadovat MFA, pokud není v práci](untrusted-networks.md)| Autorizovaný uživatel se přihlásí do aplikace v důvěryhodném umístění/v práci.| Uživatel není vyzván k MFA. |
| [Vyžadovat MFA, pokud není v práci](untrusted-networks.md)| Autorizovaný uživatel se přihlásí do aplikace, když není v důvěryhodném umístění/v práci.| Uživatel je vyzván k ověřování MFA a úspěšně se může přihlásit. |
| [Vyžadovat MFA (pro správce)](../fundamentals/concept-fundamentals-security-defaults.md)| Globální správce se přihlásí do aplikace.| Správce je vyzván k ověřování MFA. |
| [Riziková přihlášení](../identity-protection/howto-identity-protection-configure-risk-policies.md)| Uživatel se přihlásí do aplikace pomocí neschváleného prohlížeče.| Správce je vyzván k ověřování MFA. |
| [Správa zařízení](require-managed-devices.md)| Autorizovaný uživatel se pokusí přihlásit z autorizovaného zařízení.| Udělen přístup |
| [Správa zařízení](require-managed-devices.md)| Autorizovaný uživatel se pokusí přihlásit z neautorizovaného zařízení.| Přístup zablokován |
| [Změna hesla pro rizikové uživatele](../identity-protection/howto-identity-protection-configure-risk-policies.md)| Autorizovaný uživatel se pokusí přihlásit pomocí ohrožených přihlašovacích údajů (přihlašování s vysokým rizikem).| Uživateli se zobrazí výzva ke změně hesla nebo je přístup na základě vašich zásad zablokovaný. |


### <a name="configure-the-test-policy"></a>Konfigurovat zásady testování

V [Azure Portal](https://portal.azure.com/)můžete nakonfigurovat zásady podmíněného přístupu v části Azure Active Directory > zabezpečení > podmíněný přístup.

Pokud se chcete dozvědět víc o vytváření zásad podmíněného přístupu, přečtěte si tento příklad: [zásady podmíněného přístupu, které se zobrazí při přihlášení uživatele k Azure Portal pro MFA](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json). Tento rychlý Start vám pomůže:

* Seznámení s uživatelským rozhraním

* Získání prvního dojmu o tom, jak podmíněný přístup funguje

### <a name="enable-the-policy-in-report-only-mode"></a>Povolit zásadu v režimu pouze sestavy

Chcete-li posoudit dopad zásady, začněte tím, že povolíte zásady v [režimu pouze sestavy](concept-conditional-access-report-only.md). Zásady pouze pro sestavy jsou vyhodnocovány během přihlašování, ale ovládací prvky a řízení relace nejsou vyhodnoceny. Když zásadu uložíte v režimu jenom pro sestavy, uvidíte dopad na přihlášení v reálném čase v protokolech přihlášení. V protokolech přihlášení vyberte událost a přejděte na kartu pouze sestavy a zobrazte výsledek jednotlivých zásad pouze pro sestavy.


![režim pouze sestav ](media/plan-conditional-access/report-only-mode.png)

Když vyberete zásadu, můžete si také prohlédnout, jak se úlohy a řízení přístupu zásady vyhodnotily pomocí obrazovky s podrobnostmi zásad. Aby se zásady daly použít pro přihlášení, musí být každé nakonfigurované přiřazení splněné. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>Pochopení dopadu zásad pomocí sešitu Insights a vytváření sestav

Agregovaný dopad zásad podmíněného přístupu můžete zobrazit v sešitě Insights a vytváření sestav. Pro přístup k sešitu potřebujete předplatné Azure Monitor a budete muset [streamovat protokoly přihlášení do pracovního prostoru Log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>Simulace přihlášení pomocí nástroje co-if

Dalším způsobem, jak ověřit zásady podmíněného přístupu, je použití [nástroje citlivostní](troubleshoot-conditional-access-what-if.md)instalace, který simuluje to, které zásady se použijí pro uživatele, který se přihlašuje za hypotetické podmínky. Vyberte atributy přihlášení, které chcete testovat (například uživatele, aplikace, platforma zařízení a umístění) a zjistěte, které zásady budou platit.

> [!NOTE] 
> I když simulované spuštění přináší dobrý nápad na dopad, který má zásada podmíněného přístupu, nenahrazuje skutečný testovací běh.

### <a name="test-your-policy"></a>Testování zásad

Proveďte všechny testy v testovacím plánu s testovacími uživateli.

**Ujistěte se, že testujete kritéria vyloučení zásady**. Můžete například vyloučit uživatele nebo skupinu ze zásady, která vyžaduje MFA. Otestujte, jestli jsou vyloučení uživatelé vyzváni k ověřování MFA, protože kombinace jiných zásad může pro tyto uživatele vyžadovat MFA.

### <a name="roll-back-policies"></a>Vracení zásad zpátky

V případě, že potřebujete vrátit nově implementované zásady, použijte jednu z následujících možností:

* **Zakažte tuto zásadu.** Zakázáním zásady se zajistí, že se nepoužije, když se uživatel pokusí přihlásit. Kdykoli se můžete vrátit zpět a povolit zásadu, když ji chcete použít.

![Povolit obrázek zásady](media/plan-conditional-access/enable-policy.png)

* **Vylučte uživatele nebo skupinu ze zásad.** Pokud uživatel nemá přístup k aplikaci, můžete se rozhodnout z této zásady vyloučit uživatele.

![vyloučení uživatelů a skupin](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  Tato možnost by se měla používat zřídka, jenom v situacích, kdy je uživatel důvěryhodný. Uživatel by měl být do zásady nebo skupiny co nejdříve přidán.

* **Odstraňte zásadu.** Pokud se už zásada nepožaduje, [odstraňte](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) ji.

## <a name="manage-access-to-cloud-apps"></a>Správa přístupu ke cloudovým aplikacím

Pomocí následujících možností správy můžete řídit a spravovat zásady podmíněného přístupu:

![Snímek obrazovky ukazuje možnosti správy pro zásady jazyka C, včetně pojmenovaných umístění, vlastních ovládacích prvků, Podmínky použití, připojení N a vybraných klasických zásad.](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>Pojmenovaná umístění

Podmínka umístění zásad podmíněného přístupu umožňuje propojení nastavení řízení přístupu k síťovým umístěním vašich uživatelů. Pomocí [pojmenovaných umístění](location-condition.md)můžete vytvořit logická seskupení rozsahů IP adres nebo zemí a oblastí.

### <a name="custom-controls"></a>Vlastní ovládací prvky

[Vlastní ovládací prvky](controls.md) přesměrují uživatele na kompatibilní službu, aby splnily požadavky na ověření mimo Azure AD. Aby bylo možné tento ovládací prvek vyhovět, bude prohlížeč uživatele přesměrován na externí službu, provede požadované ověření a pak bude přesměrován zpět do služby Azure AD. Azure AD ověří odpověď, a pokud byl uživatel úspěšně ověřen nebo ověřen, uživatel pokračuje v toku podmíněného přístupu.

### <a name="terms-of-use"></a>Podmínky použití

Než budete mít přístup k určitým cloudovým aplikacím ve vašem prostředí, můžete získat souhlas od uživatelů tím, že přijmete Podmínky použití (podmínky použití). Podle tohoto [rychlého startu vytvořte podmínek použití](require-tou.md).

## <a name="troubleshoot-conditional-access"></a>Řešení potíží s podmíněným přístupem

Pokud se uživateli vyskytne problém se zásadami podmíněného přístupu, shromážděte následující informace, které vám usnadní řešení potíží.

* Hlavní název uživatele

* Zobrazované jméno uživatele

* Název operačního systému

* Časové razítko (přibližná v pořádku)

* Cílová aplikace

* Typ klientské aplikace (prohlížeč vs Client)

* ID korelace (Toto je jedinečné pro přihlášení)

Pokud uživatel obdržel zprávu s odkazem s dalšími podrobnostmi, může získat většinu těchto informací za vás.

![Nejde získat do chybové zprávy aplikace.](media/plan-conditional-access/cant-get-to-app.png)

Jakmile shromáždíte informace, podívejte se na následující zdroje informací:

* [Problémy s přihlašováním pomocí podmíněného přístupu](troubleshoot-conditional-access.md) – pochopení neočekávaných výsledků přihlašování souvisejících s podmíněným přístupem pomocí chybových zpráv a protokolu přihlášení k Azure AD.

* [Použití nástroje What-If](troubleshoot-conditional-access-what-if.md) – Pochopte, proč zásada byla nebo nebyla pro uživatele v konkrétní situaci použita nebo zda se zásada použila ve známém stavu.

## <a name="next-steps"></a>Další kroky

[Další informace o Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Další informace o identitě identity](../identity-protection/overview-identity-protection.md)

[Správa zásad podmíněného přístupu pomocí rozhraní Microsoft Graph API](/graph/api/resources/conditionalaccesspolicy)
