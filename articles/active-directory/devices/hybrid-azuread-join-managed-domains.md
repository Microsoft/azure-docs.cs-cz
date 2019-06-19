---
title: Konfigurace hybridních připojení k Azure Active Directory pro spravované domény | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat připojení k hybridní službě Azure Active Directory službě pro spravované domény.
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
ms.openlocfilehash: b24888934d7e89a13b1b07b7138be476575fc306
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204620"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Kurz: Konfigurace hybridních připojení k Azure Active Directory pro spravované domény

Podobně jako uživatel ve vaší organizaci zařízení je core identity, které chcete chránit. Identita zařízení můžete použít k ochraně vašich prostředků, kdykoli a odkudkoli. Dosažení tohoto cíle přináší identit zařízení a jejich správu v Azure Active Directory (Azure AD) pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Uvedení vašeho zařízení do služby Azure AD maximalizuje snížení produktivity uživatelů prostřednictvím jednotného přihlašování (SSO) ve vašem cloudovém a místním prostředkům. Můžete zabezpečit přístup do cloudu a místních prostředků pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) ve stejnou dobu.

V tomto kurzu se dozvíte, jak nakonfigurovat připojení k hybridní službě Azure AD pro zařízení pro počítače připojené k doméně služby Active Directory ve spravovaném prostředí. 

Spravované prostředí můžou být nasazené prostřednictvím [synchronizace hodnot hash hesel (PHS)](../hybrid/whatis-phs.md) nebo [předávací ověřování (PTA)](../hybrid/how-to-connect-pta.md) s [bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso.md). Tyto scénáře nevyžadují konfigurace federačního serveru pro ověřování.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolení zařízení s Windows nižší úrovně
> * Ověření připojených zařízení
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že znáte tyto články:

