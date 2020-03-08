---
title: Konfigurace hybridního Azure Active Directory připojení ke spravovaným doménám | Microsoft Docs
description: Naučte se konfigurovat hybridní Azure Active Directory připojení ke spravovaným doménám.
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
ms.openlocfilehash: 3407214d332cbd333fe019948d254e01d71197fb
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672278"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Kurz: Konfigurace hybridního připojení k Azure Active Directory pro spravované domény

Podobně jako uživatel ve vaší organizaci je zařízení základní identitou, kterou chcete chránit. Identitu zařízení můžete použít k ochraně svých prostředků kdykoli a z libovolného místa. Tento cíl můžete dosáhnout tím, že navedete identity zařízení a spravujete je v Azure Active Directory (Azure AD) pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Uvedení zařízení do Azure AD maximalizuje produktivitu uživatelů prostřednictvím jednotného přihlašování (SSO) napříč vaším cloudem a místními prostředky. Přístup k vašim cloudovým a místním prostředkům můžete zabezpečit současně pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) .

V tomto kurzu se naučíte konfigurovat hybridní připojení služby Azure AD pro zařízení počítačů připojená k doméně služby Active Directory ve spravovaném prostředí. 

Spravované prostředí se dá nasadit buď pomocí [synchronizace hodnot hash hesel (kosmetice)](../hybrid/whatis-phs.md) , nebo [předávacího ověřování (PTA)](../hybrid/how-to-connect-pta.md) s [bezproblémovém jednotným přihlašováním](../hybrid/how-to-connect-sso.md). Tyto scénáře nevyžadují konfiguraci federačního serveru pro ověřování.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolení zařízení s Windows nižší úrovně
> * Ověření připojených zařízení
> * Řešení potíží

## <a name="prerequisites"></a>Předpoklady

V tomto kurzu se předpokládá, že máte zkušenosti s těmito články:

