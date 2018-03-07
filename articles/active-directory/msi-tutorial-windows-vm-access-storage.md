---
title: "Používat Windows virtuální počítač MSI pro přístup k Azure Storage"
description: "Kurz vás provede procesem použití Windows virtuálního počítače spravované služby Identity (MSI) pro přístup k úložišti Azure."
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
ms.openlocfilehash: 535906e273b6d36045f5bc56d25b2085b2f71b9d
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Použití virtuálních počítačů spravovaných identitu služby Windows pro přístup k úložišti Azure pomocí přístupového klíče

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak povolit identita spravované služby (MSI) pro virtuální počítač Windows a pak tuto identitu používat pro načtení přístupových klíčů k účtu úložiště. Můžete použít jako obvykle při provádění operace úložiště, například při použití sady SDK úložiště přístupových klíčů k úložišti. V tomto kurzu jsme nahrávání a stahování objektům BLOB pomocí Azure PowerShell úložiště. Se dozvíte, jak:


> [!div class="checklist"]
> * Povolit MSI ve virtuálním počítači Windows 
> * Udělit váš virtuální počítač přístup k přístupových klíčů k účtu úložiště ve Správci prostředků 
> * Získání přístupového tokenu pomocí identity Virtuálního počítače a použít ho k načtení přístupové klíče k úložišti ze Správce prostředků 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows v nové skupině prostředků.

V tomto kurzu vytvoříme nový virtuální počítač s Windows. Můžete také povolit MSI na existující virtuální počítač.

1.  Klikněte **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu portálu Azure.
2.  Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3.  Zadejte informace o virtuálním počítači. **Uživatelské jméno** a **heslo** vytvořený, zde je přihlašovací údaje, které používáte k přihlášení k virtuálnímu počítači.
4.  Vyberte správnou **předplatné** pro virtuální počítač v rozevírací nabídce.
5.  Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač lze vytvořit v, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6.  Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

    ![Obrázek alternativní text](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolit MSI na vašem virtuálním počítači

Virtuální počítač MSI umožňuje získat přístupové tokeny z Azure AD, aniž by bylo třeba uvést přihlašovací údaje do vašeho kódu. V pozadí, povolení MSI provádí dvě věci: nainstaluje rozšíření virtuálního počítače MSI na vašem virtuálním počítači a umožňuje MSI pro virtuální počítač.  

1. Přejděte do skupiny prostředků vašeho nového virtuálního počítače a vyberte virtuální počítač, který jste vytvořili v předchozím kroku.
2. V části virtuální počítač "Nastavení" na levé straně klikněte na **konfigurace**.
3. Registrovat a povolit soubor MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Ujistěte se, kliknete na tlačítko **Uložit** konfiguraci uložíte.

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat rozšíření, které jsou ve virtuálním počítači, klikněte na tlačítko **rozšíření**. Pokud je povoleno MSI, **ManagedIdentityExtensionforWindows** se zobrazí v seznamu.

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Pokud jste již nemáte, můžete nyní vytvoří účet úložiště. Také můžete tento krok přeskočit a udělit přístup MSI virtuálních počítačů na klíče objektu stávající účet úložiště. 

1. Klikněte **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu portálu Azure.
2. Klikněte na tlačítko **úložiště**, pak **účet úložiště**, a zobrazí nový panel "Vytvořit účet úložiště".
3. Zadejte název pro účet úložiště, který budete používat později.  
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
7. Nakonec v části **vyberte** v rozevírací nabídce vyberte virtuální počítač se systémem Windows a pak klikněte na **Uložit**. 

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít jej k vyvolání Azure Resource Manager 

Pro zbývající část tohoto kurzu budeme pracovat z virtuálního počítače, které jsme vytvořili předtím. 

Musíte použít rutiny Powershellu pro Azure Resource Manager v této části.  Pokud nemáte nainstalováno, [stáhnout nejnovější verzi](https://docs.microsoft.com/powershell/azure/overview) než budete pokračovat.

1. Na portálu Azure přejděte do **virtuální počítače**, přejděte na virtuálním počítači se systémem Windows, potom ze **přehled** klikněte na stránce **Connect** v horní části. 
2. Zadejte ve vaší **uživatelské jméno** a **heslo** pro které jste přidali při vytváření virtuálního počítače Windows. 
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete v této relaci vzdálené prostředí PowerShell.
4. Pomocí Powershellu Invoke-WebRequest, vytvořte žádost na místní koncový bod MSI se získat přístupový token pro Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Hodnota parametru "prostředek" musí být přesná shoda pro očekávané službou Azure AD. Pokud používáte ID prostředku Azure Resource Manager, je nutné zahrnout do adresy koncové lomítko, v identifikátoru URI.
    
    V dalším kroku extrahujte "Obsah" elementu, který je uložený jako řetězec formátu JavaScript objekt Notation (JSON) v objektu $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    V dalším kroku extrahujte tokenu přístupu z odpovědi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Získání přístupových klíčů k účtu úložiště z Azure Resource Manager volání úložiště  

Nyní volání Resource Manager pomocí přístupového tokenu nemůžeme načíst v předchozí části, pomocí prostředí PowerShell k načtení přístupový klíč úložiště. Jakmile jsme přístupový klíč úložiště, jsme můžete volat operace nahrávání nebo stahování úložiště.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> Adresa URL je malá a velká písmena, zajistěte proto použijte přesnou stejné velká a malá písmena dříve, můžete s názvem skupiny prostředků, včetně velká písmena "G" v "Skupinyprostředků." 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Dále vytvoříme soubor s názvem "test.txt". Pak použijte přístupový klíč úložiště k ověření `New-AzureStorageContent` rutiny nahrát soubor do našich kontejner objektů blob a potom stáhněte soubor.

```bash
echo "This is a test text file." > test.txt
```

Je nutné nejprve nainstalovat rutiny Azure Storage pomocí `Install-Module Azure.Storage`. Potom nahrát objekt blob, který jste právě vytvořili, pomocí `Set-AzureStorageBlobContent` rutiny prostředí PowerShell:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Odpověď:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Můžete také stáhnout objektu blob, který jste právě nahráli, pomocí `Get-AzureStorageBlobContent` rutiny prostředí PowerShell:

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Odpověď:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](../active-directory/msi-overview.md).
- Další postup Tento stejný kurz pomocí úložiště pověření SAS najdete v tématu [pomocí virtuálních počítačů spravovaných identitu služby Windows pro přístup k úložišti Azure pomocí pověření SAS](msi-tutorial-windows-vm-access-storage-sas.md)
- Další informace o funkci SAS účtu Azure Storage najdete v tématu:
  - [Použití sdílených přístupových podpisů (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Vytváření SAS služby](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Použít následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah


