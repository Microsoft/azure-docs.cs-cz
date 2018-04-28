---
title: Začínáme s Cloud Foundry na platformě Microsoft Azure | Microsoft Docs
description: Spustit OSS nebo Foundry hrají cloudu na platformě Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 42910675bcf512a3d6c76369adc9f41215420a78
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry v Azure

Foundry cloudu je open-source platformy jako služba (PaaS) pro vytváření, nasazení a provozování 12-factor aplikace vyvinuté v různých jazyků a rozhraní. Tento dokument popisuje možnosti, které jsou na cloudu Foundry systémem Azure a jak můžete začít používat.

## <a name="cloud-foundry-offerings"></a>Pro cloud Foundry

Existují dvě formy Foundry cloudu k dispozici pro spuštění v Azure: cloudové Foundry (OSS CR) a hrají cloudu Foundry (PCF) open source. Je OSS CR zcela [open-source](https://github.com/cloudfoundry) verzi cloudu Foundry spravuje Foundation Foundry cloudu. Otáčení Foundry cloudu je distribuci enterprise Cloud Foundry z hrají Inc. softwaru Podíváme se na některé rozdíly mezi dvěma nabídky.

### <a name="open-source-cloud-foundry"></a>Open-source cloudu Foundry

Nejprve nasazení BOSH ředitele a pak nasazením Foundry cloudu, můžete nasadit Foundry cloudu operačních systémů v Azure pomocí [pokynů na Githubu](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Další informace o používání CR operačních systémů najdete v tématu [dokumentace](https://docs.cloudfoundry.org/) poskytované Foundation Foundry cloudu.

Společnost Microsoft poskytuje podporu best effort pro OSS CR těmito cestami komunity:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>kanál bosh-azure ISP na [Slack Foundry cloudu](https://slack.cloudfoundry.org/)
- [seznamu adresátů bosh CR](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub problémy pro [ISP](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) a [služby service broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Úroveň podpory pro vaše prostředky Azure, jako jsou virtuální počítače, kde spouštíte cloudu Foundry vychází z vaší smlouvy podporu Azure. Podpora komunity Best effort platí pouze pro komponenty Foundry specifické pro Cloud.

### <a name="pivotal-cloud-foundry"></a>Hrají cloudu Foundry

Otáčení Foundry cloudu obsahuje stejnou základní platformu jako distribuce operačních systémů, společně se sadou Nástroje pro správu proprietární a podnikové podpory. Pokud chcete spustit PCF v Azure, musíte získat licenci z Pivotal. Nabídka PCF z Azure marketplace zahrnuje-90denní zkušební licence.

K nástrojům patří [hrají Operations Manager](http://docs.pivotal.io/pivotalcf/customizing/), webové aplikace, která zjednodušuje nasazení a správu cloudu Foundry foundation, a [hrají aplikace správce](https://docs.pivotal.io/pivotalcf/console/), webové aplikace pro správu uživatelů a aplikací.

Kromě kanály podpory pro OSS CR výše uvedené licenci PCF vás opravňuje k Pivotal požádat o podporu. Společnost Microsoft a Pivotal také povolili podporu pracovních postupů, které vám umožní buď strany požádejte o pomoc a mít dotazu směrovány odpovídajícím způsobem v závislosti na tom, kde je problém.

## <a name="azure-service-broker"></a>Zprostředkovatele služby Azure

Umožňuje Foundry cloudu ["dvanácti factor aplikace"](https://12factor.net/) metody, která se zvýší úroveň čistou oddělit bezstavové aplikace, procesy a stavové služby zálohování. [Zprostředkovatelé služeb](https://docs.cloudfoundry.org/services/api.html) nabízejí konzistentní způsob, jak zřídit a vytvořte vazbu základní služby pro aplikace. [Služby Azure service broker](https://github.com/Azure/meta-azure-service-broker) obsahuje některé z klíčových služeb Azure prostřednictvím tohoto kanálu, včetně úložiště Azure a Azure SQL.

Pokud používáte hrají Foundry cloudu, služby service broker je také [k dispozici jako dlaždici](https://docs.pivotal.io/azure-sb/installing.html) z hrají sítě.

## <a name="related-resources"></a>Související prostředky

### <a name="visual-studio-team-services-plugin"></a>Visual Studio Team Services modulu plug-in

Cloud Foundry je skvěle hodí pro vývoj agilní softwaru, včetně použití průběžnou integraci (CI) a nastavené průběžné doručování (CD). Pokud používáte Visual Studio Team Services (VSTS) ke správě vašich projektů a chce nastavit až CI/CD kanálu cílení Foundry cloudu, můžete použít [Foundry cloudové služby VSTS sestavení rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Tento modul plug-in můžete snadno nakonfigurovat a automatizovat nasazení do cloudu Foundry, zda běžící v Azure nebo jiné prostředí.

## <a name="next-steps"></a>Další postup

- [Nasazení Foundry hrají cloudu z Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Nasazení aplikace do cloudu Foundry v Azure](./cloudfoundry-deploy-your-first-app.md)
