---
title: 'Azure AD Connect: Přihlášení uživatele | Dokumenty společnosti Microsoft'
description: Přihlášení uživatele služby Azure AD Connect pro vlastní nastavení.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a08120b98c7a08bca50453df59df313b1645c5c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331267"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Možnosti přihlášení uživatele Azure AD Connect
Azure Active Directory (Azure AD) Connect umožňuje uživatelům přihlásit se ke cloudovým i místním prostředkům pomocí stejných hesel. Tento článek popisuje klíčové koncepty pro každý model identity, které vám pomohou vybrat identitu, kterou chcete použít pro přihlášení do Azure AD.

Pokud už jste obeznámeni s modelem identity Azure AD a chcete se dozvědět více o konkrétní metodě, podívejte se na příslušný odkaz:

* [Synchronizace hash hesla](#password-hash-synchronization) s [bezproblémovým jedním přihlašování (SSO)](how-to-connect-sso.md)
* [Předávací ověřování](how-to-connect-pta.md) s [bezproblémovým jednotného přihlašování (SSO)](how-to-connect-sso.md)
* [Federované služby SSO (se službou Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federace s PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Je důležité si uvědomit, že konfigurací federace pro Azure AD navážete vztah důvěryhodnosti mezi klientem Azure AD a federovanými doménami. S tímto vztahem důvěryhodnosti federované domény uživatelé budou mít přístup ke cloudovým prostředkům Azure AD v rámci klienta.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Výběr metody přihlášení uživatele pro vaši organizaci
Prvním rozhodnutím o implementaci Služby Azure AD Connect je výběr metody ověřování, kterou budou uživatelé používat k přihlášení. Je důležité, abyste zvolili správnou metodu, která splňuje požadavky vaší organizace na zabezpečení a pokročilé požadavky. Ověřování je důležité, protože ověří identity uživatele pro přístup k aplikacím a datům v cloudu. Chcete-li zvolit správnou metodu ověřování, je třeba zvážit čas, existující infrastrukturu, složitost a náklady na implementaci vaší volby. Tyto faktory se liší pro každou organizaci a mohou se v průběhu času měnit.

Azure AD podporuje následující metody ověřování: 

* **Cloud authentication** – když zvolíte tuto metodu ověřování, Azure AD zpracovává proces ověřování pro přihlášení uživatele. S cloudovým ověřováním si můžete vybrat ze dvou možností: 
   * **Synchronizace hash hesla (PHS)** – Synchronizace hash hesla umožňuje uživatelům používat stejné uživatelské jméno a heslo, které používají místně, aniž by museli nasazovat další infrastrukturu kromě Azure AD Connect. 
   * **Předávací ověřování (PTA)** – Tato možnost je podobná synchronizaci hodnoty hash hesla, ale poskytuje jednoduché ověření hesla pomocí místních softwarových agentů pro organizace se silnými zásadami zabezpečení a dodržování předpisů.
* **Federované ověřování** – Když zvolíte tuto metodu ověřování, Azure AD předá proces ověřování samostatnému důvěryhodnému ověřovacímu systému, jako je například služba AD FS nebo federační systém třetí strany, aby ověřil přihlášení uživatele. 

Pro většinu organizací, které chtějí povolit přihlášení uživatele k Office 365, SaaS aplikacím a dalším prostředkům založeným na Azure AD, doporučujeme výchozí možnost synchronizace hodnot hash hesel.
 
Podrobné informace o výběru metody ověřování najdete v [tématu Výběr správné metody ověřování pro řešení hybridní identity služby Azure Active Directory.](../../security/fundamentals/choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Synchronizace hodnot hash hesel
Při synchronizaci hodnot hash hesel se hashy uživatelských hesel synchronizují z místní služby Active Directory do služby Azure AD. Při změně hesla nebo obnovení místní, nové heslo hashy jsou synchronizovány do Služby Azure AD okamžitě tak, aby vaši uživatelé mohou vždy použít stejné heslo pro cloudové prostředky a místní prostředky. Hesla se nikdy neodesílají do Azure AD nebo se ukládají ve službě Azure AD ve prostém textu. Synchronizace hash hesla spolu s zpětným zápisem hesla můžete povolit samoobslužné resetování hesla ve službě Azure AD.

Kromě toho můžete povolit [bezproblémové jednotné přihlašovací služby](how-to-connect-sso.md) pro uživatele na počítačích spojených s doménou, které jsou v podnikové síti. S jedním přihlášením, povolené uživatelé stačí zadat uživatelské jméno, které jim pomohou bezpečně přistupovat ke cloudovým prostředkům.

![Synchronizace hodnot hash hesel](./media/plan-connect-user-signin/passwordhash.png)

Další informace naleznete v článku [synchronizace hodnot hash hesel.](how-to-connect-password-hash-synchronization.md)

### <a name="pass-through-authentication"></a>Předávací ověřování
Při předávacím ověřování je heslo uživatele ověřeno podle místního řadiče služby Active Directory. Heslo nemusí být k dispozici ve službě Azure AD v žádné formě. To umožňuje místní zásady, jako je omezení hodin přihlášení, které mají být vyhodnoceny během ověřování na cloudové služby.

Předávací ověřování používá jednoduchého agenta v počítači se serverem Windows Server 2012 R2, který je připojen k doméně v místním prostředí. Tento agent naslouchá požadavkům na ověření hesla. Nevyžaduje žádné příchozí porty, které mají být otevřené pro Internet.

Kromě toho můžete také povolit jednotné přihlašování pro uživatele v počítačích spojených s doménou, které jsou v podnikové síti. S jedním přihlášením, povolené uživatelé stačí zadat uživatelské jméno, které jim pomohou bezpečně přistupovat ke cloudovým prostředkům.
![Předávací ověřování](./media/plan-connect-user-signin/pta.png)

Další informace naleznete v tématu:
- [Předávací ověřování](how-to-connect-pta.md)
- [Jednotné přihlašování](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federace, která používá novou nebo existující farmu se službou AD FS v systému Windows Server 2012 R2
Díky federovanému přihlášení se uživatelé můžou přihlásit ke službám založeným na Azure AD pomocí svých místních hesel. Když jsou v podnikové síti, ani nemusí zadávat svá hesla. Pomocí možnosti federace se službou AD FS můžete nasadit novou nebo existující farmu se službou AD FS v systému Windows Server 2012 R2. Pokud se rozhodnete zadat existující farmu, Azure AD Connect nakonfiguruje vztah důvěryhodnosti mezi vaší farmě a Azure AD tak, aby se uživatelé mohli přihlásit.

<center>

![Federace se službou AD FS v systému Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Nasazení federace se službou AD FS v systému Windows Server 2012 R2

Pokud nasazujete novou farmu, potřebujete:

* Server Windows Server 2012 R2 pro federační server.
* Server Windows Server 2012 R2 pro proxy webové aplikace.
* Soubor .pfx s jedním certifikátem TLS/SSL pro zamýšlený název federační služby. Například: fs.contoso.com.

Pokud nasazujete novou farmu nebo používáte existující farmu, potřebujete:

* Pověření místního správce na federačních serverech.
* Pověření místního správce na všech serverech pracovní skupiny (nikoli na doménu), na které chcete nasadit roli proxy webové aplikace.
* Počítač, na kterém průvodce spustíte, aby se mohl připojit k jiným počítačům, do kterých chcete nainstalovat službu AD FS nebo proxy webové aplikace pomocí vzdálené správy systému Windows.

Další informace naleznete [v tématu Konfigurace přisuzování s ad fs](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federace s PingFederate
Díky federovanému přihlášení se uživatelé můžou přihlásit ke službám založeným na Azure AD pomocí svých místních hesel. Když jsou v podnikové síti, ani nemusí zadávat svá hesla.

Další informace o konfiguraci pingfederate pro použití s Azure Active Directory najdete v [tématu PingFederate Integration with Azure Active Directory and Office 365](https://www.pingidentity.com/AzureADConnect)

Informace o nastavení Azure AD Connect pomocí PingFederate najdete v tématu [vlastní instalace Azure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Přihlaste se pomocí starší verze služby AD FS nebo řešení jiného výrobce
Pokud jste již nakonfigurovali přihlášení do cloudu pomocí starší verze služby AD FS (například Služby AD FS 2.0) nebo poskytovatele federace třetí strany, můžete přeskočit konfiguraci přihlášení uživatele prostřednictvím služby Azure AD Connect. To vám umožní získat nejnovější synchronizace a další funkce Azure AD Connect při stále pomocí stávajícího řešení pro přihlášení.

Další informace naleznete v [seznamu kompatibility azure ad federace třetích stran](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Přihlášení uživatele a hlavní jméno uživatele
### <a name="understanding-user-principal-name"></a>Principy hlavního názvu uživatele
Ve službě Active Directory je výchozí příponou hlavního uživatelského jména (UPN) název DNS domény, ve které byl uživatelský účet vytvořen. Ve většině případů se jedná o název domény, který je registrován jako podniková doména v Internetu. Pomocí domén a vztahů důvěryhodnosti služby Active Directory však můžete přidat další přípony hlavního názvu uživatele.

Hlavní název uživatele má formát username@domain. Například pro doménu služby Active Directory s názvem "contoso.com"john@contoso.commůže mít uživatel s názvem Jan hlavní název uživatele " ". Hlavní uživatel je založen na RFC 822. Přestože hlavní název uživatele a e-mail sdílejí stejný formát, hodnota hlavního název uživatele může nebo nemusí být stejná jako e-mailová adresa uživatele.

### <a name="user-principal-name-in-azure-ad"></a>Hlavní jméno uživatele ve službě Azure AD
Průvodce azure ad připojit používá userPrincipalName atribut nebo umožňuje zadat atribut (ve vlastní instalaci), který má být použit z místního jako hlavní název uživatele ve službě Azure AD. Toto je hodnota, která se používá pro přihlášení do Služby Azure AD. Pokud hodnota atributu userPrincipalName neodpovídá ověřené doméně ve službě Azure AD, azure ad nahradí ji výchozí hodnotou .onmicrosoft.com.

Každý adresář ve službě Azure Active Directory obsahuje předdefinovaný název domény s formátem contoso.onmicrosoft.com, který vám umožní začít používat Azure nebo jiné služby Microsoftu. Můžete zlepšit a zjednodušit přihlašovací prostředí pomocí vlastních domén. Informace o vlastních názvech domén ve službě Azure AD a o tom, jak ověřit doménu, najdete v tématu [Přidání vlastního názvu domény do služby Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Konfigurace přihlášení k Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Konfigurace přihlášení Azure AD pomocí Azure AD Connect
Prostředí přihlašování k Azure AD závisí na tom, jestli Azure AD může odpovídat příponě hlavního uživatelského jména uživatele, který se synchronizuje s jednou z vlastních domén, které jsou ověřeny v adresáři Azure AD. Azure AD Connect poskytuje pomoc při konfiguraci nastavení přihlášení Služby Azure AD tak, aby uživatelské prostředí pro přihlášení v cloudu je podobné místnímu prostředí.

Azure AD Connect uvádí přípony UPN, které jsou definované pro domény a pokusí se je porovnat s vlastní doménou ve službě Azure AD. Pak vám pomůže s vhodnými opatřeními, která je třeba přijmout.
Přihlašovací stránka Služby Azure AD obsahuje seznam přípon hlavního názvového práva, které jsou definovány pro místní službu Active Directory, a zobrazuje odpovídající stav oproti každé příponě. Hodnoty stavu mohou být jedny z následujících hodnot:

| Stav | Popis | Je nutná akce |
|:--- |:--- |:--- |
| Ověřené |Azure AD Connect našel odpovídající ověřenou doménu ve službě Azure AD. Všichni uživatelé pro tuto doménu se můžou přihlásit pomocí svých místních přihlašovacích údajů. |Není nutná žádná akce. |
| Neověřeno |Azure AD Connect našel odpovídající vlastní doménu ve službě Azure AD, ale není ověřena. Přípona hlavního názvu uživatele této domény bude po synchronizaci změněna na výchozí příponu onmicrosoft.com, pokud doména není ověřena. | [Ověřte vlastní doménu ve službě Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Nepřidáno |Azure AD Connect nenalezl vlastní doménu, která odpovídá příponě hlavního názvu uživatele. Přípona hlavního názvu uživatele této domény se změní na výchozí příponu .onmicrosoft.com, pokud doména není přidána a ověřena v Azure. | [Přidejte a ověřte vlastní doménu, která odpovídá příponě hlavního názvu uživatele.](../fundamentals/add-custom-domain.md) |

Přihlašovací stránka Služby Azure AD obsahuje seznam přípon HLAVNÍHO názvu uživatele, které jsou definované pro místní službu Active Directory a odpovídající vlastní doménu ve službě Azure AD s aktuálním stavem ověření. Ve vlastní instalaci teď můžete vybrat atribut pro hlavní název uživatele na přihlašovací stránce **Azure AD.**

![Přihlašovací stránka Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Kliknutím na tlačítko aktualizovat můžete znovu načíst nejnovější stav vlastních domén z Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Výběr atributu pro hlavní název uživatele ve službě Azure AD
Atribut userPrincipalName je atribut, který uživatelé používají při přihlášení k Azure AD a Office 365. Měli byste ověřit domény (označované také jako přípony UPN), které se používají ve službě Azure AD před synchronizací uživatelů.

Důrazně doporučujeme zachovat výchozí atribut userPrincipalName. Pokud je tento atribut nesměrovatelný a nelze jej ověřit, je možné vybrat jiný atribut (například e-mail) jako atribut, který obsahuje přihlašovací ID. To se označuje jako alternativní ID. Hodnota atributu Alternativní ID musí dodržovat standard RFC 822. Alternativní ID můžete použít s heslem s přihlašování a federace přihlašování jako řešení přihlášení.

> [!NOTE]
> Použití alternativního ID není kompatibilní se všemi úlohami Office 365. Další informace najdete v článku [Konfigurace alternativního přihlašovacího ID](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Různé stavy vlastní domény a jejich vliv na prostředí přihlašování k Azure
Je velmi důležité pochopit vztah mezi stavy vlastní domény v adresáři Azure AD a přípony HLAVNÍHO názvu uživatele, které jsou definovány místně. Pojďme si projít různé možné prostředí přihlášení do Azure při nastavování synchronizace pomocí Azure AD Connect.

Následující informace předpokládejme, že se zabýváme příponou hlavní telefonní hodcontoso.com, která se používá v místním adresáři user@contoso.comjako součást hlavního názvového práva -- například .

###### <a name="express-settingspassword-hash-synchronization"></a>Expresní nastavení/synchronizace hash hesla

| Stav | Vliv na uživatelské prostředí přihlašování k Azure |
|:---:|:--- |
| Nepřidáno |V takovém případě nebyla v adresáři Azure AD přidána žádná vlastní doména pro contoso.com. Uživatelé, kteří mají místní hlavní uživatele @contoso.com s příponou, nebudou moct k přihlášení do Azure používat svůj místní hlavní hlupávací den. Místo toho budou muset použít nový hlavní název uživatele, který jim poskytuje Azure AD přidáním přípony pro výchozí adresář Azure AD. Například pokud synchronizujete uživatele do adresáře Azure AD azurecontoso.onmicrosoft.com, user@contoso.com pak místní uživatel bude user@azurecontoso.onmicrosoft.commít UPN . |
| Neověřeno |V takovém případě máme vlastní doménu contoso.com, která je přidána v adresáři Azure AD. Nicméně, to ještě není ověřeno. Pokud budete pokračovat v synchronizaci uživatelů bez ověření domény, pak uživatelům bude přiřazen nový hlavní název uživatele azure ad, stejně jako ve scénáři "Nepřidáno". |
| Ověřené |V takovém případě máme vlastní doménovou contoso.com, která je už přidána a ověřena ve službě Azure AD pro příponu UPN. Uživatelé budou moct používat své místní hlavní user@contoso.comuživatelské jméno, například k přihlášení do Azure po synchronizaci s Azure AD. |

###### <a name="ad-fs-federation"></a>Federace AD FS
Ve službě Azure AD nelze vytvořit federaci s výchozí doménou .onmicrosoft.com ve službě Azure AD nebo neověřenou vlastní doménou ve službě Azure AD. Když spustíte průvodce Azure AD Connect, pokud vyberete neověřenou doménu, se kterou chcete vytvořit federaci, azure ad connect vás vyzve k vytvoření potřebných záznamů, kde je váš DNS hostovaný pro doménu. Další informace najdete [v tématu Ověření domény Azure AD vybrané pro federaci](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Pokud jste vybrali možnost přihlášení uživatele **Federace se službou AD FS**, pak musíte mít vlastní doménu, abyste mohli pokračovat ve vytváření federace ve službě Azure AD. Pro naši diskusi to znamená, že bychom měli mít vlastní doménu contoso.com přidána v adresáři Azure AD.

| Stav | Vliv na uživatelské prostředí přihlašování k Azure |
|:---:|:--- |
| Nepřidáno |V tomto případě Azure AD Connect nenašel odpovídající vlastní doménu pro příponu UPN contoso.com v adresáři Azure AD. Pokud potřebujete, aby se uživatelé přihlašovali pomocí služby AD FS s user@contoso.commístním upn (například), musíte přidat vlastní contoso.com domény. |
| Neověřeno |V takovém případě Azure AD Connect zobrazí výzvu s příslušnými podrobnostmi o tom, jak můžete ověřit svou doménu v pozdější fázi. |
| Ověřené |V takovém případě můžete pokračovat v konfiguraci bez jakékoli další akce. |

## <a name="changing-the-user-sign-in-method"></a>Změna metody přihlašování uživatelů
Metodu přihlášení uživatele můžete změnit z federace, synchronizace hodnot hash hesel nebo předávacího ověřování pomocí úloh, které jsou dostupné ve službě Azure AD Connect po počáteční konfiguraci služby Azure AD Connect s průvodcem. Spusťte průvodce Azure AD Connect znovu a zobrazí se seznam úloh, které můžete provádět. V seznamu úkolů **vyberte Změnit přihlášení uživatele.**

![Změna přihlášení uživatele](./media/plan-connect-user-signin/changeusersignin.png)

Na další stránce budete vyzváni k zadání přihlašovacích údajů pro Azure AD.

![Připojení k Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Na stránce **přihlášení uživatele** vyberte požadované přihlášení uživatele.

![Připojení k Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Pokud provádíte pouze dočasný přechod na synchronizaci hodnot hash hesel, zaškrtněte políčko **Nepřevádět uživatelské účty.** Nezaškrtnutí možnosti převede každého uživatele na federované a může to trvat několik hodin.
>
>

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [integraci místních identit pomocí služby Azure Active Directory](whatis-hybrid-identity.md).
- Další informace o [konceptech návrhu Azure AD Connect](plan-connect-design-concepts.md).
