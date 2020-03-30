---
title: Kurz`:` Použití spravované identity pro přístup k Úložišti Azure pomocí přihlašovacích údajů SAS – Azure AD
description: Kurz, který ukazuje, jak používat spravovanou identitu přiřazenou systému Windows pro přístup k Azure Storage pomocí pověření SAS namísto přístupového klíče účtu úložiště.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c344c25a696500182030ff849a001ad586c92032
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232159"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Kurz: Použití spravované identity přiřazené k virtuálnímu počítači windows pro systém ovou službu pro přístup k Úložišti Azure prostřednictvím přihlašovacích údajů SAS

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tento kurz ukazuje, jak použít systémem přiřazenou identitu pro virtuální počítač (VM) windows k získání přihlašovacích údajů k podpisu sdíleného přístupu (SAS) úložiště. Konkrétně se bude jednat o [pověření SAS služby](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

Služba SAS poskytuje možnost udělit omezený přístup k objektům v účtu úložiště, po omezenou dobu a konkrétní službu (v našem případě služby objektu blob), bez vystavení přístupového klíče účtu. Pověření SAS můžete použít obvyklým způsobem při operacích s úložištěm, třeba při použití sady SDK služby Storage. V tomto kurzu předvedeme nahrávání a stahování objektu blob pomocí Azure Storage PowerShell. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Udělení přístupu k SAS účtu úložiště v Resource Manageru pro virtuální počítač 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení SAS z Resource Manageru 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Teď vytvoříte účet úložiště (pokud ho ještě nemáte). Můžete také přeskočit tento krok a udělit systémově přiřazené spravované identity přístup k pověření SAS existujícího účtu úložiště. 

1. V levém horním rohu na webu Azure Portal klikněte na tlačítko pro **vytvoření nové služby**.
2. Klikněte na **Úložiště** a potom na **Účet úložiště**. Zobrazí se nový panel Vytvořit účet úložiště.
3. Zadejte název tohoto účtu úložiště, který použijete později.  
4. V polích **Model nasazení** a **Druh účtu** nastavte Resource manager a Pro obecné účely (v uvedeném pořadí). 
5. Ověřte, že pole **Předplatné** a **Skupina prostředků** se shodují s údaji zadanými při vytvoření virtuálního počítače v předchozím kroku.
6. Klikněte na **Vytvořit**.

    ![Vytvoření nového účtu úložiště](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Vytvoření kontejneru objektů blob v účtu úložiště

Později nahrajeme a stáhneme soubor do nového účtu úložiště. Soubory vyžadují úložiště objektů blob. Proto potřebujeme vytvořit kontejner objektů blob, do kterého soubor uložíme.

1. Přejděte zpět k nově vytvořenému účtu úložiště.
2. Na panelu vlevo pod položkou Blob service klikněte na odkaz **Kontejnery**.
3. Když nahoře na stránce kliknete na **+ Kontejner**, vysune se panel Nový kontejner.
4. Pojmenujte kontejner, vyberte úroveň přístupu a klikněte na **OK**. Zadaný název použijeme v další části tohoto kurzu. 

    ![Vytvoření kontejneru úložiště](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Udělení přístupu k použití SAS úložiště spravované identitě přiřazené systémem virtuálního počítače 

Azure Storage nativně nepodporuje ověřování Azure AD.  Spravovanou identitu však můžete použít k načtení SAS úložiště ze Správce prostředků a potom použít SAS pro přístup k úložišti.  V tomto kroku udělíte spravované identitě přiřazené systémem virtuálního počítače přístup k SAS účtu úložiště.   

1. Přejděte zpět k nově vytvořenému účtu úložiště.   
2. Na panelu vlevo klikněte na odkaz **Řízení přístupu (IAM)**.  
3. Kliknutím **na + Přidat přiřazení role** v horní části stránky přidáte nové přiřazení role pro váš virtuální počítač.
4. Na pravé straně stránky nastavte položku **Role** na Přispěvatel účtů úložiště.  
5. V dalším rozevíracím seznamu **Přiřadit přístup k** nastavte prostředek na Virtuální počítač.  
6. Potom se ujistěte, že v rozevíracím seznamu **Předplatné** je správné předplatné, a nastavte **Skupinu prostředků** na Všechny skupiny prostředků.  
7. Nakonec **vyberte** v rozevíracím seznamu svůj virtuální počítač s Windows a klikněte na **Uložit**. 

    ![Alternativní text k obrázku](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Azure Resource Manageru 

Ve zbývající části kurzu použijeme k práci dříve vytvořený virtuální počítač.

V této části budete muset používat rutiny prostředí PowerShell pro Azure Resource Manager.  Pokud ji nemáte nainstalovanou, [stáhněte si před pokračováním nejnovější verzi.](https://docs.microsoft.com/powershell/azure/overview)

1. Na webu Azure Portal přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a potom nahoře na stránce **Přehled** klikněte na **Připojit**.
2. Zadejte své **Uživatelské jméno** a **Heslo**, které jste přidali při vytváření virtuálního počítače s Windows. 
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** virtuálního počítače, otevřete ve vzdálené relaci PowerShell. 
4. Pomocí příkazu Invoke-WebRequest v PowerShellu požádejte místní spravovanou identitu o koncový bod prostředků Azure k získání přístupového tokenu pro Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Hodnota parametru „resource“ musí přesně odpovídat hodnotě, kterou očekává Azure AD. Při použití ID prostředku Azure Resource Manageru musí být v identifikátoru URI koncové lomítko.
    
    V dalším kroku extrahujte element Content, který je uložený jako formátovaný řetězec JSON (JavaScript Object Notation) v objektu $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Potom z odpovědi extrahujte přístupový token.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Získání pověření SAS z Azure Resource Manageru kvůli volání úložiště 

Teď použijte PowerShell k volání Správce prostředků pomocí přístupového tokenu, který jsme načetli v předchozí části, k vytvoření pověření SAS úložiště. Jakmile budeme mít pověření SAS, můžeme volat operace úložiště.

V tomto požadavku použijeme k vytvoření pověření SAS následující parametry požadavku HTTP:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Tato parametry se zahrnou do textu požadavku POST na pověření SAS. Další informace o parametrech pro vytvoření pověření SAS najdete v [referenčních informacích k REST pro výpis SAS služby](/rest/api/storagerp/storageaccounts/listservicesas).

Nejprve převeďte parametry na JSON `listServiceSas` a pak zavolejte koncový bod úložiště a vytvořte pověření SAS:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> V adrese URL se rozlišují velká a malá písmena. Proto zkontrolujte, jestli používáte přesně stejná velká a malá písmena, jaká jste použili při pojmenování skupiny prostředků, a zkontrolujte také velké G ve výrazu „resourceGroup“. 

Nyní můžeme extrahovat pověření SAS z odpovědi:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Pokud zkontrolujete SAS cred uvidíte něco takového:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Dále vytvoříme soubor s názvem test.txt. Potom použijte pověření SAS k `New-AzStorageContent` ověření pomocí rutiny, nahrajte soubor do našeho kontejneru objektů blob a stáhněte soubor.

```bash
echo "This is a test text file." > test.txt
```

Nejprve je potřeba nainstalovat rutiny Azure Storage pomocí `Install-Module Azure.Storage`. Potom nahrajte právě vytvořený objekt blob pomocí rutiny PowerShellu `Set-AzStorageBlobContent`:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
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

Právě nahraný objekt blob si také můžete stáhnout pomocí rutiny PowerShellu `Get-AzStorageBlobContent`:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat systémově přiřazenou spravovanou identitu virtuálního počítače s Windows pro přístup k Azure Storage pomocí pověření SAS.  Další informace o SAS služby Azure Storage najdete tady:

> [!div class="nextstepaction"]
>[Použití sdílených přístupových podpisů (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)


