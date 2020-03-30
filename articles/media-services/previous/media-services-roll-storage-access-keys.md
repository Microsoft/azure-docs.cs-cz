---
title: Aktualizace mediálních služeb po přístupových klíčích pro zpřístupnění ke rolling storage | Dokumenty společnosti Microsoft
description: V tomto článku se můžete naučit aktualizovat služby Media Services po zpřístupnění klíčů ke rolling storage.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 2a0d1c5af572c88dc11bed950b46706f0a2f081f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981966"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Aktualizace Media Services po postupném zavedení přístupových klíčů k úložišti 

Když vytvoříte nový účet Azure Media Services (AMS), budete také vyzváni k výběru účtu Azure Storage, který se používá k ukládání mediálního obsahu. Do účtu Mediálních služeb můžete přidat více než jeden účet úložiště. Tento článek ukazuje, jak otáčet klíče úložiště. Také ukazuje, jak přidat účty úložiště do účtu média. 

Chcete-li provést akce popsané v tomto článku, měli byste používat [Azure Resource Manager API](/rest/api/media/operations/azure-media-services-rest-api-reference) a [Powershell](https://docs.microsoft.com/powershell/module/az.media).  Další informace najdete v tématu [Správa prostředků Azure pomocí PowerShellu a Správce prostředků](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

Když se vytvoří nový účet úložiště, Azure generuje dva 512bitové klíče přístupu k úložišti, které se používají k ověření přístupu k vašemu účtu úložiště. Chcete-li, aby byla připojení úložiště bezpečnější, doporučujeme pravidelně obnovovat a otáčet přístupový klíč úložiště. Dva přístupové klíče (primární a sekundární) jsou k dispozici, aby vám umožní udržovat připojení k účtu úložiště pomocí jednoho přístupového klíče při regeneraci druhého přístupového klíče. Tento postup se také nazývá "klíčem pro sádkový přístup".

Mediální služby závisí na klíči úložiště, který je k němu poskytnut. Konkrétně lokátory, které se používají k streamování nebo stahování vašich datových zdrojů, závisí na zadaném přístupovém klíči úložiště. Při vytvoření účtu AMS trvá závislost na primárním přístupovém klíči úložiště ve výchozím nastavení, ale jako uživatel můžete aktualizovat klíč úložiště, který má AMS. Je třeba, aby media services vědět, který klíč použít pomocí následujících kroků popsaných v tomto článku.  

>[!NOTE]
> Pokud máte více účtů úložiště, provedete tento postup s každým účtem úložiště. Pořadí, ve kterém střídáte klíče úložiště, není pevné. Nejprve můžete otočit sekundární klíč a potom primární klíč nebo naopak.
>
> Před provedením kroků popsaných v tomto článku na produkčním účtu je nezapomeňte otestovat na předprodukčním účtu.
>

## <a name="steps-to-rotate-storage-keys"></a>Postup otočení klíčů úložiště 
 
 1. Změňte účet úložiště Primární klíč prostřednictvím rutiny powershellu nebo [portálu Azure.](https://portal.azure.com/)
 2. Volání rutiny Sync-AzMediaServiceStorageKeys s příslušnými paramy, které nutí mediální účet k vyzvednutí klíčů účtu úložiště
 
    Následující příklad ukazuje, jak synchronizovat klíče s účty úložiště.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Počkejte asi hodinu. Ověřte, zda scénáře streamování fungují.
 4. Změňte sekundární klíč účtu úložiště prostřednictvím rutiny powershellu nebo portálu Azure.
 5. Volání Sync-AzMediaServiceStorageKeys powershell s příslušnými params vynutit mediální účet vyzvednout nové klíče účtu úložiště. 
 6. Počkejte asi hodinu. Ověřte, zda scénáře streamování fungují.
 
### <a name="a-powershell-cmdlet-example"></a>Příklad rutiny powershellu 

Následující příklad ukazuje, jak získat účet úložiště a synchronizovat jej s účtem AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Postup přidání účtů úložiště do účtu AMS

Následující článek ukazuje, jak přidat účty úložiště do účtu AMS: [Připojení více účtů úložiště k účtu Mediální služby](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Poděkování
Rádi bychom ocenili následující lidi, kteří přispěli k vytvoření tohoto dokumentu: Cenk Dingiloglu, Milan Gada, Seva Titov.
