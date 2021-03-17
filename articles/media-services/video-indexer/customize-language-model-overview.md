---
title: Přizpůsobení jazykového modelu v Video Indexer – Azure
titleSuffix: Azure Media Services
description: Tento článek poskytuje přehled toho, co je jazykový model v Video Indexer a jak ho přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 29490e08748a37f7eb93fbb8804f55f74c53df35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047130"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Přizpůsobení jazykového modelu pomocí Video Indexer

Video Indexer podporuje automatické rozpoznávání řeči prostřednictvím integrace s Microsoft [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Jazykové modely můžete přizpůsobit nahráním přizpůsobeného textu, konkrétně textu z domény, kde chcete, aby se modul přizpůsobil. Jakmile provedete svůj model, budou se rozpoznat nová slova, která se objeví v textu úpravy. předpokládá se výchozí výslovnost a jazykový model se seznámí s novými pravděpodobnými sekvencemi slov. Vlastní jazykové modely se podporují pro angličtinu, španělštinu, francouzštinu, němčinu, italštinu, čínštinu (zjednodušenou), japonštinu, ruštinu, portugalštinu, hindština a korejštinu. 

Podíváme se na velmi konkrétní slovo, jako je například "Kubernetes" (v kontextu služby Azure Kubernetes), jako příklad. Vzhledem k tomu, že je slovo v Video Indexer nové, je rozpoznáno jako "komunity". Chcete-li tento model rozpoznat jako "Kubernetes", je nutné ho vyškolit. V jiných případech existují slova, ale jazykový model je neočekává, že se zobrazí v určitém kontextu. Například "kontejnerová služba" není 2 – sekvence slov, které nespecializované jazykové modely rozpoznávají jako konkrétní sadu slov.

Máte možnost nahrávat slova bez kontextu do seznamu v textovém souboru. To je považováno za částečné přizpůsobení. Alternativně můžete odeslat textové soubory dokumentace nebo věty týkající se vašeho obsahu pro lepší úpravu.

Pomocí rozhraní Video Indexer API nebo webu můžete vytvořit a upravit vlastní jazykové modely, jak je popsáno v tématech v části [Další kroky](#next-steps) v tomto tématu.

## <a name="best-practices-for-custom-language-models"></a>Osvědčené postupy pro vlastní jazykové modely

Video Indexer se učí na základě pravděpodobnosti kombinací slov, takže se můžete seznámit nejlépe:

* Poskytněte dostatek reálných příkladů vět, které by byly mluvené.
* Vloží jenom jednu větu na řádek, ne další. V opačném případě se systém bude učit mezi větami.
* Je možné vložit jedno slovo jako větu, aby bylo možné slovo zvýšit proti ostatním, ale systém se učí nejlépe od plných vět.
* Pokud je to možné, popište nová slova nebo akronymy, pokud je to možné, a poskytněte tolik příkladů využití v plné větě, aby systém mohl co nejvíc poskytnout co nejvíce kontextu.
* Zkuste vložit několik možností přizpůsobení a podívejte se, jak to budou fungovat.
* Vyhněte se opakování přesně stejné věty několikrát. Může vytvořit posun proti zbytku vstupu.
* Vyhněte se zahrnutí neobvyklých symbolů (~, # @% &), protože budou zahozeny. Věty, ve kterých se zobrazují, budou také zahozeny.
* Vyhněte se vkládání příliš velkých vstupů, jako jsou stovky tisíc vět, protože by to mělo zvýšit účinek zvýšení.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md)

[Přizpůsobení jazykového modelu s použitím webu](customize-language-model-with-website.md)
