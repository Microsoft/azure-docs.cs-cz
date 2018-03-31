---
title: Pomocí rozhraní REST API pro přístup k rozhraní API služby Azure Mobile Engagement
description: Popisuje, jak používat Mobile Engagement REST API pro přístup k rozhraní API služby Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: c3c0de39ba13ed47345b1042a9dceda075dc50b3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Pomocí REST pro přístup k rozhraní API služby Azure Mobile Engagement
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Azure Mobile Engagement poskytuje [REST API služby Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) pro správu zařízení, kampaní Reach/nabízených atd.

> [!NOTE]
> Službu Azure Mobile Engagement vyřadíme z provozu v březnu 2018. V současnosti je dostupná jenom pro stávající zákazníky. Další informace najdete v tématu [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Pokud nechcete používat rozhraní REST API přímo, poskytujeme také [soubor Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) , můžete pomocí nástrojů můžete vygenerovat pomocí sady SDK pro preferovaný jazyk. Doporučujeme používat [AutoRest](https://github.com/Azure/AutoRest) nástroj pro generování vaší sady SDK z našich souboru Swagger. Vytvořili jsme .NET SDK podobným způsobem, který umožňuje pracovat s Tato rozhraní API pomocí obálku C# a nemusíte dělat vyjednávání tokenu ověřování a aktualizujte sami. V tématu [služby rozhraní API .NET SDK – ukázka](mobile-engagement-dotnet-sdk-service-api.md) se dozvíte, jak používat .net SDK pro rozhraní API
