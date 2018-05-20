---
title: Použít virtuální počítač s Linuxem přiřazený uživatelem MSI pro přístup k Azure Resource Manager
description: Kurz vás provede procesem pomocí User-Assigned spravované služby Identity (MSI) na virtuální počítač s Linuxem, přístup k Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6d4f7378ccd24af4281793dbc93df40830a1b31a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-a-user-assigned-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Kurz: Použijte identitu uživatele přiřazené na virtuální počítač s Linuxem, pro přístup k Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tento kurz vysvětluje, jak vytvořit uživatele přiřazené identity, přiřaďte k Linux virtuálního počítače (VM) a potom tuto identitu používat pro přístup k rozhraní API služby Azure Resource Manager. Identita spravované služby je automaticky prováděna nástrojem Azure. Umožňují ověřování pro služby, které podporují ověřování Azure AD, aniž by museli přihlašovací údaje pro vložení do vašeho kódu. 

Identita spravované služby je automaticky prováděna nástrojem Azure. Umožňují ověřování pro služby, které podporují ověřování Azure AD, aniž by museli přihlašovací údaje pro vložení do vašeho kódu.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření uživatele přiřazené identity
> * Přiřaďte uživatele přiřazené identity virtuálního počítače s Linuxem 
> * Udělení přístupu identity přiřazeného uživatele do skupiny prostředků ve službě Správce prostředků Azure 
> * Získání přístupového tokenu pomocí přiřazeného identity uživatele a použít jej k vyvolání Azure Resource Manager 

## <a name="prerequisites"></a>Požadavky

