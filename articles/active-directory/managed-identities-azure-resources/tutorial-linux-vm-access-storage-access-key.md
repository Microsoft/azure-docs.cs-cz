---
title: Použití spravované identity přiřazené systémem na virtuální počítači s Linuxem pro přístup k Azure Storage
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Storage.
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
ms.date: 11/20/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36d4bb8adace344d5f60f3dfb530a65e0a228b4b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196157"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Kurz: Použití spravované identity systém přiřadil virtuálního počítače s Linuxem pro přístup k Azure Storage prostřednictvím přístupového klíče

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem na virtuálním počítači s Linuxem načíst přístupové klíče k účtu úložiště. Přístupový klíč k úložišti můžete použít obvyklým způsobem při operacích s úložištěm, třeba při použití sady SDK služby Storage. V tomto kurzu použijeme k nahrání a stažení objektů blob sadu nástrojů Azure CLI. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělit virtuálnímu počítači přístup k přístupovým klíčům účtu úložiště v Resource Manageru. 
> * Získat přístupový token pomocí identity virtuálního počítače a použít ho k načtení přístupových klíčů k úložišti z Resource Manageru.  

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

1. Přejděte zpět k nově vytvořenému účtu úložiště.
2. Nalevo pod položkou „Blob service“ klikněte na odkaz **Kontejnery**.
3. Když nahoře na stránce kliknete na **+ Kontejner**, vysune se panel Nový kontejner.
4. Pojmenujte kontejner, vyberte úroveň přístupu a klikněte na **OK**. Zadaný název použijeme v další části tohoto kurzu. 

    ![Vytvoření kontejneru úložiště](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Udělení přístupu pro použití přístupových klíčů k účtu úložiště spravované identitě přiřazené systémem na virtuálním počítači

V tomto kroku udělíte spravované identitě přiřazené systémem na virtuálním počítači přístup ke klíčům k vašemu účtu úložiště.   

1. Přejděte zpět k nově vytvořenému účtu úložiště.
2. Na panelu vlevo klikněte na odkaz **Řízení přístupu (IAM)**.  
3. Klikněte na tlačítko **+ přidat přiřazení role** nad stránky a přidat nové přiřazení role pro váš virtuální počítač
4. Na pravé straně stránky nastavte položku **Role** na Role služby Operátor klíčů účtů úložiště. 
5. V dalším rozevíracím seznamu **Přiřadit přístup k** nastavte prostředek na Virtuální počítač.  
6. Potom se ujistěte, že v rozevíracím seznamu **Předplatné** je správné předplatné, a nastavte **Skupinu prostředků** na Všechny skupiny prostředků.  
7. Nakonec **vyberte** v rozevíracím seznamu svůj virtuální počítač s Linuxem a klikněte na **Uložit**. 

    ![Text k alternativnímu obrázku](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

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
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Získání přístupových klíčů k účtu úložiště z Azure Resource Manageru kvůli volání úložiště  

Teď použijte CURL k volání Resource Manageru. Použijte přístupový token, který jste načetli v předchozí části, a načtěte přístupový klíč k úložišti. Jakmile máte přístupový klíč k úložišti, můžete volat operace nahrávání do úložiště nebo stahování z úložiště. Nezapomeňte nahradit parametry `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` a `<STORAGE ACCOUNT NAME>` vlastními hodnotami. Hodnotu `<ACCESS TOKEN>` nahraďte dříve získaným přístupovým tokenem:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> V textu předchozí adresy URL se rozlišují velká a malá písmena. Proto zkontrolujte, že jste je u svých skupin prostředků zadali správně. Je také důležité vědět, že se jedná o požadavek POST, a nikoli o požadavek GET. Ujistěte se, že parametrem -d předáte hodnotu, která zachycuje limit délky. Tato hodnota může být NULL.  

V odpovědi CURL získáte seznam klíčů:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Vytvořte ukázkový soubor objektů blob, který nahrajete do kontejneru úložiště objektů blob. Na linuxovém virtuálním počítači k tomu použijte následující příkaz: 

```bash
echo "This is a test file." > test.txt
```

Potom použijte k ověření příkaz `az storage` rozhraní CLI a přístupový klíč k úložišti a nahrajte soubor do kontejneru objektů blob. K tomuto kroku potřebujete na svém virtuálním počítači [nainstalovanou nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), pokud ji ještě nemáte.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Odpověď: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Ke stažení souboru také můžete použít Azure CLI a k ověření přístupový klíč k úložišti. 

Požadavek: 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Odpověď: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
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
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
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

V tomto kurzu jste se dozvěděli, jak použít spravovanou identitu přiřazenou systémem na virtuálním počítači s Linuxem pro přístup k Azure Storage pomocí přístupového klíče.  Další informace o přístupových klíčích Azure Storage:

> [!div class="nextstepaction"]
>[Správa přístupových klíčů k úložišti](/azure/storage/common/storage-create-storage-account)
