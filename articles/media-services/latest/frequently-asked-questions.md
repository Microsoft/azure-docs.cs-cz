---
title: Nejčastější dotazy k Azure Media Services v3 | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy v3 Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/29/2018
ms.author: juliako
ms.openlocfilehash: 7fe59763162ca53ea0256d5902aeece90525f214
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654875"
---
# <a name="azure-media-services-v3-preview-frequently-asked-questions"></a>Azure Media Services v3 (preview) nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy v3 Azure Media Services (AMS).

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Můžete použít na portálu Azure ke správě prostředků v3?

Zatím ne. Můžete použít jednu z podporovaných sad SDK. V tématu výukové programy a ukázky v této sadě dokumentů.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Existuje rozhraní API pro konfiguraci jednotky rezervované pro média?

Tým služby Media Services je odstranění RUs v v3. Ale práce potřebné služby není úplný. Do té doby se zákazníci, mají nastavení RUs pomocí portálu Azure nebo rozhraní API pro AMS v2 (jak je popsáno v [škálování zpracování média](../previous/media-services-scale-media-processing-overview.md). 

S3 RUs jsou nezbytné, aby **VideoAnalyzerPreset** a **AudioAnalyzerPreset** přednastavení V3.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>Má V3 Asset žádná koncepce AssetFile?

AssetFiles byly odebrány z rozhraní API pro AMS aby bylo možné oddělit Media Services z závislostí sady SDK úložiště. Teď úložiště, není Media Services, uchová informace, které patří úložiště. 

## <a name="where-did-client-side-storage-encryption-go"></a>Kde? šifrování úložiště na straně klienta

Doporučujeme vám teď úložiště serverové šifrování (ve výchozím nastavení zapnutý). Další informace najdete v tématu [šifrování služby úložiště Azure pro Data v klidovém stavu](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Co je metoda doporučené nahrávání?

Doporučujeme, abyste že ingestuje použití http (s). Další informace najdete v tématu [ingestování http (s)](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Jak funguje stránkování?

Služba Media Services podporuje pro prostředky, které podporují OData $top, ale hodnota předaný $top musí být menší než 1 000 (například velikost stránky pro stránkování).

Můžete buď získat malé ukázkové položek pomocí $top (například 100 poslední položky) nebo na stránku, když všechny položky pomocí stránkování. 

Služba Media Services nepodporuje stránkování prostřednictvím data s uživatelem zadaná velikost stránky.

Další informace najdete v tématu [filtrování, řazení, stránkování](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Jak načíst entity ve službě Media Services v3?

V3 je založena na jednotné rozhraní API prostor, který zveřejňuje funkce Správa a provoz založený na **Azure Resource Manager**. V souladu s **Azure Resource Manager**, jsou vždy jedinečné názvy prostředků. Proto můžete použít všechny řetězce jedinečný identifikátor (například GUID) pro názvy prostředků. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přehled v3 Media Services](media-services-overview.md)
