---
title: Použití spravované identity přiřazené uživatelem na virtuálním počítači s Linuxem pro přístup k Azure Resource Manageru
description: Tento kurz vás provede procesem použití spravované identity přiřazené uživatelem na virtuálním počítači s Linuxem pro přístup k Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: priyamo
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 857991ee171dca8e579b1e6dbfd97551ee857530
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749913"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Kurz: Použití spravované identity přiřazené uživateli na virtuální počítač s Linuxem pro přístup k Azure Resource Manageru

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tento kurz vysvětluje, jak vytvořit spravovanou identitu přiřazenou uživatelem, přiřadit ji k virtuálnímu počítači s Linuxem a pak ji použít pro přístup k rozhraní API Azure Resource Manageru. Spravované identity pro prostředky Azure se spravují automaticky v Azure. Umožňují ověřování ve službách, které podporují ověřování Azure AD, bez nutnosti vložení přihlašovacích údajů do kódu. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření spravované identity přiřazené uživatelem
> * Přiřaďte spravovanou identitu přiřazenou uživatelem k virtuálnímu počítači s Linuxem 
> * Udělení přístupu spravované identitě přiřazené uživatelem ke skupině prostředků v Azure Resource Manageru 
> * Získání přístupového tokenu pomocí spravované identity přiřazené uživatelem a jeho použití k volání Azure Resource Manageru 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Přihlášení k webu Azure Portal](https://portal.azure.com)

- [Vytvoření virtuálního počítače s Linuxem](/azure/virtual-machines/linux/quick-create-portal)

- Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

1. Pokud používáte konzolu rozhraní příkazového řádku (místo relace služby Azure Cloud Shell), začněte přihlášením k Azure. Použijte účet přidružený k předplatnému Azure, ve kterém chcete vytvořit novou spravovanou identitu přiřazenou uživatelem:

    ```azurecli
    az login
    ```

2. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create). Parametr `-g` určuje skupinu prostředků, ve které se spravovaná identita přiřazená uživatelem vytvoří, a parametr `-n` určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<UAMI NAME>` vlastními hodnotami:
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

Odpověď bude obsahovat podrobnosti o vytvořené spravované identitě přiřazené uživatelem podobně jako v následujícím příkladu. Poznamenejte si hodnotu `id` pro vaši spravovanou identitu přiřazenou uživatelem, protože ji použijete v dalším kroku:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-a-user-assigned-managed-identity-to-your-linux-vm"></a>Přiřazení spravované identity přiřazené uživatelem k virtuálnímu počítači s Linuxem

Klienti můžou spravovanou identitu přiřazenou uživatelem používat pro několik prostředků Azure. Pomocí následujících příkazů přiřaďte spravovanou identitu přiřazenou uživatelem k jednomu virtuálnímu počítači. Jako hodnotu parametru `-IdentityID` použijte vlastnost `Id` vrácenou v předchozím kroku.

Přiřadit virtuálním počítačům s Linuxem pomocí spravované identity přiřazené uživateli [az vm identity přiřadit](/cli/azure/vm). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. Jako hodnotu parametru `--identities` použijte vlastnost `id` vrácenou v předchozím kroku.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-your-user-assigned-managed-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Udělení přístupu spravované identitě přiřazené uživatelem ke skupině prostředků v Azure Resource Manageru 

Spravované identity pro prostředky Azure poskytují identity, které můžete v kódu použít k odesílání požadavků na přístupové tokeny pro ověření v rozhraních API prostředků, která podporují ověřování Azure AD. V tomto kurzu budete v kódu přistupovat k rozhraní API Azure Resource Manageru.  

Než bude mít kód přístup k rozhraní API, je potřeba udělit identitě přístup k prostředku v Azure Resource Manageru. V tomto případě ke skupině prostředků, která obsahuje virtuální počítač. Aktualizujte hodnoty `<SUBSCRIPTION ID>` a `<RESOURCE GROUP>` odpovídajícím způsobem pro vaše prostředí. Kromě toho nahraďte `<UAMI PRINCIPALID>` vlastností `principalId` vrácenou příkazem `az identity create` v části [Vytvoření spravované identity přiřazené uživatelem](#create-a-user-assigned-managed-identity):

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
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

   Požadavek CURL pro získání přístupového tokenu je znázorněný v následujícím příkladu. Nezapomeňte nahradit `<CLIENT ID>` vlastností `clientId` vrácenou příkazem `az identity create` v části [Vytvoření spravované identity přiřazené uživatelem](#create-a-user-assigned-managed-identity): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Hodnota parametru `resource` musí přesně odpovídat hodnotě, kterou očekává Azure AD. Pokud použijete ID prostředku Resource Manageru, musíte v identifikátoru URI zahrnout koncové lomítko. 
    
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

5. Použijte přístupový token k přístupu k Azure Resource Manageru a čtení vlastností skupiny prostředků, ke které jste spravované identitě přiřazené uživatelem předtím udělili přístup. Nezapomeňte nahradit `<SUBSCRIPTION ID>` a `<RESOURCE GROUP>` hodnotami, které jste zadali dříve, a `<ACCESS TOKEN>` tokenem vráceným v předchozím kroku.

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
    
## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvořit spravovanou identitu přiřazenou uživatelem a připojit ji k virtuálnímu počítači s Linuxem kvůli přístupu k rozhraní API Azure Resource Manageru.  Další informace o Azure Resource Manageru:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

