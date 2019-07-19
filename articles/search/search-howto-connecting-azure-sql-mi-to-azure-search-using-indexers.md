---
title: Připojení ke službě Azure SQL Managed instance pro indexování vyhledávání – Azure Search
description: Povolte veřejný koncový bod, aby bylo možné povolit připojení ke spravovaným instancím SQL z indexeru na Azure Search.
author: vl8163264128
manager: briansmi
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: ae947e85f600b3bc380898ad5820239aa823794d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229125"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Konfigurace připojení z Azure Search indexeru na spravovanou instanci SQL
Jak je uvedeno v části [připojení Azure SQL Database k Azure Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), vytváření indexerů pro **spravované instance SQL** podporuje Azure Search prostřednictvím veřejného koncového bodu.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Vytvoření spravované instance Azure SQL pomocí veřejného koncového bodu
Vytvořte spravovanou instanci SQL pomocí vybrané možnosti **Povolit veřejný koncový bod** .

   ![Povolit veřejný koncový bod](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Povolit veřejný koncový bod")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Povolit veřejný koncový bod spravované instance Azure SQL
V existující spravované instanci SQL můžete povolit také veřejný koncový bod v části **zabezpečení** > **Povolit** **veřejný koncový bod** > **virtuální sítě** > .

   ![Povolit veřejný koncový bod](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Povolit veřejný koncový bod")

## <a name="verify-nsg-rules"></a>Ověřit pravidla NSG
Ověřte, že skupina zabezpečení sítě má správná **příchozí pravidla zabezpečení** , která umožňují připojení ze služeb Azure.

   ![NSG příchozí pravidlo zabezpečení](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG příchozí pravidlo zabezpečení")

## <a name="get-public-endpoint-connection-string"></a>Získat připojovací řetězec veřejného koncového bodu
Ujistěte se, že používáte připojovací řetězec pro **veřejný koncový bod** (port 3342, ne port 1433).

   ![Připojovací řetězec veřejného koncového bodu](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Připojovací řetězec veřejného koncového bodu")

## <a name="next-steps"></a>Další postup
V případě konfigurace ze systému teď můžete určit spravovanou instanci SQL jako zdroj dat pro Azure Search indexer pomocí portálu nebo REST API. Další informace najdete v tématu [připojení Azure SQL Database k Azure Search pomocí indexerů](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
