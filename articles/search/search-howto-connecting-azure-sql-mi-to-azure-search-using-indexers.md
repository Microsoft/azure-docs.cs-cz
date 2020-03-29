---
title: Připojení spravované instance Azure SQL pro indexování vyhledávání
titleSuffix: Azure Cognitive Search
description: Povolit veřejný koncový bod povolit připojení k SQL spravované instance z indexeru na Azure Cognitive Search.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964885"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Konfigurace připojení z indexeru Azure Cognitive Search na spravovanou instanci SQL

Jak je uvedeno v [článku Připojení Azure SQL Database k Azure Cognitive Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), vytváření indexerů proti **spravovaným instancím SQL** je podporováno Azure Cognitive Search prostřednictvím veřejného koncového bodu.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Vytvoření spravované instance Azure SQL s veřejným koncovým bodem
Vytvořte spravovanou instanci SQL s vybranou možností **Povolit veřejný koncový bod.**

   ![Povolení veřejného koncového bodu](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Povolení veřejného koncového bodu")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Povolení veřejného koncového bodu spravované instance Azure SQL
Veřejný koncový bod můžete také povolit na existující spravované instanci SQL v části **Povolit** > veřejný**koncový bod** > sítě Security**Virtual** > **Network .**

   ![Povolení veřejného koncového bodu](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Povolení veřejného koncového bodu")

## <a name="verify-nsg-rules"></a>Ověření pravidel nsg
Zkontrolujte, zda skupina zabezpečení sítě má správná **příchozí pravidla zabezpečení,** která umožňují připojení ze služeb Azure.

   ![Pravidlo příchozího zabezpečení skupiny NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Pravidlo příchozího zabezpečení skupiny NSG")

> [!NOTE]
> Indexery stále vyžadují, aby byla spravovaná instance SQL nakonfigurována s veřejným koncovým bodem pro čtení dat.
> Můžete však omezit příchozí přístup k tomuto veřejnému koncovému bodu`public_endpoint_inbound`nahrazením aktuálního pravidla ( ) následujícími pravidly 2:
>
> * Povolení příchozího přístupu `AzureCognitiveSearch` z [výrobní ho dispozičního štítku](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("ZDROJ" = `AzureCognitiveSearch`"NAME" = `cognitive_search_inbound`)
>
> * Povolení příchozího přístupu z IP adresy vyhledávací služby, kterou lze získat příkazem ping `<your-search-service-name>.search.windows.net`na její plně kvalifikovaný název domény (např.). ("Zdroj" `IP address`= , "JMÉNO" = `search_service_inbound`)
>
> Pro každé z těchto 2 pravidel `3342`nastavte "PORT" `TCP`= , `Any`"PROTOCOL" = , "DESTINATION" = , "ACTION" =`Allow`

## <a name="get-public-endpoint-connection-string"></a>Získat připojovací řetězec veřejného koncového bodu
Ujistěte se, že používáte připojovací řetězec pro **veřejný koncový bod** (port 3342, nikoli port 1433).

   ![Připojovací řetězec veřejného koncového bodu](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Připojovací řetězec veřejného koncového bodu")

## <a name="next-steps"></a>Další kroky
S konfigurací z cesty, můžete teď zadat SQL spravované instance jako zdroj dat pro Indexer Azure Cognitive Search pomocí portálu nebo REST API. Další informace najdete [v tématu Připojení Azure SQL Database k Azure Cognitive Search pomocí indexerů.](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
