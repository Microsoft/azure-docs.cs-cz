---
title: Principy fakturace Azure CDN | Dokumentace Microsoftu
description: Tyto nejčastější dotazy popisuje, jak funguje fakturace Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: af8e57f39b5b83b1d1be09c29d8b6eb5d49c7b6c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309262"
---
# <a name="understanding-azure-cdn-billing"></a>Principy fakturace Azure CDN

Tyto nejčastější dotazy popisuje fakturační strukturu pro obsah hostovaný v Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Co je fakturační oblasti?
Fakturační oblasti je geografické oblasti, která umožňuje určit, jaké sazba se účtuje za doručování objektů z Azure CDN. Aktuální fakturační zóny a oblasti jsou následující:

- Zóna 1: Severní Amerika, Evropa, Střední východ a Afrika

- Zóna 2: Asie a Tichomoří (včetně Japonska)

- Zóna 3: Jižní Amerika

- Zóna 4: Austrálie a Nový Zéland

- Zóna 5: Indie

Informace o oblastech point-of-presence (POP) najdete v tématu [místa POP sítě CDN Azure podle oblasti](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Například pomocí protokolu POP umístěný v Mexiku je v oblasti Severní Amerika a je proto zahrnuty v zóně 1. 

Informace o cenách Azure CDN najdete v tématu [ceny Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Jak se počítají poplatky doručování podle oblasti?
Oblast fakturace Azure CDN je založena na umístění zdrojového serveru, doručování obsahu koncovému uživateli. Cíl (fyzické umístění) klienta se nepovažuje za fakturační oblasti.

Například pokud uživatel nachází v Mexiku vydá požadavek na a tato žádost obsloužena server umístěný v USA POP z důvodu podmínek partnerský vztah nebo provoz, bude fakturační oblasti USA.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Co je fakturovatelná transakce Azure CDN?
Jakékoli požadavky HTTP (S), které končí CDN jednotka představuje účtovatelnou událost, která zahrnuje všechny typy odpovědi: Úspěch, selhání nebo jiné. Různé odpovědi však může generovat částky různý provoz. Například *304 nedojde ke změně* a ostatní pouze hlavičky odpovědi generovat malý provoz, protože jsou malé hlavičky odpovědi; podobně chybové odpovědi (například *404 Nenalezeno*) se účtují ale malé zpoplatněné kvůli datové části odpovědi malý.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Jaké další náklady na Azure jsou spojeny s využitím Azure CDN?
Použití Azure CDN se účtují taky využití poplatky za služby využité jako zdroj pro objekty. Tyto náklady jsou obvykle malá část celkové náklady na využití CDN.

Pokud používáte Azure Blob storage jako zdroj pro obsah, také účtovat následující poplatky za úložiště pro mezipaměť výplně:

- Skutečné využité GB: Skutečnou velikost úložiště vaší zdrojové objekty.

- Přenosy v GB: Množství dat přenášených tak, aby vyplnil mezipaměti CDN.

- Transakce: Podle potřeby mezipaměť zaplní.

Další informace o fakturaci Azure Storage najdete v tématu [Principy Azure Storage fakturace – šířka pásma, transakce a kapacita](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Pokud používáte *hostovaná služba doručování*, budou účtovat poplatky následujícím způsobem:

- Výpočetní čas: Výpočetní instance, které se chovají jako zdroj.

- Přenos výpočetní prostředky Azure: Data se přenáší z výpočetní instance tak, aby vyplnil mezipaměti Azure CDN.

Pokud klient používá požadavky na zjištění rozsahu bajtů (bez ohledu na původ služby), platí následující aspekty:

- A *požadavek na zjištění rozsahu bajtů* je fakturovatelných transakcí v CDN. Když klient vydá požadavek na rozsah bajtů, tento požadavek je pro podmnožinu (oblast) objektu. CDN odpoví jenom částečné část obsahu, který je požadovaný. Tato odpověď s částečným je fakturovatelných transakcí a velikost přenosu je omezena na velikost odpovědi v rozsahu (včetně záhlaví).

- Když žádost odchází pouze část objektu (zadáním hlavičky byte-range), CDN může načíst celý objekt do mezipaměti. I když fakturovatelných transakcí z CDN je pro částečné odpovědi, může zahrnovat fakturovatelných transakcí z původního zdroje v důsledku toho plnou velikost objektu.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Nastane, kolik aktivit přenosu pro podporu mezipaměti?
Pokaždé, když potřebuje CDN POP tak, aby vyplnil své mezipaměti odešle požadavek na původní název pro objekt do mezipaměti. V důsledku toho původ s sebou nese náklady fakturovatelných transakcí na každé Neúspěšné přístupy do mezipaměti. Počet nezdařených přístupů k mezipaměti závisí na několika faktorech:

- Jak lze uložit do mezipaměti obsah je: Pokud obsah obsahuje vysoké hodnoty TTL (time-to-live) / vypršení platnosti hodnoty a je často přistupuje, takže zůstane v mezipaměti, bude většinu zatížení oblíbených zařizuje služba CDN. Typické dobrý poměr přístupů do mezipaměti se že více než 90 %, což znamená, že méně než 10 % požadavky klientů se muset vrátit na původní název, buď pro Neúspěšné přístupy do mezipaměti nebo objekt aktualizovat.

- Kolik uzlů je potřeba načíst objekt: Pokaždé, když uzel služeb načte objekt z původního zdroje, budou vám účtovány fakturovatelných transakcí. V důsledku toho víc globálních obsah (získat přístup z více uzlů) má za následek více fakturovatelných transakcí.

- Hodnota TTL vliv: Vyšší hodnota TTL pro objekt znamená, že je potřeba načítat ze zdroje méně často. Také znamená, že klienti, jako jsou prohlížeče, může ukládat do mezipaměti déle, objekt, který může snížit transakce do CDN.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Jak můžu nejefektivněji spravovat náklady?
Nastavte interval TTL, ZÍSKÁ nejdelší možné obsahu. 
