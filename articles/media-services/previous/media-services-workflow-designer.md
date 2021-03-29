---
title: Vytváření pokročilých pracovních postupů kódování pomocí Návrhář postupu provádění | Microsoft Docs
description: Přečtěte si, jak vytvořit pokročilé pracovní postupy kódování pomocí Návrhář postupu provádění.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 744d302afd21e6521fb17bf7bef6e68d21fb8372
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639394"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Vytváření pokročilých pracovních postupů kódování pomocí Návrháře postupu provádění

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Přehled
**Návrhář postupu provádění** je desktopový Nástroj pro Windows, který se používá k návrhu a vytváření vlastních pracovních postupů pro kódování pomocí **Media Encoder Premium Workflow**.
Pomocí výkonného nástroje pro návrháře pracovních postupů můžete navrhovat a vytvářet komplexní pracovní postupy, které se spustí v **Media Encoderu Premium**.  

Pracovní postupy mohou zahrnovat logiku rozhodování o zákaznících a větvení na základě vlastností vstupního zdrojového souboru. Můžete vytvářet pracovní postupy s přepisovatelnými vlastnostmi a dynamickými hodnotami, aby bylo možné i nejsložitější úlohy kódování snadno opakovat a přizpůsobit v cloudu.

Mezi ukázkové pracovní postupy, které můžete vytvořit, patří:

* Pracovní postupy založené na rozhodnutích, které kontrolují zdrojový obsah pro rozlišení a zakódovat pouze požadované stopy výstupu.  To je užitečné, pokud chcete odstranit odpadní stopy, které by byly vygenerovány při neúmyslném škálování zdrojového obsahu.
* K podpoře titulků, překryvů a spojování obsahu lze použít více vstupních souborů. 

Tento nástroj lze také použít k úpravě kteréhokoli z našich [publikovaných pracovních postupů](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Pokud chcete získat kopii Návrhář postupu prováděního nástroje, kontaktujte prosím mepd@microsoft.com .

Po vytvoření je soubor pracovního postupu možné ho nahrát jako Asset a pak ho použít k Kódování mediálních souborů. Informace o tom, jak kódovat pomocí **Media Encoder Premium Workflow** pomocí **rozhraní .NET**, najdete v tématu [Rozšířené kódování pomocí Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Upravit existující pracovní postupy
Výchozí [publikované pracovní postupy](media-services-workflow-designer.md#existing_workflows) lze upravit pomocí nástroje návrháře. Výchozí soubory pracovního postupu můžete získat [tady](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/media-services/previous/media-services-encode-with-premium-workflow.md). Složka obsahuje také popis těchto souborů.

Následující videa ukazují, jak používat návrháře.

### <a name="day-1--getting-started"></a>Den 1 – Začínáme
1. den pokrývá video:

* Přehled návrháře
* Základní pracovní postupy – "Hello World"
* Vytváření více výstupních souborů MP4 pro použití s Azure Media Servicesm streamování

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Den 2
1. den pokrývá video:

* Různé scénáře zdrojového souboru – manipulace se zvukem
* Pracovní postupy s pokročilou logikou
* Fáze grafu

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Den 3
Video o dni 3 se zabývá:

* Skriptování v rámci pracovních postupů/modrotisky
* Omezení s aktuálním kodérem
* Otázky a odpovědi

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [novou žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Výuková videa pro Azure Premium Encoder Návrhář postupu provádění](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

