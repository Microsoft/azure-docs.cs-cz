---
title: 'Azure AD Connect: Přihlášení uživatele | Microsoft Docs'
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
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 6a6e83ad73f561cd8aa4fc629fb9b48449af6d0a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect uživatelské možnosti přihlášení
Připojení služby Azure Active Directory (Azure AD) umožňuje uživatelům přihlásit do cloudu a místní prostředky pomocí stejnými hesly. Tento článek popisuje klíčové koncepty pro každý model identity, které vám pomohou zvolit identity, která chcete použít pro přihlášení ke službě Azure AD.

Pokud jste již obeznámeni s modelem identity Azure AD a chcete získat další informace o konkrétní metody, podívejte se na příslušný odkaz:

* [Synchronizaci hodnoty hash hesla](#password-hash-synchronization) s [bezproblémové jednotné přihlašování (SSO)](active-directory-aadconnect-sso.md)
* [Předávací ověřování](active-directory-aadconnect-pass-through-authentication.md) s [bezproblémové jednotné přihlašování (SSO)](active-directory-aadconnect-sso.md)
* [Federované jednotné přihlašování (službou Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)

> [!NOTE] 
> Je důležité si pamatovat, že konfigurace federace pro Azure AD, je vytvořit vztah důvěryhodnosti mezi vašeho klienta Azure AD a federované domény. S touto doménou federovaného vztahu důvěryhodnosti budou mít uživatelé přístup k prostředkům cloudu Azure AD v rámci klienta.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Výběr uživatele přihlašovací metoda pro vaši organizaci
Pro většinu organizací, které právě chcete povolit přihlášení uživatele k Office 365, aplikace SaaS a jiné na základě AD prostředky Azure doporučujeme, abyste výchozí možnost synchronizace hodnoty hash hesla. Některé organizace, ale mají konkrétní důvod nejsou možné použít tuto možnost. Můžete se buď federované možnost přihlášení, například služby AD FS nebo předávací ověřování. Následující tabulka vám pomůže vám pomůže zajistit správnou volbou.

Je nutné | PBS pomocí jednotného přihlašování| PTA pomocí jednotného přihlašování| AD FS |
 --- | --- | --- | --- |
Automaticky synchronizujte nové, obraťte se na, účty uživatelů a skupin ve službě Active Directory v místě do cloudu.|w|w|w|
Nastavení klienta pro Office 365 hybridní scénáře.|w|w|w|
Povolte vlastní uživatelé přihlásit a získat přístup ke službám cloudu pomocí hesla pro místní.|w|w|w|
Implementaci jednotného přihlašování pomocí podnikové přihlašovací údaje.|w|w|w|
Ujistěte se, že se žádná hesla ukládat v cloudu.||x*|w|
Povolte místní služby Multi-Factor authentication řešení.|||w|

* Prostřednictvím lightweight agenta.

### <a name="password-hash-synchronization"></a>Synchronizace hodnot hash hesel
S synchronizaci hodnoty hash hesla se synchronizují hodnot hash hesel uživatelů z místní služby Active Directory do Azure AD. Když hesla se změněným nebo resetovaným místní, nové hodnoty hash hesla jsou synchronizovány do Azure AD okamžitě tak, aby vaši uživatelé mohou vždy použít stejné heslo pro cloudové a místní prostředků. Hesla se nikdy odeslat do služby Azure AD nebo uložené ve službě Azure AD ve formátu prostého textu. Synchronizaci hodnoty hash hesla společně s zpětný zápis hesla můžete povolit samoobslužné služby ve službě Azure AD pro vytvoření nového hesla.

Kromě toho můžete povolit [bezproblémové SSO](active-directory-aadconnect-sso.md) pro uživatele v doméně počítače, které jsou v podnikové síti. Pomocí jednotného přihlašování povolení uživatelé muset zadat uživatelské jméno, aby jim pomohl bezpečný přístup k prostředkům cloudu.

![Synchronizace hodnot hash hesel](./media/active-directory-aadconnect-user-signin/passwordhash.png)

Další informace najdete v tématu [synchronizaci hodnoty hash hesla](active-directory-aadconnectsync-implement-password-hash-synchronization.md) článku.

### <a name="pass-through-authentication"></a>Předávací ověřování
S předávací ověřování za který uživatelské heslo bude ověřeno řadič místní služby Active Directory. Heslo nemusí být k dispozici ve službě Azure Active Directory žádný formulář. To umožňuje místní zásady, třeba hodinu přihlašovací omezení, který se má vyhodnotit během ověřování do cloudu služeb.

Předávací ověřování používá jednoduchý agenta na počítači připojeném k doméně systému Windows Server 2012 R2 v místním prostředí. Tento agent naslouchá požadavkům ověřování hesla. Nevyžaduje žádné příchozí porty otevřené k Internetu.

Kromě toho můžete také povolit jednotné přihlašování pro uživatele v doméně počítače, které jsou v podnikové síti. Pomocí jednotného přihlašování povolení uživatelé muset zadat uživatelské jméno, aby jim pomohl bezpečný přístup k prostředkům cloudu.
![Předávací ověřování](./media/active-directory-aadconnect-user-signin/pta.png)

Další informace naleznete v tématu:
- [Předávací ověřování](active-directory-aadconnect-pass-through-authentication.md)
- [Jednotné přihlašování](active-directory-aadconnect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federace, která používá nový nebo existující farmy se službou AD FS ve Windows serveru 2012 R2
Federované přihlášení můžete uživatelům přihlásit ke službám Azure na základě AD s místními hesla. Když jsou v podnikové síti, i nemají k zadání hesla. Pomocí možnosti federační službou AD FS můžete nasadit nové nebo existující farmy se službou AD FS ve Windows serveru 2012 R2. Pokud zvolíte možnost zadejte existující farmy, Azure AD Connect nakonfiguruje vztah důvěryhodnosti mezi farmy a Azure AD, tak, aby vaši uživatelé můžete přihlásit.

<center>![Federace se službou AD FS ve Windows serveru 2012 R2](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Nasazení federační službou AD FS ve Windows serveru 2012 R2

Pokud nasazujete novou farmu, je třeba:

* Server Windows Server 2012 R2 pro federační server.
* Server Windows Server 2012 R2 pro Proxy webových aplikací.
* Soubor .pfx pro název vaší služby federačního určený s jedním certifikátem SSL. Příklad: fs.contoso.com.

Pokud provádíte nasazení nové farmy nebo použití existující farmy, je třeba:

* Přihlašovací údaje místního správce na federačních serverech.
* Přihlašovací údaje místního správce na žádném ze serverů pracovní skupiny (není připojený k doméně), kterou chcete nasadit role Proxy webových aplikací na.
* Na počítač, který jste spustili Průvodce být schopni připojit k jiné počítače, které chcete nainstalovat službu AD FS nebo Proxy webových aplikací na pomocí vzdálené správy systému Windows.

Další informace najdete v tématu [Konfigurace jednotného přihlašování se službou AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Přihlaste se pomocí dřívější verze služby AD FS nebo řešení třetí strany
Pokud jste již nakonfigurovali cloudu přihlásit pomocí starší verze služby AD FS (například služby AD FS 2.0) nebo zprostředkovatele federování třetích stran, můžete přeskočit konfigurace přihlášení uživatele přes Azure AD Connect. To vám umožní získat nejnovější synchronizace a dalším funkcím služby Azure AD Connect při stále pomocí stávajícího řešení pro přihlášení.

Další informace najdete v tématu [seznam kompatibility federace třetí strany služby Azure AD](active-directory-aadconnect-federation-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Přihlášení uživatele a hlavní název uživatele
### <a name="understanding-user-principal-name"></a>Principy hlavní název uživatele
Ve službě Active Directory je příponou hlavního názvu (UPN) uživatele výchozí název DNS domény, kde byl vytvořený uživatelský účet. Ve většině případů to je název domény, který je registrovaný jako podnikové doméně v síti Internet. Můžete však přidat další přípony UPN s použitím domény služby Active Directory a vztahy důvěryhodnosti.

Hlavní název uživatele uživatele má formát username@domain. Pro doménu služby Active Directory s názvem "contoso.com", například uživatel s názvem Jan může mít hlavní název uživatele "john@contoso.com". Hlavní název uživatele uživatele je založena na RFC 822. I když UPN a e-mailu sdílet stejný formát, hodnota hlavní název uživatele pro uživatele může nebo nemusí být stejný jako e-mailovou adresu uživatele.

### <a name="user-principal-name-in-azure-ad"></a>Hlavní název uživatele ve službě Azure AD
Průvodce Azure AD Connect používá atribut userPrincipalName, nebo můžete zadat atribut (ve vlastní instalaci) pro použití v místní jako hlavní název uživatele ve službě Azure AD. Toto je hodnota, která se používá pro přihlášení ke službě Azure AD. Pokud hodnota atributu userPrincipalName neodpovídá ověřenou doménu ve službě Azure AD, pak Azure AD ho nahradí výchozí. onmicrosoft.com hodnotu.

Každý adresář v Azure Active Directory se dodává s názvem předdefinované domény, formát contoso.onmicrosoft.com, že umožňuje můžete začít používat Azure nebo jiných službách Microsoftu. Můžete zlepšit a zjednodušení přihlašování pomocí vlastních domén. Informace o vlastních názvů domén v Azure AD a postup ověření domény najdete v tématu [přidání vlastního názvu domény do Azure Active Directory](../add-custom-domain.md#add-the-custom-domain-name-to-your-directory).

## <a name="azure-ad-sign-in-configuration"></a>Konfigurace přihlášení k Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD přihlášení konfigurace službou Azure AD Connect
Přihlašování Azure AD závisí na tom, jestli může Azure AD shodovat s příponou hlavního názvu uživatele uživatele, který je právě synchronizované s jedním z vlastní domény, která jsou ověřena v adresáři služby Azure AD. Azure AD Connect poskytuje pomoc při konfiguraci nastavení služby Azure AD přihlášení, tak, aby uživatel přihlašování uživatelů v cloudu je podobná místní prostředí.

Azure AD Connect obsahuje seznam přípon UPN, které jsou definovány pro domény a pokusí se porovnat je s vlastní domény ve službě Azure AD. Potom pomáhá s příslušnou akci, která je potřeba provést.
Na přihlašovací stránku služby Azure AD seznam přípon UPN, které jsou definovány pro místní služby Active Directory a zobrazí odpovídající stav pro každou příponu. Hodnoty stavu může být jedna z následujících akcí:

| Okres | Popis | Akce, které jsou potřeba |
|:--- |:--- |:--- |
| Ověřeno |Azure AD Connect nalezena že odpovídající ověření domény ve službě Azure AD. Všechny uživatele pro tuto doménu můžete přihlásit pomocí jejich místních přihlašovacích údajů. |Není vyžadována žádná akce. |
| Nebyla ověřena. |Azure AD Connect v Azure AD najít odpovídající vlastní doménu, ale není ověřen. Přípona UPN uživatelé tuto doménu se změní na výchozí hodnoty. přípona onmicrosoft.com po synchronizaci, pokud není doméně ověřit. | [Ověření vlastní domény ve službě Azure AD.](../add-custom-domain.md#verify-the-custom-domain-name-in-azure-ad) |
| Nebyla přidána |Azure AD Connect nenašli vlastní doménu, která odpovídaly příponu UPN. Přípona UPN uživatelé tuto doménu se změní na výchozí hodnoty. přípona onmicrosoft.com, pokud doména není přidat a ověřit v Azure. | [Přidání a ověření vlastní domény, která odpovídá příponu UPN.](../add-custom-domain.md) |

Azure AD přihlašovací stránka obsahuje seznam přípon UPN, které jsou definovány pro místní služby Active Directory a odpovídající vlastní domény ve službě Azure AD s aktuální stav ověření. Ve vlastní instalaci, můžete nyní vybrat atribut pro hlavní název uživatele na **přihlášení k Azure AD** stránky.

![Azure AD přihlašovací stránky](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Kliknutím na tlačítko Aktualizovat a znovu načtěte nejnovější stav vlastní domény z Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Výběr atribut pro hlavní název uživatele ve službě Azure AD
Atribut userPrincipalName je atribut, který uživatelé používají při přihlášení k Azure AD a Office 365. Měli byste ověřit domén (označované také jako přípony UPN), které se používají ve službě Azure AD před synchronizací uživatelů.

Důrazně doporučujeme ponechat výchozí atribut userPrincipalName. Pokud tento atribut je nepoužívající a nelze ověřit, je možné vybrat jiný atribut (například e-mail) jako atribut, který obsahuje přihlášení. To se označuje jako alternativní ID. Hodnota atributu alternativní ID musí následovat standard RFC 822. Můžete vytvořit alternativní ID se heslo jednotné přihlašování i jako řešení přihlašování federaci jednotné přihlašování.

> [!NOTE]
> Použití atributu alternativní ID není kompatibilní se všemi úlohami Office 365. Další informace najdete v tématu [konfigurace alternativního přihlašovacího ID](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Stavy jiné vlastní domény a jejich vliv na Azure přihlašování uživatelů
Je velmi důležité, abyste pochopili vztah mezi stavy vlastní doménu v adresáři služby Azure AD a přípon UPN, které jsou definované v místě. Přejděte prostřednictvím různé možné Azure přihlášení při nastavení synchronizace pomocí Azure AD Connect.

Následující informace Předpokládejme, že nám nevadí contoso.com přípona UPN, které se používá v místním adresáři jako součást UPN – například user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Expresní nastavení a hesla synchronizaci hodnoty hash
| Okres | Vliv na činnost koncového uživatele Azure přihlášení |
|:---:|:--- |
| Nebyla přidána |V takovém případě žádné vlastní domény pro doménu contoso.com přidala v adresáři služby Azure AD. Uživatelé, kteří mají UPN místně s příponou @contoso.com nebudou moci používat své místní UPN pro přihlášení k Azure. Místo toho budete muset použít nového názvu UPN, které se službou Azure AD přidáním přípona výchozí adresář Azure AD poskytuje k nim. Například, pokud se synchronizuje uživatelům azurecontoso.onmicrosoft.com adresář Azure AD a místní uživatel user@contoso.com bude mít název UPN user@azurecontoso.onmicrosoft.com. |
| Nebyla ověřena. |V takovém případě máme vlastní domény contoso.com, který je přidán v adresáři služby Azure AD. Nicméně je ještě nebyl ověřen. Pokud jste pokračujte s synchronizaci uživatelů bez ověření domény uživatele bude přiřazen nový hlavní název uživatele Azure AD, stejně jako ve scénáři "Nepřidáno". |
| Ověřeno |V takovém případě máme vlastní domény contoso.com, které již přidat a ověřit ve službě Azure AD pro tuto příponu UPN. Uživatelé budou moci používat své místní hlavní název uživatele, například user@contoso.com, pro přihlášení k Azure po se synchronizují do Azure AD. |

###### <a name="ad-fs-federation"></a>Federaci služby AD FS
Nelze vytvořit federaci s výchozím. doméně onmicrosoft.com v Azure AD nebo neověřené vlastní domény ve službě Azure AD. Pokud používáte Průvodce službou Azure AD Connect, pokud zvolíte možnost vytvořit federaci s neověřenou doménu, Azure AD Connect vyzve vás s nezbytné záznamy, které chcete vytvořit, je hostitelem serveru DNS pro doménu. Další informace najdete v tématu [Ověřte vybrané pro federaci Azure AD domény](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Pokud jste vybrali možnost přihlášení uživatele **federační službou AD FS**, pak musí mít vlastní doménu chcete pokračovat ve vytváření federace ve službě Azure AD. Naše diskusi to znamená, že jsme by měl mít vlastní domény contoso.com, přidali v adresáři služby Azure AD.

| Okres | Vliv na uživatele Azure přihlašování uživatelů |
|:---:|:--- |
| Nebyla přidána |V takovém případě Azure AD Connect nebyl nalezen odpovídající vlastní domény pro doménu contoso.com příponu UPN v adresáři služby Azure AD. Je nutné přidat vlastní doménu contoso.com, pokud potřebujete uživatelům přihlášení pomocí služby AD FS s jejich místní hlavní název uživatele (například user@contoso.com). |
| Nebyla ověřena. |Azure AD Connect v takovém případě vyzve příslušné podrobnosti o tom, jak můžete ověřit doménu v pozdější fázi. |
| Ověřeno |V takovém případě můžete přejít k tématu s konfigurací bez jakékoli další akce. |

## <a name="changing-the-user-sign-in-method"></a>Změna metody přihlášení uživatele
Metoda přihlašování uživatelů můžete změnit z federačního, synchronizaci hodnoty hash hesla nebo předávací ověřování pomocí úlohy, které jsou k dispozici ve službě Azure AD Connect po počáteční konfiguraci služby Azure AD Connect pomocí průvodce. Znovu spusťte Průvodce službou Azure AD Connect a zobrazí se seznam úloh, které můžete provádět. Vyberte **změnit přihlášení uživatele** ze seznamu úloh.

![Změna přihlašovacích údajů uživatele](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Na další stránce budete vyzváni k zadání pověření pro Azure AD.

![Připojení k Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Na **přihlášení uživatele** vyberte přihlášení požadovaného uživatele.

![Připojení k Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Pokud chcete vytvořit pouze dočasné přepínače k synchronizaci hodnoty hash hesla, pak vyberte **nepřevádějí uživatelské účty** zaškrtávací políčko. Není možnost Kontrola bude každý uživatel federovaný převést, a může trvat několik hodin.
>
>

## <a name="next-steps"></a>Další postup
- Další informace o [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).
- Další informace o [koncepty návrhu Azure AD Connect](active-directory-aadconnect-design-concepts.md).
