---
title: Vytváření pokročilých pracovních postupů kódování pomocí návrháře postupu provádění | Dokumentace Microsoftu
description: Další informace o vytváření pokročilých pracovních postupů kódování pomocí návrháře postupu provádění.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: 1521d18ab94c647aebf303b660574afe6612474d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250508"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Vytváření pokročilých pracovních postupů kódování pomocí Návrháře postupu provádění
## <a name="overview"></a>Přehled
**Návrháře postupu provádění** je nástroj klasické pracovní plochy Windows, který umožňuje navrhovat a vytvářet vlastní pracovní postupy pro kódování pomocí **pracovní postup kodéru Media Encoder Premium**.
Pomocí power tool Návrháře pracovního postupu můžete navrhovat a vytvářet komplexní pracovní postupy, které se spustí v **Media Encoder Premium**.  

Pracovní postupy mohou zahrnovat logiku pro rozhodování o zákazníka a větvení podle vlastnosti vstupní zdrojový soubor. Můžete vytvářet pracovní postupy pomocí přepisovatelné vlastnosti a dynamické hodnoty, které usnadňují opakujte a přizpůsobit v cloudu i nejsložitější kódovacích úloh.

Příklady pracovních postupů, které můžete vytvořit patří:

* Rozhodování na základě pracovních postupů, které kontrola zdrojový obsah pro řešení a kódování pouze sleduje požadovaný výstup.  Toto je helfpul odstraněním nevyužité, které by být generovány upscaling neúmyslně obsahu zdroje stop.
* Více vstupních souborů lze použít pro podporu popisků, překrytí a více společně obsah. 

Tento nástroj lze také změnit libovolné z našich [publikování pracovních postupů](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Pokud chcete získat kopii nástroje Návrhář postupu provádění, obraťte se prosím na mepd@microsoft.com.
> 
> 

Jakmile se vytvoří soubor pracovního postupu, se dají nahrát, jako prostředek a potom použije pro kódování mediálních souborů. Informace o tom, jak kódovat s **pracovní postup kodéru Media Encoder Premium** pomocí **.NET**, naleznete v tématu [Pokročilé kódování pomocí Media Encoderu Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Úprava stávajících pracovních postupů
Výchozí hodnota [publikování pracovních postupů](media-services-workflow-designer.md#existing_workflows) může být upraveno pomocí návrháře nástroj. Můžete získat výchozí soubory pracovního postupu [tady](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Složka také obsahuje popis těchto souborů.

Následující video předvádí způsob použití návrháře.

### <a name="day-1--getting-started"></a>Dne 1 – Začínáme
1. den video obsahuje:

* Přehled Návrháře
* Základní pracovní postupy – "Hello World"
* Vytvoření několika výstupních souborů MP4 pro použití se službou Azure Media Services streamování

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>2. den
2. den video obsahuje:

* Různé scénáře zdroje souborů – zpracování zvuku
* Pracovní postupy s pokročilou logikou
* Graf fází

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Den 3
Den 3 video obsahuje:

* Skriptování v rámci služby pracovních postupů a plány
* Omezení s aktuální kodér
* FUNKCE Q &AMP; A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

Pokud potřebujete podporu, nebo máte otázky k vytváření vlastních pracovních postupů v nástroji Návrhář pracovního postupu, pošlete prosím e-mail na mepd@microsoft.com.

## <a name="see-also"></a>Viz také
[Azure Premium Encoder pracovního postupu návrháře Školicí videa](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

