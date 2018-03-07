---
title: "Používat pro přístup k Azure Storage MSI virtuálních počítačů Linux"
description: "Kurz vás provede procesem pomocí Linux virtuálního počítače spravované služby Identity (MSI) pro přístup k úložišti Azure."
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: de397117f6891d28d24a4279d94e4e4ecc1e7afc
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Použít identitu služby spravované Linux virtuálního počítače pro přístup k úložišti Azure pomocí přístupového klíče

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak povolit identita spravované služby (MSI) pro virtuální počítač s Linuxem a použít tuto identitu obnovit přístupových klíčů k účtu úložiště. Můžete použít přístupový klíč k úložišti obvyklým při provádění operace úložiště, například při použití sady SDK úložiště. V tomto kurzu jsme nahrávání a stahování objektům BLOB pomocí rozhraní příkazového řádku Azure. Se dozvíte, jak:

> [!div class="checklist"]
> * Povolit MSI na virtuální počítač s Linuxem 
> * Udělit váš virtuální počítač přístup k přístupových klíčů k účtu úložiště ve Správci prostředků 
> * Získání přístupového tokenu pomocí identity Virtuálního počítače a použít ho k načtení přístupové klíče k úložišti ze Správce prostředků  

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Linuxem. Můžete také povolit MSI na existující virtuální počítač.

1. Klikněte **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**, vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Vyberte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač lze vytvořit v, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte typ filtru disku podporované. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

## <a name="enable-msi-on-your-vm"></a>Povolit MSI na vašem virtuálním počítači

Virtuální počítač MSI umožňuje získat přístupové tokeny z Azure AD, aniž by bylo třeba uvést přihlašovací údaje do vašeho kódu. V pozadí, povolení MSI provádí dvě věci: nainstaluje rozšíření virtuálního počítače MSI na vašem virtuálním počítači a umožňuje spravovat Identity služby pro virtuální počítač.  

1. Přejděte do skupiny prostředků vašeho nového virtuálního počítače a vyberte virtuální počítač, který jste vytvořili v předchozím kroku.
2. V části virtuální počítač "Nastavení" na levé straně klikněte na **konfigurace**.
3. Registrovat a povolit soubor MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Ujistěte se, kliknete na tlačítko **Uložit** konfiguraci uložíte.

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat rozšíření, které jsou ve virtuálním počítači, klikněte na tlačítko **rozšíření**. Pokud je povoleno MSI, **ManagedIdentityExtensionforLinux** se zobrazí v seznamu.

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Pokud jste již nemáte, můžete nyní vytvoří účet úložiště.  Také můžete tento krok přeskočit a udělit přístup MSI virtuálních počítačů na klíče objektu stávající účet úložiště. 

