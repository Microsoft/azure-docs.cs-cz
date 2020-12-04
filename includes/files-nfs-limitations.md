---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: db4bc51b937535b7b53273bf01057d9b6c5198b3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584601"
---
Ve verzi Preview má systém souborů NFS tato omezení:

- Systém souborů NFS 4,1 aktuálně podporuje pouze povinné funkce ze [specifikace protokolu](https://tools.ietf.org/html/rfc5661). Volitelné funkce, jako jsou delegování a zpětné volání všech druhů, zamknutí zámků a downgradů a ověřování pomocí protokolu Kerberos a šifrování se nepodporuje.
- Pokud je většina vašich požadavků orientovaných na metadata, pak bude latence ve srovnání s operacemi čtení, zápisu a aktualizace horší.
- Aby bylo možné vytvořit sdílenou složku NFS, je nutné vytvořit nový účet úložiště.
- Podporují se jenom rozhraní REST API roviny správy. Rozhraní REST API pro rovinu dat nejsou k dispozici, což znamená, že nástroje, jako Průzkumník služby Storage, nebudou pracovat se sdílenými složkami NFS, ani nebude možné procházet sdílená data NFS v Azure Portal.
- AzCopy se v tuto chvíli nepodporuje.
- Dostupné jenom pro úroveň Premium.
- Aktuálně dostupné jenom pro místně redundantní úložiště (LRS).

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funkce ještě nejsou podporované.

Pro sdílené složky NFS nejsou k dispozici také následující funkce služby soubory Azure:

- Ověřování na základě identity
- Podpora Azure Backup
- Snímky
- Obnovitelné odstranění
- Úplná podpora šifrování při přenosu (podrobnosti najdete v tématu [zabezpečení NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (k dispozici pouze pro klienty se systémem Windows, který systém souborů NFS 4,1 nepodporuje)
