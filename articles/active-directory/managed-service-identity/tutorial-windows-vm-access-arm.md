---
title: Použití MSI virtuálního počítače s Windows pro přístup k Azure Resource Manageru
description: Tento kurz vás provede procesem použití identity spravované služby (MSI) virtuálního počítače s Windows pro přístup k Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 7466c3ca87ed47b6d7dfe3d725197d3a6027fdf9
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901013"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>Použití identity spravované služby (MSI) virtuálního počítače s Windows k přístupu k Resource Manageru

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak povolit identitu spravované služby (MSI) pro virtuální počítač s Windows. Tuto identitu pak můžete použít pro přístup k rozhraní API Azure Resource Manageru. Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolení MSI na virtuálním počítači s Windows 
> * Udělení přístupu virtuálnímu počítači ke skupině prostředků v Azure Resource Manageru 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Azure Resource Manageru

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows v nové skupině prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Windows.  MSI také můžete povolit na stávajícím virtuálním počítači.

1.  Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2.  Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3.  Zadejte informace o virtuálním počítači. Vytvořené **Uživatelské jméno** a **Heslo** použijete při přihlášení k virtuálnímu počítači.
4.  V rozevíracím seznamu zvolte pro virtuální počítač správné **předplatné**.
5.  Pokud chcete vybrat novou **skupinu prostředků**, ve které se má virtuální počítač vytvořit, zvolte **Vytvořit novou**. Jakmile budete hotovi, klikněte na **OK**.
6.  Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. Na stránce Nastavení ponechte výchozí nastavení a klikněte na **OK**.

    ![Text k alternativnímu obrázku](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolení MSI na virtuálním počítači 

Funkce MSI virtuálního počítače umožňuje získat z Azure AD přístupové tokeny bez nutnosti vložení přihlašovacích údajů do kódu. Když na virtuálním počítači povolíte MSI, stanou se dvě věci: virtuální počítač se zaregistruje v Azure Active Directory, aby se vytvořila jeho spravovaná identita, a tato identita se nakonfiguruje na virtuálním počítači.

1.  Vyberte **virtuální počítač**, na kterém chcete povolit MSI.  
2.  Na navigačním panelu vlevo klikněte na **Konfigurace**. 
3.  Zobrazí se **Identita spravované služby**. Pokud chcete funkci MSI zaregistrovat a povolit, vyberte **Ano**. Pokud ji chcete zakázat, vyberte Ne. 
4.  Nezapomeňte konfiguraci uložit kliknutím na **Uložit**.  
    ![Alternativní text k obrázku](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Udělení přístupu virtuálnímu počítači ke skupině prostředků v Resource Manageru
Když použijete MSI, může kód získat přístupové tokeny používané při ověření přístupu k prostředkům, které podporují ověřování Azure AD.  Azure Resource Manager podporuje ověřování Azure AD.  Nejdříve potřebujeme udělit identitě virtuálního počítače přístup k prostředku v Resource Manageru. V tomto případě se jedná o skupinu prostředků, do které patří virtuální počítač.  

1.  Přejděte ke kartě **Skupiny prostředků**. 
2.  Vyberte **skupinu prostředků**, kterou jste pro **virtuální počítač s Windows** vytvořili. 
3.  Na panelu vlevo přejděte na **Řízení přístupu (IAM)**. 
4.  Klikněte na **Přidat** a přiřaďte svému **virtuálnímu počítači s Windows** novou roli.  V poli **Role** zvolte **Čtenář**. 
5.  V dalším rozevíracím seznamu **Přiřadit přístup k** vyberte prostředek **Virtuální počítač**. 
6.  Potom zkontrolujte, že je v rozevíracím seznamu **Předplatné** uvedené správné předplatné. A ve **skupině prostředků** vyberte **Všechny skupiny prostředků**. 
7.  Nakonec **vyberte** v rozevíracím seznamu svůj virtuální počítač s Windows a klikněte na **Uložit**.

    ![Text k alternativnímu obrázku](../media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Azure Resource Manageru 

V této části budete muset použít **PowerShell**.  Pokud ho nemáte nainstalovaný, stáhněte si ho [odtud](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  Na portálu přejděte na **Virtuální počítače**, přejděte na svůj virtuální počítač s Windows a v části **Přehled** klikněte na **Připojit**. 
2.  Zadejte své **uživatelské jméno** a **heslo**, které jste přidali při vytváření virtuálního počítače s Windows. 
3.  Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete ve vzdálené relaci **PowerShell**. 
4.  Pomocí Invoke-WebRequest v PowerShellu požádejte místní koncový bod MSI o přístupový token pro Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Hodnota parametru „resource“ musí přesně odpovídat hodnotě, kterou očekává Azure AD. Při použití ID prostředku Azure Resource Manageru musí být v identifikátoru URI koncové lomítko.
    
    V dalším kroku extrahujte úplnou odpověď, která je uložena jako formátovaný řetězec JSON (JavaScript Object Notation) v objektu $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Potom z odpovědi extrahujte přístupový token.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Nakonec proveďte pomocí přístupového tokenu volání Azure Resource Manageru. V tomto příkladu také používáme Invoke-WebRequest v PowerShellu pro provedení volání do Azure Resource Manageru a zahrnujeme přístupový token do autorizační hlavičky.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > V adrese URL se rozlišují velká a malá písmena. Proto zkontrolujte, jestli používáte přesně stejná velká a malá písmena, jaká jste použili při pojmenování skupiny prostředků, a zkontrolujte také velké G ve výrazu „resourceGroup“.
        
    Následující příkaz vrátí podrobnosti o skupině prostředků:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak vytvořit identitu přiřazenou uživateli a připojit ji k virtuálnímu počítači Azure kvůli přístupu k rozhraní API Azure Resource Manageru.  Další informace o Azure Resource Manageru:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

