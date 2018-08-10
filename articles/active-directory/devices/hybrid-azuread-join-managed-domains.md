---
title: Jak nakonfigurovat hybridní služby Azure Active Directory zařízení připojená k | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zařízení Azure Active Directory připojená k hybridní.
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
ms.topic: article
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 55818bf3c6997925fbac32f913d573d630bc20f4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004374"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Kurz: Konfigurace hybridní připojení k Azure Active Directory pro spravované domény

Podobným způsobem pro uživatele zařízení se mění jiné identity, které chcete chránit a také použít k ochraně vašich prostředků na libovolný čas a umístění. Dosažení tohoto cíle tak, že vaše zařízení identit do služby Azure AD pomocí jedné z následujících metod:

- Připojení k Azure AD
- Připojení k hybridní službě Azure AD
- Registrace Azure AD

Přeneste zařízení do služby Azure AD, maximalizovat produktivitu vašich uživatelů prostřednictvím jednotného přihlašování (SSO) ve vašich cloudových a místních prostředků. Ve stejnou dobu, můžete zabezpečit přístup do cloudu a místních prostředků pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md).

V tomto kurzu se dozvíte, jak nakonfigurovat připojení k hybridní službě Azure AD službě pro zařízení ve spravovaných doménách.

> [!div class="checklist"]
> * Konfigurace připojení k hybridní službě Azure AD
> * Povolení zařízením s Windows nižší úrovně
> * Ověřit zařízení připojené k doméně 
> * Řešení potíží 


## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že máte zkušenosti s:
    
-  [Úvod do správy zařízení ve službě Azure Active Directory](../device-management-introduction.md)
    
-  [Plánování implementace hybridního připojení Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Jak řídit How hybridní připojení Azure AD pro vaše zařízení](hybrid-azuread-join-control.md)
  

Chcete-li nakonfigurovat scénář v tomto článku, musíte [nejnovější verzi služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 nebo vyšší) k instalaci. 
 

Počínaje verzí 1.1.819.0, Azure AD Connect poskytuje průvodce ke konfiguraci připojení k hybridní službě Azure AD. Průvodce umožňuje výrazně zjednodušuje proces konfigurace. Související Průvodce nakonfiguruje spojovací body služby (SCP) pro registraci zařízení.

Jednotlivé kroky konfigurace v tomto článku jsou podle tohoto průvodce. 

Připojení k hybridní službě Azure AD vyžaduje, aby zařízení mít přístup k následujícím prostředkům společnosti Microsoft z v síti vaší organizace:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- https://autologon.microsoftazuread-sso.com (Pokud používáte nebo plánujete použít bezproblémové jednotné přihlašování)

Pokud vaše organizace potřebuje přístup k Internetu prostřednictvím odchozího proxy serveru, od Windows 10 1709, můžete nakonfigurovat nastavení proxy serveru na počítači pomocí objektu zásad skupiny (GPO). Pokud v počítači běží starší než Windows 10 1709 cokoli, je nutné implementovat Proxy Auto-Discovery WPAD (Web) umožňující počítače s Windows 10 postup registrace zařízení ve službě Azure AD. 

Pokud vaše organizace potřebuje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, musí Ujistěte se, že vaše počítače s Windows 10 můžete provádět ověření odchozího proxy serveru. Protože počítače s Windows 10 spusťte registraci zařízení pomocí místní počítač, je nutné nakonfigurovat odchozího proxy serveru ověřování pomocí místní počítač. Proveďte u svého poskytovatele odchozího proxy serveru na požadavky na konfiguraci. 



## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace připojení k hybridní službě Azure AD

Pokud chcete konfigurovat připojení k Azure AD hybridní pomocí služby Azure AD Connect, budete potřebovat:

- Přihlašovací údaje globálního správce pro vašeho tenanta Azure AD.  

- Přihlašovací údaje podnikového správce pro jednotlivé doménové struktury.


**Ke konfiguraci připojení k hybridní službě Azure AD pomocí služby Azure AD Connect:**

1. Spusťte Azure AD Connect a pak klikněte na tlačítko **konfigurovat**.

    ![Uvítání](./media/hybrid-azuread-join-managed-domains/11.png)

2. Na **další úkoly** stránce **konfigurovat možnosti zařízení**a potom klikněte na tlačítko **Další**. 

    ![Další úlohy](./media/hybrid-azuread-join-managed-domains/12.png)

3. Na **přehled** klikněte na **Další**. 

    ![Přehled](./media/hybrid-azuread-join-managed-domains/13.png)

