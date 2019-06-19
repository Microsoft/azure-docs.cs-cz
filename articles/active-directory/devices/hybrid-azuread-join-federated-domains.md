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
ms.openlocfilehash: 738b4f47054081f0fb1b1a530bdf21cbf07a7726
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204701"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Kurz: Konfigurace připojení k hybridní službě Azure Active Directory službě u federovaných domén

Podobně jako uživatel ve vaší organizaci zařízení je core identity, které chcete chránit. Identita zařízení můžete použít k ochraně vašich prostředků, kdykoli a odkudkoli. Dosažení tohoto cíle přináší identit zařízení a jejich správu v Azure Active Directory (Azure AD) pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Uvedení vašeho zařízení do služby Azure AD maximalizuje snížení produktivity uživatelů prostřednictvím jednotného přihlašování (SSO) ve vašem cloudovém a místním prostředkům. Můžete zabezpečit přístup do cloudu a místních prostředků pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) ve stejnou dobu.

V tomto kurzu se dozvíte, jak nakonfigurovat připojení k hybridní službě Azure AD službě pro zařízení pro počítače připojené k doméně služby Active Directory ve federovaném prostředí pomocí služby Active Directory Federation Services (AD FS).

> [!NOTE]
> Pokud vaše prostředí federované používá zprostředkovatel identity než služba AD FS, musíte zajistit, že váš poskytovatel identit podporuje protokol WS-Trust. WS-Trust se vyžaduje pro ověření aktuálního hybridní služby Azure AD vaší Windows připojených k zařízení pomocí služby Azure AD. Pokud máte zařízení s nižší verzí Windows, které chcete připojení k hybridní službě Azure AD vašeho zprostředkovatele identity musí podporovat WIAORMULTIAUTHN deklarace identity. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolení zařízením s Windows nižší úrovně
> * Ověření registrace
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá této you'e znáte tyto články:

- [Co je identita, zařízení?](overview.md)
- [Jak naplánovat vaši implementaci připojení k hybridní službě Azure AD](hybrid-azuread-join-plan.md)
- [Jak provést řízené ověřování připojení k hybridní službě Azure AD](hybrid-azuread-join-control.md)

Ke konfiguraci scénáře v tomto kurzu budete potřebovat:

- Windows Server 2012 R2 se službou AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) verze 1.1.819.0 nebo novější

Počínaje verzí 1.1.819.0, Azure AD Connect obsahuje průvodce, který můžete použít ke konfiguraci připojení k hybridní službě Azure AD. Průvodce výrazně zjednodušuje proces konfigurace. Související průvodce:

- Nakonfiguruje spojovací body služby (SCP) pro registraci zařízení
- Zálohuje existující vztah důvěryhodnosti přijímající strany Azure AD.
- Aktualizuje pravidla deklarace identity ve vztahu důvěryhodnosti Azure AD.

Jednotlivé kroky konfigurace v tomto článku jsou založeny na pomocí Průvodce Azure AD Connect. Pokud máte starší verzi služby Azure AD Connect nainstalovaný, musíte upgradovat 1.1.819 nebo později pomocí průvodce. Pokud instalaci nejnovější verze služby Azure AD Connect není pro vás, přečtěte si téma [postup při ruční konfiguraci připojení k hybridní službě Azure AD](hybrid-azuread-join-manual.md).

Připojení k hybridní službě Azure AD vyžaduje, aby zařízení mít přístup k následujícím prostředkům společnosti Microsoft z v síti vaší organizace:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Vaše organizace službu tokenů zabezpečení (STS) (u federovaných domén)
- `https://autologon.microsoftazuread-sso.com` (Pokud používáte nebo plánujete používat bezproblémové jednotné přihlašování)

