---
title: Přehled vyhrazených event hubs – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje přehled dedciated Azure Event Hubs, která nabízí nasazení jednoho tenanta služby event hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ed1fedab45ce59af2c67ed8a2ef60714d26d5801
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091022"
---
# <a name="overview-of-event-hubs-dedicated"></a>Přehled služby Event Hubs Dedicated

*Event hubs úrovně Dedicated* nabízí nasazení jednoho tenanta pro zákazníky s nejnáročnější požadavky na kapacitu. V plném rozsahu, Azure Event Hubs můžete příchozího přenosu dat více než 2 miliony událostí za sekundu nebo až 2 GB za sekundu telemetrických dat s latencí plně trvalého úložiště a sekunda. Také to umožňuje integrovaná řešení ve zpracování v reálném čase i dávkové zpracování ve stejném systému. S [Event Hubs Capture](event-hubs-capture-overview.md) zahrnutých v nabídce, lze omezit složitost vašeho řešení tak, že jeden datový proud podporovat kanály v reálném čase i na základě služby batch.

Následující tabulka porovnává dostupné úrovně služby Event Hubs. Nabídka Event hubs úrovně Dedicated je pevnou měsíční cenu ve srovnání s využití ceny pro většinu funkcí úrovně Standard. Vyhrazené úroveň nabízí všechny funkce plánu Standard, ale s enterprise škálování kapacity pro zákazníky s náročné úlohy. 

| Funkce | Standard | Vyhrazený |
| --- |:---:|:---:|:---:|
| Události příchozího přenosu dat | Platba za milion událostí | Zahrnuje |
| Jednotka propustnosti (1 MB/s příchozí, 2 MB/s odchozí) | Platba za hodinu | Zahrnuje |
| Velikost zprávy | 256 kB | 1 MB |
| Zásady vydavatele | Ano | Ano |   
| Skupiny příjemců | 20 | 20 |
| Přehrání zprávy | Ano | Ano |
| Maximální počet jednotek propustnosti | 20 (flexibilní, aby 100)   | 1 jednotka kapacity (CU) ≈ 50 |
| Zprostředkovaná připojení | 1 000 zahrnuté | 100 tisíc v základu |
| Další zprostředkovaná připojení | Ano | Ano |
| Uchovávání zpráv | 1 den v základu | Až 7 dnů v ceně |
| Zachycování | Platba za hodinu | Zahrnuje |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Výhody služby Event hubs úrovně Dedicated kapacity

Při použití vyhrazená Služba Event Hubs jsou dostupné následující výhody:

* Hostování se bez zbytečných dat jiných tenantů jednoho tenanta.
* Velikost zprávy se zvýší na 1 MB než 256 KB pro úroveň Standard.
* Pokaždé, když Opakovatelný výkon.
* Garantované kapacity pro splnění potřeb rozšíření.
* Zahrnuje [zachycení](event-hubs-capture-overview.md) funkce Event Hubs, zajistit integraci s mikrodávek i dlouhodobé uchovávání.
* Nula údržby: služba spravuje Vyrovnávání zatížení, operační systém aktualizací, oprav zabezpečení a dělení.
* Opravili jsme cenou.
* Uchovávání zpráv až na 7 dnů bez dalších poplatků.

Event hubs úrovně Dedicated odebere také některá omezení propustnosti standardní nabídky. Jednotky propustnosti na úrovni Standard mít nárok na 1 000 událostí za sekundu nebo 1 MB za sekundu příchozího přenosu dat za jednotek Propustnosti a double šířka výchozí přenos. Nabídka vyhrazené škálování neplatí žádná omezení na příchozího přenosu dat a událostí odchozího přenosu dat se počítá. Tato omezení jsou jenom řídí kapacitu zpracování u zakoupené event hubs.

Tato vyhrazená a vyhrazeném prostředí poskytuje další možnosti, které jsou jedinečné pro tuto vrstvu, například:

* Určuje počet obory názvů v clusteru.
* Určuje omezení propustnosti na všechny obory názvů.
* Konfiguruje počet služby event hubs v rámci každého oboru názvů.
* Určuje limit počtu oddílů.

Tato služba je zaměřený na největší telemetrická data uživatele a je dostupný pro zákazníky se smlouvou enterprise.

## <a name="how-to-onboard"></a>Jak se zapojit

Kapacitu můžete škálovat směrem nahoru nebo dolů v průběhu měsíce přizpůsobit svým potřebám přidáváním nebo odebíráním kapacitní jednotky. Vyhrazený plán je jedinečný v tom, že budou mít více praktických zkušeností připojení ze služby Event Hubs produktovému týmu a získejte flexibilní nasazení, který je pro vás nejvhodnější. Jak tato skladová položka [požádat podporu fakturace](https://ms.portal.azure.com/#create/Microsoft.Support) nebo zástupce společnosti Microsoft.

## <a name="next-steps"></a>Další postup

Kontaktujte zástupce Microsoftu nebo Microsoft Support a získejte další podrobnosti o Event Hubs vyhrazenou kapacitu. Můžete také další informace o Event Hubs cenové úrovně návštěvou následujících odkazů:

- [Cenách služby Event hubs úrovně Dedicated](https://azure.microsoft.com/pricing/details/event-hubs/). Můžete také kontaktovat zástupce Microsoftu nebo Microsoft Support a získejte další podrobnosti o kapacita vyhrazená Služba Event Hubs.
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md) obsahuje informace o cenách a odpovědi na některé nejčastější dotazy o službě Event Hubs. 
