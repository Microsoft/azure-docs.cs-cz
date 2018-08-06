---
title: Přehled Jenkinse a Azure | Microsoft Docs
description: Hostujte automatizační server Jenkinse pro sestavování a nasazování v Azure a rozšiřte své kanály průběžné integrace a nasazování (CI/CD) s využitím výpočetních prostředků a prostředků úložiště Azure.
ms.topic: overview
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/25/2018
ms.openlocfilehash: ecb4ea7aee005cb539910b2cb25f0b84de7ba510
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281551"
---
# <a name="azure-and-jenkins"></a>Azure a Jenkins

[Jenkins](https://jenkins.io/) je oblíbený open-source automatizační server, který se používá k nastavení průběžné integrace a doručování (CI/CD) pro softwarové projekty. Nasazení Jenkinse můžete hostovat v Azure nebo můžete rozšířit stávající konfiguraci Jenkinse s využitím prostředků Azure. K dispozici jsou také moduly plug-in Jenkinse pro zjednodušení průběžné integrace a doručování (CI/CD) vašich aplikací do Azure.

Tento článek je úvodem do používání Azure s Jenkinsem a podrobně popisuje základní funkce Azure dostupné pro uživatele Jenkinse. Pokud chcete začít s vlastním serverem Jenkinse v Azure, přečtěte si náš [rychlý start](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hostování serverů Jenkinse v Azure

Díky hostování Jenkinse v Azure můžete centralizovat automatizaci sestavování a škálovat své nasazení s ohledem na rostoucí požadavky vašich softwarových projektů. K nasazení Jenkinse v Azure můžete použít:
 
- [Šablonu řešení Jenkinse](install-jenkins-solution-template.md) na webu Azure Marketplace.
- [Virtuální počítače Azure](/azure/virtual-machines/linux/overview). Informace o vytvoření instance Jenkinse na virtuálním počítači najdete v našem [kurzu](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd).
- Pokud používáte cluster Kubernetes spuštěný ve službě [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), přečtěte si naše [postupy](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Nasazení Jenkinse v Azure můžete monitorovat a spravovat pomocí [Log Analytics](/azure/log-analytics/log-analytics-overview) a [Azure CLI](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Škálování automatizace sestavování na vyžádání

Přidáním agentů sestavení do stávajícího nasazení Jenkinse můžete škálovat kapacitu sestavování Jenkinse s ohledem na rostoucí počet sestavení a složitost úloh a kanálů. Tyto agenty sestavení můžete spouštět na virtuálních počítačích Azure s využitím [modulu plug-in Azure VM Agents](jenkins-azure-vm-agents.md). Další podrobnosti najdete v našem [kurzu](/azure/jenkins/jenkins-azure-vm-agents).

Po nakonfigurování [instančního objektu Azure](/azure/azure-resource-manager/resource-group-overview) můžou úlohy a kanály Jenkinse využívat tyto přihlašovací údaje k následujícím úlohám:

- Bezpečné ukládání a archivace artefaktů sestavení ve službě [Azure Storage](/azure/storage/common/storage-introduction) s využitím [modulu plug-in Azure Storage](https://plugins.jenkins.io/windows-azure-storage). Další informace najdete v [postupech pro úložiště Jenkinse](/azure/storage/common/storage-java-jenkins-continuous-integration-solution).
- Správa a konfigurace prostředků Azure pomocí [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Nasazení vlastního kódu do služeb Azure

Pomocí modulů plug-in Jenkinse můžete nasazovat aplikace do Azure v rámci kanálů CI/CD Jenkinse. Nasazení do služby [Azure App Service](/azure/app-service/) a [Azure Container Service](/azure/container-service/kubernetes/) vám umožní připravovat, testovat a vydávat aktualizace vašich aplikací bez nutnosti správy základní infrastruktury.

 Moduly plug-in je možné nasadit do následujících služeb a prostředí:

- [Azure Web App on Linux](/azure/app-service/containers/app-service-linux-intro). Začněte prostudováním tohoto [kurzu](java-deploy-webapp-tutorial.md).
- [Webová aplikace Azure](/azure/app-service/app-service-web-overview). Začněte prostudováním těchto [postupů](deploy-Jenkins-app-service-plugin.md).