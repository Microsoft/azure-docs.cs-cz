---
title: Po vrácení přístupových klíčů k úložišti aktualizuje Media Services | Microsoft Docs
description: Tento článek poskytují pokyny o tom, jak aktualizovat Media Services po vrácení přístupových klíčů k úložišti.
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
ms.date: 08/09/2017
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: ebaa679c43bd577d4f37feb7f3a025086490e25f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Po vrácení přístupových klíčů k úložišti aktualizuje Media Services

Když vytvoříte nový účet Azure Media Services (AMS), zobrazí se výzva, vyberte účet úložiště Azure, který se používá k ukládání mediální obsah. Můžete přidat více než jeden účty úložiště pro váš účet Media Services. Toto téma ukazuje, jak otočit klíče úložiště. Také ukazuje, jak přidat účty úložiště k účtu media. 

K provedení akce popsané v tomto tématu, měli byste použít [rozhraní API ARM](https://docs.microsoft.com/rest/api/media/mediaservice) a [prostředí Powershell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media).  Další informace najdete v tématu [jak ke správě prostředků Azure pomocí prostředí PowerShell a správce prostředků](../../azure-resource-manager/powershell-azure-resource-manager.md).

## <a name="overview"></a>Přehled

Při vytvoření nového účtu úložiště vygeneruje Azure dva 512bitové přístupové klíče k úložišti, které se používají k ověření přístupu k účtu úložiště. K lepšímu zabezpečení připojení k úložišti, doporučujeme pravidelně znovu vygenerovat a otočit přístupový klíč k úložišti. Chcete-li povolit, abyste mohli udržovat připojení k účtu úložiště používat jeden přístupový klíč, zatímco si znovu vygenerujete druhý přístupový klíč jsou k dispozici dva přístupové klíče (primární i sekundární). Tento postup je také označován "postupného přístupových klíčů".

Služba Media Services, závisí na klíč úložiště, který mu je poskytnut. Konkrétně lokátory, které se používají ke streamování nebo stažení vaše prostředky závisí na přístupový klíč zadaný úložiště. Při vytváření účtu AMS trvá závislost na přístupový klíč primárního úložiště ve výchozím nastavení ale jako uživatel, můžete aktualizovat klíč úložiště, který má AMS. Musí se ujistěte, že se chcete, aby služba Media Services vědět, které klíč pro použití podle následujících kroků popsaných v tomto tématu.  

>[!NOTE]
> Pokud máte více účtů úložiště, můžete provést tento postup se každý účet úložiště. Pořadí, ve kterém otočit klíčů k úložišti není pevný. Otočit sekundární klíče první a pak primární klíč nebo naopak naopak.
>
> Před provedením kroků popsaných v tomto tématu na produkční účet, nezapomeňte otestovat v předprodukční účtu.
>

## <a name="steps-to-rotate-storage-keys"></a>Postup střídání úložiště klíčů 
 
 1. Změnit primární klíč účtu úložiště prostřednictvím rutiny prostředí powershell nebo [Azure](https://portal.azure.com/) portálu.
 2. Volání rutiny AzureRmMediaServiceStorageKeys synchronizace s odpovídající parametry vynutit účtu media a pokračovat tam, klíče účtu úložiště
 
    Následující příklad ukazuje, jak synchronizovat klíčů na účty úložiště.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Počkejte, než hodinu. Ověřte, že streamování scénářů pracují.
 4. Změnit sekundární klíč účtu úložiště prostřednictvím rutiny prostředí powershell nebo portálu Azure.
 5. Volání synchronizace AzureRmMediaServiceStorageKeys prostředí powershell s odpovídající parametry vynutit účtu media zachycení nových klíčů účtu úložiště. 
 6. Počkejte, než hodinu. Ověřte, že streamování scénářů pracují.
 
### <a name="a-powershell-cmdlet-example"></a>V příkladu rutiny prostředí powershell 

Následující příklad ukazuje, jak získat účet úložiště a synchronizovat s účtu AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Postup pro přidání do vašeho účtu AMS účty úložiště

Následující téma ukazuje, jak přidat účty úložiště do vašeho účtu AMS: [k účtu Media Services připojit více účtů úložiště](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Potvrzování
Rádi bychom se na vědomí následující osob, které podílí k vytvoření tohoto dokumentu: Cenk Dingiloglu, Gada Milán Seva Titov.
