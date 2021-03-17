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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75392398"
---
Následující tabulka popisuje jednotlivé hlavní kvóty, omezení, výchozí hodnoty a omezení v Azure Scheduleru.

| Prostředek | Popis omezení |
| -------- | ----------------- |
| **Velikost úlohy** | Maximální velikost úlohy je 16 000. Pokud operace PUT nebo PATCH způsobí velikost úlohy větší, než je tento limit, vrátí se stavový kód 400 Špatný požadavek. | 
| **Kolekce úloh** | Maximální počet kolekcí úloh na jedno předplatné Azure je 200 000. | 
| **Počet úloh na kolekci** | Ve výchozím nastavení je maximální počet úloh pět úloh v bezplatné kolekci úloh a 50 úloh ve standardní kolekci úloh. Můžete změnit maximální počet úloh v kolekci úloh. Všechny úlohy v kolekci úloh jsou omezeny na hodnotu nastavenou v kolekci úloh. Pokud se pokusíte vytvořit více úloh než maximální kvóta úloh, požadavek se nezdaří s kódem stavu konfliktu 409. | 
| **Čas spuštění** | Maximum "čas spuštění" je 18 měsíců. |
| **Interval opakování** | Maximální délka opakování je 18 měsíců. | 
| **Frekvence** | Ve výchozím nastavení je maximální kvóta frekvence jedna hodina v bezplatné kolekci úloh a jedna minuta ve standardní kolekci úloh. <p>Maximální četnost pro kolekci úloh můžete nastavit na nižší, než je maximum. Všechny úlohy v kolekci úloh jsou omezeny na hodnotu nastavenou v kolekci úloh. Pokud se pokusíte vytvořit úlohu s vyšší frekvencí, než je maximální frekvence v kolekci úloh, požadavek se nezdaří s kódem stavu konfliktu 409. | 
| **Velikost textu** | Maximální velikost těla požadavku je 8 192 znaků. |
| **Velikost adresy URL požadavku** | Maximální velikost adresy URL požadavku je 2 048 znaků. |
| **Počet hlaviček** | Maximální počet hlaviček je 50 hlaviček. | 
| **Agregovaná velikost záhlaví** | Maximální velikost agregované hlavičky je 4 096 znaků. |
| **Prodlev** | Časový limit žádosti je statický, to znamená, že není možné konfigurovat. a pro akce HTTP je 60 sekund. V případě delšího provozu použijte asynchronní protokoly HTTP. Například vrátí 202 hned, ale pokračuje v práci na pozadí. | 
| **Historie úlohy** | Maximální tělo odpovědi uložené v historii úlohy je 2 048 bajtů. |
| **Uchování historie úloh** | Historie úloh je udržována po dobu až dvou měsíců nebo až do posledního 1 000 spuštění. | 
| **Dokončené a chybné uchování úlohy** | Dokončené a chybné úlohy se uchovávají po dobu 60 dnů. |
||| 

