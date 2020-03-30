---
title: Přizpůsobení modelu osoby v video indexeru – Azure
titleSuffix: Azure Media Services
description: Tento článek poskytuje přehled o tom, co je model osoby v Video Indexer a jak jej přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838299"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Přizpůsobení modelu osoby v videoindexeru

Video Indexer podporuje rozpoznávání celebrit ve vašich videích. Funkce rozpoznávání celebrit pokrývá přibližně jeden milion tváří založených na běžně požadovaném zdroji dat, jako je IMDB, Wikipedia a top LinkedIn influencers. Plochy, které nejsou rozpoznány video indexerem, jsou stále detekovány, ale zůstanou nepojmenované. Zákazníci mohou vytvářet vlastní modely osob a povolit video indexeru rozpoznat tváře, které nejsou ve výchozím nastavení rozpoznány. Zákazníci mohou vytvořit tyto modely osob spárováním jména osoby s obrazovými soubory obličeje osoby.  

Pokud váš účet vyhovuje různým případům použití, můžete využít možnosti vytvořit více modelů osob na účet. Pokud má být například obsah ve vašem účtu seřazen do různých kanálů, můžete pro každý kanál vytvořit samostatný model osob. 

> [!NOTE]
> Každý model osoby podporuje až 1 milion lidí a každý účet má limit 50 modelů osob. 

Po vytvoření modelu jej můžete použít poskytnutím ID modelu konkrétního modelu osoby při nahrávání/indexování nebo přeindexování videa. Trénování nové tváře pro video aktualizuje konkrétní vlastní model, ke kterému bylo video přidruženo. 

Pokud nepotřebujete podporu modelu více osob, nepřiřazujte id modelu osoby k videu při nahrávání nebo indexování nebo přeindexování. V takovém případě bude video indexer používat ve vašem účtu výchozí model osoby. 

Pomocí webu Video Indexer můžete upravit tváře, které byly zjištěny ve videu, a spravovat ve svém účtu více vlastních modelů osob, jak je popsáno v [tématu Přizpůsobení osoby pomocí webu.](customize-person-model-with-website.md) Rozhraní API můžete také použít, jak je popsáno v [části Přizpůsobení modelu osoby pomocí rozhraní API](customize-person-model-with-api.md).
