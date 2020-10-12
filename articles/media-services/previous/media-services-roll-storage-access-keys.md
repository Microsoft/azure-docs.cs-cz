---
title: Aktualizovat Media Services po navracení přístupových klíčů k úložišti | Microsoft Docs
description: V tomto článku najdete pokyny, jak aktualizovat Media Services po zavedení přístupových klíčů k úložišti.
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
ms.openlocfilehash: c68bedb37722fb6a8b7ad9dccdeaaaa4fab9d020
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264126"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Aktualizace Media Services po postupném zavedení přístupových klíčů k úložišti

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Když vytvoříte nový účet Azure Media Services (AMS), zobrazí se také výzva k výběru účtu Azure Storage, který se používá k uložení mediálního obsahu. K vašemu Media Services účtu můžete přidat více účtů úložiště. Tento článek ukazuje, jak otočit klíče úložiště. Také ukazuje, jak přidat účty úložiště k účtu média. 

K provedení akcí popsaných v tomto článku byste měli používat [rozhraní Azure Resource Manager API](/rest/api/media/operations/azure-media-services-rest-api-reference) a [PowerShell](/powershell/module/az.media).  Další informace najdete v tématu [Správa prostředků Azure pomocí PowerShellu a správce prostředků](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

Když se vytvoří nový účet úložiště, Azure vygeneruje 2 512 přístupové klíče k úložišti, které se používají k ověření přístupu k vašemu účtu úložiště. Aby se vaše připojení úložiště lépe zabezpečilo, doporučuje se pravidelně znovu vygenerovat a otočit přístupový klíč k úložišti. K dispozici jsou dva přístupové klíče (primární a sekundární), aby bylo možné spravovat připojení k účtu úložiště pomocí jednoho přístupového klíče, zatímco znovu vygenerujete druhý přístupový klíč. Tento postup se nazývá také "přístupové klíče pro vracení".

Media Services závisí na klíči úložiště, který je mu k dispozici. Konkrétně Lokátory, které se používají ke streamování nebo stahování vašich assetů, závisí na zadaném přístupovém klíči úložiště. Při vytvoření účtu AMS se ve výchozím nastavení převezme závislost na primárním přístupovém klíči úložiště, ale jako uživatel můžete aktualizovat klíč úložiště, který AMS má. Musíte se ujistit, že Media Services znát, který klíč chcete použít, podle kroků popsaných v tomto článku.  

>[!NOTE]
> Pokud máte více účtů úložiště, provedete tento postup u každého účtu úložiště. Pořadí, ve kterém se klíče úložiště otočí, není pevně dané. Sekundární klíč můžete nejdřív otočit a pak primární klíč nebo naopak.
>
> Před provedením kroků popsaných v tomto článku na produkčním účtu se ujistěte, že je otestujete na předprodukčním účtu.
>

## <a name="steps-to-rotate-storage-keys"></a>Postup pro otočení klíčů úložiště 
 
 1. Pomocí rutiny PowerShellu nebo webu [Azure](https://portal.azure.com/) Portal změňte primární klíč účtu úložiště.
 2. Volejte Sync-AzMediaServiceStorageKeys rutinu s příslušnými parametry pro vynucení účtu média pro vyzvednutí klíčů účtu úložiště.
 
    Následující příklad ukazuje, jak synchronizovat klíče s účty úložiště.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Počkejte hodinu nebo. Ověřte, jestli fungují scénáře streamování.
 4. Pomocí rutiny PowerShellu nebo Azure Portal změňte sekundární klíč účtu úložiště.
 5. Pokud chcete vynutit, aby si účet média vybral nové klíče účtu úložiště, zavolejte Sync-AzMediaServiceStorageKeys PowerShell s příslušnými parametry. 
 6. Počkejte hodinu nebo. Ověřte, jestli fungují scénáře streamování.
 
### <a name="a-powershell-cmdlet-example"></a>Příklad rutiny PowerShellu 

Následující příklad ukazuje, jak získat účet úložiště a synchronizovat ho s účtem AMS.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```
 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Postup přidání účtů úložiště do účtu AMS

Následující článek ukazuje, jak přidat účty úložiště do účtu AMS: [připojení více účtů úložiště k účtu Media Services](./media-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Poděkování
Chtěli bychom potvrdit následující lidi, kteří přispěli k vytváření tohoto dokumentu: Cenk Dingiloglu, Milán Gada, Seva Titov.
