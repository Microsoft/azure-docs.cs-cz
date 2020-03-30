---
title: Přiřazení popisků citlivosti skupinám – Azure AD | Dokumenty společnosti Microsoft
description: Jak vytvořit pravidla členství pro automatické vyplnění skupin a odkaz na pravidlo.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329728"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Přiřazení popisků citlivosti skupinám Office 365 ve službě Azure Active Directory (preview)

Azure Active Directory (Azure AD) podporuje použití popisků citlivosti publikovaných [centrem dodržování předpisů Microsoftu 365](https://sip.protection.office.com/homepage) pro skupiny Office 365. Popisky citlivosti platí pro skupiny mezi službami, jako je Outlook, Microsoft Teams a SharePoint. Tato funkce je aktuálně ve verzi Public Preview. Další informace o podpoře aplikací Office 365 najdete v tématu [Podpora popisků citlivosti v Office 365](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> Chcete-li tuto funkci nakonfigurovat, musí být ve vaší organizaci Azure AD alespoň jedna aktivní licence Služby Azure Active Directory Premium P1.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Povolení podpory popisků citlivosti v PowerShellu

Chcete-li na skupiny použít publikované popisky, musíte nejprve tuto funkci povolit. Tyto kroky povolit funkci ve službě Azure AD.

1. Otevřete v počítači okno prostředí Windows PowerShell. Můžete ji otevřít bez zvýšených oprávnění.
1. Spuštěním následujících příkazů se připravte na spouštění rutin.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Na stránce **Přihlásit se ke svému účtu** zadejte účet správce a heslo, abyste se připojili ke službě, a **vyberte Přihlásit se**.
1. Načtení aktuálního nastavení skupiny pro organizaci Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Pokud pro tuto organizaci Azure AD nebyla vytvořena žádná nastavení skupiny, musíte nejprve vytvořit nastavení. Postupujte podle pokynů v [rutinách Služby Azure Active Directory pro konfiguraci nastavení skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) k vytvoření nastavení skupiny pro tuto organizaci Azure AD.

1. Dále zobrazte aktuální nastavení skupiny.

    ```PowerShell
    $Setting.Values
    ```

1. Poté tuto funkci povolte:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Pak uložte změny a použijte nastavení:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

A to je vše. Tuto funkci jste povolili a publikované štítky můžete použít na skupiny.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Přiřazení štítku nové skupině na webu Azure Portal

1. Přihlaste se do [Centra pro správu Azure AD](https://aad.portal.azure.com).
1. Vyberte **Skupiny**a potom vyberte **Nová skupina**.
1. Na stránce **Nová skupina** vyberte **Office 365**a vyplňte požadované informace pro novou skupinu a ze seznamu vyberte popisek citlivosti.

   ![Přiřazení popisku citlivosti na stránce Nové skupiny](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Uložte změny a vyberte **Vytvořit**.

Vaše skupina je vytvořena a nastavení webu a skupiny přidružené k vybranému popisku se pak automaticky vynucují.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Přiřazení popisku existující skupině na webu Azure Portal

1. Přihlaste se do [Centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce skupin nebo jako vlastník skupiny.
1. Vyberte **skupiny**.
1. Na stránce **Všechny skupiny** vyberte skupinu, kterou chcete označit.
1. Na stránce vybrané skupiny vyberte **Vlastnosti** a ze seznamu vyberte popisek citlivosti.

   ![Přiřazení popisku citlivosti na stránce přehledu pro skupinu](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Vyberte **Uložit** a uložte tak provedené změny.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Odebrání popisku z existující skupiny na webu Azure Portal

1. Přihlaste se do [Centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce globálního správce nebo skupin nebo vlastníka skupiny.
1. Vyberte **skupiny**.
1. Na stránce **Všechny skupiny** vyberte skupinu, ze které chcete popisek odebrat.
1. Na stránce **Skupina** vyberte **Vlastnosti**.
1. Vyberte **Odebrat**.
1. Vyberte **Uložit**, aby se tyto změny použily.

## <a name="using-classic-azure-ad-classifications"></a>Použití klasických klasifikací Azure AD

Po povolení této funkce se "klasické" klasifikace pro skupiny zobrazí pouze existující skupiny a weby a měli byste je použít pro nové skupiny pouze v případě, že vytváříte skupiny v aplikacích, které nepodporují popisky citlivosti. Správce je může v případě potřeby později převést na popisky citlivosti. Klasické klasifikace jsou staré klasifikace, které nastavíte `ClassificationList` definováním hodnot pro nastavení v prostředí Azure AD PowerShell. Pokud je tato funkce povolena, nebudou tyto klasifikace použity pro skupiny.

## <a name="troubleshooting-issues"></a>Řešení potíží

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Popisky citlivosti nejsou k dispozici pro přiřazení ve skupině.

Možnost popisku citlivosti je zobrazena pouze pro skupiny, pokud jsou splněny všechny následující podmínky:

1. Popisky jsou publikovány v Centru dodržování předpisů Microsoft 365 pro tohoto klienta.
1. Tato funkce je povolena, EnableMIPLabels je nastavena na True v prostředí PowerShell.
1. Skupina je skupina Office 365.
1. Klient má aktivní licenci Azure Active Directory Premium P1.
1. Aktuální přihlášený uživatel má dostatečná oprávnění k přiřazení popisků. Uživatel musí být globální správce, správce skupiny nebo vlastník skupiny.

Ujistěte se, že jsou splněny všechny podmínky, abyste mohli přiřadit štítky skupině.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Popisek, který chci přiřadit, není v seznamu

Pokud popisek, který hledáte, není v seznamu, může tomu tak být z jednoho z následujících důvodů:

- Popisek nemusí být publikován v Centru dodržování předpisů Microsoft 365. To by se mohlo vztahovat i na popisky, které již nejsou publikovány. Další informace získáte od správce.
- Popisek může být publikován, ale není k dispozici uživateli, který je přihlášen. Další informace o tom, jak získat přístup k štítku, získáte od správce.

### <a name="how-to-change-the-label-on-a-group"></a>Jak změnit popisek ve skupině

Popisky lze kdykoli vyměnit stejným způsobem jako přiřazení popisku existující skupině takto:

1. Přihlaste se do [Centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce globálního nebo skupinového správce nebo jako vlastník skupiny.
1. Vyberte **skupiny**.
1. Na stránce **Všechny skupiny** vyberte skupinu, kterou chcete označit.
1. Na stránce vybrané skupiny vyberte **Vlastnosti** a ze seznamu vyberte nový popisek citlivosti.
1. Vyberte **Uložit**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Změny nastavení skupiny publikovaných popisků nejsou ve skupinách aktualizovány.

Jako osvědčený postup nedoporučujeme měnit nastavení skupiny pro popisek po označení se použije na skupiny. Pokud provedete změny nastavení skupiny přidružené k publikovaným popiskům v [Centru dodržování předpisů microsoftu 365](https://sip.protection.office.com/homepage), nebudou tyto změny zásad automaticky použity na ovlivněné skupiny.

Pokud musíte provést změnu, použijte [skript Prostředí Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) ručně použít aktualizace pro ovlivněné skupiny. Tato metoda zajišťuje, že všechny existující skupiny vynucují nové nastavení.

## <a name="next-steps"></a>Další kroky

- [Použití popisků citlivosti s Microsoft Teams, skupinami Office 365 a weby SharePointu](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Aktualizace skupin po ruční změně zásad popisků pomocí skriptu Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Úprava nastavení skupiny](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Správa skupin pomocí powershellových příkazů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
