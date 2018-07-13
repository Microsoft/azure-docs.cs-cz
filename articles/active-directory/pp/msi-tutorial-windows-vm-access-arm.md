---
title: Použití MSI virtuálního počítače Windows pro přístup k Azure Resource Manageru
description: Kurz vás provede procesem jeho použití Windows virtuální počítač Identity spravované služby (MSI) pro přístup k Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 089835d9c60780fb90f5c19c75c185ee0a908a25
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007271"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>Použít pro přístup k Resource Manageru Windows virtuální počítač Identity spravované služby (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak povolit Identity spravované služby (MSI) pro Windows virtuální počítač (VM). Potom můžete tuto identitu pro přístup k rozhraní API Azure Resource Manageru. Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření do služeb, které podporují ověřování Azure AD, aniž by bylo nutné vložit pověření do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolení MSI ve virtuálním počítači Windows 
> * Udělení přístupu vašich virtuálních počítačů do skupiny prostředků v Azure Resource Manageru 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít jej k vyvolání Azure Resource Manageru

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Windows.  Můžete také povolit MSI na existující virtuální počítač.

1.  Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu webu Azure portal.
2.  Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3.  Zadejte informace o virtuálním počítači. **Uživatelské jméno** a **heslo** vytvořený, tady je přihlašovací údaje použijete k přihlášení k virtuálnímu počítači.
4.  Zvolte správné **předplatné** pro virtuální počítač v rozevírací nabídce.
5.  Chcete-li vybrat nový **skupiny prostředků** ve kterém chcete vytvořit virtuální počítač, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6.  Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. Na stránce nastavení ponechte výchozí nastavení a klikněte na tlačítko **OK**.

    ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolení MSI ve virtuálním počítači 

MSI virtuálního počítače můžete k získání přístupových tokenů z Azure AD, aniž byste museli vložit pověření do kódu. Povolení MSI Azure zjistí, chcete-li vytvořit spravovanou identitu pro váš virtuální počítač. Pod pokličkou, povolení MSI provede dvě věci: nainstaluje rozšíření MSI virtuálního počítače na virtuálním počítači a umožňuje MSI v Azure Resource Manageru.

1.  Vyberte **virtuální počítač** , že chcete povolit na MSI.  
2.  Na navigačním panelu vlevo klikněte na tlačítko **konfigurace**. 
3.  Zobrazí **identita spravované služby**. Chcete-li zaregistrovat a povolit MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne. 
4.  Zajištění kliknutí na **Uložit** uložte konfiguraci.  
    ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat a ověřit, jaká rozšíření jsou na tomto virtuálním počítači, klikněte na tlačítko **rozšíření**. Pokud je povolené MSI, pak **ManagedIdentityExtensionforWindows** se zobrazí v seznamu.

    ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Udělení přístupu vašich virtuálních počítačů do skupiny prostředků v Resource Manageru
Použití MSI kódu můžete získat přístupové tokeny k ověření prostředků, které podporují ověřování Azure AD.  Azure Resource Manager podporuje ověřování Azure AD.  Nejdřív potřebujeme k udělení přístupu identity tento virtuální počítač k prostředků v Resource Manageru v tomto případě skupina prostředků, ve kterém se nachází virtuální počítač.  

1.  Přejděte na kartu pro **skupiny prostředků**. 
2.  Vyberte konkrétní **skupiny prostředků** jste vytvořili pro vaše **virtuálního počítače Windows**. 
3.  Přejděte na **řízení přístupu (IAM)** na levém panelu. 
4.  Potom **přidat** přiřazení nové role pro vaše **virtuálního počítače Windows**.  Zvolte **Role** jako **čtečky**. 
5.  V dalším rozevíracím seznamu **přiřadit přístup k** prostředek **virtuálního počítače**. 
6.  Dále, zajistit správné předplatné je uvedené v **předplatné** rozevíracího seznamu. A pro **skupiny prostředků**vyberte **všechny skupiny prostředků**. 
7.  Nakonec v **vyberte** zvolte v rozevíracím seznamu a klikněte na virtuální počítač Windows **Uložit**.

    ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a použít jej k vyvolání Azure Resource Manageru 

Budete muset použít **Powershellu** v této části.  Pokud nejsou nainstalována, si ho stáhnout [tady](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  Na portálu přejděte na **virtuálních počítačů** a přejděte ke svému virtuálnímu počítači Windows a v **přehled**, klikněte na tlačítko **připojit**. 
2.  Zadejte vaše **uživatelské jméno** a **heslo** pro které jste přidali po vytvoření virtuálního počítače Windows. 
3.  Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete **Powershellu** ve vzdálené relaci. 
4.  Použití Powershellu Invoke-WebRequest, vytvořte žádost na místním koncovým bodem MSI pro získání přístupového tokenu pro Azure Resource Manageru.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Hodnota parametru "prostředek" musí být přesná shoda pro co se očekává službou Azure AD. Při použití ID prostředku Azure Resource Manageru, je nutné zahrnout do adresy koncové lomítko v identifikátoru URI.
    
    V dalším kroku extrahujte úplnou odpověď, která je uložena jako řetězec JSON JavaScript Object Notation () ve formátu $response objektu. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    V dalším kroku extrahujte přístupový token z odpovědi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Nakonec proveďte volání Azure Resource Manageru pomocí přístupového tokenu. V tomto příkladu jsme také používáte Powershellu Invoke-WebRequest volání do Azure Resource Manageru a zahrnout přístupového tokenu v autorizační hlavičce.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > Adresa URL je velká a malá písmena, zajistěte proto, pokud používáte rozlišovat velikost písmen stejné, jako jste použili dříve při názvem skupiny prostředků a velká písmena "G" v "resourceGroups."
        
    Následující příkaz vrátí podrobnosti o skupině prostředků:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.

