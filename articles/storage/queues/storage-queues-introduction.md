---
title: Seznámení se službou Azure Queue Storage-Azure Storage
description: Podívejte se na Úvod do Azure Queue Storage, službu pro ukládání velkého počtu zpráv. Služba Queue Storage obsahuje formát adresy URL, účet úložiště, frontu a zprávu.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 32ce5af5371047ae814602e9118f622ee036bd9c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276208"
---
# <a name="what-is-azure-queue-storage"></a>Co je Azure Queue Storage?

Azure Queue Storage je služba pro ukládání velkých objemů zpráv. Přístup ke zprávám odkudkoli na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Zpráva fronty může mít velikost až 64 KB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování.

## <a name="queue-storage-concepts"></a>Queue Storage koncepty

Queue Storage obsahuje následující součásti:

![Diagram znázorňující vztah mezi účtem úložiště, frontami a zprávami.](./media/storage-queues-introduction/queue1.png)

- **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Následující adresa URL odkazuje na frontu v diagramu:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Účet úložiště:** Veškerý přístup k Azure Storage se provádí prostřednictvím účtu úložiště. Informace o kapacitě účtu úložiště najdete v tématu [škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Fronta:** Fronta obsahuje sadu zpráv. Název fronty **musí** být malými písmeny. Informace o pojmenovávání front najdete v tématu [pojmenování front a metadat](/rest/api/storageservices/naming-queues-and-metadata).

- **Zpráva:** Zprávu v libovolném formátu o velikosti až 64 kB. Před verzí 2017-07-29 je maximální povolený čas na živé maximum sedm dní. V případě verze 2017-07-29 nebo novější může být maximální doba do živého kladného čísla nebo-1, což znamená, že platnost zprávy nevyprší. Pokud je tento parametr vynechán, výchozí hodnota TTL (Time to Live) je sedm dní.

## <a name="next-steps"></a>Další kroky

- [Vytvoření účtu úložiště](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Začínáme s Queue Storage pomocí .NET](storage-dotnet-how-to-use-queues.md)
- [Začínáme s Queue Storage pomocí jazyka Java](storage-java-how-to-use-queue-storage.md)
- [Začínáme s Queue Storage pomocí Pythonu](storage-python-how-to-use-queue-storage.md)
- [Začínáme s Queue Storage pomocí Node.js](storage-nodejs-how-to-use-queues.md)
