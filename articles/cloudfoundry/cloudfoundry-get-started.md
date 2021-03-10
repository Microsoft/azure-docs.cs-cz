---
title: Začínáme s Cloud Foundry na Microsoft Azure
description: Spuštění OSS nebo Pivot Cloud Foundry v Microsoft Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: d3b13dffcf251f79a04182d32f23fd40e6ed6044
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562890"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry v Azure

Cloud Foundry je opensourcová služba typu PaaS (platforma jako služba) pro vytváření, nasazování a provozování aplikací typu 12-Factor vyvinutých v různých jazycích a architekturách. Tento dokument popisuje možnosti, které máte ke spuštění Cloud Foundry v Azure, a jak můžete začít.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry nabídky

Existují dvě formy Cloud Foundry k dispozici pro spuštění v Azure: Open-Source Cloud Foundry (OSS CF) a Pivoted Cloud Foundry (PCF). OSS CF je zcela [Open Source](https://github.com/cloudfoundry) verze Cloud Foundry spravovaná Cloud Foundry Foundation. Pivoting Cloud Foundry je podniková distribuce Cloud Foundry od společnosti Pivoted Software Inc. Podíváme se na některé rozdíly mezi dvěma nabídkami.

### <a name="open-source-cloud-foundry"></a>Open-Source Cloud Foundry

OSS Cloud Foundry v Azure můžete nasadit tak, že nejdřív nasadíte ředitele BOSH a pak nasadíte Cloud Foundry pomocí [pokynů uvedených na GitHubu](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Další informace o použití OSS CF najdete v [dokumentaci](https://docs.cloudfoundry.org/) poskytované Cloud Foundry Foundation.

Microsoft poskytuje nejlepší podporu pro OSS CF prostřednictvím následujících kanálů komunity:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>Bosh – kanál Azure-CPI při [Cloud Foundry časové rezervy](https://slack.cloudfoundry.org/)
- [CF – seznam adresátů Bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problémy GitHubu pro [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) a [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Úroveň podpory vašich prostředků Azure, jako jsou třeba virtuální počítače, na kterých spouštíte Cloud Foundry, je založená na vaší smlouvě o podpoře Azure. Nejlepší podpora komunity se vztahuje pouze na komponenty specifické pro Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Cloud Foundry Pivot

Pivoting Cloud Foundry zahrnuje stejnou základní platformu jako distribuci OSS, spolu se sadou proprietárních nástrojů pro správu a podnikovou podporou. Pokud chcete spustit PCF v Azure, musíte získat licenci od pivotu. Nabídka PCF z webu Azure Marketplace zahrnuje zkušební licenci 90.

Mezi tyto nástroje patří [pivoting Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), Webová aplikace, která zjednodušuje nasazování a správu Cloud Foundry Foundation a [správce pivoted Apps](https://docs.pivotal.io/pivotalcf/console/), webovou aplikaci pro správu uživatelů a aplikací.

Kromě kanálů podpory uvedených pro OSS CF výše vám licence PCF opravňuje k tomu, abyste se obrátili na podporu. Microsoft a Pivoted také povolily pracovní postupy podpory, které vám umožňují požádat o pomoc obě strany a podle toho, kde se problém týká, je váš dotaz správně směrován.

## <a name="azure-service-broker"></a>Service Broker Azure

Cloud Foundry podporuje metodologii ["32bitová aplikace"](https://12factor.net/) , která podporuje čisté oddělení bezstavových procesů aplikace a stavové služby pro zálohování. [Služby Service Broker](https://docs.cloudfoundry.org/services/api.html) nabízejí jednotný způsob, jak zřídit a navazovat služby zálohování pro aplikace. [Azure Service Broker](https://github.com/Azure/meta-azure-service-broker) poskytuje některé z klíčových služeb Azure prostřednictvím tohoto kanálu, včetně Azure Storage a Azure SQL.

Pokud používáte Cloud Foundry Pivoted, služba Service Broker je také [k dispozici jako dlaždice](https://docs.pivotal.io/azure-sb/installing.html) ze pivoted Network.

## <a name="related-resources"></a>Související prostředky

### <a name="azure-devops-services-plugin"></a>Modul plug-in Azure DevOps Services

Cloud Foundry je vhodný pro agilní vývoj softwaru, včetně použití průběžné integrace (CI) a průběžného doručování (CD). Použijete-li Azure DevOps Services ke správě projektů a chcete nastavit kanál CI/CD, který cílí na Cloud Foundry, můžete použít [rozšíření build Cloud Foundry Azure DevOps Services](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Modul plug-in usnadňuje konfiguraci a automatizaci nasazení Cloud Foundry, ať už běží v Azure nebo jiném prostředí.

## <a name="next-steps"></a>Další kroky

- [Nasazení Pivot Cloud Foundry z Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Nasazení aplikace pro Cloud Foundry v Azure](./cloudfoundry-deploy-your-first-app.md)
