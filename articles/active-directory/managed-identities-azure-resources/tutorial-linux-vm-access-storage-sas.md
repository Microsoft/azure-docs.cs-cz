---
title: Použití spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Storage prostřednictvím pověření SAS
description: V tomto kurzu se dozvíte, jak použít spravovanou identitu přiřazenou systémem na virtuálním počítači s Linuxem pro přístup k Azure Storage s pověřením SAS místo přístupového klíče účtu úložiště.
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 32fa417d5fcb5c8547dfc7c10b34b3c97ed70559
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626061"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-identity-to-access-azure-storage-via-a-sas-credential"></a>Kurz: Použití spravované přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Storage prostřednictvím pověření SAS

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem na virtuálním počítači s Linuxem získat pověření sdíleného přístupového podpisu (SAS) úložiště. Konkrétně se bude jednat o [pověření SAS služby](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

SAS služby poskytuje možnost získat po omezenou dobu omezený přístup k objektům v účtu úložiště pro konkrétní službu (v našem případě službu Blob service) bez zveřejnění přístupového klíče účtu. Pověření SAS můžete použít obvyklým způsobem při operacích s úložištěm, třeba při použití sady SDK služby Storage. V tomto kurzu si ukážeme nahrání a stažení objektu blob pomocí rozhraní příkazového řádku Azure Storage. V tomto kurzu se naučíte:


> [!div class="checklist"]
> * vytvořit účet úložiště
> * Vytvoření kontejneru objektů blob v účtu úložiště
> * Udělení přístupu k SAS účtu úložiště v Resource Manageru pro virtuální počítač 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení SAS z Resource Manageru 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Teď vytvoříte účet úložiště (pokud ho ještě nemáte).  Tento krok také můžete přeskočit a udělit spravované identitě přiřazené systémem virtuálního počítače přístup ke klíčům stávajícího účtu úložiště. 

1. V levém horním rohu na webu Azure Portal klikněte na tlačítko pro **vytvoření nové služby**.
2. Klikněte na **Úložiště** a potom na **Účet úložiště**. Zobrazí se nový panel Vytvořit účet úložiště.
3. Zadejte **název** tohoto účtu úložiště, který použijete později.  
4. V polích **Model nasazení** a **Druh účtu** nastavte Resource manager a Pro obecné účely (v uvedeném pořadí). 
5. Ověřte, že pole **Předplatné** a **Skupina prostředků** se shodují s údaji zadanými při vytvoření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvoření nového účtu úložiště](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Vytvoření kontejneru objektů blob v účtu úložiště

Později nahrajeme a stáhneme soubor do nového účtu úložiště. Soubory vyžadují úložiště objektů blob. Proto potřebujeme vytvořit kontejner objektů blob, do kterého soubor uložíme.

1. Vraťte se k nově vytvořenému účtu úložiště.
2. Na panelu vlevo pod položkou Blob service klikněte na odkaz **Kontejnery**.
3. Když nahoře na stránce kliknete na **+ Kontejner**, vysune se panel Nový kontejner.
4. Pojmenujte kontejner, vyberte úroveň přístupu a klikněte na **OK**. Zadaný název použijeme v další části tohoto kurzu. 

    ![Vytvoření kontejneru úložiště](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Udělení přístupu k použití SAS úložiště spravované identitě přiřazené systémem virtuálního počítače 

Azure Storage nativně nepodporuje ověřování Azure AD.  Pomocí spravované identity přiřazené systémem virtuálního počítače ale můžete načíst SAS úložiště z Resource Manageru a pak ho použít pro přístup k úložišti.  V tomto kroku udělíte spravované identitě přiřazené systémem virtuálního počítače přístup k SAS účtu úložiště.   

1. Vraťte se k nově vytvořenému účtu úložiště.   
2. Na panelu vlevo klikněte na odkaz **Řízení přístupu (IAM)**.  
3. Nahoře na stránce klikněte na **+ Přidat** a přiřaďte virtuálnímu počítači novou roli.
4. Na pravé straně stránky nastavte položku **Role** na Přispěvatel účtů úložiště. 
5. V dalším rozevíracím seznamu **Přiřadit přístup k** nastavte prostředek na Virtuální počítač.  
6. Potom se ujistěte, že v rozevíracím seznamu **Předplatné** je správné předplatné, a nastavte **Skupinu prostředků** na Všechny skupiny prostředků.  
7. Nakonec **vyberte** v rozevíracím seznamu svůj virtuální počítač s Linuxem a klikněte na **Uložit**.  

    ![Text k alternativnímu obrázku](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Azure Resource Manageru

Ve zbývající části kurzu použijeme k práci dříve vytvořený virtuální počítač.

K dokončení tohoto postupu budete potřebovat klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Pokud potřebujete pomoc při konfiguraci klíčů klienta SSH, přečtěte si, [jak na počítači s Windows v Azure používat klíče SSH](../../virtual-machines/linux/ssh-from-windows.md) nebo [jak na linuxových virtuálních počítačích v Azure vytvářet a používat pár veřejného a privátního klíče SSH](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na webu Azure Portal přejděte na **Virtuální počítače**, přejděte ke svému linuxovému virtuálnímu počítači a potom nahoře na stránce **Přehled** klikněte na **Připojit**. Zkopírujte řetězec pro připojení k vašemu virtuálnímu počítači. 
2. Použijte klienta SSH a připojte se ke svému virtuálnímu počítači.  
3. Dále se zobrazí výzva k zadání **hesla**, které jste přidali při vytvoření **virtuálního počítače s Linuxem**. Pak byste měli být přihlášeni.  
4. K získání přístupového tokenu Azure Resource Manageru použijte CURL.  

    Žádost CURL o přístupový token i odpověď jsou níže:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > V předchozím požadavku platí, že hodnota parametru „resource“ musí přesně odpovídat hodnotě, kterou očekává Azure AD. Při použití ID prostředku Azure Resource Manageru musí být v identifikátoru URI koncové lomítko.
    > V následující odpovědi byl kvůli stručnosti prvek access_token zkrácen.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Získání pověření SAS z Azure Resource Manageru kvůli volání úložiště

Teď použijte CURL k volání Resource Manageru. Použijte přístupový token, který jste načetli v předchozí části, a vytvořte pověření SAS úložiště. Jakmile budete mít pověření SAS, můžete volat operace nahrávání do úložiště nebo stahování z úložiště.

V tomto požadavku použijeme k vytvoření pověření SAS následující parametry požadavku HTTP:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Tato parametry se zahrnou do textu požadavku POST na pověření SAS. Další informace o parametrech pro vytvoření pověření SAS najdete v [referenčních informacích k REST pro výpis SAS služby](/rest/api/storagerp/storageaccounts/listservicesas).

Pomocí následujícího požadavku CURL získejte pověření SAS. Nezapomeňte nahradit hodnoty parametrů `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` a `<EXPIRATION TIME>` vlastními hodnotami. Hodnotu `<ACCESS TOKEN>` nahraďte dříve získaným přístupovým tokenem:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> V textu předchozí adresy URL se rozlišují velká a malá písmena. Proto zkontrolujte, že jste je u svých skupin prostředků zadali správně. Je také důležité vědět, že se jedná o požadavek POST, a nikoli o požadavek GET.

V odpovědi CURL se vrátí pověření SAS:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Vytvořte ukázkový soubor objektu blob, který nahrajete do kontejneru úložiště objektů blob. Na virtuálním počítači s Linuxem k tomu použijte následující příkaz. 

```bash
echo "This is a test file." > test.txt
```

Potom proveďte ověření pomocí příkazu `az storage` rozhraní příkazového řádku a pověření SAS a nahrajte soubor do kontejneru objektů blob. K tomuto kroku potřebujete na svém virtuálním počítači [nainstalovanou nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), pokud ji ještě nemáte.

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Odpověď: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

Ke stažení souboru také můžete použít Azure CLI a k ověření pověření SAS. 

Požadavek: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

Odpověď: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli, jak použít spravovanou identitu přiřazenou systémem na virtuálním počítači s Linuxem pro přístup k Azure Storage pomocí pověření SAS.  Další informace o SAS služby Azure Storage najdete tady:

> [!div class="nextstepaction"]
>[Použití sdílených přístupových podpisů (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
