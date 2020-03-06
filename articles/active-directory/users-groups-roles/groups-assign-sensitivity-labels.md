---
title: Přiřazení popisků citlivosti skupinám – Azure AD | Microsoft Docs
description: Jak vytvořit pravidla členství pro automatické naplňování skupin a odkaz na pravidlo.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78329728"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Přiřazení popisků citlivosti skupinám Office 365 v Azure Active Directory (Preview)

Azure Active Directory (Azure AD) podporuje použití popisků citlivostí publikovaných [centrem dodržování předpisů Microsoft 365](https://sip.protection.office.com/homepage) do skupin Office 365. Popisky citlivosti se vztahují na skupinu napříč službami, jako je Outlook, Microsoft teams a SharePoint. Tato funkce je aktuálně ve verzi Public Preview. Další informace o podpoře aplikací Office 365 najdete v článku [Podpora sady office 365 pro popisky citlivosti](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> Pokud chcete tuto funkci nakonfigurovat, musí být ve vaší organizaci Azure AD aspoň jedna licence Active Azure Active Directory Premium P1.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Povolit podporu popisku citlivosti v PowerShellu

Pokud chcete použít publikované popisky na skupiny, musíte ji nejdřív povolit. Tyto kroky povolují funkci ve službě Azure AD.

1. Otevřete v počítači okno prostředí Windows PowerShell. Můžete ho otevřít bez zvýšených oprávnění.
1. Spuštěním následujících příkazů se připravte na spouštění rutin.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Na stránce **Přihlásit se k účtu** zadejte účet správce a heslo, abyste se připojili ke službě, a vyberte **Přihlásit**se.
1. Načte aktuální nastavení skupiny pro organizaci Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Pokud se pro tuto organizaci Azure AD nevytvořilo žádné nastavení skupiny, musíte nejdřív vytvořit nastavení. Podle pokynů v části [Azure Active Directory rutiny nakonfigurujte nastavení skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) pro vytvoření nastavení skupiny pro tuto organizaci Azure AD.

1. Potom zobrazte aktuální nastavení skupiny.

    ```PowerShell
    $Setting.Values
    ```

1. Pak funkci povolte:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Pak změny uložte a použijte nastavení:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

A to je vše. Tuto funkci jste povolili a můžete použít publikované popisky na skupiny.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Přiřazení popisku nové skupině v Azure Portal

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com).
1. Vyberte **skupiny**a pak vyberte **Nová skupina**.
1. Na stránce **Nová skupina** vyberte **Office 365**a potom vyplňte požadované informace pro novou skupinu a v seznamu vyberte popisek citlivosti.

   ![Přiřazení popisku citlivosti na stránce nové skupiny](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Uložte změny a vyberte **vytvořit**.

Vaše skupina se vytvoří a automaticky se vynutila nastavení lokality a skupiny přidružená k vybranému popisku.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Přiřazení popisku existující skupině v Azure Portal

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce skupin nebo jako vlastníkem skupiny.
1. Vyberte **skupiny**.
1. Na stránce **všechny skupiny** vyberte skupinu, kterou chcete označit.
1. Na stránce vybrané skupiny vyberte **vlastnosti** a v seznamu vyberte popisek citlivosti.

   ![Přiřazení popisku citlivosti na stránce Přehled pro skupinu](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Kliknutím na **Uložit** uložte změny.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Odebrání popisku z existující skupiny v Azure Portal

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu globálního správce nebo skupiny správce nebo jako vlastníkem skupiny.
1. Vyberte **skupiny**.
1. Na stránce **všechny skupiny** vyberte skupinu, ze které chcete odebrat popisek.
1. Na stránce **Skupina** vyberte možnost **vlastnosti**.
1. Vyberte **Odebrat**.
1. Pokud chcete změny použít, vyberte **Uložit** .

## <a name="using-classic-azure-ad-classifications"></a>Používání klasifikací Azure AD Classic

Po povolení této funkce se "klasické" klasifikace pro skupiny zobrazí jenom existující skupiny a lokality a měli byste je používat pro nové skupiny jenom v případě, že vytváříte skupiny v aplikacích, které nepodporují popisky citlivosti. Správce je může v případě potřeby převést na popisky citlivosti později. Standardní klasifikace jsou staré klasifikace, které jste nastavili definováním hodnot pro nastavení `ClassificationList` ve službě Azure AD PowerShell. Když je tato funkce povolená, nebudou se tyto klasifikace u skupin použít.

## <a name="troubleshooting-issues"></a>Řešení potíží

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Popisky citlivosti nejsou k dispozici pro přiřazení ke skupině.

Možnost popisek citlivosti se zobrazí pouze pro skupiny, pokud jsou splněny všechny následující podmínky:

1. Štítky jsou publikovány v centru dodržování předpisů Microsoft 365 pro tohoto tenanta.
1. Funkce je povolená, EnableMIPLabels je v PowerShellu nastavená na true.
1. Skupina je skupina Office 365.
1. Tenant má aktivní licenci Azure Active Directory Premium P1.
1. Aktuálně přihlášený uživatel má dostatečná oprávnění k přiřazování popisků. Uživatel musí být buď globální správce, správce skupiny, nebo vlastník skupiny.

Ujistěte se prosím, že jsou splněné všechny podmínky, aby bylo možné přiřadit popisky ke skupině.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Popisek, který chci přiřadit, není v seznamu.

Pokud popisek, který hledáte, není v seznamu, může to být případ z některého z následujících důvodů:

- Popisek se nemusí publikovat v centru kompatibility Microsoft 365. To se může vztahovat i na popisky, které už nejsou publikované. Další informace najdete u správce.
- Popisek může být publikovaný, ale není dostupný pro uživatele, který je přihlášený. Další informace o tom, jak získat přístup k popisku, získáte u správce.

### <a name="how-to-change-the-label-on-a-group"></a>Změna popisku skupiny

Popisky lze kdykoli měnit pomocí stejných kroků, jako přiřazení popisku existující skupině, a to následujícím způsobem:

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí globálního účtu nebo účtu správce skupiny nebo jako vlastníkem skupiny.
1. Vyberte **skupiny**.
1. Na stránce **všechny skupiny** vyberte skupinu, kterou chcete označit.
1. Na stránce vybrané skupiny vyberte **vlastnosti** a v seznamu vyberte nový popisek citlivosti.
1. Vyberte **Save** (Uložit).

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Změny nastavení skupiny u publikovaných popisků se ve skupinách neaktualizují.

Jako osvědčený postup doporučujeme, abyste po použití popisku na skupiny nezměnili nastavení skupiny pro popisek. Když provedete změny nastavení skupiny přidruženého k publikovaným popiskům v [centru dodržování předpisů Microsoft 365](https://sip.protection.office.com/homepage), tyto změny zásad se na ovlivněné skupiny automaticky nepoužijí.

Pokud je třeba provést změnu, použijte [skript Azure AD PowerShellu](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) k ručnímu provedení aktualizací pro ovlivněné skupiny. Tato metoda zajistí, že nové nastavení vynutila všechny existující skupiny.

## <a name="next-steps"></a>Další kroky

- [Použití popisků citlivosti s Microsoft teams, skupinami Office 365 a weby služby SharePoint](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Aktualizace skupin po ruční změně popisku pomocí skriptu Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Úprava nastavení skupiny](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Správa skupin pomocí powershellových příkazů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
