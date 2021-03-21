---
title: Zpráva k vydání verze Azure Data Box Gateway 2101 | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro Azure Data Box Gateway, na kterých běží verze 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072638"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Zpráva k vydání verze Azure Data Box Gateway 2101

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro vydání 2101 Azure Data Box Gateway.

Poznámky k verzi se průběžně aktualizují. Jako kritické problémy, které vyžadují řešení, se přidají. Před nasazením Azure Data Box Gateway pečlivě zkontrolujte informace v poznámkách k verzi.  

Tato verze odpovídá verzím softwaru:

- **Data Box Gateway 2101 (1.6.1475.2528)** – KB 4603462

> [!NOTE]
> Aktualizace 2101 se dá použít jenom pro všechna zařízení, na kterých běží Obecná dostupnost softwaru (GA) nebo novější verze.

## <a name="whats-new"></a>Novinky

Tato verze obsahuje následující opravy chyb:

- **Odeslat problém** – Tato verze opravuje problém s nahráváním, kdy se dá odeslat restart, protože selhání může zpomalit dokončení nahrávání. K tomuto problému může dojít při nahrávání datové sady, která se primárně skládá ze souborů, které jsou ve velké velikosti vzhledem k dostupné šířce pásma, zejména v případě, že je omezení šířky pásma aktivní. Tato změna zajišťuje dostatek příležitostí k dokončení nahrávání před restartováním nahrání pro daný soubor.

Tato verze také obsahuje následující aktualizace:

- Všechny kumulativní aktualizace systému Windows a aktualizace rozhraní .NET Framework vydané až do října 2020.
- Statická IP adresa pro Azure Data Box Gateway se zachovává mezi aktualizacemi softwaru.

## <a name="known-issues-in-this-release"></a>Známé problémy v této verzi

Pro tuto verzi se neuvedly žádné nové problémy. Všechny uvedené verze převzaly problémy z předchozích verzí. Pokud chcete zobrazit seznam známých problémů, přejděte ke [známým problémům ve verzi GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Další kroky

- [Příprava k nasazení služby Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
