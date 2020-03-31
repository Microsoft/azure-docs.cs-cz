---
title: Poradce při potížích s virtuálními počítači a selháníprostředí Azure DevTest Labs
description: Zjistěte, jak řešit potíže s problémy s virtuálním počítačem (VM) a selháním vytváření prostředí v laboratořích Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166350"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Poradce při potížích s virtuálním počítačem (VM) a selháním vytváření prostředí v laboratořích Azure DevTest Labs
DevTest Labs poskytuje upozornění, pokud název počítače je neplatný nebo pokud se chystáte porušit zásady testovacího prostředí. Někdy se vedle `X` virtuálního počítače testovacího prostředí nebo stavu prostředí zobrazí červená, která vás informuje, že se něco pokazilo.  Tento článek obsahuje několik triků, které můžete použít k nalezení základního problému a doufejme, že se v budoucnu vyhnete problému.

## <a name="portal-notifications"></a>Oznámení portálu
Pokud používáte portál Azure, první místo, na které se můžete podívat, je **panel oznámení**.  Panel oznámení, který je k dispozici na hlavním panelu příkazů kliknutím na **ikonu zvonku**, vám řekne, zda byl virtuální počítač testovacího prostředí nebo vytvoření prostředí úspěšné nebo ne.  Pokud došlo k chybě, zobrazí se chybová zpráva spojená s selháním vytvoření. Podrobnosti často poskytují další informace, které vám pomohou problém vyřešit. V následujícím příkladu se nezdařilo vytvoření virtuálního počítače z důvodu vyčerpání jader. Podrobná zpráva vám řekne, jak problém vyřešit a požádat o zvýšení kvóty jádra.

![Oznámení o portálu Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>Virtuální ms ve stavu poškození
Pokud se stav virtuálního počítače v testovacím prostředí zobrazuje jako **poškozený**, základní virtuální počítač byl pravděpodobně odstraněn ze stránky **Virtuální počítač,** na kterou může uživatel přejít ze stránky **Virtuální počítače** (ne ze stránky DevTest Labs). Vyčistěte své testovací prostředí v DevTest Labs odstraněním virtuálního počítače z laboratoře. Potom znovu vytvořte virtuální počítač v testovacím prostředí. 

![Virtuální virtuální byl v poškozeném stavu](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Protokoly aktivit
Podívejte se na protokoly aktivit, pokud vyšetřujete selhání někdy po pokusu o vytvoření virtuálního počítače nebo prostředí. V této části se dozvíte, jak najít protokoly pro virtuální chod y a prostředí.

## <a name="activity-logs-for-virtual-machines"></a>Protokoly aktivit pro virtuální počítače

1. Na domovské stránce testovacího prostředí vyberte virtuální počítač a spusťte stránku **Virtuální počítač.**
2. Na stránce **Virtuální počítač** vyberte v části **MONITOROVÁNÍ** v levé nabídce **protokol aktivit,** chcete-li zobrazit všechny protokoly přidružené k virtuálnímu počítači.
3. V položkách protokolu aktivit vyberte operaci, která se nezdařila. Obvykle se nazývá `Write Virtualmachines`operace, která selhala .
4. V pravém podokně přepněte na kartu JSON. Zobrazí se podrobnosti v zobrazení JSON protokolu.

    ![Protokol aktivit pro virtuální hod](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Prohlédněte si deník JSON, `statusMessage` dokud nenajdete nemovitost. Poskytuje hlavní chybovou zprávu a další podrobné informace, pokud je to možné. Následující JSON je příkladem pro jádro citoval překročena chyba vidět dříve v tomto článku.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Protokol aktivit pro prostředí

Chcete-li zobrazit protokol aktivit pro vytvoření prostředí, postupujte takto:

1. Na domovské stránce testovacího prostředí vyberte **konfigurace a zásady** v levé nabídce.
2. na stránce **Konfigurace a zásady** vyberte v nabídce **protokoly aktivit.**
3. Vyhledejte chybu v seznamu aktivit v protokolu a vyberte ji.
4. V pravém podokně přepněte na kartu JSON a vyhledejte **stavZpráva**.

    ![Protokol aktivit prostředí](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Protokoly nasazení šablon Správce prostředků
Pokud vaše prostředí nebo virtuální počítač byl vytvořen prostřednictvím automatizace, je tu ještě jedno poslední místo, kde hledat informace o chybě. To je protokol nasazení šablony Azure Resource Manager. Když se prostředek testovacího prostředí vytvoří prostřednictvím automatizace, často se provádí prostřednictvím nasazení šablony Azure Resource Manager. Ukázkové šablony Azure Resource Manageru, které vytvářejí prostředky DevTest Labs, najdete.[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)

Chcete-li zobrazit protokoly nasazení šablony testovacího prostředí, postupujte takto:

1. Spusťte stránku pro skupinu prostředků, ve kterém existuje testovací prostředí.
2. V levé nabídce v části Nastavení vyberte **Možnost** **Nasazení** .
3. Vyhledejte nasazení se stavem se nezdařilo a vyberte je.
4. Na stránce **Nasazení** vyberte odkaz **Podrobnosti operace** pro operaci, která se nezdařila.
5. Podrobnosti o operaci, která se nezdařila v okně **Podrobnosti operace.**

## <a name="next-steps"></a>Další kroky
Viz [Řešení potíží s chybami artefaktů](devtest-lab-troubleshoot-artifact-failure.md)
