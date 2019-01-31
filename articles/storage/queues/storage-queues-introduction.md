---
title: Seznámení se službou Azure Queue storage | Dokumentace Microsoftu
description: Seznámení se službou Azure Queue storage
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: b173934db17b8c3ac5a48e599b75478fb214c240
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458393"
---
# <a name="introduction-to-queues"></a>Seznámení s frontami

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště.

## <a name="common-uses"></a>Běžné použití

Toto jsou některá běžná použití úložiště služby Queue Storage:

* Vytvoření backlogu práce k asynchronnímu zpracování
* Předávání zpráv z webové role Azure do role pracovního procesu Azure

## <a name="queue-service-concepts"></a>Koncepty služby front

Služba front obsahuje následující součásti:

![Koncepty front](./media/storage-queues-introduction/queue1.png)

* **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL:   
    https://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Účet úložiště:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Podrobné informace o kapacitě účtu úložiště najdete v článku [Škálovatelnost a cíle výkonnosti úložiště Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Fronta:** Fronta obsahuje sadu zpráv. Všechny zprávy musí být ve frontě. Upozorňujeme, že název fronty musí být psaný malými písmeny. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **zpráva:** Zprávu v libovolném formátu o velikosti až 64 KB. Maximální doba, kterou může zpráva zůstat ve frontě je sedm dní.

## <a name="next-steps"></a>Další postup

* [Vytvoření účtu úložiště](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Začínáme s frontami pomocí .NET](storage-dotnet-how-to-use-queues.md)
