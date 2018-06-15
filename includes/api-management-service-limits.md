---
title: zahrnout soubor
description: zahrnout soubor
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: b40e2ba448236bcef72ffe501b4b14fa0dd64e1c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31434935"
---
| Prostředek | Omezení |
| --- | --- |
| Jednotky škálování | 10 každou oblast<sup>1</sup> |
| Mezipaměť | 5 GB na jednotku<sup>1</sup> |
| Back-end souběžných připojení<sup>2</sup> za autority HTTP | 2048 na jednotku<sup>3</sup> |
| Velikost maximální odpovědi v mezipaměti | 10MB. |
| Zásady maximální velikost dokumentu | 256KB |
| Maximální brány vlastní domény | 20 na instanci služby<sup>4</sup> |


<sup>1</sup>omezení API Management se liší u jednotlivých cenových úrovní. Pokud chcete zobrazit cenové úrovně a jejich omezení škálování přejděte do [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> připojení jsou ve fondu a znovu použít, pokud explicitně uzavřené back-end.
<sup>3</sup> na jednotku úrovně Basic, Standard a Premium. Úroveň Developer je omezena na 1024.
<sup>4</sup> dostupných v jenom na úrovni Premium.


