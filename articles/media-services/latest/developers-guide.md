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
ms.date: 02/16/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 2f0191bd181a8e10fa59f6d1d53da348e6440aba
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405550"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Začněte vyvíjet s využitím rozhraní API služby Media Services v3 pomocí sady SDK a nástroje

Jako vývojář, můžete použít Media Services [rozhraní REST API](https://aka.ms/ams-v3-rest-sdk) nebo klientských knihoven, které umožňují pracovat s rozhraním REST API snadno vytvářet, spravovat a udržovat vlastní multimediální pracovní postupy. Rozhraní API služby Media Services v3 je založená na specifikaci OpenAPI (dříve označované jako Swagger).

Toto téma obsahuje odkazy na sady SDK, nástroje, dokumentaci. Poskytuje také některé užitečné informace pro jiné vývojáře env.

## <a name="prerequisites"></a>Požadavky

Jak začít s vývojem ve službě Media Services, budete potřebovat:

- Aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [Vytvoření účtu Media Services](create-account-cli-how-to.md)

## <a name="start-developing"></a>Začít vyvíjet

Azure Media Services podporuje následující sady SDK a nástroje 

- [Azure CLI](https://aka.ms/ams-v3-cli) 
- [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)
- [Python SDK](https://aka.ms/ams-v3-python-sdk)
- [Go SDK](https://aka.ms/ams-v3-go-sdk)
- [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Průzkumník Azure Media Services

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) je nástroj Windows zákazníkům, kteří chtějí informace o Media Services k dispozici. AMSE je jazyce Winforms /C# aplikaci, která nahrání, stažení, kódování, streamování videa na Vyžádání a živého obsahu pomocí služby Media Services. Nástroj AMSE je pro klienty, kteří chtějí testování Media Services bez psaní kódu. Kód AMSE je k dispozici jako prostředek pro zákazníky, kteří chtějí vyvíjet s využitím Media Services.

AMSE je Opensourcový projekt, je podporované komunitou (jsou hlášeny problémy https://github.com/Azure/Azure-Media-Services-Explorer/issues). Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v článku [kódu k pravidlům chování](https://opensource.microsoft.com/codeofconduct/faq/) nebo se obraťte na opencode@microsoft.com s případnými dotazy nebo připomínky.

## <a name="rest"></a>REST

Jak začít s vývojem pomocí rozhraní REST API služby Media Services, nainstalujte klienta REST. Například **Postman**, **Visual Studio Code** pomocí modulu plug-in REST nebo **Telerik Fiddler**. Používáme [Postman](media-rest-apis-with-postman.md) pro Media Services v3 ukázky.

Prozkoumejte Media Services [reference rozhraní REST API](https://aka.ms/ams-v3-rest-ref) dokumentace a prohlédněte si tyto příklady:

- [Kurz: Vzdálený soubor na základě adresy URL kódování a streamování videa – REST](stream-files-tutorial-with-rest.md)
- [Nahrání souborů do účtu Azure Media Services – REST](upload-files-rest-how-to.md)
- [Vytváření filtrů pomocí Media Services – REST](filters-dynamic-manifest-rest-howto.md)
- [Rozhraní REST API založené na Azure Resource Manageru](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Prozkoumejte Media Services [rozhraní příkazového řádku Azure ref](https://aka.ms/ams-v3-cli-ref) dokumentace a prohlédněte si tyto příklady:

- [Rezervované pro média škálovací jednotky – rozhraní příkazového řádku](media-reserved-units-cli-how-to.md)
- [Vytvoření účtu Azure Media Services – rozhraní příkazového řádku](./scripts/cli-create-account.md) 
- [Resetovat přihlašovací údaje účtu – rozhraní příkazového řádku](./scripts/cli-reset-account-credentials.md)
- [Vytvoření prostředků – rozhraní příkazového řádku](./scripts/cli-create-asset.md)
- [Nahrát soubor – rozhraní příkazového řádku](./scripts/cli-upload-file-asset.md)
- [Vytvoření transformace – rozhraní příkazového řádku](./scripts/cli-create-transform.md)
- [Vytvoření úlohy – CLI](./scripts/cli-create-jobs.md)
- [Vytvoření EventGrid – rozhraní příkazového řádku](./scripts/cli-create-event-grid.md)
- [Publikování assetu – rozhraní příkazového řádku](./scripts/cli-publish-asset.md)
- [Filtr – rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Prozkoumejte Media Services [.NET ref](https://aka.ms/ams-v3-dotnet-ref) dokumentace a prohlédněte si tyto příklady:

- [Kurz: Nahrávání, kódování a streamování videí – .NET](stream-files-tutorial-with-api.md) 
- [Kurz: Live Stream pomocí Media Services v3 – .NET](stream-live-tutorial-with-api.md)
- [Kurz: Analýza videa pomocí Media Services v3 – .NET](analyze-videos-tutorial-with-api.md)
- [Vytvoření vstupní úlohy z místního souboru – .NET](job-input-from-local-file-how-to.md)
- [Vytvoření vstupní úlohy z adresy URL HTTPS – .NET](job-input-from-http-how-to.md)
- [Kódování pomocí vlastní transformace – .NET](customize-encoder-presets-how-to.md)
- [Používat dynamické šifrování AES-128 a služba doručování klíčů – .NET](protect-with-aes128.md)
- [DRM dynamického šifrování a licence doručování služba – .NET](protect-with-drm.md)
- [Získejte podpisový klíč ze stávající zásady – .NET](get-content-key-policy-dotnet-howto.md)
- [Vytváření filtrů pomocí Media Services – .NET](filters-dynamic-manifest-dotnet-howto.md)

## <a name="java"></a>Java

Projděte si Media Services [Java ref](https://aka.ms/ams-v3-java-ref) dokumentaci.

## <a name="nodejs"></a>Node.js

Prozkoumejte Media Services [Node.js ref](https://aka.ms/ams-v3-nodejs-ref) dokumentace a podívejte se na [ukázky](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , která ukazují, jak použít rozhraní API služby Media Services s využitím node.js.

## <a name="python"></a>Python

Projděte si Media Services [Python ref](https://aka.ms/ams-v3-python-ref) dokumentaci.

## <a name="go"></a>Přejít

Projděte si Media Services [Go ref](https://aka.ms/ams-v3-go-ref) dokumentaci.

## <a name="next-steps"></a>Další postup

- [Vytvoření účtu – rozhraní příkazového řádku](create-account-cli-how-to.md)
- [Přístup k rozhraní API – rozhraní příkazového řádku](access-api-cli-how-to.md)

