---
title: Získat informace o šifrovacím klíči
titleSuffix: Azure Cognitive Search
description: Načte název a verzi šifrovacího klíče, které se používají v indexu nebo v mapě synonym, abyste mohli klíč spravovat v Azure Key Vault.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: 37ff94608e9756142f70a4f3c64d0a6f7eeea685
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88932895"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>Získat informace o klíčích spravovaných zákazníkem z indexů a mapování synonym

V Azure Kognitivní hledání se šifrovací klíče spravované zákazníky vytvářejí, ukládají a spravují v Azure Key Vault. Pokud potřebujete zjistit, zda je objekt zašifrovaný nebo který byl použit jako název klíče nebo verze, použijte REST API nebo sadu SDK k načtení vlastnosti **EncryptionKey** z definice mapy v indexu nebo synonym. 

Doporučujeme [Povolit protokolování](../key-vault/general/logging.md) Key Vault, abyste mohli sledovat použití klíče.

## <a name="get-the-admin-api-key"></a>Získat klíč rozhraní API pro správu

Pokud chcete získat definice objektů z vyhledávací služby, musíte se ověřit s právy správce. Nejjednodušší způsob, jak získat klíč rozhraní API pro správu, je prostřednictvím portálu.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a otevřete stránku Přehled vyhledávací služby.

1. Na levé straně klikněte na **klíče** a zkopírujte rozhraní API pro správu. Pro načtení mapování indexů a synonym je vyžadován klíč správce.

V případě zbývajících kroků přepněte do PowerShellu a REST API. Na portálu se nezobrazují mapy synonym ani vlastnosti šifrovacího klíče indexů.

## <a name="use-powershell-and-rest"></a>Použití PowerShellu a REST

Spusťte následující příkazy, abyste nastavili proměnné a získali definice objektů.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Další kroky

Když teď víte, který šifrovací klíč a verzi se používá, můžete klíč spravovat v Azure Key Vault nebo kontrolovat další nastavení konfigurace.

+ [Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí PowerShellu](../key-vault/secrets/quick-create-powershell.md)

+ [Konfigurace klíčů spravovaných zákazníkem pro šifrování dat v Azure Kognitivní hledání](search-security-manage-encryption-keys.md)