- Pokud vaše jsou obeznámeni s identita spravované služby, podívejte se [přehled](overview.md) části. **Nezapomeňte si přečíst [rozdíly mezi systémem a uživatel přiřazené identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.
- Při vytváření požadovaný prostředek a kroky správy role v tomto kurzu, musí váš účet oprávnění "Vlastník" v příslušné oboru (skupina vaše předplatné nebo prostředek). Pokud potřebujete pomoc s přiřazení role, přečtěte si [Use Role-Based řízení přístupu ke správě přístupu k prostředkům předplatného Azure](/azure/role-based-access-control/role-assignments-portal).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

Pro tento kurz je nejprve vytvořit nový virtuální počítač s Linuxem. Můžete se také rozhodnout použít existující virtuální počítač.

1. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**, vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

    ![Vytvoření virtuálního počítače s Linuxem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Vyberte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač lze vytvořit v, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte typ filtru disku podporované. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

## <a name="create-a-user-assigned-identity"></a>Vytvoření uživatele přiřazené identity

1. Pokud používáte rozhraní příkazového řádku konzoly (ne relaci prostředí cloudu Azure), spusťte po přihlášení k Azure. Použijte účet, který je přidružený předplatnému Azure, ve kterém chcete vytvořit nový uživatel s přiřazenou identity:

    ```azurecli
    az login
    ```

2. Vytvoření identity přiřazený uživatelem pomocí [vytvoření az identity](/cli/azure/identity#az_identity_create). `-g` Parametr určuje skupinu prostředků, kde se má vytvořit soubor MSI, a `-n` parametr určuje její název. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<MSI NAME>` hodnoty parametrů s vlastními hodnotami:
    
    > [!IMPORTANT]
    > Vytvoření identity uživatele přiřazené podporuje pouze alfanumerické znaky a spojovníky (0 – 9 nebo a-z nebo A-Z nebo -) znaků. Kromě toho název by měl být omezený na 24 znaků pro přiřazení virtuálního počítače nebo VMSS správně fungovat. Vraťte se zpět pro aktualizace. Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Odpověď obsahuje podrobnosti o uživatel s přiřazenou identity vytvořený, podobně jako v následujícím příkladu. Poznámka: `id` hodnoty pro své uživatele přiřazené identity, jak se použije v dalším kroku:

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

## <a name="assign-a-user-assigned-identity-to-your-linux-vm"></a>Přiřazení uživatele přiřazené identity k virtuálním počítačům s Linuxem

Uživatel přiřazený identity lze klienty na několik prostředků Azure. Použijte následující příkazy identity uživatele přiřazené přiřadit jeden virtuální počítač. Použití `Id` vrácena vlastnost v předchozím kroku `-IdentityID` parametr.

Soubor MSI přiřazený uživatelem přiřadit virtuálním počítačům s Linuxem pomocí [az virtuálních počítačů přiřazení identity](/cli/azure/vm#az_vm_assign_identity). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. Použití `id` vrácena vlastnost v předchozím kroku `--identities` hodnotu parametru.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Udělit přístup identity přiřazeného uživatele do skupiny prostředků ve službě Správce prostředků Azure 

Identita spravované služby (MSI) poskytuje identity, které váš kód můžete vyžádat přístupové tokeny k ověřování na prostředek rozhraní API, ověření podpory Azure AD. V tomto kurzu bude váš kód přístup k rozhraní API služby Azure Resource Manager.  

Váš kód mohli dostat k rozhraní API, musíte přidělit identitě přístup k prostředku ve službě Správce prostředků Azure. V tomto případě skupině prostředků, ve kterém se nachází virtuální počítač. Aktualizujte hodnotu pro `<SUBSCRIPTION ID>` a `<RESOURCE GROUP>` jako vhodné pro vaše prostředí. Kromě toho nahradit `<MSI PRINCIPALID>` s `principalId` vlastnost vrácený `az identity create` v [vytvořit instalační služby MSI přiřazený uživatelem](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Odpověď obsahuje podrobné informace o přiřazení role vytvořený, podobně jako v následujícím příkladu:

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

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít jej k vyvolání Resource Manager 

Pro zbývající část tohoto kurzu budeme pracovat z virtuálního počítače, které jsme vytvořili předtím.

K dokončení těchto kroků, potřebujete klientem SSH. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). 

1. Přihlaste se k Azure [portál](https://portal.azure.com).
2. Na portálu, přejděte na **virtuální počítače** a přejděte na virtuální počítač Linux a v **přehled**, klikněte na tlačítko **Connect**. Zkopírujte řetězce pro připojení k virtuálnímu počítači.
3. Připojte k virtuálnímu počítači pomocí SSH klienta podle vašeho výběru. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [jak klíče použití SSH se systémem Windows v Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. V okně terminálu pomocí CURL, vytvořte žádost na koncový bod Azure Instance Metadata služby (IMDS) identity k získání přístupu tokenu pro Azure Resource Manager.  

   CURL žádost získat přístupový token je znázorněno v následujícím příkladu. Nezapomeňte nahradit `<CLIENT ID>` s `clientId` vlastnost vrácený `az identity create` v [vytvoření identity uživatele přiřazené](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Hodnota `resource` parametr musí být přesná shoda pro očekávané službou Azure AD. Pokud používáte ID prostředku Resource Manager, je nutné zahrnout do adresy koncové lomítko, v identifikátoru URI. 
    
    Odpověď obsahuje přístupový token, potřebujete získat přístup k Azure Resource Manager. 
    
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

5. Pomocí přístupového tokenu pro přístup k Azure Resource Manager a číst vlastnosti služby skupiny prostředků, do které jste dříve udělen přístup identity vaší přiřazeného uživatele. Nezapomeňte nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` s hodnotami, které jste zadali dříve, a `<ACCESS TOKEN>` s tokenem, vrátí se v předchozím kroku.

    > [!NOTE]
    > Adresa URL je malá a velká písmena, takže je nutné přesný velká a malá písmena jste použili dříve, když jste s názvem skupiny prostředků a velká písmena "G" v `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Odpověď obsahuje konkrétní informace skupinu prostředků, podobně jako v následujícím příkladu: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Další postup

- Přehled identita spravované služby najdete v tématu [přehled](overview.md).

