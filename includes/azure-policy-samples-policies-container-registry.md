---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: a1b12a72434034ecc6cbe527cc3de6454e4293a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79381549"
---
|Name (Název) |Popis |Efekty |Version |GitHub |
|---|---|---|---|---|
|[Registry kontejnerů by měly být šifrovány pomocí klíče spravovaného zákazníkem (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Registry kontejnerů auditu, které nemají šifrování povolené pomocí klíčů spravovaných zákazníkem (CMK). Pro více informací o šifrování CMK navštivte: https://aka.ms/acr/CMK. |Audit, zakázáno |1.0.0-náhled |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Registrů kontejnerů by neměly umožňovat neomezený přístup k síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Registry kontejnerů auditu, které nemají nakonfigurovaná žádná pravidla sítě (IP nebo VNET) a ve výchozím nastavení umožňují veškerý přístup k síti. Registry kontejnerů s alespoň jedním pravidlem IP / firewallu nebo nakonfigurovanou virtuální sítí budou považovány za kompatibilní. Další informace o pravidlech sítě registru https://aka.ms/acr/vnetkontejnerů naleznete na adrese: . |Audit, zakázáno |1.0.0-náhled |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
