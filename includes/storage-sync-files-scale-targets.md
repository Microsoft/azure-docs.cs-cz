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
ms.openlocfilehash: c04875f92ee58c24567fbb4d73d8d01697810f99
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391592"
---
| Prostředek | Výběr cílového umístění | Omezení pevné velikosti |
|----------|--------------|------------|
| Služby synchronizace úložiště na oblast | 20 služeb synchronizace úložiště | Ano |
| Skupiny synchronizace na službu synchronizace úložiště | skupiny synchronizace 100 | Ano |
| Registrované servery na službu synchronizace úložiště | servery 99 | Ano |
| Cloudové koncové body na skupinu synchronizace | 1 koncový bod cloudu | Ano |
| Koncové body serveru na skupinu synchronizace | koncové body serveru 50 | Ne |
| Koncové body serveru na server | 30 koncových bodů serveru | Ano |
| Objekty systému souborů (adresáře a soubory) na skupinu synchronizace | objekty 50 000 000 | Ne |
| Maximální počet objektů systému souborů (adresářů a souborů) v adresáři | objekty 5 000 000 | Ano |
| Maximální velikost popisovače zabezpečení objektů (adresářů a souborů) | 64 KiB | Ano |
| Velikost souboru | 100 GiB | Ne |
| Minimální velikost souboru, který se má převrstvený | 64 KiB | Ano |
| Souběžné relace synchronizace | Agent v4 a novější: omezení se liší v závislosti na dostupných systémových prostředcích. <BR> V3 agent: dvě aktivní relace synchronizace na jeden procesor nebo maximálně osm aktivních relací synchronizace na jeden server. | Ano

> [!Note]  
> Koncový bod Azure File Sync může škálovat až na velikost sdílené složky Azure. Pokud je dosaženo limitu velikosti sdílené složky Azure, nebude synchronizace moct fungovat.
