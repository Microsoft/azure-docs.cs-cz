---
title: Rozdíly mezi rozhraními API pro správu a rozhraními API služeb
description: Rozhraní API fungují na různých vrstvách Azure Batch služby.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115393"
---
# <a name="service-level-and-management-level-apis"></a>Rozhraní API na úrovni služby a úrovni správy

Azure Batch má dvě sady rozhraní API, jednu pro úroveň služby a jednu pro úroveň správy. Pojmenování je často podobné, ale vrací různé výsledky. Pokud chcete protokoly aktivit, musíte použít rozhraní API pro správu. Rozhraní API na úrovni služby přecházejí z vrstvy správy prostředků Azure a nezaprotokolují se.


Služba Batch Management a služba Batch mají rozhraní API pro fond, například. 
- Toto rozhraní API k odstranění fondu je cílené přímo na účet Batch:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Toto rozhraní API pro odstranění https://docs.microsoft.com/rest/api/batchmanagement/pool/delete fondu je zaměřené na Management.Azure.com vrstvu.

