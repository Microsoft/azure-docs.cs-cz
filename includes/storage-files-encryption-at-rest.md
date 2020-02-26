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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597783"
---
Všechna data uložená v souborech Azure jsou v klidovém stavu zašifrovaná pomocí šifrování služby Azure Storage (SSE). Šifrování služby Storage funguje podobně jako BitLocker ve Windows: data se šifrují pod úrovní systému souborů. Vzhledem k tomu, že se data šifrují pod systémem souborů sdílené složky Azure, protože je zašifrovaná na disk, nemusíte mít přístup k základnímu klíči v klientovi, aby bylo možné číst a zapisovat do sdílené složky Azure.

Ve výchozím nastavení se data uložená v souborech Azure šifrují pomocí klíčů spravovaných Microsoftem. V případě klíčů spravovaných Microsoftem uchovává společnost Microsoft klíče pro šifrování a dešifrování dat a zodpovídá za pravidelné střídání. Můžete se také rozhodnout, že budete spravovat vlastní klíče, což vám umožní řídit proces otáčení. Pokud se rozhodnete šifrovat sdílené složky pomocí klíčů spravovaných zákazníkem, soubory Azure budou mít autorizaci pro přístup k vašim klíčům, aby mohli plnit požadavky na čtení a zápis z vašich klientů. Pomocí klíčů spravovaných zákazníkem můžete tuto autorizaci kdykoli odvolat, ale to znamená, že sdílená složka Azure už nebude přístupná přes protokol SMB nebo rozhraní REST API.

Služba soubory Azure používá stejné schéma šifrování jako jiné služby úložiště Azure, jako je Azure Blob Storage. Další informace o šifrování služby Azure Storage (SSE) najdete v tématu [šifrování Azure Storage pro](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)neaktivní neaktivní data.