---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100645538"
---
Následující omezení se vztahují na Azure Event Grid **témata** (systémová, vlastní a partnerská témata). 

| Prostředek | Omezení |
| --- | --- |
| Vlastní témata pro každé předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Míra publikování vlastního nebo partnerského tématu (příchozí) | 5 000 událostí za sekundu nebo 5 MB/s (podle toho, co je splněno jako první) |
| Velikost události | 1 MB  |
| Připojení privátního koncového bodu na téma  | 64 | 
| Pravidla brány firewall protokolu IP pro každé téma | 16 | 

Následující omezení platí pro Azure Event Grid **domény**. 

| Prostředek | Omezení |
| --- | --- |
| Témata na doménu události | 100 000 |
| Odběry událostí na téma v rámci domény | 500 |
| Odběry událostí oboru domény | 50 |
| Frekvence publikování pro doménu události (příchozí) | 5 000 událostí za sekundu nebo 5 MB/s (podle toho, co je splněno jako první) |
| Domény událostí na předplatné Azure | 100 |
| Připojení privátního koncového bodu na doménu | 64 | 
| Pravidla brány firewall protokolu IP pro každou doménu | 16 | 


