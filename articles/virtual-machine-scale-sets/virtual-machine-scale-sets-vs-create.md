---
title: Nasazení Škálovací sady virtuálních počítačů pomocí sady Visual Studio | Dokumentace Microsoftu
description: Nasazení Škálovací sady virtuálních počítačů pomocí sady Visual Studio a šablonu Resource Manageru
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: manayar
ms.openlocfilehash: 3d472aeaae7e7f02eba58aadea1df042d6c0f27b
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741418"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Vytvoření Škálovací sady virtuálních počítačů pomocí sady Visual Studio
V tomto článku se dozvíte, jak nasadit Azure Virtual Machine Scale Sets s použitím nasazení skupiny prostředků Visual Studio.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) je prostředek Azure Compute k nasazení a správu kolekcí podobných virtuálních počítačů pomocí automatického škálování a vyrovnávání zatížení. Můžete zřídit a nasadit Škálovací sady virtuálních počítačů pomocí [šablon Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates). Šablony Azure Resource Manageru můžete nasadit s využitím rozhraní příkazového řádku Azure, PowerShell, REST a také přímo ze sady Visual Studio. Visual Studio poskytuje sadu ukázkových šablon, které můžete nasadit jako součást projekt nasazení skupiny prostředků Azure.

Nasazení skupiny prostředků Azure představují způsob, jak seskupit a publikovat sady souvisejících prostředků Azure v rámci operace jedno nasazení. Další informace o nich zde: [vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Požadavky
Abyste mohli začít nasazení Škálovací sady virtuálních počítačů v sadě Visual Studio, budete potřebovat následující:

* Visual Studio 2013 nebo novější
* Azure SDK 2.7, 2.8 nebo 2.9

>[!NOTE]
>Tyto pokyny předpokládají, že používáte Visual Studio s [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Vytvoření projektu
1. Vytvoření nového projektu v sadě Visual Studio výběrem **soubor | Nové | Projekt**.
   
    ![Nový soubor][file_new]

2. V části **Visual C# | Cloud**, zvolte **Azure Resource Manageru** vytvoření projektu pro nasazení šablony Azure Resource Manageru.
   
    ![Vytvoření projektu][create_project]

3. V seznamu šablon vyberte Linuxu nebo Windows nastavte šablony škálování virtuálního počítače.
   
   ![Vybrat šablonu][select_Template]

4. Po vytvoření projektu zobrazí nasazení skriptů prostředí PowerShell, šablony Azure Resource Manageru a soubor parametrů pro Škálovací sady virtuálních počítačů.
   
    ![Průzkumník řešení][solution_explorer]

## <a name="customize-your-project"></a>Přizpůsobení vašeho projektu
Nyní můžete upravit šablonu, kterou chcete přizpůsobit pro potřeby vaší aplikace, jako je například přidávání vlastností rozšíření virtuálního počítače nebo úpravě pravidel Vyrovnávání zatížení. Ve výchozím nastavení jsou šablony sad škálování virtuálních počítačů nakonfigurovat pro nasazení AzureDiagnostics rozšíření, které umožňuje snadno přidat pravidla automatického škálování. Kromě toho nasadí nástroj pro vyrovnávání zatížení s použitím veřejné IP adresy, nakonfigurovanou pravidla příchozího překladu adres. 

Nástroje pro vyrovnávání zatížení vám umožní připojit se k instancím virtuálních počítačů s SSH (Linux) nebo protokol RDP (Windows). Rozsah front-endových portů začíná 50000. Pro linux, to znamená, že pokud je SSH na port 50000 vám jsou směrovány na portu 22 první virtuální počítač ve Škálovací sadě. Připojení k portu 50001 je směrován na port 22 druhý virtuální počítač a tak dále.

 Dobrým způsobem, jak upravit šablony pomocí sady Visual Studio je použití osnovy JSON pro uspořádání parametrů, proměnné a prostředky. Potom co porozumíte schématu sady Visual Studio může odkazovat si chyby ve vaší šabloně před jejím nasazením.

![Průzkumník JSON][json_explorer]

## <a name="deploy-the-project"></a>Nasazení projektu
1. Nasazení šablony Azure Resource Manageru k vytvoření prostředku Škálovací sady virtuálních počítačů. Klikněte pravým tlačítkem na uzel projektu a zvolte **nasadit | Nové nasazení**.
   
    ![Nasazení šablony][5deploy_Template]
    
2. V dialogovém okně "Nasazení do skupiny prostředků" vyberte své předplatné.
   
    ![Nasazení šablony][6deploy_Template]

3. Z tohoto místa můžete vytvořit skupinu prostředků Azure k nasazení šablony.
   
    ![Nová skupina prostředků][new_resource]

4. Klepnutím na tlačítko **upravit parametry** zadat parametry, které jsou předány do šablony. Zadejte uživatelské jméno a heslo pro operační systém, který je potřebný k vytvoření nasazení. Pokud nemáte k dispozici nástroje PowerShell Tools pro Visual Studio nainstalovali, se doporučuje zkontrolovat **ukládat hesla** k zamezení skryté příkazového řádku Powershellu nebo pomocí [keyvault podporu](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Upravit parametry][edit_parameters]

5. Nyní klikejte na příkaz **nasadit**. **Výstup** okno zobrazuje průběh nasazení. Všimněte si, že akce se provádí **nasadit AzureResourceGroup.ps1** skriptu.
   
   ![Okno výstup][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Zkoumání Škálovací sadu virtuálních počítačů
Po dokončení nasazení můžete zobrazit novou Škálovací sadu virtuálních počítačů v sadě Visual Studio **Průzkumníka cloudu** (aktualizace seznamu). Průzkumník cloudu umožňuje spravovat prostředky Azure v sadě Visual Studio při vývoji aplikace. Můžete také zobrazit, vaše Škálovací sada virtuálních počítačů v [webu Azure portal](https://portal.azure.com) a [Azure Resource Exploreru](https://resources.azure.com/).

![Průzkumník cloudu][cloud_explorer]

 Portál poskytuje nejlepší způsob, jak vizuálně spravovat infrastrukturu Azure s webovým prohlížečem, zatímco Azure Resource Exploreru poskytuje snadný způsob, jak zkoumat a ladit prostředky Azure, poskytuje okno do "zobrazení instance" a také zobrazuje příkazy prostředí PowerShell pro prostředky máte před sebou.

## <a name="next-steps"></a>Další postup
Po úspěšném nasazení Škálovací sady virtuálních počítačů pomocí sady Visual Studio, můžete dále přizpůsobit váš projekt tak, aby vyhovoval vašim požadavkům aplikace. Třeba konfigurace automatického škálování tak, že přidáte **Insights** prostředků infrastruktury přidání do šablony (jako je samostatných virtuálních počítačů), nebo nasazování aplikací pomocí rozšíření vlastních skriptů. Dobrým příkladem šablony lze nalézt v [šablony pro rychlý start Azure](https://github.com/Azure/azure-quickstart-templates) úložiště GitHub (vyhledejte "vmss").

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
