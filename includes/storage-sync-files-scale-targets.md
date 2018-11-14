---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 03fe587ede297ac7dea90b7a5fb2d5323f60659e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628113"
---
| Prostředek | Cíl | Pevný limit |
|----------|--------------|------------|
| Služby synchronizace úložiště na předplatné | 15 služby synchronizace úložiště na oblast | Ne |
| Skupiny synchronizace služby synchronizace úložiště | 100 skupiny synchronizace. | Ano |
| Registrované servery služby synchronizace úložiště | 99 servery | Ano |
| Koncové body cloudu na skupinu synchronizace | koncový bod cloudu. 1 | Ano |
| Koncové body serveru na skupinu synchronizace | 50 koncové body serveru | Ne |
| Koncové body serveru na server | Koncové body serveru 33-99 | Ano, ale se liší v závislosti na konfiguraci (procesor, paměť, svazky, změny v souboru, počet souborů atd.) |
| Velikost koncového bodu | 4 TiB | Ne |
| Soubor systémových objektů (adresářů a souborů) na skupinu synchronizace | 25 milionů objektů | Ne |
| Maximální počet objektů systému souborů (adresářů a souborů) v adresáři | 200 000 objektů | Ano |
| Délka názvu maximální objektu (adresářů a souborů) | 255 znaků | Ano |
| Velikost popisovač zabezpečení objektu maximální pro (adresářů a souborů) | 4 KiB | Ano |
| Velikost souboru | 100 GiB | Ne |
| Minimální velikost souboru pro soubor, který chcete být rozvrstvena | 64 KiB | Ano |
| Synchronizace souběžných relací | V4 agent: omezení se liší v závislosti na dostupných systémových prostředcích. <BR> Agent verze 3: 2 activesyncu relací na procesor nebo maximálně 8 active synchronizovat relace na serveru | Ano
