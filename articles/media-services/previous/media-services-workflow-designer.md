---
title: Vytvoření pokročilých pracovních postupů kódování pomocí návrháře pracovních postupů | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvořit pokročilé pracovní postupy kódování pomocí Návrháře pracovních postupů.
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
ms.date: 03/20/2019
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 4dceb558532305c6d2e84563e25ab05508423090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72801954"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Vytváření pokročilých pracovních postupů kódování pomocí Návrháře postupu provádění  
## <a name="overview"></a>Přehled
**Návrhář pracovních postupů** je nástroj pro stolní počítače systému Windows, který se používá k navrhování a vytváření vlastních pracovních postupů pro kódování pomocí **pracovního postupu programu Media Encoder Premium**.
Pomocí výkonu nástroje návrháře pracovních postupů můžete navrhovat a vytvářet složité pracovní postupy, které budou spuštěny v **programu Media Encoder Premium**.  

Pracovní postupy mohou zahrnovat logiku rozhodování zákazníka a větvení na základě vlastností vstupního zdrojového souboru. Můžete vytvářet pracovní postupy s překážnými vlastnostmi a dynamickými hodnotami, aby bylo možné v cloudu snadno opakovat a přizpůsobit i ty nejsložitější úlohy kódování.

Mezi příklady pracovních postupů, které můžete vytvořit, patří:

* Pracovní postupy založené na rozhodnutí, které kontrolují zdrojový obsah pro rozlišení a kódují pouze požadované výstupní stopy.  To je užitečné tím, že eliminuje plýtvání stopy, které by byly generovány upscaling zdrojového obsahu neúmyslně.
* Více vstupních souborů lze použít k podpoře titulků, překryvy a sešívání obsahu. 

Tento nástroj lze také použít k úpravě některého z našich [publikovaných pracovních postupů](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Chcete-li získat kopii nástroje Návrhář mepd@microsoft.compracovního postupu, obraťte se na aplikaci .

Jakmile je soubor pracovního postupu vytvořen, lze jej nahrát jako datový zdroj a poté jej použít pro kódování mediálních souborů. Informace o kódování pomocí **pracovního postupu kodéru technologie Media Encoder Premium** pomocí **rozhraní .NET**naleznete v [tématu Pokročilé kódování pomocí pracovního postupu programu Media Encoder Premium](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Úprava existujících pracovních postupů
Výchozí [publikované pracovní postupy](media-services-workflow-designer.md#existing_workflows) lze upravit pomocí nástroje návrháře. Zde můžete získat výchozí [soubory](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)pracovního postupu . Složka také obsahuje popis těchto souborů.

Následující videa ukazují, jak používat návrháře.

### <a name="day-1--getting-started"></a>Den 1 – Začínáme
Den 1 video zahrnuje:

* Přehled návrháře
* Základní pracovní postupy – "Hello World"
* Vytváření více výstupních souborů MP4 pro použití se streamováním služby Azure Media Services

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>2. den
Den 2 video zahrnuje:

* Scénáře s různými zdrojovými soubory – zpracování zvuku
* Pracovní postupy s pokročilou logikou
* Fáze grafu

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Den 3
Den 3 video zahrnuje:

* Skriptování uvnitř pracovních postupů/podrobných plánů
* Omezení se stávajícím kodérem
* Q&A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [Novou žádost o podporu.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Školicí videa návrháře pracovních postupů pro azure premium kodér](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

