---
title: Konfigurace hybridního Azure Active Directory připojení ke spravovaným doménám | Microsoft Docs
description: Naučte se konfigurovat hybridní Azure Active Directory připojení ke spravovaným doménám.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4545e92767a427b8cd89af07ed4d06053685977a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578001"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Kurz: Konfigurace hybridního připojení k Azure Active Directory pro spravované domény

V tomto kurzu se naučíte konfigurovat službu Hybrid Azure Active Directory (Azure AD) JOIN pro zařízení připojená k doméně služby Active Directory. Tato metoda podporuje spravované prostředí, které zahrnuje místní službu Active Directory a Azure AD.

Podobně jako uživatel ve vaší organizaci je zařízení základní identitou, kterou chcete chránit. Identitu zařízení můžete použít k ochraně svých prostředků kdykoli a z libovolného místa. Tento cíl můžete dosáhnout správou identit zařízení ve službě Azure AD. Použijte jednu z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Tento článek se zaměřuje na hybridní připojení ke službě Azure AD.

Uvedení zařízení do Azure AD maximalizuje produktivitu uživatelů prostřednictvím jednotného přihlašování (SSO) napříč vaším cloudem a místními prostředky. Přístup k vašim cloudovým a místním prostředkům můžete zabezpečit současně pomocí [podmíněného přístupu](../conditional-access/howto-conditional-access-policy-compliant-device.md) .

Spravované prostředí můžete nasadit pomocí [synchronizace hodnot hash hesel (kosmetice)](../hybrid/whatis-phs.md) nebo [předávacího ověřování (PTA)](../hybrid/how-to-connect-pta.md) pomocí [bezproblémového jednotného přihlašování](../hybrid/how-to-connect-sso.md). Tyto scénáře nevyžadují konfiguraci federačního serveru pro ověřování.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolení zařízení s Windows nižší úrovně
> * Ověření připojených zařízení
> * Řešení potíží

## <a name="prerequisites"></a>Předpoklady

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 nebo novější)
- Přihlašovací údaje globálního správce pro vašeho tenanta Azure AD
- Přihlašovací údaje podnikového správce pro jednotlivé doménové struktury

Seznamte se s těmito články:

