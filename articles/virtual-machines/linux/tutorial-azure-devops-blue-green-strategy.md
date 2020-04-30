---
title: Kurz – konfigurace Kanárských nasazení pro Azure Linux Virtual Machines
description: V tomto kurzu se dozvíte, jak nastavit kanál průběžného nasazování (CD), který aktualizuje skupinu virtuálních počítačů Azure pomocí strategie nasazení Blue-zelená.
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120489"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Kurz – konfigurace strategie nasazení Blue-zelená pro Azure Linux Virtual Machines


## <a name="iaas---configure-cicd"></a>IaaS – konfigurace CI/CD 
Azure Pipelines poskytuje kompletní, plně vybavenou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů. Kanál průběžného doručování pro virtuální počítač Azure můžete nakonfigurovat přímo z Azure Portal. Tento dokument obsahuje kroky spojené s nastavením kanálu CI/CD, který používá strategii Blue-zelená pro nasazení na více počítačů. Můžete se také podívat na jiné strategie, jako je například [válcování](https://aka.ms/AA7jlh8) a [Kanárské](https://aka.ms/AA7jdrz), které jsou podporované předem z Azure Portal. 

 
 **Konfigurovat CI/CD na Virtual Machines**

Virtuální počítače je možné přidat jako cíle do [skupiny nasazení](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) a můžou být cílené na aktualizace pro více počítačů. Po nasazení poskytuje zobrazení **Historie nasazení** v rámci skupiny nasazení možnost sledovatelnost z virtuálního počítače do kanálu a pak na potvrzení. 
 
  
**Blue-zelená nasazení**: nasazení Blue-zelená zkracuje výpadky díky stejnému pohotovostnímu prostředí. Jedno z prostředí je v reálném čase. Při přípravě na novou verzi dokončíte finální fázi testování v zeleném prostředí. Jakmile software v zeleném prostředí pracuje, přepněte provoz tak, aby všechny příchozí požadavky přešly do zeleného prostředí – modré prostředí je teď nečinné.
Pomocí možnosti průběžné doručování můžete nakonfigurovat nasazení Blue-zelená na vaše "**virtuální počítače**" z Azure Portal. 

Tady je podrobný návod.

1. Přihlaste se ke svému Azure Portal a přejděte k virtuálnímu počítači. 
2. V levém podokně virtuálního počítače se dostanete k **průběžnému doručování**. Pak klikněte na **Konfigurovat**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Na panelu Konfigurace klikněte na možnost **organizace Azure DevOps** a vyberte existující účet, nebo ho vytvořte. Pak vyberte projekt, pod kterým chcete kanál nakonfigurovat.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Skupina nasazení je logická sada cílových počítačů nasazení, které reprezentují fyzická prostředí; například "dev", "test", "UAT" a "produkční". Můžete vytvořit novou skupinu nasazení nebo vybrat existující skupinu nasazení. 
5. Vyberte kanál sestavení, který publikuje balíček, který se má nasadit do virtuálního počítače. Všimněte si, že publikovaný balíček by měl mít skript nasazení _Deploy. ps1_ nebo _Deploy.sh_ ve `deployscripts` složce v kořenovém adresáři balíčku. Tento skript nasazení se spustí v kanálu Azure DevOps za běhu.
6. Vyberte strategii nasazení dle vašeho výběru. Vyberte **modrou zelenou**.
7. Přidejte "modrou" nebo "zelenou" značku do virtuálních počítačů, které mají být součástí nasazení s modrou zelenou verzí. Pokud je virtuální počítač pro roli v pohotovostním režimu, měli byste ho označit jako "zelený", jinak ho označit jako "modrý".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. Kliknutím na tlačítko **OK** nakonfigurujte kanál průběžného doručování. Teď budete mít k dispozici profil průběžného doručování nakonfigurovaný pro nasazení na virtuální počítač.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Pokud chcete zobrazit konfiguraci kanálu, klikněte na **Upravit** kanál verze v Azure DevOps. Kanál se skládá ze tří fází. První fáze je fáze skupiny nasazení a nasazuje se do virtuálních počítačů, které jsou označené jako _zelené_ (pohotovostní virtuální počítače). Druhá fáze pozastaví kanál a počká na ruční zásah, aby bylo možné pokračovat v běhu. Jakmile uživatel splní, že je nasazení stabilní, může nyní přesměrovat provoz na _zelených_ virtuálních počítačů a obnovit běh kanálu, který pak bude na virtuálních počítačích měnit _modré_ a _zelené_ značky. Tím se zajistěte, aby virtuální počítače, které mají starší verzi aplikace, byly označeny _zeleně_ a byly nasazeny do příštího spuštění kanálu.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. Úloha spustit skript pro nasazení ve výchozím nastavení spustí skript nasazení _Deploy. ps1_ nebo _Deploy.sh_ ve `deployscripts` složce v kořenovém adresáři publikovaného balíčku. Ujistěte se, že vybraný kanál sestavení je publikovaná v kořenové složce balíčku.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Další strategie nasazení
- [Konfigurace strategie postupné implementace](https://aka.ms/AA7jlh8)
- [Konfigurovat strategii pro Kanárské nasazení](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Projekt Azure DevOps 
Začněte pracovat s Azure snadněji než kdykoli dřív.
 
Pomocí DevOps Projects spusťte aplikaci v jakékoli službě Azure v rámci pouhých tří kroků: Vyberte jazyk aplikace, modul runtime a službu Azure.
 
[Další informace](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Další materiály a zdroje informací 
- [Nasazení do Azure Virtual Machines pomocí projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementace průběžného nasazování vaší aplikace do sady škálování virtuálních počítačů Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
