---
title: Použití MSI virtuálního počítače Windows pro přístup k úložišti Azure
description: Kurz vás provede procesem jeho použití Windows virtuální počítač Identity spravované služby (MSI) pro přístup k úložišti Azure.
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
ms.openlocfilehash: 6e1364db9ecba65b90be525141f03fb9b4a33d28
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610832"
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Použít Windows virtuálního počítače identitu spravované služby pro přístup k Azure Storage prostřednictvím přístupového klíče

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak povolit Identity spravované služby (MSI) pro virtuální počítač Windows a pak tuto identitu můžete získat přístupové klíče účtu úložiště. Můžete použít jako obvykle při provádění operace úložiště, například při použití sady SDK služby Storage přístupových klíčů k úložišti. Pro účely tohoto kurzu jsme nahrání a stažení objektů BLOB pomocí Powershellu pro Azure Storage. Se dozvíte, jak:


> [!div class="checklist"]
> * Povolení MSI na virtuálním počítači s Windows 
> * Udělit váš virtuální počítač přístup k přístupové klíče účtu úložiště v Resource Manageru 
> * Získání přístupového tokenu pomocí identity Virtuálního počítače a použít ho k získání přístupových klíčů úložiště ze Správce prostředků 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

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

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolení MSI ve virtuálním počítači

MSI virtuálního počítače můžete k získání přístupových tokenů z Azure AD, aniž byste museli vložit pověření do kódu. Pod pokličkou, povolení MSI provede dvě věci: nainstaluje rozšíření MSI virtuálního počítače na virtuálním počítači a umožňují MSI pro virtuální počítač.  

1. Přejděte do skupiny prostředků vašeho nového virtuálního počítače a vyberte virtuální počítač, který jste vytvořili v předchozím kroku.
2. V části virtuální počítač "Nastavení" na levé straně klikněte na **konfigurace**.
3. Chcete-li zaregistrovat a povolit MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Zajištění kliknutí na **Uložit** uložte konfiguraci.

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat jaká rozšíření jsou na virtuálním počítači, klikněte na tlačítko **rozšíření**. Pokud je povolené MSI, **ManagedIdentityExtensionforWindows** se zobrazí v seznamu.

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Pokud již nemáte, můžete nyní vytvoří účet úložiště. Také můžete tento krok přeskočit a udělit MSI virtuálního počítače přístup ke klíčům existující účet úložiště. 

1. Klikněte na tlačítko **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu webu Azure portal.
2. Klikněte na tlačítko **úložiště**, pak **účtu úložiště**, a zobrazí nový panel "Vytvoření účtu úložiště".
3. Zadejte název účtu úložiště, který budete používat později.  
4. **Model nasazení** a **druh účtu** musí být nastavená na "Resource Manageru" a "Obecné použití", v uvedeném pořadí. 
5. Zkontrolujte **předplatné** a **skupiny prostředků** shodovat s těmi, které jste zadali při vytváření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvořit nový účet úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Vytvořte kontejner objektů blob v účtu úložiště

Dále budeme nahrávat a stáhnout soubor do nového účtu úložiště. Protože soubory vyžadují úložiště objektů blob, potřebujeme vytvořit kontejner objektů blob, do kterého chcete soubor uložit.

1. Přejděte zpět do vaší nově vytvořený účet úložiště.
2. Klikněte na tlačítko **kontejnery** odkazu na levé straně v části "Blob service."
3. Klikněte na tlačítko **+ kontejner** horní části stránky a "Nový kontejner" vysune.
4. Zadejte název kontejneru, vyberte úroveň přístupu a pak klikněte na tlačítko **OK**. Zadaný název se použije v pozdější části kurzu. 

    ![Vytvoření kontejneru úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Udělení přístupu MSI virtuálního počítače a použít přístupové klíče účtu úložiště 

Úložiště Azure nepodporuje nativně ověřování Azure AD.  Můžete však použít soubor MSI k načtení přístupové klíče účtu úložiště správce prostředků a pak použít klíč pro přístup k úložišti.  V tomto kroku udělíte vašeho přístupu MSI virtuálního počítače ke klíčům k vašemu účtu úložiště.   

1. Přejděte zpět do vaší nově vytvořený účet úložiště.  
2. Klikněte na tlačítko **řízení přístupu (IAM)** odkaz na levém panelu.  
3. Klikněte na tlačítko **+ přidat** nad stránky a přidat nové přiřazení role pro váš virtuální počítač
4. Nastavte **Role** na "Úložiště účtu klíč Role služby operátor", na pravé straně stránky. 
5. V dalším rozevíracím seznamu, nastavte **přiřadit přístup k** prostředku "Virtuální počítač".  
6. Dále, zajistit správné předplatné je uvedené v **předplatné** rozevírací seznam, nastavte **skupiny prostředků** na "Všechny skupiny prostředků".  
7. Nakonec v části **vyberte** zvolte virtuální počítač Windows v rozevíracím seznamu a potom klikněte na tlačítko **Uložit**. 

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít jej k vyvolání Azure Resource Manageru 

Zbytek tohoto kurzu budeme pracovat z virtuálního počítače, které jsme vytvořili dříve. 

Je potřeba použít rutiny Powershellu pro Azure Resource Manager v této části.  Pokud nemáte ji nainstalovánu, [stáhnout nejnovější verzi](https://docs.microsoft.com/powershell/azure/overview) než budete pokračovat.

1. Na webu Azure Portal, přejděte na **virtuálních počítačů**, přejděte na Windows virtuální počítač, pak z **přehled** stránce klikněte na **připojit** v horní části. 
2. Zadejte vaše **uživatelské jméno** a **heslo** pro které jste přidali po vytvoření virtuálního počítače Windows. 
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete prostředí PowerShell ve vzdálené relaci.
4. Použití Powershellu Invoke-WebRequest, vytvořte žádost na místním koncovým bodem MSI pro získání přístupového tokenu pro Azure Resource Manageru.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
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
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Získejte z Azure Resource Manageru pro volání úložiště přístupové klíče účtu úložiště  

Nyní pomocí prostředí PowerShell Resource Manageru pomocí přístupového tokenu, můžeme načíst v předchozím oddílu volat pro načtení přístupového klíče úložiště. Jakmile budeme mít přístupový klíč úložiště, můžeme volání operací nahrávání a stahování úložiště.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> Adresa URL je velká a malá písmena, zajistěte proto používat přesně stejnou velká a malá písmena, s názvem skupiny prostředků, včetně velké písmeno "G" v "resourceGroups." 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Dále vytvoříme soubor s názvem "test.txt". Přístupový klíč úložiště pak používají k ověření `New-AzureStorageContent` rutiny, soubor nahrát do našich kontejner objektů blob a pak si stáhnout soubor.

```bash
echo "This is a test text file." > test.txt
```

Je potřeba nejdřív nainstalovat nejnovější verzi rutin Azure Storage pomocí `Install-Module Azure.Storage`. Pak nahrát objekt blob, které jste právě vytvořili, pomocí `Set-AzureStorageBlobContent` rutinu Powershellu:

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
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Další postup Tento stejný kurz s použitím úložiště pověření SAS najdete v tématu [použít pro přístup k úložišti Azure pomocí pověření SAS Windows virtuálního počítače identitu spravované služby](msi-tutorial-windows-vm-access-storage-sas.md)
- Další informace o funkci SAS účtu Azure Storage najdete v tématu:
  - [Použití sdílených přístupových podpisů (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Vytváření SAS služby](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah


