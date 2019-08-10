---
title: Konfigurace hybridních Azure Active Directory připojení k federovaným doménám | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat službu Hybrid Azure Active Directory JOIN pro federované domény.
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
ms.openlocfilehash: de9b50267e0b5c453a8f2a3df607ad7730acd277
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879439"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Kurz: Konfigurace hybridních Azure Active Directory připojení k federovaným doménám

Podobně jako uživatel ve vaší organizaci je zařízení základní identitou, kterou chcete chránit. Identitu zařízení můžete použít k ochraně svých prostředků kdykoli a z libovolného místa. Tento cíl můžete dosáhnout tím, že navedete identity zařízení a spravujete je v Azure Active Directory (Azure AD) pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Uvedení zařízení do Azure AD maximalizuje produktivitu uživatelů prostřednictvím jednotného přihlašování (SSO) napříč vaším cloudem a místními prostředky. Přístup k vašim cloudovým a místním prostředkům můžete zabezpečit současně pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) .

Federované prostředí by mělo mít poskytovatele identity, který podporuje následující požadavky. Pokud máte federované prostředí pomocí Active Directory Federation Services (AD FS) (AD FS), jsou již podporovány níže uvedené požadavky.

- **WIAORMULTIAUTHN deklarace identity:** Tato deklarace identity se vyžaduje k tomu, aby se pro zařízení se systémem Windows na nižší úrovni mohla připojit hybridní služba Azure AD.
- **Protokol WS-Trust:** Tento protokol je nutný k ověření současných zařízení s Windows připojených k hybridní službě Azure AD pomocí Azure AD.
  Pokud používáte AD FS, je nutné povolit následující koncové body WS-Trust:`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **AD FS/Services/Trust/2005/windowstransport** , **AD FS/Services/Trust/13/windowstransport** by měly být povolené jenom jako intranetové koncové body a nesmí být zveřejněné jako extranetové koncové body prostřednictvím proxy webových aplikací. Další informace o tom, jak zakázat koncové body systému WIndows WS-Trust, najdete v tématu [zakázání koncových bodů systému Windows WS-Trust na proxy serveru](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pomocí konzoly pro správu AD FS v části**koncové body** **služby** > můžete zjistit, jaké koncové body jsou povolené.

V tomto kurzu se naučíte konfigurovat hybridní připojení služby Azure AD pro počítače připojené k doméně služby Active Directory ve federovaném prostředí pomocí AD FS.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolit zařízení se starší verzí Windows
> * Ověření registrace
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že you'e znají tyto články:

- [Co je identita zařízení?](overview.md)
- [Jak naplánovat vaši hybridní implementaci služby Azure AD JOIN](hybrid-azuread-join-plan.md)
- [Postup při řízení ověřování hybridního připojení ke službě Azure AD](hybrid-azuread-join-control.md)

Ke konfiguraci scénáře v tomto kurzu budete potřebovat:

- Windows Server 2012 R2 se službou AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) verze 1.1.819.0 nebo novější

Počínaje verzí 1.1.819.0 Azure AD Connect obsahuje průvodce, který můžete použít ke konfiguraci hybridního připojení k Azure AD. Průvodce významně zjednodušuje proces konfigurace. Související průvodce:

- Nakonfiguruje spojovací body služby (SCP) pro registraci zařízení.
- Zálohuje existující vztah důvěryhodnosti přijímající strany Azure AD.
- Aktualizuje pravidla deklarace identity ve vztahu důvěryhodnosti Azure AD.

Kroky konfigurace v tomto článku jsou založené na používání Průvodce Azure AD Connect. Pokud máte nainstalovanou starší verzi Azure AD Connect, musíte ji upgradovat na 1.1.819 nebo novější, abyste mohli průvodce použít. Pokud se instalace nejnovější verze Azure AD Connect pro vás nepoužívá, přečtěte si téma [Postup ruční konfigurace hybridního připojení k Azure AD](hybrid-azuread-join-manual.md).

Služba připojení k hybridní službě Azure AD vyžaduje, aby zařízení měla přístup k následujícím prostředkům Microsoftu z vaší organizace v síti:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Služba tokenů zabezpečení vaší organizace (STS) (pro federované domény)
- `https://autologon.microsoftazuread-sso.com`(Pokud používáte nebo plánujete používat bezproblémové přihlašování SSO)

