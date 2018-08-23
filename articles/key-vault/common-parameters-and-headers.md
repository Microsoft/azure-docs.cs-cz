---
title: Běžné parametry a záhlaví
description: Parametry a hlavičky, které jsou společné pro všechny operace, které lze provést související s prostředky služby Key Vault.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: a319dc670b5b1dab163b2d3aa623fc4fb9ce1c3a
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42054420"
---
# <a name="common-parameters-and-headers"></a>Běžné parametry a záhlaví

Následující informace jsou společné pro všechny operace, které lze provést související s prostředky služby Key Vault:

- Nahraďte `{api-version}` s api-version v identifikátoru URI.
- Nahraďte `{subscription-id}` identifikátorem předplatného v identifikátoru URI
- Nahraďte `{resource-group-name}` s vybranou skupinou prostředků. Další informace najdete v tématu Použití skupin prostředků ke správě prostředků Azure.
- Nahraďte `{vault-name}` s názvem služby key vault v identifikátoru URI.
- Nastavte hlavičku Content-Type na application/json.
- Nastavte hlavičku autorizace webového tokenu JSON, který získáte z Azure Active Directory (AAD). Další informace najdete v tématu [ověřování Azure Resource Manageru](authentication-requests-and-responses.md) požadavky.

## <a name="common-error-response"></a>Běžné chybové odpovědi
Služba bude používat stavové kódy HTTP indikuje úspěch nebo selhání. Kromě toho selhání obsahovat odpovědi v následujícím formátu:

   {  
     "Chyba": {  
     "kód": "Chybného požadavku",  
     "message": "sku trezoru klíčů je neplatný."  
     }  
   }  

|Název elementu | Typ | Popis |
|---|---|---|
| kód | řetězec | Typ chyby, ke které došlo.|
| zpráva | řetězec | Popis co způsobilo chybu. |



## <a name="see-also"></a>Viz také
 [Reference k REST API služby Azure Key Vault](/rest/api/keyvault/)
