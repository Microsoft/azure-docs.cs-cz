---
title: Použití identity spravované služby na virtuálním počítači s Linuxem k přístupu k Azure Resource Manageru
description: Tento kurz vás povede při použití identity spravované služby na virtuálním počítači s Linuxem k přístupu k Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 6ef3c901005b34d7ae849a2358f1f8af42bb339b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887041"
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-resource-manager"></a>Použití identity spravované služby na virtuálním počítači s Linuxem k přístupu k Azure Resource Manageru

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto rychlém startu se dozvíte, jak pomocí identity přiřazené systémem pro virtuální počítač s Linuxem získat přístup k rozhraní API Azure Resource Manageru. Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolení identity spravované služby na virtuálním počítači s Linuxem 
> * Udělení přístupu virtuálnímu počítači ke skupině prostředků v Azure Resource Manageru 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Azure Resource Manageru 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Přihlášení k webu Azure Portal](https://portal.azure.com)

- [Vytvoření virtuálního počítače s Linuxem](/azure/virtual-machines/linux/quick-create-portal)

- [Povolení identity přiřazené systémem pro váš virtuální počítač](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="grant-your-vm-access-to-a-resource-group-in-azure-resource-manager"></a>Udělení přístupu virtuálnímu počítači ke skupině prostředků v Azure Resource Manageru 

Když použijete identitu spravované služby, může kód získat přístupové tokeny používané při ověření přístupu k prostředkům, které podporují ověřování Azure AD. Rozhraní API Azure Resource Manageru podporuje ověřování Azure AD. Nejdříve potřebujeme udělit identitě virtuálního počítače přístup k prostředku Azure Resource Manageru. V tomto případě se jedná o skupinu prostředků, ve které je virtuální počítač.  

1. Přejděte ke kartě **Skupiny prostředků**.
2. Vyberte konkrétní **skupinu prostředků**, kterou jste vytvořili dříve.
3. Na panelu vlevo přejděte na **Řízení přístupu (IAM)**.
4. Klikněte na **Přidat** a přiřaďte svému virtuálnímu počítači novou roli. V poli **Role** zvolte **Čtenář**.
5. V dalším rozevíracím seznamu **Přiřadit přístup k** vyberte prostředek **Virtuální počítač**.
6. Potom zkontrolujte, že je v rozevíracím seznamu **Předplatné** uvedené správné předplatné. A ve **skupině prostředků** vyberte **Všechny skupiny prostředků**.
7. Nakonec **vyberte** v rozevíracím seznamu svůj linuxový virtuální počítač a klikněte na **Uložit**.

    ![Text k alternativnímu obrázku](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Resource Manageru 

K dokončení tohoto postupu budete potřebovat klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc při konfiguraci klíčů klienta SSH, přečtěte si, [jak na počítači s Windows v Azure používat klíče SSH](../../virtual-machines/linux/ssh-from-windows.md) nebo [jak na linuxových virtuálních počítačích v Azure vytvářet a používat pár veřejného a privátního klíče SSH](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu přejděte ke svému linuxovému virtuálnímu počítači a v části **Přehled** klikněte na **Připojit**.  
2. **Připojte** se vybraným klientem SSH k virtuálnímu počítači. 
3. V okně terminálu pomocí nástroje CURL odešlete do místního koncového bodu identity spravované služby požadavek na přístupový token pro Azure Resource Manager.  
 
    Žádost CURL o přístupový token je níže.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
    
    > [!NOTE]
    > Hodnota parametru „resource“ musí přesně odpovídat hodnotě, kterou očekává Azure AD.  V ID prostředku Resource Manageru musí být v identifikátoru URI koncové lomítko. 
    
    V odpovědi je přístupový token, který potřebujete pro přístup k Azure Resource Manageru. 
    
    Odpověď:  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    Přístupový token můžete použít pro přístup k Azure Resource Manageru, například ke čtení podrobností o skupině prostředků, ke které jste virtuálnímu počítači udělili přístup. Hodnoty \<SUBSCRIPTION ID\>, \<RESOURCE GROUP\> a \<ACCESS TOKEN\> nahraďte dříve vytvořenými hodnotami. 
    
    > [!NOTE]
    > V adrese URL se rozlišují velká a malá písmena. Proto zkontrolujte, jestli používáte přesně stejná velká a malá písmena jako při pojmenování skupiny prostředků. Zkontrolujte také velké G ve výrazu „resourceGroup“.  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Vrácená odpověď s informacemi o konkrétní skupině prostředků: 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```     

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak vytvořit identitu přiřazenou uživateli a připojit ji k virtuálnímu počítači Azure kvůli přístupu k rozhraní API Azure Resource Manageru.  Další informace o Azure Resource Manageru:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)