---
title: Nejčastější dotazy k přehledům prodejců
description: Nejčastější dotazy týkající se funkce Přehledy prodejců na portálu partnerů cloudu.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285380"
---
<a name="seller-insights-faq"></a>Nejčastější dotazy k přehledům prodejců
===================

Tento článek obsahuje pokyny pro běžné uživatelské postupy v rámci a otázky týkající se prodejce Insights.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Najít definice hodnot ve staženém transakčním souboru
------------------------------------------------------------------

Definice hodnot metrik v transakčním souboru naleznete v článku [Definice přehledů prodejců](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Zobrazit podrobnosti o zákaznících o transakcích, za které jsem byl zaplacen
-------------------------------------------------------------

Po stažení transakcí z modulu Výplata vyhledejte sloupec označený **Stav výplaty**a použijte filtr pouze pro zobrazení hodnoty "Vyplaceno". Zobrazí se následující sloupce obsahující podrobnosti o **zákazníkovi: Název společnosti**, **E-mail zákazníka**, **Země zákazníka**, **Stav zákazníka**a **PSČ zákazníka**.


<a name="calculate-my-open-accounts-receivable"></a>Výpočet otevřených pohledávek
-------------------------------------

Po stažení transakcí z modulu Výplata vyhledejte sloupec označený **Stav výplaty**a použijte filtr tak, aby zobrazoval pouze hodnotu "Nadcházející výplata" a "Není připraven a výplata". Pak sečte sloupec s názvem **Částka výplaty (PC)**.


<a name="calculate-revenue-by-customer-usage-period"></a>Vypočítat výnosy podle období využití odběratele
------------------------------------------

Po stažení transakcí z modulu Výplata vyhledejte sloupec označený **Stav transakce**a filtrujte hodnotu "Zaplaceno".   Pro každou uvedenou transakci představuje sloupec označený **Částka výplaty (PC)** částku, která vám byla vyplacena.  Chcete-li odhadnout období využití přidružené k transakci, použijte sloupec **Datum poplatku**, což je aproximace posledního dne použití za období, na které se transakce vztahuje.


<a name="calculate-your-bad-debt"></a>Spočítejte si svůj nedobytný dluh
---------------------

Po stažení transakcí z modulu Výplata vyhledejte sloupec označený **Stav konečné sbírky**a použijte filtr pouze na hodnotu "Odepsat". Pak sečte sloupec s názvem **Částka výplaty (PC)**.


<a name="view-payout-or-customer-contact-information"></a>Zobrazit informace o výplatě nebo kontaktech zákazníka
-------------------------------------------

Přihlaste se jako uživatel s rolí vlastníka a ne s rolí přispěvatele. Pouze role vlastníka bude vidět výplatu a informace o zákaznících. Další informace o uživatelských rolích naleznete v článku [Správa uživatelů](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Spočítejte si výplaty záloh
----------------------------

Po stažení transakcí z modulu Výplata vyhledejte sloupec s názvem **Typ transakce**a použijte filtr pouze na zobrazení hodnoty "Poplatek". Dále vyhledejte sloupec označený **Stav konečné kolekce**a použijte filtr pouze pro zobrazení hodnoty "Probíhá". Nakonec sečte sloupec **Částka výplaty (PC)** pro výpočet všech záloh, které vám byly vyplaceny před vyzvednutím od zákazníka.


<a name="calculate-customer-refunds"></a>Vypočítat refundace odběratele
--------------------------

Po stažení transakcí z modulu Výplata vyhledejte sloupec označený **Stav konečné sbírky**a použijte filtr pouze pro zobrazení hodnoty "Refund". Sečtete sloupec **Částka poplatku (PC)** pro výpočet všech refundací zpracovaných pro vaše zákazníky.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Určení transakcí, které zahrnovaly partnera microsoft channel
----------------------------------------------------------------

Všechny transakce ve sloupci **Azure Typ licence,** které jsou filtrovány k zobrazení hodnot "Enterprise prostřednictvím prodejce" a "Poskytovatel cloudových řešení" zahrnují partnera Microsoft Channel. Další podrobnosti o partnerovi najdete **v** části Stažení modulu výplaty **a** v modulu Zákazník.


<a name="identify-trial-usage-and-trial-conversions"></a>Identifikace využití zkušební verze a konverzí zkušební verze
------------------------------------------

Stažení modulu Objednávka, Využití a Výplata nyní obsahují **datum ukončení zkušební verze,** které vám pomohou zjistit, kdy zkušební období pro danou konkrétní objednávku skončilo, pokud je to možné. Chcete-li zobrazit využití zkušební verze a objednávky, vyhledejte sloupec **Typ fakturace skladové** položky ve stahování a použijte filtr pouze pro zobrazení hodnoty "Zkušební verze". Chcete-li zobrazit zkušební konverze, vyhledejte sloupec **Datum ukončení zkušební verze** ve stahování a použijte filtr pouze pro zobrazení objednávek, pokud je datum ukončení zkušební **doby** za dnešním datem a sloupec **Datum zrušení** je prázdný nebo novější než datum ukončení **zkušební verze**.


<a name="when-is-my-monthly-payout-calculated"></a>Kdy se počítá moje měsíční výplata
------------------------------------

Vaše výplaty jsou vám vydávány do 15. Třetí den v měsíci společnost Microsoft vypočítá částku výplaty za předchozí měsíc a aktualizuje všechny příslušné transakce s poplatky ve stahování pomocí sloupce **"Nadcházející** výplata" ve sloupci Stav výplaty. Tyto transakce zůstanou v tomto stavu, dokud nebude žádost o platbu odeslána na váš bankovní účet, a v té době bude **jejich stav výplaty** aktualizován na "Vyplaceno" a "Datum výplaty" bude aktualizováno tak, aby zobrazovala datum, kdy jsme odeslali žádost o platbu do vaší banky.


<a name="calculate-customer-acquisition-and-loss"></a>Výpočet získávání a ztráty odběratele
---------------------------------------

Datum, kdy zákazník poprvé zakoupil jednu z vašich nabídek, můžete zobrazit tak, že v souboru ke stažení zákazníka najdete sloupec **Datum získané.** Podobně můžete vidět datum, po kterém již nebyly zveřejněny žádné nabídky vyhledáním sloupce **Datum ztráty** v zákazníkovi ke stažení.


<a name="finding-more-help"></a>Hledání další nápovědy
-----------------

- [Definice přehledů prodejců](./si-insights-definitions-v4.md) – hledání definic pro metriky a data

- [Začínáme s přehledy prodejců](./si-getting-started.md) – Úvod do funkce Přehledy prodejců.

