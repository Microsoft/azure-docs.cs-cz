---
title: "Použít virtuální počítač s Linuxem přiřazený uživatelem MSI pro přístup k Azure Resource Manager"
description: "Kurz vás provede procesem pomocí User-Assigned spravované služby Identity (MSI) na virtuální počítač s Linuxem, přístup k Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c2b6d70e441dc3d300f49adff1c02d7cc65788d2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Používat přiřazený uživatelem spravované služby Identity (MSI) na virtuální počítač s Linuxem, pro přístup k Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tento kurz vysvětluje, jak vytvořit přiřadit uživatele spravované služby Identity (MSI), přiřaďte k Linux virtuálního počítače (VM) a potom tuto identitu používat pro přístup k rozhraní API služby Azure Resource Manager. 

Identita spravované služby je automaticky prováděna nástrojem Azure. Umožňují ověřování pro služby, které podporují ověřování Azure AD, aniž by museli přihlašovací údaje pro vložení do vašeho kódu.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření MSI se přiřazený uživatelem
> * Soubor MSI přiřadit virtuálního počítače s Linuxem 
> * Udělení přístupu MSI do skupiny prostředků ve službě Správce prostředků Azure 
> * Získání přístupového tokenu pomocí soubor MSI a použít jej k vyvolání Azure Resource Manager 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

V tomto kurzu spustit příklady skriptu rozhraní příkazového řádku, máte dvě možnosti:

- Použití [prostředí cloudu Azure](~/articles/cloud-shell/overview.md) z portálu Azure nebo prostřednictvím tlačítko "Zkuste ho", umístěný v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure v [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

Pro tento kurz je nejprve vytvořit nový virtuální počítač s Linuxem. Můžete se také rozhodnout použít existující virtuální počítač.

1. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**, vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

    ![Vytvoření virtuálního počítače s Linuxem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Vyberte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač lze vytvořit v, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte typ filtru disku podporované. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

## <a name="create-a-user-assigned-msi"></a>Vytvoření MSI se přiřazený uživatelem

1. Pokud používáte rozhraní příkazového řádku konzoly (ne relaci prostředí cloudu Azure), spusťte po přihlášení k Azure. Používáte účet, který je přidružen k předplatnému Azure, ve kterém chcete vytvořit nový soubor MSI:

    ```azurecli
    az login
    ```

2. Vytvoření přiřazený uživatelem MSI pomocí [vytvoření az identity](/cli/azure/identity#az_identity_create). `-g` Parametr určuje skupinu prostředků, kde se má vytvořit soubor MSI, a `-n` parametr určuje její název. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<MSI NAME>` hodnoty parametrů s vlastními hodnotami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Odpověď obsahuje podrobnosti pro MSI přiřazený uživatelem vytvořené, podobně jako v následujícím příkladu. Poznámka: `id` hodnota MSI, jak se použije v dalším kroku:

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Přiřazený uživatelem Instalační služby MSI přiřadit virtuálním počítačům s Linuxem

Na rozdíl od MSI přiřadit systému MSI se přiřazený uživatelem lze klienty na několik prostředků Azure. V tomto kurzu přiřazení pro jeden virtuální počítač. Můžete je také přiřadit k více než jeden virtuální počítač.

Soubor MSI přiřazený uživatelem přiřadit virtuálním počítačům s Linuxem pomocí [az virtuálních počítačů přiřazení identity](/cli/azure/vm#az_vm_assign_identity). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. Použití `id` vrácena vlastnost v předchozím kroku `--identities` hodnota parametru:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Udělit přístup přiřazený uživatelem MSI do skupiny prostředků ve službě Správce prostředků Azure 

MSI poskytuje kódu s přístupový token k ověření na prostředek rozhraní API, které podporují ověřování Azure AD. V tomto kurzu váš kód přistupuje k rozhraní API služby Azure Resource Manager. 

Váš kód mohli dostat k rozhraní API, když, budete muset udělit MSI identity přístup k prostředku ve službě Správce prostředků Azure. V tomto případě skupině prostředků, ve kterém se nachází virtuální počítač. Aktualizujte hodnoty pro `<SUBSCRIPTION ID>` a `<RESOURCE GROUP>` jako vhodné pro vaše prostředí. Kromě toho nahradit `<MSI PRINCIPALID>` s `principalId` vlastnost vrácený `az identity create` v [vytvořit instalační služby MSI přiřazený uživatelem](#create-a-user-assigned-msi):

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

K dokončení těchto kroků, potřebujete klientem SSH. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [jak klíče použití SSH se systémem Windows v Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu Azure přejděte do **virtuální počítače**, přejděte na Linux virtuálního počítače, pak z **přehled** klikněte na stránce **Connect** v horní části. Zkopírujte řetězce pro připojení k virtuálnímu počítači.
2. **Připojit** na virtuální počítač s klientem SSH podle svého výběru.  
3. V okně terminálu pomocí CURL, vytvořte žádost na místní koncový bod MSI se získat přístupový token pro Azure Resource Manager.  

   CURL žádost získat přístupový token je znázorněno v následujícím příkladu. Nezapomeňte nahradit `<CLIENT ID>` s `clientId` vlastnost vrácený `az identity create` v [vytvořit instalační služby MSI přiřazený uživatelem](#create-a-user-assigned-msi): 
    
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

4. Teď pomocí přístupového tokenu pro přístup k Azure Resource Manager a číst vlastnosti služby skupiny prostředků, do které dříve udělen přístup MSI přiřazený uživatelem. Nezapomeňte nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` s hodnotami, které jste zadali dříve, a `<ACCESS TOKEN>` s tokenem, vrátí se v předchozím kroku.

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

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
