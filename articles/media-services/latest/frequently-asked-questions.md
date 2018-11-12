---
title: Azure Media Services v3 – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje odpovědi na Azure Media Services v3 – nejčastější dotazy.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 26661a213b5df5424bf9ab9ad799345ae35620ea
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036763"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Nejčastější dotazy k Azure Media Services v3

Tento článek obsahuje odpovědi na nejčastější dotazy v3 Azure Media Services (AMS).

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Můžete použít na webu Azure portal ke správě prostředků v3

Zatím ne. Můžete použít jednu z podporovaných sad SDK. Prohlédněte si kurzy a ukázky v tomto dokumentu.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Existuje rozhraní API pro konfiguraci rezervovaných jednotek médií?

V současné době je nutné použít rozhraní API pro AMS v2 ke konfiguraci rezervované jednotky médií (jak je popsáno v [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). 

Při použití **VideoAnalyzerPreset** a/nebo **AudioAnalyzerPreset**, nastavit váš účet Media Services k 10 rezervované jednotky médií S3.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>Nemá V3 Asset koncept AssetFile?

AssetFiles byly odebrány z rozhraní API pro AMS, aby bylo možné oddělit Media Services od závislostí sady SDK služby Storage. Teď úložiště, ne Media Services, uchová informace, které patří úložiště. 

## <a name="where-did-client-side-storage-encryption-go"></a>Kde najdu šifrování na straně klienta úložiště?

Doporučujeme nyní šifrování na straně serveru úložiště, (která je ve výchozím). Další informace najdete v tématu [šifrování služby Azure Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Co je metoda doporučené nahrávání?

Doporučujeme, abyste že ingestuje použití http (s). Další informace najdete v tématu [http (s) ingestování](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Jak funguje stránkování?

Služba Media Services podporuje pro prostředky, které podporují OData $top, ale hodnota předaná $top musí být menší než 1 000 (například velikost stránky pro stránkování).

Díky tomu můžete buď získat malým vzorkem položek pomocí $top (například 100 nejnovější položky) nebo na stránce ale všechny položky pomocí stránkování. 

Media Services nepodporuje stránkování dat s uživatelem zadaná velikost stránky.

Další informace najdete v tématu [filtrování, řazení, stránkování](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Jak načíst entitu v Media Services v3?

V3 je založen na sjednocené rozhraní API povrch, který zpřístupňuje funkce správy a operace založená na **Azure Resource Manageru**. V souladu s maticí **Azure Resource Manageru**, jsou vždy jedinečné názvy prostředků. Jako názvy prostředků tedy můžete použít jakékoli řetězce jedinečného identifikátoru (například identifikátory GUID). 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přehled služby Media Services v3](media-services-overview.md)
