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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755606"
---
| Prostředek | Omezení |
| --- | --- |
| Jednotky škálování | 10 za oblasti<sup>1</sup> |
| Mezipaměť | 5 GB za každou jednotku<sup>1</sup> |
| Souběžné back-end připojení<sup>2</sup> za autorita protokolu HTTP | 2 048 na jednotku<sup>3</sup> |
| Velikost maximální odpověď uložená v mezipaměti | 10MB |
| Zásady maximální velikosti dokumentu | 256KB |
| Maximální brány vlastní domény | 20 za instanci služby<sup>4</sup> |


<sup>1</sup>omezení API Management se liší u jednotlivých cenových úrovní. Chcete-li zobrazit ceny vrstvy a jejich limity škálování přejděte na [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> připojení jsou ve fondu a znovu použít, pokud explicitně ukončeno back-endu.
<sup>3</sup> na jednotku úrovně Basic, Standard a Premium. Úroveň Developer je omezená na 1024.
<sup>4</sup> k dispozici pouze na úrovni Premium.


