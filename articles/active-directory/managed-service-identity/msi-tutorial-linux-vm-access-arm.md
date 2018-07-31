---
title: Použití uživatelem přiřazené identity spravované služby na virtuálním počítači s Linuxem k přístupu k Azure Resource Manageru
description: Tento kurz vás provede procesem použití uživatelem přiřazené identity spravované služby na virtuálním počítači s Linuxem k přístupu k Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c2735d385b0a3c2201ec2dad83c0c32fe44d458c
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258239"
---
# <a name="tutorial-use-a-user-assigned-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Kurz: Použití identity přiřazené uživatelem na virtuálním počítači s Linuxem pro přístup k Azure Resource Manageru

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tento kurz vysvětluje, jak vytvořit identitu přiřazenou uživatelem, přiřadit ji k virtuálnímu počítači s Linuxem a pak ji použít pro přístup k rozhraní API Azure Resource Manageru. Identity spravovaných služeb se spravují automaticky v Azure. Umožňují ověřování ve službách, které podporují ověřování Azure AD, bez nutnosti vložení přihlašovacích údajů do kódu. 

Identity spravovaných služeb se spravují automaticky v Azure. Umožňují ověřování ve službách, které podporují ověřování Azure AD, bez nutnosti vložení přihlašovacích údajů do kódu.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření identity přiřazené uživatelem
> * Přiřazení identity přiřazené uživatelem k virtuálnímu počítači s Linuxem 
> * Udělení přístupu identitě přiřazené uživatelem ke skupině prostředků v Azure Resource Manageru 
> * Získání přístupového tokenu pomocí identity přiřazené uživatelem a jeho použití k volání Azure Resource Manageru 

## <a name="prerequisites"></a>Požadavky

