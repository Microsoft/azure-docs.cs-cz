---
title: 'Azure AD Connect: Přihlášení uživatele | Dokumentace Microsoftu'
description: Azure AD Connect přihlášení uživatele pro vlastní nastavení.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 38086d0e975956aefe4fcde4eda67d939d58f617
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365838"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect uživatelské možnosti přihlášení
Azure Active Directory (Azure AD) Connect umožňuje uživatelům se přihlásit k cloudových a místních prostředků pomocí přihlašovali stejnými hesly. Tento článek popisuje klíčové koncepty pro každý model identit vám pomůže vybrat identitu, která chcete použít pro přihlášení k Azure AD.

Pokud jste již obeznámeni s modelem identity Azure AD a chcete získat další informace o konkrétní metodu, najdete v článku na příslušný odkaz:

* [Synchronizace hodnot hash hesel](#password-hash-synchronization) s [bezproblémové jednotné přihlašování (SSO)](how-to-connect-sso.md)
* [Předávací ověřování](how-to-connect-pta.md) s [bezproblémové jednotné přihlašování (SSO)](how-to-connect-sso.md)
* [Federované jednotné přihlašování (pomocí služby Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federace s PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Je dobré si uvědomit, že konfigurace federace pro službu Azure AD, je vytvořit vztah důvěryhodnosti mezi vaším tenantem Azure AD a federovaných domén. S touto doménou federovaného vztahu důvěryhodnosti budou mít uživatelé přístup k prostředkům cloudové služby Azure AD v rámci tenanta.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Výběr uživatele přihlašovací metoda pro vaši organizaci
První rozhodnutí implementace služby Azure AD Connect je zvolíte, jakou metodu ověřování, budou uživatelé používat k přihlášení. Je důležité zajistit, aby že zvolit správnou metodu, která vyhovuje zabezpečení a vyšším požadavkům vaší organizace. Ověřování je důležité, protože se ověřit identit uživatelů k přístupu k aplikacím a datům v cloudu. Zvolit metodu správné ověření, je potřeba zvážit čas, stávající infrastruktury, složitost a náklady na implementaci podle vašeho výběru. Tyto faktory se liší pro každý organizace a může v průběhu času měnit.

Azure AD podporuje následující metody ověřování: 

* **Cloudové ověřování** – Pokud zvolíte tuto metodu ověřování Azure AD zpracovává proces ověřování pro uživatele přihlášení. Pomocí cloudového ověřování můžete vybrat ze dvou možností: 
   * **Synchronizace hodnot hash hesel (PHS)** – umožňuje uživatelům používat stejné uživatelské jméno a heslo, které používají místní synchronizace hodnot Hash hesel bez nutnosti nasazovat žádnou další infrastrukturu kromě služby Azure AD Connect. 
   * **Předávací ověřování (PTA)** – tato možnost je podobný synchronizace hodnot hash hesel, ale poskytuje jednoduché heslo ověřování pomocí místní software agenty pro organizace s silné zásady zabezpečení a dodržování předpisů.
* **Federované ověřování** – Pokud vyberte tuto metodu ověřování Azure AD se přebírají proces ověření pro důvěryhodného oddělený ověřovací systém, například služby AD FS nebo systém federování třetích stran, k ověření uživatele je přihlášení. 

Pro většinu organizací, které chcete povolit přihlášení uživatele k Office 365, aplikacím SaaS a dalších založené na AD prostředků Azure doporučujeme, abyste výchozí možnost synchronizace hodnoty hash hesla.
 
Podrobné informace o volbě metody ověřování najdete v tématu [zvolte správné ověřování pro vaše řešení hybridní identity Azure Active Directory](../../security/azure-ad-choose-authn.md)

### <a name="password-hash-synchronization"></a>Synchronizace hodnot hash hesel
Pomocí synchronizace hodnot hash hesel hodnoty hash hesel uživatelů synchronizované z místní služby Active Directory do služby Azure AD. Když hesla se změněným nebo resetovaným lokálně, nové hodnoty hash hesel se synchronizují do služby Azure AD okamžitě tak, aby vaši uživatelé mohou vždy použít stejné heslo pro cloudové prostředky a místních prostředků. Hesla se nikdy odeslána do služby Azure AD nebo uložené ve službě Azure AD ve formátu prostého textu. Synchronizace hodnot hash hesel společně se zpětným zápisem hesla můžete povolit samoobslužné resetování hesla ve službě Azure AD.

Kromě toho můžete povolit [bezproblémového jednotného přihlašování](how-to-connect-sso.md) pro uživatele v doméně počítače, které jsou v podnikové síti. Pomocí jednotného přihlašování povolení uživatelé muset zadat uživatelské jméno jim umožní bezpečný přístup k prostředkům cloudu.

![Synchronizace hodnot hash hesel](./media/plan-connect-user-signin/passwordhash.png)

Další informace najdete v tématu [synchronizaci hodnot hash hesel](how-to-connect-password-hash-synchronization.md) článku.

### <a name="pass-through-authentication"></a>Předávací ověřování
Předávací ověřování který uživatelské heslo bude ověřeno místního kontroleru služby Active Directory. Heslo nemusí být k dispozici ve službě Azure AD v libovolné formě. To umožňuje místní zásady, jako je například omezení přihlášení hodinu, který se má vyhodnotit během ověřování do cloudu služby.

Předávací ověřování používá jednoduché agenta na počítači připojeném k doméně systému Windows Server 2012 R2 v místním prostředí. Tento agent naslouchá žádostí o ověření hesla. Nevyžaduje žádné příchozí porty otevřené Internetu.

Kromě toho můžete také povolit jednotné přihlašování pro uživatele v doméně počítače, které jsou v podnikové síti. Pomocí jednotného přihlašování povolení uživatelé muset zadat uživatelské jméno jim umožní bezpečný přístup k prostředkům cloudu.
![Předávací ověřování](./media/plan-connect-user-signin/pta.png)

Další informace naleznete v tématu:
- [Předávací ověřování](how-to-connect-pta.md)
- [Jednotné přihlašování](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federace, která používá nové nebo existující farmy se službou AD FS ve Windows serveru 2012 R2
Pomocí federovaného přihlašování můžou vaši uživatelé přihlašovat ke službám Azure na základě AD pomocí svých místních hesel. Když jsou v podnikové síti, ještě nemají k zadání hesla. Pomocí možnosti federační službou AD FS, můžete nasadit do nové nebo existující farmy se službou AD FS ve Windows serveru 2012 R2. Pokud se rozhodnete zadat existující farmy, Azure AD Connect nakonfiguruje vztah důvěryhodnosti mezi farmy a Azure AD tak, aby vaši uživatelé mohou přihlásit.

<center>![Federace se službou AD FS ve Windows serveru 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Nasazení federace se službou AD FS ve Windows serveru 2012 R2

Pokud nasazujete novou farmu, budete potřebovat:

* Server Windows Server 2012 R2 pro federační server.
* Server Windows Server 2012 R2 pro Proxy webových aplikací.
* Soubor .pfx s jedním certifikátem SSL pro název vaší zamýšlený federační služby. Příklad: fs.contoso.com.

Pokud nasazujete nové farmy nebo použití existující farmy, potřebujete:

* Přihlašovací údaje místního správce na federačních serverech.
* Přihlašovací údaje místního správce na žádném ze serverů pracovní skupiny (nikoli připojených k doméně), kterou chcete nasadit role Proxy webových aplikací na.
* Počítač, na kterém spustíte Průvodce na bude moct připojit k jiným počítačům, které chcete nainstalovat službu AD FS nebo Proxy webových aplikací pomocí vzdálené správy Windows.

Další informace najdete v tématu [Konfigurace jednotného přihlašování se službou AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federace s PingFederate
Pomocí federovaného přihlašování můžou vaši uživatelé přihlašovat ke službám Azure na základě AD pomocí svých místních hesel. Když jsou v podnikové síti, ještě nemají k zadání hesla.

Další informace o konfiguraci služby PingFederate pro použití se službou Azure Active Directory najdete v tématu [PingFederate integrace s Azure Active Directory a Office 365](https://www.pingidentity.com/AzureADConnect)

Informace o nastavení Azure AD Connect s použitím služby PingFederate, naleznete v tématu [vlastní instalace Azure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Přihlaste se pomocí starší verze služby AD FS nebo řešení třetí strany
Pokud jste si už nakonfigurovali cloudové přihlašování pomocí starší verze služby AD FS (jako je například služba AD FS 2.0) nebo poskytovatel federace třetích stran, můžete přeskočit konfigurace přihlašování uživatele prostřednictvím služby Azure AD Connect. To vám umožní získat nejnovější synchronizace a další funkce služby Azure AD Connect stále přitom vaše stávající řešení pro přihlášení.

Další informace najdete v tématu [seznam kompatibility federace třetí strany Azure AD](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Přihlášení uživatele a hlavní název uživatele
### <a name="understanding-user-principal-name"></a>Principy hlavní název uživatele
Výchozí příponou hlavního názvu (UPN) uživatele ve službě Active Directory, je název DNS domény, kde byl vytvořen uživatelský účet. Ve většině případů toto je název domény, který je registrovaný jako podnikové doméně v síti Internet. Můžete však přidat další přípony UPN s využitím domény služby Active Directory a vztahy důvěryhodnosti.

Hlavní název uživatele uživatele má formát username@domain. Pro doménu Active Directory s názvem "contoso.com", například uživateli jménem Jan může mít hlavní název uživatele "john@contoso.com". Uživatel (UPN) podle RFC 822. Přestože hlavní název uživatele a e-mailu sdílejí stejný formát, hodnotu hlavního názvu uživatele pro uživatele může nebo nemusí být stejné jako e-mailovou adresu uživatele.

### <a name="user-principal-name-in-azure-ad"></a>Hlavní název uživatele ve službě Azure AD
Průvodce Azure AD Connect používá atribut userPrincipalName, nebo vám umožní určit atribut (ve vlastní instalaci) pro použití v místním jako hlavní název uživatele ve službě Azure AD. Jedná se o hodnotu, která se používá pro přihlášení k Azure AD. Pokud hodnota atributu userPrincipalName neodpovídá ověřené domény ve službě Azure AD, Azure AD nahradí ho s výchozím. onmicrosoft.com hodnotu.

Každý adresář služby Azure Active Directory se dodává s názvem integrované domény, formátu contoso.onmicrosoft.com, díky tomu můžete začít používat Azure nebo jiné služby Microsoftu. Můžete zlepšit a zjednodušit přihlašovací prostředí s použitím vlastních domén. Informace o vlastních názvů domén ve službě Azure AD a jak ověření domény najdete v tématu [přidání vlastního názvu domény do Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Konfigurace přihlášení k Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Konfigurace Azure AD přihlášení s využitím Azure AD Connect
Přihlašování Azure AD závisí na Azure AD, jestli odpovídá příponou hlavního názvu uživatele, který synchronizuje do jedné z vlastních domén, které jsou v adresáři Azure AD ověřit uživatele. Azure AD Connect poskytuje pomoc při konfiguraci Azure AD přihlášení nastavení tak, aby uživatel přihlašovací prostředí v cloudu je podobně jako v místním prostředí.

Azure AD Connect najdete přípony UPN, které jsou definovány pro domény a pokusí se porovnat je s vlastní domény ve službě Azure AD. Pak se vám pomůžou s příslušnou akci, kterou je potřeba provést.
Na přihlašovací stránku Azure AD najdete přípony UPN, které jsou definovány pro místní služby Active Directory a zobrazí odpovídající stav pro každou příponu. Hodnoty stavu může být jedna z následujících akcí:

| Stav | Popis | Vyžaduje se akce |
|:--- |:--- |:--- |
| Ověřeno |Azure AD Connect nalezena že odpovídající ověřené domény ve službě Azure AD. Všechny uživatele pro tuto doménu můžete přihlásit pomocí svých místních přihlašovacích údajů. |Není vyžadována žádná akce. |
| Neověřeno |Azure AD Connect nalezena odpovídající vlastní domény ve službě Azure AD, ale není ověřený. Přípona UPN uživatelů tato doména se změní na výchozí hodnotu. přípona onmicrosoft.com po synchronizaci, pokud doména není ověřený. | [Ověření vlastní domény ve službě Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Nepřidáno |Azure AD Connect se nepovedlo najít vlastní doménu, která odpovídaly přípona UPN. Přípona UPN uživatelů tato doména se změní na výchozí hodnotu. přípona onmicrosoft.com, pokud doména není přidat a ověřit v Azure. | [Přidání a ověření vlastní domény, který odpovídá přípona UPN.](../fundamentals/add-custom-domain.md) |

Na přihlašovací stránku Azure AD najdete přípony UPN, které jsou definovány pro místní služby Active Directory a odpovídající vlastní domény ve službě Azure AD zobrazí aktuální stav ověření. Vlastní instalace, teď můžete vybrat atribut pro zadaný hlavní název uživatele na **přihlášení Azure AD** stránky.

![Azure AD přihlašovací stránky](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Můžete kliknout na tlačítko Aktualizovat se znovu načíst nejnovější stav vlastních domén ze služby Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Výběr atributů pro zadaný hlavní název uživatele ve službě Azure AD
Atribut userPrincipalName je atribut, který uživatelé používají při přihlášení k Azure AD a Office 365. Měli byste ověřit domén (označované také jako přípony UPN), které se používají ve službě Azure AD před synchronizací uživatelů.

Důrazně doporučujeme ponechat výchozí atribut userPrincipalName. Pokud tento atribut je nepoužívající a nedá se ověřit, je možné vybrat jiný atribut (například e-mail) jako atribut, který obsahuje ID přihlášení. To se označuje jako alternativní ID. Hodnota atributu alternativní ID musí postupovat podle standardu RFC 822. Alternativní ID můžete použít s heslem jednotného přihlašování a federace jednotného přihlašování jako řešení přihlašování.

> [!NOTE]
> Použití atributu alternativní ID není kompatibilní se všemi úlohami Office 365. Další informace najdete v tématu [konfigurace alternativního přihlašovacího ID](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Stavy jiné vlastní domény a jejich vliv na prostředí Azure přihlášení
Je velmi důležité porozumět vztahu mezi stavy vlastní doménu v adresáři služby Azure AD a přípony UPN, které jsou definovány v místním. Podívejme se různé možné Azure přihlašovací prostředí při při nastavování synchronizace pomocí služby Azure AD Connect.

Následující informace, Předpokládejme, že jsme se contoso.com přípona UPN, který je používán v místním adresáři jako součást UPN – například user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Express synchronizaci hodnot hash hesel se/nastavení
| Stav | Dopad na činnost koncového uživatele přihlášení do Azure |
|:---:|:--- |
| Nepřidáno |V takovém případě žádné vlastní domény pro doménu contoso.com se přidala v adresáři Azure AD. Uživatelé, kteří mají UPN místní s příponou @contoso.com nebudou moci používat své místní hlavní název uživatele pro přihlášení k Azure. Místo toho budete muset použít nový název UPN, která je k dispozici k nim pomocí Azure AD tak, že přidáte příponu pro výchozí adresář Azure AD. Například, pokud synchronizujete uživatelům azurecontoso.onmicrosoft.com adresář Azure AD a místní uživatel user@contoso.com dostanou název UPN user@azurecontoso.onmicrosoft.com. |
| Neověřeno |V takovém případě budeme mít vlastní doménu contoso.com, který je přidán v adresáři Azure AD. Ale je ještě není ověřen. Pokud vám pokračujte se synchronizací uživatelům bez ověření domény, pak uživatelé se přiřadí nový název UPN službou Azure AD, stejně jako ve scénáři s "Nebyl přidán". |
| Ověřeno |V takovém případě budeme mít vlastní doménu contoso.com, které již přidat a ověřit ve službě Azure AD pro tuto příponu UPN. Uživatelé budou moci používat své místní hlavní název uživatele, například user@contoso.com, k přihlášení do Azure se synchronizují do Azure AD. |

###### <a name="ad-fs-federation"></a>Služby ADFS
Nelze vytvořit federaci s výchozím. doméně onmicrosoft.com v Azure AD nebo neověřené vlastní domény ve službě Azure AD. Pokud vyberete neověřenou doménu pro vytvoření federace s pomocí Průvodce Azure AD Connect, Azure AD Connect vyzve vás s nezbytné záznamy, které chcete vytvořit, kde se hostuje váš server DNS pro doménu. Další informace najdete v tématu [ověření domény Azure AD vybrané k federaci](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Pokud jste vybrali možnost uživatelů přihlásit **federace se službou AD FS**, pak musí mít vlastní doménu chcete pokračovat ve vytváření federace ve službě Azure AD. Diskuse to znamená, že jsme by měl mít vlastní doménu contoso.com přidán v adresáři Azure AD.

| Stav | Dopad na uživatele Azure přihlašovací prostředí |
|:---:|:--- |
| Nepřidáno |V tomto případě Azure AD Connect nepovedlo najít odpovídající vlastní domény pro doménu contoso.com přípona UPN v adresáři Azure AD. Budete muset přidat vlastní doménu contoso.com, pokud potřebujete uživatelům umožní přihlásit se pomocí služby AD FS s jejich místní hlavní název uživatele (například user@contoso.com). |
| Neověřeno |Azure AD Connect v tomto případě vyzve příslušné podrobnosti o tom, jak můžete ověřit vaši doménu v pozdější fázi. |
| Ověřeno |V takovém případě můžete přejít k tématu s konfigurací bez jakékoli další akce. |

## <a name="changing-the-user-sign-in-method"></a>Změna metody přihlašování uživatele
Pomocí úloh, které jsou k dispozici ve službě Azure AD Connect po počáteční konfiguraci služby Azure AD Connect pomocí průvodce můžete změnit metodu přihlašování uživatele z federace, synchronizaci hodnot hash hesel nebo předávací ověřování. Znovu spusťte Průvodce Azure AD Connect a zobrazí se seznam úloh, které můžete provádět. Vyberte **změnit přihlášení uživatele** ze seznamu úkolů.

![Změna přihlašovacích údajů uživatele](./media/plan-connect-user-signin/changeusersignin.png)

Na další stránce budete vyzváni k zadání přihlašovacích údajů Azure AD.

![Připojení k Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Na **přihlášení uživatele** vyberte přihlášení požadovaného uživatele.

![Připojení k Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Pokud děláte jenom dočasné přepínač na synchronizaci hodnot hash hesel, vyberte **nepřevádět uživatelské účty** zaškrtávací políčko. Nekontrolují se možnost převede každému uživateli federovaných a může trvat několik hodin.
>
>

## <a name="next-steps"></a>Další postup
- Další informace o [integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).
- Další informace o [koncepty návrhu Azure AD Connect](plan-connect-design-concepts.md).
