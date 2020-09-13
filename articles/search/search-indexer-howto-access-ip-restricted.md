---
title: Povolení přístupu k rozsahům IP adres indexeru
titleSuffix: Azure Cognitive Search
description: Návod, který popisuje, jak nastavit pravidla brány firewall protokolu IP, aby mohli indexery mít přístup.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463584"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>Nastavení pravidel brány firewall protokolu IP pro povolení přístupu indexeru

Pravidla brány firewall protokolu IP v prostředcích Azure, jako jsou účty úložiště, Cosmos DB účty a servery SQL Azure, povolují pouze provoz pocházející z konkrétních rozsahů IP adres pro přístup k datům.

Tento článek popisuje, jak nakonfigurovat pravidla protokolu IP pomocí Azure Portal pro účet úložiště, aby mohli indexovací služby Azure Kognitivní hledání přistupovat k datům bezpečně. I když je tato příručka specifická pro úložiště, je možné ji přímo přeložit na jiné prostředky Azure, které také nabízejí pravidla brány firewall protokolu IP pro zabezpečení přístupu k datům.

> [!NOTE]
> Pravidla brány firewall protokolu IP pro účet úložiště platí jenom v případě, že je účet úložiště a vyhledávací služba v různých oblastech. Pokud to vaše instalace nepovoluje, doporučujeme použít [možnost výjimky důvěryhodné služby](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Získat IP adresu vyhledávací služby

Získejte plně kvalifikovaný název domény (FQDN) vaší vyhledávací služby. Bude vypadat nějak takto `<search-service-name>.search.windows.net` : Plně kvalifikovaný název domény můžete najít vyhledáním vyhledávací služby na Azure Portal.

   ![Získat plně kvalifikovaný název domény služby](media\search-indexer-howto-secure-access\search-service-portal.png "Získat plně kvalifikovaný název domény služby")

IP adresu vyhledávací služby lze získat pomocí `nslookup` (nebo `ping` ) plně kvalifikovaného názvu domény (FQDN). To bude jedna z IP adres, které se mají přidat do pravidel brány firewall.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Získat rozsahy IP adres pro značku služby AzureCognitiveSearch

Rozsahy IP adres pro `AzureCognitiveSearch` značku služby lze získat buď prostřednictvím [rozhraní Discovery API (Preview)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) , nebo [souboru JSON ke stažení](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

Pro tento návod za předpokladu, že je vyhledávací služba ve veřejném cloudu Azure, je potřeba stáhnout [veřejný soubor JSON pro Azure](https://www.microsoft.com/download/details.aspx?id=56519) .

   ![Stáhnout soubor JSON](media\search-indexer-howto-secure-access\service-tag.png "Stáhnout soubor JSON")

V souboru JSON, za předpokladu, že je služba vyhledávání v Středozápadní USA, seznam IP adres pro prostředí pro spuštění indexeru pro více tenantů je uvedený níže.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Pro IP adresy/32 vyřaďte "/32" (52.253.133.74/32-> 52.253.133.74), které se dají použít i v doslovném znění.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Přidat rozsahy IP adres do pravidel brány firewall protokolu IP

Nejjednodušší způsob, jak přidat rozsahy IP adres do pravidla brány firewall účtu úložiště, je prostřednictvím Azure Portal. Na portálu vyhledejte účet úložiště a přejděte na kartu**brány firewall a virtuální sítě**.

   ![Brána firewall a virtuální sítě](media\search-indexer-howto-secure-access\storage-firewall.png "Brána firewall a virtuální sítě")

Do rozsahu adres přidejte tři IP adresy, které jste dříve získali (1 pro IP adresu služby Search, 2 pro `AzureCognitiveSearch` značku služby), a klikněte na**Uložit**.

   ![Pravidla IP adresy brány firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Pravidla IP adresy brány firewall")

Pravidla brány firewall zabírají 5-10 minut, aby se aktualizovaly po tom, kdy indexer budou mít přístup k datům v účtu úložiště.

## <a name="next-steps"></a>Další kroky

Když teď víte, jak získat dvě sady IP adres pro povolení přístupu k indexům, použijte následující odkazy, které aktualizují pravidla brány firewall protokolu IP pro některé běžné zdroje dat.

- [Konfigurace Azure Storage bran firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [Konfigurace brány firewall protokolu IP pro CosmosDB](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Konfigurace brány firewall protokolu IP pro Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)