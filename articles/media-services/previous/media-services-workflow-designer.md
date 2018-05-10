---
title: Vytvoření Pokročilé kódování pracovních postupů pomocí návrháře pracovních postupů | Microsoft Docs
description: Další informace o tom, jak vytvořit Pokročilé kódování pracovní postupy pomocí Návrháře pracovního postupu.
services: media-services
documentationcenter: ''
author: anilmur
manager: cfowler
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: d75a3b58934b3da05a15700ecaf82226b549fffa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Vytváření pokročilých pracovních postupů kódování pomocí Návrháře postupu provádění
## <a name="overview"></a>Přehled
**Návrhář postupu provádění** je nástroj plochy Windows, který slouží k návrhu a vytvořit vlastní pracovní postupy pro kódování s **Media Encoder Premium pracovního postupu**.
Pomocí možnosti nástroje návrháře pracovního postupu můžete navrhnout a vytvořit komplexní pracovní postupy, které budou spouštět v **Media Encoder Premium**.  

Pracovní postupy můžou obsahovat logiku rozhodnutí zákazníka a vytvoření větve založený na vlastnosti souboru vstupní zdroj. Pracovní postupy můžete vytvořit pomocí přepisovatelné vlastnosti a dynamické hodnoty tak, aby snadno opakovat a přizpůsobit v cloudu i těch nejsložitějších úlohy kódování.

Příklady pracovních postupů, které můžete vytvořit patří:

* Pracovní postupy, které kontrolují zdrojový obsah pro řešení a kódování pouze sleduje požadovaný výstupní založené na rozhodnutí.  Toto je helfpul odstraněním nevyužité sleduje, které by vytvořilo upscaling obsahu neúmyslně zdroje.
* Více vstupní soubory můžete použít pro podporu titulků, překryvů a více společně obsah. 

Tento nástroj lze také můžete upravit všechny naše [publikovaná pracovních](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Pokud chcete získat vaší kopie nástroj Návrhář postupu provádění, obraťte se na mepd@microsoft.com.
> 
> 

Po vytvoření souboru pracovního postupu je možné uložit jako prostředek a pak použije pro kódování mediálních souborů. Informace o tom, jak kódování s **Media Encoder Premium pracovního postupu** pomocí **.NET**, najdete v části [Advanced kódování s pracovním postupem Premium Media Encoder](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Úprava stávajících pracovními postupy
Výchozí hodnota [publikovaná pracovních](media-services-workflow-designer.md#existing_workflows) se dají změnit pomocí nástroje návrháře. Výchozí nastavení můžete získat soubory pracovního postupu [zde](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Složka obsahuje také popis těchto souborů.

Následující videa ukazují, jak používat návrháře.

### <a name="day-1--getting-started"></a>Den 1 – Začínáme
1 den video obsahuje:

* Návrhář – přehled
* Základní pracovní postupy – "Hello, World"
* Vytvoření více výstupní soubory MP4 pro použití se službou Azure Media Services streamování

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Den 2
Video den 2 obsahuje:

* Různých scénáře souboru zdroje – zpracování zvuk
* Pracovní postupy s pokročilé logiky
* Fáze grafu

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Den 3
Den 3 video obsahuje:

* Skriptování uvnitř pracovních nebo plány
* Omezení s aktuální kodér
* MODUL OTÁZKY A ODPOVĚDI

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

Pokud potřebujete podporu nebo máte otázky o vytváření vlastních pracovních postupů v nástroji Návrhář pracovního postupu, pošlete e-mail na mepd@microsoft.com.

## <a name="see-also"></a>Viz také
[Azure Premium kodér pracovního postupu návrháře vzdělávání videa](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

