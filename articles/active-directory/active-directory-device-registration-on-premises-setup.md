---
title: Nastavení místního podmíněného přístupu v Azure Active Directory | Dokumentace Microsoftu
description: Podrobný návod k povolení podmíněného přístupu k místním aplikacím pomocí služby Active Directory Federation Services (AD FS) v systému Windows Server 2012 R2.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: devices
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: de5ccd62a9624ad463a51fa204c0b5dca19992ef
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226165"
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Nastavení místního podmíněného přístupu pomocí registrace zařízení služby Azure Active Directory
Pokud požadujete uživatelům připojení k pracovní ploše svoje osobní zařízení pro službu registrace zařízení služby Azure Active Directory (Azure AD), můžete svoje zařízení označit jako známé pro vaši organizaci. Toto je podrobný návod k povolení podmíněného přístupu k místním aplikacím pomocí služby Active Directory Federation Services (AD FS) v systému Windows Server 2012 R2.

> [!NOTE]
> Při použití zařízení, které jsou registrovány zásady podmíněného přístupu služby registrace zařízení služby Azure Active Directory, je požadován k licenci Office 365 nebo licenci Azure AD Premium. Patří mezi ně zásady, které vynucují službou AD FS v místních prostředků.
> 
> Další informace o scénářích podmíněného přístupu pro místní prostředky, najdete v části [připojení k síti na pracovišti z libovolného zařízení pro jednotné přihlašování a bezproblémové dvouúrovňové ověřování napříč podnikovými aplikacemi](https://technet.microsoft.com/library/dn280945.aspx).

Tyto možnosti jsou dostupné pro zákazníky, kteří si koupí licenci Azure Active Directory Premium.

## <a name="supported-devices"></a>Podporovaná zařízení
* Zařízení připojená k doméně Windows 7
* Zařízení s Windows 8.1 osobní a připojený k doméně
* iOS 6 nebo novější pro prohlížeč Safari
* Android 4.0 nebo novější, Samsung GS3 nebo novější telefony, Samsung Galaxy Poznámka 2 nebo novější tablety

## <a name="scenario-prerequisites"></a>Požadavky na scénář
* Předplatné Office 365 nebo Azure Active Directory Premium
* Klient služby Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 nebo novější)
* Aktualizované schéma v systému Windows Server 2012 R2
* Licence pro Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services, nakonfigurované pro jednotné přihlašování do služby Azure AD
* Proxy webových aplikací systému Windows Server 2012 R2 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(Stažení služby Azure AD Connect)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Ověřená doména

## <a name="known-issues-in-this-release"></a>Známé problémy v této verzi
* Zásady podmíněného přístupu podle zařízení vyžadují zpětný zápis objektů zařízení do služby Active Directory ze služby Azure Active Directory. Může trvat až tři hodiny objekty zařízení k zapsání zpět do služby Active Directory.
* zařízení se systémem iOS 7 vždy vyzvat uživatele k výběru certifikátu během ověření klientského certifikátu.
* Některé verze iOS 8 předtím, než iOS 8.3, nebudou fungovat.

## <a name="scenario-assumptions"></a>Předpoklady pro scénář
Tento scénář předpokládá, že máte hybridní prostředí, který se skládá z tenanta služby Azure AD a místní Active Directory. Tyto klienti musí být připojené službou Azure AD Connect s ověřenou doménou a službou AD FS pro SSO. Pomocí následujícího kontrolního seznamu vám pomohou nakonfigurovat podle požadavků vašeho prostředí.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Kontrolní seznam: Požadavky pro scénář podmíněného přístupu
Připojení vašeho tenanta Azure AD s vaší místní instancí Active Directory.

## <a name="configure-azure-active-directory-device-registration-service"></a>Konfigurace služby registrace zařízení služby Azure Active Directory
Použijte tuto příručku pro nasazení a konfigurace služby registrace zařízení služby Azure Active Directory pro vaši organizaci.

Tento průvodce to předpokládá, že jste nakonfigurovali Windows Server Active Directory a přihlásili k odběru Microsoft Azure Active Directory. Viz požadavky popsané výše.

Pokud chcete nasadit službu registrace zařízení služby Azure Active Directory v tenantu Azure Active Directory, úkoly v následujícím kontrolním seznamu udělejte v pořadí. Po kliknutí na odkaz přejdete na principiální téma, vraťte se na tento kontrolní seznam později, tak, abyste mohli pokračovat ve zbývajících úkolech. Některé úlohy zahrnout krok ověření scénář, který může pomoci potvrdit, zda byl úspěšně dokončen v kroku.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Část 1: Registrace zařízení povolit Azure Active Directory
Postupujte podle kroků v seznamu úkolů povolit a nakonfigurovat služba Azure Active Directory device registration.

