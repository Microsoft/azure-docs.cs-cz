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
ms.openlocfilehash: 29518fb971649087d8a5afa39d69c7fc2c014f98
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331001"
---
| Prostředek | Cíl | Omezení pevné velikosti |
|----------|--------------|------------|
| Služby synchronizace úložiště na oblast | 100 služby synchronizace úložiště | Yes |
| Skupiny synchronizace na službu synchronizace úložiště | skupiny synchronizace 200 | Yes |
| Registrované servery na službu synchronizace úložiště | servery 99 | Yes |
| Cloudové koncové body na skupinu synchronizace | 1 koncový bod cloudu | Yes |
| Koncové body serveru na skupinu synchronizace | koncové body serveru 100 | Yes |
| Koncové body serveru na server | 30 koncových bodů serveru | Yes |
| Objekty systému souborů (adresáře a soubory) na skupinu synchronizace | objekty 100 000 000 | No |
| Maximální počet objektů systému souborů (adresářů a souborů) v adresáři | objekty 5 000 000 | Yes |
| Maximální velikost popisovače zabezpečení objektů (adresářů a souborů) | 64 KiB | Yes |
| Velikost souboru | 100 GiB | No |
| Minimální velikost souboru, který se má převrstvený | V9 a novější: na základě velikosti clusteru systému souborů (velikost clusteru systému souborů Double). Pokud je například velikost clusteru systému souborů 4kb, minimální velikost souboru bude 8 KB.<br> V8 a starší verze: 64 KiB  | Yes |

> [!Note]  
> Koncový bod Azure File Sync může škálovat až na velikost sdílené složky Azure. Pokud je dosaženo limitu velikosti sdílené složky Azure, nebude synchronizace moct fungovat.
