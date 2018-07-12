---
title: Použití MSI virtuálního počítače s Linuxem pro přístup k Azure Storage pomocí pověření SAS
description: Kurz, který popisuje, jak používat Linux VM Identity spravované služby (MSI) pro přístup k Azure Storage, pomocí pověření SAS místo přístupový klíč účtu úložiště.
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
ms.openlocfilehash: fc7c5b4ab025666fc7fa1d9073198ec90d8e71c3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611023"
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Použít Linux VM identita spravované služby pro přístup k Azure Storage pomocí pověření SAS

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak povolit Identity spravované služby (MSI) pro virtuální počítač s Linuxem, a pak pomocí MSI k získání přihlašovacích údajů úložiště sdíleného přístupového podpisu (SAS). Konkrétně [pověření SAS služby](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

SAS služby umožňuje udělit omezený přístup k objektům v účtu úložiště pro konkrétní službu (v našem případě služby blob service), bez vystavení přístupový klíč účtu a po omezenou dobu. Můžete použít pověření SAS jako obvykle při provádění operace úložiště, například při použití sady SDK služby Storage. Pro účely tohoto kurzu vám ukážeme odesílání a stahování objektu blob s využitím rozhraní příkazového řádku Azure Storage. Se dozvíte, jak:


> [!div class="checklist"]
> * Povolení MSI na virtuální počítač s Linuxem 
> * Udělení přístupu vašich virtuálních počítačů na účet úložiště SAS v Resource Manageru 
> * Získání přístupového tokenu pomocí identity Virtuálního počítače a použít ho k získání sdíleného přístupového podpisu ze Správce prostředků 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Linuxem do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Linuxem. Můžete také povolit MSI na existující virtuální počítač.

1. Klikněte na tlačítko **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu webu Azure portal.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Zvolte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač, aby se v aplikaci, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Chcete-li zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte filtr typu disku podporované. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

## <a name="enable-msi-on-your-vm"></a>Povolení MSI ve virtuálním počítači

MSI virtuálního počítače můžete k získání přístupových tokenů z Azure AD, aniž byste museli vložit pověření do kódu. Pod pokličkou, povolení MSI provede dvě věci: nainstaluje rozšíření MSI virtuálního počítače na virtuálním počítači a umožňují identita spravované služby pro virtuální počítač.  

1. Přejděte do skupiny prostředků vašeho nového virtuálního počítače a vyberte virtuální počítač, který jste vytvořili v předchozím kroku.
2. V části virtuální počítač "Nastavení" na levé straně klikněte na **konfigurace**.
3. Chcete-li zaregistrovat a povolit MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Zajištění kliknutí na **Uložit** uložte konfiguraci.

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat jaká rozšíření jsou na virtuálním počítači, klikněte na tlačítko **rozšíření**. Pokud je povolené MSI, **ManagedIdentityExtensionforLinux** se zobrazí v seznamu.

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Pokud již nemáte, můžete nyní vytvoří účet úložiště.  Také můžete tento krok přeskočit a udělit MSI virtuálního počítače přístup ke klíčům existující účet úložiště. 

1. Klikněte na tlačítko **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu webu Azure portal.
2. Klikněte na tlačítko **úložiště**, pak **účtu úložiště**, a zobrazí nový panel "Vytvoření účtu úložiště".
3. Zadejte **název** pro účet úložiště, který budete používat později.  
4. **Model nasazení** a **druh účtu** musí být nastavená na "Resource Manageru" a "Obecné použití", v uvedeném pořadí. 
5. Zkontrolujte **předplatné** a **skupiny prostředků** shodovat s těmi, které jste zadali při vytváření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvořit nový účet úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Vytvořte kontejner objektů blob v účtu úložiště

Dále budeme nahrávat a stáhnout soubor do nového účtu úložiště. Protože soubory vyžadují úložiště objektů blob, potřebujeme vytvořit kontejner objektů blob, do kterého chcete soubor uložit.

1. Přejděte zpět do vaší nově vytvořený účet úložiště.
2. Klikněte na tlačítko **kontejnery** odkaz na levém panelu, v části "Blob service."
3. Klikněte na tlačítko **+ kontejner** horní části stránky a "Nový kontejner" vysune.
4. Zadejte název kontejneru, vyberte úroveň přístupu a pak klikněte na tlačítko **OK**. Zadaný název se použije v pozdější části kurzu. 

    ![Vytvoření kontejneru úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Udělení přístupu MSI virtuálního počítače a použít úložiště SAS 

Úložiště Azure nepodporuje nativně ověřování Azure AD.  Můžete však použít soubor MSI k načtení úložiště SAS v Resource Manageru a pak použít SAS pro přístup k úložišti.  V tomto kroku udělení přístupu MSI virtuálního počítače do účtu úložiště SAS.   

1. Přejděte zpět do vaší nově vytvořený účet úložiště...   
2. Klikněte na tlačítko **řízení přístupu (IAM)** odkaz na levém panelu.  
3. Klikněte na tlačítko **+ přidat** nad stránky a přidat nové přiřazení role pro váš virtuální počítač
4. Nastavte **Role** na "Přispěvatel účtů úložiště", na pravé straně stránky. 
5. V dalším rozevíracím seznamu, nastavte **přiřadit přístup k** prostředku "Virtuální počítač".  
6. Dále, zajistit správné předplatné je uvedené v **předplatné** rozevírací seznam, nastavte **skupiny prostředků** na "Všechny skupiny prostředků".  
7. Nakonec v části **vyberte** zvolte virtuální počítač s Linuxem v rozevíracím seznamu a potom klikněte na tlačítko **Uložit**.  

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít jej k vyvolání Azure Resource Manageru

Zbytek tohoto kurzu budeme pracovat z virtuálního počítače, které jsme vytvořili dříve.

K dokončení těchto kroků budete potřebovat klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Pokud potřebujete pomoc, konfigurace klíčů vašeho klienta SSH, přečtěte si téma [jak používat klíče SSH s Windows v Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), nebo [postupy vytváření a používání veřejných a privátních pár klíčů SSH pro virtuální počítače s Linuxem v Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Na webu Azure Portal, přejděte na **virtuálních počítačů**, přejděte na virtuální počítač s Linuxem, pak z **přehled** stránce klikněte na **připojit** v horní části. Zkopírujte řetězec pro připojení k vašemu virtuálnímu počítači. 
2. Připojte se k virtuálnímu počítači pomocí klienta SSH.  
3. Dále, zobrazí se výzva k zadání ve vaší **heslo** přidat při vytváření **virtuálního počítače s Linuxem**. By měl pak se úspěšně přihlásíte.  
4. Používáme nástroj CURL k získání přístupového tokenu pro Azure Resource Manageru.  

    CURL žádostí a odpovědí pro přístupový token je nižší než:
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > V předchozí žádosti musí být hodnota parametru "prostředek" přesná shoda pro co se očekává službou Azure AD. Při použití ID prostředku Azure Resource Manageru, je nutné zahrnout do adresy koncové lomítko v identifikátoru URI.
    > V následující odpověď access_token element jako byla zkrácena pro zkrácení.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Získejte z Azure Resource Manageru pro volání úložiště pověření SAS

Teď používáme nástroj CURL k volání Resource Manageru pomocí přístupového tokenu, který jsme získali v předchozí části, chcete-li vytvořit přihlašovací údaje úložiště SAS. Jakmile budeme mít pověření SAS, jsme volání operací nahrávání a stahování úložiště.

Pro tento požadavek použijeme k vytvoření pověření SAS parametry požadavku HTTP podle:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Tyto parametry jsou zahrnuty v textu POST žádosti o pověření SAS. Další informace o parametrech pro vytvoření pověření SAS najdete v tématu [Reference k rozhraní REST SAS služby seznamu](/rest/api/storagerp/storageaccounts/listservicesas).

Použijte následující požadavek CURL k získání pověření SAS. Nezapomeňte nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>`, a `<EXPIRATION TIME>` parametr hodnoty vlastními hodnotami. Nahraďte `<ACCESS TOKEN>` hodnotu pomocí přístupového tokenu, který jste získali dříve:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Text v předchozí adresy URL je velká a malá písmena, zajistěte proto, pokud používáte horní malá pro vaší skupiny prostředků tak, aby odrážely odpovídajícím způsobem. Kromě toho je důležité vědět, že se jedná o požadavek POST požadavek GET.

CURL odpověď vrátí pověření SAS:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Vytvoření ukázkového souboru objektu blob k nahrání do kontejneru úložiště objektů blob. Na virtuálním počítači s Linuxem můžete to provést pomocí následujícího příkazu. 

```bash
echo "This is a test file." > test.txt
```

V dalším kroku ověřování pomocí rozhraní příkazového řádku `az storage` příkazu pomocí pověření SAS a nahrání souboru do kontejneru objektů blob. Pro tento krok je potřeba [nainstalujte nejnovější Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na vašem virtuálním počítači, pokud jste tak již neučinili.

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

Kromě toho můžete stáhnout soubor pomocí Azure CLI a ověřování pomocí pověření SAS. 

Žádost: 

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

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Další postup Tento stejný kurz pomocí klíče účtu úložiště, najdete v článku [používat pro přístup k Azure Storage s Linuxem virtuálního počítače identitu spravované služby](msi-tutorial-linux-vm-access-storage.md)
- Další informace o funkci SAS účtu Azure Storage najdete v tématu:
  - [Použití sdílených přístupových podpisů (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Vytváření SAS služby](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.
