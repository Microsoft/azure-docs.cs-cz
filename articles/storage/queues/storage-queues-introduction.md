---
title: Úvod do Azure Queue storage | Microsoft Docs
description: Úvod do Azure Queue storage
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.openlocfilehash: 792e8c7efb2a627fbc1abde1389015949d5931e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873825"
---
# <a name="introduction-to-queues"></a>Seznámení s frontami

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště.

## <a name="common-uses"></a>Běžná použití

Toto jsou některá běžná použití úložiště služby Queue Storage:

* Vytvoření backlogu práce k asynchronnímu zpracování
* Předávání zpráv z webové role Azure do role pracovního procesu Azure

## <a name="queue-service-concepts"></a>Koncepty služby front

Služba front obsahuje následující součásti:

![Koncepty fronty](./media/storage-queues-introduction/queue1.png)

* **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Účet úložiště:** veškerý přístup do služby Azure Storage se provádí prostřednictvím účtu úložiště. Podrobné informace o kapacitě účtu úložiště najdete v článku [Škálovatelnost a cíle výkonnosti služby Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Fronta:** Fronta obsahuje sadu zpráv. Všechny zprávy musí být ve frontě. Upozorňujeme, že název fronty musí být psaný malými písmeny. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Zpráva:** Zprávu v libovolném formátu o velikosti až 64 kB. Maximální doba, kterou může zpráva zůstat ve frontě je sedm dní.

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu úložiště](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Začínáme s fronty pomocí rozhraní .NET](storage-dotnet-how-to-use-queues.md)
