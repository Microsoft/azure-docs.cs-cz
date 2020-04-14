---
title: Nasazení škálovací sady virtuálních strojů pomocí Sady Visual Studio
description: Nasazení škálovacích sad virtuálních strojů pomocí sady Visual Studio a šablony Správce prostředků
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mimckitt
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: mimckitt
ms.openlocfilehash: adc91d5f4f79be8a85dfed7d10a882493f6427b0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273337"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Jak vytvořit škálovací sadu virtuálních strojů pomocí sady Visual Studio

Tento článek ukazuje, jak nasadit škálovatkapacitu virtuálního počítače Azure pomocí nasazení skupiny prostředků Visual Studio.

[Škálovací sady virtuálních počítačů Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) je výpočetní prostředek Azure Compute pro nasazení a správu kolekce podobných virtuálních počítačů s automatickým škálováním a vyrovnáváním zatížení. Škálovací sady virtuálních strojů můžete zřídit a nasadit pomocí [šablon Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Šablony Azure Resource Manageru se můžou nasadit pomocí Azure CLI, PowerShellu, REST a taky přímo z Visual Studia. Visual Studio poskytuje sadu ukázkových šablon, které můžete nasadit jako součást projektu nasazení skupiny prostředků Azure.

Nasazení skupiny prostředků Azure jsou způsob, jak seskupit a publikovat sadu souvisejících prostředků Azure v rámci jedné operace nasazení. Další informace najdete [v tématu Vytváření a nasazování skupin prostředků Azure prostřednictvím sady Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít nasazovat škálovací sady virtuálních strojů v sadě Visual Studio, potřebujete následující požadavky:

* Visual Studio 2013 nebo novější
* Azure SDK 2.7, 2.8 nebo 2.9

>[!NOTE]
>Tento článek používá Visual Studio 2019 s [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Vytvoření projektu<a name="creating-a-project"></a> 

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**.

1. V **okně Vytvořit nový projekt**zvolte **Skupinu prostředků Azure** pro C# a pak vyberte **Další**.

1. V **okně Konfigurace nového projektu**zadejte název a vyberte **Vytvořit**.

    ![Pojmenování a vytvoření projektu](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Ze seznamu šablon zvolte buď šablonu **Windows Virtual Machine Scale Set** nebo Linux Virtual Machine Scale **Set.** Vyberte **OK**.

   ![Výběr šablony virtuálního počítače](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Explorer **řešení** po vytvoření projektu obsahuje skript nasazení prostředí PowerShell, šablonu Azure Resource Manager a soubor parametrů pro škálovací sadu virtuálních strojů.

## <a name="customize-your-project"></a>Přizpůsobení projektu

Nyní můžete šablonu upravit a přizpůsobit ji potřebám vaší aplikace. Můžete přidat vlastnosti rozšíření virtuálního počítače nebo upravit pravidla vyrovnávání zatížení. Ve výchozím nastavení jsou šablony škálovací sady virtuálních strojů nakonfigurované k nasazení rozšíření **AzureDiagnostics,** což usnadňuje přidání pravidel automatického škálování. Šablony také nasazují vyvyřičování zatížení s veřejnou IP adresou, nakonfigurovanou s pravidly příchozího nat.

Nástroj pro vyrovnávání zatížení umožňuje připojit se k instancí virtuálního počítače pomocí SSH (Linux) nebo RDP (Windows). Rozsah předního portu začíná na 50000. Pro Linux, pokud SSH port 50000, vyrovnávání zatížení vás trasy port 22 z prvního virtuálního počítače v škálovací sadě. Připojení k portu 50001 je směrováno na port 22 druhého virtuálního počítače a tak dále.

 Dobrým způsobem, jak upravit šablony v sadě Visual Studio, je použít **osnovu JSON**. Můžete uspořádat parametry, proměnné a zdroje. S pochopení schématu Visual Studio můžete poukázat na chyby v šabloně před nasazením.

![Průzkumník JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Nasazení projektu

Nasazení šablony Azure Resource Manager k vytvoření prostředku škálování virtuálních strojů:

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a zvolte **Nasadit** > **nový**.

    ![Nasazení projektu](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. V **okně Nasadit do skupiny prostředků**zvolte, které předplatné chcete použít, a vyberte skupinu prostředků. V případě potřeby můžete vytvořit skupinu prostředků.

1. Dále vyberte **Upravit parametry,** chcete-li zadat parametry, které jsou předány do šablony.

   ![Zadat předplatné a skupinu prostředků](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Zadejte uživatelské jméno a heslo pro operační systém. Tyto hodnoty jsou nutné k vytvoření nasazení. Pokud nemáte nainstalované nástroje prostředí PowerShell pro Visual Studio, vyberte **Uložit hesla,** abyste se vyhnuli skrytému příkazovému řádku prostředí PowerShell, nebo použijte [podporu trezoru klíčů](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Chcete-li pokračovat, vyberte **uložit.**

    ![Upravit parametry nasazení](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. V **okně Nasadit do skupiny prostředků**vyberte **Nasadit**. Akce spustí skript **Deploy-AzureResourceGroup.ps1.** Okno **Výstup** zobrazuje průběh nasazení.

   ![Výstup zobrazuje výsledky](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Prozkoumejte svou škálovací sadu virtuálních strojů<a name="exploring-your-virtual-machine-scale-set"></a>

Vyberte **Zobrazit** > **Průzkumníka cloudu,** chcete-li zobrazit novou škálovací sadu virtuálních strojů. V případě potřeby použijte **použít tlačítko Aktualizovat vše**.

![Průzkumník cloudu](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** umožňuje spravovat prostředky Azure ve Visual Studiu při vývoji aplikací. Škálovací sadu virtuálních strojů můžete taky zobrazit na [webu Azure Portal](https://portal.azure.com) a Azure Resource [Explorer](https://resources.azure.com/).

 Portál poskytuje nejlepší způsob správy infrastruktury Azure pomocí webového prohlížeče. Azure Resource Explorer poskytuje snadný způsob, jak prozkoumat a ladit prostředky Azure. Azure Resource Explorer nabízí zobrazení instance a také zobrazuje příkazy PowerShellu pro prostředky, které se díváte na.

## <a name="next-steps"></a>Další kroky

Po úspěšném nasazení škálovacísady virtuálních strojů prostřednictvím sady Visual Studio, můžete dále přizpůsobit projekt tak, aby vyhovoval vašim požadavkům na aplikace. Automatické škálování nakonfigurujte například přidáním prostředku **Přehledů.** Do šablony můžete přidat infrastrukturu, například samostatné virtuální počítače, nebo nasadit aplikace pomocí rozšíření vlastního skriptu. Dobré příklady šablon najdete v úložišti GitHub [azure quickstart šablony.](https://github.com/Azure/azure-quickstart-templates) Vyhledejte `vmss`.
