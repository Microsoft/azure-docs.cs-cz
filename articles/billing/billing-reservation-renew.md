---
title: Automatické obnovení rezervací Azure
description: Přečtěte si, jak můžete automaticky obnovit rezervace Azure a pokračovat v získávání slev rezervací.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679455"
---
# <a name="automatically-renew-reservations"></a>Automatické obnovení rezervací

Můžete obnovit rezervace pro automatické zakoupení náhrady, když vyprší platnost existující rezervace. Automatické obnovení představuje snadný způsob, jak pokračovat v získávání slev rezervace. Také vám ušetříme, abyste museli pozorně monitorovat vypršení platnosti rezervace. Při automatickém obnovení zabráníte ztrátě výhod úspory, protože nemusíte ručně obnovovat. Nastavení obnovení je ve výchozím nastavení vypnuté. Povolte nebo zakažte nastavení obnovení kdykoli, až do vypršení platnosti existující rezervace.

Když vyprší platnost existující rezervace, vytvoří se při obnovení rezervace nová rezervace. Nerozšiřuje pojem existující rezervace.

Kdykoliv se můžete přihlásit k automatickému prodloužení platnosti. Cena za obnovení je k dispozici 30 dní před vypršením platnosti existující rezervace. Když zapnete obnovení více než 30 dní před vypršením platnosti rezervace, pošleme vám e-mail s podrobnostmi o obnově 30 dnů před vypršením platnosti. Cena za rezervaci se může změnit mezi časem, kdy zamknete cenu za obnovení a čas obnovení. Pokud ano, vaše náklady na obnovení jsou nižší z těchto dvou nákladů. Můžete provádět změny rezervovaného množství. Pokud tak učiníte, obnovení se aktualizuje tak, aby se v době změny množství používala sada cen za uvedení na trh.

Nemusíte si obnovit žádnou povinnost a můžete si obnovení kdykoliv odhlásit, než vyprší platnost existující rezervace.

## <a name="set-up-renewal"></a>Nastavení obnovení

Přejít na Azure Portal > **rezervacích**.

1. Vyberte rezervaci.
2. Klikněte na **obnovení**.
3. **Po vypršení platnosti vyberte automaticky koupit novou rezervaci**.  
  ![Příklad znázorňující obnovení rezervace](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Pokud neobnovíte

Vaše služby budou nadále fungovat normálně. Po vypršení platnosti rezervace se vám účtují poplatky za průběžné platby za vaše využití.

## <a name="required-renewal-permissions"></a>Požadovaná oprávnění k obnovení

K obnovení rezervace jsou nutné následující podmínky:

- Musíte být vlastníkem existující rezervace.
- Pokud je rezervace vymezená na jedno předplatné nebo skupinu prostředků, musíte být vlastníkem předplatného.
- Pokud má sdílený rozsah, musíte být vlastníkem předplatného.

## <a name="default-renewal-settings"></a>Výchozí nastavení obnovení

Ve výchozím nastavení zdědí obnovení všechny vlastnosti z rezervace s vypršenou platností. Nákup obnovení rezervace má stejnou SKU, oblast, rozsah, fakturační předplatné, termín a množství.

Můžete ale aktualizovat nákupní množství rezervace, aby se optimalizoval vaše úspory.

## <a name="when-the-new-reservation-is-purchased"></a>Při nákupu nové rezervace

Po vypršení platnosti existující rezervace se zakoupí nová rezervace. Zkusíme zabránit jakémukoli zpoždění mezi dvěma rezervacemi. Kontinuita zajišťuje, že vaše náklady jsou předvídatelné a Vy i nadále získáte slevy.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Změna nadřazené rezervace po nastavení obnovení

Pokud provedete některé z následujících změn rezervace, obnovení rezervace se zruší:

- Rozdělit
- Sloučit
- Přenos rezervace z jednoho účtu na jiný
- Převod rezervace z předplatného WebDirect do předplatného Enterprise Agreement (EA) nebo jakékoli jiné metody nákupu
- Prodloužit registraci

Nová rezervace zdědí nastavení rozsah a flexibilita velikosti instance od rezervace s vypršenou platností během obnovování.

## <a name="new-reservation-permissions"></a>Nová oprávnění rezervace

Azure kopíruje oprávnění z rezervace s vypršenou platností na novou rezervaci. Kromě toho má správce účtu předplatného pro nákup rezervace přístup k nové rezervaci.

## <a name="potential-renewal-problems"></a>Potenciální problémy s obnovením

Azure nemusí toto obnovení zpracovat, pokud:

- Platbu nejde shromáždit.
- Během obnovování dojde k systémové chybě.
- Vyprší platnost SKU, která není aktivní během obnovování
- EA se obnoví na jiný EA.

Pokud dojde k některé z předchozích podmínek a obnovení je deaktivováno, obdržíte e-mailové oznámení.

## <a name="renewal-notification"></a>Oznámení o prodloužení

E-maily se odesílají různým lidem v závislosti na metodě nákupu:

- Zákazníci se smlouvou EA – e-maily se odesílají na portál EA na portálu EA.
- Zákazníci s jednotlivými předplatnými a tarify průběžných plateb – e-maily se odesílají uživatelům, kteří jsou nastaveni jako správci účtu.
- Zákazníci s poskytovatelem Cloud Solution Provider – e-maily se odesílají na kontaktní oznámení partnera.

## <a name="next-steps"></a>Další postup
- Další informace o Azure Reservations najdete v tématu [co jsou Azure reservations?](billing-save-compute-costs-reservations.md)
