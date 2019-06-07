---
title: Úvod do fronty Azure – Azure Storage
description: Úvod do fronty Azure
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: overview
ms.date: 06/07/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: bc3045e3d3b6977555818fcdb3dcaf3246ebd200
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754809"
---
# <a name="what-are-azure-queues"></a>Co jsou fronty Azure?

Azure Queue storage je služba pro ukládání velkého počtu zpráv. Přístup k zprávy z libovolného místa na světě prostřednictvím ověřených volání přes protokol HTTP nebo HTTPS. Fronty zpráv může být velikost až 64 KB. Fronta může obsahovat milióny zpráv až do vyčerpání celkové kapacity účtu úložiště.

## <a name="common-uses"></a>Běžné použití

Toto jsou některá běžná použití úložiště služby Queue Storage:

* Vytvoření backlogu práce k asynchronnímu zpracování
* Předávání zpráv z webové role Azure do role pracovního procesu Azure

## <a name="queue-service-concepts"></a>Koncepty služby front

Služba front obsahuje následující součásti:

![Koncepty front](./media/storage-queues-introduction/queue1.png)

* **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Účet úložiště:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Podrobné informace o kapacitě účtu úložiště najdete v článku [Škálovatelnost a cíle výkonnosti úložiště Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Fronta:** Fronta obsahuje sadu zpráv. Název fronty **musí** obsahovat jenom malá písmena. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **zpráva:** Zprávu v libovolném formátu o velikosti až 64 KB. Dříve než ve verzi 2017-07-29 time-to-live maximální povolený počet je sedm dní. Verze 2017-07-29 nebo novější, maximální time-to-live je libovolné kladné číslo, nebo -1, která udává, že zpráva platnost pasu nevyprší. Pokud je tento parametr vynechán, výchozí čas TTL je sedm dní.

## <a name="next-steps"></a>Další postup

* [Vytvoření účtu úložiště](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Začínáme s frontami pomocí .NET](storage-dotnet-how-to-use-queues.md)
