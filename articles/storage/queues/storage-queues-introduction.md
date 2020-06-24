---
title: Seznámení s frontami Azure – Azure Storage
description: Seznámení s frontami Azure
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: 678ab8a3f3dace17671c242939c20cb5499b2e39
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84805130"
---
# <a name="what-are-azure-queues"></a>Co jsou fronty Azure?

Azure Queue Storage je služba pro ukládání velkých objemů zpráv. Přístup ke zprávám odkudkoli na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Zpráva fronty může mít velikost až 64 KB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování.

## <a name="queue-service-concepts"></a>Služba front koncepty

Služba front obsahuje následující součásti:

![Diagram znázorňující vztah mezi účtem úložiště, frontami a zprávami](./media/storage-queues-introduction/queue1.png)

* **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Účet úložiště:** Veškerý přístup k Azure Storage se provádí prostřednictvím účtu úložiště. Informace o kapacitě účtu úložiště najdete v tématu [škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Fronta:** Fronta obsahuje sadu zpráv. Název fronty **musí** být malými písmeny. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Zpráva:** Zprávu v libovolném formátu o velikosti až 64 kB. Před verzí 2017-07-29 je maximální povolený čas na živé maximum sedm dní. V případě verze 2017-07-29 nebo novější může být maximální doba do živého kladného čísla nebo-1, což znamená, že platnost zprávy nevyprší. Pokud je tento parametr vynechán, výchozí hodnota TTL (Time to Live) je sedm dní.

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu úložiště](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Začínáme s frontami pomocí .NET](storage-dotnet-how-to-use-queues.md)
