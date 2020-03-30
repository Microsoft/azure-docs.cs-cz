---
title: Správa souhlasu s aplikacemi a vyhodnocení žádostí o souhlas – Azure AD
description: Přečtěte si, jak spravovat žádosti o souhlas, když je souhlas uživatele zakázán nebo omezen, a jak vyhodnotit žádost o souhlas správce pro celý klient s aplikací.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367847"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Správa souhlasu s žádostmi a vyhodnocení žádostí o souhlas

Společnost Microsoft [doporučuje](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) zakázat souhlas koncových uživatelů s aplikacemi. Tím se centralizuje rozhodovací proces s týmem správce zabezpečení a identity vaší organizace.

Po zakázání nebo omezení souhlasu koncových uživatelů existuje několik důležitých aspektů, které zajistí, že vaše organizace zůstane v bezpečí a zároveň umožní použití důležitých podnikových aplikací. Tyto kroky jsou zásadní pro minimalizaci dopadu na tým podpory vaší organizace a správce IT a zároveň zabraňují používání nespravovaných účtů v aplikacích třetích stran.

## <a name="process-changes-and-education"></a>Procesní změny a vzdělávání

 1. Zvažte možnost povolení [pracovního postupu souhlasu správce (preview),](configure-admin-consent-workflow.md) aby uživatelé mohli požádat o schválení správce přímo z obrazovky souhlasu.

 2. Zajistěte, aby všichni správci rozuměli [rozhraní pro oprávnění a souhlas](../develop/consent-framework.md), jak výzva k [souhlasu](../develop/application-consent-experience.md) funguje a jak [vyhodnotit žádost o souhlas správce pro celý klient](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Zkontrolujte stávající procesy vaší organizace, aby uživatelé požádali o schválení aplikace správcem a v případě potřeby proveďte aktualizace. Pokud dojde ke změně procesů:
    * Aktualizujte příslušnou dokumentaci, monitorování, automatizaci a tak dále.
    * Komunikujte změny procesů všem postiženým uživatelům, vývojářům, týmům podpory a správcům IT.

## <a name="auditing-and-monitoring"></a>Auditování a sledování

1. [Auditujte aplikace a udělujte oprávnění](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) ve vaší organizaci, abyste zajistili, že k datům dříve nebyl udělen žádný neoprávněný nebo podezřelý přístup k aplikacím.

2. Zkontrolujte [zjišťování a nápravu nedovoleného souhlasu granty v Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) pro další osvědčené postupy a záruky proti podezřelým aplikacím požadujícím souhlas OAuth.

3. Pokud má vaše organizace příslušnou licenci:

    * Používejte další [funkce auditování aplikací OAuth v Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth).
    * Pomocí [sešitů Azure Monitor můžete monitorovat oprávnění a](../reports-monitoring/howto-use-azure-monitor-workbooks.md) aktivity související s souhlasem. Sešit *Přehledy souhlasu* poskytuje zobrazení aplikací podle počtu neúspěšných žádostí o souhlas. To může být užitečné pro stanovení priorit aplikací pro správce ke kontrole a rozhodnout, zda jim udělit souhlas správce.

### <a name="additional-considerations-for-reducing-friction"></a>Další aspekty pro snížení tření

Chcete-li minimalizovat dopad na důvěryhodné důležité podnikové aplikace, které jsou již používány, zvažte proaktivní udělení souhlasu správce aplikacím, které mají vysoký počet udělených souhlasů uživatelů:

1. Udělejte si soupis aplikací, které už byly přidány do vaší organizace s vysokým využitím, na základě protokolů přihlášení nebo aktivity udělení souhlasu. Skript prostředí [PowerShell](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) lze použít k rychlému a snadnému zjišťování aplikací s velkým počtem udělených souhlasů uživatelů.

2. Vyhodnoťte nejlepší aplikace, kterým ještě nebyl udělen souhlas správce.

   > [!IMPORTANT]
   > Pečlivě vyhodnoťte aplikaci před udělením souhlasu správce pro celý klient, i když mnoho uživatelů v organizaci již souhlasilo pro sebe.

3. Pro každou aplikaci, která je schválena, udělte souhlas správce pro celý klient pomocí jedné z níže uvedených metod.

4. U každé schválené aplikace zvažte [omezení přístupu uživatelů](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Vyhodnocení žádosti o souhlas správce pro celý klient

Udělení souhlasu správce pro celý klient je citlivá operace.  Oprávnění budou udělena jménem celé organizace a mohou zahrnovat oprávnění k pokusům o vysoce privilegované operace. Například správa rolí, úplný přístup ke všem poštovním schránkám nebo všem webům a plné zosobnění uživatelů.

Před udělením souhlasu správce pro celý tenant a musíš zajistit, abyste aplikaci a vydavateli aplikace důvěřovali, abyste měli úroveň přístupu, který udělujete. Pokud si nejste jisti, že rozumíte tomu, kdo řídí aplikaci a proč aplikace požaduje oprávnění, *neudělte souhlas*.

Následující seznam obsahuje některá doporučení, která je třeba zvážit při vyhodnocování žádosti o udělení souhlasu správce.

* **Seznamte se s [rozhraním o oprávněních a souhlasech](../develop/consent-framework.md) na platformě microsoftidentity.**

* **Pochopit rozdíl mezi [delegovanými oprávněními a oprávněními aplikací](../develop/v2-permissions-and-consent.md#permission-types).**

   Oprávnění aplikace umožňují aplikaci přístup k datům pro celou organizaci bez jakékoli interakce s uživatelem. Delegovaná oprávnění umožňují aplikaci jednat jménem uživatele, který byl v určitém okamžiku přihlášen k aplikaci.

* **Seznamte se s požadovanými oprávněními.**

   Oprávnění požadovaná aplikací jsou uvedena v [řádku s výzvou k souhlasu](../develop/application-consent-experience.md). Rozbalením názvu oprávnění se zobrazí popis oprávnění. Popis oprávnění aplikace obvykle skončí v "bez přihlášeného uživatele". Popis delegovaných oprávnění bude obecně končit "jménem přihlášeného uživatele". Oprávnění pro rozhraní Microsoft Graph API jsou popsána v [Microsoft Graph Permissions Reference]- naleznete v dokumentaci pro jiná rozhraní API pochopit oprávnění, která zveřejňují.

   Pokud nerozumíte požadovanému povolení, *neudělte souhlas*.

* **Zjistěte, která aplikace požaduje oprávnění a kdo ji publikoval.**

   Buďte opatrní škodlivých aplikací se snaží vypadat jako jiné aplikace.

   Máte-li pochybnosti o oprávněnosti aplikace nebo jejího vydavatele, *neudělte souhlas*. Místo toho vyhledejte další potvrzení (například přímo od vydavatele aplikace).

* **Ujistěte se, že požadovaná oprávnění jsou zarovnána s funkcemi, které očekáváte od aplikace.**

   Například aplikace nabízející správu webu služby SharePoint může vyžadovat delegovaný přístup ke všem kolekcím webů, ale nemusí nutně vyžadovat úplný přístup ke všem poštovním schránkám nebo úplná oprávnění k zosobnění v adresáři.

   Pokud máte podezření, že aplikace požaduje více oprávnění, než potřebuje, *neudělte souhlas*. Další podrobnosti získáte od vydavatele aplikace.

## <a name="granting-consent-as-an-administrator"></a>Udělení souhlasu správcem

### <a name="granting-tenant-wide-admin-consent"></a>Udělení souhlasu správce pro celý klient

Viz [Udělení souhlasu správce pro celý tenant a aplikaci](grant-admin-consent.md) pro podrobné pokyny pro udělení souhlasu správce pro celý tenant a z portálu Azure, pomocí Azure AD PowerShell, nebo z výzvy k souhlasu sám.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Udělení souhlasu jménem konkrétního uživatele

Namísto udělení souhlasu pro celou organizaci může správce také použít [rozhraní MICROSFT Graph API](https://docs.microsoft.com/graph/use-the-api) k udělení souhlasu s delegovanými oprávněními jménem jednoho uživatele. Další informace naleznete v [tématu Získání přístupu jménem uživatele](https://docs.microsoft.com/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Omezení přístupu uživatelů k aplikacím

Přístup uživatelů k aplikacím může být stále omezen, i když byl udělen souhlas správce pro celý klient. Další informace o tom, jak vyžadovat přiřazení uživatele k aplikaci, naleznete [v metodách pro přiřazení uživatelů a skupin](methods-for-assigning-users-and-groups.md).

Podrobnější přehled, včetně toho, jak zpracovat další složité scénáře, najdete v článku [pomocí Azure AD pro správu přístupu k aplikacím](what-is-access-management.md).

## <a name="next-steps"></a>Další kroky

[Pět kroků k zabezpečení infrastruktury identit](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[Konfigurace pracovního postupu souhlasu správce](configure-admin-consent-workflow.md)

[Konfigurace způsobu, jakým koncoví uživatelé uzaměňují souhlas s aplikacemi](configure-user-consent.md)

[Oprávnění a souhlas v platformě microsoft identity](../develop/active-directory-v2-scopes.md)

[Azure AD na StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)