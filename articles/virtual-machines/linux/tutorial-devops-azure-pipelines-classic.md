---
title: Kurz – konfigurace kumulovaných nasazení pro Azure Linux Virtual Machines
description: V tomto kurzu se dozvíte, jak nastavit kanál průběžného nasazování (CD), který postupně aktualizuje skupinu Azure Linux Virtual Machines pomocí postupné strategie nasazení.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113475"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Kurz – konfigurace postupné strategie nasazení pro Azure Linux Virtual Machines

Azure DevOps je integrovaná služba Azure, která automatizuje každou část procesu DevOps s průběžnou integrací a průběžným doručováním pro libovolný prostředek Azure.
Ať už vaše aplikace používá virtuální počítače, webové aplikace, Kubernetes nebo jakýkoli jiný prostředek, můžete implementovat infrastrukturu jako kód, průběžnou integraci, průběžné testování, průběžné doručování a nepřetržité monitorování s využitím Azure a Azure DevOps.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS – konfigurace CI/CD 
Azure Pipelines poskytuje kompletní, plně vybavenou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů. Kanál průběžného doručování pro virtuální počítač Azure můžete nakonfigurovat přímo z Azure Portal. Tento dokument obsahuje kroky spojené s nastavením kanálu CI/CD pro zavedení nasazení na více počítačů z Azure Portal. Můžete se také podívat na jiné strategie, jako je [Kanárské](https://aka.ms/AA7jdrz) a [modrá-zelená](https://aka.ms/AA83fwu), které jsou podporované předem z Azure Portal. 


**Konfigurovat CI/CD na Virtual Machines**

Virtuální počítače se dají přidat jako cíle do [skupiny nasazení](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) a můžou se zaměřit na více než počítačovou aktualizaci. Po nasazení poskytuje **Historie nasazení** v rámci skupiny nasazení možnost sledovatelnost z virtuálního počítače do kanálu a pak na potvrzení. 
 

**Postupná nasazení**: postupné nasazení nahrazuje instance předchozí verze aplikace s instancemi nové verze aplikace v pevné sadě počítačů (kumulovaná sada) v každé iteraci. Podíváme se, jak můžete nakonfigurovat postupnou aktualizaci virtuálních počítačů.  
Můžete nakonfigurovat kumulativní aktualizace pro vaše "**virtuální počítače**" v rámci Azure Portal pomocí možnosti průběžné doručování. 

Tady je podrobný návod. 
1. Přihlaste se ke svému Azure Portal a přejděte k virtuálnímu počítači. 
2. V levém podokně virtuální počítač přejděte do nabídky **průběžné doručování** . Pak klikněte na **Konfigurovat**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Na panelu Konfigurace klikněte na "DevOps organizace Azure" a vyberte existující účet, nebo ho vytvořte. Pak vyberte projekt, pod kterým chcete kanál nakonfigurovat.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Skupina nasazení je logická sada cílových počítačů nasazení, které reprezentují fyzická prostředí; například "dev", "test", "UAT" a "produkční". Můžete vytvořit novou skupinu nasazení nebo vybrat existující skupinu nasazení. 
5. Vyberte kanál sestavení, který publikuje balíček, který se má nasadit do virtuálního počítače. Všimněte si, že publikovaný balíček by měl mít skript nasazení _Deploy. ps1_ nebo _Deploy.sh_ ve `deployscripts` složce v kořenovém adresáři balíčku. Tento skript nasazení se spustí v kanálu Azure DevOps v době běhu.
6. Vyberte strategii nasazení dle vašeho výběru. V takovém případě umožňuje vybrat možnost ' válcování '.
7. Volitelně můžete označit počítač pomocí role. Například "Web", "DB" atd. To vám pomůže cílit jenom na virtuální počítače, které mají konkrétní roli.
8. Kliknutím na tlačítko **OK** v dialogovém okně nakonfigurujte kanál průběžného doručování. 
9. Po dokončení budete mít pro nasazení do virtuálního počítače nakonfigurovaný kanál průběžného doručování.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Uvidíte, že probíhá nasazení na virtuální počítač. Kliknutím na odkaz můžete přejít na kanál. Kliknutím na **Release-1** Zobrazte nasazení. Případně můžete kliknout na **Upravit** a upravit definici kanálu vydání. 
11. Pokud máte několik virtuálních počítačů, které se mají nakonfigurovat, opakujte kroky 2-4 pro přidání dalších virtuálních počítačů do skupiny nasazení. Všimněte si, že pokud vyberete skupinu nasazení, pro kterou už existuje běh kanálu, virtuální počítač se přidá do skupiny nasazení bez vytváření nových kanálů. 
12. Po dokončení klikněte na definici kanálu, přejděte k organizaci Azure DevOps a klikněte na **Upravit** kanál vydání. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Klikněte na úlohu propojit **1, 1 úloha** ve fázi **vývoje** . Klikněte na fázi **nasazení** .
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. V podokně Konfigurace na pravé straně můžete určit počet počítačů, které chcete paralelně nasadit v každé iteraci. V případě, že chcete nasadit na více počítačů najednou, můžete ho zadat pomocí posuvníku z podmínek v procentech.  

15. Úloha spustit skript pro nasazení ve výchozím nastavení spustí skript nasazení _Deploy. ps1_ nebo _Deploy.sh_ ve složce deployscripts v kořenovém adresáři publikovaného balíčku.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Další strategie nasazení

- [Konfigurovat strategii pro Kanárské nasazení](https://aka.ms/AA7jdrz)
- [Konfigurace strategie nasazení Blue-zelená](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Projekt Azure DevOps 
Začněte pracovat s Azure snadněji než kdykoli dřív.
 
Pomocí DevOps Projects spusťte aplikaci v jakékoli službě Azure v rámci pouhých tří kroků: Vyberte jazyk aplikace, modul runtime a službu Azure.
 
[Další informace](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Další materiály a zdroje informací 
- [Nasazení do Azure Virtual Machines pomocí projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementace průběžného nasazování vaší aplikace do sady škálování virtuálních počítačů Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
