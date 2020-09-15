---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7e009516721473554517d1677bdef7a7451e4007
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564821"
---
Ve verzi Preview má systém souborů NFS tato omezení:

- Systém souborů NFS 4,1 aktuálně podporuje pouze povinné funkce ze [specifikace protokolu](https://tools.ietf.org/html/rfc5661). Volitelné funkce, jako jsou delegování a zpětné volání všech druhů, zamknutí zámků a downgradů a ověřování pomocí protokolu Kerberos a šifrování se nepodporuje.
- Pokud je většina vašich požadavků zaměřená na metadata, bude latence ve srovnání s operacemi otevřít/zavřít horší.
- Aby bylo možné vytvořit sdílenou složku NFS, je nutné vytvořit nový účet úložiště.
- Podporují se jenom rozhraní REST API roviny správy. Rozhraní REST API pro rovinu dat nejsou k dispozici, což znamená, že nástroje, jako je Průzkumník služby Storage, nebudou pracovat se sdílenými složkami systému souborů NFS, ani nebude možné procházet sdílená data NFS v Azure Portal.
- Dostupné jenom pro úroveň Premium.
- Aktuálně dostupné jenom pro místně redundantní úložiště.

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funkce ještě nejsou podporované.

Pro sdílené složky NFS nejsou k dispozici také následující funkce služby soubory Azure:

- Ověřování na základě identity
- Podpora Azure Backup
- Snímky
- Obnovitelné odstranění
- Úplná podpora šifrování při přenosu (podrobnosti najdete v tématu [zabezpečení NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (k dispozici pouze pro klienty se systémem Windows, který systém souborů NFS 4,1 nepodporuje)