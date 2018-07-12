---
title: Použít uživatel přiřazený na virtuální počítač s Linuxem MSI pro přístup k Azure Storage
description: Kurz vás provede procesem pomocí uživatel přiřazené Identity spravované služby (MSI) na virtuální počítač s Linuxem pro přístup k úložišti Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4a1a2d0c40012649f6cd89193fd3f704f325e38a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611040"
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Použít uživatelsky přiřazené Identity spravované služby (MSI) na virtuální počítač s Linuxem pro přístup k Azure Storage

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak vytvořit a používat uživatelsky přiřazené Identity spravované služby (MSI) z virtuálního počítače s Linuxem a pak použít pro přístup k úložišti Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření uživatele přiřazeny Identity spravované služby (MSI)
> * Přiřadit uživateli přiřazena MSI pro virtuální počítač s Linuxem
> * Udělení přístupu MSI k instanci služby Azure Storage
> * Získání přístupového tokenu pomocí uživatelsky přiřazené identity MSI a používat ho pro přístup k Azure Storage

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Spuštění ukázkové skripty rozhraní příkazového řádku v tomto kurzu, máte dvě možnosti:

- Použití [Azure Cloud Shell](~/articles/cloud-shell/overview.md) z webu Azure portal nebo přes tlačítko "Vyzkoušet", nachází v pravém horním rohu každý blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější) Pokud byste radši chtěli použít místní konzoly příkazového řádku.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Linuxem do nové skupiny prostředků

Nejprve vytvořte nový virtuální počítač s Linuxem. Pokud dáváte přednost, můžete také povolit MSI na existující virtuální počítač.

1. Klikněte na tlačítko **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu webu Azure portal.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Zvolte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač, aby se v aplikaci, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Chcete-li zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte filtr typu disku podporované. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

## <a name="create-a-user-assigned-msi"></a>Vytvořit instalační služby MSI přiřazená uživatelem

1. Pokud používáte rozhraní příkazového řádku konzoly (namísto Azure Cloud Shell relace), začněte tím, že přihlášení do Azure. Použijte účet, který je přidružený k předplatnému Azure, ve kterém chcete vytvořit nový soubor MSI:

    ```azurecli
    az login
    ```

