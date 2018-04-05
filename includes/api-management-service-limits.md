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
ms.openlocfilehash: bee289da3f18edd0cb425f3d9acde084567a3b13
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
| Prostředek | Omezení |
| --- | --- |
| Jednotky škálování | 10 každou oblast<sup>1</sup> |
| Mezipaměť | 5 GB na jednotku<sup>1</sup> |
| Back-end souběžných připojení<sup>2</sup> za autority HTTP | 2048 na jednotku<sup>3</sup> |
| Velikost maximální odpovědi v mezipaměti | 10MB. |
| Maximální brány vlastní domény | 20 na instanci služby<sup>4</sup> |


<sup>1</sup>omezení API Management se liší u jednotlivých cenových úrovní. Pokud chcete zobrazit cenové úrovně a jejich omezení škálování přejděte do [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> připojení jsou ve fondu a znovu použít, pokud explicitně uzavřené back-end.
<sup>3</sup> na jednotku úrovně Basic, Standard a Premium. Úroveň Developer je omezena na 1024.
<sup>4</sup> dostupných v jenom na úrovni Premium.


