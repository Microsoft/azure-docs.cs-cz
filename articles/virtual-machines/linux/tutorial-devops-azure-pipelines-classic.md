---
title: Kurz – integrovaný DevOps pro IaaS a PaaS v Azure
description: V tomto kurzu se dozvíte, jak nastavit průběžnou integraci (CI) a průběžné nasazování (CD) aplikace na virtuální počítače Azure pomocí Azure Pipelines.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912524"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Kurz: integrovaný DevOps pro IaaS a PaaS v Azure

V případě komplexních řešení v Azure můžou týmy implementovat postupy DevOps v každé z fází životního cyklu aplikací: plánování, vývoj, poskytování a provozování. 

Níže jsou uvedené některé služby Azure, které zjednodušují cloudové úlohy a dají se kombinovat, aby bylo možné využít úžasné výkonné scénáře.
Tyto technologie v kombinaci s lidmi a procesy umožňují týmům nepřetržitě poskytovat zákazníkům hodnotu. 

- Azure: https://portal.azure.com – portál pro sestavování cloudových úloh. Spravujte a sledujte všechno z jednoduchých webových aplikací až po komplexní cloudové aplikace. 
- Azure DevOps: https://dev.azure.com – naplánování inteligentnější, lepší spolupráce a rychlejší dodávání se sadou moderních služeb pro vývoj 
- Azure Machine Learning Studio: data pro https://ml.azure.com přípravu, výuku a nasazení modelů strojového učení 
 

Azure DevOps je integrovaná služba Azure, která automatizuje každou část procesu DevOps s průběžnou integrací a průběžným doručováním pro libovolný prostředek Azure.
Bez ohledu na to, jestli vaše aplikace používá virtuální počítače, webové aplikace, Kubernetes nebo jakýkoli jiný prostředek, můžete implementovat, infrastrukturu jako kód, průběžnou integraci, průběžné testování, průběžné doručování a nepřetržité monitorování s využitím Azure a Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS – konfigurace CI/CD 
Azure Pipelines poskytuje kompletní, plně vybavenou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů. Kanál průběžného doručování pro virtuální počítač Azure můžete nakonfigurovat přímo z Azure Portal. Tento dokument obsahuje kroky spojené s nastavením kanálu CI/CD pro nasazení na více počítačů z Azure Portal. Nakonfigurujte CI/CD na Virtual Machines.

Virtuální počítače se dají přidat jako cíle do [skupiny nasazení](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) a můžou se zaměřit na aktualizace s více počítači. Zobrazení historie nasazení v rámci skupin nasazení poskytují sledovatelnost z virtuálního počítače do kanálu a pak na potvrzení. 
 
**Postupné aktualizace**: postupné nasazení nahrazuje instance předchozí verze aplikace s instancemi nové verze aplikace v pevně dané sadě počítačů (kumulovaná sada) v každé iteraci. Podíváme se, jak můžete nakonfigurovat postupnou aktualizaci virtuálních počítačů.  
Můžete nakonfigurovat kumulativní aktualizace pro vaše "**virtuální počítače**" v rámci Azure Portal pomocí možnosti průběžné doručování. 

