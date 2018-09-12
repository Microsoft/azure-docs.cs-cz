---
title: Omezení a výchozích hodnot
description: Omezení a výchozích hodnot
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4be0695402b66fdb2a027bfbada0e0b26e02d36f
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378716"
---
# <a name="scheduler-limits-and-defaults"></a>Omezení a výchozích hodnot
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Kvóty pro Scheduler, omezení, výchozí hodnoty a omezení
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Hlavička x-ms-request-id
Každý požadavek směřovaný služba Plánovač vrátí hlavičku odpovědi s názvem **x-ms-request-id**. Této hlavičky obsahuje neprůhledná hodnota, která jednoznačně identifikuje požadavek.

Pokud si ověříte, že žádost je správně formulovat žádost se opakovaně nedaří zálohovat, můžete použít tuto hodnotu hlásit chyby společnosti Microsoft. V sestavě zahrnují hodnotu x-ms-request-id, jak dlouho byla podána žádost, identifikátor předplatné, kolekci úloh nebo úlohy a typ operace, která k pokusu o žádost.

## <a name="see-also"></a>Viz také
 [Co je Scheduler?](scheduler-intro.md)

 [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)

 [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)

 [Plány a fakturace v Azure Scheduleru](scheduler-plans-billing.md)

 [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)

 [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)

 [Vysoká dostupnost a spolehlivost Azure Scheduleru](scheduler-high-availability-reliability.md)

 [Odchozí ověření Azure Scheduleru](scheduler-outbound-authentication.md)

