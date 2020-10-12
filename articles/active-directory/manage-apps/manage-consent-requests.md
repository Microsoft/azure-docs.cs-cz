---
title: Správa souhlasu s aplikacemi a vyhodnocení žádostí o souhlas v Azure Active Directory
description: Naučte se spravovat žádosti o souhlas, pokud je uživatel zakázaný nebo omezený, a jak vyhodnotit žádost o souhlas správce na úrovni tenanta pro aplikaci v Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.openlocfilehash: 3d95d2551f8e078f4252a19dc850345793c040d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89420451"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Správa souhlasu s aplikacemi a vyhodnocení žádostí o souhlas

Microsoft [doporučuje](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) zakázat pro aplikace souhlas s koncovým uživatelem. Tím se soustřeďuje rozhodovací proces na tým zabezpečení a správce identit ve vaší organizaci.

Po zákazu nebo omezení souhlasu koncového uživatele je k dispozici několik důležitých informací, které zajistí, že vaše organizace zůstane zabezpečená a zároveň umožňuje použití důležitých podnikových aplikací. Tyto kroky jsou zásadní pro minimalizaci dopadu na tým podpory vaší organizace a správce IT a zároveň zabraňují používání nespravovaných účtů v aplikacích třetích stran.

## <a name="process-changes-and-education"></a>Zpracování změn a vzdělávání

 1. Zvažte povolení [pracovního postupu pro vyjádření souhlasu správce (Preview)](configure-admin-consent-workflow.md) , který uživatelům umožní požádat o schválení správce přímo z obrazovky pro vyjádření souhlasu.

 2. Zajistěte, aby všichni správci pochopili [rámec oprávnění a souhlasu](../develop/consent-framework.md), jak se [výzva k vyjádření souhlasu](../develop/application-consent-experience.md) funguje, a jak [vyhodnotit žádost o souhlas správce na úrovni tenanta](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Zkontrolujte stávající procesy vaší organizace, aby si uživatelé mohli požádat o schválení pro aplikaci správcem, a pokud to bude nutné, proveďte v případě potřeby aktualizace. V případě změny procesů:
    * Aktualizujte příslušnou dokumentaci, monitorování, automatizaci atd.
    * Oznamovat změny procesů všem postiženým uživatelům, vývojářům, týmům podpory a správcům IT.

## <a name="auditing-and-monitoring"></a>Auditování a sledování

1. [Auditujte aplikace a udělená oprávnění](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) ve vaší organizaci, abyste zajistili, že přístup k datům předtím udělil žádné neoprávněné nebo podezřelé aplikace.

2. Projděte si téma [zjištění a náprava nezákonných grantů souhlasu v Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) , kde najdete další osvědčené postupy a bezpečnostní opatření proti podezřelým aplikacím, které požadují

3. Pokud má vaše organizace odpovídající licenci:

    * Použijte další [funkce auditování aplikace OAuth v Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth).
    * Pomocí [Azure monitor sešity můžete monitorovat oprávnění a související aktivity související se souhlasem](../reports-monitoring/howto-use-azure-monitor-workbooks.md) . Sešit s *informacemi o souhlasu* nabízí zobrazení aplikací podle počtu neúspěšných žádostí o souhlas. To může být užitečné k určení priorit aplikací pro správce, kteří si můžou prohlédnout a rozhodnout, jestli mají udělit souhlas správce.

### <a name="additional-considerations-for-reducing-friction"></a>Další požadavky pro snížení tření

Aby se minimalizoval dopad na důvěryhodné, nepostradatelné aplikace, které se už používají, zvažte proaktivní udělení souhlasu správce aplikacím, které mají vysoký počet udělení souhlasu uživatele:

1. Využijte inventarizaci aplikací, které jsou už přidané do vaší organizace, s vysokým využitím na základě přihlašovacích protokolů nebo aktivity udělení souhlasu. [Skript](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) PowerShellu se dá použít k rychlému a snadnému zjištění aplikací s velkým počtem grantů na vyjádření souhlasu uživatele.

2. Vyhodnoťte hlavní aplikace, u kterých ještě nebyl udělen souhlas správce.

   > [!IMPORTANT]
   > Pečlivě vyhodnoťte aplikaci před udělením souhlasu správce na úrovni tenanta, a to i v případě, že je mnoho uživatelů v organizaci již nesouhlasí.

3. Pro každou schválenou aplikaci udělte souhlas správce na úrovni tenanta pomocí jedné z metod popsaných níže.

4. U každé schválené aplikace zvažte [omezení přístupu uživatelů](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Vyhodnocení žádosti o souhlas správce na úrovni tenanta

Udělení souhlasu správce na úrovni tenanta je citlivá operace.  Oprávnění se udělují jménem celé organizace a můžou zahrnovat oprávnění k pokusu o vysoce privilegované operace. Například Správa rolí, úplný přístup ke všem poštovním schránkám nebo všem webům a úplná zosobnění uživatelů.

Před udělením souhlasu správce na úrovni tenanta musíte zajistit, aby aplikace a vydavatel aplikace důvěřovaly pro úroveň přístupu, kterou udělíte. Pokud si nejste jistí, kdo aplikace řídí a proč aplikace požaduje oprávnění, *neudělí souhlas*.

Následující seznam uvádí doporučení, která je potřeba vzít v úvahu při vyhodnocování žádosti o udělení souhlasu správce.

* **Seznamte se s [oprávněními a rámec souhlasu](../develop/consent-framework.md) na platformě Microsoft identity.**

* **Pochopte rozdíl mezi [delegovanými oprávněními a oprávněními aplikace](../develop/v2-permissions-and-consent.md#permission-types).**

   Oprávnění aplikací umožňují aplikaci přístup k datům celé organizace bez nutnosti zásahu uživatele. Delegovaná oprávnění umožňují, aby aplikace fungovala jménem uživatele, který byl v nějakém okamžiku podepsán do aplikace.

* **Pochopení požadovaných oprávnění**

   Oprávnění požadovaná aplikací jsou uvedena na stránce s výzvou k [vyjádření souhlasu](../develop/application-consent-experience.md). Při rozbalení názvu oprávnění se zobrazí popis oprávnění. Popis oprávnění aplikace bude obecně končit "bez přihlášeného uživatele". Popis delegovaných oprávnění bude obecně končit "jménem přihlášeného uživatele". Oprávnění pro rozhraní Microsoft Graph API jsou popsána v [referenčních informacích k Microsoft Graph oprávnění](https://docs.microsoft.com/graph/permissions-reference) – informace o dalších rozhraních API najdete v dokumentaci pro pochopení oprávnění, která zveřejňuje.

   Pokud nerozumíte požadovanému oprávnění, *neudělí souhlas*.

* **Zjistěte, která aplikace požaduje oprávnění a kdo aplikaci publikoval.**

   Přistupují opatrně škodlivých aplikací, které se pokoušejí najít jako jiné aplikace.

   Pokud si nejste jistí legitimita aplikace nebo její vydavatele, *neudělí souhlas*. Místo toho se můžete pokusit o další potvrzení (například přímo z vydavatele aplikace).

* **Ujistěte se, že požadovaná oprávnění jsou zarovnána s funkcemi, které očekáváte od aplikace.**

   Například aplikace nabízející správu webů služby SharePoint může vyžadovat delegovaný přístup ke čtení všech kolekcí webů, ale nemusí nutně potřebovat úplný přístup ke všem poštovním schránkám nebo úplné oprávnění k zosobnění v adresáři.

   Pokud máte podezření, že aplikace požaduje více oprávnění, než potřebuje, *neudělí souhlas*. Podrobnější informace získáte od vydavatele aplikace.

## <a name="granting-consent-as-an-administrator"></a>Udělení souhlasu jako správce

### <a name="granting-tenant-wide-admin-consent"></a>Udělení souhlasu správce na úrovni tenanta
Podrobné pokyny pro udělení souhlasu správce na úrovni tenanta od Azure Portal, pomocí Azure AD PowerShellu nebo ze samotného výzvy k vyjádření souhlasu najdete v tématu [udělení souhlasu správce pro celou aplikaci](grant-admin-consent.md) .

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Udělení souhlasu jménem konkrétního uživatele
Místo udělení souhlasu celé organizaci může správce použít [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api) k udělení souhlasu pro delegovaná oprávnění jménem jednoho uživatele. Další informace najdete v tématu [získání přístupu jménem uživatele](https://docs.microsoft.com/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Omezení přístupu uživatelů k aplikacím
Přístup uživatelů k aplikacím se pořád může omezit i v případě, že je udělený souhlas správce na úrovni tenanta. Další informace o tom, jak vyžadovat přiřazení uživatele k aplikaci, najdete v tématu [metody přiřazení uživatelů a skupin](methods-for-assigning-users-and-groups.md).

Další informace, včetně toho, jak zvládnout další složité scénáře, najdete v tématu [používání služby Azure AD pro správu přístupu k aplikacím](what-is-access-management.md).

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Zakažte všechny operace souhlasu uživatele s ostatními aplikacemi.
Zákaz souhlasu uživatele pro celý adresář zabrání koncovým uživatelům v posílání do libovolné aplikace. Správci mohou i nadále souhlasit jménem uživatele. Chcete-li získat další informace o souhlasu aplikace a o tom, proč můžete nebo nebudete chtít souhlas, přečtěte si článek [porozumění souhlasu uživatele a správce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Pokud chcete zakázat všechny operace souhlasu uživatele s ostatními uživateli v celém adresáři, postupujte podle následujících kroků:
1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.
3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4.  V navigační nabídce vyberte **Uživatelé a skupiny** .
5.  Vyberte **Uživatelská nastavení**.
6.  Zakažte všechny operace souhlasu s ostatními uživateli tím, že nastavením **uživatelů umožníte aplikacím přístup k jejich** přepínači **ne** a klikněte na tlačítko **Uložit** .

## <a name="next-steps"></a>Další kroky
* [Pět kroků pro zabezpečení infrastruktury identity](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)
* [Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)
* [Konfigurace způsobu vyjadřování souhlasu koncových uživatelů s aplikacemi](configure-user-consent.md)
* [Oprávnění a souhlas na platformě Microsoft identity](../develop/active-directory-v2-scopes.md)
