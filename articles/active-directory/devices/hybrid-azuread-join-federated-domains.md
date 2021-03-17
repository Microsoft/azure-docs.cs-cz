---
title: Konfigurace hybridních Azure Active Directory připojení k federovaným doménám | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat službu Hybrid Azure Active Directory JOIN pro federované domény.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2a455e1ee6f8f714cf50ebdf6a59dab568489ca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646295"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Kurz: Konfigurace hybridního připojení k Azure Active Directory pro federované domény

Podobně jako uživatel ve vaší organizaci je zařízení základní identitou, kterou chcete chránit. Identitu zařízení můžete použít k ochraně svých prostředků kdykoli a z libovolného místa. Tento cíl můžete dosáhnout tím, že navedete identity zařízení a spravujete je v Azure Active Directory (Azure AD) pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Uvedení zařízení do Azure AD maximalizuje produktivitu uživatelů prostřednictvím jednotného přihlašování (SSO) napříč vaším cloudem a místními prostředky. Přístup k vašim cloudovým a místním prostředkům můžete zabezpečit současně pomocí [podmíněného přístupu](../conditional-access/howto-conditional-access-policy-compliant-device.md) .

Federované prostředí by mělo mít poskytovatele identity, který podporuje následující požadavky. Pokud máte federované prostředí pomocí Active Directory Federation Services (AD FS) (AD FS), jsou již podporovány níže uvedené požadavky.

