---
title: Síťové požadavky
description: Požadavky na síť a osvědčené síťové postupy pro optimální zážitek
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 73bbfad4c0535fa00b1aa53764eb52acb83124f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680372"
---
# <a name="network-requirements"></a>Síťové požadavky

Stabilní síťové připojení s nízkou latencí k datovému centru Azure je důležité pro dobré uživatelské prostředí ve vzdáleném vykreslování Azure. Špatné podmínky v síti může mít za následek vynechání připojení, nestabilní, nervózní nebo 'skákání' hologramy a znatelné zpoždění při aktualizaci grafu scény na straně serveru.

## <a name="guidelines-for-network-connectivity"></a>Pokyny pro připojení k síti

Přesné požadavky na síť závisí na konkrétním případu použití, jako je počet a četnost úprav grafu vzdálené scény a také složitost vykresleného zobrazení, ale existuje řada pokynů, které zajišťují, že vaše zkušenosti jsou co nejlepší:

* Připojení k internetu musí podporovat alespoň **50 Mb/s a** **10 Mb/s** konzistentně pro relaci vzdáleného vykreslování Azure pro jednoho uživatele, za předpokladu, že v síti neexistuje žádný konkurenční provoz. Doporučujeme vyšší sazby pro lepší zážitky. S více uživateli ve stejné síti, tyto požadavky vertikálně vertikálně vertikálně navýšit odpovídajícím způsobem.
* Použití **pásma Wi-Fi v pásmu 5 GHz** obvykle přinese lepší výsledky než pásmo 2,4 GHz Wi-Fi, i když obojí by mělo fungovat.
* Pokud jsou v okolí jiné Sítě Wi-Fi, nepoužívejte kanály Wi-Fi, které tyto jiné sítě používají. Pomocí nástrojů pro skenování sítě, jako je [WifiInfoView,](https://www.nirsoft.net/utils/wifi_information_view.html) můžete ověřit, zda kanály, které vaše síť Wi-Fi používá, jsou bez konkurenčního provozu.
* Přísně **se vyhněte použití opakovačů Wi-Fi** nebo přeposílání LAN-over-powerline.
* **Vyhněte se konkurenčnímu provozu intenzivnímu šířky pásma** – například streamování videa nebo her – ve stejné síti Wi-Fi.
* S **dobrou sílu signálu Wi-Fi** je zásadní. Pokud je to možné, držte se blízko přístupového bodu Wi-Fi a vyhněte se překážkám mezi klientským zařízením a přístupovými body.
* Ujistěte se, že se vždy připojíte k **nejbližšímu datovému centru Azure** pro vaši [oblast](regions.md). Čím blíže je datové centrum, tím nižší je latence sítě, což má obrovský vliv na stabilitu hologramu.

## <a name="network-performance-tests"></a>Testy výkonu sítě

Pokud chcete získat počáteční pochopení toho, zda je kvalita připojení k síti dostatečná ke spuštění vzdáleného vykreslování Azure, existují existující online nástroje, které můžete použít. Důrazně doporučujeme spustit tyto online nástroje z přiměřeně výkonného přenosného počítače připojeného ke stejné síti Wi-Fi jako zařízení, na které plánujete spustit klientskou aplikaci Vzdálené vykreslování Azure. Výsledky získané spuštěním testů na mobilním telefonu nebo HoloLens2 jsou obvykle méně užitečné, protože se ukázalo, že vykazují významné rozdíly na zařízeních s nízkým výkonem. Umístění, ve kterém umístíte přenosný počítač by měl být zhruba na stejném místě, na kterém očekáváte, že budete používat zařízení, které spouští klientskou aplikaci Azure Remote Rendering.

Zde je několik jednoduchých kroků pro rychlý test připojení k síti:

1. **Spusťte nástroj pro testování sítě, jako je www.speedtest.net, abyste získali data o celkové latenci a šířce pásma připojení k síti.**
Vyberte server, který je vám nejblíže, a spusťte test. Zatímco server nebude datové centrum Azure, ke kterému se připojí vzdálené vykreslování Azure, výsledná data jsou stále užitečná pro pochopení výkonu vašeho připojení k internetu a Wi-Fi.
   * **Minimální požadavek na** vzdálené vykreslování Azure: Přibližně 40 Mb/s za datovým proudem a 5 Mb/s proti proudu.
   * **Doporučeno** pro vzdálené vykreslování Azure: Přibližně 100 Mb/s po proudu a 10 Mb/s proti proudu.
Doporučujeme spustit test vícekrát a při nejhorších výsledcích.
1. **Použijte nástroj, jako je www.azurespeed.com, který měří latenci datových center Azure**. Vyberte datové centrum Azure podporované vzdáleným vykreslováním Azure, které je vám nejblíže (viz [podporované oblasti](regions.md)) a spusťte **test latence**. Pokud se zobrazí odchylka v číslech, která vidíte, dejte výsledky nějaký čas na stabilizaci.
   * **Minimální požadavek** na vzdálené vykreslování Azure: Latence by měla být konzistentně menší než 100 ms.
   * **Doporučeno** pro vzdálené vykreslování Azure: Latence by měla být konzistentně menší než 70 ms.

Zatímco nízká latence není zárukou, že vzdálené vykreslování Azure bude fungovat dobře ve vaší síti, obvykle jsme viděli, že funguje dobře v situacích, kdy tyto testy úspěšně proběhly.
Pokud se při spuštění vzdáleného vykreslování Azure setkáváte s artefakty, jako je nestabilní, nervózní nebo skákací hologramy, přečtěte [si průvodce odstraňováním potíží](../resources/troubleshoot.md).

## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
