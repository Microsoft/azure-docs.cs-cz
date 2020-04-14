---
title: Principy fakturace Azure CDN | Dokumenty společnosti Microsoft
description: Tento faq popisuje, jak azure cdn fakturace funguje.
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
ms.openlocfilehash: d3a2dfba98f83d34c3e83ec865e3b692f7dbacd2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254219"
---
# <a name="understanding-azure-cdn-billing"></a>Principy fakturace Azure CDN

Tento faq popisuje strukturu fakturace pro obsah hostovaný Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Co je fakturační oblast?
Fakturační oblast je geografická oblast, která se používá k určení, jaká sazba se účtuje za doručení objektů z Azure CDN. Aktuální fakturační zóny a jejich oblasti jsou následující:

- Zóna 1: Severní Amerika, Evropa, Střední východ a Afrika

- Zóna 2: Asie a Tichomoří (včetně Japonska)

- Zóna 3: Jižní Amerika

- Zóna 4: Austrálie a Nový Zéland

- Zóna 5: Indie

Informace o oblastech bodu přítomnosti (POP) naleznete v [tématu Azure CDN POP umístění podle oblasti](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Například protokol POP umístěný v Mexiku se nachází v oblasti Severní Amerika a je proto zahrnut do zóny 1. 

Informace o cenách Azure CDN najdete v [tématu Ceny sítě doručování obsahu](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Jak se poplatky za doručení počítají podle oblasti?
Fakturační oblast Azure CDN je založená na umístění zdrojového serveru, který doručuje obsah koncovému uživateli. Cíl (fyzické umístění) klienta se nepovažuje za fakturační oblast.

Pokud například uživatel se sídlem v Mexiku vydá požadavek a tento požadavek je obsluhován serverem umístěným v protokolu POP ve Spojených státech z důvodu partnerského vztahu nebo provozních podmínek, bude fakturační oblastí Spojené státy.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Co je fakturovatelná transakce Azure CDN?
Jakýkoli požadavek HTTP(S), který končí v CDN, je fakturovatelná událost, která zahrnuje všechny typy odpovědí: úspěch, selhání nebo jiné. Různé odpovědi však mohou generovat různé částky provozu. Například *304 Not Modified* a jiné odpovědi pouze v záhlaví generují malý provoz, protože se jedná o malou odpověď záhlaví; podobně chybové odpovědi (například *404 Not Found)* jsou fakturovatelné, ale vynakládají malé náklady z důvodu malé užitečné části odezvy.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Jaké další náklady na Azure jsou spojené s používáním Azure CDN?
Použití Azure CDN také účtuje některé poplatky za využití na služby používané jako původ pro vaše objekty. Tyto náklady jsou obvykle malý zlomek celkových nákladů na využití CDN.

Pokud používáte úložiště objektů Blob Azure jako původ pro váš obsah, vám také vznikají následující poplatky za úložiště pro vyplňování mezipaměti:

- Skutečné použité GB: Skutečné úložiště zdrojových objektů.

- Transakce: Podle potřeby k vyplnění mezipaměti.

- Přenosy v GB: Množství dat přenesených k vyplnění mezipamětí CDN.

> [!NOTE]
> Od října 2019, Pokud používáte Azure CDN od Microsoftu, náklady na přenos dat z Origins hostované v Azure na CDN popy je zdarma. Azure CDN od Verizonu a Azure CDN od Akamai podléhají sazbám popsaným níže.

Další informace o fakturaci úložiště Azure najdete [v tématu Principy fakturace úložiště Azure – šířka pásma, transakce a kapacita](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Pokud používáte *hostované poskytování služeb*, budou vám účtovány poplatky následujícím způsobem:

- Výpočetní čas Azure: Výpočetní instance, které fungují jako původ.

- Převod výpočetních prostředků Azure: Přenosy dat z výpočetních instancí k vyplnění mezipamětí Azure CDN.

Pokud váš klient používá požadavky na rozsah bajtů (bez ohledu na službu původu), platí následující požadavky:

- *Požadavek na rozsah bajtů* je fakturovatelná transakce v cdn. Když klient vydá požadavek na rozsah bajtů, tento požadavek je pro podmnožinu (rozsah) objektu. CDN odpoví pouze částečnou část obsahu, který je požadován. Tato částečná odpověď je fakturovatelná transakce a částka převodu je omezena na velikost odezvy rozsahu (plus hlavičky).

- Když požadavek dorazí pouze část objektu (zadáním hlavičky rozsahu bajtů), CDN může načíst celý objekt do mezipaměti. V důsledku toho i v případě, že fakturovatelná transakce z CDN je pro částečnou odpověď, fakturovatelné transakce z počátku může zahrnovat plnou velikost objektu.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Kolik přenosové aktivity dochází k podpoře mezipaměti?
Pokaždé, když cdn POP potřebuje vyplnit svou mezipaměť, provede požadavek na původ objektu, který je uložen do mezipaměti. V důsledku toho vznik vznik unese fakturovatelné transakce na každé chybě mezipaměti. Počet neúspěšných pokusů o dolet závisí na řadě faktorů:

- Jak cache obsah je: Pokud obsah má vysoké Hodnoty TTL (time-to-live) /expirace a je často přistupovat, takže zůstane populární v mezipaměti, pak drtivá většina zatížení je zpracována CDN. Typický dobrý poměr přístupů do mezipaměti je více než 90 %, což znamená, že méně než 10 % požadavků klientů se musí vrátit do původního stavu, a to buď pro nedoletovou chybě, nebo pro aktualizaci objektu.

- Kolik uzlů je třeba načíst objekt: Pokaždé, když uzel načte objekt z počátku, vznikne fakturovatelné transakce. Výsledkem je, že více globálního obsahu (přístupného z více uzlů) vede k více fakturovatelným transakcím.

- Vliv TTL: Vyšší TTL pro objekt znamená, že je třeba načíst z počátku méně často. To také znamená, že klienti, jako jsou prohlížeče, mohou objekt ukládat do mezipaměti déle, což může snížit transakce na CDN.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Které služby původu jsou způsobilé pro bezplatný přenos dat pomocí Azure CDN od Microsoftu? 
Pokud jako původ CDN použijete některou z následujících služeb Azure, nebude se vám z přenosu dat z originu do přístupových polí CDN účtovat. 

- Azure Storage
- Mediální služby Azure
- Azure Virtual Machines
- Virtual Network
- Load Balancer
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Brána Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Služba Azure App
- Azure Functions
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Průzkumník dat Azure
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Databáze Azure SQL
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Jak mohu spravovat své náklady co nejefektivněji?
Nastavte u svého obsahu co nejdelší TTL. 
