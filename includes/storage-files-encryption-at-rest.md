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
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011694"
---
Všechna data uložená v souborech Azure jsou v klidovém stavu zašifrovaná pomocí šifrování služby Azure Storage (SSE). Šifrování služby Storage funguje podobně jako BitLocker ve Windows: data se šifrují pod úrovní systému souborů. Vzhledem k tomu, že se data šifrují pod systémem souborů sdílené složky Azure, protože je zašifrovaná na disk, nemusíte mít přístup k základnímu klíči v klientovi, aby bylo možné číst a zapisovat do sdílené složky Azure. Šifrování v klidovém umístění platí pro protokoly SMB i NFS.

Ve výchozím nastavení se data uložená v souborech Azure šifrují pomocí klíčů spravovaných Microsoftem. V případě klíčů spravovaných Microsoftem uchovává společnost Microsoft klíče pro šifrování a dešifrování dat a zodpovídá za pravidelné střídání. Můžete se také rozhodnout, že budete spravovat vlastní klíče, což vám umožní řídit proces otáčení. Pokud se rozhodnete šifrovat sdílené složky pomocí klíčů spravovaných zákazníkem, soubory Azure budou mít autorizaci pro přístup k vašim klíčům, aby mohli plnit požadavky na čtení a zápis z vašich klientů. Pomocí klíčů spravovaných zákazníkem můžete tuto autorizaci kdykoli odvolat, ale to znamená, že sdílená složka Azure už nebude přístupná přes protokol SMB nebo rozhraní REST API.

Služba soubory Azure používá stejné schéma šifrování jako jiné služby úložiště Azure, jako je Azure Blob Storage. Další informace o šifrování služby Azure Storage (SSE) najdete v tématu [šifrování Azure Storage pro](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)neaktivní neaktivní data.