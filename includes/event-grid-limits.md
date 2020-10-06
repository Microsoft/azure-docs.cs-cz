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
ms.openlocfilehash: e084256d9c2043d4382ca180ef3178175b301367
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745709"
---
Následující omezení se vztahují na Azure Event Grid **témata** (systémová, vlastní a partnerská témata). 

| Prostředek | Omezení |
| --- | --- |
| Vlastní témata pro každé předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Míra publikování vlastního nebo partnerského tématu (příchozí) | 5 000 událostí za sekundu nebo 1 MB/s (podle toho, co je splněno jako první)<br/>Neplatí pro systémová témata. |
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


