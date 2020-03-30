---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597783"
---
Všechna data uložená v Azure Files se šifrují v klidovém stavu pomocí šifrování služby Azure storage Service (SSE). Šifrování služby úložiště funguje podobně jako nástroj BitLocker v systému Windows: data jsou šifrována pod úrovní systému souborů. Vzhledem k tomu, že data jsou šifrovaná pod souborovým systémem sdílené složky Azure, protože jsou zakódována na disk, nemusíte mít přístup k podkladovému klíči klienta ke čtení nebo zápisu do sdílené složky Azure.

Ve výchozím nastavení se data uložená v souborech Azure šifrují pomocí klíčů spravovaných microsoftem. S microsoft-spravované klíče, Microsoft drží klíče k šifrování/ dešifrování dat a je zodpovědný za jejich otáčení v pravidelných intervalech. Můžete také zvolit správu vlastních klíčů, což vám dává kontrolu nad procesem otáčení. Pokud se rozhodnete zašifrovat sdílené složky pomocí klíčů spravovaných zákazníky, soubory Azure mají oprávnění k přístupu k vašim klíčům za účelem splnění požadavků na čtení a zápis od vašich klientů. Pomocí klíčů spravovaných zákazníkem můžete tuto autorizaci kdykoli odvolat, ale to znamená, že vaše sdílená složka Azure už nebude přístupná prostřednictvím rozhraní SMB nebo rozhraní API FileREST.

Azure Files používá stejné schéma šifrování jako ostatní služby úložiště Azure, jako je úložiště objektů blob Azure. Další informace o šifrování služby úložiště Azure (SSE), najdete v [tématu šifrování úložiště Azure pro data v klidovém stavu](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).