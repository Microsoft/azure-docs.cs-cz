---
title: Nasazení sady škálování virtuálních počítačů pomocí sady Visual Studio
description: Nasazení Virtual Machine Scale Sets pomocí sady Visual Studio a šablony Správce prostředků
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: visual-studio
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 2db281f1b3278e37deca8486971a7f7a83f85ccf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86503077"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Jak vytvořit sadu škálování virtuálního počítače pomocí sady Visual Studio

V tomto článku se dozvíte, jak nasadit sadu škálování virtuálních počítačů Azure pomocí nasazení skupiny prostředků sady Visual Studio.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) je výpočetní prostředek Azure pro nasazení a správu kolekce podobných virtuálních počítačů s využitím automatického škálování a vyrovnávání zatížení. Pomocí [šablon Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)můžete zřídit a nasadit Virtual Machine Scale Sets. Šablony Azure Resource Manager můžete nasadit pomocí Azure CLI, PowerShellu, REST a také přímo ze sady Visual Studio. Sada Visual Studio poskytuje sadu ukázkových šablon, které můžete nasadit v rámci projektu nasazení skupiny prostředků Azure.

Nasazení skupiny prostředků Azure je způsob, jak seskupit a publikovat sadu souvisejících prostředků Azure v rámci jediné operace nasazení. Další informace najdete v tématu [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít nasazovat Virtual Machine Scale Sets v aplikaci Visual Studio, potřebujete následující požadavky:

* Visual Studio 2013 nebo novější
* Azure SDK 2,7, 2,8 nebo 2,9

>[!NOTE]
>Tento článek používá sadu Visual Studio 2019 se sadou [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Vytvořit projekt <a name="creating-a-project"></a> 

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.

1. V možnosti **vytvořit nový projekt**vyberte **skupinu prostředků Azure** pro C# a pak vyberte **Další**.

1. V **konfiguraci nového projektu**zadejte název a vyberte **vytvořit**.

    ![Název a vytvoření projektu](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. V seznamu šablon vyberte šablonu **Windows Virtual Machine Scale set** nebo **Linux Virtual Machine Scale set** . Vyberte **OK**.

   ![Vybrat šablonu virtuálního počítače](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Po vytvoření projektu **Průzkumník řešení** obsahuje skript nasazení prostředí PowerShell, šablonu Azure Resource Manager a soubor parametrů pro sadu škálování virtuálního počítače.

## <a name="customize-your-project"></a>Přizpůsobení projektu

Nyní můžete šablonu upravit, abyste ji mohli přizpůsobit potřebám vaší aplikace. Můžete přidat vlastnosti rozšíření virtuálního počítače nebo upravit pravidla vyrovnávání zatížení. Ve výchozím nastavení jsou šablony sady škálování virtuálních počítačů nakonfigurované pro nasazení rozšíření **AzureDiagnostics** , což usnadňuje přidávání pravidel automatického škálování. Šablony také nasazují Nástroj pro vyrovnávání zatížení s veřejnou IP adresou nakonfigurovanou s pravidly příchozího překladu adres (NAT).

Nástroj pro vyrovnávání zatížení umožňuje připojit se k instancím virtuálních počítačů pomocí SSH (Linux) nebo RDP (Windows). Rozsah portů front-endu začíná na 50000. Pro Linux se při vyrovnávání zatížení na portu 50000 vyrovnávání zatížení směruje na port 22 prvního virtuálního počítače v sadě škálování. Připojení k portu 50001 se směruje na port 22 druhého virtuálního počítače atd.

 Dobrým způsobem, jak upravit šablony pomocí sady Visual Studio, je použít **osnovu JSON**. Můžete uspořádat parametry, proměnné a prostředky. Díky porozumění schématu může Visual Studio nahlásit chyby ve vaší šabloně ještě před tím, než je nasadíte.

![Průzkumník JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Nasadit projekt

Nasazením šablony Azure Resource Manager vytvořte prostředek sady škálování virtuálního počítače:

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt a vyberte **nasadit**  >  **Nový**.

    ![Nasazení projektu](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. V možnosti **nasadit do skupiny prostředků**vyberte předplatné, které chcete použít, a vyberte skupinu prostředků. V případě potřeby můžete vytvořit skupinu prostředků.

1. V dalším kroku vyberte **Upravit parametry** a zadejte parametry, které se předávají do šablony.

   ![Zadejte předplatné a skupinu prostředků.](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Zadejte uživatelské jméno a heslo pro operační systém. Tyto hodnoty jsou nutné k vytvoření nasazení. Pokud nemáte nainstalované PowerShell Tools for Visual Studio, vyberte **Uložit hesla** , abyste se vyhnuli skrytému příkazovému řádku PowerShellu, nebo použijte [podporu Key Vault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Pokračujte výběrem **Uložit** .

    ![Upravit parametry nasazení](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. V v **nasazení do skupiny prostředků**vyberte **nasadit**. Akce spustí skript **Deploy-AzureResourceGroup.ps1** . V okně **výstup** se zobrazí průběh nasazení.

   ![Výstup zobrazuje výsledky](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Prozkoumejte sadu škálování virtuálního počítače <a name="exploring-your-virtual-machine-scale-set"></a>

Vyberte **Zobrazit**  >  **Průzkumníka cloudu** a zobrazte novou sadu škálování virtuálního počítače. V případě potřeby použijte **Aktualizovat vše**.

![Průzkumník cloudu](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Průzkumník cloudu** umožňuje spravovat prostředky Azure v nástroji Visual Studio při vývoji aplikací. Můžete si také zobrazit sadu škálování virtuálního počítače v [Azure Portal](https://portal.azure.com) a [Azure Resource Explorer](https://resources.azure.com/).

 Portál nabízí nejlepší způsob, jak spravovat infrastrukturu Azure pomocí webového prohlížeče. Azure Resource Explorer poskytuje snadný způsob, jak prozkoumat a ladit prostředky Azure. Azure Resource Explorer nabízí zobrazení instance a také zobrazí příkazy PowerShellu pro prostředky, na které se díváte.

## <a name="next-steps"></a>Další kroky

Po úspěšném nasazení Virtual Machine Scale Sets prostřednictvím sady Visual Studio můžete projekt dále přizpůsobit tak, aby vyhovoval vašim požadavkům vaší aplikace. Například můžete nakonfigurovat automatické škálování přidáním prostředku **Insights** . Do šablony můžete přidat infrastrukturu, jako jsou například samostatné virtuální počítače, nebo nasadit aplikace pomocí rozšíření vlastních skriptů. Dobré příklady šablon najdete v úložišti GitHub [šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates) . Vyhledejte `vmss`.
