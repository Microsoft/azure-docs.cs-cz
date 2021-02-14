---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 09cf8985d6a123cb5b99ee216c9dfd55c296cf48
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095391"
---
|Název<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Služba signalizace Azure by měla používat privátní odkaz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Privátní odkaz Azure umožňuje připojit virtuální síť ke službám Azure bez veřejné IP adresy ve zdroji nebo cíli. Platforma privátních odkazů zpracovává připojení mezi klientem a službami přes páteřní síť Azure. Mapováním privátních koncových bodů na prostředky signalizace namísto celé služby budete chráněni i proti rizikům úniku dat. Další informace najdete tady: [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink) . |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
