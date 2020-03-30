---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74796175"
---
| Prostředek | Cíl | Pevný limit |
|----------|--------------|------------|
| Služby synchronizace úložiště pro oblast | 20 Služby synchronizace úložiště | Ano |
| Skupiny synchronizace podle služby synchronizace úložiště | 100 skupin synchronizace | Ano |
| Registrované servery na službu synchronizace úložiště | 99 serverů | Ano |
| Cloudové koncové body na skupinu synchronizace | 1 koncový bod cloudu | Ano |
| Koncové body serveru na skupinu synchronizace | 50 koncových bodů serveru | Ne |
| Koncové body serveru na server | 30 koncových bodů serveru | Ano |
| Objekty systému souborů (adresáře a soubory) na skupinu synchronizace | 100 milionů objektů | Ne |
| Maximální počet objektů systému souborů (adresářů a souborů) v adresáři | 5 milionů objektů | Ano |
| Maximální velikost popisovače zabezpečení objektu (adresářů a souborů) | 64 KiB | Ano |
| Velikost souboru | 100 GiB | Ne |
| Minimální velikost souboru, který má být vrstvený | V9: Na základě velikosti clusteru systému souborů (dvojitá velikost clusteru systému souborů). Pokud je například velikost clusteru systému souborů 4 kb, minimální velikost souboru bude 8 kb.<br> V8 a starší: 64 KiB  | Ano |

> [!Note]  
> Koncový bod Azure File Sync můžete škálovat až na velikost sdílené složky Azure. Pokud je dosaženo limitu velikosti sdílené složky Azure, synchronizace nebude moct fungovat.
