---
title: zahrnout soubor
description: zahrnout soubor
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: be52bf289898dfcd3ee669f12329669f6fabd356
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006476"
---
Následující tabulka popisuje každý z hlavní kvót, omezení, výchozí hodnoty a omezení ve službě Azure Scheduler.

| Prostředek | Popis omezení |
| -------- | ----------------- |
| **Velikost úlohy** | Maximální velikost je 16 kB. Pokud PUT nebo PATCH operace výsledkem úlohy velikost větší než tento limit, vrátí se stavový kód 400 Chybný požadavek. | 
| **Kolekce úloh** | Maximální počet kolekcí úloh na předplatné Azure je 200 000. | 
| **Počet úloh v kolekci** | Ve výchozím nastavení je maximální počet úloh pět úloh v kolekci úloh free a 50 úloh v kolekci standard. <p>Můžete změnit maximální počet úloh na kolekci úloh. Všechny úlohy v kolekci úloh jsou omezené na hodnotu nastavenou na kolekci úloh. Pokud se pokusíte vytvořit víc úloh než kvóta maximální úlohy, požadavek selže s kódem stavu 409 konflikt. | 
| **Čas spuštění** | Maximální počet "čas spuštění" je 18 měsíců. |
| **Rozsah opakování** | Období maximální počet opakování je 18 měsíců. | 
| **Frekvence** | Výchozí kvóta maximální frekvence je v kolekci úloh free jednu hodinu a minutu v kolekci standardní úloh. <p>Můžete nastavit maximální frekvence na kolekci úloh nižší než maximální délka. Všechny úlohy v kolekci úloh jsou omezené na hodnotu nastavenou na kolekci úloh. Při pokusu o vytvoření úlohy s vyšší frekvencí, než je maximální četnost na kolekci úloh, požadavek selže s kódem stavu 409 konflikt. | 
| **Velikost textu** | Velikost maximální tělo pro žádost je 8192 znaků. |
| **Velikost adresy URL požadavku** | Maximální velikost pro adresu URL žádosti je 2 048 znaků. |
| **Počet hlaviček** | Záhlaví maximální počet je 50 záhlaví. | 
| **Velikost agregační záhlaví** | Velikost záhlaví maximální agregace je 4096 znaků. |
| **časový limit** | Časový limit žádosti je statická (nejde konfigurovat) a je 60 sekund pro akce HTTP. Delší probíhajících operací postupujte podle asynchronní protokoly HTTP. Například vrátit 202 okamžitě, ale pokračovat v práci na pozadí. | 
| **Historie úlohy** | Text maximální odpovědi, které jsou uloženy v historii úloh je 2 048 bajtů. |
| **Uchování historie úlohy** | Historie úlohy, zůstane po dobu až 2 měsíců nebo až po poslední 1 000 spuštění. | 
| **Dokončené a chybnou úloha uchovávání** | Dokončené a chybnou úlohy uchovávají po dobu 60 dnů. |
||| 

