---
title: Přehled služby Azure DevOps Starter | Microsoft Docs
description: Přečtěte si, jak vám Azure DevOps Starter pomůže spouštět, nasazovat a spravovat vaše aplikace z jednoho zobrazení Azure Portal.
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
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 91e62cae242279e1923fc31970c233ec70bf7f11
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87461336"
---
# <a name="overview-of-azure-devops-starter"></a>Přehled služby Azure DevOps Starter

 Azure DevOps Starter usnadňuje začátek práce v Azure. Pomůže vám spustit svou oblíbenou aplikaci ve vaší zvolené službě Azure v několika rychlých krocích od Azure Portal. 

 DevOps Starter nastaví vše, co potřebujete pro vývoj, nasazování a monitorování vaší aplikace. Pomocí řídicího panelu DevOps Starter můžete monitorovat potvrzení kódu, sestavení a nasazení, a to vše z jednoho zobrazení v Azure Portal.

## <a name="advantages-of-using-devops-starter"></a>Výhody používání DevOps Starter

  DevOps Starter automatizuje automatickou instalaci celého kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.  Můžete začít s existujícím kódem nebo použít jednu z poskytnutých ukázkových aplikací. Pak můžete tuto aplikaci rychle nasadit do různých služeb Azure, jako jsou Virtual Machines, App Service, Azure Kubernetes Services (AKS), Azure SQL Database a Azure Service Fabric.  

  DevOps Starter provádí veškerou práci při počáteční konfiguraci kanálu DevOps, včetně všeho od nastavení počátečního úložiště Git, konfigurace kanálu CI/CD, vytvoření prostředku Application Insights pro monitorování a poskytování jediného zobrazení celého řešení s vytvořením řídicího panelu DevOps Projects v Azure Portal.

DevOps Starter můžete použít k těmto akcím:

* Rychle nasadit aplikace do Azure
* Automatizace nastavení kanálu CI/CD
* Zobrazení a pochopení způsobu správného nastavení kanálu CI/CD
* Další přizpůsobení kanálů vydání na základě konkrétních scénářů

## <a name="how-to-use-devops-starter"></a>Jak používat DevOps Starter?

  DevOps Starter je k dispozici z Azure Portal. Vytvoříte DevOps Starter Resource stejným způsobem jako jiný prostředek Azure z portálu. DevOps Projects pro různé možnosti konfigurace poskytuje podrobné prostředí s podobným průvodcem.  

V rámci počátečního nastavení zvolíte několik možností konfigurace. K těmto možnostem patří:

* Použití poskytnuté ukázkové aplikace nebo uvedení vlastního kódu
* Výběr jazyka aplikace
* Výběr architektury aplikace založené na jazyku
* Výběr služby Azure (cíl nasazení)
* Vytvoření nové organizace Azure DevOps nebo použití stávající organizace 
* Volba předplatného Azure
* Výběr umístění služeb Azure
* Výběr z různých cenových úrovní pro služby Azure

Po použití DevOps Starter můžete také odstranit všechny prostředky z jednoho místa na řídicím panelu DevOps Starter na Azure Portal.

## <a name="devops-starter-and-azure-devops-integration"></a>Integrace DevOps Starter a Azure DevOps

DevOps Starter využívá Azure DevOps. DevOps Starter automaticky automatizuje veškerou práci, která je potřeba v Azure Pipelines k nastavení kanálu CI/CD. Vytvoří úložiště Git v nové nebo existující organizaci Azure DevOps a pak potvrdí ukázkovou aplikaci nebo svůj existující kód do nového úložiště Git.  

Automatizace také naváže Trigger CI pro sestavení tak, že každé nové potvrzení kódu inicializuje sestavení. DevOps Starter vytvoří Trigger CD a nasadí každé nové úspěšné sestavení do služby Azure podle vašeho výběru.  

Kanály buildu a verze se dají přizpůsobit pro další scénáře. Kromě toho můžete klonovat kanály sestavení a vydání pro použití v jiných projektech.

Po vytvoření DevOps Starter můžete:

* Přizpůsobit kanál sestavení a verzí
* Pomocí žádostí o přijetí změn spravovat tok kódu a udržovat vysokou kvalitu
* Testovat a sestavovat každé potvrzení před sloučením kódu kvůli zvýšení kvality
* Sledujte své nevyřízené položky a problémy přímo spolu s vaší aplikací.

## <a name="getting-started-with-devops-starter"></a>Začínáme s DevOps Starter

* [Začínáme s DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>Úvodní videa pro DevOps

* [Vytvoření CI/CD s využitím Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
