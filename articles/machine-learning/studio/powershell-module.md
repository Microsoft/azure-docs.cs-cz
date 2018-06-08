---
title: Modul prostředí PowerShell pro Machine Learning | Microsoft Docs
description: Modul PowerShell pro Azure Machine Learning je dostupný v režimu veřejné zkušební verze. Použijte PowerShell k vytváření a správě pracovní prostory, experimenty, webové služby a další.
keywords: experiment,lineární regrese,algoritmy Machine Learningu,kurz Machine Learningu,techniky prediktivního modelování,experiment z oblasti datové vědy
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
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
ms.openlocfilehash: 6ecd2d9a1519cd89058385ad1e40aee9b3fc9082
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835691"
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Modul PowerShell pro Microsoft Azure Machine Learning
Modul prostředí PowerShell pro Azure Machine Learning je výkonný nástroj, který umožňuje spravovat pracovní prostory, experimenty, datové sady, Classic webové služby a další pomocí prostředí Windows PowerShell.

Můžete zobrazit dokumentaci a stáhnout modul, společně s úplný zdrojový kód, na adrese [ https://aka.ms/amlps ](https://aka.ms/amlps). 

> [!NOTE]
> Modul Powershellu pro Azure Machine Learning je aktuálně v režimu preview. Modul bude třeba zlepšit a rozšířit během tohoto období preview. Dohlížet na [Cortana Intelligence a Machine Learning Blog](https://blogs.technet.microsoft.com/machinelearning/) pro příspěvky a informace.

## <a name="what-is-the-machine-learning-powershell-module"></a>Co je modul Machine Learning PowerShell?
Modul Machine Learning PowerShell. Na základě NET DLL modul, který vám umožní plně spravovat pracovních prostorů Azure Machine Learning, experimenty, datové sady, Classic webové služby a koncových bodů webové služby Classic z prostředí Windows PowerShell. 

Spolu s modulem, si můžete stáhnout úplný zdrojový kód, který zahrnuje řádně oddělených [vrstvu rozhraní API jazyka C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Můžete odkazovat na tuto knihovnu DLL z projektu rozhraní .NET a spravovat Azure Machine Learning prostřednictvím rozhraní .NET kódu. Kromě toho knihovnu DLL, závisí na základní rozhraní REST API, který můžete použít přímo z vašeho oblíbeného klienta.

## <a name="what-can-i-do-with-the-powershell-module"></a>Co můžu s modulem PowerShell dělat?
S modulem PowerShell můžete provádět například některé z těchto úloh. Tyto a mnoho dalších funkcí najdete také v [úplné dokumentaci](https://aka.ms/amlps).

* Zřiďte nový pracovní prostor s pomocí certifikátu pro správu ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace)).
* Exportujte a importujte soubor JSON s grafem experimentu ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) a [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph)).
* Spusťte experiment ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment)).
* Z prediktivního experimentu vytvořte webovou službu ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice)).
* Vytvoření koncového bodu na publikované webové službě ([přidat AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Zavolejte koncový bod webové služby RRS či BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) a [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)).

Na jednoduchém příkladu si můžeme ukázat, jak se pomocí PowerShell spustí existující experiment:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Podrobnější případu použití, najdete v článku na modulu PowerShell pro automatizaci úloh běžně požadovaný: [vytvořit mnoho modely Machine Learning a webové koncové body služby z jednoho experimentu pomocí prostředí PowerShell](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Jak mám začít?
Pokud chcete začít s Machine Learning PowerShell, stáhněte si [vydaný balíček](https://github.com/hning86/azuremlps/releases) z GitHubu a postupujte podle [instalačních pokynů](https://github.com/hning86/azuremlps/blob/master/README.md). Pokyny popisují, jak odblokovat stáhli rozbalené DLL a importujte ho do prostředí PowerShell. Většina rutin vyžaduje, abyste zadali ID pracovního prostoru, jeho autorizační token a oblast Azure, ve které se pracovní prostor nachází. Prostřednictvím výchozí config.json soubor, který je nejjednodušší způsob, jak tyto hodnoty zadat ručně. Podle pokynů také vysvětlují, jak nakonfigurovat tento soubor. 

Pokud chcete, můžete naklonovat stromu git upravit kód a zkompilovat ho místně pomocí sady Visual Studio.

## <a name="next-steps"></a>Další postup
Úplnou dokumentaci pro modul PowerShell na můžete najít [ https://aka.ms/amlps ](https://aka.ms/amlps). 

Příklad rozšířené informace o použití modulu ve scénáři reálného, podívejte se na případ podrobné použití, [vytvořit mnoho modely Machine Learning a webové koncové body služby z jednoho experimentu pomocí prostředí PowerShell](create-models-and-endpoints-with-powershell.md).
