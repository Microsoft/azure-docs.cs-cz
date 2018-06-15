---
title: Nastavení místního podmíněného přístupu v Azure Active Directory | Microsoft Docs
description: Podrobný návod k povolení podmíněného přístupu k místním aplikacím pomocí služby Active Directory Federation Services (AD FS) v systému Windows Server 2012 R2.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: 0ce4497a8bebf9078363509c1f962728ab4189f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764202"
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Nastavení místního podmíněného přístupu pomocí registrace zařízení služby Azure Active Directory
Pokud požadujete uživatelům připojení k pracovní ploše svých osobních zařízení pro službu Azure Active Directory (Azure AD) device registration service, můžete svoje zařízení označit jako známé pro vaši organizaci. Toto je podrobný návod k povolení podmíněného přístupu k místním aplikacím pomocí služby Active Directory Federation Services (AD FS) v systému Windows Server 2012 R2.

> [!NOTE]
> Office 365 licence nebo licenci Azure AD Premium je potřeba při použití zařízení, která jsou zaregistrována v zásady podmíněného přístupu služby registrace zařízení služby Azure Active Directory. Mezi ně patří zásady, které vynucuje služby AD FS v místních prostředků.
> 
> Další informace o scénářích podmíněného přístupu pro místní prostředky najdete v tématu [připojení k síti na pracovišti z libovolného zařízení pro jednotné přihlašování a bezproblémové dvouúrovňové ověřování napříč podnikovými aplikacemi](https://technet.microsoft.com/library/dn280945.aspx).

Tyto možnosti jsou dostupné pro zákazníky, kteří zakoupit licenci Azure Active Directory Premium.

## <a name="supported-devices"></a>Podporovaná zařízení
* Zařízení připojených k doméně systému Windows 7
* Zařízení s Windows 8.1 osobní a připojený k doméně
* iOS 6 nebo novější pro prohlížeč Safari
* Android 4.0 nebo novější, Samsung GS3 nebo telefony novější, Samsung Galaxy Poznámka 2 nebo novější tablety

## <a name="scenario-prerequisites"></a>Předpoklady pro scénář
* Předplatné Office 365 nebo Azure Active Directory Premium
* Klient služby Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 nebo novější)
* Aktualizované schéma v systému Windows Server 2012 R2
* Licence pro Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services, nakonfigurovat jednotné přihlašování do služby Azure AD
* Proxy webových aplikací systému Windows Server 2012 R2 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(stažení Azure AD Connect)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Ověřené domény

## <a name="known-issues-in-this-release"></a>Známé problémy v této verzi
* Zásady podmíněného přístupu na základě zařízení vyžadují zpětný zápis objektů zařízení do služby Active Directory z Azure Active Directory. Může trvat až tři hodiny pro objekty zařízení pro zápis zpátky do služby Active Directory.
* zařízení se systémem iOS 7 vždy zobrazí výzva k výběru certifikátu během ověření klientského certifikátu.
* Některé verze systému iOS 8 před iOS 8.3 nefungují.

## <a name="scenario-assumptions"></a>Předpoklady pro scénář
Tento scénář předpokládá, že máte hybridní prostředí skládající se z klienta služby Azure AD a místní Active Directory. Tyto klienty by měl být připojen službou Azure AD Connect, s ověřenou doménu a se službou AD FS pro jednotné přihlašování. Pomocí následujícího kontrolního seznamu vám pomohou nakonfigurovat vaše prostředí podle požadavků.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Kontrolní seznam: Požadavky pro scénář podmíněného přístupu
Klientovi služby Azure AD Connect s vaší místní instancí Active Directory.

## <a name="configure-azure-active-directory-device-registration-service"></a>Konfigurace služby Azure Active Directory device registration service
Použijte tuto příručku můžete nasadit a nakonfigurovat službu Azure Active Directory device registration service ve vaší organizaci.

Tato příručka předpokládá, že jste nakonfigurovali Windows Server Active Directory a mít předplatné Microsoft Azure Active Directory. Viz požadavky popsané výše.

Pokud chcete nasadit službu Azure Active Directory device registration service pomocí klienta služby Azure Active Directory, úkoly v následující kontrolní seznam v pořadí. Při použití odkazu přejdete na principiální téma, vraťte tento kontrolní seznam i později, tak, že můžete pokračovat ve zbývajících úkolech. Některé úlohy zahrnují krok ověření scénář, který vám může pomoct potvrďte, zda byl úspěšně dokončen v kroku.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Část 1: Registrace zařízení povolení Azure Active Directory
Postupujte podle kroků v seznamu úkolů povolit a nakonfigurovat službu Azure Active Directory device registration service.

