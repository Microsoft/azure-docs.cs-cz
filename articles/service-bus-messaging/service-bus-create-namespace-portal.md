---
title: Vytvoření oboru názvů služby Service Bus na webu Azure Portal | Dokumentace Microsoftu
description: Vytvořte obor názvů služby Service Bus pomocí webu Azure Portal.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/29/2018
ms.author: spelluru
ms.openlocfilehash: 699129e9f75cce76d1682e3e2e2fb83d248ea92e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696021"
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Vytvoření oboru názvů služby Service Bus pomocí webu Azure Portal

Obor názvů je kontejner oboru pro všechny součásti zasílání zpráv. Součástí jednoho oboru názvů může být několik front a témat, přičemž obory názvů často slouží jako kontejnery aplikací. Obor názvů služby Service Bus je možné vytvořit dvěma způsoby:

1. Portál Azure Portal (tento článek)
2. [Šablony Resource Manageru][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Vytvoření oboru názvů na webu Azure Portal

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Blahopřejeme! Právě jste vytvořili obor názvů zasílání zpráv služby Service Bus.

## <a name="next-steps"></a>Další kroky

Podívejte se na [ukázky pro Service Bus na GitHubu][github-samples], které představují některé pokročilejší funkce zasílání zpráv Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
