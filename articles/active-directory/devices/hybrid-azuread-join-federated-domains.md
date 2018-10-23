---
title: Postup konfigurace hybridních zařízení připojených k Azure Active Directory | Microsoft Docs
description: Zjistěte, jak nakonfigurovat hybridní zařízení připojená k Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/25/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: c7396d7322958442fab51417eb350f26f7ada78e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352656"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Kurz: Konfigurace hybridního připojení k Azure Active Directory pro federované domény

Zařízení se podobně jako uživatel stávají další identitou, kterou chcete chránit a pomocí které zároveň chcete nepřetržitě chránit své prostředky, ať jsou kdekoli. Můžete toho docílit tím, že pomocí některé z následujících metod přenesete identity vašich zařízení do Azure AD:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Přenosem zařízení do Azure AD maximalizujete produktivitu uživatelů díky jednotnému přihlašování ke cloudovým i místním prostředkům. Současně můžete zabezpečit přístup k vašim cloudovým i místním prostředkům s využitím [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md).

V tomto kurzu se dozvíte, jak nakonfigurovat hybridní připojení k Azure AD pro zařízení federovaná pomocí služby AD FS.

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolení zařízení s Windows nižší úrovně
> * Ověření registrace
> * Řešení potíží


## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá znalost následujících témat:

-  [Úvod do správy zařízení v Azure Active Directory](../device-management-introduction.md)

-  [Plánování implementace hybridního připojení Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Jak řídit How hybridní připojení Azure AD pro vaše zařízení](hybrid-azuread-join-control.md)


Ke konfiguraci scénáře v tomto kurzu budete potřebovat:

- Windows Server 2012 R2 se službou AD FS

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) verze 1.1.819.0 nebo novější. 
 

Azure AD Connect od verze 1.1.819.0 nabízí průvodce konfigurací hybridního připojení k Azure AD. Tento průvodce vám umožní výrazně zjednodušit proces konfigurace. Související průvodce:

- Konfiguruje spojovací body služby (SCP) pro registraci zařízení.

- Zálohuje existující vztah důvěryhodnosti přijímající strany Azure AD.

- Aktualizuje pravidla deklarace identity ve vztahu důvěryhodnosti Azure AD.

Postup konfigurace v tomto článku vychází z tohoto průvodce. Pokud máte nainstalovanou starší verzi Azure AD Connect, je potřeba provést upgrade na verzi 1.1.819 nebo novější. Pokud nemůžete nainstalovat nejnovější verzi Azure AD Connect, přečtěte si, [jak ručně nakonfigurovat registraci zařízení](../device-management-hybrid-azuread-joined-devices-setup.md).

Pro hybridní připojení k Azure AD je potřeba, aby zařízení měla ze sítě vaší organizace přístup k následujícím prostředkům Microsoftu:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- Služba tokenů zabezpečení vaší organizace (federované domény)
- https://autologon.microsoftazuread-sso.com (Pokud využíváte bezproblémové jednotné přihlašování nebo to plánujete)

Od verze Windows 10 1803 platí, že pokud se nezdaří okamžité připojení k hybridní službě Azure AD pro federované domény, jako je AD FS, využíváme Azure AD Connect k synchronizaci objektu počítače v Azure AD a ten se potom použije k dokončení registrace zařízení pro připojení k hybridní službě Azure AD.

Pokud vaše organizace potřebuje přístup k internetu přes odchozí proxy server, od verze Windows 10 1709 můžete [na počítači nakonfigurovat nastavení proxy serveru pomocí objektu zásad skupiny](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/). Pokud váš počítač využívá starší verzi než Windows 10 1709, je potřeba implementovat Automatické zjišťování webových proxy serverů (WPAD), kterým umožníte počítačům s Windows 10 registrovat zařízení v Azure AD. 

Pokud vaše organizace potřebuje přístup k internetu přes ověřený odchozí proxy server, je potřeba zajistit, aby se vaše počítače s Windows 10 mohly úspěšně ověřit u odchozího proxy serveru. Vzhledem k tomu, že se na počítačích s Windows 10 provádí registrace zařízení s využitím kontextu počítače, je potřeba nakonfigurovat ověřování odchozího proxy serveru s použitím kontextu počítače. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru. 


## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Ke konfiguraci hybridního připojení k Azure AD pomocí Azure AD Connect potřebujete:

- Přihlašovací údaje globálního správce vašeho tenanta Azure AD.  

- Přihlašovací údaje podnikového správce jednotlivých doménových struktur.

- Přihlašovací údaje správce vaší služby AD FS. 


**Konfigurace hybridního připojení k Azure AD pomocí Azure AD Connect:**

1. Spusťte Azure AD Connect a pak klikněte na **Konfigurovat**.

    ![Uvítání](./media/hybrid-azuread-join-federated-domains/11.png)

2. Na stránce **Další úlohy** vyberte **Konfigurovat možnosti zařízení** a pak klikněte na **Další**. 

    ![Další úlohy](./media/hybrid-azuread-join-federated-domains/12.png)

3. Na stránce **Přehled** klikněte na **Další**. 

    ![Přehled](./media/hybrid-azuread-join-federated-domains/13.png)

