---
title: Azure Media Services starší verze komponent | Microsoft Docs
description: Toto téma popisuje Azure Media Services starších komponent.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2019
ms.author: juliako
ms.openlocfilehash: e4ff157f58a68c68f8610c6c473f5d69897650ad
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338416"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starší součásti

V průběhu času byla k dispozici stabilní vylepšení a vylepšení komponent Media Service. V důsledku toho byly některé starší komponenty vyřazeny. Pokyny, jak migrovat aplikaci ze starší verze na aktuální komponentu, najdete v následujících článcích.

## <a name="legacy-components-and-migration-guidance"></a>Starší verze komponent a pokyny k migraci

Oznamujeme vyřazení *Windows Azure Media Encoder* (WAME) a *Azure Media Encoderch* (ázev) mediálních procesorů. Tyto procesory se zařadí do 30. listopadu 2019.

* [Migrace z Windows Azure Media Encoder na Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrace z Azure Media Encoder na Media Encoder Standard](migrate-azure-media-encoder.md)

Oznamujeme také vyřazení *Azure Media Indexer v1* a *Azure Media Indexer v2 Preview*. Procesor médií [Azure Media Indexer v1](media-services-index-content.md) bude vyřazení od 1. října 2020. Od 1. ledna 2019 se vyřadí procesory médií[verze Preview [Azure Media Indexer v2](media-services-process-content-with-indexer2.md) .  [Azure Media Services video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) nahrazuje tyto starší verze procesorů médií.

* [Migrujte z Azure Media Indexer V1 a Azure Media Indexer v2 na Azure Media Services video indexer](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Další kroky

[Pokyny k migraci pro přesun z Media Services V2 na V3](../latest/migrate-from-v2-to-v3.md)