4. Na **připojit ke službě Azure AD** stránky, zadejte přihlašovací údaje globálního správce pro vašeho tenanta Azure AD.  

    ![Připojení k Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

5. Na **možnosti zařízení** stránce **konfigurovat hybridní službě Azure AD join**a potom klikněte na tlačítko **Další**. 

    ![Možnosti zařízení](./media/hybrid-azuread-join-managed-domains/15.png)

6. Na **spojovací bod služby** stránky pro každou doménovou strukturu chcete, aby Azure AD Connect pro konfiguraci spojovacího bodu služby, proveďte následující kroky a pak klikněte na tlačítko **Další**: 

    ![Spojovací bod služby](./media/hybrid-azuread-join-managed-domains/16.png)

    a. Vyberte doménovou strukturu.

    b. Vyberte ověřovací služby.

    c. Klikněte na tlačítko **přidat** zadat přihlašovací údaje podnikového správce.


7. Na **operační systémy zařízení** stránky, vyberte operační systémy používané zařízení ve vašem prostředí služby Active Directory a potom klikněte na **Další**. 

    ![Operační systém zařízení](./media/hybrid-azuread-join-managed-domains/17.png)


8. Na **připraveno ke konfiguraci** klikněte na **konfigurovat**. 

    ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-managed-domains/19.png)

9. Na **konfiguraci Dokončit** klikněte na **ukončovací**. 

    ![Dokončení konfigurace](./media/hybrid-azuread-join-managed-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Povolení zařízením s Windows nižší úrovně

Pokud jsou některé z vašich zařízení připojených k doméně Windows nižší úrovně zařízení, budete muset:

- Aktualizovat nastavení zařízení
 
- Konfigurace nastavení místního intranetu k registraci zařízení

### <a name="update-device-settings"></a>Aktualizovat nastavení zařízení 

K registraci zařízení s Windows nižší úrovně, budete muset Ujistěte se, že jsou nastavené nastavení zařízení a povolit uživatelům registraci zařízení ve službě Azure AD. Na webu Azure Portal, tato nastavení najdete v části:

`Home > [Name of your tenant] > Devices - Device settings`  


    
Tyto zásady musí být nastaveno na **všechny**: **uživatelé můžou registrovat svoje zařízení s Azure AD**

![Registrace zařízení](media/hybrid-azuread-join-managed-domains/23.png)



### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu k registraci zařízení

K úspěšnému dokončení hybridní připojení ke službě Azure AD z vašich zařízení Windows nižší úrovně a aby se zabránilo ověření výzev ohledně certifikátů při ověřování zařízení do služby Azure AD, můžete odeslat zásady do zařízení připojených k doméně Chcete-li přidat následující adresy URL místního intranetu zóna v aplikaci Internet Explorer:

- `https://device.login.microsoftonline.com`

- `https://autologon.microsoftazuread-sso.com`.

Kromě toho je potřeba povolit **povolit aktualizace stavového řádku prostřednictvím skriptu** do zóny místního intranetu uživatele.

## <a name="verify-the-registration"></a>Ověření registrace

Pokud chcete ověřit stav registrace zařízení ve vašem tenantovi Azure, můžete použít ** [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) ** rutiny v ** [modul Powershellu pro Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

Při použití **Get-MSolDevice** rutiny pro kontrolu podrobností služby:

- Objekt se **id zařízení** , který odpovídá ID na Windows, klient musí existovat.
- Hodnota pro **DeviceTrustType** musí být **připojený k doméně**. Jde o ekvivalent **připojená k hybridní službě Azure AD** stavu na stránce zařízení na portálu Azure AD.
- Hodnota pro **povoleno** musí být **True** pro zařízení, která se používají v podmíněného přístupu. 


**Chcete-li zkontrolovat podrobnosti o služby:**

1. Otevřít **prostředí Windows PowerShell** jako správce.

2. Typ `Connect-MsolService` pro připojení k tenantovi Azure.  

3. Zadejte `get-msoldevice -deviceId <deviceId>`.

6. Ověřte, že **povoleno** je nastavena na **True**.





## <a name="troubleshoot-your-implementation"></a>Řešení potíží s vaší implementace

Pokud dochází k problémům s dokončením hybridní připojení ke službě Azure AD pro doménu zařízení připojená k Windows, naleznete v tématu:

- [Řešení potíží s hybridní službě Azure AD join pro aktuální zařízení s Windows](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s připojení k hybridní službě Azure AD pro zařízení s Windows nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace připojení k hybridní službě Azure Active Directory službě u federovaných domén](hybrid-azuread-join-federated-domains.md)
> [ruční konfigurace připojení k hybridní službě Azure Active Directory](hybrid-azuread-join-manual-steps.md)