- [Co je identita, zařízení?](overview.md)
- [Jak naplánovat vaši implementaci připojení k hybridní službě Azure AD](hybrid-azuread-join-plan.md)
- [Jak provést řízené ověřování připojení k hybridní službě Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD nepodporuje čipové karty ani certifikáty ve spravovaných doménách.

Chcete-li nakonfigurovat scénář v tomto článku, musíte [nejnovější verzi služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 nebo novější) nainstalovaný.

Ověřte, že má Azure AD Connect synchronizuje objekty počítače zařízení, která chcete k hybridní službě Azure AD připojená k Azure AD. Pokud objekty počítače patří do konkrétní organizační jednotky (OU), musíte také nakonfigurovat organizační jednotky pro synchronizaci ve službě Azure AD Connect. Další informace o tom, jak synchronizovat objekty počítačů pomocí Azure AD Connect najdete v tématu [konfigurovat filtrování pomocí služby Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Počínaje verzí 1.1.819.0, Azure AD Connect obsahuje průvodce, který můžete použít ke konfiguraci připojení k hybridní službě Azure AD. Průvodce výrazně zjednodušuje proces konfigurace. Průvodce konfiguruje spojovací body služby (SCP) pro registraci zařízení.

Jednotlivé kroky konfigurace v tomto článku jsou založeny na pomocí Průvodce ve službě Azure AD Connect.

Připojení k hybridní službě Azure AD vyžaduje, aby zařízení mít přístup k následujícím prostředkům společnosti Microsoft z v síti vaší organizace:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Pokud používáte nebo plánujete používat bezproblémové jednotné přihlašování)

Pokud vaše organizace vyžaduje, aby přístup k Internetu přes odchozí proxy server, Microsoft doporučuje [implementace Proxy Auto-Discovery WPAD (Web)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) umožňující počítače s Windows 10 pro registrace zařízení ve službě Azure AD. Pokud narazíte na problémy konfigurace a správa WPAD, přečtěte si téma [řešení potíží s automatickou detekci](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Pokud nepoužíváte WPAD a potřeba ke konfiguraci nastavení proxy serveru na počítači, můžete provést tak od verze Windows 10 1709. Další informace najdete v tématu [nastavení konfigurace služby WinHTTP pomocí objektu zásad skupiny (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Při konfiguraci nastavení proxy serveru na počítači s použitím nastavení služby WinHTTP, všechny počítače, které se nemůže připojit k nakonfigurovaný proxy server se nepodaří připojit k Internetu.

Pokud vaše organizace potřebuje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, musí Ujistěte se, že vaše počítače s Windows 10 můžete provádět ověření odchozího proxy serveru. Protože počítače s Windows 10 spustit registrace zařízení pomocí místní počítač, musíte nakonfigurovat odchozího proxy serveru ověřování pomocí místní počítač. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Ke konfiguraci hybridního připojení k Azure AD pomocí Azure AD Connect potřebujete:

- Přihlašovací údaje globálního správce pro vašeho tenanta Azure AD
- Přihlašovací údaje podnikového správce pro jednotlivé doménové struktury

**Konfigurace připojení k hybridní službě Azure AD pomocí služby Azure AD Connect:**

1. Spusťte Azure AD Connect a pak vyberte **konfigurovat**.

   ![Vítej](./media/hybrid-azuread-join-managed-domains/11.png)

1. Na **další úkoly** stránce **konfigurovat možnosti zařízení**a pak vyberte **Další**.

   ![Další úlohy](./media/hybrid-azuread-join-managed-domains/12.png)

1. Na **přehled** stránce **Další**.

   ![Přehled](./media/hybrid-azuread-join-managed-domains/13.png)

1. Na stránce **Připojit k Azure AD** zadejte přihlašovací údaje globálního správce vašeho tenanta Azure AD.  

   ![Připojení k Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. Na **možnosti zařízení** stránce **konfigurovat hybridní službě Azure AD join**a pak vyberte **Další**.

   ![Možnosti zařízení](./media/hybrid-azuread-join-managed-domains/15.png)

1. Na **spojovací bod služby** stránky pro každou doménovou strukturu, ve kterém chcete Azure AD Connect ke konfiguraci spojovacího bodu služby, proveďte následující kroky a pak vyberte **Další**:

   ![Spojovací bod služby](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Vyberte doménovou strukturu.
   1. Vyberte ověřovací službu.
   1. Vyberte **přidat** zadat přihlašovací údaje podnikového správce.

1. Na **operační systémy zařízení** stránky, vyberte operační systémy, že zařízení ve vaší prostředí služby Active Directory a pak vyberte **Další**.

   ![Operační systém zařízení](./media/hybrid-azuread-join-managed-domains/17.png)

1. Na **připraveno ke konfiguraci** stránce **konfigurovat**.

   ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-managed-domains/19.png)

1. Na **konfiguraci Dokončit** stránce **ukončovací**.

   ![Dokončení konfigurace](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Povolení zařízením s Windows nižší úrovně

Pokud některé z vašich zařízení připojených k doméně jsou zařízení s nižší verzí Windows, musíte mít:

- Konfigurace nastavení místního intranetu pro registraci zařízení
- Konfigurace bezproblémového jednotného přihlašování
- Nainstalujte Microsoft síti na pracovišti připojit pro Windows nižší úrovně počítače

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

o úspěšné dokončení připojení k hybridní službě Azure AD službě zařízení Windows nižší úrovně a pokud chcete vyhnout výzev ohledně certifikátů při ověřování zařízení do služby Azure AD, můžete zásadu odeslat do zařízení připojených k doméně přidat následující adresy URL do zóny místního intranetu v aplikaci Internet Průzkumník:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Musíte také povolit **povolit aktualizace stavového řádku prostřednictvím skriptu** do zóny místního intranetu uživatele.

### <a name="configure-seamless-sso"></a>Konfigurace bezproblémového jednotného přihlašování

K úspěšnému dokončení připojení ke službě Azure AD hybridní zařízení starší verze Windows ve spravované doméně, která používá [PHS].. /Hybrid/whatis-phs.MD) nebo [PTA](../hybrid/how-to-connect-pta.md) jako metodu ověřování cloudové služby Azure AD, musíte také [konfigurace bezproblémového jednotného přihlašování](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Nainstalujte Microsoft síti na pracovišti připojit pro Windows nižší úrovně počítače

K registraci zařízení s Windows nižší úrovně, musíte nainstalovat organizace [Microsoft Workplace Join pro počítače s Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join pro počítače s Windows 10 je k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systém distribuce softwaru, jako je [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Balíček podporuje možnosti standardní tichou instalaci pomocí `quiet` parametru. Aktuální větev nástroje Configuration Manager nabízí výhody starší verze, jako je schopnost sledování dokončení registrace.

Instalační program vytvoří naplánovanou úlohu v systému, na kterém běží v kontextu uživatele. Úloha se aktivuje, když uživatel přihlásí do Windows. Úloha tiše připojí zařízení s Azure AD pomocí přihlašovacích údajů uživatele po ověří se službou Azure AD.

## <a name="verify-the-registration"></a>Ověření registrace

Pokud chcete ověřit stav registrace zařízení ve vašem tenantovi Azure, můžete použít **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** rutiny v [modul Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Při použití **Get-MSolDevice** rutiny pro kontrolu podrobností služby:

- Objekt se **ID zařízení** , který odpovídá ID na Windows, klient musí existovat.
- Hodnota **DeviceTrustType** (Stav důvěryhodnosti zařízení) musí být nastavená na **Domain Joined** (Připojeno k doméně). Toto nastavení je ekvivalentní **připojená k hybridní službě Azure AD** stavu na **zařízení** stránky na portálu Azure AD.
- Pro zařízení, která se používají v podmíněného přístupu, hodnota **povoleno** musí být **True** a **DeviceTrustLevel** musí být **spravované**.

**Chcete-li zkontrolovat podrobnosti o službě**:

1. Otevřete prostředí Windows PowerShell jako správce.
1. Zadejte `Connect-MsolService` pro připojení k tenantovi Azure.  
1. Zadejte `get-msoldevice -deviceId <deviceId>`.
1. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud dochází k problémům s dokončením připojení k hybridní službě Azure AD pro zařízení s Windows připojených k doméně, přečtěte si:

- [Řešení potíží s hybridní připojení k Azure AD pro aktuální zařízení s Windows](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridní služby Azure AD join pro zařízení s Windows nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další postup

Zjistěte, jak [Správa identit zařízení pomocí webu Azure portal](device-management-azure-portal.md).
