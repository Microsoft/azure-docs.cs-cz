---
title: Jak nakonfigurovat ceny a dostupnost konzultačních služeb v partnerském centru Microsoftu
description: Přečtěte si, jak konfigurovat konzultační informace nabídky služby a dostupnost na trhu na komerčním webu Microsoftu pomocí partnerského centra.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780225"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>Jak nakonfigurovat ceny a dostupnost konzultačních služeb

Tento článek vysvětluje, jak definovat dostupnost trhu a podrobnosti o cenách vaší nabídky konzultační služby na komerčním webu Microsoft Marketplace.

> [!NOTE]
> Konzultační nabídky služby jsou pouze výpisy. Všechny transakce musí být spravované mezi vámi a vašimi zákazníky mimo komerční web Marketplace.

## <a name="markets"></a>Trhy

V části **trhy** vyberete země nebo oblasti, kde bude vaše konzultační služba k dispozici.

1. V části **trhy** vyberte odkaz **Upravit trhy** .
2. V dialogovém okně, které se zobrazí, vyberte umístění na trhu, kde chcete nabídku zpřístupnit. Musíte vybrat minimálně jeden z nich, maximálně 141 trhů.
3. Výběrem **Uložit** zavřete dialogové okno.

## <a name="preview-audience"></a>Cílová skupina Preview

Když publikujete nebo aktualizujete nabídku konzultačních služeb, Partnerská služba vytvoří verzi Preview tohoto výpisu. Tato verze Preview je viditelná jenom pro uživatele, kteří mají **skrytý klíč**.

Do pole **Skrýt klíč** zadejte alfanumerický řetězec, který budete používat pro přístup k verzi Preview vaší nabídky.

## <a name="pricing-informational-only"></a>Ceny (jenom informativní)

V části s **cenami** definujte, jestli se jedná o bezplatnou nebo placenou nabídku.

Pro placené nabídky určete, zda je cena pevná nebo odhadovaná. Pokud je odhadovaná cena, musí popis vaší nabídky popsat, k jakým faktorům bude tato cena platit.

Pokud jste v části **trhy** zvolili jednu zemi nebo oblast, zadejte cenu v měně podporované pro daný trh a vyberte **Uložit koncept**. Seznam podporovaných měn najdete v tématu [geografická dostupnost a podpora měny pro komerční tržiště](./marketplace-geo-availability-currencies.md) .

Pokud jste v části **trhy** vybrali více zemí nebo oblastí, zadejte cenu za USA dolarů (USD) a vyberte **Uložit koncept**. Partnerské centrum převede tuto cenu na místní měnu všech vybraných trhů pomocí směnných tarifů, které jsou k dispozici při uložení konceptu.

Chcete-li ověřit převod nebo nastavit vlastní ceny na individuálním trhu, je nutné exportovat, upravit a poté importovat tabulku s cenami:

1. V části **ceny** vyberte odkaz **exportovat data cen** . Tím se stáhne soubor do zařízení.
1. Otevřete soubor exportedPrice.xlsx v aplikaci Microsoft Excel.
1. V tabulce můžete upravovat ceny a měny pro každý trh. Seznam podporovaných měn najdete v tématu [geografická dostupnost a podpora měny pro komerční tržiště](./marketplace-geo-availability-currencies.md) . Až budete hotovi, soubor uložte.
1. V partnerském centru v části **ceny** vyberte odkaz **importovat data ceny** . Při importu souboru se přepíší předchozí informace o cenách.

> [!IMPORTANT]
> Ceny, které definujete v partnerském centru, jsou statické a v směnných kurzech nenásledují žádné odchylky. Pokud chcete po publikování změnit cenu na jednom nebo více trzích, aktualizujte a znovu odešlete nabídku v partnerském centru.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="next-steps"></a>Další kroky

* [Kontrola a publikování](review-publish-offer.md)