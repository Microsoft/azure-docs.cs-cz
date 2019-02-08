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
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: a447c359c38c2173ea42b6d717067fc8b3a88f9a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875487"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Nejčastější dotazy k Azure Media Services v3

Tento článek obsahuje odpovědi na nejčastější dotazy v3 Azure Media Services (AMS).

## <a name="v3-apis"></a>rozhraní API v3

### <a name="how-do-i-configure-media-reserved-units"></a>Jak nakonfigurovat rezervovaných jednotek médií?

Pro analýzu zvuku a videa analytických úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme pro účet zřídit s 10 použité položky S3. Pokud potřebujete více než 10 použité položky S3, otevřete lístek podpory pomocí [webu Azure portal](https://portal.azure.com/).

Podrobnosti najdete v tématu [škálování zpracování médií pomocí rozhraní příkazového řádku](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Co je doporučovaná metoda pro proces videa?

Odeslání úlohy pomocí adresy URL pro http (s), video se doporučuje. Další informace najdete [http (s) ingestování](job-input-from-http-how-to.md). Nejsou vyžadovány pouze vytvořit Asset s vstupního videa, než může být zpracována.

### <a name="how-does-pagination-work"></a>Jak funguje stránkování?

Při použití stránkování, vždy používejte odkaz na další a výčet kolekce není závislý na konkrétní stránce velikost. Podrobnosti a příklady najdete v tématu [filtrování, řazení, stránkování](entities-overview.md).

## <a name="live-streaming"></a>Živé streamování 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Jak vložit konce, videa a obrázků slaty během živé streamování?

Služba Media Services v3 kódování v reálném čase zatím nepodporuje vkládání video nebo obrázkových slaty během živého datového proudu. 

Můžete použít [live encoder místní](recommended-on-premises-live-encoders.md) přepnutí zdrojového videa. Mnoho aplikací poskytují možnost přepnout zdrojů, včetně Telestream Wirecast, přepínání Studio (v Iosu), OBS Studio (bezplatnou aplikaci) a mnoho dalších.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 a v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Můžete použít na webu Azure portal ke správě prostředků v3

Zatím ne. Můžete použít jednu z podporovaných sad SDK. Prohlédněte si kurzy a ukázky v tomto dokumentu.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Je ve verzi 3 koncept AssetFile?

AssetFiles byly odebrány z rozhraní API pro AMS, aby bylo možné oddělit Media Services od závislostí sady SDK služby Storage. Teď úložiště, ne Media Services, uchová informace, které patří úložiště. 

Další informace najdete v tématu [migrace do služby Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Kde najdu šifrování na straně klienta úložiště?

Nyní se doporučuje použít šifrování úložiště na straně serveru (který je ve výchozím). Další informace najdete v tématu [šifrování služby Azure Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přehled služby Media Services v3](media-services-overview.md)