- Pokud Identitu spravované služby neznáte, projděte si část [Přehled](overview.md). **Nezapomeňte si prostudovat [rozdíly mezi systémovými a uživatelem přiřazenými identitami](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- K provedení kroků v tomto kurzu potřebných k vytvoření prostředku a správě rolí potřebuje váš účet oprávnění vlastníka v odpovídajícím oboru (vaše předplatné nebo skupina prostředků). Pokud potřebujete pomoc s přiřazením role, přečtěte si téma [Použití řízení přístupu na základě role ke správě přístupu k prostředkům předplatného Azure](/azure/role-based-access-control/role-assignments-portal).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Linuxem v nové skupině prostředků

V tomto kurzu nejprve vytvoříte nový virtuální počítač s Linuxem. Můžete použít také existující virtuální počítač.

1. V levém horním rohu webu Azure Portal klikněte na **Vytvořit prostředek**.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. V poli **Typ ověřování** vyberte **Veřejný klíč SSH** nebo **Heslo**. Vytvořené přihlašovací údaje umožňují přihlásit se k virtuálnímu počítači.

    ![Vytvoření virtuálního počítače s Linuxem](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. V rozevíracím seznamu zvolte pro virtuální počítač **Předplatné**.
5. Pokud chcete vybrat novou **skupinu prostředků**, ve které chcete vytvořit virtuální počítač, zvolte **Vytvořit novou**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr Podporovaný typ disku. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

## <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživatelem

1. Pokud používáte konzolu rozhraní příkazového řádku (místo relace služby Azure Cloud Shell), začněte přihlášením k Azure. Použijte účet přidružený k předplatnému Azure, ve kterém chcete vytvořit novou identitu přiřazenou uživatelem:

    ```azurecli
    az login
    ```

2. Vytvořte identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az_identity_create). Parametr `-g` určuje skupinu prostředků, ve které se identita spravované služby vytvoří, a parametr `-n` určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<MSI NAME>` vlastními hodnotami:
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <MSI NAME>
```

Odpověď bude obsahovat podrobnosti o vytvořené identitě přiřazené uživatelem podobně jako v následujícím příkladu. Poznamenejte si hodnotu `id` pro vaši identitu přiřazenou uživatelem, protože ji použijete v dalším kroku:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
"location": "westcentralus",
"name": "<MSI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-a-user-assigned-identity-to-your-linux-vm"></a>Přiřazení identity přiřazené uživatelem k virtuálnímu počítači s Linuxem

Klienti můžou identitu přiřazenou uživatelem používat pro několik prostředků Azure. Pomocí následujících příkazů přiřaďte identitu přiřazenou uživatelem k jednomu virtuálnímu počítači. Jako hodnotu parametru `-IdentityID` použijte vlastnost `Id` vrácenou v předchozím kroku.

Přiřaďte identitu spravované služby přiřazenou uživatelem k virtuálnímu počítači s Linuxem pomocí příkazu [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. Jako hodnotu parametru `--identities` použijte vlastnost `id` vrácenou v předchozím kroku.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Udělení přístupu identitě přiřazené uživatelem ke skupině prostředků v Azure Resource Manageru 

Identita spravované služby (MSI) poskytuje identity, které můžete v kódu použít k odesílání požadavků na přístupové tokeny pro ověření v rozhraních API prostředků, která podporují ověřování Azure AD. V tomto kurzu budete v kódu přistupovat k rozhraní API Azure Resource Manageru.  

Než bude mít kód přístup k rozhraní API, je potřeba udělit identitě přístup k prostředku v Azure Resource Manageru. V tomto případě ke skupině prostředků, která obsahuje virtuální počítač. Aktualizujte hodnoty `<SUBSCRIPTION ID>` a `<RESOURCE GROUP>` odpovídajícím způsobem pro vaše prostředí. Kromě toho nahraďte `<MSI PRINCIPALID>` vlastností `principalId` vrácenou příkazem `az identity create` v části [Vytvoření MSI přiřazené uživatelem](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Odpověď bude obsahovat podrobnosti o vytvořeném přiřazení role podobně jako v následujícím příkladu:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Resource Manageru 

Ve zbývající části kurzu použijeme k práci dříve vytvořený virtuální počítač.

K dokončení tohoto postupu potřebujete klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Linuxem a v části **Přehled** klikněte na **Připojit**. Zkopírujte řetězec pro připojení k vašemu virtuálnímu počítači.
3. Připojte se vybraným klientem SSH k virtuálnímu počítači. Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc při konfiguraci klíčů klienta SSH, přečtěte si, [jak na počítači s Windows v Azure používat klíče SSH](~/articles/virtual-machines/linux/ssh-from-windows.md) nebo [jak na linuxových virtuálních počítačích v Azure vytvářet a používat pár veřejného a privátního klíče SSH](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. V okně terminálu pomocí nástroje CURL odešlete do koncového bodu identity služby Azure Instance Metadata Service (IMDS) žádost o přístupový token Azure Resource Manageru.  

   Požadavek CURL pro získání přístupového tokenu je znázorněný v následujícím příkladu. Nezapomeňte nahradit `<CLIENT ID>` vlastností `clientId` vrácenou příkazem `az identity create` v části [Vytvoření identity přiřazené uživatelem](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Hodnota parametru `resource` musí přesně odpovídat hodnotě, kterou očekává Azure AD. Pokud použijete ID prostředku Resource Manageru, musíte v identifikátoru URI zahrnout koncové lomítko. 
    
    V odpovědi je přístupový token, který potřebujete pro přístup k Azure Resource Manageru. 
    
    Příklad odpovědi:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Použijte přístupový token k přístupu k Azure Resource Manageru a čtení vlastností skupiny prostředků, ke které jste identitě spravované uživatelem předtím udělili přístup. Nezapomeňte nahradit `<SUBSCRIPTION ID>` a `<RESOURCE GROUP>` hodnotami, které jste zadali dříve, a `<ACCESS TOKEN>` tokenem vráceným v předchozím kroku.

    > [!NOTE]
    > V adrese URL se rozlišují velká a malá písmena. Proto zkontrolujte, jestli používáte přesně stejná velká a malá písmena, jaká jste použili při pojmenování skupiny prostředků, a zkontrolujte také velké G ve výrazu `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Odpověď bude obsahovat informace o konkrétní skupině prostředků podobně jako v následujícím příkladu: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit identitu přiřazenou uživatelem a připojit ji k virtuálnímu počítači s Linuxem kvůli přístupu k rozhraní API Azure Resource Manageru.  Další informace o Azure Resource Manageru:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

