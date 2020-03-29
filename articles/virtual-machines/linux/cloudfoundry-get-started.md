---
title: Začínáme s Cloud Foundry v Microsoft Azure
description: Spuštění OSS nebo pivotal Cloud Foundry v Microsoft Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: ba0b4a636d8f785f1c169e4066c9cf6d25ba81db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970052"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry v Azure

Cloud Foundry je opensourcová služba typu PaaS (platforma jako služba) pro vytváření, nasazování a provozování aplikací typu 12-Factor vyvinutých v různých jazycích a architekturách. Tento dokument popisuje možnosti, které máte pro spuštění Cloud Foundry v Azure a jak můžete začít.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry nabídky

V Azure jsou k dispozici dvě formy cloudové sléváry: open source Cloud Foundry (OSS CF) a Pivotal Cloud Foundry (PCF). OSS CF je zcela [open-source](https://github.com/cloudfoundry) verze Cloud Foundry spravované Nadací Cloud Foundry Foundation. Pivotal Cloud Foundry je podniková distribuce Cloud Foundry od Pivotal Software Inc. Podíváme se na některé rozdíly mezi těmito dvěma nabídkami.

### <a name="open-source-cloud-foundry"></a>Open-source Cloud Foundry

OSS Cloud Foundry můžete nasadit v Azure tak, že nejprve nasadíte ředitele BOSH a poté nasadíte Cloud Foundry pomocí [pokynů uvedených na GitHubu](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Další informace o používání OSS CF najdete v [dokumentaci](https://docs.cloudfoundry.org/) poskytované Nadací cloudové slévárny.

Společnost Microsoft poskytuje nejlepší úsilí podporu Pro OSS CF prostřednictvím následujících komunitních kanálů:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>bosh-azure-cpi kanál na [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [cf-bosh mailing list](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problémy GitHubu pro [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) a [servisního zprostředkovatele](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Úroveň podpory pro vaše prostředky Azure, jako jsou virtuální počítače, ve kterých spouštěte Cloud Foundry, je založena na vaší smlouvě o podpoře Azure. Podpora komunity s nejlepším úsilím se vztahuje pouze na komponenty specifické pro slévárnu cloudu.

### <a name="pivotal-cloud-foundry"></a>Slévárna s každova klíčového cloudu

Pivotal Cloud Foundry zahrnuje stejnou základní platformu jako distribuci OSS, spolu se sadou proprietárních nástrojů pro správu a podnikové podpory. Chcete-li spustit PCF v Azure, musíte získat licenci od Pivotal. Nabídka PCF z Azure Marketplace obsahuje 90denní zkušební licenci.

Mezi tyto nástroje patří [Správce nástrojů Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), webová aplikace, která zjednodušuje nasazení a správu nadace Cloud Foundry, a Správce [klíčových aplikací](https://docs.pivotal.io/pivotalcf/console/), webové aplikace pro správu uživatelů a aplikací.

Kromě podpůrných kanálů uvedených výše pro OSS CF vás licence PCF opravňuje kontaktovat pivotal pro podporu. Společnosti Microsoft a Pivotal také povolily pracovní postupy podpory, které vám umožňují kontaktovat některou ze stran o pomoc a váš dotaz správně směrovat v závislosti na tom, kde se jedná o problém.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry podporuje metodiku ["dvanáctifaktorové aplikace",](https://12factor.net/) která podporuje čisté oddělení bezstavových aplikačních procesů a stavové podpůrné služby. [Zprostředkovatelé služeb](https://docs.cloudfoundry.org/services/api.html) nabízejí konzistentní způsob, jak zřídit a vázat podpůrné služby pro aplikace. [Zprostředkovatel služeb Azure](https://github.com/Azure/meta-azure-service-broker) poskytuje některé klíčové služby Azure prostřednictvím tohoto kanálu, včetně úložiště Azure a Azure SQL.

Pokud používáte Pivotal Cloud Foundry, zprostředkovatel služeb je také [k dispozici jako dlaždice](https://docs.pivotal.io/azure-sb/installing.html) z pivotal sítě.

## <a name="related-resources"></a>Související prostředky

### <a name="azure-devops-services-plugin"></a>Plugin azure devops services

Cloud Foundry je vhodný pro agilní vývoj softwaru, včetně použití průběžné integrace (CI) a průběžného doručování (CD). Pokud ke správě projektů používáte služby Azure DevOps Services a chcete nastavit kanál CI/CD zaměřený na cloudovou slévárnu, můžete použít [rozšíření sestavení Azure DevOps Services Cloud Foundry](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Díky pluginu je konfigurace a automatizace nasazení ve službě Cloud Foundry, ať už běží v Azure nebo v jiném prostředí.

## <a name="next-steps"></a>Další kroky

- [Nasazení pivotal Cloud Foundry z Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Nasazení aplikace do Cloud Foundry v Azure](./cloudfoundry-deploy-your-first-app.md)
