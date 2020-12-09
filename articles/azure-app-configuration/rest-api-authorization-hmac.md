---
title: Konfigurace aplikací Azure REST API – ověřování HMAC
description: Použití HMAC k autorizaci pro konfiguraci aplikací Azure pomocí REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3746fcf06aea48c9c80696b634d6323b9cb21822
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932655"
---
# <a name="hmac-authorization---rest-api-reference"></a>Ověřování HMAC – REST API Reference

Je-li použito ověřování HMAC, operace spadají do jedné ze dvou kategorií, čtení nebo zápis. Přístupové klíče pro čtení i zápis udělují oprávnění k volání všech operací. Přístupové klíče jen pro čtení udělují oprávnění k volání pouze operací čtení. Zda je přístupová klávesa jen pro čtení nebo čtení i zápis, závisí na její `readOnly` Vlastnosti. Všechny pokusy o vytvoření žádosti o zápis pomocí přístupového klíče jen pro čtení budou mít za následek neoprávněnou žádost.

## <a name="obtaining-access-keys"></a>Získání přístupových klíčů

Specifikace popisů přístupových klíčů a rozhraní API používaných k jejich získání je podrobně popsána v tématu Specifikace poskytovatele prostředků konfigurace aplikace Azure [zde](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json). Přístupové klíče se získávají prostřednictvím operace "ConfigurationStores_ListKeys".

## <a name="errors"></a>Chyby

```http
HTTP/1.1 403 Forbidden
```

**Důvod:** Přístupový klíč, který se používá k ověření žádosti, neposkytuje požadovaná oprávnění k provedení požadované operace.
**Řešení:** Získejte přístupový klíč, který poskytuje oprávnění k provedení požadované operace a jeho použití k ověření žádosti.
