---
title: Uživatel přiřazený MSI na virtuální počítač s Linuxem používat pro přístup k Azure Storage
description: Kurz vás provede procesem pomocí uživatele přiřazené spravované služby Identity (MSI) na virtuální počítač s Linuxem pro přístup k úložišti Azure.
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
ms.openlocfilehash: dd82f1757d9c5a5fc8fb110cc36ec9f4bbd73e8a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Použít pro přístup k úložišti Azure přiřazený uživatelem spravované služby Identity (MSI) na virtuální počítač s Linuxem

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tento kurz ukazuje, jak vytvořit a použít přiřazený uživatelem spravované služby Identity (MSI) z virtuální počítač s Linuxem a pak ji použít k přístupu k Azure Storage. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření uživatele přiřazené identita spravované služby (MSI)
> * Přiřadit MSI přiřazené pro virtuální počítač s Linuxem
> * Udělení přístupu MSI k instanci Azure Storage
> * Získání přístupového tokenu pomocí Instalační služby MSI identity přiřazený uživatelem a použít ho k přístupu k Azure Storage

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

V tomto kurzu spustit příklady skriptu rozhraní příkazového řádku, máte dvě možnosti:

- Použití [prostředí cloudu Azure](~/articles/cloud-shell/overview.md) z portálu Azure nebo prostřednictvím tlačítko "Zkuste ho", umístěný v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

Nejprve vytvořte nový virtuální počítač s Linuxem. Můžete také povolit MSI na existující virtuální počítač, pokud dáváte přednost.

1. Klikněte **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**, vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

    ![Obrázek alternativní text](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Přiřazený uživatelem Instalační služby MSI přiřadit virtuálním počítačům s Linuxem

Na rozdíl od MSI přiřadit systému MSI se přiřazený uživatelem lze klienty na několik prostředků Azure. V tomto kurzu přiřazení pro jeden virtuální počítač. Můžete je také přiřadit k více než jeden virtuální počítač.

Soubor MSI přiřazený uživatelem přiřadit virtuálním počítačům s Linuxem pomocí [az virtuálních počítačů přiřazení identity](/cli/azure/vm#az_vm_assign_identity). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. Použití `id` vrácena vlastnost v předchozím kroku `--identities` hodnota parametru:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Pokud jste již nemáte, teď vytvořit účet úložiště. Také můžete tento krok přeskočit a použít existující účet úložiště, pokud dáváte přednost. 

1. Klikněte **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu portálu Azure.
2. Klikněte na tlačítko **úložiště**, pak **účet úložiště**, a zobrazí nový panel "Vytvořit účet úložiště".
3. Zadejte **název** pro účet úložiště, který použijete později.  
4. **Model nasazení** a **účet typu** musí být nastavena na "Resource manager" a "Obecné účely", v uvedeném pořadí. 
5. Ujistěte se, **předplatné** a **skupiny prostředků** odpovídat jsou zadány při vytvoření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvořit nový účet úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Vytvořte kontejner objektů blob v účtu úložiště

Protože soubory vyžadují úložiště objektů blob, musíte vytvořit kontejner objektů blob, ve kterém chcete soubor uložit. Potom nahrávání a stahování souboru ke kontejneru objektů blob v nový účet úložiště.

1. Přejděte zpět na vaše nově vytvořený účet úložiště.
2. Klikněte **kontejnery** odkaz na levé straně, v části "Služby objektů Blob."
3. Klikněte na tlačítko **+ kontejner** nahoře na stránce a "Nový kontejner" panely snímky limitu.
4. Zadejte název kontejneru, vybrat úroveň přístupu, a pak klikněte na tlačítko **OK**. Později v tomto kurzu se také používá název, který jste zadali. 

    ![Vytvoření kontejneru úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Nahrání souboru do nově vytvořený kontejner pak kliknutím na název kontejneru **nahrát**, vyberte soubor a pak klikněte na tlačítko **nahrát**.

    ![Nahrát textový soubor](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Udělení přístupu vaší MSI přiřadit uživatele k kontejner Azure Storage

Pomocí souboru MSI kódu můžete získat přístupové tokeny k ověřování k prostředkům, které podporují ověřování Azure AD. V tomto kurzu použijete Azure Storage.

Nejprve udělíte přístup identity MSI ke kontejneru úložiště Azure. V takovém případě použijete kontejneru vytvořili dříve. Aktualizujte hodnoty pro `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, a `<CONTAINER NAME>` jako vhodné pro vaše prostředí. Kromě toho nahradit `<MSI PRINCIPALID>` s `principalId` vlastnost vrácený `az identity create` v [vytvořit instalační služby MSI přiřazený uživatelem](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

Odpověď obsahuje podrobnosti o vytvoření přiřazení role:

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

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Získání přístupového tokenu pomocí identity MSI přiřazený uživatelem a použít jej k vyvolání Azure Storage

Pro zbývající část tohoto kurzu budete muset pracovat z virtuálního počítače, které jste vytvořili dříve.

K dokončení těchto kroků, potřebujete klientem SSH. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [jak klíče použití SSH se systémem Windows v Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu Azure přejděte do **virtuální počítače**, přejděte na Linux virtuálního počítače, pak z **přehled** klikněte na stránce **Connect** v horní části. Zkopírujte řetězce pro připojení k virtuálnímu počítači.
2. **Připojit** na virtuální počítač s klientem SSH podle svého výběru. 
3. V okně terminálu pomocí CURL, vytvořte žádost na místní koncový bod MSI k získání tokenu přístupu pro Azure Storage.

   CURL žádost získat přístupový token je znázorněno v následujícím příkladu. Nezapomeňte nahradit `<CLIENT ID>` s `clientId` vlastnost vrácený `az identity create` v [vytvořit instalační služby MSI přiřazený uživatelem](#create-a-user-assigned-msi):
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > V předchozí požadavek, hodnota `resource` parametr musí být přesná shoda pro očekávané službou Azure AD. Pokud používáte ID prostředku Azure Storage, je nutné zahrnout do adresy koncové lomítko, v identifikátoru URI.
   > V následující odpovědi, jako byla zkrácena jako stručný výtah element access_token.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Teď pomocí přístupového tokenu pro přístup k úložišti Azure, například ke čtení obsahu ukázkový soubor, který jste dříve nahráli do kontejneru. Nahraďte hodnoty `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, a `<FILE NAME>` s hodnotami, které jste zadali dříve, a `<ACCESS TOKEN>` s tokenem, vrátí se v předchozím kroku.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Odpověď obsahuje obsah souboru:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Další postup

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Další postup Tento stejný kurz pomocí úložiště pověření SAS najdete v tématu [používal Linux virtuálního počítače spravované služby identitu pro přístup k úložišti Azure pomocí pověření SAS](msi-tutorial-linux-vm-access-storage-sas.md)
- Další informace o funkci SAS účtu Azure Storage najdete v tématu:
  - [Použití sdílených přístupových podpisů (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Vytváření SAS služby](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.