| Úkol | Referenční informace | 
| --- | --- |
| Povolte registraci zařízení ve vašem tenantovi Azure Active Directory a povolit zařízením připojit k pracovní ploše. Ve výchozím nastavení není povolené ověřování Azure Multi-Factor Authentication pro službu. Doporučujeme však použít Vícefaktorové ověřování při registraci zařízení. Před povolením služby Multi-Factor Authentication ve službě Active Directory registrace, ujistěte se, že je služba AD FS nakonfigurovaný pro poskytovatele služby Multi-Factor Authentication. |[Povolení registrace zařízení služby Azure Active Directory](active-directory-device-registration-get-started.md)| 
|Zařízení zjistit služby registrace zařízení služby Azure Active Directory pomocí známých záznamů DNS. Nakonfigurujte záznamy DNS vaší společnosti tak, aby mohla zařízení zjistit služby registrace zařízení služby Azure Active Directory. |[Konfigurace zjišťování registrace zařízení ve službě Azure Active Directory](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Část 2: Nasaďte a nakonfigurujte Windows Server 2012 R2 Active Directory Federation Services a nastavte vztah federace s Azure AD

| Úkol | Referenční informace |
| --- | --- |
| Nasazení služby Active Directory Domain Services s rozšířeními schématu systému Windows Server 2012 R2. Nemusíte upgradovat všechny řadiče domény na Windows Server 2012 R2. Upgrade schématu je jediným požadavkem. |[Upgradu vašeho schématu Active Directory Domain Services](#upgrade-your-active-directory-domain-services-schema) |
| Zařízení zjistit služby registrace zařízení služby Azure Active Directory pomocí známých záznamů DNS. Nakonfigurujte záznamy DNS vaší společnosti tak, aby mohla zařízení zjistit služby registrace zařízení služby Azure Active Directory. |[Příprava zařízení podporu Active Directory](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>3. část: Zpětný zápis zařízení povolit ve službě Azure AD
| Úkol | Referenční informace |
| --- | --- |
| Dokončení druhé části "Povolení zpětného zápisu zařízení ve službě Azure AD Connect." Po dokončení se vraťte do tohoto průvodce. |[Povolení zpětného zápisu zařízení v Azure AD Connect](./connect/active-directory-aadconnect-feature-device-writeback.md) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Volitelné] 4. část: Povolení služby Multi-Factor Authentication
Důrazně doporučujeme konfigurovat jednu z několika možností pro ověřování službou Multi-Factor Authentication. Pokud chcete vyžadovat Vícefaktorové ověřování, přečtěte si téma [vyberte řešení zabezpečení Multi-Factor Authentication vám](authentication/concept-mfa-whichversion.md). Obsahuje popis jednotlivých řešení a odkazy, které vám pomohou nakonfigurovat řešení podle vašeho výběru.

## <a name="part-5-verification"></a>Část 5: ověření
Nasazení je nyní dokončen a můžete vyzkoušet některé scénáře. Pomocí následujících odkazů můžete experimentovat se službou a seznámit se s jeho funkcí.

| Úkol | Referenční informace |
| --- | --- |
| Připojte se k použití služby registrace zařízení služby Azure Active Directory některá zařízení k pracovní ploše. Můžete připojit zařízení s Androidem, iOS a Windows. |[Připojení zařízení k pracovišti prostřednictvím služby registrace zařízení služby Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Zobrazit a povolit nebo zakázat registrovaná zařízení pomocí portálu správce. Při plnění tohoto úkolu zobrazit některé registrovaná zařízení pomocí portálu správce. |[Azure Active Directory – registrace zařízení – přehled služby](active-directory-device-registration-get-started.md) |
| Ověřte, že objekty zařízení jsou zapsány zpět z Azure Active Directory na Windows Server Active Directory. |[Ověřte, že registrovaná zařízení, zapíšou se zpět do služby Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Teď, když uživatelé mohou registrovat svá zařízení, můžete vytvořit aplikaci ve službě AD FS, díky kterým můžou pouze k registrovaným zařízením zásady přístupu. V této úloze vytvoříte pravidlo přístupu aplikace a vlastní zprávu o odepření přístupu. |[Vytvoření zásad přístupu aplikací a vlastní zprávy o odepřeném přístupu](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrace služby Azure Active Directory se službou Active Directory on-premises

**Přejděte na téma:**

- [Integrace místních adresářů se službou Azure Active Directory](./connect/active-directory-aadconnect.md) – Pokud chcete zkontrolovat koncepční informace.

- [Vlastní instalace služby Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) – pokyny k instalaci.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Upgradu vašeho schématu Active Directory Domain Services
> [!NOTE]
> Po upgradu vašeho schématu služby Active Directory, proces je nevratný. Doporučujeme nejprve provést upgrade v testovacím prostředí.
> 

1. Přihlaste se k řadiči domény s účtem, který má oprávnění správce schématu i správce podnikové sítě.
2. Kopírovat **[media] \support\adprep** adresáře a podadresářů na jeden z řadičů domény služby Active Directory (kde **[media]** je cesta k instalačnímu médiu systému Windows Server 2012 R2).
4. Z příkazového řádku, přejděte **adprep** adresář a spusťte **adprep.exe/forestprep**. Postupujte podle pokynů na obrazovce a dokončit upgrade schématu.

## <a name="prepare-your-active-directory-to-support-devices"></a>Příprava služby Active Directory pro podporu zařízení
> [!NOTE]
> Jde o jednorázovou operaci, která je nutné spustit připravit doménovou strukturu Active Directory pro podporu zařízení. Chcete-li provést tento postup, musíte být přihlášeni pomocí oprávnění správce na úrovni podniku a vaší doménové struktury služby Active Directory musí mít schématu Windows serveru 2012 R2.
> 


### <a name="prepare-your-active-directory-forest"></a>Příprava doménové struktury služby Active Directory
1. Na federačním serveru, otevřete příkazové okno prostředí Windows PowerShell a zadejte **inicializace ADDeviceRegistration**. 
2. Po zobrazení výzvy k **ServiceAccountName**, zadejte název účtu služby, který jste vybrali jako účet služby pro službu AD FS. Pokud je účet gMSA, zadejte účet ve **domain\accountname$** formátu. Pro účet domény, použijte formát **domain\accountname**.

### <a name="enable-device-authentication-in-ad-fs"></a>Povolit ověřování zařízení ve službě AD FS
1. Na federačním serveru, otevřete konzolu pro správu služby AD FS a přejděte na **služby AD FS** > **zásady ověřování**.
2. Na **akce** vyberte **upravit globální primární ověřování**.
3. Zkontrolujte **povolit ověřování zařízení**a pak vyberte **OK**.
4. Ve výchozím nastavení služby AD FS pravidelně odebere nevyužité zařízení ze služby Active Directory. Tento úkol zakážete, když používáte službu registrace zařízení služby Azure Active Directory tak, aby zařízení se dají spravovat v Azure.

### <a name="disable-unused-device-cleanup"></a>Zakázat čištění nevyužitých zařízení
Na federačním serveru, otevřete příkazové okno prostředí Windows PowerShell a zadejte **Set AdfsDeviceRegistration - MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Příprava služby Azure AD Connect pro zpětný zápis zařízení
Doplňte část 1: Příprava Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Připojte se k zařízení k pracovní ploše pomocí služby registrace zařízení služby Azure Active Directory

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Připojte se k zařízení s Iosem pomocí registrace zařízení služby Azure Active Directory
Registrace zařízení služby Azure Active Directory používá profil bezdrátového proces registrace pro zařízení s Iosem. Tento proces začíná, když uživatel připojí k adrese URL profilu registrace s prohlížečem Safari. Formát adresy URL vypadá takto:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

V takovém případě `yourdomainname` je název domény, který jste nakonfigurovali v Azure Active Directory. Například pokud je název domény contoso.com, adresa URL vypadá takto:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Adresu URL svým uživatelům sdělit mnoha různými způsoby. Například jedna metoda, doporučujeme publikuje tuto adresu URL v zpráva Přístup byl odepřen vlastní aplikaci ve službě AD FS. Tyto informace najdete v následující části [vytvoření zásad přístupu aplikací a vlastní zprávy o odepřeném přístupu](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Připojte se k zařízení s Windows 8.1 pomocí registrace zařízení služby Azure Active Directory
1. Na zařízení s Windows 8.1, vyberte **nastavení počítače** > **sítě** > **pracoviště**.
2. Zadejte své uživatelské jméno ve formátu hlavního názvu uživatele. například **dan@contoso.com**.
3. Vyberte **připojit**.
4. Po zobrazení výzvy, přihlaste se pomocí svých přihlašovacích údajů. Zařízení je teď připojené.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Připojte se k zařízení s Windows 7 pomocí registrace zařízení služby Azure Active Directory
K registraci zařízení Windows 7 připojených k doméně, je potřeba nasadit [balíček softwaru registrace zařízení](https://www.microsoft.com/download/details.aspx?id=53554).

Pokyny o tom, jak pomocí balíčku najdete v tématu [balíčky Instalační služby systému Windows pro počítače s Windows 10](device-management-hybrid-azuread-joined-devices-setup.md#windows-installer-packages-for-non-windows-10-computers).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Ověřte, že registrovaná zařízení, zapíšou se zpět do služby Active Directory
Můžete zobrazit a ověřit, že vaše zařízení objekty byly zapsány zpět do služby Active Directory pomocí LDP.exe nebo nástroje ADSI Edit. Obě jsou dostupné nástroje pro správu služby Active Directory.

Ve výchozím nastavení jsou umístěny objekty zařízení, které jsou zapsány zpět z Azure Active Directory ve stejné doméně jako farmu služby AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Vytvoření zásad přístupu aplikací a vlastní zprávy o odepřeném přístupu
Vezměte v úvahu následující scénář: vytvoření aplikace vztah důvěryhodnosti předávající strany ve službě AD FS a nakonfigurovat autorizační pravidlo vystavování, která umožňuje pouze k registrovaným zařízením. Teď jsou povolené jenom zařízení, které jsou registrovány pro přístup k aplikaci. 

Chcete-li můžou vaši uživatelé pro získání přístupu k aplikaci, nakonfigurovat vlastní zprávu odepření přístupu, který obsahuje pokyny pro připojení zařízení. Teď budou mít uživatelé bezproblémové způsob, jak zaregistrovat zařízení, aby mohli získat přístup aplikaci.

Následující kroky ukazují, jak implementovat tento scénář.

> [!NOTE]
> V této části se předpokládá, že jste již nakonfigurovali vztahu důvěryhodnosti předávající strany pro aplikaci ve službě AD FS.
> 

1. Otevřete nástroj AD FS MMC a potom vyberte **služby AD FS** > **vztahy důvěryhodnosti** > **vztahy důvěryhodnosti předávající strany**.
2. Vyhledejte aplikaci, ke kterému tento nový pravidlo přístupu použito. Klikněte pravým tlačítkem na aplikaci a pak vyberte **upravit pravidla deklarací identity**.
3. Vyberte **autorizační pravidla vystavování** kartu a potom vyberte **přidat pravidlo**.
4. Z **pravidlo deklarace identity** šablony rozevíracího seznamu vyberte **povolení nebo odmítnutí uživatele na příchozí deklarace identity základě**. Pak vyberte **Další**.
5. V **název pravidla deklarace identity** zadejte **povolovala přístup z registrovaných zařízení**.
6. Z **typ příchozí deklarace** rozevíracího seznamu vyberte **je uživatel zaregistrovaný**.
7. V **hodnota příchozí deklarace** zadejte **true**.
8. Vyberte **povolit přístup uživatelům s touto příchozí deklarací identity** přepínač.
9. Vyberte **Dokončit**a pak vyberte **použít**.
10. Odeberte všechna pravidla, která jsou méně restriktivní než pravidlo, které jste vytvořili. Například odstranit výchozí pravidlo **povolit všem uživatelům přístup ke**.

Vaše aplikace je nyní nakonfigurována pro povolení přístupu pouze v případě, že uživatel pochází ze zařízení, které jsou zaregistrované a připojen k pracovní ploše. Zásady rozšířené přístupu najdete v části [Přehled řízení rizik pomocí dodatečného vícefaktorového ověřování citlivých aplikací](https://technet.microsoft.com/library/dn280949.aspx).

Dále je nutné nakonfigurovat vlastní chybové zprávy pro vaši aplikaci. Chybová zpráva dá uživatelům vědět, že se musíte připojit svoje zařízení k pracovní ploše před přístupem k aplikaci. Zpráva o odepření přístupu vlastní aplikace můžete vytvořit pomocí vlastních HTML a prostředí PowerShell.

Na federačním serveru otevřete příkazové okno Powershellu a pak zadejte následující příkaz. Nahraďte položky, které jsou specifické pro váš systém části příkazu:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Zařízení musí zaregistrovat, získat přístup k této aplikaci.

**Pokud používáte zařízení se systémem iOS, vyberte tento odkaz na připojení zařízení**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Připojte toto zařízení s iOS k pracovišti.

Pokud používáte zařízení s Windows 8.1, můžete svoje zařízení připojíte tak, že vyberete **nastavení počítače**> **sítě** > **pracoviště**.

V předchozích příkazech **název vztahu důvěryhodnosti přijímající strany** je název vaší aplikace objekt vztahu důvěryhodnosti předávající strany ve službě AD FS.
A **vasedomena.com** je název domény, kterou jste nakonfigurovali v Azure Active Directory (například contoso.com).
Nezapomeňte odebrat všechna zalomení řádků (pokud existuje) z obsahu HTML, který můžete předat **Set-AdfsRelyingPartyWebContent** rutiny.

Nyní když uživatelům přístup k aplikaci ze zařízení, která není registrována službou Azure Active Directory device registration service, zobrazí chybu.

