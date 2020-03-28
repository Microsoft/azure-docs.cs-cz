---
title: Konfigurace hybridního připojení služby Azure Active Directory pro spravované domény | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat hybridní připojení k Azure Active Directory pro spravované domény.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239109"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Kurz: Konfigurace hybridního připojení k Azure Active Directory pro spravované domény

V tomto kurzu se dozvíte, jak nakonfigurovat hybridní připojení služby Azure Active Directory (Azure AD) pro zařízení připojená k doméně služby Active Directory. Tato metoda podporuje spravované prostředí, které zahrnuje místní služby Active Directory a Azure AD.

Stejně jako uživatel ve vaší organizaci je zařízení základní identitou, kterou chcete chránit. Identitu zařízení můžete použít k ochraně prostředků kdykoli a z libovolného místa. Tohoto cíle můžete dosáhnout správou identit zařízení ve službě Azure AD. Použijte jednu z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Tento článek se zaměřuje na hybridní připojení Azure AD.

Uvedení vašich zařízení do Azure AD maximalizuje produktivitu uživatelů prostřednictvím jednotného přihlašování (SSO) napříč cloudovými a místními prostředky. Podmíněný přístup můžete zabezpečit pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) současně.

Spravované prostředí můžete nasadit pomocí [synchronizace hash hesel (PHS)](../hybrid/whatis-phs.md) nebo [předávacího ověřování (PTA)](../hybrid/how-to-connect-pta.md) s [bezproblémovým jednotném přihlašování](../hybrid/how-to-connect-sso.md). Tyto scénáře nevyžadují konfiguraci federačního serveru pro ověřování.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolení zařízení s Windows nižší úrovně
> * Ověření připojených zařízení
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 nebo novější)
- Přihlašovací údaje globálního správce pro vašeho klienta Azure AD
- Pověření správce rozlehlé sítě pro každou doménovou struktura

Seznamte se s těmito články:

