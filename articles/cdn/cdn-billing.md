---
title: Princip fakturace Azure CDN | Microsoft Docs
description: Seznamte se s fakturační strukturou obsahu hostovaného službou Azure Content Delivery Network, včetně fakturačních oblastí, poplatků za doručení a správy nákladů.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: allensu
ms.openlocfilehash: aa2f00a732a3978524fc017481285859c9535387
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018677"
---
# <a name="understanding-azure-cdn-billing"></a>Principy fakturace Azure CDN

V této části najdete popis fakturační struktury obsahu hostovaného službou Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Co je fakturační oblast?
Oblast fakturace je geografická oblast sloužící k určení, jakou sazbou se účtují poplatky za doručování objektů z Azure CDN. Aktuální fakturační zóna a jejich oblasti jsou následující:

- Zóna 1: Severní Amerika, Evropa, Střední východ a Afrika

- Zóna 2: Asie a Tichomoří (včetně Japonska)

- Zóna 3: Jižní Amerika

- Zóna 4: Austrálie a Nový Zéland

- Zóna 5: Indie

Informace o oblastech bodu přítomnosti (POP) najdete v tématu [Azure CDN umístění pop podle oblasti](./cdn-pop-locations.md). Například bod POP umístěný v Mexiku je v oblasti Severní Amerika a je proto zahrnutý v zóně 1. 

Informace o cenách Azure CDN najdete v článku [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Jak se účtují poplatky za doručování podle oblasti?
Oblast fakturace Azure CDN je založena na umístění zdrojového serveru, který doručuje obsah koncovému uživateli. Cíl (fyzické umístění) klienta se nepovažuje za fakturační oblast.

Pokud například uživatel umístěný v Mexiku vydá požadavek a tato žádost je obsluhovaná serverem umístěným v USA POP kvůli podmínkám partnerských vztahů nebo přenosů, bude USA oblastí pro fakturaci.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Co je fakturovatelná Azure CDN transakce?
Každá žádost HTTP (S), která končí v CDN, je fakturovatelná událost, která zahrnuje všechny typy odpovědí: úspěch, selhání nebo jiné. Různé odezvy ale můžou vygenerovat různé objemy provozu. Například *304 Nezměněno* a jiné odpovědi pouze hlavičky generují malý provoz, protože se jedná o malou odpověď hlavičky; Podobně se neúčtují odpovědi na chyby (například *404, které se nenašly*), ale z důvodu datové části s malou odezvou se účtují malé náklady.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Jaké další náklady na Azure jsou spojené s Azure CDN používání?
Při použití Azure CDN se také vyskytnou poplatky za využití služeb, které se používají jako původ vašich objektů. Tyto náklady obvykle představují malou část celkových nákladů na využití CDN.

Pokud jako zdroj obsahu používáte službu Azure Blob Storage, účtují se také následující poplatky za úložiště pro ukládání do mezipaměti:

- Využité skutečné GB: skutečné úložiště vašich zdrojových objektů.

- Transakce: podle potřeby můžete mezipaměť vyplnit.

- Přenosy v GB: množství dat přenesených do vyplňování mezipamětí CDN.

> [!NOTE]
> Od října 2019 platí, že pokud používáte Azure CDN od Microsoftu, účtují se náklady na přenos dat ze zdrojů hostovaných v Azure do CDN pop. Azure CDN z Verizon a Azure CDN z Akamai podléhají sazbám popsaným níže.

Další informace o fakturaci Azure Storage najdete v tématu [porozumění Azure Storage fakturace – šířka pásma, transakce a kapacita](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Pokud používáte *doručování hostované služby*, účtují se vám poplatky následujícím způsobem:

- Výpočetní čas Azure: výpočetní instance, které fungují jako původ.

- Azure COMPUTE Transfer: přenos dat z výpočetních instancí pro vyplňování mezipamětí Azure CDN.

Pokud váš klient používá požadavky na rozsah bajtů (bez ohledu na počáteční službu), platí následující požadavky:

- *Požadavek na rozsah bajtů* je fakturovatelná transakce v CDN. Když klient vydá požadavek na rozsah bajtů, je tento požadavek určen pro podmnožinu (rozsah) objektu. CDN odpoví jenom na částečnou část obsahu, který je požadován. Tato částečná odpověď je fakturovatelná transakce a velikost přenosů je omezená na velikost odpovědi rozsahu (plus hlavičky).

- Když žádost dorazí jenom na část objektu (zadáním hlavičky rozsahu bajtů), CDN může načíst celý objekt do své mezipaměti. V důsledku toho platí, že i když Fakturovatelné transakce z CDN je pro částečnou odpověď, fakturovatelná transakce od počátku může zahrnovat celou velikost objektu.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>K kolika aktivit přenosů dochází pro podporu mezipaměti?
Pokaždé, když POP POP potřebuje vyplnit svou mezipaměť, vytvoří požadavek na počátek objektu v mezipaměti. V důsledku toho původ při každé neúspěšné operaci ukládání do mezipaměti zaznamená fakturovatelnou transakci. Počet neúspěšných přístupů do mezipaměti závisí na několika faktorech:

- Jak obsah ukládat do mezipaměti: Pokud má obsah vysoké hodnoty TTL (Time-to-Live)/Expiration a často se k němu přistupoval, aby byl v mezipaměti oblíbený, pak je velká většina zatížení zpracována přes CDN. Typický dobrý poměr přístupů do mezipaměti je dobře větší než 90%, což znamená, že méně než 10% požadavků klientů se musí vrátit ke zdroji, a to buď v případě neúspěšného pokusu o ukládání do mezipaměti, nebo obnovení objektu.

- Kolik uzlů potřebuje načíst objekt: pokaždé, když uzel načte objekt ze zdroje, dojde k transakci Fakturovatelné. Výsledkem je, že více globálních obsahu (ke kterému se dostanete z více uzlů) má za následek Fakturovatelné transakce.

- Vliv TTL: vyšší hodnota TTL pro objekt znamená, že musí být načtena z původu méně často. Také to znamená, že klienti, jako jsou například prohlížeče, mohou objekt ukládat do mezipaměti, což může snížit transakce do sítě CDN.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Které služby původu mají nárok na bezplatné přenosy dat s Azure CDN od Microsoftu? 
Pokud jako zdroj CDN použijete jednu z následujících služeb Azure, nebudete se účtovat z přenosu dat od počátku do bodů POP CDN. 

- Azure Storage
- Azure Media Services
- Azure Virtual Machines
- Virtual Network
- Load Balancer
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Služba Azure App Service
- Azure Functions
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Průzkumník dat Azure
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL Database
- Spravovaná instance Azure SQL
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Návody spravovat mé náklady efektivněji?
Nastavte nejdelší možnou hodnotu TTL u vašeho obsahu.