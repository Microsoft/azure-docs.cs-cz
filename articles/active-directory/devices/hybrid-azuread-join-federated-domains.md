---
title: Konfigurace připojení k hybridní službě Azure Active Directory službě u federovaných domén | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat připojení k hybridní službě Azure Active Directory službě u federovaných domén.
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
ms.openlocfilehash: ae4b57d86461526b285e77aa408373b5d7f5aedf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513366"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Kurz: Konfigurace připojení k hybridní službě Azure Active Directory službě u federovaných domén

Podobným způsobem pro uživatele zařízení je jiné identity jádro, které chcete chránit a použít ji k ochraně vašich prostředků, kdykoli a odkudkoli. Dosažení tohoto cíle přináší a správou identit zařízení ve službě Azure AD pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Přenosem zařízení do Azure AD maximalizujete produktivitu uživatelů díky jednotnému přihlašování ke cloudovým i místním prostředkům. Současně můžete zabezpečit přístup k vašim cloudovým i místním prostředkům s využitím [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md).

V tomto kurzu se dozvíte, jak nakonfigurovat připojení k hybridní službě Azure AD pro zařízení pro počítače připojené k doméně AD ve federovaném prostředí pomocí služby AD FS.

> [!NOTE]
> Pokud vaše prostředí federované používá zprostředkovatel identity než služba AD FS, je potřeba zajistit, že váš poskytovatel identit podporuje protokol WS-Trust. WS-Trust, je potřeba ověřit váš Windows aktuální hybridní službě Azure AD připojené zařízení s Azure AD. Kromě toho pokud máte Windows nižší úrovně zařízení, které potřebujete k hybridní službě Azure AD join, zprostředkovatele identity potřebovat pro podporu deklarací identity WIAORMULTIAUTHN. 


> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolení zařízení s Windows nižší úrovně
> * Ověření registrace
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá znalost následujících témat:

- [Seznámení se správou identit zařízení ve službě Azure Active Directory](../device-management-introduction.md)
- [Plánování implementace hybridního připojení Azure Active Directory](hybrid-azuread-join-plan.md)
- [Jak provést řízené ověřování připojení k hybridní službě Azure AD](hybrid-azuread-join-control.md)

Ke konfiguraci scénáře v tomto kurzu budete potřebovat:

- Windows Server 2012 R2 se službou AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) verze 1.1.819.0 nebo novější.

Azure AD Connect od verze 1.1.819.0 nabízí průvodce konfigurací hybridního připojení k Azure AD. Tento průvodce vám umožní výrazně zjednodušit proces konfigurace. Související průvodce:

- Konfiguruje spojovací body služby (SCP) pro registraci zařízení.
- Zálohuje existující vztah důvěryhodnosti přijímající strany Azure AD.
- Aktualizuje pravidla deklarace identity ve vztahu důvěryhodnosti Azure AD.

Postup konfigurace v tomto článku vychází z tohoto průvodce. Pokud máte nainstalovanou starší verzi Azure AD Connect, je potřeba provést upgrade na verzi 1.1.819 nebo novější. Pokud instalaci nejnovější verze služby Azure AD Connect není pro vás, přečtěte si téma [postup při ruční konfiguraci připojení k hybridní službě Azure AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual).

Pro hybridní připojení k Azure AD je potřeba, aby zařízení měla ze sítě vaší organizace přístup k následujícím prostředkům Microsoftu:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Služba tokenů zabezpečení vaší organizace (federované domény)
- `https://autologon.microsoftazuread-sso.com` (Pokud využíváte bezproblémové jednotné přihlašování nebo to plánujete)

