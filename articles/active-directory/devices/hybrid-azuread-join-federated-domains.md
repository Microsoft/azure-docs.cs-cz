---
title: Konfigurace hybridního připojení služby Azure Active Directory pro federované domény | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat hybridní připojení služby Azure Active Directory pro federované domény.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a61c89199c89f09b5cc0e553dbbf48655ad1b6a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239095"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Kurz: Konfigurace hybridního připojení k Azure Active Directory pro federované domény

Stejně jako uživatel ve vaší organizaci je zařízení základní identitou, kterou chcete chránit. Identitu zařízení můžete použít k ochraně prostředků kdykoli a z libovolného místa. Tohoto cíle můžete dosáhnout tím, že přivedete identity zařízení a spravujete je ve službě Azure Active Directory (Azure AD) pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Uvedení vašich zařízení do Azure AD maximalizuje produktivitu uživatelů prostřednictvím jednotného přihlašování (SSO) napříč cloudovými a místními prostředky. Podmíněný přístup můžete zabezpečit pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) současně.

Federované prostředí by mělo mít zprostředkovatele identity, který podporuje následující požadavky. Pokud máte federované prostředí používající službu AD FS (Active Directory Federation Services), jsou již následující požadavky podporovány.

- **Tvrzení WIAORMULTIAUTHN:** Tato deklarace je vyžadována k provedení hybridního připojení Azure AD pro zařízení nižší úrovně windows.
- **WS-Trust protokol:** Tento protokol je nutný k ověření aktuálního hybridního zařízení Azure AD pro Windows pomocí Azure AD.
  Pokud používáte službu AD FS, musíte povolit následující koncové body WS-Trust:`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **Adfs/services/trust/2005/windowstransport** a **adfs/services/trust/13/windowstransport** by měly být povoleny pouze jako koncové body směřující k intranetu a nesmí být vystaveny jako koncové body směřující k extranetu prostřednictvím proxy webové aplikace. Další informace o zakázání koncových bodů systému WS-Trust systému Windows naleznete [v tématu Zakázání koncových bodů systému WS-Trust systému Windows na serveru proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Můžete vidět, jaké koncové body jsou povoleny prostřednictvím konzoly pro správu služby AD FS v části Koncové body **služby** > **Endpoints**.

V tomto kurzu se dozvíte, jak nakonfigurovat hybridní připojení Azure AD pro počítače se službou Active Directory pro počítače se službou Active Directory v federovaném prostředí pomocí služby AD FS.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolení zařízení nižší úrovně systému Windows
> * Ověření registrace
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste obeznámeni s těmito články:

- [Co je identita zařízení?](overview.md)
- [Jak naplánovat implementaci hybridního připojení k Azure AD](hybrid-azuread-join-plan.md)
- [Jak provést řízené ověření hybridního připojení Azure AD](hybrid-azuread-join-control.md)

Ke konfiguraci scénáře v tomto kurzu budete potřebovat:

- Windows Server 2012 R2 se službou AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) verze 1.1.819.0 nebo novější

Počínaje verzí 1.1.819.0 azure ad connect obsahuje průvodce, který můžete použít ke konfiguraci hybridního připojení Azure AD. Průvodce výrazně zjednodušuje proces konfigurace. Související průvodce:

- Konfiguruje body připojení služby (SCP) pro registraci zařízení.
- Zálohuje existující vztah důvěryhodnosti přijímající strany Azure AD.
- Aktualizuje pravidla deklarace identity ve vztahu důvěryhodnosti Azure AD.

Kroky konfigurace v tomto článku jsou založeny na použití průvodce Azure AD Connect. Pokud máte nainstalovanou starší verzi služby Azure AD Connect, musíte ji upgradovat na 1.1.819 nebo novější, abyste mohli průvodce používat. Pokud instalace nejnovější verze Azure AD Connect není možnost pro vás, podívejte [se, jak ručně nakonfigurovat hybridní připojení Azure AD](hybrid-azuread-join-manual.md).

Hybridní připojení Azure AD vyžaduje, aby zařízení měla přístup k následujícím prostředkům Microsoftu z vlastní sítě vaší organizace:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Služba tokenů zabezpečení vaší organizace (STS) (pro federované domény)
- `https://autologon.microsoftazuread-sso.com`(Pokud používáte nebo plánujete používat bezproblémové jednotné přihlašovat)