2. Vytvoření uživatelsky přiřazené MSI pomocí [vytvoření az identity](/cli/azure/identity#az_identity_create). `-g` Parametr určuje skupinu prostředků, ve kterém se vytvoří soubor MSI, a `-n` parametr určuje její název. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<MSI NAME>` parametr hodnoty vlastními hodnotami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Odpověď obsahuje podrobnosti pro uživatelsky přiřazené MSI vytvořený, podobně jako v následujícím příkladu. Poznámka: `id` hodnota MSI, jak se použije v dalším kroku:

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Přiřadit uživateli přiřazena Instalační služby MSI virtuálního počítače s Linuxem

Na rozdíl od systém přiřadil MSI je možné instalační služby MSI uživatelsky přiřazené klienty na několika prostředcích Azure. Pro účely tohoto kurzu můžete ji přiřadit k jeden virtuální počítač. Můžete je také přiřadit k více než jednomu virtuálnímu počítači.

Přiřadit MSI uživatelsky přiřazené k virtuálním počítačům s Linuxem pomocí [az vm přiřadit identity](/cli/azure/vm#az-vm-identity-assign). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` parametr hodnoty vlastními hodnotami. Použití `id` vlastnost vrátila v předchozím kroku pro `--identities` hodnota parametru:

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Pokud již nemáte, nyní vytvořit účet úložiště. Také můžete tento krok přeskočit a použít existující účet úložiště, který preferujete. 

1. Klikněte na tlačítko **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu webu Azure portal.
2. Klikněte na tlačítko **úložiště**, pak **účtu úložiště**, a zobrazí nový panel "Vytvoření účtu úložiště".
3. Zadejte **název** pro účet úložiště, které použijete později.  
4. **Model nasazení** a **druh účtu** musí být nastavená na "Resource Manageru" a "Obecné použití", v uvedeném pořadí. 
5. Zkontrolujte **předplatné** a **skupiny prostředků** shodovat s těmi, které jste zadali při vytváření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvořit nový účet úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Vytvořte kontejner objektů blob v účtu úložiště

Protože soubory vyžadují úložiště objektů blob, je potřeba vytvořit kontejner objektů blob, do kterého chcete soubor uložit. Potom nahrávání a stahování souboru do kontejneru objektů blob v rámci nového účtu úložiště.

1. Přejděte zpět do vaší nově vytvořený účet úložiště.
2. Klikněte na tlačítko **kontejnery** odkazu na levé straně v části "Blob service."
3. Klikněte na tlačítko **+ kontejner** horní části stránky a "Nový kontejner" vysune.
4. Zadejte název kontejneru, vyberte úroveň přístupu a pak klikněte na tlačítko **OK**. Zadaný název se také používá v pozdější části kurzu. 

    ![Vytvoření kontejneru úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Nahrání souboru do nově vytvořený kontejner pak kliknutím na název kontejneru **nahrát**, vyberte soubor a pak klikněte na tlačítko **nahrát**.

    ![Nahrát textový soubor](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Udělit přístup uživatele přiřazeny MSI do kontejneru služby Azure Storage

Pomocí MSI, může váš kód získat přístupové tokeny pro mohli ověřovat prostředky, které podporují ověřování Azure AD. V tomto kurzu použijete Azure Storage.

Nejdřív udělit MSI identitě přístup ke kontejneru služby Azure Storage. V tomto případě použijete kontejneru, který vytvořili dříve. Aktualizujte hodnoty pro `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, a `<CONTAINER NAME>` pro vaše prostředí vhodný. Kromě toho nahradit `<MSI PRINCIPALID>` s `principalId` vlastnosti vrácené `az identity create` v příkaz [vytvořit instalační služby MSI uživatelsky přiřazené](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

Odpověď obsahuje podrobnosti o přiřazení role vytvořené:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Získání přístupového tokenu pomocí MSI uživatelsky přiřazené identity a použít jej k vyvolání služby Azure Storage

Pro zbývající část tohoto kurzu budete muset pracovat z virtuálního počítače, které jste vytvořili dříve.

K dokončení těchto kroků, budete potřebovat klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc, konfigurace klíčů vašeho klienta SSH, přečtěte si téma [jak používat klíče SSH s Windows v Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), nebo [postupy vytváření a používání veřejných a privátních pár klíčů SSH pro virtuální počítače s Linuxem v Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Na webu Azure Portal, přejděte na **virtuálních počítačů**, přejděte na virtuální počítač s Linuxem, pak z **přehled** stránce klikněte na **připojit** v horní části. Zkopírujte řetězec pro připojení k vašemu virtuálnímu počítači.
2. **Připojit** k virtuálnímu počítači s klientem SSH podle vašeho výběru. 
3. V okně terminálu pomocí příkazu CURL, ujistěte se, požadavek na místní koncový bod MSI pro získání přístupového tokenu pro službu Azure Storage.

   Požadavek CURL k získání přístupového tokenu je znázorněno v následujícím příkladu. Nezapomeňte nahradit `<CLIENT ID>` s `clientId` vlastnosti vrácené `az identity create` v příkaz [vytvořit instalační služby MSI uživatelsky přiřazené](#create-a-user-assigned-msi):
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > V předchozí žádosti o výhody `resource` parametr musí být přesná shoda pro co se očekává službou Azure AD. Při použití ID prostředku Azure Storage, je nutné zahrnout do adresy koncové lomítko v identifikátoru URI.
   > V následující odpověď access_token element jako byla zkrácena pro zkrácení.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Teď pomocí přístupového tokenu pro přístup k úložišti Azure, třeba když chcete číst obsah ukázkového souboru, který byl dříve odeslán do kontejneru. Nahradit hodnoty `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, a `<FILE NAME>` hodnotami, které jste zadali dříve, a `<ACCESS TOKEN>` pomocí tokenu vrácenému v předchozím kroku.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Odpověď obsahuje obsah souboru:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Další postup

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Další postup Tento stejný kurz s použitím úložiště pověření SAS najdete v tématu [použít Linux VM identita spravované služby pro přístup k Azure Storage pomocí pověření SAS](msi-tutorial-linux-vm-access-storage-sas.md)
- Další informace o funkci SAS účtu Azure Storage najdete v tématu:
  - [Použití sdílených přístupových podpisů (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Vytváření SAS služby](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.





