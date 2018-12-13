---
title: Přizpůsobení modelu osoby v Video Indexer – Azure
titlesuffix: Azure Media Services
description: Tento článek poskytuje přehled o jaký je model osoby v Video Indexer a jak ji přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285022"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Přizpůsobení modelu osoby v Video Indexer


Video Indexer podporuje rozpoznávání tváří a rozpoznávání celebrit v případě video obsahu. Funkce rozpoznávání celebrit pokrývá přibližně 1 000 000 tváří založené na zdroji dat běžně požadovaných například IMDB Wikipedia a horní vlivné osoby LinkedIn. Zjištění tváří, které nejsou rozpoznány aplikací funkce rozpoznávání celebrit; Nicméně jsou ponechána bez názvu. Po nahrání videa do modulu Video Indexer a získat výsledky zpět, můžete přejít zpět a pojmenujte tváří, které nebyly rozpoznány. Jakmile je označování plochy s názvem pro rozpoznávání tváře a název nechejte se přidat do vašeho účtu osoba modelu. Video Indexer pak zjistit tento tvář budoucí videa a posledních videa.

Video Indexer webu nebo rozhraní API můžete použít k úpravě tváří, které byly zjištěny v videa ve vašem účtu, jak je popsáno v následujících tématech:

- [Přizpůsobení modelu osoba s použitím rozhraní API](customize-person-model-with-api.md)
- [Přizpůsobení modelu osoba na webu](customize-person-model-with-website.md)