Počínaje Windows 10 1803, pokud okamžité hybridní připojení Azure AD pro federované prostředí pomocí služby AD FS selže, spoléháme na Azure AD Connect pro synchronizaci objektu počítače ve službě Azure AD, který se následně používá k dokončení registrace zařízení pro hybridní Azure AD připojit. Ověřte, že Azure AD Connect synchronizoval objekty počítače zařízení, která chcete být hybridní Azure AD připojen k Azure AD. Pokud objekty počítače patří do konkrétních organizačních jednotek (OU), musíte také nakonfigurovat organizační jednotky pro synchronizaci v Azure AD Connect. Další informace o synchronizaci objektů počítače pomocí služby Azure AD Connect najdete v [tématu Konfigurace filtrování pomocí služby Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Pokud vaše organizace vyžaduje přístup k internetu prostřednictvím odchozího proxy serveru, společnost Microsoft doporučuje [implementovat automatické zjišťování webového proxy serveru (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) pro povolení počítačů s Windows 10 pro registraci zařízení pomocí služby Azure AD. Pokud narazíte na problémy s konfigurací a správou wpad, [přečtěte si článek Poradce při potížích s automatickou detekcí](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Pokud nepoužíváte WPAD a chcete konfigurovat nastavení proxy serveru v počítači, můžete tak učinit počínaje systémem Windows 10 1709. Další informace naleznete [v tématu Konfigurace nastavení winhttp pomocí objektu zásad skupiny (GPO).](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)

> [!NOTE]
> Pokud nakonfigurujete nastavení serveru proxy v počítači pomocí nastavení WinHTTP, všechny počítače, které se nemohou připojit k nakonfigurovanému serveru proxy, se nepřipojují k Internetu.

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, musíte se ujistit, že se počítače se systémem Windows 10 mohou úspěšně ověřit na odchozím proxy serveru. Vzhledem k tomu, že počítače se systémem Windows 10 spouštějí registraci zařízení pomocí kontextu počítače, je nutné nakonfigurovat ověřování odchozího proxy serveru pomocí kontextu počítače. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

Chcete-li ověřit, zda je zařízení schopno přistupovat k výše uvedeným prostředkům společnosti Microsoft v rámci systémového účtu, můžete použít skript [Test Device Registration Connectivity.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Chcete-li nakonfigurovat hybridní připojení Azure AD pomocí služby Azure AD Connect, potřebujete:

- Přihlašovací údaje globálního správce pro vašeho klienta Azure AD  
- Pověření správce rozlehlé sítě pro každou doménovou struktura
- Přihlašovací údaje správce služby AD FS

**Konfigurace hybridního připojení Azure AD pomocí služby Azure AD Connect:**

1. Spusťte Azure AD Connect a pak vyberte **Konfigurovat**.

   ![Vítáme vás](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na stránce **Další úkoly** vyberte **Konfigurovat možnosti zařízení**a pak vyberte **Další**.

   ![Další úlohy](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na stránce **Přehled** vyberte **Další**.

   ![Přehled](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na stránce **Připojit k Azure AD** zadejte přihlašovací údaje globálního správce pro klienta Azure AD a pak vyberte **Další**.

   ![Připojení k Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na stránce **Možnosti zařízení** vyberte **Konfigurovat hybridní připojení Azure AD**a pak vyberte **Další**.

   ![Možnosti zařízení](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na stránce **SCP** proveďte následující kroky a pak vyberte **Další**:

   ![Spojovací bod služby](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Vyberte doménovou strukturu.
   1. Vyberte ověřovací službu. Server **Služby AD FS** musíte vybrat, pokud vaše organizace nemá výhradně klienty windows 10 a nenakonfigurovali jste synchronizaci počítače a zařízení nebo pokud vaše organizace nepoužívá bezproblémové jednotné přihlašování.
   1. Výběrem **možnosti Přidat** zadejte pověření správce rozlehlé sítě.

1. Na stránce **Operační systémy Zařízení** vyberte operační systémy, které používají zařízení v prostředí služby Active Directory, a pak vyberte **Další**.

   ![Operační systém zařízení](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na stránce **konfigurace federace** zadejte pověření správce služby AD FS a vyberte **další**.

   ![Konfigurace federace](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na stránce **Připraveno ke konfiguraci** vyberte **Konfigurovat**.

   ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na stránce **Dokončení konfigurace** vyberte **Ukončit**.

   ![Dokončení konfigurace](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Povolení zařízení nižší úrovně systému Windows

Pokud jsou některá zařízení připojená k doméně zařízení mj.

- Konfigurace nastavení místního intranetu pro registraci zařízení
- Instalace připojení k webu Microsoft Workplace Join pro počítače se systémem Windows

> [!NOTE]
> Podpora windows 7 skončila 14. ledna 2020. Další [informace, Podpora pro Windows 7 byla ukončena](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Chcete-li úspěšně dokončit hybridní připojení Azure AD zařízení nižší úrovně systému Windows a vyhnout se výzvám k certifikátu při ověřování zařízení ve službě Azure AD, můžete do zařízení spojených s doménou vytvořit zásadu a přidat následující adresy URL do místní zóny intranet v Internetu. Explorer:

- `https://device.login.microsoftonline.com`
- STS vaší organizace (pro federované domény)
- `https://autologon.microsoftazuread-sso.com`(Pro bezproblémové jednotné přihlašuje)

Musíte také povolit **povolit aktualizace stavového řádku prostřednictvím skriptu** v místní intranetové zóně uživatele.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instalace připojení k webu Microsoft Workplace Join pro počítače se systémem Windows

Chcete-li zaregistrovat zařízení nižší úrovně systému Windows, musí organizace nainstalovat [připojení k webu Microsoft Workplace Join pro počítače s jiným systémem než Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Připojení k webu Microsoft Workplace pro počítače s jiným i nese systémem Windows 10 je k dispozici v centru pro stahování Microsoft Download Center.

Balíček můžete nasadit pomocí systému distribuce softwaru, jako je [Microsoft Endpoint Configuration Manager](/configmgr/). Balíček podporuje standardní možnosti `quiet` tiché instalace s parametrem. Aktuální pobočka nástroje Configuration Manager nabízí výhody oproti dřívějším verzím, například možnost sledovat dokončené registrace.

Instalační program vytvoří naplánovanou úlohu v systému, která je spuštěna v uživatelském kontextu. Úloha se spustí, když se uživatel přihlásí k systému Windows. Úloha bezobslužně připojí zařízení s Azure AD pomocí přihlašovacích údajů uživatele po ověření pomocí Azure AD.

## <a name="verify-the-registration"></a>Ověření registrace

K ověření stavu registrace zařízení v tenantovi Azure můžete použít rutinu **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** v [modulu Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Při použití **rutiny Get-MSolDevice** ke kontrole podrobností o službě:

- Musí existovat objekt s **ID zařízení,** který odpovídá ID klienta systému Windows.
- Hodnota **DeviceTrustType** (Stav důvěryhodnosti zařízení) musí být nastavená na **Domain Joined** (Připojeno k doméně). Toto nastavení je ekvivalentní stavu **hybridní Azure AD připojil** v rámci **zařízení** na portálu Azure AD.
- Pro zařízení, která se používají v podmíněném přístupu, musí být hodnota **enabled** **true** a **DeviceTrustLevel** musí být **spravována**.

**Chcete-li zkontrolovat podrobnosti o službě**:

1. Spusťte Windows PowerShell jako správce.
1. Vstupem `Connect-MsolService` se můžete připojit ke svému tenantovi Azure.  
1. Zadejte `get-msoldevice -deviceId <deviceId>`.
1. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud máte problémy s dokončením hybridního připojení Azure AD pro zařízení s Windows připojená k doméně, přečtěte si:

- [Poradce při potížích s hybridním připojením Azure AD pro aktuální zařízení s Windows](troubleshoot-hybrid-join-windows-current.md)
- [Poradce při potížích s hybridním připojením Azure AD pro zařízení s nižší úrovní windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [spravovat identity zařízení pomocí portálu Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