Od verze Windows 10 1803 se při spuštění služby hybridního připojení služby Azure AD pro federované prostředí pomocí AD FS nespoléháme na Azure AD Connect synchronizaci objektu počítače v Azure AD, který se následně používá k dokončení registrace zařízení pro hybridní Azure. Spojení AD. Ověřte, že Azure AD Connect synchronizoval objekty počítačů zařízení, která chcete mít k Azure AD připojená k hybridní službě Azure AD. Pokud objekty počítače patří konkrétním organizačním jednotkám (OU), musíte také nakonfigurovat organizační jednotky pro synchronizaci v Azure AD Connect. Další informace o tom, jak synchronizovat objekty počítačů pomocí Azure AD Connect, najdete v tématu [Konfigurace filtrování pomocí Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím odchozího proxy serveru, doporučuje Microsoft [implementovat automatické zjišťování webových proxy serverů (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) a povolit tak počítačům s Windows 10 registraci zařízení ve službě Azure AD. Pokud narazíte na problémy s konfigurací a správou WPAD, přečtěte si téma [řešení potíží s automatickým](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)) 

Pokud nepoužíváte WPAD a chcete v počítači nakonfigurovat nastavení proxy serveru, můžete tak učinit od Windows 10 1709. Další informace najdete v tématu [Konfigurace nastavení WinHTTP pomocí objektu zásad skupiny (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Pokud nakonfigurujete nastavení proxy serveru na svém počítači pomocí nastavení WinHTTP, nepůjde se připojit k Internetu bez jakýchkoli počítačů, které se nemůžou připojit k nakonfigurovanému proxy serveru.

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, musíte se ujistit, že počítače s Windows 10 se můžou úspěšně ověřit u odchozího proxy serveru. Vzhledem k tomu, že počítače s Windows 10 spouští registraci zařízení pomocí kontextu počítače, musíte nakonfigurovat ověřování odchozího proxy serveru pomocí kontextu počítače. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

Pokud chcete ověřit, jestli má zařízení přístup k výše uvedeným prostředkům Microsoftu pod účtem System, můžete použít skript pro [připojení k registraci testovacího zařízení](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Ke konfiguraci hybridního připojení k Azure AD pomocí Azure AD Connect budete potřebovat:

- Přihlašovací údaje globálního správce pro vašeho tenanta Azure AD  
- Přihlašovací údaje podnikového správce pro jednotlivé doménové struktury
- Přihlašovací údaje správce AD FS

**Konfigurace hybridního připojení ke službě Azure AD pomocí Azure AD Connect**:

1. Spusťte Azure AD Connect a pak vyberte **Konfigurovat**.

   ![Vítej](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na stránce **další úlohy** vyberte **Konfigurovat možnosti zařízení**a pak vyberte **Další**.

   ![Další úkoly](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na stránce **Přehled** vyberte **Další**.

   ![Přehled](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na stránce **připojit ke službě Azure AD** zadejte přihlašovací údaje globálního správce pro vašeho TENANTA Azure AD a pak vyberte **Další**.

   ![Připojení ke službě Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na stránce **Možnosti zařízení** vyberte **Konfigurovat hybridní připojení k Azure AD**a pak vyberte **Další**.

   ![Možnosti zařízení](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na stránce **spojovací bod** služby proveďte následující kroky a potom vyberte **Další**:

   ![Spojovací bod služby](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Vyberte doménovou strukturu.
   1. Vyberte ověřovací službu. Musíte vybrat **AD FS Server** , pokud vaše organizace nemá výhradně klienty s Windows 10 a Vy jste nakonfigurovali synchronizaci počítačů a zařízení, nebo vaše organizace používá bezproblémové jednotné přihlašování.
   1. Vyberte **Přidat** a zadejte přihlašovací údaje podnikového správce.

1. Na stránce **operační systémy zařízení** vyberte operační systémy, které zařízení v prostředí služby Active Directory používají, a pak vyberte **Další**.

   ![Operační systém zařízení](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na stránce **Konfigurace federace** zadejte přihlašovací údaje správce AD FS a pak vyberte **Další**.

   ![Konfigurace federace](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na stránce **připraveno ke konfiguraci** vyberte **Konfigurovat**.

   ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na stránce **Konfigurace byla dokončena** vyberte možnost **ukončit**.

   ![Dokončení konfigurace](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Povolit zařízení se starší verzí Windows

Pokud jsou některá zařízení připojená k doméně zařízení se starší verzí Windows, musíte:

- Konfigurace nastavení místního intranetu pro registraci zařízení
- Nainstalovat Microsoft Workplace Join pro počítače se starší verzí Windows

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Aby bylo možné úspěšně dokončit připojení hybridní služby Azure AD k zařízením se starší verzí Windows a vyhnout se zobrazování výzev k certifikátu při ověřování zařízení ve službě Azure AD, můžete do zařízení připojených k doméně přidat tyto adresy URL do zóny Místní intranet v Internetu. Prohlížeč

- `https://device.login.microsoftonline.com`
- STS vaší organizace (pro federované domény)
- `https://autologon.microsoftazuread-sso.com`(Pro bezproblémové jednotné přihlašování)

Musíte taky povolit možnost **Povolit aktualizace stavového řádku prostřednictvím skriptu** v zóně místního intranetu uživatele.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Nainstalovat Microsoft Workplace Join pro počítače se starší verzí Windows

Aby bylo možné zaregistrovat zařízení se starší verzí Windows, musí organizace nainstalovat [Microsoft Workplace JOIN pro počítače s jiným systémem než Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join pro počítače s jiným systémem než Windows 10 je k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systému distribuce softwaru, jako je [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Balíček podporuje standardní možnosti bezobslužné instalace s `quiet` parametrem. Aktuální větev Configuration Manager nabízí výhody oproti starším verzím, jako je schopnost sledovat dokončené registrace.

Instalační program vytvoří v systému naplánovanou úlohu, která běží v uživatelském kontextu. Úkol se aktivuje, když se uživatel přihlásí k Windows. Úloha se tiše připojí k zařízení pomocí Azure AD s použitím přihlašovacích údajů uživatele po ověření pomocí Azure AD.

## <a name="verify-the-registration"></a>Ověření registrace

Pokud chcete ověřit stav registrace zařízení ve vašem tenantovi Azure, můžete použít rutinu **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** v [modulu Azure Active Directory PowerShellu](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Při kontrole podrobností služby použijte rutinu **Get-MSolDevice** :

- Musí existovat objekt s **ID zařízení** , které odpovídá ID na klientském počítači se systémem Windows.
- Hodnota **DeviceTrustType** (Stav důvěryhodnosti zařízení) musí být nastavená na **Domain Joined** (Připojeno k doméně). Toto nastavení se rovná stavu připojenému k **hybridní službě Azure AD** v části **zařízení** na portálu Azure AD.
- U zařízení, která se používají v podmíněném přístupu, musí být povolená hodnota **true** a musí se **DeviceTrustLevel** **Spravovat**.

**Postup kontroly podrobností služby**:

1. Otevřete Windows PowerShell jako správce.
1. Zadejte `Connect-MsolService` , abyste se připojili k vašemu tenantovi Azure.  
1. Zadejte `get-msoldevice -deviceId <deviceId>`.
1. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud dochází k problémům s dokončením hybridního připojení služby Azure AD pro zařízení s Windows připojená k doméně, přečtěte si téma:

- [Řešení potíží s hybridním připojením ke službě Azure AD pro aktuální zařízení s Windows](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridním připojením ke službě Azure AD pro zařízení se starší verzí Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další postup

Naučte se [Spravovat identity zařízení pomocí Azure Portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