4. Na stránce **Připojit k Azure AD** zadejte přihlašovací údaje globálního správce vašeho tenanta Azure AD a pak klikněte na **Další**.   

    ![Připojení k Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

5. Na stránce **Možnosti zařízení** vyberte **Konfigurovat hybridní připojení k Azure AD** a pak klikněte na **Další**. 

    ![Možnosti zařízení](./media/hybrid-azuread-join-federated-domains/15.png)

6. Na stránce **Spojovací bod služby** proveďte následující kroky a pak klikněte na **Další**: 

    ![Spojovací bod služby](./media/hybrid-azuread-join-federated-domains/16.png)

    a. Vyberte doménovou strukturu.

    b. Vyberte ověřovací službu. Pokud vaše organizace nepoužívá výhradně klienty s Windows 10 a nenakonfigurovali jste synchronizaci počítačů a zařízení nebo vaše organizace nepoužívá bezproblémové jednotné přihlašování, musíte vybrat server služby AD FS.

    c. Klikněte na **Přidat** a zadejte přihlašovací údaje podnikového správce.


7. Na stránce **Operační systémy zařízení** vyberte operační systémy, které se používají na zařízeních ve vašem prostředí Active Directory, a pak klikněte na **Další**. 

    ![Operační systém zařízení](./media/hybrid-azuread-join-federated-domains/17.png)

8. Na stránce **Konfigurace federace** zadejte přihlašovací údaje správce vaší služby AD FS a pak klikněte na **Další**. 

    ![Konfigurace federace](./media/hybrid-azuread-join-federated-domains/18.png)

9. Na stránce **Připraveno ke konfiguraci** klikněte na **Konfigurovat**. 

    ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-federated-domains/19.png)

10. Na stránce **Dokončení konfigurace** klikněte na **Ukončit**. 

    ![Dokončení konfigurace](./media/hybrid-azuread-join-federated-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá z vašich zařízení připojených k doméně zařízení s Windows nižší úrovně, je potřeba provést následující:

- Aktualizace nastavení zařízení
 
- Konfigurace nastavení místního intranetu pro registraci zařízení


### <a name="update-device-settings"></a>Aktualizace nastavení zařízení 

Pokud chcete registrovat zařízení s Windows nižší úrovně, je potřeba zajistit nastavení zařízení, které uživatelům umožní registrovat zařízení v Azure AD. Na webu Azure Portal najdete toto nastavení v části:

`Home > [Name of your tenant] > Devices - Device settings`  


    
Následující zásada musí být nastavená na hodnotu **Všichni**: **Uživatelé můžou registrovat svoje zařízení do Azure AD**.

![Registrace zařízení](./media/hybrid-azuread-join-federated-domains/23.png)


### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Pokud chcete úspěšně dokončit hybridní připojení zařízení s Windows nižší úrovně k Azure AD a zabránit výzvám k zadání certifikátu při ověřování zařízení vůči Azure AD, můžete zařízením připojeným k doméně nabídnout zásadu, která přidá následující adresy URL do zóny Místní intranet v Internet Exploreru:

- `https://device.login.microsoftonline.com`

- `https://device.login.microsoftonline.com`

- Služba tokenů zabezpečení vaší organizace (STS – federované domény)

- `https://autologon.microsoftazuread-sso.com` (pro bezproblémové jednotné přihlašování)

Kromě toho je potřeba v zóně místního intranetu uživatele povolit možnost **Povolit aktualizace stavového řádku přes skript**.



## <a name="verify-the-registration"></a>Ověření registrace

K ověření stavu registrace zařízení v tenantovi Azure můžete použít rutinu **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** v **[modulu Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

Když použijete rutinu **Get-MSolDevice** ke kontrole podrobností služby:

- Musí existovat objekt s **ID zařízení**, které odpovídá ID v klientovi Windows.
- Hodnota **DeviceTrustType** (Stav důvěryhodnosti zařízení) musí být nastavená na **Domain Joined** (Připojeno k doméně). Jedná se o ekvivalent stavu **Hybridně připojeno k Azure AD** na stránce Zařízení na portálu Azure AD.
- U zařízení, která se používají k podmíněnému přístupu, musí být hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda) a hodnota **DeviceTrustLevel** (Úroveň důvěryhodnosti zařízení) musí být nastavená na **Managed** (Spravované). 


**Kontrola podrobností služby:**

1. Otevřete **Windows PowerShell** jako správce.

2. Zadejte `Connect-MsolService` a připojte se ke svému tenantovi Azure.  

3. Zadejte `get-msoldevice -deviceId <deviceId>`.

6. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).





## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud máte problémy s dokončením hybridního připojení k Azure AD pro zařízení s Windows připojená k doméně, přečtěte si následující témata:

- [Řešení potíží s hybridním připojením aktuálních zařízení s Windows k Azure AD](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridním připojením zařízení s Windows nižší úrovně k Azure AD](troubleshoot-hybrid-join-windows-legacy.md)



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace hybridního připojení k Azure Active Directory pro spravované domény](hybrid-azuread-join-managed-domains.md)
> [Ruční konfigurace hybridního připojení k Azure Active Directory](hybrid-azuread-join-manual-steps.md)




<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
