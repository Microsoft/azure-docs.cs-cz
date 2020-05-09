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
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "74796175"
---
| Prostředek | Cíl | Omezení pevné velikosti |
|----------|--------------|------------|
| Služby synchronizace úložiště na oblast | 20 služeb synchronizace úložiště | Ano |
| Skupiny synchronizace na službu synchronizace úložiště | skupiny synchronizace 100 | Ano |
| Registrované servery na službu synchronizace úložiště | servery 99 | Ano |
| Cloudové koncové body na skupinu synchronizace | 1 koncový bod cloudu | Ano |
| Koncové body serveru na skupinu synchronizace | koncové body serveru 50 | No |
| Koncové body serveru na server | 30 koncových bodů serveru | Ano |
| Objekty systému souborů (adresáře a soubory) na skupinu synchronizace | objekty 100 000 000 | No |
| Maximální počet objektů systému souborů (adresářů a souborů) v adresáři | objekty 5 000 000 | Ano |
| Maximální velikost popisovače zabezpečení objektů (adresářů a souborů) | 64 KiB | Ano |
| Velikost souboru | 100 GiB | No |
| Minimální velikost souboru, který se má převrstvený | V9: na základě velikosti clusteru systému souborů (velikost clusteru systému souborů Double). Pokud je například velikost clusteru systému souborů 4kb, minimální velikost souboru bude 8 KB.<br> V8 a starší verze: 64 KiB  | Ano |

> [!Note]  
> Koncový bod Azure File Sync může škálovat až na velikost sdílené složky Azure. Pokud je dosaženo limitu velikosti sdílené složky Azure, nebude synchronizace moct fungovat.