- **WIAORMULTIAUTHN deklarace identity:** Tato deklarace identity se vyžaduje k tomu, aby se pro zařízení se systémem Windows na nižší úrovni mohla připojit hybridní služba Azure AD.
- **Protokol WS-Trust:** Tento protokol je nutný k ověření současných zařízení s Windows připojených k hybridní službě Azure AD pomocí Azure AD.
  Pokud používáte AD FS, je nutné povolit následující WS-Trust koncové body: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **AD FS/Services/Trust/2005/windowstransport** a **AD FS/Services/Trust/13/windowstransport** by měly být povolené jenom jako intranetové koncové body a nesmí být zveřejněné jako extranetové koncové body prostřednictvím proxy webových aplikací. Další informace o tom, jak zakázat WS-Trust koncové body Windows, najdete v tématu [zakázání WS-Trust koncových bodů Windows na proxy serveru](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pomocí konzoly pro správu AD FS v části   >  **koncové body** služby můžete zjistit, jaké koncové body jsou povolené.

V tomto kurzu se naučíte konfigurovat hybridní připojení služby Azure AD pro počítače připojené k doméně služby Active Directory ve federovaném prostředí pomocí AD FS.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolit zařízení se starší verzí Windows
> * Ověření registrace
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že máte zkušenosti s těmito články:

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
- `https://autologon.microsoftazuread-sso.com` (Pokud používáte nebo plánujete používat bezproblémové přihlašování SSO)

> [!WARNING]
> Pokud vaše organizace používá proxy servery, které zachycují provoz SSL pro scénáře, jako je prevence ztráty dat nebo omezení tenanta Azure AD, zajistěte, aby byl provoz do ' https://device.login.microsoftonline.com ' vyloučený z přerušení TLS a prověřený. Vyloučení se nepovedlo https://device.login.microsoftonline.com , může způsobit rušení s ověřováním klientským certifikátem a způsobuje problémy s registrací zařízení a podmíněným přístupem na základě zařízení.

Od verze Windows 10 1803, pokud se okamžité připojení hybridní služby Azure AD pro federované prostředí pomocí AD FS nepovede, spoléháme na Azure AD Connect synchronizaci objektu počítače v Azure AD, který se následně používá k dokončení registrace zařízení pro připojení k hybridní službě Azure AD. Ověřte, že Azure AD Connect synchronizoval objekty počítačů zařízení, která chcete mít k Azure AD připojená k hybridní službě Azure AD. Pokud objekty počítače patří konkrétním organizačním jednotkám (OU), musíte také nakonfigurovat organizační jednotky pro synchronizaci v Azure AD Connect. Další informace o tom, jak synchronizovat objekty počítačů pomocí Azure AD Connect, najdete v tématu [Konfigurace filtrování pomocí Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím odchozího proxy serveru, doporučuje Microsoft [implementovat automatické zjišťování webových proxy serverů (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) a povolit tak počítačům s Windows 10 registraci zařízení ve službě Azure AD. Pokud narazíte na problémy s konfigurací a správou WPAD, přečtěte si téma [řešení potíží s automatickým](/previous-versions/tn-archive/cc302643(v=technet.10)) 

Pokud nepoužíváte WPAD a chcete v počítači nakonfigurovat nastavení proxy serveru, můžete tak učinit od Windows 10 1709. Další informace najdete v tématu [Konfigurace nastavení WinHTTP pomocí objektu zásad skupiny (GPO)](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Pokud nakonfigurujete nastavení proxy serveru na svém počítači pomocí nastavení WinHTTP, nepůjde se připojit k Internetu bez jakýchkoli počítačů, které se nemůžou připojit k nakonfigurovanému proxy serveru.

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, musíte se ujistit, že počítače s Windows 10 se můžou úspěšně ověřit u odchozího proxy serveru. Vzhledem k tomu, že počítače s Windows 10 spouští registraci zařízení pomocí kontextu počítače, musíte nakonfigurovat ověřování odchozího proxy serveru pomocí kontextu počítače. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

Pokud chcete ověřit, jestli má zařízení přístup k výše uvedeným prostředkům Microsoftu pod účtem System, můžete použít skript pro [připojení k registraci testovacího zařízení](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Ke konfiguraci hybridního připojení k Azure AD pomocí Azure AD Connect budete potřebovat:

- Přihlašovací údaje globálního správce pro vašeho tenanta Azure AD  
- Přihlašovací údaje podnikového správce pro jednotlivé doménové struktury
- Přihlašovací údaje správce AD FS

**Konfigurace hybridního připojení ke službě Azure AD pomocí Azure AD Connect**:

1. Spusťte Azure AD Connect a pak vyberte **Konfigurovat**.

   ![Vítáme vás](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na stránce **další úlohy** vyberte **Konfigurovat možnosti zařízení** a pak vyberte **Další**.

   ![Další úlohy](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na stránce **Přehled** vyberte **Další**.

   ![Přehled](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na stránce **připojit ke službě Azure AD** zadejte přihlašovací údaje globálního správce pro vašeho TENANTA Azure AD a pak vyberte **Další**.

   ![Připojení k Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na stránce **Možnosti zařízení** vyberte **Konfigurovat hybridní připojení k Azure AD** a pak vyberte **Další**.

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

> [!NOTE]
> Podpora Windows 7 skončila 14. ledna 2020. Další informace najdete v případě, [že podpora pro Windows 7 skončila](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Aby bylo možné úspěšně dokončit připojení hybridní služby Azure AD k zařízením se starší verzí Windows a vyhnout se zobrazování výzev k certifikátu při ověřování zařízení ve službě Azure AD, můžete do zařízení připojených k doméně přidat tyto adresy URL do zóny Místní intranet v Internet Exploreru:

- `https://device.login.microsoftonline.com`
- STS vaší organizace (pro federované domény)
- `https://autologon.microsoftazuread-sso.com` (Pro bezproblémové jednotné přihlašování)

Musíte taky povolit možnost **Povolit aktualizace stavového řádku prostřednictvím skriptu** v zóně místního intranetu uživatele.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Nainstalovat Microsoft Workplace Join pro počítače se starší verzí Windows

Aby bylo možné zaregistrovat zařízení se starší verzí Windows, musí organizace nainstalovat [Microsoft Workplace JOIN pro počítače s jiným systémem než Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join pro počítače s jiným systémem než Windows 10 je k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systému distribuce softwaru, jako je [Microsoft Endpoint Configuration Manager](/configmgr/). Balíček podporuje standardní možnosti bezobslužné instalace s `quiet` parametrem. Aktuální větev Configuration Manager nabízí výhody oproti starším verzím, jako je schopnost sledovat dokončené registrace.

Instalační program vytvoří v systému naplánovanou úlohu, která běží v uživatelském kontextu. Úkol se aktivuje, když se uživatel přihlásí k Windows. Úloha se tiše připojí k zařízení pomocí Azure AD s použitím přihlašovacích údajů uživatele po ověření pomocí Azure AD.

## <a name="verify-the-registration"></a>Ověření registrace

Tady jsou tři způsoby, jak vyhledat a ověřit stav zařízení:

### <a name="locally-on-the-device"></a>Místně na zařízení

1. Otevřete Windows PowerShell.
2. Zadejte `dsregcmd /status`.
3. Ověřte, že jsou **AzureAdJoined** i **DomainJoined** nastavené na **Ano**.
4. Můžete použít **DeviceID** a porovnat stav služby pomocí Azure Portal nebo PowerShellu.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

1. V případě, že přejdete na stránku zařízení, použijte [přímý odkaz](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informace o tom, jak najít zařízení, najdete v tématu [Správa identit zařízení pomocí Azure Portal](./device-management-azure-portal.md).
3. Pokud **zaregistrovaný** sloupec **čeká na vyřízení**, připojení k hybridní službě Azure AD se nedokončilo. Ve federovaném prostředí se to může stát jenom v případě, že se nepovedlo zaregistrovat a AAD Connect je nakonfigurované k synchronizaci zařízení.
4. Pokud **zaregistrovaný** sloupec obsahuje **Datum a čas**, připojení k hybridní službě Azure AD se dokončilo.

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

Ověřte stav registrace zařízení v tenantovi Azure pomocí **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)**. Tato rutina je v [modulu Azure Active Directory PowerShellu](/powershell/azure/active-directory/install-msonlinev1).

Při kontrole podrobností služby použijte rutinu **Get-MSolDevice** :

- Musí existovat objekt s **ID zařízení** , které odpovídá ID na klientském počítači se systémem Windows.
- Hodnota pro **DeviceTrustType** je **připojená k doméně**. Toto nastavení se rovná stavu **připojenému k hybridní službě Azure AD** na stránce **zařízení** na portálu Azure AD.
- U zařízení, která se používají v podmíněném přístupu, je **povolená** hodnota **true** a **DeviceTrustLevel** je **spravovaná**.

1. Spusťte Windows PowerShell jako správce.
2. Zadejte `Connect-MsolService` , abyste se připojili k vašemu Tenantovi Azure.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Spočítat všechna hybridní zařízení připojená k Azure AD (s výjimkou stavu **čekání** )

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Spočítat všechna zařízení připojená k hybridní službě Azure AD ve stavu **čekání**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Zobrazit všechna zařízení připojená k hybridní službě Azure AD

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Vypsat všechna zařízení připojená k hybridní službě Azure AD s **probíhajícím** stavem

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Seznam podrobností o jednom zařízení:

1. Zadejte `get-msoldevice -deviceId <deviceId>` (Jedná se o **DeviceID** získanou místně na zařízení).
2. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud dochází k problémům s dokončením hybridního připojení služby Azure AD pro zařízení s Windows připojená k doméně, přečtěte si téma:

- [Řešení potíží se zařízeními pomocí příkazu dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Řešení potíží s hybridním připojením ke službě Azure AD pro aktuální zařízení s Windows](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridním připojením ke službě Azure AD pro zařízení se starší verzí Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

Naučte se [Spravovat identity zařízení pomocí Azure Portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png