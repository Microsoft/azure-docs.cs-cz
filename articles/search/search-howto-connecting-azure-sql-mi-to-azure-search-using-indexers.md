---
title: Připojení ke službě Azure SQL Managed instance pro indexování vyhledávání
titleSuffix: Azure Cognitive Search
description: Povolením veřejného koncového bodu umožníte připojení ke spravovaným instancím SQL z indexeru v Azure Kognitivní hledání.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e8625724f67caac99ae799674f9db9399e11ad8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294250"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Konfigurace připojení ze služby Azure Kognitivní hledání indexer na spravovanou instanci SQL

Jak je uvedeno v části [připojení Azure SQL Database k Azure kognitivní hledání pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), vytváření indexerů pro **spravované instance SQL** podporuje Azure kognitivní hledání prostřednictvím veřejného koncového bodu.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Vytvoření spravované instance Azure SQL pomocí veřejného koncového bodu
Vytvořte spravovanou instanci SQL pomocí vybrané možnosti **Povolit veřejný koncový bod** .

   ![Povolit veřejný koncový bod](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Povolit veřejný koncový bod")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Povolit veřejný koncový bod spravované instance Azure SQL
V existující spravované instanci SQL můžete povolit také veřejný koncový bod v části **zabezpečení**  >  **Virtual network**  >  **Povolit veřejný koncový bod**virtuální sítě  >  **Enable**.

   ![Povolení veřejného koncového bodu pomocí virtuální sítě spravované instance](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Povolit veřejný koncový bod")

## <a name="verify-nsg-rules"></a>Ověřit pravidla NSG
Ověřte, že skupina zabezpečení sítě má správná **příchozí pravidla zabezpečení** , která umožňují připojení ze služeb Azure.

   ![NSG příchozí pravidlo zabezpečení](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG příchozí pravidlo zabezpečení")

> [!NOTE]
> Indexery stále vyžadují, aby byla nakonfigurovaná instance SQL nakonfigurovaná s veřejným koncovým bodem, aby bylo možné číst data.
> Můžete ale omezit příchozí přístup k tomuto veřejnému koncovému bodu tím, že nahradíte aktuální pravidlo ( `public_endpoint_inbound` ) následujícími 2 pravidly:
>
> * Povolení příchozího přístupu ze `AzureCognitiveSearch` [značky služby](../virtual-network/service-tags-overview.md#available-service-tags) ("zdroj" = " `AzureCognitiveSearch` název" = `cognitive_search_inbound` )
>
> * Povolení příchozího přístupu z IP adresy služby vyhledávání, která se dá získat pomocí příkazového testu pro plně kvalifikovaný název domény (např., `<your-search-service-name>.search.windows.net` ). ("zdroj" = `IP address` "Name" = `search_service_inbound` )
>
> U každého z těchto 2 pravidel nastavte "PORT" = `3342` , "protokol" = `TCP` , "cíl" = `Any` , "Action" = `Allow`

## <a name="get-public-endpoint-connection-string"></a>Získat připojovací řetězec veřejného koncového bodu
Ujistěte se, že používáte připojovací řetězec pro **veřejný koncový bod** (port 3342, ne port 1433).

   ![Připojovací řetězec veřejného koncového bodu](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Připojovací řetězec veřejného koncového bodu")

## <a name="next-steps"></a>Další kroky
S konfigurací ze systému teď můžete jako zdroj dat pro službu Azure Kognitivní hledání indexer zadat spravovanou instanci SQL, a to buď pomocí portálu, nebo REST API. Další informace najdete v tématu [připojení Azure SQL Database k Azure kognitivní hledání pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .