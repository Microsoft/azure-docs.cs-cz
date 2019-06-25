---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175214"
---
| Resource | Target | Pevný limit |
|----------|--------------|------------|
| Služby synchronizace úložiště na oblast | 20 služby synchronizace úložiště | Ano |
| Skupiny synchronizace služby synchronizace úložiště | 100 skupiny synchronizace. | Ano |
| Registrované servery služby synchronizace úložiště | 99 servery | Ano |
| Koncové body cloudu na skupinu synchronizace | koncový bod cloudu. 1 | Ano |
| Koncové body serveru na skupinu synchronizace | 50 koncové body serveru | Ne |
| Koncové body serveru na server | 30 koncové body serveru | Ano |
| Soubor systémových objektů (adresářů a souborů) na skupinu synchronizace | 25 milionů objektů | Ne |
| Maximální počet objektů systému souborů (adresářů a souborů) v adresáři | 1 milion objekty | Ano |
| Velikost popisovač zabezpečení objektu maximální pro (adresářů a souborů) | 64 KiB | Ano |
| Velikost souboru | 100 GiB | Ne |
| Minimální velikost souboru pro soubor, který chcete být rozvrstvena | 64 KiB | Ano |
| Synchronizace souběžných relací | Agent verze 4 a novější: Limit se liší v závislosti na dostupných systémových prostředcích. <BR> Agent verze 3: Dvě activesyncu relací na procesor a maximálně osm relací active sync na serveru. | Ano

> [!Note]  
> Koncový bod Azure File Sync můžete vertikálně navýšit kapacitu na velikost sdílené složky Azure. Při dosažení omezení velikosti sdílené složky Azure file sync nebude možné provozovat.