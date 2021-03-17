---
title: Azure Stack Edge & zpráva k vydání verze Azure Data Box Gateway 2007 | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro Azure Stack Edge a Data Box Gateway spuštění 2007 verze.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581809"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Zpráva k vydání verze Azure Stack Edge a Azure Data Box Gateway 2007

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro vydání 2007 pro Azure Stack Edge a Data Box Gateway.

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením Azure Stack Edge/Data Box Gateway pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Tato verze odpovídá verzím softwaru:

- **Azure Stack Edge 2007 (1.6.1280.1667)** – KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** – KB 4566550

> [!NOTE]
> Aktualizace 2007 se dá použít jenom pro všechna zařízení, na kterých běží Obecná dostupnost softwaru (GA) nebo novější verze.

## <a name="whats-new"></a>Co je nového

Tato verze obsahuje následující opravy chyb:

- **Odeslat problém** – Tato verze opravuje problém nahrávání, kde se nahrávání restartuje kvůli selháním, které může zpomalit dokončení nahrávání. K tomuto problému může dojít při nahrávání datové sady, která se primárně skládá ze souborů, které jsou ve velké velikosti vzhledem k dostupné šířce pásma, zejména v případě, že je omezení šířky pásma aktivní. Tato změna zajistí, že se před dokončením nahrávání pro daný soubor dokončí dostatek příležitostí.

Tato verze také obsahuje následující aktualizace:

- Základní image pro virtuální pevný disk Windows se aktualizovala.
- K dispozici jsou všechny kumulativní aktualizace systému Windows a aktualizace rozhraní .NET Framework, které byly vydány prostřednictvím května 2020.
- Tato verze podporuje IoT Edge 1.0.9.3 na hraničních zařízeních Azure Stack.

## <a name="known-issues-in-this-release"></a>Známé problémy v této verzi

Pro tuto verzi se neuvedly žádné nové problémy. Všechny uvedené verze převzaly problémy z předchozích verzí. Pokud chcete zobrazit seznam známých problémů, přejděte ke [známým problémům ve verzi GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)
- [Příprava k nasazení služby Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
