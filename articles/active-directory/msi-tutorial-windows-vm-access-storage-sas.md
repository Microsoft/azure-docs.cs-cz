---
title: "Použití Windows virtuální počítač MSI pro přístup k úložišti Azure pomocí pověření SAS"
description: "Kurz ukazuje, jak používat pro přístup k Azure Storage, pomocí pověření SAS místo přístupový klíč účtu úložiště Windows virtuálního počítače spravované služby Identity (MSI)."
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
ms.openlocfilehash: 0986c4470c233a21a88624f0affedb5986d86589
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Použití virtuálních počítačů spravovaných identitu služby Windows pro přístup k úložišti Azure pomocí pověření SAS

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Tento kurz ukazuje, jak povolit identita spravované služby (MSI) pro virtuální počítač Windows, potom použijte soubor MSI se získat pověření sdíleného přístupového podpisu (SAS) úložiště. Konkrétně [pověření SAS služby](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

SAS služby poskytuje možnost udělit omezený přístup k objektům v účtu úložiště po omezenou dobu a specifické služby (v našem případě služby objektů blob), bez vystavení přístupový klíč účtu. Můžete použít pověření SAS obvyklým při provádění operace úložiště, například při použití sady SDK úložiště. V tomto kurzu ukážeme odesílání a stahování objekt blob pomocí Azure PowerShell úložiště. Se dozvíte, jak:


> [!div class="checklist"]
> * Povolit MSI ve virtuálním počítači Windows 
> * Udělit přístup virtuálních počítačů na účet úložiště SAS ve službě Správce prostředků 
> * Získání přístupového tokenu pomocí identity Virtuálního počítače a použít ho k načtení SAS ze Správce prostředků 

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
2. V části virtuální počítač "Nastavení" na levém panelu klikněte na **konfigurace**.
3. Registrovat a povolit soubor MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Ujistěte se, kliknete na tlačítko **Uložit** konfiguraci uložíte.

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat rozšíření, které jsou ve virtuálním počítači, klikněte na tlačítko **rozšíření**. Pokud je povoleno MSI, **ManagedIdentityExtensionforWindows** se zobrazí v seznamu.

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Pokud jste již nemáte, můžete nyní vytvoří účet úložiště. Také můžete tento krok přeskočit a zajišťují vašim MSI virtuální počítač přístup k pověření SAS stávající účet úložiště. 

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
2. Klikněte **kontejnery** odkaz na levém panelu, v části "Služby objektů Blob."
3. Klikněte na tlačítko **+ kontejner** nahoře na stránce a "Nový kontejner" panely snímky limitu.
4. Zadejte název kontejneru, vybrat úroveň přístupu, a pak klikněte na tlačítko **OK**. Zadaný název se použije později v tomto kurzu. 

    ![Vytvoření kontejneru úložiště](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Udělení přístupu MSI Virtuálního počítače použít úložiště SAS 

Úložiště Azure nenabízí nativní podporu ověřování Azure AD.  Můžete však použít MSI k načtení úložiště SAS ze Správce prostředků a pak použít pro přístup k úložišti SAS.  V tomto kroku udělíte MSI virtuální počítač přístup k vašemu účtu úložiště SAS.   

1. Přejděte zpět na vaše nově vytvořený účet úložiště.   
2. Klikněte **přístup k ovládacímu prvku (IAM)** odkaz na levém panelu.  
3. Klikněte na tlačítko **+ přidat** nad stránku přidáte nové přiřazení role pro virtuální počítač
4. Nastavit **Role** k "Úložiště účet Přispěvatel", na pravé straně stránky.  
5. V dalším rozevíracím nastavte **přiřadit přístup** prostředků "Virtuální počítač".  
6. Dále zkontrolujte správné předplatné, je uvedena ve **předplatné** rozevíracího seznamu, pak nastavte **skupiny prostředků** na "Všechny skupiny prostředků".  
7. Nakonec v části **vyberte** v rozevírací nabídce vyberte virtuální počítač se systémem Windows a pak klikněte na **Uložit**. 

    ![Obrázek alternativní text](media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

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

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Získání pověření SAS z Azure Resource Manager volání úložiště 

Nyní volání Resource Manager pomocí přístupového tokenu, který jsme načíst v předchozí části, vytvoření pověření SAS úložiště pomocí prostředí PowerShell. Jakmile jsme pověření SAS, jsme můžete volat operace úložiště.

Pro tento požadavek použijeme parametrů žádosti protokolu HTTP použijte k vytvoření pověření SAS:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Tyto parametry jsou zahrnuty v textu POST požadavku pro přihlašovací údaje SAS. Další informace o parametry pro vytvoření pověření SAS naleznete v tématu [referenční seznam Service SAS REST](/rest/api/storagerp/storageaccounts/listservicesas).

Nejdřív převést do formátu JSON parametry, pak volání úložiště `listServiceSas` koncový bod pro vytvoření SAS pověření:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Adresa URL je malá a velká písmena, zajistěte proto použijte přesnou stejné velká a malá písmena dříve, můžete s názvem skupiny prostředků, včetně velká písmena "G" v "Skupinyprostředků." 

Nyní jsme můžete extrahovat pověření SAS z odpovědi:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Je-li si prohlédnout pověření SAS se zobrazí přibližně takto:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Dále vytvoříme soubor s názvem "test.txt". Pak použijte přihlašovací údaje SAS k ověření pomocí `New-AzureStorageContent` rutiny nahrát soubor do našich kontejner objektů blob a potom stáhněte soubor.

```bash
echo "This is a test text file." > test.txt
```

Je nutné nejprve nainstalovat rutiny Azure Storage pomocí `Install-Module Azure.Storage`. Potom nahrát objekt blob, který jste právě vytvořili, pomocí `Set-AzureStorageBlobContent` rutiny prostředí PowerShell:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Odpověď:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
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
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](../active-directory/msi-overview.md).
- Další informace o udělat Tento stejný kurz pomocí klíč účtu úložiště, najdete v tématu [pomocí virtuálních počítačů spravovaných identitu služby Windows pro přístup k úložišti Azure](msi-tutorial-windows-vm-access-storage.md)
- Další informace o funkci SAS účtu Azure Storage najdete v tématu:
  - [Použití sdílených přístupových podpisů (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Vytváření SAS služby](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.


