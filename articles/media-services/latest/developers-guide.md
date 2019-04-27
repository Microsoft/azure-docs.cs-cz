---
title: Azure Media Services v3 – sady SDK pro Azure
description: Tento článek poskytuje přehled o tom, jak začít s vývojem pomocí rozhraní API služby Media Services v3 pomocí sad SDK.
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
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732446"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>Vývoj pomocí sady SDK rozhraní API služby Media Services v3

Jako vývojář, můžete použít Media Services [rozhraní REST API](https://aka.ms/ams-v3-rest-ref) nebo klientských knihoven, které umožňují pracovat s rozhraním REST API snadno vytvářet, spravovat a udržovat vlastní multimediální pracovní postupy. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) rozhraní API je založena na specifikaci OpenAPI (dříve označované jako Swagger).

> [!NOTE]
> Sady SDK služby Azure Media Services v3 nemusí být bezpečné pro vlákna. Při vývoji aplikace Vícevláknová, měli byste přidat vlastní logiku synchronizace vlákno při ochraně klienta nebo použít nový objekt AzureMediaServicesClient na vlákno. Také byste měli být opatrní z více vláken problémy spojené s uvedením volitelné objekty poskytované kódu do klienta (například HttpClient instance v rozhraní .NET).

Toto téma obsahuje odkazy na sady SDK, nástroje, návody.

## <a name="prerequisites"></a>Požadavky

Jak začít s vývojem ve službě Media Services, budete potřebovat:

- Aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [Další informace o základních konceptech](concepts-overview.md)
- Kontrola [vývoj s využitím Media Services – rozhraní API v3](media-services-apis-overview.md)
- [Vytvoření účtu Azure Media Services – rozhraní příkazového řádku](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Zahájení vývoje s využitím sad SDK

### <a name="net"></a>.NET

Použití [sady .NET SDK](https://aka.ms/ams-v3-dotnet-sdk) k [připojit ke službě Media Services](configure-connect-dotnet-howto.md).

Prozkoumejte Media Services [.NET – referenční informace](https://aka.ms/ams-v3-dotnet-ref) dokumentaci.

### <a name="java"></a>Java

Použití [sady Java SDK](https://aka.ms/ams-v3-java-sdk) k [připojit ke službě Media Services](configure-connect-java-howto.md).

Projděte si Media Services [referenční informace k Java](https://aka.ms/ams-v3-java-ref) dokumentaci.

### <a name="nodejs"></a>Node.js

Použití [sady Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) k [připojit ke službě Media Services](configure-connect-nodejs-howto.md).

Prozkoumejte Media Services [referenční informace k Node.js](https://aka.ms/ams-v3-nodejs-ref) dokumentace a podívejte se na [ukázky](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , která ukazují, jak použít rozhraní API služby Media Services s využitím node.js.

### <a name="python"></a>Python

Použití [Python SDK](https://aka.ms/ams-v3-python-sdk).

Projděte si Media Services [referenční informace k Pythonu](https://aka.ms/ams-v3-python-ref) dokumentaci.

### <a name="go"></a>Přejít

Použití [Go SDK](https://aka.ms/ams-v3-go-sdk).

Projděte si Media Services [najdete odkaz](https://aka.ms/ams-v3-go-ref) dokumentaci.

### <a name="ruby"></a>Ruby

Použití [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Průzkumník Azure Media Services

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) je nástroj Windows zákazníkům, kteří chtějí informace o Media Services k dispozici. AMSE je jazyce Winforms /C# aplikaci, která nahrání, stažení, kódování, streamování videa na Vyžádání a živého obsahu pomocí služby Media Services. Nástroj AMSE je pro klienty, kteří chtějí testování Media Services bez psaní kódu. Kód AMSE je k dispozici jako prostředek pro zákazníky, kteří chtějí vyvíjet s využitím Media Services.

AMSE je Opensourcový projekt, je podporované komunitou (jsou hlášeny problémy https://github.com/Azure/Azure-Media-Services-Explorer/issues). Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v článku [kódu k pravidlům chování](https://opensource.microsoft.com/codeofconduct/faq/) nebo se obraťte na opencode@microsoft.com s případnými dotazy nebo připomínky.

## <a name="next-steps"></a>Další postup

[Přehled](media-services-overview.md)