- [Co je identita zařízení?](overview.md)
- [Postupy: plánování implementace služby Hybrid Azure Active Directory JOIN](hybrid-azuread-join-plan.md)
- [Řízené ověřování hybridního připojení k Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD nepodporuje čipové karty ani certifikáty ve spravovaných doménách.

Ověřte, že Azure AD Connect synchronizoval objekty počítačů zařízení, která chcete mít k Azure AD připojená k hybridní službě Azure AD. Pokud objekty počítače patří konkrétním organizačním jednotkám (OU), nakonfigurujte organizační jednotky pro synchronizaci v Azure AD Connect. Další informace o tom, jak synchronizovat objekty počítačů pomocí Azure AD Connect, najdete v tématu [filtrování na základě organizační jednotky](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Abyste mohli úspěšně připojit synchronizaci zařízení, můžete jako součást konfigurace registrace zařízení vyloučit z konfigurace Azure AD Connect synchronizace výchozí atributy zařízení. Další informace o výchozích atributech zařízení synchronizovaných do AAD najdete v tématu [atributy synchronizované pomocí Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Počínaje verzí 1.1.819.0 Azure AD Connect obsahuje průvodce pro konfiguraci hybridního připojení k Azure AD. Průvodce významně zjednodušuje proces konfigurace. Průvodce nakonfiguruje spojovací body služby (SCP) pro registraci zařízení.

Kroky konfigurace v tomto článku jsou založené na používání Průvodce v Azure AD Connect.

Služba připojení k hybridní službě Azure AD vyžaduje, aby zařízení měla přístup k následujícím prostředkům Microsoftu z vaší organizace v síti:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Pokud používáte nebo plánujete používat bezproblémové přihlašování SSO)

> [!WARNING]
> Pokud vaše organizace používá proxy servery, které zachycují provoz SSL pro scénáře, jako je prevence ztráty dat nebo omezení tenanta Azure AD, zajistěte, aby byl provoz do ' https://device.login.microsoftonline.com ' vyloučený z přerušení TLS a prověřený. Vyloučení se nepovedlo https://device.login.microsoftonline.com , může způsobit rušení s ověřováním klientským certifikátem a způsobuje problémy s registrací zařízení a podmíněným přístupem na základě zařízení.

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím odchozího proxy serveru, můžete pomocí [implementace automatického zjišťování webových proxy serverů (WPAD)](/previous-versions/tn-archive/cc995261(v=technet.10)) povolit počítačům s Windows 10 registraci zařízení ve službě Azure AD. Problémy s konfigurací a správou protokolu WPAD najdete v tématu [řešení potíží při automatickém zjišťování](/previous-versions/tn-archive/cc302643(v=technet.10)). V zařízeních s Windows 10 starších než 1709 aktualizace je k dispozici jen možnost WPAD pro konfiguraci proxy serveru pro práci s hybridním připojením k Azure AD. 

Pokud službu WPAD nepoužíváte, můžete na svém počítači nakonfigurovat nastavení proxy serveru WinHTTP počínaje systémem Windows 10 1709. Další informace najdete v tématu [nastavení proxy WinHTTP nasazená objektem zásad skupiny](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Pokud nakonfigurujete nastavení proxy serveru na svém počítači pomocí nastavení WinHTTP, nepůjde se připojit k Internetu bez jakýchkoli počítačů, které se nemůžou připojit k nakonfigurovanému proxy serveru.

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, ujistěte se, že počítače s Windows 10 se můžou úspěšně ověřit u odchozího proxy serveru. Vzhledem k tomu, že počítače s Windows 10 spouští registraci zařízení pomocí kontextu počítače, nakonfigurujte ověřování odchozího proxy pomocí kontextu počítače. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

Ověřte, že zařízení má přístup k výše uvedeným prostředkům společnosti Microsoft pod účtem System pomocí skriptu pro [připojení k registraci zařízení](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Konfigurace hybridního připojení ke službě Azure AD pomocí Azure AD Connect:

1. Spusťte Azure AD Connect a pak vyberte **Konfigurovat**.

1. V **další úlohy** vyberte **Konfigurovat možnosti zařízení** a pak vyberte **Další**.

   ![Další úlohy](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. V **přehledu** vyberte **Další**.

1. V části **připojit k Azure AD** zadejte přihlašovací údaje globálního správce pro vašeho TENANTA Azure AD.  

1. V **Možnosti zařízení** vyberte **Konfigurovat hybridní připojení k Azure AD** a pak vyberte **Další**.

   ![Možnosti zařízení](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. V části **operační systémy zařízení** vyberte operační systémy, které zařízení v prostředí služby Active Directory používají, a pak vyberte **Další**.

   ![Operační systém zařízení](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. V části **Konfigurace spojovacího bodu** služby pro každou doménovou strukturu, ve které chcete Azure AD Connect nakonfigurovat SCP, proveďte následující kroky a pak vyberte **Další**.

   1. Vyberte **doménovou strukturu**.
   1. Vyberte **ověřovací službu**.
   1. Vyberte **Přidat** a zadejte přihlašovací údaje podnikového správce.

   ![Spojovací bod služby](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. V **Nastavení připraveno ke konfiguraci** vyberte **Konfigurovat**.

1. V **nastavení dokončeno** vyberte možnost **ukončit**.

## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá zařízení připojená k doméně zařízení se systémem Windows nižší úrovně, musíte:

- Konfigurace nastavení místního intranetu pro registraci zařízení
- Konfigurace bezproblémového jednotného přihlašování
- Nainstalovat Microsoft Workplace Join pro počítače nižší úrovně Windows

> [!NOTE]
> Podpora Windows 7 skončila 14. ledna 2020. Další informace najdete v tématu [Podpora Windows 7 skončila](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Pokud chcete dokončit připojení k hybridní službě Azure AD ze zařízení se systémem Windows nižší úrovně a vyhnout se jim výzvy při ověřování zařízení ve službě Azure AD, můžete do zařízení připojených k doméně přidat tyto adresy URL do zóny Místní intranet v Internet Exploreru:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Musíte taky povolit možnost **Povolit aktualizace stavového řádku prostřednictvím skriptu** v zóně místního intranetu uživatele.

### <a name="configure-seamless-sso"></a>Konfigurace bezproblémového jednotného přihlašování

Pokud chcete dokončit hybridní připojení k Azure AD ze zařízení se systémem Windows nižší úrovně ve spravované doméně, která jako metodu ověřování Azure AD používá [synchronizaci hodnot hash hesel](../hybrid/whatis-phs.md) nebo [předávací ověřování](../hybrid/how-to-connect-pta.md) , musíte taky [nakonfigurovat bezproblémové přihlašování](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Nainstalovat Microsoft Workplace Join pro počítače nižší úrovně Windows

Aby bylo možné zaregistrovat zařízení Windows nižší úrovně, musí organizace nainstalovat [Microsoft Workplace JOIN pro počítače s jiným systémem než Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join pro počítače s jiným systémem než Windows 10 je k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systému distribuce softwaru, jako je [Microsoft Endpoint Configuration Manager](/configmgr/). Balíček podporuje standardní možnosti bezobslužné instalace s `quiet` parametrem. Aktuální verze Configuration Manager nabízí výhody oproti starším verzím, jako je schopnost sledovat dokončené registrace.

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
3. Pokud **zaregistrovaný** sloupec **čeká na vyřízení**, připojení k hybridní službě Azure AD se nedokončilo.
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

Pokud dochází k problémům s dokončováním hybridního připojení služby Azure AD pro zařízení s Windows připojená k doméně, přečtěte si téma:

- [Řešení potíží se zařízeními pomocí příkazu dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Řešení potíží se zařízeními připojenými službou Hybrid Azure Active Directory Join](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s modulem hybridní Azure Active Directory připojená zařízení nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a Naučte se spravovat identity zařízení pomocí Azure Portal.
> [!div class="nextstepaction"]
> [Správa identit zařízení](device-management-azure-portal.md)
