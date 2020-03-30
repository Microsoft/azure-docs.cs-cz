---
title: Přizpůsobení jazykového modelu v video indexeru – Azure
titleSuffix: Azure Media Services
description: Tento článek poskytuje přehled o tom, co je jazykový model v Video Indexer a jak jej přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b096b9352be65033f2fb782b118e815dc16b43b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838315"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Přizpůsobení jazykového modelu pomocí videoindexeru

Video Indexer podporuje automatické rozpoznávání řeči prostřednictvím integrace se [službou](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/)Microsoft Custom Speech Service . Jazykový model si můžete přizpůsobit nahráním adaptačního textu, konkrétně textu z domény, jehož slovní zásobu chcete přizpůsobit. Jakmile táhnete model, budou rozpoznána nová slova uvedená v textu adaptace za předpokladu výchozí výslovnosti a jazykový model se naučí nové pravděpodobné sekvence slov. Vlastní jazykové modely jsou podporovány pro angličtinu, španělštinu, francouzštinu, němčinu, italštinu, čínštinu (zjednodušenou), japonštinu, ruštinu, brazilskou portugalštinu, hindštinu a korejštinu. 

Vezměme si slovo, které je velmi specifické, jako "Kubernetes" (v kontextu služby Azure Kubernetes), jako příklad. Vzhledem k tomu, slovo je nové Video Indexer, je uznáván jako "komunity". Musíte trénovat model rozpoznat jako "Kubernetes". V ostatních případech slova existují, ale jazykový model neočekává, že se zobrazí v určitém kontextu. Například "kontejnerová služba" není dvouslovná sekvence, kterou by nespecializovaný jazykový model rozpoznal jako určitou sadu slov.

Máte možnost nahrát slova bez kontextu v seznamu v textovém souboru. To se považuje za částečnou adaptaci. Případně můžete nahrát textové soubory dokumentace nebo věty související s vaším obsahem pro lepší přizpůsobení.

Rozhraní API videoindexeru nebo web můžete použít k vytvoření a úpravám vlastních jazykových modelů, jak je popsáno v tématech v části [Další kroky](#next-steps) tohoto tématu.

## <a name="best-practices-for-custom-language-models"></a>Doporučené postupy pro vlastní jazykové modely

Video Indexer se učí na základě pravděpodobností kombinací slov, takže se nejlépe naučíte:

* Uveďte dostatek skutečných příkladů vět, jak by se mluvilo.
* Vložte pouze jednu větu na řádek, ne více. V opačném případě se systém naučí pravděpodobnosti napříč větami.
* Je v pořádku dát jedno slovo jako větu, která posílí slovo proti ostatním, ale systém se nejlépe učí z celých vět.
* Při zavádění nových slov nebo zkratky, pokud je to možné, dát co nejvíce příkladů použití v celé větě dát co nejvíce kontextu, jak je to možné do systému.
* Snažte se dát několik možností přizpůsobení, a uvidíte, jak pracují pro vás.
* Vyhněte se opakování přesně stejné věty vícekrát. To může vytvořit zaujatost proti zbytku vstupu.
* Vyhněte se zahrnutí neobvyklých symbolů (~, # @ % &), protože budou zahozeny. Věty, ve kterých se zobrazí, budou také vyřazeny.
* Vyhněte se uvedení příliš velké vstupy, jako jsou stovky tisíc vět, protože tím se zředí účinek zvýšení.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md)

[Přizpůsobení jazykového modelu pomocí webu](customize-language-model-with-website.md)