1. Klikněte **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu portálu Azure.
2. Klikněte na tlačítko **úložiště**, pak **účet úložiště**, a zobrazí nový panel "Vytvořit účet úložiště".
3. Zadejte **název** pro účet úložiště, který budete používat později.  
4. **Model nasazení** a **účet typu** musí být nastavena na "Resource manager" a "Obecné účely", v uvedeném pořadí. 
5. Ujistěte se, **předplatné** a **skupiny prostředků** odpovídat jsou zadány při vytvoření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvořit nový účet úložiště](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Vytvořte kontejner objektů blob v účtu úložiště

Později jsme se nahrávání a stahování souboru na nový účet úložiště. Protože soubory vyžadují úložiště objektů blob, je potřeba vytvořit kontejner objektů blob pro uložení souboru.

1. Přejděte zpět na vaše nově vytvořený účet úložiště.
2. Klikněte **kontejnery** odkaz na levé straně, v části "Služby objektů Blob."
3. Klikněte na tlačítko **+ kontejner** nahoře na stránce a "Nový kontejner" panely snímky limitu.
4. Zadejte název kontejneru, vybrat úroveň přístupu, a pak klikněte na tlačítko **OK**. Zadaný název se použije později v tomto kurzu. 

    ![Vytvoření kontejneru úložiště](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Udělit přístup MSI Virtuálního počítače k použití přístupových klíčů k účtu úložiště

Úložiště Azure nenabízí nativní podporu ověřování Azure AD.  Můžete však použít MSI k načtení přístupových klíčů k účtu úložiště ze Správce prostředků a pak použít klíč pro přístup k úložišti.  V tomto kroku zajišťují vašim MSI virtuální počítač přístup k klíče účtu úložiště.   

1. Přejděte zpět na vaše nově vytvořený účet úložiště.
2. Klikněte **přístup k ovládacímu prvku (IAM)** odkaz na levém panelu.  
3. Klikněte na tlačítko **+ přidat** nad stránku přidáte nové přiřazení role pro virtuální počítač
4. Nastavit **Role** "Úložiště účet klíč operátor služby role" na pravé straně stránky. 
5. V dalším rozevíracím nastavte **přiřadit přístup** prostředků "Virtuální počítač".  
6. Dále zkontrolujte správné předplatné, je uvedena ve **předplatné** rozevíracího seznamu, pak nastavte **skupiny prostředků** na "Všechny skupiny prostředků".  
7. Nakonec v části **vyberte** v rozevírací nabídce vyberte virtuální počítač Linux a pak klikněte na **Uložit**. 

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít jej k vyvolání Azure Resource Manager

Pro zbývající část tohoto kurzu budeme pracovat z virtuálního počítače, které jsme vytvořili předtím.

K dokončení těchto kroků budete potřebovat klientem SSH. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [jak klíče použití SSH se systémem Windows v Azure](../virtual-machines/linux/ssh-from-windows.md), nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu Azure přejděte do **virtuální počítače**, přejděte na Linux virtuálního počítače, pak z **přehled** klikněte na stránce **Connect** v horní části. Zkopírujte řetězce pro připojení k virtuálnímu počítači. 
2. Připojte k virtuálnímu počítači pomocí vašeho klienta SSH.  
3. Další, zobrazí se výzva k zadání ve vaší **heslo** jste přidali při vytváření **virtuálního počítače s Linuxem**. Můžete by pak se úspěšně přihlášeni.  
4. Použijte CURL k získání přístupu tokenu pro Azure Resource Manager.  

    Zde je CURL žádosti a odpovědi pro přístupový token:
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > V předchozí požadavek musí být hodnota parametru "prostředek" přesnou shodu pro očekávané službou Azure AD. Pokud používáte ID prostředku Azure Resource Manager, je nutné zahrnout do adresy koncové lomítko, v identifikátoru URI.
    > V následující odpovědi, jako byla zkrácena jako stručný výtah element access_token.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Získání přístupových klíčů k účtu úložiště z Azure Resource Manager volání úložiště  

Nyní pomocí CURL volání Resource Manager pomocí přístupového tokenu nemůžeme načíst v předchozí části, k načtení přístupový klíč úložiště. Jakmile jsme přístupový klíč úložiště, jsme můžete volat operace nahrávání nebo stahování úložiště. Nezapomeňte nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, a `<STORAGE ACCOUNT NAME>` hodnoty parametrů s vlastními hodnotami. Nahraďte `<ACCESS TOKEN>` hodnotu s tímto tokenem přístupu, který jste získali dříve:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Text v předchozí adresa URL je velká a malá písmena, zajistěte proto, pokud používáte horní malá pro vaší skupiny prostředků, aby odrážela ji odpovídajícím způsobem. Kromě toho je důležité vědět, že se jedná o požadavek POST požadavek GET a ujistěte se, že předat hodnotu pro zachycení limit délky s -d, který může mít hodnotu NULL.  

Odpověď CURL vám poskytuje seznam klíčů:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Vytvořte soubor ukázkových objektů blob k nahrání do vašeho kontejneru úložiště objektů blob. Na virtuální počítač s Linuxem můžete to provést pomocí následujícího příkazu. 

```bash
echo "This is a test file." > test.txt
```

V dalším kroku ověření pomocí rozhraní příkazového řádku `az storage` příkaz pomocí přístupový klíč úložiště a soubor odešlete do kontejneru objektů blob. V tomto kroku budete muset [nainstalovat nejnovější rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na vašem virtuálním počítači, pokud jste tak ještě neučinili.
 

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

Kromě toho můžete stáhnout soubor pomocí rozhraní příkazového řádku Azure a ověřování pomocí přístupového klíče úložiště. 

Žádost: 

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

- Přehled MSI najdete v tématu [identita spravované služby přehled](../active-directory/msi-overview.md).
- Další postup Tento stejný kurz pomocí úložiště pověření SAS najdete v tématu [používal Linux virtuálního počítače spravované služby identitu pro přístup k úložišti Azure pomocí pověření SAS](msi-tutorial-linux-vm-access-storage-sas.md)
- Další informace o funkci SAS účtu Azure Storage najdete v tématu:
  - [Použití sdílených přístupových podpisů (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Vytváření SAS služby](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
