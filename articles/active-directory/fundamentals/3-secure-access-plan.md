---
title: Vytvoření plánu zabezpečení pro externí přístup k Azure Active Directory
description: Naplánujte zabezpečení pro externí přístup k prostředkům vaší organizace.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd04d24e0e46b053381858be037a009b1987f8e9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932516"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. vytvoření plánu zabezpečení pro externí přístup 

Teď, když jste [zjistili požadované zabezpečení stav Security stav pro externí přístup](1-secure-access-posture.md) a [zjistili jste aktuální stav spolupráce](2-secure-access-current-state.md), můžete vytvořit externí plán zabezpečení uživatelů a zásad správného řízení. 

Tento plán by měl zdokumentovat následující:

* Aplikace a další prostředky, které mají být seskupeny pro přístup.

* Příslušné přihlašovací podmínky pro externí uživatele. Ty můžou zahrnovat stav zařízení, přihlašovací umístění, požadavky klientské aplikace a riziko pro uživatele.

* Zásady pro firmy, kdy se má zkontrolovat a odebrat přístup 

* Uživatelská populace, která se má seskupit a zpracovat podobně.

Až budou tyto oblasti popsané, můžete k implementaci tohoto plánu použít zásady správy identit a přístupu od Microsoftu nebo jiného poskytovatele identity (IdP).

## <a name="document-resources-to-be-grouped-for-access"></a>Dokumenty pro přístup k prostředkům, které se mají seskupit

Existuje několik způsobů, jak seskupit prostředky pro přístup. 

* Microsoft Teams seskupuje soubory, vlákna konverzace a další prostředky na jednom místě. Měli byste formulovat strategii pro externí přístup pro Microsoft Teams. Podívejte se [na zabezpečený přístup k týmům, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md).

* Balíčky pro přístup pro správu oprávnění umožňují vytvořit jeden balíček aplikací a dalších prostředků, ke kterým můžete udělit přístup. 

* Zásady podmíněného přístupu se dají použít až pro 250 aplikací se stejnými požadavky na přístup.

Budete ale spravovat přístup, ale musíte zdokumentovat, které aplikace se mají seskupit dohromady. Požadavky by měly zahrnovat:

* **Profil rizika**. Jaké riziko má vaše podnikání v případě, že špatný objekt actor získal přístup k aplikaci? Zvažte kódování každé aplikace jako vysoké, střední nebo nízké. Buďte opatrní při seskupování vysoce rizikových aplikací s nízkým rizikem. 

   * Aplikace pro dokumenty, které by se neměly nikdy sdílet s externími uživateli

* **Architektury dodržování předpisů**. Co když musí aplikace splňovat nějaké architektury dodržování předpisů? Jaké jsou požadavky na přístup a kontrolu?

* **Aplikace pro konkrétní role nebo oddělení úloh** Existují aplikace, které by se měly seskupit, protože všichni uživatelé v určité roli úlohy nebo oddělení budou potřebovat přístup?

* **Aplikace zaměřené na spolupráci**. Které aplikace zaměřené na spolupráci by měly mít přístup k externím uživatelům? Je možné, že bude potřeba mít přístup k Microsoft teams a SharePointu. V případě aplikací pro produktivitu v Office 365, jako je Word a Excel, budou externí uživatelé přinášet vlastní licence, nebo je budete muset licencovat a poskytnout přístup?

Pro každé seskupení aplikací a prostředků, které chcete zpřístupnit externím uživatelům, zdokumentujte tyto informace:

* Popisný název skupiny, například *High_Risk_External_Access_Finance*. 

* Úplný seznam všech aplikací a prostředků ve skupině.

* Vlastníci aplikace a prostředků a kontaktní údaje.

* Bez ohledu na to, jestli je přístup řízený nebo delegovaný vlastníkovi firmy.

* Všechny požadavky, například dokončení kontroly na pozadí nebo školení, pro přístup.

* Všechny požadavky na dodržování předpisů pro přístup k prostředkům.

