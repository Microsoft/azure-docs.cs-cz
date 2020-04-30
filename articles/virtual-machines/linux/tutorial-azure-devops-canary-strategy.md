---
title: Kurz – konfigurace Kanárských nasazení pro Azure Linux Virtual Machines
description: V tomto kurzu se dozvíte, jak nastavit kanál průběžného nasazování (CD), který aktualizuje skupinu Azure Linux Virtual Machines s využitím strategie pro Kanárské nasazení.
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120475"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Kurz – konfigurace strategie nasazení pro Kanárské prostředí pro Azure Linux Virtual Machines


## <a name="iaas---configure-cicd"></a>IaaS – konfigurace CI/CD 
Azure Pipelines poskytuje kompletní, plně vybavenou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů. Kanál průběžného doručování pro virtuální počítač Azure můžete nakonfigurovat přímo z Azure Portal. Tento dokument obsahuje kroky spojené s nastavením kanálu CI/CD, který využívá strategii pro nasazení na více počítačů. Můžete se také podívat na jiné strategie, jako je například [valení](https://aka.ms/AA7jlh8) a [modrá-zelená](https://aka.ms/AA83fwu), která je dostupná předem z Azure Portal. 


**Konfigurovat CI/CD na Virtual Machines**

Virtuální počítače je možné přidat jako cíle do [skupiny nasazení](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) a můžou být cílené na aktualizace pro více počítačů. Po nasazení poskytuje **Historie nasazení** v rámci skupiny nasazení sledovatelnost z virtuálního počítače do kanálu a pak na potvrzení změn. 
 
  
**Kanárské nasazení**: v rámci Kanárských nasazení se zpomaluje změna na malou podmnožinu uživatelů. Když v nové verzi získáte větší jistotu, můžete ji začít vydávat na více serverů v infrastruktuře a následně do ní směrovat více uživatelů. Pomocí možnosti průběžné doručování můžete nakonfigurovat Kanárské nasazení na své virtuální Azure Portal počítače. Tady je podrobný návod. 
1. Přihlaste se ke svému Azure Portal a přejděte k virtuálnímu počítači. 
2. V levém podokně virtuální počítač přejděte do nabídky **průběžné doručování** . Klikněte na **Konfigurovat**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Na panelu Konfigurace klikněte na možnost **organizace Azure DevOps** a vyberte existující účet, nebo ho vytvořte. Pak vyberte projekt, pod kterým chcete kanál nakonfigurovat.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Skupina nasazení je logická sada cílových počítačů nasazení, které reprezentují fyzická prostředí; například "dev", "test", "UAT" a "produkční". Můžete vytvořit novou skupinu nasazení nebo vybrat existující skupinu nasazení. 
5. Vyberte kanál sestavení, který publikuje balíček, který se má nasadit do virtuálního počítače. Všimněte si, že publikovaný balíček by měl mít skript nasazení _Deploy. ps1_ nebo _Deploy.sh_ ve `deployscripts` složce v kořenovém adresáři balíčku. Tento skript nasazení se spustí v rámci kanálu Azure DevOps v době běhu.
6. Vyberte strategii nasazení dle vašeho výběru. Vyberte možnost **Kanárské**.
7. Přidejte značku "Kanárské" do virtuálních počítačů, které mají být součástí Kanárských nasazení a označením "prod" na virtuální počítače, které jsou součástí nasazení po úspěšném nasazení. Značky umožňují cílit jenom na virtuální počítače, které mají konkrétní roli.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. Kliknutím na tlačítko **OK** v dialogovém okně nakonfigurujte kanál průběžného doručování. Teď budete mít k dispozici profil průběžného doručování nakonfigurovaný pro nasazení na virtuální počítač.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Pokud chcete zobrazit konfiguraci kanálu, klikněte na **Upravit** kanál verze v Azure DevOps. Kanál se skládá ze tří fází. První fáze je fáze skupiny nasazení a nasazuje se do virtuálních počítačů, které jsou označené jako _Kanárské_. Druhá fáze pozastaví kanál a počká na ruční zásah, aby bylo možné pokračovat v běhu. Jakmile je uživatel přesvědčen o tom, že je Kanárské nasazení stabilní, může pokračovat v běhu kanálu a potom spustí třetí fázi, která nasadí do virtuálních počítačů označených jako _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. Před pokračováním spuštění kanálu se ujistěte, že je aspoň jeden virtuální počítač označený jako _prod_. Ve třetí fázi kanálu bude aplikace nasazena pouze do těch virtuálních počítačů, které mají _výrobní_ značku.

11. Úloha spustit skript pro nasazení ve výchozím nastavení spustí skript nasazení _Deploy. ps1_ nebo _Deploy.sh_ ve složce deployscripts v kořenovém adresáři publikovaného balíčku. Ujistěte se, že vybraný kanál sestavení je publikovaná v kořenové složce balíčku. 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Další strategie nasazení
- [Konfigurace strategie postupné implementace](https://aka.ms/AA7jlh8)
- [Konfigurace strategie nasazení Blue-zelená](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Projekt Azure DevOps 
Začněte pracovat s Azure snadněji než kdykoli dřív.
 
Pomocí DevOps Projects spusťte aplikaci v jakékoli službě Azure v rámci pouhých tří kroků: Vyberte jazyk aplikace, modul runtime a službu Azure.
 
[Další informace](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Další materiály a zdroje informací 
- [Nasazení do Azure Virtual Machines pomocí projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementace průběžného nasazování vaší aplikace do sady škálování virtuálních počítačů Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
