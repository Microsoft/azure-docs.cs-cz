---
title: "Interní informace o stavu spolehlivé infrastruktury Azure Service Manager a spolehlivé kolekce | Microsoft Docs"
description: "Podrobné informace o konceptech spolehlivé kolekce a návrhu v Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Interní informace o stavu spolehlivé infrastruktury Azure Service Manager a spolehlivé kolekce
Tento dokument obsahuje podrobné uvnitř spolehlivé správce stavu a spolehlivé kolekcí najdete v části Jak základní komponenty fungují na pozadí.

> [!NOTE]
> Tento dokument je práce v průběhu. Přidávání komentářů k Řekněte nám, co téma, které chcete zobrazit další informace o tomto článku.
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>Model místní trvalost: kontrolního bodu a protokolu
Správce spolehlivé stavu a spolehlivé kolekce postupujte podle trvalost model, který se nazývá kontrolního bodu a protokolu.
V tomto modelu je každé změně stavu nejprve zaznamenána na disku a potom se použijí v paměti.
Stav dokončení samotné je nastavené jako trvalé jen občas (také známa jako Kontrolní bod).
Výhodou je, že rozdíly jsou převedena na sekvenční připojovacího zápisy na disk pro zlepšení výkonu.

Abyste lépe pochopili modelu kontrolního bodu a protokolu, nejprve Podíváme se na scénář nekonečné disku.
Správce spolehlivé stavu zaznamená každé operace předtím, než se replikují.
Protokolování umožňuje spolehlivé kolekce použití operace jenom v paměti.
Vzhledem k tomu, že protokoly jsou nastavené jako trvalé, i když replika selže a je třeba restartovat, spolehlivé správce stavu má dostatek informací v protokolu opakování všechny operace, které repliky ztratil.
Protože disk je nekonečno, záznamy protokolu nikdy třeba je odebrat a spolehlivé kolekce je potřeba spravovat jenom stav v paměti.

Nyní Podíváme se na scénář konečné disku.
Jako záznamy protokolu hromadit, spolehlivé správce stavu spustí nedostatek místa na disku.
Předtím, než k tomu dojde, je potřeba zkrátit protokol, aby uvolnil prostor pro novější záznamy spolehlivé správce stavu.
Správce spolehlivé stavu požadavků spolehlivé kolekce kontrolního bodu jejich stavu v paměti na disk.
V tomto okamžiku by spolehlivé kolekce zachována jeho stav v paměti.
Jakmile spolehlivé kolekce dokončit jejich kontrolní body, spolehlivé správce stavu můžete zkrátit protokol pro uvolnění místa na disku.
Když repliku je třeba restartovat, spolehlivé kolekce obnovit jejich kontrolní bod stavu a spolehlivé správce stavu obnoví hraje zpět všechny změny stavu, k nimž došlo od posledního kontrolního bodu.

Jiné hodnoty add vytváření kontrolních bodů je, že vylepšuje časy obnovení v běžné scénáře. Protokol obsahuje všechny operace, které došlo od posledního kontrolního bodu.
Proto může zahrnovat víc verzí položky jako více hodnot pro daný řádek v spolehlivé slovníku.
Naproti tomu kontrolní body spolehlivé kolekce nejnovější verze jednotlivých hodnot pro klíč.

## <a name="next-steps"></a>Další kroky
* [Transakce a zámky.](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

