---
title: Rozdíly mezi oprávněními pro správu a řešení API služeb – Azure Batch | Dokumenty společnosti Microsoft
description: Api fungují na různých vrstvách služby Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672765"
---
# <a name="service-level-and-management-level-apis"></a>Api úrovně služeb a úrovně správy

Azure Batch má dvě sady api, jednu pro úroveň služeb a jednu pro úroveň správy. Pojmenování je často podobné, ale vrátí různé výsledky. Pokud chcete protokoly aktivit, musíte použít správu API. Api úrovně služeb obejít vrstvu Azure Resource Management a nejsou protokolovány.


Správa dávek a dávková služba mají například api pro fond. 
- Toto rozhraní API pro odstranění fondu je zaměřena přímo na dávkový účet:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Toto rozhraní https://docs.microsoft.com/rest/api/batchmanagement/pool/delete API k odstranění fondu je zaměřena na vrstvu management.azure.com.

