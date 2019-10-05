---
title: Přehled Azure DevOps Projects | Microsoft Docs
description: Pochopení hodnoty Azure DevOps Projects
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: d39702f8e30046dd3cf634fc67ed7095471cd629
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971475"
---
# <a name="overview-of-azure-devops-projects"></a>Přehled Azure DevOps Projects

 Azure DevOps Projects usnadňuje začátek práce s Azure. Pomůže vám spustit svou oblíbenou aplikaci ve vaší zvolené službě Azure v několika rychlých krocích od Azure Portal. 

 DevOps Projects nastaví vše, co potřebujete pro vývoj, nasazování a monitorování vaší aplikace. Pomocí řídicího panelu DevOps Projects můžete monitorovat potvrzení kódu, sestavení a nasazení, a to vše z jednoho zobrazení v Azure Portal.

## <a name="why-should-i-use-devops-projects"></a>Proč mám použít DevOps Projects?

  DevOps projekt automatizuje nastavení kompletního kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.  Můžete začít s existujícím kódem nebo použít jednu z poskytnutých ukázkových aplikací. Pak můžete tuto aplikaci rychle nasadit do různých služeb Azure, jako jsou Virtual Machines, App Service, Azure Kubernetes Services (AKS), Azure SQL Database a Azure Service Fabric.  

  DevOps Projects provádí veškerou práci při počáteční konfiguraci kanálu DevOps, včetně všeho od nastavení počátečního úložiště Git, konfigurace kanálu CI/CD, vytvoření prostředku Application Insights pro monitorování a poskytování jednoho zobrazení celého řešení s vytvořením DevOps Projectsho řídicího panelu v Azure Portal.

DevOps Projects můžete použít k těmto akcím:

* Rychle nasadit aplikace do Azure
* Automatizace nastavení kanálu CI/CD
* Zobrazení a pochopení způsobu správného nastavení kanálu CI/CD
* Další přizpůsobení kanálů vydání na základě konkrétních scénářů

## <a name="how-do-i-use-devops-projects"></a>Návody použít DevOps Projects?

  DevOps Projects je k dispozici z Azure Portal. Prostředek DevOps Projects vytvoříte stejným způsobem jako jiný prostředek Azure z portálu. DevOps Projects pro různé možnosti konfigurace poskytuje podrobné prostředí s podobným průvodcem.  

V rámci počátečního nastavení zvolíte několik možností konfigurace. K těmto možnostem patří:

* Použití poskytnuté ukázkové aplikace nebo uvedení vlastního kódu
* Výběr jazyka aplikace
* Výběr architektury aplikace založené na jazyku
* Výběr služby Azure (cíl nasazení)
* Vytvoření nové organizace Azure DevOps nebo použití stávající organizace 
* Volba předplatného Azure
* Výběr umístění služeb Azure
* Výběr z různých cenových úrovní pro služby Azure

Po použití DevOps Projects můžete také odstranit všechny prostředky z jednoho místa z řídicího panelu DevOps Projects na Azure Portal.

## <a name="devops-projects-and-azure-devops-integration"></a>Integrace DevOps Projects a Azure DevOps

DevOps Projects využívá Azure DevOps. DevOps Projects automatizuje veškerou práci, která je potřebná v Azure Pipelines k nastavení kanálu CI/CD. Vytvoří úložiště Git v nové nebo existující organizaci Azure DevOps a pak potvrdí ukázkovou aplikaci nebo svůj existující kód do nového úložiště Git.  

Automatizace také naváže Trigger CI pro sestavení tak, že každé nové potvrzení kódu inicializuje sestavení. DevOps Projects vytvoří Trigger CD a nasadí každé nové úspěšné sestavení do služby Azure podle vašeho výběru.  

Kanály buildu a verze se dají přizpůsobit pro další scénáře. Kromě toho můžete klonovat kanály sestavení a vydání pro použití v jiných projektech.

Po vytvoření projektu DevOps můžete:

* Přizpůsobit kanál sestavení a verzí
* Pomocí žádostí o přijetí změn spravovat tok kódu a udržovat vysokou kvalitu
* Testovat a sestavovat každé potvrzení před sloučením kódu kvůli zvýšení kvality
* Sledujte své nevyřízené položky a problémy přímo spolu s vaší aplikací.

## <a name="how-do-i-start-using-devops-projects"></a>Návody začít používat DevOps Projects?

* [Začínáme s DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>DevOps Projects videa

* [Vytvoření CI/CD pomocí Azure DevOps Projects](https://www.youtube.com/watch?v=NuYDAs3kNV8)
