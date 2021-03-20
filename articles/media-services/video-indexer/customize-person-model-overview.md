---
title: Přizpůsobení modelu osoby v Video Indexer – Azure
titleSuffix: Azure Media Services
description: Tento článek poskytuje přehled toho, co je model osoby v Video Indexer a jak ho přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 613ad2d0ab90bef016e80a33be12b71cba5e2f71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047053"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Přizpůsobení modelu osoby v Video Indexer

Video Indexer podporuje rozpoznávání celebrit ve vašich videích. Funkce rozpoznávání celebrit se zabývá přibližně 1 000 000 obličejemi na základě běžně vyžádaného zdroje dat, jako jsou IMDB, Wikipedii a nejvyšší vlivy na LinkedIn. Jsou pořád zjištěné plošky, které Video Indexer nerozpoznaly, ale nejsou pojmenované. Zákazníci mohou vytvořit vlastní modely osob a povolit Video Indexer rozpoznávání ploch, které nejsou ve výchozím nastavení rozpoznány. Zákazníci můžou tyto modely uživatelů sestavovat spárováním názvu osoby s obrázkovým obrázkem plochy osoby.  

Pokud váš účet využívá různé případy použití, můžete využít možnost vytvořit pro každý účet více modelů osob. Například pokud je obsah ve vašem účtu navržený tak, aby se seřadil do různých kanálů, možná budete chtít pro každý kanál vytvořit samostatný model osoby. 

> [!NOTE]
> Každý model Persone podporuje až 1 000 000 lidí a každý účet má limit 50ch modelů osob. 

Po vytvoření modelu je můžete použít k poskytnutí ID modelu konkrétního modelu osoby při nahrávání/indexování nebo přeindexování videa. Školení nové plochy pro video aktualizuje konkrétní vlastní model, ke kterému byl video přidruženo. 

Pokud nepotřebujete podporu modelu více osob, při nahrávání/indexování nebo přeindexování nepřiřazujte své video ID modelu osoby. V takovém případě Video Indexer použije ve svém účtu výchozí model Person. 

Web Video Indexer můžete použít k úpravě ploch zjištěných ve videu a ke správě více vlastních uživatelských modelů v účtu, jak je popsáno v tématu [Přizpůsobení modelu osoby pomocí webu](customize-person-model-with-website.md) . Můžete také použít rozhraní API, jak je popsáno v tématu [Přizpůsobení modelu osoby pomocí rozhraní API](customize-person-model-with-api.md).
