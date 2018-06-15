---
title: Společné parametry a hlavičky
description: Parametry a hlavičky, které jsou společné pro všechny operace, které může provádět týkající se prostředků Key Vault.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: ead1ac550c9b7c489edefd35d5672a9955e78255
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012068"
---
# <a name="common-parameters-and-headers"></a>Společné parametry a hlavičky

Tyto informace jsou společná pro všechny operace, které může provádět týkající se prostředků Key Vault:

- Nahraďte `{api-version}` verzí rozhraní api v identifikátoru URI.
- Nahraďte `{subscription-id}` s ID vašeho předplatného v identifikátoru URI
- Nahraďte `{resource-group-name}` se skupinou prostředků. Další informace najdete v tématu Použití skupin prostředků ke správě prostředků Azure.
- Nahraďte `{vault-name}` s název trezoru klíčů v identifikátoru URI.
- Nastavte hlavičku Content-Type na application/json.
- Nastavte hlavičku autorizace webového tokenu JSON, kterou získáte z Azure Active Directory (AAD). Další informace najdete v tématu [ověřování Azure Resource Manager](authentication-requests-and-responses.md) požadavky.

## <a name="common-error-response"></a>Běžné chybové odpovědi
Služba bude používat stavové kódy HTTP indikující úspěch nebo selhání. Kromě toho selhání obsahovat odpověď v následujícím formátu:

   {  
     "Chyba": {  
     "kód": "Struktura BadRequest",  
     "zpráva": "trezor klíčů sku je neplatný."  
     }  
   }  

|Název elementu | Typ | Popis |
|---|---|---|
| Kód | řetězec | Typ chyby, která došlo k chybě.|
| zpráva | řetězec | Popis co způsobilo chybu. |



## <a name="see-also"></a>Viz také
 [Referenční dokumentace rozhraní API REST Azure Key Vault](/rest/api/keyvault/)
