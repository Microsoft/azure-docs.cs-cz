---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 47eae616ffc62e42448da23fb02152dae17aa548
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116653"
---
| Prostředek | Cíl | Omezení pevné velikosti |
|----------|--------------|------------|
| Služby synchronizace úložiště na oblast | 100 služby synchronizace úložiště | Ano |
| Skupiny synchronizace na službu synchronizace úložiště | skupiny synchronizace 200 | Ano |
| Registrované servery na službu synchronizace úložiště | servery 99 | Ano |
| Cloudové koncové body na skupinu synchronizace | 1 koncový bod cloudu | Ano |
| Koncové body serveru na skupinu synchronizace | koncové body serveru 50 | Ne |
| Koncové body serveru na server | 30 koncových bodů serveru | Ano |
| Objekty systému souborů (adresáře a soubory) na skupinu synchronizace | objekty 100 000 000 | Ne |
| Maximální počet objektů systému souborů (adresářů a souborů) v adresáři | objekty 5 000 000 | Ano |
| Maximální velikost popisovače zabezpečení objektů (adresářů a souborů) | 64 KiB | Ano |
| Velikost souboru | 100 GiB | Ne |
| Minimální velikost souboru, který se má převrstvený | V9 a novější: na základě velikosti clusteru systému souborů (velikost clusteru systému souborů Double). Pokud je například velikost clusteru systému souborů 4kb, minimální velikost souboru bude 8 KB.<br> V8 a starší verze: 64 KiB  | Ano |

> [!Note]  
> Koncový bod Azure File Sync může škálovat až na velikost sdílené složky Azure. Pokud je dosaženo limitu velikosti sdílené složky Azure, nebude synchronizace moct fungovat.