Od verze Windows 10 1803, pokud se nezdaří okamžité hybridní připojení Azure AD k federovaném prostředí pomocí služby AD FS, spoléháme na Azure AD Connect pro synchronizaci objekt počítače v Azure AD, která se následně používá k dokončení registrace zařízení pro hybridní Azure Připojení ke službě AD. Ověřte, že má Azure AD Connect synchronizuje objekty počítače zařízení, která chcete k hybridní službě Azure AD připojená k Azure AD. Pokud objekty počítače patří do konkrétní organizační jednotky (OU), musíte také nakonfigurovat organizační jednotky pro synchronizaci ve službě Azure AD Connect. Další informace o tom, jak synchronizovat objekty počítačů pomocí Azure AD Connect najdete v tématu [konfigurovat filtrování pomocí služby Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Pokud vaše organizace vyžaduje, aby přístup k Internetu přes odchozí proxy server, Microsoft doporučuje [implementace Proxy Auto-Discovery WPAD (Web)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) umožňující počítače s Windows 10 pro registrace zařízení ve službě Azure AD. Pokud narazíte na problémy konfigurace a správa WPAD, přečtěte si téma [řešení potíží s automatickou detekci](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Pokud nemáte použít WPAD a chcete nakonfigurovat nastavení proxy serveru na počítači, můžete provést tak od verze Windows 10 1709. Další informace najdete v tématu [nastavení konfigurace služby WinHTTP pomocí objektu zásad skupiny (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Při konfiguraci nastavení proxy serveru na počítači s použitím nastavení služby WinHTTP, všechny počítače, které se nemůže připojit k nakonfigurovaný proxy server se nepodaří připojit k Internetu.

Pokud vaše organizace potřebuje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, musí Ujistěte se, že vaše počítače s Windows 10 můžete provádět ověření odchozího proxy serveru. Protože počítače s Windows 10 spustit registrace zařízení pomocí místní počítač, musíte nakonfigurovat odchozího proxy serveru ověřování pomocí místní počítač. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Konfigurace připojení k hybridní službě Azure AD pomocí služby Azure AD Connect, budete potřebovat:

- Přihlašovací údaje globálního správce pro vašeho tenanta Azure AD  
- Přihlašovací údaje podnikového správce pro jednotlivé doménové struktury
- Přihlašovací údaje Správce služby AD FS

**Konfigurace připojení k hybridní službě Azure AD pomocí služby Azure AD Connect**:

1. Spusťte Azure AD Connect a pak vyberte **konfigurovat**.

   ![Vítej](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na **další úkoly** stránce **konfigurovat možnosti zařízení**a pak vyberte **Další**.

   ![Další úlohy](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na **přehled** stránce **Další**.

   ![Přehled](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na **připojit ke službě Azure AD** stránky, zadejte přihlašovací údaje globálního správce pro vašeho tenanta Azure AD a pak vyberte **Další**.

   ![Připojení k Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na **možnosti zařízení** stránce **konfigurovat hybridní službě Azure AD join**a pak vyberte **Další**.

   ![Možnosti zařízení](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na **spojovací bod služby** stránce, proveďte následující kroky a pak vyberte **Další**:

   ![Spojovací bod služby](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Vyberte doménovou strukturu.
   1. Vyberte ověřovací službu. Musíte vybrat **serveru služby AD FS** Pokud má vaše organizace výhradně klientů s Windows 10 a nakonfigurovali jste synchronizaci počítače/zařízení nebo vaše organizace používá bezproblémového jednotného přihlašování.
   1. Vyberte **přidat** zadat přihlašovací údaje podnikového správce.

1. Na **operační systémy zařízení** vyberte operační systémy, které používají zařízení ve vašem prostředí služby Active Directory a pak vyberte **Další**.

   ![Operační systém zařízení](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na **konfigurace federace** stránky, zadejte přihlašovací údaje Správce služby AD FS a pak vyberte **Další**.

   ![Konfigurace federace](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na **připraveno ke konfiguraci** stránce **konfigurovat**.

   ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na **konfiguraci Dokončit** stránce **ukončovací**.

   ![Dokončení konfigurace](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Povolení zařízením s Windows nižší úrovně

Pokud některé z vašich zařízení připojených k doméně jsou zařízení s nižší verzí Windows, musíte mít:

- Konfigurace nastavení místního intranetu pro registraci zařízení
- Nainstalujte Microsoft síti na pracovišti připojit pro Windows nižší úrovně počítače

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Chcete-li úspěšně dokončit připojení k hybridní službě Azure AD službě zařízení Windows nižší úrovně a pokud chcete vyhnout výzev ohledně certifikátů při ověřování zařízení do služby Azure AD, můžete zásadu odeslat do zařízení připojených k doméně přidat následující adresy URL do zóny místního intranetu v aplikaci Internet Průzkumník:

- `https://device.login.microsoftonline.com`
- Služba tokenů zabezpečení vaší organizace (u federovaných domén)
- `https://autologon.microsoftazuread-sso.com` (Pro bezproblémové jednotné přihlašování)

Musíte také povolit **povolit aktualizace stavového řádku prostřednictvím skriptu** do zóny místního intranetu uživatele.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Nainstalujte Microsoft síti na pracovišti připojit pro Windows nižší úrovně počítače

K registraci zařízení s Windows nižší úrovně, musíte nainstalovat organizace [Microsoft Workplace Join pro počítače s Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join pro počítače s Windows 10 je k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systém distribuce softwaru, jako je [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Balíček podporuje možnosti standardní tichou instalaci pomocí `quiet` parametru. Aktuální větev nástroje Configuration Manager nabízí výhody starší verze, jako je schopnost sledování dokončení registrace.

Instalační program vytvoří naplánovanou úlohu v systému, na kterém běží v kontextu uživatele. Úloha se aktivuje, když uživatel přihlásí do Windows. Úloha tiše připojí zařízení s Azure AD pomocí přihlašovacích údajů uživatele po ověří se službou Azure AD.

## <a name="verify-the-registration"></a>Ověření registrace

Pokud chcete ověřit stav registrace zařízení ve vašem tenantovi Azure, můžete použít **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** rutiny v [modul Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Při použití **Get-MSolDevice** rutiny pro kontrolu podrobností služby:

- Objekt se **ID zařízení** , který odpovídá ID na Windows, klient musí existovat.
- Hodnota **DeviceTrustType** (Stav důvěryhodnosti zařízení) musí být nastavená na **Domain Joined** (Připojeno k doméně). Toto nastavení je ekvivalentní **připojená k hybridní službě Azure AD** stavu v rámci **zařízení** na portálu Azure AD.
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

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
