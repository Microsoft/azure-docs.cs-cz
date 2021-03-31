---
title: Běžné parametry a záhlaví
description: Parametry a hlavičky společné pro všechny operace, které můžete provádět v souvislosti s Key Vault prostředky.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83005820"
---
# <a name="common-parameters-and-headers"></a>Běžné parametry a záhlaví

Následující informace jsou společné pro všechny operace, které můžete provádět v souvislosti s Key Vault prostředky:

- Hlavička protokolu HTTP `Host` musí být vždy přítomna a musí obsahovat název hostitele trezoru. Příklad: `Host: contoso.vault.azure.net`. Všimněte si, že většina technologií klienta naplní `Host` hlavičku z identifikátoru URI. V případě instance `GET https://contoso.vault.azure.net/secrets/mysecret{...}` nastaví `Host` jako `contoso.vault.azure.net` . To znamená, že pokud k Key Vault přistupujete pomocí nezpracované IP adresy `GET https://10.0.0.23/secrets/mysecret{...}` , jako je automatická hodnota `Host` hlavičky chybná, bude nutné ručně zkontrolovat, že `Host` Hlavička obsahuje název hostitele trezoru.
- Nahraďte `{api-version}` verzí API-Version v identifikátoru URI.
- Nahraďte `{subscription-id}` identifikátorem vašeho předplatného v identifikátoru URI.
- Nahraďte `{resource-group-name}` skupinou prostředků. Další informace najdete v článku Použití skupin prostředků ke správě prostředků Azure.
- Nahraďte `{vault-name}` názvem vašeho trezoru klíčů v identifikátoru URI.
- Nastavte hlavičku Content-Type na Application/JSON.
- Nastavte hlavičkou autorizace na JSON Web Token, kterou získáte z Azure Active Directory (AAD). Další informace najdete v tématu [ověřování žádostí Azure Resource Manager](authentication-requests-and-responses.md) .

## <a name="common-error-response"></a>Běžná chybová odpověď
Služba bude používat stavové kódy HTTP k indikaci úspěchu nebo selhání. Kromě toho chyby obsahují odpověď v následujícím formátu:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Název elementu | Typ | Popis |
|---|---|---|
| kód | řetězec | Typ chyby, ke které došlo.|
| zpráva | řetězec | Popis toho, co způsobilo chybu. |



## <a name="see-also"></a>Viz také
 [Odkaz na Azure Key Vault REST API](/rest/api/keyvault/)
