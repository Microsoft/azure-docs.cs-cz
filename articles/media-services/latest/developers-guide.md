---
title: Azure Media Services v3 – sady SDK pro Azure
description: Tento článek poskytuje přehled o tom, jak začít s vývojem pomocí rozhraní API služby Media Services v3 pomocí sady SDK a nástroje.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: d97682de6c8eb7dd36e25cbd4b8d66d328745748
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543625"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Začněte vyvíjet s využitím rozhraní API služby Media Services v3 pomocí sady SDK a nástroje

Jako vývojář, můžete použít Media Services [rozhraní REST API](https://aka.ms/ams-v3-rest-ref) nebo klientských knihoven, které umožňují pracovat s rozhraním REST API snadno vytvářet, spravovat a udržovat vlastní multimediální pracovní postupy. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) rozhraní API je založena na specifikaci OpenAPI (dříve označované jako Swagger).

Než začnete s vývojem, projděte si [vývoj s využitím rozhraní API služby Media Services v3](media-services-apis-overview.md).

> [!NOTE]
> Sady SDK služby Azure Media Services v3 nemusí být bezpečné pro vlákna. Při vývoji aplikace Vícevláknová, měli byste přidat vlastní logiku synchronizace vlákno při ochraně klienta nebo použít nový objekt AzureMediaServicesClient na vlákno. Také byste měli být opatrní z více vláken problémy spojené s uvedením volitelné objekty poskytované kódu do klienta (například HttpClient instance v rozhraní .NET).

Toto téma obsahuje odkazy na sady SDK, nástroje, další documentaion.

## <a name="prerequisites"></a>Požadavky

Jak začít s vývojem ve službě Media Services, budete potřebovat:

- Aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [Další informace o základních konceptech](concepts-overview.md)
- [Vytvoření účtu Azure Media Services – rozhraní příkazového řádku](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Zahájení vývoje s využitím sad SDK

### <a name="net"></a>.NET

Použití [sady .NET SDK](https://aka.ms/ams-v3-dotnet-sdk).

Prozkoumejte Media Services [.NET ref](https://aka.ms/ams-v3-dotnet-ref) dokumentaci.

### <a name="java"></a>Java

Použití [Java SDK](https://aka.ms/ams-v3-java-sdk).

Projděte si Media Services [Java ref](https://aka.ms/ams-v3-java-ref) dokumentaci.

### <a name="nodejs"></a>Node.js

Použití [sady Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk).

Prozkoumejte Media Services [Node.js ref](https://aka.ms/ams-v3-nodejs-ref) dokumentace a podívejte se na [ukázky](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , která ukazují, jak použít rozhraní API služby Media Services s využitím node.js.

### <a name="python"></a>Python

Použití [Python SDK](https://aka.ms/ams-v3-python-sdk).

Projděte si Media Services [Python ref](https://aka.ms/ams-v3-python-ref) dokumentaci.

### <a name="go"></a>Přejít

Použití [Go SDK](https://aka.ms/ams-v3-go-sdk).

Projděte si Media Services [Go ref](https://aka.ms/ams-v3-go-ref) dokumentaci.

### <a name="ruby"></a>Ruby

Použití [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Průzkumník Azure Media Services

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) je nástroj Windows zákazníkům, kteří chtějí informace o Media Services k dispozici. AMSE je jazyce Winforms /C# aplikaci, která nahrání, stažení, kódování, streamování videa na Vyžádání a živého obsahu pomocí služby Media Services. Nástroj AMSE je pro klienty, kteří chtějí testování Media Services bez psaní kódu. Kód AMSE je k dispozici jako prostředek pro zákazníky, kteří chtějí vyvíjet s využitím Media Services.

AMSE je Opensourcový projekt, je podporované komunitou (jsou hlášeny problémy https://github.com/Azure/Azure-Media-Services-Explorer/issues). Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v článku [kódu k pravidlům chování](https://opensource.microsoft.com/codeofconduct/faq/) nebo se obraťte na opencode@microsoft.com s případnými dotazy nebo připomínky.

## <a name="next-steps"></a>Další postup

[Přehled](media-services-overview.md)
