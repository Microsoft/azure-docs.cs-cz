---
title: Řešení potíží se selháním vytváření virtuálních počítačů a prostředí Azure DevTest Labs | Dokumentace Microsoftu
description: Informace o odstraňování problémů virtuálních počítačů (VM) a chyby při vytváření prostředí ve službě Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: 7baa5e4c113e6c21c6123ac7c8399533a7dfb358
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410304"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Řešení potíží s virtuálním počítači (VM) a chyby při vytváření prostředí ve službě Azure DevTest Labs
DevTest Labs poskytuje upozornění Pokud název počítače je neplatný nebo pokud se chystáte porušení zásad testovacího prostředí. V některých případech se zobrazí červený `X` vedle vašeho testovacího prostředí stav virtuálního počítače nebo prostředí, která informuje o tom, že došlo k chybě.  Tento článek obsahuje několik triky, které slouží k vyhledání daný problém a snad problému v budoucnu vyhnout.

## <a name="portal-notifications"></a>Upozornění portálu
Pokud používáte na webu Azure portal, je podívat se na první místo **panel oznámení**.  Panel oznámení, k dispozici na panelu příkazů hlavní kliknutím **ikonu zvonku**, vám dá vědět, zda vytváření virtuálních počítačů nebo prostředí testovacího prostředí byla nebo nebyla úspěšná.  Pokud došlo k chybě, zobrazí chybová zpráva přidružená k selhání vytvoření. Další podrobnosti často poskytují informace, které vám pomohou vyřešit problém. V následujícím příkladu vytvoření virtuálního počítače se nezdařilo z důvodu nedostatku jader. Podrobná zpráva vysvětluje, jak tento problém vyřešit a požádat o zvýšení kvóty jader.

![Oznámení Azure portal](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>Protokoly aktivit
Podívejte se na protokoly aktivit, pokud zkoumáte selhání nějakou dobu po pokusu o vytvoření virtuálního počítače nebo prostředí. Tato část ukazuje, jak najít protokoly pro virtuální počítače a prostředí.

## <a name="activity-logs-for-virtual-machines"></a>Protokoly aktivit pro virtuální počítače

1. Na domovské stránce testovacího prostředí, vyberte virtuální počítač spustit **virtuálního počítače** stránky.
2. Na **virtuálního počítače** stránku, **monitorování** část v levé nabídce vyberte **protokolu aktivit** zobrazíte všechny protokoly, které jsou přidružená k virtuálnímu počítači.
3. V položky protokolu aktivit vyberte operace, která selhala. Obvykle se nazývá neúspěšnou operaci `Write Virtualmachines`.
4. V pravém podokně přepněte na kartu JSON. Zobrazí podrobnosti ve formátu JSON zobrazení protokolu.

    ![Protokol aktivit pro virtuální počítač](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Prohlédněte si protokolu JSON, dokud nenajdete `statusMessage` vlastnost. Poskytuje hlavní chybovou zprávu a další podrobné informace, pokud je k dispozici. Následující kód JSON je příklad základní v uvozovkách, překročil chyba viděli dříve v tomto článku.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Protokol aktivit pro prostředí

Pokud chcete zobrazit v protokolu aktivit vytváření prostředí, postupujte takto:

1. Na domovské stránce testovacího prostředí, vyberte **konfigurace a zásad** v nabídce vlevo.
2. na **konfigurace a zásad** stránce **protokoly aktivit** v nabídce.
3. Vyhledejte chyby v seznamu aktivit v protokolu a vyberte ji.
4. V pravém podokně, přepněte na kartu JSON a vyhledejte **statusMessage**.

    ![Protokol aktivit prostředí](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Protokoly nasazení šablony Resource Manageru
Pokud vaše prostředí nebo na virtuálním počítači byl vytvořen pomocí automatizace, je poslední pohromadě hledat informace o chybě. To je protokol pro nasazení šablony Azure Resource Manageru. Vytvoření prostředku testovacího prostředí díky automatizaci se často provádí prostřednictvím k nasazení šablony Azure Resource Manageru. Zobrazit[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) pro ukázkové šablony Azure Resource Manageru, které vytvářejí prostředky DevTest Labs.

Pokud chcete zobrazit protokoly nasazení šablony testovacího prostředí, postupujte podle těchto kroků:

1. Úvodní stránka pro skupinu prostředků, ve které existuje testovacího prostředí.
2. Vyberte **nasazení** v nabídce vlevo v části **nastavení**.
3. Hledat nasazení se stav selhání a vyberte ji.
4. Na **nasazení** stránce **podrobnosti o operaci** odkaz pro operace, která selhala.
5. Zobrazit podrobnosti o operaci, která se nezdařilo. **podrobnosti o operaci** okna.

## <a name="next-steps"></a>Další postup
Zobrazit [řešení potíží s selhání artefaktů](devtest-lab-troubleshoot-artifact-failure.md)