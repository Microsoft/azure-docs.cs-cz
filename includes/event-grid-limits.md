---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b884bc72381c98af77f2f49336f3dd5762c68734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859302"
---
Následující omezení se vztahují na Azure Event Grid **témata** (systémová, vlastní a partnerská témata). 

| Prostředek | Omezení |
| --- | --- |
| Vlastní témata pro každé předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Míra publikování vlastního nebo partnerského tématu (příchozí) | 5 000 událostí za sekundu nebo 1 MB/s (podle toho, co je splněno jako první) |
| Velikost události | 1 MB  |
| Připojení privátního koncového bodu na téma  | 64 | 
| Pravidla brány firewall protokolu IP pro každé téma | 16 | 

Následující omezení platí pro Azure Event Grid **domény**. 

| Prostředek | Omezení |
| --- | --- |
| Témata na doménu události | 100 000 |
| Odběry událostí na téma v rámci domény | 500 |
| Odběry událostí oboru domény | 50 |
| Frekvence publikování pro doménu události (příchozí) | 5 000 událostí za sekundu nebo 1 MB/s (podle toho, co je splněno jako první) |
| Domény událostí na předplatné Azure | 100 |
| Připojení privátního koncového bodu na doménu | 64 | 
| Pravidla brány firewall protokolu IP pro každou doménu | 16 | 


