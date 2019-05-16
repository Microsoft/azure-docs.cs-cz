---
title: Přizpůsobení modelu osoby v Video Indexer – Azure
titlesuffix: Azure Media Services
description: Tento článek poskytuje přehled o jaký je model osoby v Video Indexer a jak ji přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c74b913fc3ac35039d914fc97c9c438d2e4a3069
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799430"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Přizpůsobení modelu osoby v Video Indexer

Video Indexer umožňuje rozpoznávání celebrit videí. Funkce rozpoznávání celebrit pokrývá přibližně jednoho milionu tváří, které jsou založené na zdroji dat běžně požadovaných například IMDB Wikipedia a horní vlivné osoby LinkedIn. Detekována i tváří, které nejsou rozpoznány modulem Video Indexer, ale jsou ponechána bez názvu. Zákazníky můžete vytvářet vlastní modely osoby a povolit Video Indexer k rozpoznávání tváří, které nejsou rozpoznány ve výchozím nastavení. Zákazníci mohou vytvářet tyto osoby modely tak, že spárujete s soubory obrázků pro rozpoznávání tváře osoby jméno osoby.  

Pokud váš účet určeného pro různé případy použití, vám může přinést nebudou moct vytvořit několik modelů osoba na jeden účet. Pokud se obsah ve vašem účtu na rozděleny na různé kanály, můžete chtít vytvořit samostatné osoba model pro každý kanál. 

> [!NOTE]
> Každá osoba model podporuje až 1 milión lidí a každý účet může obsahovat maximálně 50 modelů osoby. 

Po vytvoření modelu, můžete zadáním ID modelu konkrétní osobě modelu při nahrávání/indexování nebo Reindexace videa. Školení nové tváře video, aktualizuje konkrétní vlastního modelu, který byl přidružený videa. 

Pokud nepotřebujete podpora více modelů osoby, nepřiřazujte osoba ID modelu videa při nahrávání/indexování nebo přeindexování. V takovém případě Video Indexer použije výchozí model osoba ve vašem účtu. 

Video Indexer webu můžete použít k úpravě tváří, které byly zjištěny ve videu a ke správě více vlastních modelů osoba ve vašem účtu, jak je popsáno v [přizpůsobit osoba model s použitím webu](customize-person-model-with-website.md) tématu. Můžete také použít rozhraní API, jak je popsáno v [přizpůsobit osoba model s použitím rozhraní API](customize-person-model-with-api.md).