* Jakékoli další výzvy, například vyžadující vícefaktorové ověřování pro konkrétní prostředky.

* Jak často bude přístup přezkoumán, kým a kde bude zdokumentován.

Tento typ plánu zásad správného řízení může a taky musí být dokončený i pro interní přístup.

## <a name="document-sign-in-conditions-for-external-users"></a>Dokumentující podmínky pro přihlášení externích uživatelů

V rámci vašeho plánu musíte při přístupu k prostředkům určit požadavky na přihlášení pro externí uživatele. Požadavky na přihlášení jsou často založené na profilu rizika prostředků a hodnocení rizik přihlášení uživatelů.

Přihlašovací podmínky se konfigurují v [podmíněném přístupu Azure AD](../conditional-access/overview.md) a tvoří podmínku a výsledek. Například kdy požadovat vícefaktorové ověřování

**Podmínky přihlášení na základě rizik prostředků.**

| Profil rizika aplikace| Vezměte v úvahu tyto zásady, které aktivují Multi-Factor Authentication. |
| - |-|
| Nízké riziko| Vyžadovat MFA pro konkrétní sady aplikací |
| Riziko med| Vyžadovat vícefaktorové ověřování, když se vyskytují další rizika |
| Vysoké riziko| Vyžadovat vícefaktorové ověřování vždy pro externí uživatele |


