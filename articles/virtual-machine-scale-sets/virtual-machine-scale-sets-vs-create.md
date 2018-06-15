---
title: Nasazení sadu škálování virtuálního počítače pomocí sady Visual Studio | Microsoft Docs
description: Nasazení sady škálování virtuálního počítače pomocí sady Visual Studio a šablony Resource Manageru
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73454abc11a832a1b7f4131bf13699bd0a94edea
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782142"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Jak vytvořit sadu škálování virtuálního počítače pomocí sady Visual Studio
Tento článek ukazuje, jak nasadit služby Azure sadu škálování virtuálního počítače pomocí Visual Studio nasazení skupiny prostředků.

[Azure sady škálování virtuálního počítače](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) je prostředek Azure Compute k nasazení a správu kolekcí podobných virtuálních počítačů s automatickému škálování a vyrovnávání zatížení. Můžete zřídit a nasadit sady škálování virtuálního počítače pomocí [šablon Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates). Šablony Azure Resource Manageru můžete nasadit pomocí rozhraní příkazového řádku Azure, prostředí PowerShell, REST a také přímo ze sady Visual Studio. Visual Studio poskytuje sadu příklad šablony, které můžete nasadit jako součást projektu nasazení skupiny prostředků Azure.

Nasazení skupiny prostředků Azure představují způsob, jak skupiny a publikovat sadu souvisejících prostředků Azure v rámci operace jedno nasazení. Další informace o nich zde: [vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Požadavky
Abyste mohli začít nasazení sady škálování virtuálního počítače v sadě Visual Studio, budete potřebovat následující:

* Visual Studio 2013 nebo novější
* Azure SDK 2.7, 2.8 nebo 2.9

>[!NOTE]
>Tyto pokyny předpokládají, že používáte Visual Studio s [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Vytvoření projektu
1. Vytvořte nový projekt v sadě Visual Studio výběrem **souboru | Nové | Projekt**.
   
    ![Nový soubor][file_new]

2. V části **Visual C# | Cloud**, zvolte **Azure Resource Manager** k vytvoření projektu pro nasazení šablonu Azure Resource Manager.
   
    ![Vytvoření projektu][create_project]

3. Seznam šablon vyberte buď Linux nebo Windows nastavte šablony škálování virtuálního počítače.
   
   ![Vyberte šablonu][select_Template]

4. Po vytvoření projektu zobrazí nasazení skriptů prostředí PowerShell, šablonu Azure Resource Manager a soubor parametru pro sadu škálování virtuálního počítače.
   
    ![Průzkumník řešení][solution_explorer]

## <a name="customize-your-project"></a>Přizpůsobení projektu
Nyní můžete upravit šablonu, kterou chcete přizpůsobit pro potřeby vaší aplikace, například přidávání vlastnosti rozšíření virtuálního počítače nebo úprava pravidla Vyrovnávání zatížení. Ve výchozím nastavení jsou nakonfigurovat šablony sad škálování virtuálního počítače pro nasazení AzureDiagnostics rozšíření, které umožňuje snadno přidat pravidla automatického škálování. Kromě toho nasadí nástroj pro vyrovnávání zatížení s veřejnou IP adresu, nakonfigurované příchozích pravidel NAT. 

Nástroje pro vyrovnávání zatížení umožňuje připojit se k instancím virtuálních počítačů pomocí protokolu SSH (Linux) nebo protokolu RDP (Windows). Rozsah portů front-endu spustí na 50000. Pro linux, to znamená, že pokud jste SSH port 50000, můžete směrují na port 22 první virtuální počítač ve Škálovací sadě. Připojení k portu 50001 se směruje na port 22 druhý virtuální počítač a tak dále.

 Dobrým způsobem, jak upravit vaše šablony v prostředí Visual Studio je použít k uspořádání parametry, proměnné a prostředky osnovy JSON. Se seznámíte s schéma můžete před nasazením sady Visual Studio upozorníme na chyby ve vaší šabloně.

![Průzkumník JSON][json_explorer]

## <a name="deploy-the-project"></a>Nasazení projektu
1. Nasazení šablony Azure Resource Manageru k vytvoření prostředku sadu škálování virtuálního počítače. Klikněte pravým tlačítkem na uzel projektu a zvolte **nasadit | Nové nasazení**.
   
    ![Nasazení šablony][5deploy_Template]
    
2. V dialogovém okně "Nasazení pro skupinu prostředků" vyberte své předplatné.
   
    ![Nasazení šablony][6deploy_Template]

3. Tady můžete vytvořit skupinu prostředků Azure k nasazení vaší šablony.
   
    ![Novou skupinu prostředků][new_resource]

4. Klikněte na tlačítko **upravit parametry** zadat parametry, které se předávají do šablony. Zadejte uživatelské jméno a heslo pro operační systém, který je nezbytný k vytvoření nasazení. Pokud nemáte prostředí PowerShell nástroje pro sadu Visual Studio, se doporučuje zkontrolovat **ukládat hesla** vyhnout skrytá příkazovém řádku prostředí PowerShell, nebo použijte [keyvault podporu](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Upravit parametry][edit_parameters]

5. Klikněte na tlačítko **nasadit**. **Výstup** v okně se zobrazí průběh nasazení. Všimněte si, že akce se provádí **nasadit AzureResourceGroup.ps1** skriptu.
   
   ![Výstup – okno][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Prohlížení vaší Škálovací sadu virtuálních počítačů
Po dokončení nasazení můžete zobrazit novou sadu škálování virtuálního počítače v sadě Visual Studio **Průzkumník cloudu** (aktualizaci seznamu). Průzkumník cloudu umožňuje spravovat prostředky Azure v sadě Visual Studio při vývoji aplikace. Můžete také zobrazit vaše sadu škálování virtuálního počítače v [portál Azure](https://portal.azure.com) a [Průzkumníka prostředků Azure](https://resources.azure.com/).

![Průzkumník cloudu][cloud_explorer]

 Na portálu poskytuje nejlepší způsob, jak vizuálně spravovat infrastrukturu Azure s webovým prohlížečem, zatímco Průzkumníka prostředků Azure poskytuje snadný způsob, jak prozkoumat a ladění prostředky Azure, udělíte okno do "zobrazení instance" a také zobrazuje příkazy prostředí PowerShell pro prostředky, které se díváte.

## <a name="next-steps"></a>Další kroky
Jakmile jste úspěšně nasazena sady škálování virtuálního počítače pomocí sady Visual Studio, můžete dále přizpůsobit podle svých potřeb aplikace projektu. Můžete například nakonfigurovat automatické škálování přidáním **Insights** prostředek, přidávání infrastruktury do vaší šablony (jako samostatné virtuální počítače) nebo nasazení aplikace, které používají rozšíření vlastních skriptů. Dobrým příkladem šablony lze nalézt v [šablon Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates) úložiště GitHub (vyhledejte "vmss").

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
