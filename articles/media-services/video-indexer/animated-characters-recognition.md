---
title: Animovaná detekce znaků pomocí Video Indexer
titleSuffix: Azure Media Services
description: Toto téma ukazuje, jak použít animovanou detekci znaků pomocí Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854746"
---
# <a name="animated-character-detection-preview"></a>Detekce animovaných postav (Preview)

Azure Media Services Video Indexer podporuje detekci, seskupení a rozpoznávání znaků v animovaném obsahu prostřednictvím integrace s [Cognitive Services vlastní vize](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Tato funkce je k dispozici prostřednictvím portálu i přes rozhraní API.

Po nahrání animovaného videa s konkrétním modelem animace Video Indexer extrahuje klíčové snímky, detekuje animované znaky v těchto rámečcích, seskupí podobný znak a zvolí nejlepší vzorek. Pak pošle seskupené znaky Custom Vision, které identifikují znaky na základě modelů, na kterých byla vyškolená. 

Než začnete s školením modelu, zjistí se namelessly znaky. Při přidávání názvů a výukovém modelu Video Indexer rozpozná tyto znaky a odpovídajícím způsobem je pojmenujte.

## <a name="flow-diagram"></a>Diagram toku

Následující diagram znázorňuje tok animovaného procesu detekce znaků.

![Diagram toku](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Účty

V závislosti na typu Video Indexer účtu jsou k dispozici různé sady funkcí. Informace o tom, jak připojit účet k Azure, najdete v tématu [Vytvoření účtu video indexer připojeného k Azure](connect-to-azure.md).

* Zkušební účet: Video Indexer používá k vytvoření modelu interní účet Custom Vision a připojuje ho ke svému účtu Video Indexer. 
* Placený účet: připojíte účet Custom Vision k účtu Video Indexer (pokud ho ještě nemáte, musíte nejdřív vytvořit účet).

### <a name="trial-vs-paid"></a>Zkušební verze vs. placená

|Funkce|Zkušební verze|Placené|
|---|---|---|
|Účet Custom Vision|Spravováno na pozadí Video Indexer. |Váš účet Custom Vision je připojen k Video Indexer.|
|Počet modelů animace|Jednu|Až 100 modelů na účet (omezení Custom Vision).|
|Trénování modelu|Video Indexer navlacích model pro nové znaky další příklady stávajících znaků.|Vlastník účtu naplní model, jakmile budou připraveni provést změny.|
|Rozšířené možnosti v Custom Vision|K portálu Custom Vision nemáte přístup.|Modely můžete upravit sami na portálu Custom Vision.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Použití animovaného rozpoznávání znaků s portálem a rozhraním API

Podrobnosti najdete v tématu [použití animovaného rozpoznávání znaků s portálem a rozhraním API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Omezení

* V současné době není schopnost "Identifikace animace" v oblasti East-Asia podporována.
* Znaky, které se zdají být malé nebo daleko ve videu, nemusí být správně identifikovány, pokud kvalita videa není nízká.
* Doporučení je použití modelu na sadu animovaných znaků (například na animovanou řadu).

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)