- [Co je identita zařízení?](overview.md)
- [Jak naplánovat vaši hybridní implementaci služby Azure AD JOIN](hybrid-azuread-join-plan.md)
- [Postup při řízení ověřování hybridního připojení ke službě Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD nepodporuje čipové karty ani certifikáty ve spravovaných doménách.

Pokud chcete nakonfigurovat scénář v tomto článku, potřebujete nainstalovanou [nejnovější verzi Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 nebo novější).

Ověřte, že Azure AD Connect synchronizoval objekty počítačů zařízení, která chcete mít k Azure AD připojená k hybridní službě Azure AD. Pokud objekty počítače patří konkrétním organizačním jednotkám (OU), musíte také nakonfigurovat organizační jednotky pro synchronizaci v Azure AD Connect. Další informace o tom, jak synchronizovat objekty počítačů pomocí Azure AD Connect, najdete v tématu [Konfigurace filtrování pomocí Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Počínaje verzí 1.1.819.0 Azure AD Connect obsahuje průvodce, který můžete použít ke konfiguraci hybridního připojení k Azure AD. Průvodce významně zjednodušuje proces konfigurace. Průvodce nakonfiguruje spojovací body služby (SCP) pro registraci zařízení.

Kroky konfigurace v tomto článku jsou založené na používání Průvodce v Azure AD Connect.

Služba připojení k hybridní službě Azure AD vyžaduje, aby zařízení měla přístup k následujícím prostředkům Microsoftu z vaší organizace v síti:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Pokud používáte nebo plánujete používat bezproblémové přihlašování SSO)

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím odchozího proxy serveru, doporučuje Microsoft [implementovat automatické zjišťování webových proxy serverů (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) a povolit tak počítačům s Windows 10 registraci zařízení ve službě Azure AD. Pokud narazíte na problémy s konfigurací a správou WPAD, přečtěte si téma [řešení potíží s automatickým](/previous-versions/tn-archive/cc302643(v=technet.10)) 

Pokud nepoužíváte protokol WPAD a potřebujete nakonfigurovat nastavení proxy serveru v počítači, můžete tak učinit od Windows 10 1709. Další informace najdete v tématu [Konfigurace nastavení WinHTTP pomocí objektu zásad skupiny (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Pokud nakonfigurujete nastavení proxy serveru na svém počítači pomocí nastavení WinHTTP, nepůjde se připojit k Internetu bez jakýchkoli počítačů, které se nemůžou připojit k nakonfigurovanému proxy serveru.

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, musíte se ujistit, že počítače s Windows 10 se můžou úspěšně ověřit u odchozího proxy serveru. Vzhledem k tomu, že počítače s Windows 10 spouští registraci zařízení pomocí kontextu počítače, musíte nakonfigurovat ověřování odchozího proxy serveru pomocí kontextu počítače. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

Pokud chcete ověřit, jestli má zařízení přístup k výše uvedeným prostředkům Microsoftu pod účtem System, můžete použít skript pro [připojení k registraci testovacího zařízení](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Ke konfiguraci hybridního připojení k Azure AD pomocí Azure AD Connect potřebujete:

- Přihlašovací údaje globálního správce pro vašeho tenanta Azure AD
- Přihlašovací údaje podnikového správce pro jednotlivé doménové struktury

**Konfigurace hybridního připojení ke službě Azure AD pomocí Azure AD Connect:**

1. Spusťte Azure AD Connect a pak vyberte **Konfigurovat**.

   ![Uvítání](./media/hybrid-azuread-join-managed-domains/11.png)

1. Na stránce **další úlohy** vyberte **Konfigurovat možnosti zařízení**a pak vyberte **Další**.

   ![Další úlohy](./media/hybrid-azuread-join-managed-domains/12.png)

1. Na stránce **Přehled** vyberte **Další**.

   ![Přehled](./media/hybrid-azuread-join-managed-domains/13.png)

1. Na stránce **Připojit k Azure AD** zadejte přihlašovací údaje globálního správce vašeho tenanta Azure AD.  

   ![Připojení k Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. Na stránce **Možnosti zařízení** vyberte **Konfigurovat hybridní připojení k Azure AD**a pak vyberte **Další**.

   ![Možnosti zařízení](./media/hybrid-azuread-join-managed-domains/15.png)

1. Na stránce **SCP** pro každou doménovou strukturu, ve které chcete Azure AD Connect nakonfigurovat spojovací bod služby, proveďte následující kroky a potom vyberte **Další**:

   ![Spojovací bod služby](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Vyberte doménovou strukturu.
   1. Vyberte ověřovací službu.
   1. Vyberte **Přidat** a zadejte přihlašovací údaje podnikového správce.

1. Na stránce **operační systémy zařízení** vyberte operační systémy, které zařízení v prostředí služby Active Directory používají, a pak vyberte **Další**.

   ![Operační systém zařízení](./media/hybrid-azuread-join-managed-domains/17.png)

1. Na stránce **připraveno ke konfiguraci** vyberte **Konfigurovat**.

   ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-managed-domains/19.png)

1. Na stránce **Konfigurace byla dokončena** vyberte možnost **ukončit**.

   ![Dokončení konfigurace](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Povolit zařízení se starší verzí Windows

Pokud jsou některá zařízení připojená k doméně zařízení se starší verzí Windows, musíte:

- Konfigurace nastavení místního intranetu pro registraci zařízení
- Konfigurace bezproblémového jednotného přihlašování
- Nainstalovat Microsoft Workplace Join pro počítače se starší verzí Windows

> [!NOTE]
> Podpora Windows 7 skončila 14. ledna 2020. Další informace najdete v případě, [že podpora pro Windows 7 skončila](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Aby bylo možné úspěšně dokončit připojení hybridní služby Azure AD k zařízením se starší verzí Windows a vyhnout se zobrazování výzev k certifikátu při ověřování zařízení ve službě Azure AD, můžete do zařízení připojených k doméně přidat tyto adresy URL do zóny Místní intranet v Internetu. Prohlížeč

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Musíte taky povolit možnost **Povolit aktualizace stavového řádku prostřednictvím skriptu** v zóně místního intranetu uživatele.

### <a name="configure-seamless-sso"></a>Konfigurace bezproblémového jednotného přihlašování

K úspěšnému dokončení připojení hybridního služby Azure AD ze zařízení se starší verzí Windows ve spravované doméně, která jako metodu ověřování Azure AD používá [kosmetice](../hybrid/whatis-phs.md) nebo [PTA](../hybrid/how-to-connect-pta.md) , musíte taky [nakonfigurovat bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Nainstalovat Microsoft Workplace Join pro počítače se starší verzí Windows

Aby bylo možné zaregistrovat zařízení se starší verzí Windows, musí organizace nainstalovat [Microsoft Workplace JOIN pro počítače s jiným systémem než Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join pro počítače s jiným systémem než Windows 10 je k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systému distribuce softwaru, jako je [Microsoft Endpoint Configuration Manager](/configmgr/). Balíček podporuje standardní možnosti bezobslužné instalace s parametrem `quiet`. Aktuální větev Configuration Manager nabízí výhody oproti starším verzím, jako je schopnost sledovat dokončené registrace.

Instalační program vytvoří v systému naplánovanou úlohu, která běží v uživatelském kontextu. Úkol se aktivuje, když se uživatel přihlásí k Windows. Úloha se tiše připojí k zařízení pomocí Azure AD s použitím přihlašovacích údajů uživatele po ověření pomocí Azure AD.

## <a name="verify-the-registration"></a>Ověření registrace

Pokud chcete ověřit stav registrace zařízení ve vašem tenantovi Azure, můžete použít rutinu **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** v [modulu Azure Active Directory PowerShellu](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Při kontrole podrobností služby použijte rutinu **Get-MSolDevice** :

- Musí existovat objekt s **ID zařízení** , které odpovídá ID na klientském počítači se systémem Windows.
- Hodnota **DeviceTrustType** (Stav důvěryhodnosti zařízení) musí být nastavená na **Domain Joined** (Připojeno k doméně). Toto nastavení se rovná stavu **připojenému k hybridní službě Azure AD** na stránce **zařízení** na portálu Azure AD.
- U zařízení, která se používají v podmíněném přístupu, musí být **povolená** hodnota **true** a musí se **DeviceTrustLevel** **Spravovat**.

**Postup kontroly podrobností služby**:

1. Otevřete Windows PowerShell jako správce.
1. Zadejte `Connect-MsolService` pro připojení k vašemu tenantovi Azure.  
1. Zadejte `get-msoldevice -deviceId <deviceId>`.
1. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud dochází k problémům s dokončením hybridního připojení služby Azure AD pro zařízení s Windows připojená k doméně, přečtěte si téma:

- [Řešení potíží s hybridním připojením ke službě Azure AD pro aktuální zařízení s Windows](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridním připojením ke službě Azure AD pro zařízení se starší verzí Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

Naučte se [Spravovat identity zařízení pomocí Azure Portal](device-management-azure-portal.md).