Tady je podrobný návod. 
1. Přihlaste se ke svému Azure Portal a přejděte k virtuálnímu počítači. 
2. V levém podokně virtuální počítač přejděte do nabídky **průběžné doručování** . Pak klikněte na **Konfigurovat**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. Na panelu Konfigurace klikněte na "DevOps organizace Azure" a vyberte existující účet, nebo ho vytvořte. Pak vyberte projekt, pod kterým chcete kanál nakonfigurovat.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Skupina nasazení je logická sada cílových počítačů nasazení, které reprezentují fyzická prostředí; například "dev", "test", "UAT" a "produkční". Můžete vytvořit novou skupinu nasazení nebo vybrat existující skupinu nasazení. Volitelně můžete označit počítač s rolí. Například "Web", "DB" atd.  
5. Kliknutím na tlačítko **OK** v dialogovém okně nakonfigurujte kanál průběžného doručování. 
6. Po dokončení budete mít pro nasazení do virtuálního počítače nakonfigurovaný kanál průběžného doručování.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Uvidíte, že probíhá nasazení na virtuální počítač. Kliknutím na odkaz můžete přejít na kanál. Kliknutím na **Release-1** Zobrazte nasazení. Případně můžete kliknout na **Upravit** a upravit definici kanálu vydání. 
8. Pokud máte několik virtuálních počítačů, které se mají nakonfigurovat, opakujte kroky 2-5, aby se do skupiny nasazení přidaly další virtuální počítače. 
9. Po dokončení klikněte na definici kanálu, přejděte k organizaci Azure DevOps a klikněte na **Upravit** kanál vydání. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Klikněte na úlohu propojit **1, 1 úloha** ve fázi **vývoje** . Klikněte na fázi **nasazení** .  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. V podokně Konfigurace na pravé straně vidíte, že ve výchozím nastavení je kanál nakonfigurován tak, aby provedenou aktualizaci všech cílů paralelně. Nasazení můžete nakonfigurovat tak, aby se nastalo buď jednou, nebo v procentech pomocí posuvníku.  
  
  
**Kanárské ostrovy** snižuje riziko tím, že se změní na malou podmnožinu uživatelů. Když v nové verzi získáte větší jistotu, můžete ji začít vydávat na více serverů v infrastruktuře a následně do ní směrovat více uživatelů. Pomocí možnosti průběžné doručování můžete nakonfigurovat Kanárské nasazení na vaše "**virtuální počítače**Azure Portal". Tady je podrobný návod. 
1. Přihlaste se ke svému Azure Portal a přejděte k virtuálnímu počítači. 
2. Postupujte podle kroků 2-5 v předchozí části a přidejte do skupiny nasazení více virtuálních počítačů. 
3. Přidejte vlastní značku do virtuálních počítačů, které jsou součástí Kanárských nasazení. Například "Kanárské".
4. Po nakonfigurování kanálu pro virtuální počítače klikněte na kanál, spusťte organizaci Azure DevOps, **upravte** kanál a přejděte do fáze **vývoje** . Přidejte značku do filtru "Kanárské". 
5. Přidejte další fázi skupiny nasazení, nakonfigurujte fázi pomocí značek pro cílení zbývajících virtuálních počítačů ve skupině nasazení.  
6. Volitelně můžete nakonfigurovat krok ručního ověření, který může povýšit nebo odmítnout Kanárské nasazení. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Blue-zelená** zkracuje výpadky nasazení díky stejnému pohotovostnímu prostředí. Jedno z prostředí je v reálném čase. Při přípravě na novou verzi můžete provést finální fázi testování v zeleném prostředí. Jakmile software v zeleném prostředí pracuje, přepněte provoz tak, aby všechny příchozí požadavky přešly do zeleného prostředí – modré prostředí je teď nečinné.
Pomocí možnosti průběžné doručování můžete nakonfigurovat nasazení Blue-zelená na vaše "**virtuální počítače**" z Azure Portal. 

Tady je podrobný návod. 

1. Přihlaste se ke svému Azure Portal a přejděte k virtuálnímu počítači. 
2. Při přidávání několika virtuálních počítačů do skupiny nasazení postupujte podle kroků 2-5 v části **postupná aktualizace** . Přidejte vlastní značku do virtuálních počítačů, které mají být součástí nasazení s modrou zelenou. Například "Blue" nebo "zelená" pro virtuální počítače, které jsou pro roli role. 
3. Po nakonfigurování kanálu pro virtuální počítače klikněte na kanál, spusťte organizaci Azure DevOps, **upravte** kanál a přejděte do fáze **vývoje** . Přidejte značku do filtru "zelená". 
4. Přidejte fázi bez agentů, nakonfigurujte fázi s ručním ověřením a krok rozhraní API pro vyvolání a zahodí značky. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Projekt Azure DevOps 
Začněte pracovat s Azure snadněji než kdykoli dřív.
 
Pomocí DevOps Projects spusťte aplikaci v jakékoli službě Azure v rámci pouhých tří kroků: Vyberte jazyk aplikace, modul runtime a službu Azure.
 
[Další informace](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Další zdroje 
- [Nasazení do Azure Virtual Machines pomocí projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementace průběžného nasazování vaší aplikace do sady škálování virtuálních počítačů Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