V současné době můžete [vymáhat službu Multi-Factor Authentication pro uživatele B2B ve vašem tenantovi](https://docs.microsoft.com/azure/active-directory/external-identities/b2b-tutorial-require-mfa). 

**Podmínky přihlášení podle uživatelů a zařízení**.

| Riziko uživatele nebo přihlašování| Vzít v úvahu tyto zásady |
| - | - |
| Zařízení| Vyžadování zařízení, které splňuje požadavky |
| Mobilní aplikace| Vyžadovat schválené aplikace |
| Identity Protection zobrazuje vysoké riziko| Vyžadovat, aby uživatel změnil heslo |
| Síťové umístění| Vyžadovat přihlášení z konkrétního rozsahu IP adres do vysoce důvěrných projektů |

Pokud dnes použijete pro zásadu stav zařízení, musí být zařízení zaregistrované nebo připojené k vašemu tenantovi. 

Je možné použít [zásady na základě rizika ochrany identity](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk) . Problémy se ale musí zmírnit v domovském tenantovi uživatele.

V případě [síťových umístění](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-location)můžete omezit přístup k libovolnému rozsahu IP adres, který vlastníte. To můžete použít, pokud chcete, aby externí partneři přistupovali k aplikaci pouze v případě, že jsou v lokalitě ve vaší organizaci.

[Přečtěte si další informace o zásadách podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

## <a name="document-access-review-policies"></a>Zásady kontroly přístupu k dokumentu

Zdokumentujte své obchodní zásady, když potřebujete zkontrolovat přístup k prostředkům a kdy potřebujete přístup k účtu pro externí uživatele odebrat. Vstupy těchto rozhodnutí můžou zahrnovat:

* Požadavky popsané v jakýchkoli architekturách dodržování předpisů.

* Interní obchodní zásady a procesy

* Chování uživatele

I když budou vaše zásady vysoce přizpůsobené vašim potřebám, vezměte v úvahu následující skutečnosti:

* Kontroly **přístupu pro správu nároků**. Použití funkcí v rámci správy nároků na

   * [Automatické vypršení platnosti balíčků přístupu](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-lifecycle-policy)a přístup k nim tedy externí uživatel.

   * Nastavte [požadovanou četnost kontroly](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-reviews-create) přístupu.

   * Pokud používáte [připojené organizace](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-organization) k seskupení všech uživatelů z jednoho partnera, naplánujte pravidelné recenze s vlastníkem firmy a zástupcem partnera.

* **Microsoft 365 skupiny** Nastavte [Zásady vypršení platnosti skupiny](https://docs.microsoft.com/microsoft-365/solutions/microsoft-365-groups-expiration-policy?view=o365-worldwide) pro Microsoft 365 skupiny, na které se přizvaní externí uživatelé. 

* **Další možnosti**. Pokud mají externí uživatelé přístup mimo balíčky pro přístup nebo Microsoft 365 skupiny oprávnění pro správu, nastavte obchodní proces na kontrolu, kdy by se měly účty vystavovat jako neaktivní nebo odstraněné. Příklad:

   * Odeberte možnost přihlašování pro libovolný účet, ke kterému nejste přihlášení, a to po dobu 90 dnů.

   * Vyhodnoťte potřeby přístupu a proveďte akci na konci každého projektu s externími uživateli.

 

## <a name="determine-your-access-control-methods"></a>Určení metod řízení přístupu

Teď, když víte, ke kterým chcete řídit přístup, jak by se měly tyto prostředky seskupovat pro běžný přístup a požadované zásady přihlašování a přístupu, se můžete rozhodnout, jak tento plán provést. 

Některé funkce, například [Správa nároků](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview), jsou k dispozici pouze s licencemi Azure AD Premium 2 (P2). Licence pro Microsoft 365 E5 a Office 365 E5 zahrnují licence Azure AD P2. 

Další kombinace Microsoft 365, Office 365 a Azure AD umožňují také některé funkce pro správu externích uživatelů. Další informace najdete v tématu [Information Protection](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) .

> [!NOTE]
> Licence jsou vázané na uživatele. Proto můžete mít konkrétní uživatele, včetně správců a uživatelských vlastníků delegovaný řízení přístupu, na úrovni Azure AD P2 nebo Microsoft 365 E5 bez povolení těchto licencí pro všechny uživatele. Prvních 50 000 externích uživatelů je zdarma. Pokud nepovolíte licence P2 pro ostatní interní uživatele, nebudou moci používat funkce správy nároků, jako jsou balíčky přístupu. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Řízení přístupu pomocí Azure AD P2 a Microsoft/Office 365 E5
Azure AD P2 a Microsoft 365 E5 mají úplnou sadu nástrojů zabezpečení a zásad správného řízení.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>Zřizování, přihlašování, kontrola přístupu a zrušení zřízení. Tučné položky jsou preferované metody

| Funkce| Zřízení externích uživatelů| Vynutili reqs přihlášení.| Kontrola přístupu| Zrušit zřízení přístupu |
| - | - | - | - | - |
| Spolupráce B2B Azure AD| Pozvání prostřednictvím e-mailu, jednorázového hesla, samoobslužné služby| | **Pravidelná kontrola na partnera**| Odebrat účet<br>Omezit přihlášení |
| Správa nároků| **Přidat uživatele prostřednictvím přiřazení nebo samoobslužného přístupu**| | Kontroly přístupu|**Vypršení platnosti nebo odebrání balíčku přístupu**|
| Skupiny Office 365| | | Kontrola členství ve skupinách| Vypršení platnosti nebo odstranění skupiny<br> Odebrání skupiny formulářů |
| Skupiny zabezpečení služby Azure AD| | **Zásady podmíněného přístupu** (podle potřeby přidejte externí uživatele do skupin zabezpečení)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Přístup k prostředkům. Tučné položky jsou preferované metody

|Funkce | Přístup k prostředkům aplikace &| Přístup k SharePointu & OneDrive| Přístup k týmům| Zabezpečení dokumentu & e-mailu |
| - |-|-|-|-|
| Správa nároků| **Přidat uživatele prostřednictvím přiřazení nebo samoobslužného přístupu**| **Přístupové balíčky**| **Přístupové balíčky**|  |
| Skupina Office 365| | Přístup k lokalitám (a souvisejícímu obsahu), který je součástí skupiny| Přístup k týmům (a souvisejícímu obsahu), který je součástí skupiny|  |
| Popisky citlivosti| | **Ruční a automatické klasifikace a omezení přístupu**| **Ruční a automatické klasifikace a omezení přístupu**| **Ruční a automatické klasifikace a omezení přístupu** |
| Skupiny zabezpečení služby Azure AD| **Zásady podmíněného přístupu pro přístup, které nejsou zahrnuté v balíčcích přístupu**| | |  |


### <a name="entitlement-management"></a>Správa nároků 

[Balíčky pro přístup pro správu nároků](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-create) umožňují zřizování a rušení zajišťování přístupu ke skupinám a týmům, aplikacím a webům SharePointu. Můžete definovat, které připojené organizace mají povolený přístup, jestli jsou žádosti samoobslužné služby povolené a jaké pracovní postupy schvalování jsou vyžadované (pokud nějaké existují) pro udělení přístupu. Aby se zajistilo, že přístup netrvá déle než třeba, můžete definovat zásady vypršení platnosti a kontroly přístupu pro každý balíček přístupu. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Řízení přístupu pomocí Azure AD P1 a Microsoft/Office 365 E3
Můžete dosáhnout robustního řízení pomocí Azure AD P1 a Microsoft 365 E3

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Zřizování, přihlašování, kontrola přístupu a zrušení zřízení


|Funkce | Zřízení externích uživatelů| Vymáhat požadavky na přihlášení| Kontrola přístupu| Zrušit zřízení přístupu |
| - |-|-|-|-|
| Spolupráce B2B Azure AD| **Pozvání prostřednictvím e-mailu, jednorázového hesla, samoobslužné služby**| Přímá federace B2B| **Pravidelná kontrola na partnera**| Odebrat účet<br>Omezit přihlášení |
| Skupiny Microsoft nebo Office 365| | | | Vypršení platnosti nebo odstranění skupiny.<br>Odebrání ze skupiny |
| Skupiny zabezpečení| | **Přidat externí uživatele do skupin zabezpečení (org, tým, projekt atd.)**| |  |
| Zásady podmíněného přístupu| | **Zásady podmíněného přístupu přihlašování pro externí uživatele**| |  |


 ### <a name="access-to-resources"></a>Přístup k prostředkům.

|Funkce | Přístup k prostředkům aplikace &| Přístup k SharePointu & OneDrive| Přístup k týmům| Zabezpečení dokumentu & e-mailu |
| - |-|-|-|-|
| Skupiny Microsoft nebo Office 365| | **Přístup k lokalitám, které jsou součástí skupiny (a přidruženého obsahu)**|**Přístup k týmům, které jsou součástí Microsoft 365 skupiny (a souvisejícího obsahu)**|  |
| Popisky citlivosti| | Ruční klasifikace a omezení přístupu| Ruční klasifikace a omezení přístupu.| Ruční klasifikace pro omezení a šifrování |
| Zásady podmíněného přístupu| Zásady podmíněného přístupu pro řízení přístupu| | |  |
| Další metody| | Omezte přístup k webu služby SharePoint pomocí skupin zabezpečení.<br>Zakáže přímé sdílení.| **Omezení externích pozvánek v rámci týmů**|  |


### <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích o zabezpečení externího přístupu k prostředkům. Doporučujeme, abyste provedli akce v uvedeném pořadí.

1. [Určení stav zabezpečení pro externí přístup](1-secure-access-posture.md)

2. [Zjistit aktuální stav](2-secure-access-current-state.md)

3. [Vytvořte si plán zásad správného řízení](3-secure-access-plan.md) (jste tady.)

4. [Použití skupin pro zabezpečení](4-secure-access-groups.md)

5. [Přechod do Azure AD B2B](5-secure-access-b2b.md)

6. [Zabezpečený přístup se správou oprávnění](6-secure-access-entitlement-managment.md)

7. [Zabezpečený přístup pomocí zásad podmíněného přístupu](7-secure-access-conditional-access.md)

8. [Zabezpečený přístup s popisky citlivosti](8-secure-access-sensitivity-labels.md)

9. [Zabezpečený přístup k Microsoft teams, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md)