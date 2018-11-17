---
title: Modul prostředí PowerShell pro Machine Learning | Dokumentace Microsoftu
description: Modul PowerShell pro Azure Machine Learning je dostupný v režimu veřejné zkušební verze. Pomocí Powershellu vytvářet a spravovat pracovní prostory, experimenty, webové služby a další.
keywords: experiment,lineární regrese,algoritmy Machine Learningu,kurz Machine Learningu,techniky prediktivního modelování,experiment z oblasti datové vědy
services: machine-learning
documentationcenter: ''
author: hning86
ms.custom: (previous ms.author haining)
ms.author: amlstudiodocs
manager: mwinkle
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.openlocfilehash: c46b99de8a93c54246d52a3cb369fd926e7c60cd
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822037"
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Modul PowerShell pro Microsoft Azure Machine Learning
Modul PowerShell pro Azure Machine Learning je výkonný nástroj, který umožňuje použití Windows Powershellu ke správě pracovní prostory, experimenty, datové sady, klasické webové služby a další.

Můžete zobrazit dokumentaci a stáhnout modul včetně celého zdrojového kódu na [ https://aka.ms/amlps ](https://aka.ms/amlps). 

> [!NOTE]
> Modul Azure Machine Learning PowerShell je aktuálně v režimu náhledu. Modul bude nadále možné vylepšován a rozšiřován i během této doby ve verzi preview. Měli přehled o prostředcích [Cortana Intelligence a Machine Learning Blog](https://blogs.technet.microsoft.com/machinelearning/) pro zprávy a informace.

## <a name="what-is-the-machine-learning-powershell-module"></a>Co je modul Machine Learning PowerShell?
Modul Machine Learning PowerShell je. Na základě NET modul knihovny DLL, která umožňuje plně spravovat pracovní prostory Azure Machine Learning, experimenty, datové sady, klasické webové služby a koncových bodů webové služby Classic z prostředí Windows PowerShell. 

Kromě modulu si můžete stáhnout úplný zdrojový kód, který obsahuje čistě oddělených [ C# vrstvu rozhraní API](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Můžete odkazovat na tuto knihovnu DLL z vlastního .NET projektu a spravovat Azure Machine Learning prostřednictvím kódu rozhraní .NET. Kromě toho závisí na dalších rozhraních REST API, které můžete použít přímo ze svého oblíbeného klienta.

## <a name="what-can-i-do-with-the-powershell-module"></a>Co můžu s modulem PowerShell dělat?
S modulem PowerShell můžete provádět například některé z těchto úloh. Tyto a mnoho dalších funkcí najdete také v [úplné dokumentaci](https://aka.ms/amlps).

* Zřiďte nový pracovní prostor s pomocí certifikátu pro správu ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace)).
* Exportujte a importujte soubor JSON s grafem experimentu ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) a [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph)).
* Spusťte experiment ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment)).
* Z prediktivního experimentu vytvořte webovou službu ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice)).
* Vytvořit koncový bod v publikované webové službě ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Zavolejte koncový bod webové služby RRS či BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) a [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)).

Na jednoduchém příkladu si můžeme ukázat, jak se pomocí PowerShell spustí existující experiment:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Podrobnější případ použití, najdete v článku o použití modulu Powershellu pro automatizaci často požadované úlohy: [vytvoření mnoha modelů Machine Learning a webové koncové body služby z jednoho experimentu pomocí prostředí PowerShell](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Jak mám začít?
Pokud chcete začít s Machine Learning PowerShell, stáhněte si [vydaný balíček](https://github.com/hning86/azuremlps/releases) z GitHubu a postupujte podle [instalačních pokynů](https://github.com/hning86/azuremlps/blob/master/README.md). Pokyny vysvětlují, jak odblokovat DLL stažené/odblokujte a potom ho importujte do prostředí PowerShell. Většina rutin vyžaduje, abyste zadali ID pracovního prostoru, jeho autorizační token a oblast Azure, ve které se pracovní prostor nachází. Nejjednodušší způsob, jak zadat hodnoty je prostřednictvím výchozího souboru config.json. Podle pokynů také vysvětlují, jak nakonfigurovat tento soubor. 

Pokud chcete, můžete naklonovat strom systému git, upravit kód a kompilovat místně pomocí sady Visual Studio.

## <a name="next-steps"></a>Další postup
Najdete kompletní dokumentaci k modulu PowerShell na [ https://aka.ms/amlps ](https://aka.ms/amlps). 

Rozšířený příklad toho, jak použít modul ve skutečném scénáři, podívejte se na případ podrobné použití [vytvoření mnoha modelů Machine Learning a webové koncové body služby z jednoho experimentu pomocí prostředí PowerShell](create-models-and-endpoints-with-powershell.md).
