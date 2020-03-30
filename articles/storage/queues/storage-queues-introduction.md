---
title: Úvod do front Azure – Azure Storage
description: Úvod do front Azure
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 4a2bea77578282d68d86bc1a8cea765aa2cbd555
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80060851"
---
# <a name="what-are-azure-queues"></a>Co jsou fronty Azure?

Azure Queue Storage je služba pro ukládání velkých objemů zpráv. Přístup ke zprávám z libovolného místa na světě prostřednictvím ověřených volání pomocí protokolu HTTP nebo HTTPS. Zpráva fronty může mít velikost až 64 kB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytvoření nevyřízených položek práce pro zpracování asynchronně.

## <a name="queue-service-concepts"></a>Koncepty služby fronty

Služba front obsahuje následující součásti:

![Diagram znázorňující vztah mezi účtem úložiště, frontami a zprávami](./media/storage-queues-introduction/queue1.png)

* **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Účet úložiště:** Veškerý přístup k Azure Storage se provádí prostřednictvím účtu úložiště. Informace o kapacitě účtu úložiště najdete v tématu [Škálovatelnost a cíle výkonu pro účty standardního úložiště](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Fronta:** Fronta obsahuje sadu zpráv. Název fronty **musí** být všechna malá písmena. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Zpráva:** Zprávu v libovolném formátu o velikosti až 64 kB. Před verzí 2017-07-29 je maximální povolená doba do živého provozu sedm dní. Pro verzi 2017-07-29 nebo novější maximální doba aktivní může být libovolné kladné číslo nebo -1 označující, že platnost zprávy nevyprší. Pokud je tento parametr vynechán, výchozí doba aktivní je sedm dní.

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu úložiště](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Začínáme s frontami pomocí rozhraní .NET](storage-dotnet-how-to-use-queues.md)
