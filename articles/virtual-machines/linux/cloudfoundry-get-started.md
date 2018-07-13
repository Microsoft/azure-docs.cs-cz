---
title: Začínáme s Cloud Foundry v Microsoft Azure | Dokumentace Microsoftu
description: Použití OSS nebo Pivotal Cloud Foundry v Microsoft Azure
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
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972287"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry v Azure

Cloud Foundry je opensourcová služba typu PaaS (platforma jako služba) pro vytváření, nasazování a provozování aplikací typu 12-Factor vyvinutých v různých jazycích a architekturách. Tento dokument popisuje možnosti, které máte pro spuštění Cloud Foundry v Azure a jak můžete začít.

## <a name="cloud-foundry-offerings"></a>Pro cloud Foundry

Existují dvě formy Cloud Foundry je k dispozici pro použití v Azure: open-source Cloud Foundry (OSS CF) a Pivotal Cloud Foundry (PCF). Je OSS CF zcela [open source](https://github.com/cloudfoundry) verzi Cloud Foundry spravuje Cloud Foundry Foundation. Pivotal Cloud Foundry je podniková distribuce nástroje Cloud Foundry od společnosti Pivotal softwaru Inc. Podíváme na některé rozdíly mezi dvěma nabídek.

### <a name="open-source-cloud-foundry"></a>Open source Cloud Foundry

První nasazení BOSH ředitele a pak nasazení Cloud Foundry, můžete nasadit OSS Cloud Foundry v Azure pomocí [pokynů uvedených na Githubu](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Další informace o používání OSS CF, najdete v článku [dokumentaci](https://docs.cloudfoundry.org/) poskytované Cloud Foundry Foundation.

Společnost Microsoft poskytuje podporu best effort pro OSS CF těmito cestami komunity:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>kanál bosh-azure ISP na [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [do seznamu adresátů CF bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problémy Githubu pro [ISP](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) a [služby service broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Úroveň podpory pro vaše prostředky Azure, jako jsou virtuální počítače, ve kterém spouštíte Cloud Foundry je podle smlouvy o podpoře Azure. Podpora komunity Best effort platí jenom pro součásti specifické pro Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry obsahuje stejné základní platformě jako distribuční OSS, společně se sadou nástrojů pro správu proprietární a podnikovou podporu. PCF provozovat v Azure, musíte získat licenci od společnosti Pivotal. PCF nabídky na webu Azure Marketplace zahrnuje 90denní zkušební licenci.

Mezi tyto nástroje patří [Pivotal Operations Manageru](http://docs.pivotal.io/pivotalcf/customizing/), webové aplikace, která zjednodušuje nasazení a správa Cloud Foundry Foundation, a [Pivotal aplikace správce](https://docs.pivotal.io/pivotalcf/console/), webové aplikace pro správu Uživatelé a aplikace.

Kromě kanály podpory pro CF OSS výše uvedené licenci PCF vás opravňuje Pivotal požádat o podporu. Microsoft a Pivotal povolili také podporu pracovních postupů, které vám umožní smluvních požádejte o pomoc a mají váš dotaz zajistit tím správné směrování v závislosti na tom, kde je problém.

## <a name="azure-service-broker"></a>Azure Service Broker

Může vést ke vzniku cloud Foundry ["12 factor app"](https://12factor.net/) metody, která podporuje oddělit nestavových aplikačních procesy a stavové služby zálohování. [Služby můžou být zprostředkovatelé](https://docs.cloudfoundry.org/services/api.html) nabízejí konzistentní způsob, jak zřídit a pomocné služby svázat s aplikací. [Služby Azure service broker](https://github.com/Azure/meta-azure-service-broker) obsahuje některé z klíčových služeb Azure přes tento kanál, včetně služby Azure storage a Azure SQL.

Pokud používáte Pivotal Cloud Foundry, služba Service broker je také [k dispozici jako dlaždici](https://docs.pivotal.io/azure-sb/installing.html) z Pivotal sítě.

## <a name="related-resources"></a>Související prostředky

### <a name="visual-studio-team-services-plugin"></a>Plug-in Visual Studio Team Services

Cloud Foundry je vhodné pro agilní vývoj softwaru, včetně využití čipu TPM kontinuální integrace (CI) a průběžné doručování (CD). Pokud používáte Visual Studio Team Services (VSTS) ke správě vašich projektů a chcete nastavit až CI/CD kanálu cílí na Cloud Foundry, můžete použít [VSTS Cloud Foundry vytvořte rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Modul plug-in můžete snadno nakonfigurovat a automatizovat nasazení do Cloud Foundry, ať už spuštěné v Azure nebo jiného prostředí.

## <a name="next-steps"></a>Další postup

- [Nasazení Pivotal Cloud Foundry na Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Nasazení aplikace na Cloud Foundry v Azure](./cloudfoundry-deploy-your-first-app.md)
