---
title: Aktualizace Media Services po postupném zavedení přístupových klíčů k úložišti | Dokumentace Microsoftu
description: Tento článek poskytují pokyny o tom, jak aktualizace Media Services po postupném zavedení přístupových klíčů k úložišti.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: e8d8067fcf30b16dd3dbc7f6cf50129d837aa3a5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306852"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Aktualizace Media Services po postupném zavedení přístupových klíčů k úložišti

Když vytvoříte nový účet Azure Media Services (AMS), zobrazí se výzva, vyberte účet služby Azure Storage, který se používá k ukládání vašeho mediálního obsahu. Můžete přidat více účtů úložiště do účtu Media Services. Tento článek ukazuje, jak otočení klíčů úložiště. Také ukazuje, jak přidat účty úložiště do účtu media. 

K provádění akcí popsaných v tomto článku, měli byste použít [rozhraní API Azure Resource Manageru](https://docs.microsoft.com/rest/api/media/mediaservice) a [Powershellu](https://docs.microsoft.com/powershell/module/azurerm.media).  Další informace najdete v tématu [Správa prostředků Azure pomocí Resource Manageru a Powershellu](../../azure-resource-manager/powershell-azure-resource-manager.md)

## <a name="overview"></a>Přehled

Při vytvoření nového účtu úložiště vygeneruje Azure dva 512bitové přístupové klíče k úložišti, které se používají k ověření přístupu k vašemu účtu úložiště. K lepšímu zabezpečení vašeho připojení úložiště, doporučujeme pravidelně znovu vygenerujte a otočení přístupový klíč k úložišti. Aby bylo možné vám umožní spravovat připojení k účtu úložiště používat jeden přístupový klíč, zatímco znovu vygenerujete druhý přístupový klíč jsou k dispozici dva přístupové klíče (primární i sekundární). Tento postup se také nazývá "postupné přístupových klíčů".

Služba Media Services závisí na zadaný klíč k úložišti. Konkrétně lokátory, které se používají ke streamování nebo stažení vašich prostředků závisí na přístupový klíč zadaný úložiště. Po vytvoření účtu AMS trvá závislost na přístupový klíč primárního úložiště ve výchozím nastavení, ale jako uživatel, můžete aktualizovat klíč úložiště, který má AMS. Musíte nechat Media Services znáte klíč pomocí kroků popsaných v tomto článku.  

>[!NOTE]
> Pokud máte více účtů úložiště, provedli byste tento postup ke každému účtu úložiště. Pořadí, ve kterém otočení klíčů úložiště nebyl vyřešen. Můžete otočit sekundární klíče první a potom primární klíč nebo naopak versa.
>
> Před provedením kroků popsaných v tomto článku na produkčního účtu, ujistěte se, že chcete otestovat v předprodukčním prostředí účtu.
>

## <a name="steps-to-rotate-storage-keys"></a>Kroky pro otočení klíčů úložiště 
 
 1. Změna primárního klíče účtu úložiště pomocí rutiny prostředí powershell nebo [Azure](https://portal.azure.com/) portálu.
 2. Volání rutiny Sync-AzureRmMediaServiceStorageKeys s odpovídající parametry přinutit účet mediální ke sbírání klíče účtu úložiště
 
    Následující příklad ukazuje, jak synchronizovat klíče pro účty úložiště.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Počkejte přibližně do hodiny. Ověřte, že pracujete streamování scénáře.
 4. Změnit sekundární klíč účtu úložiště pomocí powershellu nebo webu Azure portal.
 5. Volání powershellu AzureRmMediaServiceStorageKeys synchronizace s odpovídající parametry přinutit účet mediální ke sbírání nové klíče účtu úložiště. 
 6. Počkejte přibližně do hodiny. Ověřte, že pracujete streamování scénáře.
 
### <a name="a-powershell-cmdlet-example"></a>Příklad rutiny prostředí powershell 

Následující příklad ukazuje, jak načíst účet úložiště a synchronizovat se účet AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Postup přidání účtů úložiště pro svůj účet AMS

V následujícím článku ukazuje, jak přidat účty úložiště pro svůj účet AMS: [k účtu Azure Media Services připojit více účtů úložiště](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Potvrzení
Rádi bychom se na vědomí následující osob, které přispívají k vytvoření tohoto dokumentu: Cenk Dingiloglu, Milán Gada Seva Titov.
