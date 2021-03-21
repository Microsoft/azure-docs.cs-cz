---
title: Požadavky sítě
description: Požadavky na síť a osvědčené síťové postupy pro optimální prostředí
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fd8686cc396d5fcee20590fbac8bccaf187b024d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735943"
---
# <a name="network-requirements"></a>Požadavky sítě

Stabilní a nízká latence síťového připojení k datovému centru Azure je důležitá pro dobrý zážitek uživatele při vzdáleném vykreslování Azure. Špatné stavy sítě mohou způsobit přerušená připojení, nestabilní, kolísání nebo přechodové hologramy a znatelné zpoždění při aktualizaci grafu scény na straně serveru.

## <a name="guidelines-for-network-connectivity"></a>Pokyny pro připojení k síti

Přesné požadavky sítě závisí na konkrétním případu použití, například na počtu a četnosti úprav v grafu vzdálené scény a na složitosti zobrazeného zobrazení, ale existuje několik pokynů, jak zajistit, aby vaše prostředí bylo co nejužitečnější:

* Připojení k Internetu musí podporovat aspoň **40 MB/s** a **5 MB/s** pro jednu relaci uživatele vzdáleného vykreslování Azure za předpokladu, že v síti nedochází k žádnému konkurenčnímu provozu. Pro lepší prostředí doporučujeme vyšší sazby. 
* **Wi-Fi** je doporučený typ sítě, protože podporuje připojení s nízkou latencí, vysokým bandwith a stabilním připojením. Některé mobilní sítě přináší kolísání, které může vést k špatnému prostředí. 
* Použití **Wi-Fiového pásma 5 GHz** obvykle vytvoří lepší výsledky než Wi-Fi pásmo 2,4 GHz, ale obě by měly fungovat.
* Pokud jsou v blízkosti jiné Wi-Fi sítě, vyhněte se použití Wi-Fi kanálů používaných v těchto dalších sítích. Pomocí skenovacích nástrojů sítě, jako je [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) , můžete ověřit, jestli kanály, které vaše Wi-Fi síť používá, nejsou konkurenčním provozem.
* Používejte naprostou **nepoužívejte předávací Wi-Fi** nebo přesměrování LAN přes elektrickou síť.
* **Vyhněte se nekonkurenčnímu přenosu šířky pásma** , jako je video nebo audiovizuální streamování – ve stejné Wi-Fi síti.
* Pokud máte ve stejném přístupovém bodu více zařízení, požadavky se budou škálovat odpovídajícím způsobem. Pokud máte více přístupových bodů v rámci prostředí, vyrovnává zatížení zařízení v rámci přístupových bodů, aby byly rovnoměrně distribuovány.
* Je nutné mít **dobrý Wi-Fi sílu signálu** . Pokud je to možné, zůstaňte blízko Wi-Fi přístupového bodu a vyhnete se překážkám mezi klientským zařízením a přístupovými body.
* Ujistěte se, že se vždy připojíte k **nejbližšímu datovému centru Azure** pro vaši [oblast](regions.md). Čím blíže datové centrum, tím nižší latence sítě, což má velký vliv na stabilitu hologramů.

> [!NOTE]
> Použitá šířka pásma je většinou spotřebovaná datovým proudem videa, který je dále rozdělen mezi informace o barvě a hloubkě (60 Hz, stereo).

## <a name="network-performance-tests"></a>Testy výkonu sítě

Pokud chcete získat počáteční informace o tom, jestli je kvalita připojení k síti dostatečná pro spuštění vzdáleného vykreslování Azure, existují existující online nástroje, které můžete použít. Důrazně doporučujeme spouštět tyto online nástroje z dostatečně výkonného přenosného počítače připojeného ke stejnému Wi-Fi jako zařízení, na kterém plánujete spustit klientskou aplikaci vzdáleného vykreslování Azure. Výsledky získané z spuštění testů na mobilním telefonu nebo v HoloLens2 jsou obvykle méně užitečné, protože byly prověřeny tak, aby na zařízeních s nízkým koncovým bodem ukázaly významnou variaci. Umístění přenosného počítače by mělo být zhruba na stejném místě, ve kterém očekáváte použít zařízení, ve kterém je spuštěná klientská aplikace pro vzdálené vykreslování Azure.

Tady je několik jednoduchých kroků pro rychlé testování připojení k síti:

1. **Spusťte nástroj pro testování sítě, jako je www.speedtest.net, a Získejte data o celkové latenci a nadřízenosti a podřízené šířce pásma síťového připojení.**
Vyberte server, který je nejblíže a spusťte test. I když nebude server datacentrum Azure, ke kterému se bude připojovat ke vzdálenému vykreslování Azure, výsledná data jsou stále užitečná pro pochopení výkonu připojení k Internetu a Wi-Fi.
   * **Minimální požadavek** na vzdálené vykreslování Azure: přibližně 40 MB/s pro odesílání dat a 5 MB/s.
   * **Doporučuje** se pro vzdálené vykreslování Azure: přibližně 100 MB/s a až 10 MB/s.
Doporučujeme spustit test několikrát a vzít nejhorší výsledky.
1. **Použijte nástroj, jako je www.azurespeed.com, který měří latenci pro datová centra Azure**. Vyberte datové centrum Azure podporované vzdáleným vykreslením Azure, které je pro vás nejblíže (viz [podporované oblasti](regions.md)) a spusťte **test latence**. Pokud se v číslech, které vidíte, vyskytnou odchylky, dejte výsledky čas do stabilizace.
   * **Minimální požadavek** na vzdálené vykreslování Azure: latence by měla být konzistentně nižší než 80 MS.
   * **Doporučuje** se pro vzdálené vykreslování Azure: latence by měla být konzistentně nižší než 40 MS.

I když nízká latence není záruka, že vzdálené vykreslování Azure bude ve vaší síti dobře fungovat, obvykle se v situacích, kdy se tyto testy úspěšně úspěšně provedlo, doplní velmi dobře.
Pokud se setkáváte s artefakty, jako je nestabilní, kolísání nebo přechodové hologramy při spuštění vzdáleného vykreslování Azure, přečtěte si [příručku k odstraňování potíží](../resources/troubleshoot.md).

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
