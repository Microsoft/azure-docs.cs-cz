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
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23867812"
---
# <a name="scheduler-limits-and-defaults"></a>Omezení a výchozích hodnot
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Scheduler kvóty, omezení, výchozí hodnoty a omezení
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Hlavička x-ms-request-id
Každý požadavek směřovaný na službu Plánovač vrátí hlavičky odpovědi s názvem**x-ms-request-id**. Tuto hlavičku obsahuje neprůhledné hodnoty, která jednoznačně identifikuje požadavek.

Pokud požadavek konzistentně selhává a ověříte, že je správně formulovali požadavek, můžete používat tuto hodnotu ohlaste chybu společnosti Microsoft. V sestavě zahrnují hodnotu x-ms-request-id, Přibližná doba, po který byl požadavek, identifikátor odběru, kolekci úloh nebo úlohy a typ operace, které se pokusili žádosti.

## <a name="see-also"></a>Viz také
 [Co je Scheduler?](scheduler-intro.md)

 [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)

 [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)

 [Plány a fakturace v Azure Scheduleru](scheduler-plans-billing.md)

 [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)

 [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)

 [Vysoká dostupnost a spolehlivost Azure Scheduleru](scheduler-high-availability-reliability.md)

 [Odchozí ověření Azure Scheduleru](scheduler-outbound-authentication.md)

