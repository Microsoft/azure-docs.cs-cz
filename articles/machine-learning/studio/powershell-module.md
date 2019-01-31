---
Název: Moduly prostředí PowerShell pro Machine Learning Studio titleSuffix: Azure Machine Learning Studio Popis: Pomocí Powershellu vytvářet a spravovat Azure Machine Learning Studio pracovní prostory, experimenty, webové služby a další. Services: machine learningu ms.service: ms.subservice strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: předchozí ms.author=haining, předchozí Autor = hning86 ms.date: 01/25/2019
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Moduly Powershellu pro Azure Machine Learning Studio

Používání modulů Powershellu, můžete programově spravovat Studio prostředky a prostředky, jako jsou pracovní prostory, datové sady a webových služeb.

Můžete pracovat s prostředky Studio pomocí tří modulů prostředí Powershell:

* [Azure PowerShell Az](#az-rm) vydána v roce 2018, zahrnuje všechny funkce AzureRM, i když se názvy různých rutin
* [AzureRM](#az-rm) vydané v 2016
* [Azure Machine Learning PowerShell classic](#classic) vydané v 2016

I když tyto moduly mají některé podobné rysy, jednotlivé jsou nastavené pro určité scénáře. Tento článek popisuje rozdíly mezi moduly Powershellu a pomůže vám při rozhodování jaké vizualizace zvolit.

## <a name="choosing-modules"></a> Výběr moduly

Volba mezi dostupné moduly Powershellu závisí na typu prostředky, které spravujete.

Zkontrolujte [podporu tabulky](#support-table) dole můžete zjistit, které prostředky jsou podporovány každý modul. Protože PowerShell – classic lze nainstalovat souběžně s Az nebo AzureRM, můžete nainstalovat následující moduly a zahrnují všechny typy prostředků (classic pomocí Az nebo classic pomocí AzureRM)

Však není doporučeno mít Az a AzureRM nainstalované ve stejnou dobu. Při rozhodování mezi Az a AzureRM, společnost Microsoft doporučuje Az pro všechny budoucí nasazení. Použijte AzureRm jenom v případě, že existují ve vašem prostředí zvláštní okolnosti, které to vyžadují.

Další informace o rozdílech mezi Az a AzureRM, jakož i naše cesta zadaná migrace, najdete v našich [Úvod do prostředí Azure PowerShell Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

## <a name="az-rm"></a> Azure PowerShell Az a AzureRM

AZ a AzureRM i spravovat nasazené pomocí řešení **Azure Resource Manageru** modelu nasazení. Tyto prostředky zahrnují pracovní prostory Studio a Studio nových webových služeb. Spravovat prostředky nasazené pomocí modelu nasazení classic, abyste používali modul PowerShell classic. Pokud chcete získat další informace o modelech nasazení, najdete v článku [Azure Resource Manageru a klasického nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) článku.

AZ je určený modul prostředí PowerShell pro interakci s Azure a obsahuje všechny předchozí funkce AzureRM. AzureRM budou dál dostávat opravy chyb, ale bude přijímat žádné nové rutiny nebo funkce. I když dojde upgradu z AzureRM, pokud narazíte na potíže s Az při práci se službou Studio, nahlásit problém a vrátí k používání AzureRM.

Pokud chcete začít pracovat s Az, postupujte [pokyny k instalaci pro Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell – classic

Sady Studio [modulu PowerShell classic](https://aka.ms/amlps) umožňuje spravovat prostředky nasazené pomocí **modelu nasazení classic**. Tyto prostředky zahrnují Studio prostředky uživatele, klasické webové služby a koncových bodů klasické webové služby.

Společnost Microsoft doporučuje používat model nasazení Resource Manageru pro všechny nové prostředky pro zjednodušení nasazení a správu prostředků. Pokud chcete získat další informace o modelech nasazení, najdete v článku [Azure Resource Manageru a klasického nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) článku.

Chcete-li začít používat PowerShell – classic, stáhněte si [balíček verze](https://github.com/hning86/azuremlps/releases) z Githubu a postupujte podle [pokyny pro instalaci](https://github.com/hning86/azuremlps/blob/master/README.md). Pokyny vysvětlují, jak odblokovat DLL stažené/odblokujte a potom ho importujte do prostředí PowerShell.

## <a name="support-table"></a> Tabulka podporu prostředí PowerShell

 **Pracovní prostory Studio** | **Az** |  **AzureRM** | **PowerShell – Classic** |
| --- | --- | --- | --- | --- |
| Vytvoření nebo odstranění pracovních prostorů | [Šablony Resource Manageru](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Šablony Resource Manageru](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Správa uživatelů pracovního prostoru |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Správa plánů závazku | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Webové služby** | **Az** | **AzureRM** | **PowerShell – Classic** |
| Správa webových služeb | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (Nové webové služby) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (Nové webové služby) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (klasické webové služby) |
| Správa koncových bodů/klíče |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (Nové webové služby) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (Nové webové služby) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (klasické webové služby) |
|||
| **Prostředky uživatele** | **Az** | **AzureRM** | **PowerShell – Classic** |
| Správa modelů trénovaných/datové sady |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Správa experimentů |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Správa vlastních modulů |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Další postup
Úplnou dokumentaci pro moduly Powershellu na následujících odkazech:
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell – Classic](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
