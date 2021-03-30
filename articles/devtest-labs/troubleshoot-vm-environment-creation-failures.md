---
title: Řešení potíží se selháním virtuálního počítače a prostředí Azure DevTest Labs
description: Naučte se řešit potíže s vytvářením virtuálních počítačů a prostředí v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476474"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Řešení chyb při vytváření virtuálních počítačů a prostředí v Azure DevTest Labs
DevTest Labs vám poskytne upozornění, pokud je název počítače neplatný nebo pokud se chystáte porušovat zásady testovacího prostředí. Někdy se zobrazí červená `X` vedle vašeho testovacího virtuálního počítače nebo stavu prostředí, která vás informuje, že došlo k nějaké chybě.  Tento článek poskytuje několik štychů, které můžete použít k vyhledání základního problému a snad, abyste se vyhnuli problému v budoucnu.

## <a name="portal-notifications"></a>Oznámení na portálu
Pokud používáte Azure Portal, první místo, kde se na něj můžete podívat, je **panel oznámení**.  Panel oznámení, který je k dispozici na hlavním panelu příkazů kliknutím na **ikonu zvonku**, vám sdělí, jestli byl virtuální počítač nebo vytvoření prostředí testovacího prostředí úspěšné nebo ne.  Pokud došlo k chybě, zobrazí se chybová zpráva přidružená k chybě při vytváření. Podrobnosti často poskytují další informace, které vám pomůžou problém vyřešit. V následujícím příkladu se virtuální počítač nepodařilo vytvořit z důvodu nedostatku jader. Podrobná zpráva vám ukáže, jak problém vyřešit a požádat o zvýšení kvóty jádra.

![Oznámení Azure Portal](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>Virtuální počítač ve stavu poškození
Pokud se stav virtuálního počítače v testovacím prostředí zobrazí jako **poškozený**, mohl by být příslušný virtuální počítač odstraněný ze stránky **virtuálního počítače** , na kterou může uživatel přejít z **Virtual Machines** stránky (ne ze stránky DevTest Labs). Vyčistěte testovací prostředí v DevTest Labs tím, že virtuální počítač odstraníte z testovacího prostředí. Pak znovu vytvořte virtuální počítač v testovacím prostředí. 

![Virtuální počítač v poškozeném stavu](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Protokoly aktivit
Pokud při pokusu o vytvoření virtuálního počítače nebo prostředí prozkoumáte nějaké chyby, podívejte se na protokoly aktivit. V této části se dozvíte, jak najít protokoly pro virtuální počítače a prostředí.

## <a name="activity-logs-for-virtual-machines"></a>Protokoly aktivit pro virtuální počítače

1. Na domovské stránce testovacího prostředí vyberte virtuální počítač, na kterém se má spustit stránka **virtuálního počítače** .
2. Na stránce **virtuální počítač** v části **monitorování** v levé nabídce vyberte **Protokol aktivit** , aby se zobrazily všechny protokoly přidružené k virtuálnímu počítači.
3. V položkách protokolu aktivit vyberte operaci, která se nezdařila. Obvykle je volána neúspěšná operace `Write Virtualmachines` .
4. V pravém podokně přepněte na kartu JSON. V zobrazení protokolu JSON se zobrazí podrobnosti.

    ![Protokol aktivit pro virtuální počítač](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Podívejte se do protokolu JSON, dokud nenajdete `statusMessage` vlastnost. Poskytuje hlavní chybovou zprávu a podrobněji podrobnější informace, pokud je to možné. Následující kód JSON je příkladem pro základní citované chyby, které jsme si poznamenali dříve v tomto článku.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Protokol aktivit pro prostředí

Pokud chcete zobrazit protokol aktivit pro vytvoření prostředí, postupujte podle těchto kroků:

1. Na domovské stránce testovacího prostředí vyberte v nabídce vlevo možnost **Konfigurace a zásady** .
2. na stránce **Konfigurace a zásady** vyberte v nabídce **protokoly aktivit** .
3. Vyhledejte chybu v seznamu aktivit v protokolu a vyberte ji.
4. V pravém podokně přepněte na kartu JSON a vyhledejte **statusMessage**.

    ![Protokol aktivity prostředí](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Protokoly nasazení Správce prostředků šablon
Pokud se vaše prostředí nebo virtuální počítač vytvořil prostřednictvím automatizace, je k dispozici jedno místo, kde najdete informace o chybě. Toto je Azure Resource Manager protokol nasazení šablony. Při vytvoření prostředku testovacího prostředí prostřednictvím automatizace se často provádí pomocí nasazení Azure Resource Manager šablon. [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)Příklady šablon Azure Resource Manager, které vytvářejí prostředky DevTest Labs, najdete v tématu.

Chcete-li zobrazit protokoly nasazení šablon testovacího prostředí, postupujte takto:

1. Spusťte stránku pro skupinu prostředků, ve které testovací prostředí existuje.
2. V části **Nastavení** v nabídce vlevo vyberte **nasazení** .
3. Vyhledejte nasazení se stavem selhání a vyberte je.
4. Na stránce **nasazení** vyberte odkaz **Podrobnosti operace** pro operaci, která selhala.
5. Zobrazí se podrobnosti o operaci, která selhala v okně **Podrobnosti operace** .

## <a name="next-steps"></a>Další kroky
Viz [Poradce při potížích s chybami artefaktů](devtest-lab-troubleshoot-artifact-failure.md)
