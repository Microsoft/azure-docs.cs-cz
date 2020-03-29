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
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392398"
---
Následující tabulka popisuje všechny hlavní kvóty, omezení, výchozí hodnoty a omezení v Plánovači Azure.

| Prostředek | Popis omezení |
| -------- | ----------------- |
| **Velikost úlohy** | Maximální velikost úlohy je 16 000. Pokud put nebo patch operace má za následek velikost úlohy větší než tento limit, je vrácen stavový kód 400 bad request. | 
| **Kolekce pracovních míst** | Maximální počet kolekcí úloh na jedno předplatné Azure je 200 000. | 
| **Úlohy na kolekci** | Ve výchozím nastavení je maximální počet úloh pět úloh v kolekci volných úloh a 50 úloh ve standardní kolekci úloh. Můžete změnit maximální počet úloh v kolekci úloh. Všechny úlohy v kolekci úloh jsou omezeny na hodnotu nastavenou v kolekci úloh. Pokud se pokusíte vytvořit více úloh, než je maximální kvóta úloh, požadavek se nezdaří s kódem stavu konfliktu 409. | 
| **Čas zahájení času** | Maximální "čas do zahájení času" je 18 měsíců. |
| **Rozpětí opakování** | Maximální doba opakování je 18 měsíců. | 
| **Frequency** | Ve výchozím nastavení je maximální kvóta frekvence jedna hodina v kolekci volných úloh a jedna minuta ve standardní kolekci úloh. <p>Maximální četnost v kolekci úloh může být nižší než maximální. Všechny úlohy v kolekci úloh jsou omezeny na hodnotu nastavenou v kolekci úloh. Pokud se pokusíte vytvořit úlohu s vyšší frekvencí, než je maximální frekvence v kolekci úloh, požadavek se nezdaří se stavovým kódem 409 Conflict. | 
| **Velikost těla** | Maximální velikost těla pro požadavek je 8 192 znaků. |
| **Velikost adresy URL požadavku** | Maximální velikost adresy URL požadavku je 2 048 znaků. |
| **Počet záhlaví** | Maximální počet záhlaví je 50 záhlaví. | 
| **Agregovaná velikost záhlaví** | Maximální agregovaná velikost záhlaví je 4 096 znaků. |
| **Časový limit** | Časový čas požadavku je statický, to znamená, že není konfigurovatelný. a je 60 sekund pro akce HTTP. Pro delší spuštěné operace postupujte podle asynchronních protokolů HTTP. Například okamžitě vraťte 202, ale pokračujte v práci na pozadí. | 
| **Historie úlohy** | Maximální tělo odezvy uložené v historii úloh je 2 048 bajtů. |
| **Uchovávání historie úloh** | Historie práce je uchovávána po dobu až dvou měsíců nebo až do posledních 1000 exekucí. | 
| **Dokončeno a došlo k zadržení úlohy** | Dokončené a chybné úlohy jsou uchovávány po dobu 60 dnů. |
||| 

