---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6e819e1078ac90ef16070702e7961122b06c1d6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107881152"
---
Ve verzi Preview má systém souborů NFS tato omezení:

- Systém souborů NFS 4,1 aktuálně podporuje pouze většinu funkcí ze [specifikace protokolu](https://tools.ietf.org/html/rfc5661). Některé funkce, jako jsou delegování a zpětné volání všech druhů, zamknutí zámků a downgradů, ověřování protokolem Kerberos a šifrování se nepodporují.
- Pokud je většina vašich požadavků orientovaných na metadata, pak bude latence ve srovnání s operacemi čtení, zápisu a aktualizace horší.
- Sdílené složky NFS se můžou povolit/vytvořit jenom pro nové účty úložiště/s, ne pro existující.
- Podporují se jenom rozhraní REST API roviny správy. Rozhraní REST API pro rovinu dat nejsou k dispozici, což znamená, že nástroje, jako Průzkumník služby Storage, nebudou pracovat se sdílenými složkami NFS, ani nebude možné procházet sdílená data NFS v Azure Portal.
- AzCopy se v tuto chvíli nepodporuje.
- Dostupné jenom pro úroveň Premium.
- Sdílené složky NFS akceptují jenom číselné UID/GID. Chcete-li klientům zabránit, aby odesílali alfanumerické UID/GID, měli byste zakázat mapování ID.
- Sdílené složky se můžou při použití privátních odkazů připojit jenom z jednoho účtu úložiště na samostatném virtuálním počítači. Pokus o připojení sdílených složek z jiných účtů úložiště se nezdaří.
- Doporučuje se spoléhat na oprávnění přiřazená k primární skupině. V některých případech může mít oprávnění přidělená neprimární skupině uživatele kvůli známé chybě přístup odepřít.

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funkce ještě nejsou podporované.

Pro sdílené složky NFS nejsou k dispozici také následující funkce služby soubory Azure:

- Ověřování na základě identity
- Podpora Azure Backup
- Snímky
- Obnovitelné odstranění
- Úplná podpora šifrování při přenosu (podrobnosti najdete v tématu [zabezpečení NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Synchronizace souborů Azure (k dispozici pouze pro klienty se systémem Windows, který systém souborů NFS 4,1 nepodporuje)
