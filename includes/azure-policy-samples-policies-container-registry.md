---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 6497a1444b3f502d3e5169ff8ace2884e4e045c9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758688"
---
|Name (Název) |Popis |Efekty |Version |GitHubu |
|---|---|---|---|---|
|[Registry kontejnerů by měly být šifrovány pomocí klíče spravovaného zákazníkem (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Registry kontejnerů auditu, které nemají šifrování povolené pomocí klíčů spravovaných zákazníkem (CMK). Pro více informací o šifrování CMK navštivte: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, zakázáno |1.0.0-náhled |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Registrů kontejnerů by neměly umožňovat neomezený přístup k síti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Registry kontejnerů auditu, které nemají nakonfigurovaná žádná pravidla sítě (IP nebo VNET) a ve výchozím nastavení umožňují veškerý přístup k síti. Registry kontejnerů s alespoň jedním pravidlem IP / firewallu nebo nakonfigurovanou virtuální sítí budou považovány za kompatibilní. Další informace o pravidlech sítě registru [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)kontejnerů naleznete na adrese: . |Audit, zakázáno |1.0.0-náhled |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
