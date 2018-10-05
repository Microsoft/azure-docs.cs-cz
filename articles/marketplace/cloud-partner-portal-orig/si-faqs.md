---
title: Prodejce Insights – nejčastější dotazy | Dokumentace Microsoftu
description: Nejčastější dotazy o funkci prodejce Insights portál partnerů cloudu.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: ec7fc3a7877cf0bffac0043a74c34d6f0f04826b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809539"
---
<a name="seller-insights-faq"></a>Prodejce Insights – nejčastější dotazy
===================

Tento článek obsahuje pokyny pro běžné postupy uživatele v rámci a dotazy týkající se Insights prodejce.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Najít definice pro hodnoty v souboru staženého transakcí
------------------------------------------------------------------

Definice metriky hodnot v souboru transakcí najdete v článku [definice Insights prodejce](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Viz podrobnosti o zákazníkovi transakcí, pro které jste byla zaplacena mi
-------------------------------------------------------------

Po stažení transakcí z modulu výběr, vyhledejte sloupec s názvem **výběr stav**a použijte filtr, aby se zobrazily pouze hodnotu "Paid na více instancí." Tyto sloupce se zobrazí podrobnosti o zákazníkovi obsahující: **název společnosti**, **zákaznických e-mailů**, **zemi zákazníka**, **stav odběratele**, a **poštovní směrovací číslo zákazníka**.


<a name="calculate-my-open-accounts-receivable"></a>Vypočítat Moje otevřít pohledávek
-------------------------------------

Po stažení transakcí z modulu výběr, vyhledejte sloupec s názvem **výběr stav**a použijte filtr, aby se zobrazily pouze hodnotu "Nadcházející výběr" a "Není připraven pro výběr." Pak provede součet sloupec s názvem **výběr částka (PC)**.


<a name="calculate-revenue-by-customer-usage-period"></a>Vypočítat výnosy podle: období využití zákazníka
------------------------------------------

Po stažení transakcí z modulu výběr, vyhledejte sloupec s názvem **stav transakce**a hodnota "Paid" filtru.   Pro každou transakci uvedený, sloupec s názvem **výběr částka (PC)** představuje velikost byly placené.  Pro odhad doby využití asociovaný s transakcí, použít sloupec **datum poplatku**, což je aproximace posledního dne spotřeby pro období, na kterou se vztahuje transakci.


<a name="calculate-your-bad-debt"></a>Vypočítat nedobytné pohledávky
---------------------

Po stažení transakcí z modulu výběr, vyhledejte sloupec s názvem **konečný stav kolekce**a použijte filtr jenom zobrazit hodnota "odepsat." Pak provede součet sloupec s názvem **výběr částka (PC)**.


<a name="view-payout-or-customer-contact-information"></a>Zobrazit výběr nebo zákazník kontaktní informace
-------------------------------------------

Přihlaste se jako uživatel s rolí "vlastník" a ne na roli "Přispěvatel". Pouze vlastník role se zobrazí informace o výplata a zákazníků. Můžete najít další informace o uživatelských rolích na článek [spravovat uživatele](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Vypočítat Moje zálohy výplaty
----------------------------

Po stažení transakcí z modulu výběr, vyhledejte sloupec s názvem **typ transakce**a použijte filtr, aby se zobrazily pouze hodnotu "Poplatek." Dále vyhledejte sloupec s názvem **konečný stav kolekce**a použijte filtr, aby se zobrazily pouze hodnotu "Probíhá". Nakonec součet **výběr částka (PC)** sloupec pro výpočet všech záloh placené před kolekce od zákazníka.


<a name="calculate-customer-refunds"></a>Výpočet náhrad zákazníka
--------------------------

Po stažení transakcí z modulu výběr, vyhledejte sloupec s názvem **konečný stav kolekce**a použijte filtr, aby se zobrazily pouze hodnotu "Refundaci." Součet **částka poplatků (PC)** zpracovat sloupec pro výpočet všechny finanční prostředky pro vaše zákazníky.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identifikujte transakce, které se podílejí kanál partnera společnosti Microsoft
----------------------------------------------------------------

Všechny transakce ve sloupci **typ licence Azure** , které jsou filtrovány k zobrazení hodnot "Enterprise přes prodejce" a "Cloud Solution Provider" zahrnují kanál partnera společnosti Microsoft. Podrobné informace o partnerovi najdete jejich **jméno prodejce** a **e-mailu prodejce** stažení modulu výběr a stáhnout modul zákazníka.


<a name="identify-trial-usage-and-trial-conversions"></a>Identifikujte zkušební použití a zkušební verze převody
------------------------------------------

Pořadí, použití a výběr modulu soubory ke stažení je teď obsahují **zkušební koncové datum** vám pomohou pochopit, zkušební období ukončení pro tento konkrétní pořadí, kde je to možné. Zkušební použití a objednávky najdete vyhledejte **fakturace typ SKU** sloupce v souborech ke stažení a použijte filtr, aby se zobrazily pouze hodnotu "Zkušební verzi." Zobrazíte zkušební převody, vyhledejte **zkušební koncové datum** sloupce v souborech ke stažení a použijte filtr, aby se zobrazily pouze objednávky, kdy **zkušební koncové datum** je dnešní datum a **zrušit datum** sloupec je prázdný nebo vyšší než **datum ukončení zkušební verze**.


<a name="when-is-my-monthly-payout-calculated"></a>Když se počítá Můj měsíční výběr
------------------------------------

Vaše výplaty jsou vydané, 15. dne každého měsíce pro všechny částky připravené pro výběr, poslední kalendáři den předchozího měsíce. Na třetí den v měsíci, bude Microsoft vypočítat velikost výběr na předchozí měsíc a aktualizovat všechny příslušné poplatek za transakce v stahování s "Nadcházející výběr" v **výběr stav** sloupce. Tyto transakce zůstane v tomto stavu, dokud nebude odeslána žádost o platbu na bankovním účtu, po kterém jejich **výběr stav** se aktualizují na "Placené Out" a "Výběr datum" zaktualizuje a zobrazí datum jsme odeslané žádost o platbu se svou bankou.


<a name="calculate-customer-acquisition-and-loss"></a>Výpočet zákazníků a ke ztrátě
---------------------------------------

Můžete zobrazit datum, kdy zákazník nejprve koupil jednu z vašich nabídek vyhledáním **získat datum** sloupec downloadu zákazníka. Podobně můžete zobrazit datum, po kterém už měli všechny nabídky někým jiným než vámi vyhledáním **ztráty datum** sloupec downloadu zákazníka.


<a name="finding-more-help"></a>Hledání další nápovědy
-----------------

- [Definice Insights prodejce](./si-insights-definitions-v4.md) -najít definice pro metriky a data

- [Začínáme s prodejce Insights](./si-getting-started.md) – Úvod do funkce prodejce přehledů.

