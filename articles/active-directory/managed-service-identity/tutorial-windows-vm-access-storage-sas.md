---
title: Použití MSI virtuálního počítače Windows pro přístup k Azure Storage pomocí pověření SAS
description: Kurz, který popisuje, jak používat pro přístup k Azure Storage, pomocí pověření SAS místo přístupový klíč účtu úložiště Windows virtuální počítač Identity spravované služby (MSI).
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 89bbf0bff107cd297f69c0bf5a4017959ea238cd
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043971"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Kurz: Použití Windows virtuálního počítače identitu spravované služby pro přístup k úložišti Azure pomocí pověření SAS

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak povolit Identity spravované služby (MSI) pro virtuální počítač Windows, a pak pomocí MSI k získání přihlašovacích údajů úložiště sdíleného přístupového podpisu (SAS). Konkrétně [pověření SAS služby](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

SAS služby umožňuje udělit omezený přístup k objektům v účtu úložiště, omezenou dobu a specifické služby (v našem případě služby blob service), bez vystavení přístupový klíč účtu. Můžete použít pověření SAS jako obvykle při provádění operace úložiště, například při použití sady SDK služby Storage. Pro účely tohoto kurzu vám ukážeme odesílání a stahování objektu blob s využitím Powershellu pro Azure Storage. Se dozvíte, jak:


> [!div class="checklist"]
> * Povolení MSI na virtuálním počítači s Windows 
> * Udělení přístupu vašich virtuálních počítačů na účet úložiště SAS v Resource Manageru 
> * Získání přístupového tokenu pomocí identity Virtuálního počítače a použít ho k získání sdíleného přístupového podpisu ze Správce prostředků 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Windows. Můžete také povolit MSI na existující virtuální počítač.

1.  Klikněte na tlačítko **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu webu Azure portal.
2.  Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3.  Zadejte informace o virtuálním počítači. **Uživatelské jméno** a **heslo** vytvořený, tady je přihlašovací údaje použijete k přihlášení k virtuálnímu počítači.
4.  Zvolte správné **předplatné** pro virtuální počítač v rozevírací nabídce.
5.  Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač, aby se v aplikaci, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6.  Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

    ![Text ALT obrázku](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolení MSI ve virtuálním počítači

MSI virtuálního počítače můžete k získání přístupových tokenů z Azure AD, aniž byste museli vložit pověření do kódu. Pod pokličkou, povolení MSI provede dvě věci: registrů svého virtuálního počítače pomocí Azure Active Directory k vytvoření jeho spravovanou identitu a nakonfiguruje identitu na virtuálním počítači.

1. Přejděte do skupiny prostředků vašeho nového virtuálního počítače a vyberte virtuální počítač, který jste vytvořili v předchozím kroku.
2. V části virtuální počítač "Nastavení" na levém panelu klikněte na **konfigurace**.
3. Chcete-li zaregistrovat a povolit MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Zajištění kliknutí na **Uložit** uložte konfiguraci.

    ![Text ALT obrázku](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Pokud již nemáte, můžete nyní vytvoří účet úložiště. Také můžete tento krok přeskočit a udělit MSI virtuálního počítače přístup k pověření SAS pro existující účet úložiště. 

1. Klikněte na tlačítko **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu webu Azure portal.
2. Klikněte na tlačítko **úložiště**, pak **účtu úložiště**, a zobrazí nový panel "Vytvoření účtu úložiště".
3. Zadejte název účtu úložiště, který budete používat později.  
4. **Model nasazení** a **druh účtu** musí být nastavená na "Resource Manageru" a "Obecné použití", v uvedeném pořadí. 
5. Zkontrolujte **předplatné** a **skupiny prostředků** shodovat s těmi, které jste zadali při vytváření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvořit nový účet úložiště](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Vytvořte kontejner objektů blob v účtu úložiště

Dále budeme nahrávat a stáhnout soubor do nového účtu úložiště. Protože soubory vyžadují úložiště objektů blob, potřebujeme vytvořit kontejner objektů blob, do kterého chcete soubor uložit.

1. Přejděte zpět do vaší nově vytvořený účet úložiště.
2. Klikněte na tlačítko **kontejnery** odkaz na levém panelu, v části "Blob service."
3. Klikněte na tlačítko **+ kontejner** horní části stránky a "Nový kontejner" vysune.
4. Zadejte název kontejneru, vyberte úroveň přístupu a pak klikněte na tlačítko **OK**. Zadaný název se použije v pozdější části kurzu. 

    ![Vytvoření kontejneru úložiště](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Udělení přístupu MSI virtuálního počítače a použít úložiště SAS 

Úložiště Azure nepodporuje nativně ověřování Azure AD.  Můžete však použít soubor MSI k načtení úložiště SAS v Resource Manageru a pak použít SAS pro přístup k úložišti.  V tomto kroku udělení přístupu MSI virtuálního počítače do účtu úložiště SAS.   

1. Přejděte zpět do vaší nově vytvořený účet úložiště.   
2. Klikněte na tlačítko **řízení přístupu (IAM)** odkaz na levém panelu.  
3. Klikněte na tlačítko **+ přidat** nad stránky a přidat nové přiřazení role pro váš virtuální počítač
4. Nastavte **Role** na "Přispěvatel účtů úložiště", na pravé straně stránky.  
5. V dalším rozevíracím seznamu, nastavte **přiřadit přístup k** prostředku "Virtuální počítač".  
6. Dále, zajistit správné předplatné je uvedené v **předplatné** rozevírací seznam, nastavte **skupiny prostředků** na "Všechny skupiny prostředků".  
7. Nakonec v části **vyberte** zvolte virtuální počítač Windows v rozevíracím seznamu a potom klikněte na tlačítko **Uložit**. 

    ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít jej k vyvolání Azure Resource Manageru 

Zbytek tohoto kurzu budeme pracovat z virtuálního počítače, které jsme vytvořili dříve.

Je potřeba použít rutiny Powershellu pro Azure Resource Manager v této části.  Pokud nemáte ji nainstalovánu, [stáhnout nejnovější verzi](https://docs.microsoft.com/powershell/azure/overview) než budete pokračovat.

1. Na webu Azure Portal, přejděte na **virtuálních počítačů**, přejděte na Windows virtuální počítač, pak z **přehled** stránce klikněte na **připojit** v horní části.
2. Zadejte vaše **uživatelské jméno** a **heslo** pro které jste přidali po vytvoření virtuálního počítače Windows. 
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete prostředí PowerShell ve vzdálené relaci. 
4. Použití Powershellu Invoke-WebRequest, vytvořte žádost na místním koncovým bodem MSI pro získání přístupového tokenu pro Azure Resource Manageru.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Hodnota parametru "prostředek" musí být přesná shoda pro co se očekává službou Azure AD. Při použití ID prostředku Azure Resource Manageru, je nutné zahrnout do adresy koncové lomítko v identifikátoru URI.
    
    V dalším kroku extrahujte "Obsah" element, který je uložený jako řetězec JSON JavaScript Object Notation () ve formátu v objektu $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    V dalším kroku extrahujte přístupový token z odpovědi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Získejte z Azure Resource Manageru pro volání úložiště pověření SAS 

Nyní pomocí prostředí PowerShell Resource Manageru pomocí přístupového tokenu, který jsme získali v předchozí části, chcete-li vytvořit přihlašovací údaje úložiště SAS volání. Jakmile budeme mít pověření SAS, jsme volání operací úložiště.

Pro tento požadavek použijeme k vytvoření pověření SAS parametry požadavku HTTP podle:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Tyto parametry jsou zahrnuty v textu POST žádosti o pověření SAS. Další informace o parametrech pro vytvoření pověření SAS najdete v tématu [Reference k rozhraní REST SAS služby seznamu](/rest/api/storagerp/storageaccounts/listservicesas).

Nejprve převeďte do formátu JSON parametry a potom voláním úložiště `listServiceSas` přihlašovacích údajů koncového bodu pro vytvoření sdíleného přístupového podpisu:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Adresa URL je velká a malá písmena, zajistěte proto používat přesně stejnou velká a malá písmena, s názvem skupiny prostředků, včetně velké písmeno "G" v "resourceGroups." 

Nyní jsme extrahovat pověření SAS z odpovědi:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Je-li si prohlédnout pověření SAS se zobrazí vypadat přibližně takto:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Dále vytvoříme soubor s názvem "test.txt". Pak používají pověření SAS k ověření `New-AzureStorageContent` rutiny, soubor nahrát do našich kontejner objektů blob a pak si stáhnout soubor.

```bash
echo "This is a test text file." > test.txt
```

Je potřeba nejdřív nainstalovat nejnovější verzi rutin Azure Storage pomocí `Install-Module Azure.Storage`. Pak nahrát objekt blob, které jste právě vytvořili, pomocí `Set-AzureStorageBlobContent` rutinu Powershellu:

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

Můžete si také stáhnout objekt blob, který jste právě nahráli, pomocí `Get-AzureStorageBlobContent` rutinu Powershellu:

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvořit identita spravované služby pro přístup k Azure Storage pomocí pověření SAS.  Další informace o Azure Storage SAS najdete v tématu:

> [!div class="nextstepaction"]
>[Použití sdílených přístupových podpisů (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)


