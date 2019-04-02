---
title: Vytvořit tenanta ve Windows Virtual Desktop Preview – Azure
description: Popisuje, jak nastavit službu Windows Virtual Desktop Preview tenantů ve službě Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 1c66b3de9e18cb74c43f20499e4065c7ec7ae5ca
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801671"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Kurz: Vytvořit tenanta v náhledu virtuální plochy Windows

Vytvoření tenanta v náhledu virtuální plochy Windows je prvním krokem k vytvoření řešení virtualizace plochy. Tenant je skupina jednoho nebo několika fondů hostitele. Každý hostitel fond se skládá z více hostitelů relací, spuštěná jako virtuální počítače v Azure a zaregistrován ke službě Windows virtuálního klienta. Každý hostitel fond obsahuje také jeden nebo více skupin aplikací, které se používají k publikování zdrojů Vzdálená plocha a Vzdálená aplikace pro uživatele. S tenantem sestavení hostitele fondy, vytvořit skupiny aplikací, přiřazení uživatelů a připojovat prostřednictvím služby.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Oprávnění grant Azure Active Directory ve službě Windows virtuálního klienta.
> * Přiřazení role aplikace TenantCreator na uživatele ve vašem tenantovi Azure Active Directory.
> * Vytvoření tenanta virtuální plochy Windows.

Zde je, co potřebujete k nastavení tenanta virtuální plochy Windows:

* [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) tenant ID pro Windows Virtual Desktop users.
* Účet globálního správce tenanta Azure Active Directory.
   * To platí také pro vytvoření virtuálního klienta Windows tenanta pro své zákazníky organizace Cloud Solution Provider (CSP). Pokud jste v organizaci zprostředkovatele kryptografických služeb, musíte být schopni se přihlásit jako globální správce Azure Active Directory zákazníka.
   * Účet správce musí pocházet z tenanta Azure Active Directory, ve které se snažíte vytvořit tenanta virtuální plochy Windows. Tento proces nepodporuje Azure Active Directory s B2B účty (Host).
   * Účet správce musí být pracovní nebo školní účet.
* ID předplatného Azure

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Oprávnění grant Azure Active Directory na službu Windows Virtual Desktop Preview

Pokud jste už udělili oprávnění k virtuálnímu klientovi Windows Azure Active Directory, můžete tuto část přeskočte.

Udělení oprávnění ke službě Windows virtuální plochy umožníte dotazování služby Azure Active Directory pro správu a úlohách pro koncové uživatele.

Udělení oprávnění služby:

1. Otevřete prohlížeč a připojte se k [stránka pro odsouhlasení podmínek virtuální plochy Windows](https://rdweb.wvd.microsoft.com).
2. Pro **souhlas možnost** > **aplikace Server**, zadejte název tenanta Azure Active Directory nebo ID adresáře a pak vyberte **odeslat**.
        ID je pro zákazníky, kteří poskytovatele Cloud Solution Provider, Microsoft ID zákazníka z portálu pro partnery. Podnikoví zákazníci ID se nachází v části **Azure Active Directory** > **vlastnosti** > **ID adresáře**.
3. Přihlaste se k virtuálnímu klientovi Windows stránka pro odsouhlasení podmínek pomocí účtu globálního správce. Například pokud jste v rámci organizace Contoso, váš účet může být admin@contoso.com nebo admin@contoso.onmicrosoft.com.  
4. Vyberte **Přijmout**.
5. Vyčkat, než jedna minuta.
6. Přejděte zpět [stránka pro odsouhlasení podmínek virtuální plochy Windows](https://rdweb.wvd.microsoft.com).
7. Přejděte na **souhlas možnost** > **klientskou aplikaci**, zadejte stejný název tenanta Azure AD nebo ID adresáře a pak vyberte **odeslat**.
8. Přihlaste se k virtuálnímu klientovi Windows stránka pro odsouhlasení podmínek jako globální správce, stejně jako v kroku 3.
9. Vyberte **Přijmout**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Přiřazení role aplikace TenantCreator uživatel ve vašem tenantovi Azure Active Directory

Přiřazení uživatele služby Azure Active Directory umožňuje TenantCreator aplikační role tohoto uživatele k vytvoření virtuálního klienta Windows tenanta přidružený k Azure Active Directory. Bude potřeba přiřadit roli TenantCreator pomocí účtu globálního správce.

Přiřazení role aplikace TenantCreator pomocí účtu globálního správce:

1. Otevřete prohlížeč a připojte se k [portálu Azure Active Directory](https://aad.portal.azure.com) pomocí účtu globálního správce.
   - Pokud pracujete s více tenanty Azure AD, je osvědčeným postupem je otevřete relaci prohlížeče privátní a zkopírujte a vložte do adresy URL.
2. Vyberte **podnikové aplikace**, vyhledejte **virtuální plochy Windows**. Uvidíte dvě aplikace, které jste zadali souhlas pro v předchozí části. Tyto dvě aplikace, vyberte **virtuální plochy Windows**.
3. Vyberte **uživatelů a skupin**a pak vyberte **přidat uživatele**.
4. Vyberte uživatele a skupiny v **přidat přiřazení** okno
5. Vyhledejte uživatelský účet, který vytvoří váš tenant virtuální plochy Windows.
   - Pro jednoduchost může to být účet globálního správce.
6. Vyberte uživatelský účet, klikněte na tlačítko **vyberte** tlačítko a pak vyberte **přiřadit**.

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Vytvoření klienta Windows virtuální plochy, ve verzi Preview

Teď, když máte oprávnění služby virtuální plochy Windows k dotazování služby Azure Active Directory a přiřazenou roli TenantCreator uživatelský účet, můžete vytvořit klienta virtuální plochy Windows.

Nejprve je potřeba [stáhnout a naimportovat modul virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

Přihlaste se k virtuálnímu klientovi Windows pomocí uživatelského účtu TenantCreator s touto rutinou:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Potom vytvořte nového tenanta virtuální plochy Windows přidružené k tenantovi Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Hodnoty v závorkách mělo být nahrazeno s hodnotami, které jsou relevantní pro vaši organizaci a tenanta. Řekněme například, že jste to skutečně TenantCreator virtuální plochy Windows pro organizaci Contoso. Rutiny, které by spuštění bude vypadat takto:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Další postup

Po vytvoření vašeho tenanta, bude nutné vytvořit fond hostitele. Další informace o fondech hostitele, pokračujte ke kurzu pro vytváření hostitele fondu ve virtuální plochy Windows.

> [!div class="nextstepaction"]
> [Kurz fondu hostitele virtuálního klienta Windows](./create-host-pools-azure-marketplace.md)
