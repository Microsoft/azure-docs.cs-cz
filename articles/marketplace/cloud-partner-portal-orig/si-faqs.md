---
title: Nejčastější dotazy k prodejci Insights
description: Nejčastější dotazy týkající se funkce prodávající přehledy portál partnerů cloudu.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285380"
---
<a name="seller-insights-faq"></a>Nejčastější dotazy k prodejci Insights
===================

Tento článek poskytuje pokyny pro běžné uživatelské postupy v rámci a otázky k přehledům prodejců.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Najít definice pro hodnoty ve staženém souboru transakcí
------------------------------------------------------------------

Definice hodnot metrik v transakčním souboru najdete v článku [definice přehledů prodejců](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Podívejte se na podrobnosti o transakcích, pro které jsem zaplatil
-------------------------------------------------------------

Po stažení transakcí z modulu výběr vyhledejte sloupec s popiskem **stav**vypsání a použijte filtr tak, aby zobrazil pouze hodnotu "vyplaceno". Zobrazí se následující sloupce obsahující údaje o zákaznících: **název společnosti**, **E-mail zákazníka**, **země zákazníka**, **zákaznická**adresa a **PSČ zákazníka**.


<a name="calculate-my-open-accounts-receivable"></a>Vypočítat moje otevřené účty pohledávky
-------------------------------------

Po stažení transakcí z modulu výběr vyhledejte sloupec s popiskem **stav**vypsání a použijte filtr tak, aby zobrazil pouze hodnotu nadcházející výběr a možnost není připravena na výběr. Pak načtěte součet sloupce s popiskem **množství (PC)**.


<a name="calculate-revenue-by-customer-usage-period"></a>Vypočítat Tržby podle období využití zákazníků
------------------------------------------

Po stažení transakcí z modulu výběr vyhledejte sloupec označený **stav transakce**a vyfiltrujte hodnotu placená.   U každé uvedené transakce představuje sloupec s popisem **(PC)** částku, kterou jste zaplatili.  Chcete-li odhadnout dobu využití spojenou s transakcí, použijte **Datum poplatku**za sloupec, což je přibližná aproximace posledního dne využití v období, na které se transakce vztahuje.


<a name="calculate-your-bad-debt"></a>Výpočet špatného dluhu
---------------------

Po stažení transakcí z modulu výběru Najděte sloupec označený jako **konečný stav kolekce**a použijte filtr pro zobrazení pouze hodnoty "zapisovat". Pak načtěte součet sloupce s popiskem **množství (PC)**.


<a name="view-payout-or-customer-contact-information"></a>Zobrazit výběr nebo kontaktní údaje zákazníka
-------------------------------------------

Přihlaste se jako uživatel s rolí vlastník a nikoli s rolí Přispěvatel. Jenom role vlastníka uvidí informace o vydaných a zákaznických údajích. Další informace o uživatelských rolích najdete v článku [Správa uživatelů](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Vypočítat výběry pro upřesnění
----------------------------

Po stažení transakcí z modulu výběr vyhledejte sloupec označený **Typ transakce**a použijte filtr tak, aby zobrazil pouze hodnotu "poplatek". Potom vyhledejte sloupec s názvem **konečný stav kolekce**a použijte filtr, aby se zobrazila pouze hodnota probíhá. Nakonec sestavte sloupec počet vydaných hodnot **(PC)** a vypočítejte všechny zálohy placené před kolekcí od zákazníka.


<a name="calculate-customer-refunds"></a>Vypočítat refundace zákazníků
--------------------------

Po stažení transakcí z modulu výběru Najděte sloupec označený jako **konečný stav kolekce**a použijte filtr pro zobrazení pouze hodnoty refundace. Chcete-li vypočítat všechny refundace pro zákazníky, sečtěte sloupec **množství poplatků (PC)** .


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Určit, které transakce se účastní partnera kanálu Microsoftu
----------------------------------------------------------------

Všechny transakce ve sloupci **typu licence Azure** , které jsou filtrovány tak, aby zobrazovaly hodnoty "Enterprise přes prodejce" a "poskytovatel řešení cloudu", zahrnují partnera kanálů Microsoftu. Další podrobnosti o partnerovi najdete v části o svém **názvu prodejce** a **e-mailu prodejce** ve stažení modulu výběr a stažení modulu zákazníka.


<a name="identify-trial-usage-and-trial-conversions"></a>Identifikace využití zkušební verze a převodů zkušební verze
------------------------------------------

Položky pořadí, využití a výběr modul pro stažení teď obsahují **zkušební datum ukončení zkušební verze** , které vám pomůže pochopit, kdy zkušební období skončilo pro příslušné pořadí, kde je to možné. Chcete-li zobrazit zkušební využití a objednávky, vyhledejte ve stažení sloupec **typ fakturace SKU** a použijte filtr pro zobrazení pouze hodnoty "zkušební verze". Chcete-li zobrazit převody zkušební verze, vyhledejte sloupec **Datum ukončení zkušební verze** ve stažení a použijte filtr tak, aby zobrazoval pouze objednávky, pokud je **Datum ukončení zkušební verze** dřívější než dnešní datum a sloupec **Datum zrušení** je prázdný nebo je pozdější než **Datum ukončení zkušební verze**.


<a name="when-is-my-monthly-payout-calculated"></a>Kdy se počítá můj měsíční výběr
------------------------------------

Vaše výběry vám vydávají 15. až 15 měsíců pro všechny částky připravené na výběr podle posledního kalendářního dne v předchozím měsíci. Třetí den v měsíci vypočítá společnost Microsoft hodnotu výběr v předchozím měsíci a aktualizuje všechny příslušné transakce poplatků ve vašem stažení se sloupcem výběr "nadcházející výběr" ve sloupci **stav** výpočtu. Tyto transakce zůstanou v tomto stavu, dokud se žádost o platbu nepošle na bankovní účet, ve kterém se **stav jeho výběrů** aktualizuje na "placené" a "datum vyčerpání" se aktualizuje, aby se zobrazilo datum odeslání žádosti o platbu do banky.


<a name="calculate-customer-acquisition-and-loss"></a>Vypočítat pořízení a ztrátu zákazníků
---------------------------------------

Datum, kdy zákazník poprvé koupil jednu z vašich nabídek, si můžete prohlédnout tak, že ve staženém zákazníkovi vyhledáte sloupec **Datum získání** . Podobně vidíte datum, po jehož uplynutí již žádná nabídka nebyla publikována, a to tak, že na stažení zákazníka vyhledáte sloupec **ztráty data** .


<a name="finding-more-help"></a>Hledání další nápovědě
-----------------

- [Definice přehledů prodejců](./si-insights-definitions-v4.md) – vyhledání metrik a dat

- [Začínáme s prodávajícími přehledy](./si-getting-started.md) – Úvod k funkci prodejce Insights.

