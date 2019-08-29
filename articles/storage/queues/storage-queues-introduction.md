---
title: Seznámení s frontami Azure – Azure Storage
description: Seznámení s frontami Azure
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/07/2019
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 86bbff167a2653fd8d89b566b551c4c53dd3614e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124569"
---
# <a name="what-are-azure-queues"></a>Co jsou fronty Azure?

Azure Queue Storage je služba pro ukládání velkého počtu zpráv. Přístup ke zprávám odkudkoli na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Zpráva fronty může mít velikost až 64 KB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování.

## <a name="queue-service-concepts"></a>Služba front koncepty

Služba front obsahuje následující součásti:

![Koncepty front](./media/storage-queues-introduction/queue1.png)

* **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Účet úložiště:** Veškerý přístup k Azure Storage se provádí prostřednictvím účtu úložiště. Podrobné informace o kapacitě účtu úložiště najdete v článku [Škálovatelnost a cíle výkonnosti úložiště Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Provedených** Fronta obsahuje sadu zpráv. Název fronty **musí** být malými písmeny. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Zpráva** Zpráva v libovolném formátu až do 64 KB. Před verzí 2017-07-29 je maximální povolený čas na živé maximum sedm dní. V případě verze 2017-07-29 nebo novější může být maximální doba do živého kladného čísla nebo-1, což znamená, že platnost zprávy nevyprší. Pokud je tento parametr vynechán, výchozí hodnota TTL (Time to Live) je sedm dní.

## <a name="next-steps"></a>Další postup

* [Vytvoření účtu úložiště](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Začínáme s frontami pomocí .NET](storage-dotnet-how-to-use-queues.md)