- [Co je identita zařízení?](overview.md)
- [Postup: Plánování hybridní implementace připojení k hybridníslužbě Azure Active Directory](hybrid-azuread-join-plan.md)
- [Řízené ověřování hybridního připojení k Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD nepodporuje čipové karty nebo certifikáty ve spravovaných doménách.

Ověřte, že Azure AD Connect synchronizoval objekty počítače zařízení, která chcete být hybridní Azure AD připojen k Azure AD. Pokud objekty počítače patří do konkrétních organizačních jednotek (OU), nakonfigurujte organizační jednotky pro synchronizaci v Azure AD Connect. Další informace o synchronizaci objektů počítače pomocí služby Azure AD Connect najdete v [tématu Filtrování založené na organizačních jednotkách](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Počínaje verzí 1.1.819.0 azure ad connect obsahuje průvodce pro konfiguraci hybridního připojení Azure AD. Průvodce výrazně zjednodušuje proces konfigurace. Průvodce konfiguruje body připojení služby (SCP) pro registraci zařízení.

Kroky konfigurace v tomto článku jsou založeny na použití průvodce ve službě Azure AD Connect.

Hybridní připojení Azure AD vyžaduje, aby zařízení měla přístup k následujícím prostředkům Microsoftu z vlastní sítě vaší organizace:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`(Pokud používáte nebo plánujete používat bezproblémové jednotné přihlašovat)

Pokud vaše organizace vyžaduje přístup k internetu prostřednictvím odchozího proxy serveru, doporučujeme [implementovat automatické zjišťování webového proxy serveru (WPAD),](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) aby se u počítačů s Windows 10 povolila registrace zařízení ve službě Azure AD. Problémy s konfigurací a správou wpadu naleznete [v tématu Poradce při potížích s automatickým zjišťováním](/previous-versions/tn-archive/cc302643(v=technet.10)).

Pokud nepoužíváte WPAD, můžete nakonfigurovat nastavení proxy serveru v počítači počínaje Windows 10 1709. Další informace naleznete v tématu [WinHTTP Proxy Settings deployed by GPO](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Pokud nakonfigurujete nastavení serveru proxy v počítači pomocí nastavení WinHTTP, všechny počítače, které se nemohou připojit k nakonfigurovanému serveru proxy, se nepřipojují k Internetu.

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, ujistěte se, že se počítače s Windows 10 mohou úspěšně ověřit na odchozím proxy serveru. Vzhledem k tomu, že počítače se systémem Windows 10 spouštějí registraci zařízení pomocí kontextu počítače, nakonfigurujte ověřování odchozího proxy serveru pomocí kontextu počítače. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

Ověřte, zda má zařízení přístup k výše uvedeným prostředkům společnosti Microsoft v rámci systémového účtu pomocí skriptu [Test Device Registration Connectivity](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Konfigurace hybridního připojení Azure AD pomocí služby Azure AD Connect:

1. Spusťte Azure AD Connect a pak vyberte **Konfigurovat**.

   ![Vítáme vás](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. V **části Další úkoly**vyberte **Konfigurovat možnosti zařízení**a pak vyberte **Další**.

   ![Další úlohy](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. V **části Přehled**vyberte další **položku**.

   ![Přehled](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. V **části Připojení k Azure AD**zadejte přihlašovací údaje globálního správce pro vašeho klienta Azure AD.  

   ![Připojení k Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. V **možnostech zařízení**vyberte **Konfigurovat hybridní připojení Azure AD**a pak vyberte **Další**.

   ![Možnosti zařízení](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. V **konfiguraci SCP**, pro každou doménovou strukturu, kde chcete Azure AD Connect nakonfigurovat SCP, proveďte následující kroky a pak vyberte **Další**.

   1. Vyberte **doménovou strukturu**.
   1. Vyberte **ověřovací službu**.
   1. Výběrem **možnosti Přidat** zadejte pověření správce rozlehlé sítě.

   ![Spojovací bod služby](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. V **části Operační systémy Zařízení**vyberte operační systémy, které zařízení používají v prostředí služby Active Directory, a pak vyberte **další**.

   ![Operační systém zařízení](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. V **aplikaci Ready to configure**vyberte **Configure**.

   ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. V **části Konfigurace dokončena**vyberte **ukončete**.

   ![Dokončení konfigurace](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá zařízení připojená k doméně zařízení mj.

- Konfigurace nastavení místního intranetu pro registraci zařízení
- Konfigurace bezproblémového jednotného přihlašuje
- Instalace připojení microsoft workplace pro počítače nižší úrovně systému Windows

> [!NOTE]
> Podpora windows 7 skončila 14. ledna 2020. Další informace naleznete v tématu [Windows 7 podpora skončila](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Chcete-li dokončit hybridní připojení Azure AD zařízení nižší úrovně systému Windows a vyhnout se výzvám k certifikátu při ověřování zařízení ve službě Azure AD, můžete do zařízení spojených s doménou vytvořit zásadu a přidat následující adresy URL do místní zóny intranet v aplikaci Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Musíte také povolit **povolit aktualizace stavového řádku prostřednictvím skriptu** v místní intranetové zóně uživatele.

### <a name="configure-seamless-sso"></a>Konfigurace bezproblémového jednotného přihlašuje

Chcete-li dokončit hybridní připojení Azure AD zařízení nižší úrovně systému Windows ve spravované doméně, která používá [synchronizaci hash hesel](../hybrid/whatis-phs.md) nebo [předávací ověřování](../hybrid/how-to-connect-pta.md) jako metodu ověřování cloudu Azure AD, musíte také [nakonfigurovat bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Instalace připojení microsoft workplace pro počítače nižší úrovně systému Windows

Chcete-li zaregistrovat zařízení windows nižší úrovně, organizace musí nainstalovat [Microsoft Workplace Join pro počítače s jiným systémem 10](https://www.microsoft.com/download/details.aspx?id=53554). Připojení k webu Microsoft Workplace pro počítače s jiným i nese systémem Windows 10 je k dispozici v centru pro stahování Microsoft Download Center.

Balíček můžete nasadit pomocí systému distribuce softwaru, jako je [Microsoft Endpoint Configuration Manager](/configmgr/). Balíček podporuje standardní možnosti `quiet` tiché instalace s parametrem. Aktuální verze nástroje Configuration Manager nabízí výhody oproti dřívějším verzím, například možnost sledovat dokončené registrace.

Instalační program vytvoří naplánovanou úlohu v systému, která je spuštěna v uživatelském kontextu. Úloha se spustí, když se uživatel přihlásí k systému Windows. Úloha bezobslužně připojí zařízení s Azure AD pomocí přihlašovacích údajů uživatele po ověření pomocí Azure AD.

## <a name="verify-the-registration"></a>Ověření registrace

Ověřte stav registrace zařízení v tenantovi Azure pomocí **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)**. Tato rutina je v [modulu Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Při použití **rutiny Get-MSolDevice** ke kontrole podrobností o službě:

- Musí existovat objekt s **ID zařízení,** který odpovídá ID klienta systému Windows.
- Hodnota **devicetrusttype** je **připojenka k doméně**. Toto nastavení je ekvivalentní stavu **hybridní Azure AD připojil** na stránce **zařízení** na portálu Azure AD.
- Pro zařízení, která se používají v podmíněném přístupu, je hodnota **Enabled** **True** a **DeviceTrustLevel** je **spravována**.

Kontrola podrobností služby:

1. Spusťte Windows PowerShell jako správce.
1. Vstupem `Connect-MsolService` se můžete připojit ke svému tenantovi Azure.  
1. Zadejte `get-msoldevice -deviceId <deviceId>`.
1. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud máte problémy s dokončením hybridního připojení azure ad pro zařízení s Windows připojená k doméně, přečtěte si:

- [Poradce při potížích s hybridními zařízeními azure active directory](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridními zařízeními Azure Active Directory, která se připojili k zařízením nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu článku, kde se dozvíte, jak spravovat identity zařízení pomocí portálu Azure.
> [!div class="nextstepaction"]
> [Správa identit zařízení](device-management-azure-portal.md)
