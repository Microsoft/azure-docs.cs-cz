---
title: Automatické prodlužování rezervací Azure
description: Přečtěte si, jak můžete rezervace Azure automaticky prodlužovat a pokračovat v získávání slev za rezervaci.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: bf7945c56fc05c33ae3cfed1c67085b3b28f847f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690506"
---
# <a name="automatically-renew-reservations"></a>Automatické prodlužování rezervací

Rezervace se můžou automaticky prodlužovat, aby se zakoupila náhrada, když vyprší platnost stávající rezervace. Automatické prodlužování představuje snadný způsob, jak pokračovat v získávání slev za rezervaci. Nebudete také muset pozorně sledovat, kdy má dojít k vypršení platnosti rezervace. Díky tomu, že při automatickém prodlužování nemusíte prodlužovat ručně, nehrozí vám, že přijdete o výhody v podobě úspor. Nastavení prodlužování je ve výchozím nastavení vypnuté. Nastavení prodlužování můžete povolit nebo zakázat kdykoli do vypršení platnosti stávající rezervace.

Když při vypršení platnosti stávající rezervace dojde k prodloužení (obnovení) rezervace, vytvoří se nová rezervace. Neprotáhne se tím doba trvání stávající rezervace.

K automatickému prodlužování můžete dát souhlas kdykoliv. Cena za prodloužení je k dispozici 30 dní před vypršením platnosti stávající rezervace. Když prodlužování povolíte více než 30 dní před vypršením platnosti rezervace, 30 dnů před vypršením platnosti vám přijde e-mail s podrobnostmi o nákladech na prodloužení. Cena rezervace se od chvíle, kdy cenu rezervace uzamknete, do okamžiku prodloužení může změnit. Pokud k tomu dojde, vaše náklady na prodloužení budou odpovídat nižší z těchto dvou cen. Množství rezervací můžete změnit. Pokud to uděláte, prodloužení se aktualizuje tak, aby používalo tržní cenu platnou v době změny množství.

Prodlužování není povinné a můžete je odvolat kdykoli před vypršením platnosti stávající rezervace.

## <a name="set-up-renewal"></a>Nastavení prodlužování

Přejděte na Azure Portal > **Rezervace**.

1. Vyberte rezervaci.
2. Klikněte na **Prodloužení**.
3. Vyberte **Po vypršení platnosti automaticky koupit novou rezervaci**.  
  ![Příklad ukazující prodloužení rezervace](./media/reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Pokud neprodloužíte

Vaše služby budou dál normálně fungovat. Po vypršení platnosti rezervace se vám za použití budou účtovat tarify průběžných plateb.

## <a name="required-renewal-permissions"></a>Požadovaná oprávnění k prodloužení

K prodloužení rezervace musíte splňovat následující podmínky:

- Musíte být vlastníkem stávající rezervace.
- Pokud je rezervace vymezená na jedno předplatné nebo skupinu prostředků, musíte být vlastníkem předplatného.
- Pokud má předplatné sdílený rozsah, musíte být vlastníkem předplatného.

## <a name="default-renewal-settings"></a>Výchozí nastavení prodloužení

Prodloužení ve výchozím nastavení zdědí všechny vlastnosti rezervace, jejíž platnost končí. Zakoupené prodloužení rezervace má stejné všechny tyto vlastnosti: skladová položka, oblast, rozsah, předplatné pro fakturaci, termín i množství.

Když ale chcete optimalizovat úspory, můžete aktualizovat množství rezervací zakoupeného prodloužení.

## <a name="when-the-new-reservation-is-purchased"></a>Kdy se zakoupí nová rezervace

Nová rezervace se zakoupí, když vyprší platnost stávající rezervace. Snažíme se zabránit jakémukoli zpoždění mezi oběma rezervacemi. Kontinuita zajistí, že vaše náklady budou předvídatelné a vy i dál získáte slevy.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Změna nadřazené rezervace po nastavení prodlužování

Pokud u rezervace, jejíž platnost končí, uděláte některou z následujících změn, prodlužování rezervace se zruší:

- Rozdělení
- Sloučit
- Převedení rezervace z jednoho účtu do jiného
- Převedení rezervace z předplatného WebDirect do předplatného EA (Enterprise Agreement) nebo jakýkoli jiný způsob nákupu
- Prodloužení registrace

Nová rezervace zdědí při prodloužení z rezervace, jejíž platnost končí, nastavení rozsahu a flexibility velikosti instance.

## <a name="new-reservation-permissions"></a>Oprávnění nové rezervace

Azure zkopíruje oprávnění z rezervace, jejíž platnost končí, do nové rezervace. K nové rezervaci má navíc přístup správce účtu předplatného zakoupené rezervace.

## <a name="potential-renewal-problems"></a>Potenciální problémy s prodloužením

Azure nemusí prodloužení zpracovat v těchto případech:

- Platbu nejde shromáždit.
- Během prodloužení dojde k systémové chybě.
- Skladová položka, jejíž platnost končí, není během prodloužení aktivní.
- Smlouva EA se při prodloužení mění na jinou smlouvu EA.

Pokud nastane některá z předchozích podmínek, přijde vám e-mailové oznámení a prodloužení se deaktivuje.

## <a name="renewal-notification"></a>Oznamování prodloužení

E-maily se odesílají různým lidem – závisí to na způsobu nákupu:

- Zákazníci se smlouvou EA – e-maily se odesílají kontaktům pro oznámení, kteří jsou nastavení na portálu EA.
- Zákazníci s jednotlivými předplatnými a tarify průběžných plateb – e-maily se odesílají uživatelům, kteří jsou nastavení jako správci účtu.
- Zákazníci CSP (Cloud Solution Provider) – e-maily se odesílají kontaktu partnera pro oznámení.

## <a name="next-steps"></a>Další kroky
- Další informace o rezervacích Azure najdete v tématu [Co jsou rezervace Azure?](save-compute-costs-reservations.md).