| Úkol | Referenční informace | 
| --- | --- |
| Povolte registraci zařízení v klientovi služby Azure Active Directory umožňuje zařízení pro připojení k síti na pracovišti. Ve výchozím nastavení není povoleno ověřování Azure Multi-Factor Authentication pro službu. Doporučujeme však používat ověřování Multi-Factor Authentication při registraci zařízení. Před povolením vícefaktorového ověřování ve službě Active Directory registrace, ujistěte se, že služba AD FS je nakonfigurován pro poskytovatele služby Multi-Factor Authentication. |[Povolení registrace zařízení služby Azure Active Directory](active-directory-device-registration-get-started.md)| 
|Zařízení zjistit služby registrace zařízení služby Azure Active Directory tak, že vyhledá známých záznamů DNS. Nakonfigurujte záznamy DNS vaší společnosti, aby zařízení můžete zjistit služby registrace zařízení služby Azure Active Directory. |[Konfigurace zjišťování nástroje registrace zařízení služby Azure Active Directory](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Část 2: Nasazení a konfigurace Windows serveru 2012 R2 Active Directory Federation Services a nastavte vztah federace se službou Azure AD

| Úkol | Referenční informace |
| --- | --- |
| Nasazení služby Active Directory Domain Services s rozšířeními schématu systému Windows Server 2012 R2. Není nutné upgradovat všechny řadiče domény na Windows Server 2012 R2. Upgrade schématu je jediným požadavkem. |[Upgradu vašeho schématu Active Directory Domain Services](#upgrade-your-active-directory-domain-services-schema) |
| Zařízení zjistit služby registrace zařízení služby Azure Active Directory tak, že vyhledá známých záznamů DNS. Nakonfigurujte záznamy DNS vaší společnosti, aby zařízení můžete zjistit služby registrace zařízení služby Azure Active Directory. |[Příprava zařízení podporu služby Active Directory](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>Část 3: Zpětný zápis zařízení povolit ve službě Azure AD
| Úkol | Referenční informace |
| --- | --- |
| Dokončení druhé části "Povolení zpětného zápisu zařízení v Azure AD Connect." Po dokončení se vraťte do této příručce. |[Povolení zpětného zápisu zařízení v Azure AD Connect](./connect/active-directory-aadconnect-feature-device-writeback.md) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Nepovinné] Část 4: Povolení služby Multi-Factor Authentication
Důrazně doporučujeme nakonfigurovat jednu z několik možností pro službu Multi-Factor Authentication. Pokud chcete vyžadovat Vícefaktorové ověřování, přečtěte si téma [vybrat řešení zabezpečení Multi-Factor Authentication pro vás](authentication/concept-mfa-whichversion.md). Obsahuje popis jednotlivých řešení a odkazy, které vám pomohou při konfiguraci řešení podle svého výběru.

## <a name="part-5-verification"></a>Část 5: ověření
Nasazení je nyní dokončen a můžete vyzkoušet některé scénáře. Pomocí následujících odkazů experimentovat se službou a seznámit se s jeho funkce.

| Úkol | Referenční informace |
| --- | --- |
| Připojení některá zařízení k pracovní ploše pomocí služby Azure Active Directory device registration service. Toho se můžete zapojit iOS, Windows a zařízení se systémem Android. |[Připojení zařízení k pracovní ploše pomocí služby Azure Active Directory device registration service](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Zobrazit a povolit nebo zakázat registrovaná zařízení pomocí portálu správce. V této úloze zobrazit některé registrovaná zařízení pomocí portálu správce. |[Přehled služby registrace zařízení služby Azure Active Directory](active-directory-device-registration-get-started.md) |
| Ověřte, že objekty zařízení jsou zapsány zpět z Azure Active Directory na Windows Server Active Directory. |[Ověřte, že registrovaná zařízení, zapíšou se zpět do služby Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Teď, když uživatelé mohou registrovat svá zařízení, můžete vytvořit aplikaci ve službě AD FS, které umožní pouze k registrovaným zařízením zásady přístupu. V této úloze vytvoříte pravidlo přístupu aplikace a vlastní zprávu o odepření přístupu. |[Vytvoření zásad přístupu aplikace a vlastní zprávu při odepření přístupu](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integraci služby Azure Active Directory s místní služby Active Directory

**Přejděte na téma:**

- [Integrace místních adresářů se službou Azure Active Directory](./connect/active-directory-aadconnect.md) – Chcete-li zkontrolovat koncepční informace.

- [Vlastní instalace Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) – pokyny k instalaci.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Upgradu vašeho schématu Active Directory Domain Services
> [!NOTE]
> Po upgradu schéma služby Active Directory, tento proces je nevratný. Doporučujeme nejprve provést upgrade v testovacím prostředí.
> 

1. Přihlaste se k řadiči domény pomocí účtu, který má oprávnění správce schématu i správce podnikové sítě.
2. Kopírování **[media] \support\adprep** adresář a podadresáře do jednoho z řadičů domény služby Active Directory (kde **[media]** je cesta k instalačnímu médiu systému Windows Server 2012 R2).
4. Z příkazového řádku, přejděte na **adprep** adresáře a spusťte **adprep.exe/forestprep**. Postupujte podle pokynů na obrazovce a dokončit upgrade schématu.

## <a name="prepare-your-active-directory-to-support-devices"></a>Příprava služby Active Directory pro podporu zařízení
> [!NOTE]
> Jedná se o jednorázovou operaci, které je třeba spustit Příprava doménové struktury služby Active Directory pro podporu zařízení. K dokončení tohoto postupu můžete musí být podepsané pomocí oprávnění správce rozlehlé sítě a doménové struktury služby Active Directory musí mít schématu Windows serveru 2012 R2.
> 


### <a name="prepare-your-active-directory-forest"></a>Příprava doménové struktury služby Active Directory
1. Na federačním serveru, otevřete příkazové okno prostředí Windows PowerShell a zadejte **inicializovat ADDeviceRegistration**. 
2. Po zobrazení výzvy k **ServiceAccountName**, zadejte název účtu služby, který jste vybrali jako účet služby pro službu AD FS. Pokud se jedná o účet gMSA, zadejte účet v **domain\accountname$** formátu. Pro účet domény, použijte formát **domain\accountname**.

### <a name="enable-device-authentication-in-ad-fs"></a>Povolit ověřování zařízení ve službě AD FS
1. Na federačním serveru, otevřete konzolu pro správu služby AD FS a přejděte na **služby AD FS** > **zásady ověřování**.
2. Na **akce** podokně, vyberte **upravit globální primární ověřování**.
3. Zkontrolujte **povolit ověřování zařízení**a potom vyberte **OK**.
4. Ve výchozím nastavení služba AD FS pravidelně odebere nepoužívané zařízení ze služby Active Directory. Při použití služby Azure Active Directory device registration service, aby zařízení můžete spravovat v Azure, zakažte tuto úlohu.

### <a name="disable-unused-device-cleanup"></a>Zakázání nepoužívaných zařízení čištění
Na federačním serveru, otevřete příkazové okno prostředí Windows PowerShell a zadejte **Set AdfsDeviceRegistration - MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Příprava Azure AD Connect pro zpětný zápis zařízení.
Dokončení část 1: Příprava Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Připojení zařízení k pracovní ploše pomocí služby Azure Active Directory device registration service

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Připojení zařízení s iOS pomocí registrace zařízení služby Azure Active Directory
Registrace zařízení služby Azure Active Directory používá proces bezdrátového profilu registrace pro zařízení s iOS. Tento proces začíná, když se uživatel připojí k adrese URL profil zápisu s Safari. Formát adresy URL je následující:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

V takovém případě `yourdomainname` je název domény, který jste nakonfigurovali v Azure Active Directory. Například pokud je název vaší domény contoso.com, adresa URL je následující:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Adresu URL uživatelům sdělit mnoha různými způsoby. Například jedna metoda, doporučujeme je publikování tuto adresu URL ve zprávě o odepření přístupu vlastní aplikaci ve službě AD FS. Tyto informace je popsaná v následující části [vytvoření zásad přístupu aplikace a vlastní zprávu při odepření přístupu](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Připojení zařízení s Windows 8.1 pomocí registrace zařízení služby Azure Active Directory
1. Na zařízení s Windows 8.1, vyberte **nastavení počítače** > **sítě** > **síti na pracovišti**.
2. Zadejte uživatelské jméno ve formátu UPN. například **dan@contoso.com**.
3. Vyberte **připojení**.
4. Pokud budete vyzváni, přihlaste se pomocí přihlašovacích údajů. Zařízení je teď připojené.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Připojení zařízení s Windows 7 pomocí registrace zařízení služby Azure Active Directory
Chcete-li zaregistrovat zařízení připojených k doméně systému Windows 7, je potřeba nasadit [balíček softwaru registrace zařízení](https://www.microsoft.com/download/details.aspx?id=53554).

Pokyny o tom, jak pomocí balíčku najdete v tématu [balíčky Instalační služby systému Windows pro počítače s Windows 10](device-management-hybrid-azuread-joined-devices-setup.md#windows-installer-packages-for-non-windows-10-computers).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Ověřte, že registrovaná zařízení, zapíšou se zpět do služby Active Directory
Můžete zobrazit a ověřit, že objektů vašeho zařízení byly zapsány zpět do služby Active Directory pomocí nástroje LDP.exe nebo ADSI Edit. Obě možnosti jsou dostupné pomocí nástroje pro správu služby Active Directory.

Ve výchozím nastavení jsou objekty zařízení, které jsou zapsány zpět z Azure Active Directory umístěny ve stejné doméně jako farmu služby AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Vytvoření zásad přístupu aplikace a vlastní zprávu při odepření přístupu
Vezměte v úvahu následující scénář: vytvoření aplikace vztahu důvěryhodnosti předávající strany ve službě AD FS a nakonfigurovat autorizační pravidlo vystavování, která umožňuje pouze k registrovaným zařízením. Nyní jsou povoleny pouze zařízení, které jsou registrovány pro přístup k aplikaci. 

Chcete-li snadno pro vaše uživatele k získání přístupu k aplikaci, nakonfigurovat vlastní zprávu odepření přístupu, která obsahuje pokyny k připojení zařízení. Uživatelé teď mají bezproblémové způsob, jak registrovat svá zařízení, aby mohli získat přístup aplikaci.

Následující kroky ukazují, jak implementovat tento scénář.

> [!NOTE]
> V této části se předpokládá, že jste již nakonfigurovali vztahu důvěryhodnosti předávající strany pro aplikaci ve službě AD FS.
> 

1. Otevřete nástroj konzoly MMC AD FS a pak vyberte **služby AD FS** > **vztahy důvěryhodnosti** > **vztahy důvěryhodnosti předávající strany**.
2. Vyhledejte aplikaci, na který se vztahuje toto nové pravidlo přístupu. Klikněte pravým tlačítkem na aplikaci a potom vyberte **upravit pravidla deklarací identity**.
3. Vyberte **autorizační pravidla vystavování** a pak vyberte **přidat pravidlo**.
4. Z **pravidlo deklarace identity** šablony rozevíracího seznamu, vyberte **povolení nebo odmítnutí uživatele na příchozí deklarace identity základě**. Pak vyberte **Další**.
5. V **název pravidla deklarací** zadejte **povolení přístupu z registrovaných zařízení**.
6. Z **typ příchozí deklarace** rozevíracího seznamu vyberte **je registrovaný uživatel**.
7. V **hodnota příchozí deklarace** zadejte **true**.
8. Vyberte **povolit přístup uživatelům s touto příchozí deklarací identity** přepínač.
9. Vyberte **Dokončit**a potom vyberte **použít**.
10. Odeberte všechna pravidla, která jsou mírnější než pravidlo, které jste vytvořili. Například odstranit výchozí pravidlo **povolit přístup všem uživatelům**.

Aplikace je nyní nakonfigurována pro povolení přístupu jenom v případě, že uživatel pochází ze zařízení, které by zaregistrovány a připojené k pracovní ploše. Pro pokročilejší přístup zásady, najdete v části [řízení rizik pomocí dodatečného vícefaktorového ověřování citlivých aplikací](https://technet.microsoft.com/library/dn280949.aspx).

Dále je nutné nakonfigurovat vlastní chybové zprávy pro vaši aplikaci. Chybová zpráva umožňuje uživatelům vědět, že se musíte připojit zařízení k pracovní ploše předtím, než získají přístup k aplikaci. Zpráva o odepření přístupu vlastní aplikaci můžete vytvořit pomocí vlastních HTML a prostředí PowerShell.

Na federačním serveru otevřete příkazové okno prostředí PowerShell a zadejte následující příkaz. Části příkazu nahraďte položky, které jsou specifické pro váš systém:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Zařízení je nutné zaregistrovat si před přístupem k této aplikaci.

**Pokud používáte zařízení se systémem iOS, vyberte tento odkaz na připojení zařízení**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Připojte toto zařízení iOS na pracovišti.

Pokud používáte zařízení s Windows 8.1, můžete svoje zařízení připojíte výběrem **nastavení počítače**> **sítě** > **síti na pracovišti**.

V předchozích příkazech **název vztahu důvěryhodnosti předávající strany** je název vaší aplikace objekt vztahu důvěryhodnosti předávající strany ve službě AD FS.
A **vase_domena.com** je název domény, který jste nakonfigurovali v Azure Active Directory (například contoso.com).
Nezapomeňte odebrat všechny konce řádků (pokud existuje) z obsah HTML, který můžete předat **Set-AdfsRelyingPartyWebContent** rutiny.

Nyní když uživatelé přístup k aplikaci ze zařízení, které není registrován u službu Azure Active Directory device registration service, uvidí k chybě.