Od verze Windows 10 1803, pokud se nezdaří okamžité připojení k hybridní službě Azure AD pro federovaném prostředí pomocí služby AD FS, spoléháme na Azure AD Connect pro synchronizaci objekt počítače v Azure AD, který se následně používá k dokončení registrace zařízení k hybridní službě Azure AD Připojte se k. Ověřte, že nástroj Azure AD Connect synchronizoval počítačové objekty zařízení, které chcete hybridně připojit k Azure AD. Pokud počítačové objekty patří do konkrétních organizačních jednotek, je potřeba v Azure AD Connect nakonfigurovat synchronizaci také těchto organizačních jednotek. Další informace o tom, jak synchronizovat objekty počítačů pomocí služby Azure AD Connect, najdete v článku na [konfiguraci filtrování pomocí služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Pokud vaše organizace vyžaduje, aby přístup k Internetu přes odchozí proxy server, Microsoft doporučuje [implementace Proxy Auto-Discovery WPAD (Web)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) umožňující počítače s Windows 10 postup registrace zařízení ve službě Azure AD. Pokud narazíte na problémy s konfigurace a správa WPAD, přejděte na [řešení potíží s automatickou detekci] (https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10). 

Pokud nepoužíváte WPAD a musíte nakonfigurovat nastavení proxy serveru na počítači, můžete provést tak od verze Windows 10 1709 podle [konfigurace nastavení služby WinHTTP pomocí objektu zásad skupiny (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Při konfiguraci nastavení proxy serveru na počítači pomocí nastavení služby WinHTTP, se nezdaří všechny počítače, které není možné se připojit k nakonfigurovaný proxy server pro připojení k Internetu.

Pokud vaše organizace potřebuje přístup k internetu přes ověřený odchozí proxy server, je potřeba zajistit, aby se vaše počítače s Windows 10 mohly úspěšně ověřit u odchozího proxy serveru. Vzhledem k tomu, že se na počítačích s Windows 10 provádí registrace zařízení s využitím kontextu počítače, je potřeba nakonfigurovat ověřování odchozího proxy serveru s použitím kontextu počítače. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Ke konfiguraci hybridního připojení k Azure AD pomocí Azure AD Connect potřebujete:

- Přihlašovací údaje globálního správce vašeho tenanta Azure AD.  
- Přihlašovací údaje podnikového správce jednotlivých doménových struktur.
- Přihlašovací údaje správce vaší služby AD FS.

**Konfigurace hybridního připojení k Azure AD pomocí Azure AD Connect:**

1. Spusťte Azure AD Connect a pak klikněte na **Konfigurovat**.

   ![Uvítání](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na stránce **Další úlohy** vyberte **Konfigurovat možnosti zařízení** a pak klikněte na **Další**.

   ![Další úlohy](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na stránce **Přehled** klikněte na **Další**.

   ![Přehled](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na stránce **Připojit k Azure AD** zadejte přihlašovací údaje globálního správce vašeho tenanta Azure AD a pak klikněte na **Další**.

   ![Připojení k Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na stránce **Možnosti zařízení** vyberte **Konfigurovat hybridní připojení k Azure AD** a pak klikněte na **Další**.

   ![Možnosti zařízení](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na stránce **Spojovací bod služby** proveďte následující kroky a pak klikněte na **Další**:

   ![Spojovací bod služby](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Vyberte doménovou strukturu.
   1. Vyberte ověřovací službu. Pokud vaše organizace nepoužívá výhradně klienty s Windows 10 a nenakonfigurovali jste synchronizaci počítačů a zařízení nebo vaše organizace nepoužívá bezproblémové jednotné přihlašování, musíte vybrat server služby AD FS.
   1. Klikněte na **Přidat** a zadejte přihlašovací údaje podnikového správce.

1. Na stránce **Operační systémy zařízení** vyberte operační systémy, které se používají na zařízeních ve vašem prostředí Active Directory, a pak klikněte na **Další**.

   ![Operační systém zařízení](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na stránce **Konfigurace federace** zadejte přihlašovací údaje správce vaší služby AD FS a pak klikněte na **Další**.

   ![Konfigurace federace](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na stránce **Připraveno ke konfiguraci** klikněte na **Konfigurovat**.

   ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na stránce **Dokončení konfigurace** klikněte na **Ukončit**.

   ![Dokončení konfigurace](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá z vašich zařízení připojených k doméně zařízení s Windows nižší úrovně, je potřeba provést následující:

- Konfigurace nastavení místního intranetu pro registraci zařízení
- Nainstalujte Microsoft síti na pracovišti připojit pro Windows nižší úrovně počítače

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Pokud chcete úspěšně dokončit hybridní připojení zařízení s Windows nižší úrovně k Azure AD a zabránit výzvám k zadání certifikátu při ověřování zařízení vůči Azure AD, můžete zařízením připojeným k doméně nabídnout zásadu, která přidá následující adresy URL do zóny Místní intranet v Internet Exploreru:

- `https://device.login.microsoftonline.com`
- Služba tokenů zabezpečení vaší organizace (STS – federované domény)
- `https://autologon.microsoftazuread-sso.com` (pro bezproblémové jednotné přihlašování)

Kromě toho je potřeba v zóně místního intranetu uživatele povolit možnost **Povolit aktualizace stavového řádku přes skript**.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Nainstalujte Microsoft síti na pracovišti připojit pro Windows nižší úrovně počítače

K registraci zařízení s Windows nižší úrovně, musíte nainstalovat organizace [Microsoft Workplace Join pro počítače s Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systém distribuce softwaru, jako je [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Balíček podporuje možnosti standardní tichou instalaci s parametrem tichý. Aktuální větev nástroje Configuration Manager nabízí výhody starší verze, jako je schopnost sledování dokončení registrace.

Instalační program vytvoří naplánovanou úlohu v systému, na kterém běží v kontextu uživatele. Úloha se aktivuje, když uživatel provede přihlášení pro Windows. Úloha tiše připojí zařízení s Azure AD s přihlašovacími údaji uživatele po ověření pomocí Azure AD.

## <a name="verify-the-registration"></a>Ověření registrace

K ověření stavu registrace zařízení v tenantovi Azure můžete použít rutinu **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** v **[modulu Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

Když použijete rutinu **Get-MSolDevice** ke kontrole podrobností služby:

- Musí existovat objekt s **ID zařízení**, které odpovídá ID v klientovi Windows.
- Hodnota **DeviceTrustType** (Stav důvěryhodnosti zařízení) musí být nastavená na **Domain Joined** (Připojeno k doméně). Jedná se o ekvivalent stavu **Hybridně připojeno k Azure AD** na stránce Zařízení na portálu Azure AD.
- U zařízení, která se používají k podmíněnému přístupu, musí být hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda) a hodnota **DeviceTrustLevel** (Úroveň důvěryhodnosti zařízení) musí být nastavená na **Managed** (Spravované).

**Kontrola podrobností služby:**

1. Otevřete **Windows PowerShell** jako správce.
1. Zadejte `Connect-MsolService` a připojte se ke svému tenantovi Azure.  
1. Zadejte `get-msoldevice -deviceId <deviceId>`.
1. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud máte problémy s dokončením hybridního připojení k Azure AD pro zařízení s Windows připojená k doméně, přečtěte si následující témata:

- [Řešení potíží s hybridním připojením aktuálních zařízení s Windows k Azure AD](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridním připojením zařízení s Windows nižší úrovně k Azure AD](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další postup

- Další informace o správě identit zařízení na portálu Azure AD najdete v tématu [Správa identit zařízení pomocí webu Azure portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
