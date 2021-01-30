---
title: Azure Stack Edge pro s poznámkou k verzi FPGA 2101 | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro verzi Azure Stack Edge 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 025694dc020bb18ce66574bac476f34034353721
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072567"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack Edge pro s poznámkou k verzi FPGA 2101

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro vydání 2101 Azure Stack Edge pro s vestavěným polem programovatelné brány (FPGA).

Poznámky k verzi se průběžně aktualizují. Jako kritické problémy, které vyžadují řešení, se přidají. Před nasazením Azure Stack hraničního zařízení pečlivě zkontrolujte informace v poznámkách k verzi.  

Tato verze odpovídá verzi softwaru:

- **Azure Stack Edge 2101 (1.6.1475.2528)** – KB 4599267

> [!NOTE]
> Aktualizace 2101 se dá použít jenom u zařízení, na kterých běží všeobecně dostupná verze softwaru (GA) nebo novější.

## <a name="whats-new"></a>Novinky

Tato verze obsahuje následující opravy chyb:

- **Odeslat problém** – Tato verze opravuje problém nahrávání, kde nahrávání restartování způsobené selháním může zpomalit dokončení nahrávání. K tomuto problému může dojít při nahrávání datové sady, která se primárně skládá ze souborů, které jsou velké vzhledem k dostupné šířce pásma, zejména v případě, že je omezení šířky pásma aktivní. Tato změna zajišťuje dostatek příležitostí k dokončení nahrávání před restartováním nahrání pro daný soubor.

Tato verze také obsahuje následující aktualizace:

- Všechny kumulativní aktualizace systému Windows a aktualizace rozhraní .NET Framework vydané až do října 2020.
- Verze firmwaru řadiče pro správu základní desky (BMC) je upgradována z 3.32.32.32 na 3.36.36.36 během instalace výrobního programu pro řešení nekompatibility s novějšími jednotkami napájení Dell.
- Statická IP adresa pro Azure Data Box Gateway se zachovává mezi aktualizacemi softwaru.
- Tato verze podporuje IoT Edge 1.0.9.3 na hraničních zařízeních Azure Stack.

## <a name="known-issues-in-this-release"></a>Známé problémy v této verzi

Pro tuto verzi se neuvedly žádné nové problémy. Všechny uvedené verze převzaly problémy z předchozích verzí. Pokud chcete zobrazit seznam známých problémů, přejděte ke [známým problémům ve verzi GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)
