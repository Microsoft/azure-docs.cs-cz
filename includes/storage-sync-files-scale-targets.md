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
ms.openlocfilehash: e7aa2b4389fe60eed80b15aff04d6f7fcbc7b013
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "68968842"
---
| Resource | Target | Omezení pevné velikosti |
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
| Souběžné relace synchronizace | Agent verze v4 a novější: Omezení se liší v závislosti na dostupných systémových prostředcích. <BR> V3 agent: Dvě aktivní relace synchronizace na jeden procesor nebo maximálně osm aktivních relací synchronizace na jeden server. | Ano

> [!Note]  
> Koncový bod Azure File Sync může škálovat až na velikost sdílené složky Azure. Pokud je dosaženo limitu velikosti sdílené složky Azure, nebude synchronizace moct fungovat.
