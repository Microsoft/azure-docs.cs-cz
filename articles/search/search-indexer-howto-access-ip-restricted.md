---
title: Povolení přístupu k rozsahům IP adres indexeru
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte pravidla brány firewall protokolu IP tak, aby povolovala přístup k datům pomocí indexeru služby Azure Kognitivní hledání.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 01a88be9c54a2701130daace26c44159ee364e4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757239"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>Konfigurace pravidel brány firewall protokolu IP pro povolení indexerových připojení (Azure Kognitivní hledání)

Pravidla brány firewall protokolu IP v prostředcích Azure, jako jsou účty úložiště, Cosmos DB účty a servery SQL Azure, povolují pouze provoz pocházející z konkrétních rozsahů IP adres pro přístup k datům.

Tento článek popisuje, jak nakonfigurovat pravidla protokolu IP pomocí Azure Portal pro účet úložiště, aby mohli indexovací služby Azure Kognitivní hledání přistupovat k datům bezpečně. I když je tato metoda specifická pro Azure Storage, funguje taky pro další prostředky Azure, které používají pravidla brány firewall protokolu IP pro zabezpečení přístupu k datům.

> [!NOTE]
> Pravidla brány firewall protokolu IP pro účet úložiště platí jenom v případě, že je účet úložiště a vyhledávací služba v různých oblastech. Pokud to vaše instalace nepovoluje, doporučujeme použít [možnost výjimky důvěryhodné služby](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Získat IP adresu vyhledávací služby

Získejte plně kvalifikovaný název domény (FQDN) vaší vyhledávací služby. Bude vypadat nějak takto `<search-service-name>.search.windows.net` : Plně kvalifikovaný název domény můžete najít vyhledáním vyhledávací služby na Azure Portal.

   ![Získat plně kvalifikovaný název domény služby](media\search-indexer-howto-secure-access\search-service-portal.png "Získat plně kvalifikovaný název domény služby")

IP adresu vyhledávací služby lze získat pomocí `nslookup` (nebo `ping` ) plně kvalifikovaného názvu domény (FQDN). V následujícím příkladu byste přidali "150.0.0.1" do příchozího pravidla v bráně firewall Azure Storage. Může trvat až 15 minut, než se aktualizuje nastavení brány firewall pro indexer vyhledávací služby, aby mohl získat přístup k Azure Storagemu účtu.

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

Pro požadavky, které pocházejí z [prostředí pro spouštění s více klienty](search-indexer-securing-resources.md#indexer-execution-environment)indexeru, se používají další IP adresy. Tento rozsah IP adres můžete získat ze značky služby.

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

Nejjednodušší způsob, jak přidat rozsahy IP adres do pravidla brány firewall účtu úložiště, je prostřednictvím Azure Portal. Na portálu vyhledejte účet úložiště a přejděte na kartu **brány firewall a virtuální sítě** .

   ![Brána firewall a virtuální sítě](media\search-indexer-howto-secure-access\storage-firewall.png "Brána firewall a virtuální sítě")

Do rozsahu adres přidejte tři IP adresy, které jste dříve získali (1 pro IP adresu služby Search, 2 pro `AzureCognitiveSearch` značku služby), a vyberte **Uložit**.

   ![Pravidla IP adresy brány firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Pravidla IP adresy brány firewall")

Aktualizace pravidel brány firewall trvá 5-10 minut, po jejichž uplynutí by indexer měl mít přístup k datům v účtu úložiště.

## <a name="next-steps"></a>Další kroky

- [Konfigurace Azure Storage bran firewall](../storage/common/storage-network-security.md)
- [Konfigurace brány firewall protokolu IP pro Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Konfigurace brány firewall protokolu IP pro Azure SQL Server](../azure-sql/database/firewall-configure.md)