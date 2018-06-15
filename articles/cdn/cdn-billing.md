---
title: Seznámení s Azure CDN fakturace | Microsoft Docs
description: Tyto nejčastější dotazy popisuje, jak funguje Azure CDN fakturace.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: v-deasim
ms.openlocfilehash: 218c493c772dc8fd212efaf60a0599fa2e896411
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163515"
---
# <a name="understanding-azure-cdn-billing"></a>Principy fakturace Azure CDN

Tyto nejčastější dotazy popisuje strukturu fakturace pro obsahu, jehož hostitelem pomocí sítě pro doručování obsahu Azure (CDN).

## <a name="what-is-a-billing-region"></a>Co je fakturace oblasti?
Fakturace oblast je geografické oblasti umožňuje určit, jaké míry je účtován pro doručení objektů z Azure CDN. Aktuální fakturace zóny a jejich oblasti jsou následující:

- Zóny 1: Severní Americe, Evropa, Střední východ a Afrika

- Zóna 2: Asie a Tichomoří (včetně Japonska)

- Zóna 3: Jižní Amerika

- Zóny 4: Austrálie a Nový Zéland

- Zóna 5: Indie

Informace o oblastech point of presence (POP) najdete v tématu [umístění Azure CDN POP podle oblasti](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Například POP, umístěný v Mexico v oblast Severní Ameriky a je proto součástí zóny 1. 

Informace o cenách Azure CDN najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/is-is/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Jak jsou vypočítávány doručení poplatky podle oblasti?
Fakturace oblasti Azure CDN je založena na umístění na zdrojovém serveru doručování obsahu pro koncového uživatele. Cíl (fyzické umístění) klienta není považováno za fakturační oblast.

Například pokud uživatel nachází v Mexico vydá požadavek a tento požadavek je obsloužených serverem, který se nachází v USA POP kvůli podmínek partnerský vztah nebo provozu, bude fakturace oblasti USA.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Co je fakturovatelná transakce Azure CDN?
Každá žádost HTTP (S), který končí na CDN je fakturovatelný událost, která obsahuje všechny typy odpovědi: Úspěch, selhání nebo jiné. Různé odezvy však může generovat objemy jiný přenos. Například *nedojde ke změně 304* a jiné reakce pouze záhlaví vytvářet malé přenosy dat, protože jsou malá odpověď záhlaví; podobně chybové odpovědi (například *404 nebyl nalezen*) jsou fakturovatelného času ale způsobit malé náklady kvůli datové části malá velikost odpovědi.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Jaké Azure náklady jsou přidruženy použití Azure CDN?
Pomocí Azure CDN způsobuje také některé poplatky za používání v rámci služeb používá jako počátek pro vaše objekty. Tyto náklady jsou obvykle malá část celkové náklady na používání CDN.

Pokud používáte úložiště objektů Blob v Azure jako počátek pro obsah, také platit následující poplatky úložiště pro výplně mezipaměti:

- Využité GB skutečná: skutečný úložiště zdrojové objekty.

- Přenosy v GB: množství dat přenášených k vyplnění mezipamětí CDN.

- Transakce: podle potřeby k vyplnění mezipaměti.

Další informace o fakturaci Azure Storage najdete v tématu [Principy Azure úložiště fakturace – šířku pásma, transakce a kapacity](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Pokud používáte *hostované služby doručení*, bude platit poplatky následujícím způsobem:

- Výpočetní čas: výpočetní instance, které fungují jako počátek.

- Přenos výpočtů Azure: přenosy dat z výpočetních instancích k vyplnění mezipamětí Azure CDN.

Pokud váš klient používá žádosti o rozsah bajtů (bez ohledu na původ service), platí následující aspekty:

- A *žádost o rozsah bajtů* je fakturovatelná transakce v CDN. Když klient vydá žádost o rozsah bajtů, tento požadavek je pro podmnožinu (oblast) objektu. CDN odpoví částečné část obsahu, který je požadovaný. Toto částečné odpovědi fakturovatelný transakce a velikost přenosu je omezená na velikost odpovědi v rozsahu (včetně záhlaví).

- Pokud požadavek dorazí pro jenom část objektu (zadáním hlavičku rozsah bajtů), může CDN načíst celý objekt do své mezipaměti. Výsledkem je i když fakturovatelný transakce od CDN je pro částečné odpověď, fakturovatelný transakce z tohoto počátku může zahrnovat plná velikost objektu.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Kolik aktivity přenosu dojde k podporu mezipaměti?
Pokaždé, když CDN POP potřebuje k vyplnění své mezipaměti počátek objekt mezipaměti se odešle požadavek. V důsledku toho počátek způsobuje fakturovatelný transakce na každý neúspěšných přístupů do mezipaměti. Počet nezdařených přístupů k mezipaměti závisí na počtu faktorů:

- Jak lze uložit do mezipaměti obsah je: Pokud obsah má vysokou hodnotu TTL (time-to-live) / vypršení platnosti hodnoty a je využívají často tak zůstane v mezipaměti, bude většina zatížení oblíbených se zpracovává souborem CDN. Typické dobrý poměr přístupů do mezipaměti je dobře že víc než 90 %, což znamená, že méně než 10 % požadavky klientů se vrátit do počátek, buď pro neúspěšných přístupů do mezipaměti nebo objekt aktualizovat.

- Kolik uzly potřebovat načíst objekt: pokaždé, když uzel načte objekt z tohoto počátku způsobuje fakturovatelný transakce. V důsledku toho globálnější obsah (přístup z více uzlů) výsledkem více fakturovatelný transakce.

- Vliv TTL: znamená vyšší hodnota TTL pro objekt ji je třeba načíst z tohoto počátku méně často. Taky to znamená, že klienti, jako jsou prohlížeče, může ukládat do mezipaměti víc, objekt, který může snížit transakcí, které chcete CDN.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Jak lze nejvíce efektivně spravovat Moje náklady?
Nastavte interval TTL, ZÍSKÁ nejdelší možné na obsah. 
