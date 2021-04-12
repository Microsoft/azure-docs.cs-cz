---
title: Přehled DevOps Starter pro Azure | Microsoft Docs
description: Pochopení hodnoty pro DevOps Starter
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
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330644"
---
# <a name="overview-of-devops-starter"></a>Přehled DevOps Starter

 DevOps Starter usnadňuje začátek práce s Azure pomocí akcí GitHubu nebo Azure DevOps. Pomůže vám spustit svou oblíbenou aplikaci ve vaší zvolené službě Azure v několika rychlých krocích od Azure Portal. 

 DevOps Starter nastaví vše, co potřebujete pro vývoj, nasazování a monitorování vaší aplikace. Pomocí řídicího panelu DevOps Starter můžete monitorovat potvrzení kódu, sestavení a nasazení, a to vše z jednoho zobrazení v Azure Portal.

## <a name="advantages-of-using-devops-starter"></a>Výhody používání DevOps Starter

  DevOps Starter následující podporuje pro automatizaci nasazení dva poskytovatele CI/CD.
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter automaticky automatizuje nastavení celé průběžné integrace (CI) a průběžného doručování (CD) pro vaši aplikaci do Azure.  Můžete začít s existujícím kódem nebo použít jednu z poskytnutých ukázkových aplikací. Pak můžete tuto aplikaci rychle nasadit do různých služeb Azure, jako jsou Virtual Machines, App Service, Azure Kubernetes Services (AKS), Azure SQL Database a Azure Service Fabric.  

  DevOps Starter provádí veškerou práci při počáteční konfiguraci kanálu DevOps, včetně všeho od nastavení počátečního úložiště Git, konfigurace kanálu CI/CD, vytvoření prostředku Application Insights pro monitorování a poskytování jediného zobrazení celého řešení s vytvořením řídicího panelu DevOps Starter v Azure Portal.

DevOps Starter můžete použít k těmto akcím:

* Rychle nasadit aplikace do Azure
* Automatizace nastavení pracovního postupu nebo kanálu CI/CD
* Zobrazit a pochopit, jak správně nastavit pracovní postup CI/CD nebo kanál
* Další přizpůsobení kanálů vydání na základě konkrétních scénářů

## <a name="how-to-use-devops-starter"></a>Jak používat DevOps Starter?

  DevOps Starter je k dispozici z Azure Portal. Vytvoříte DevOps Starter Resource stejným způsobem jako jiný prostředek Azure z portálu. DevOps Projects pro různé možnosti konfigurace poskytuje podrobné prostředí s podobným průvodcem.  

V rámci počátečního nastavení zvolíte několik možností konfigurace. K těmto možnostem patří:

* Výběr preferovaného poskytovatele CI/CD
* Použití poskytnuté ukázkové aplikace nebo uvedení vlastního kódu (pouze pro Azure DevOps)
* Výběr jazyka aplikace
* Výběr architektury aplikace založené na jazyku
* Výběr služby Azure (cíl nasazení)
* Výběr vaší organizace GitHub nebo Azure DevOps
* Volba předplatného Azure
* Výběr umístění služeb Azure
* Výběr z různých cenových úrovní pro služby Azure

Po vytvoření DevOps Starter můžete:

* Přizpůsobení pracovního postupu GitHubu nebo kanálu Azure DevOps
* Pomocí žádostí o přijetí změn spravovat tok kódu a udržovat vysokou kvalitu
* Testovat a sestavovat každé potvrzení před sloučením kódu kvůli zvýšení kvality

Po použití DevOps Starter můžete také odstranit všechny prostředky z jednoho místa na řídicím panelu DevOps Starter na Azure Portal.

## <a name="devops-starter-and-github-integration"></a>Integrace DevOps Starter a GitHubu

DevOps Starter teď podporuje akce GitHubu jako poskytovatele CI/CD. Automatizuje veškerou práci potřebnou v GitHubu a nastaví pracovní postup CI/CD pomocí akcí GitHubu. Vytvoří úložiště GitHub v existující organizaci GitHubu a pak do nového úložiště GitHubu potvrdí ukázkovou aplikaci.  

Automatizace také vytváří Trigger pro pracovní postup, aby každé nové potvrzení kódu inicializoval úlohu sestavení a nasazení v rámci pracovního postupu. Aplikace se nasadí do služby Azure podle vašeho výběru. Pracovní postup GitHubu je možné přizpůsobit pro další scénáře. 

## <a name="devops-starter-and-azure-devops-integration"></a>Integrace DevOps Starter a Azure DevOps

DevOps Starter s využitím Azure DevOps automatizuje veškerou práci potřebnou v Azure Pipelines k nastavení kanálu CI/CD. Vytvoří úložiště Git v nové nebo existující organizaci Azure DevOps a pak potvrdí ukázkovou aplikaci nebo svůj existující kód do nového úložiště Git.  

Automatizace také naváže Trigger CI pro sestavení tak, že každé nové potvrzení kódu inicializuje sestavení. DevOps Starter vytvoří Trigger CD a nasadí každé nové úspěšné sestavení do služby Azure podle vašeho výběru.  

Kanály buildu a verze se dají přizpůsobit pro další scénáře. Kromě toho můžete klonovat kanály sestavení a vydání pro použití v jiných projektech.

## <a name="getting-started-with-devops-starter"></a>Začínáme s DevOps Starter

* [Začínáme s DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Úvodní videa pro DevOps

* [Vytvoření CI/CD s využitím Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
