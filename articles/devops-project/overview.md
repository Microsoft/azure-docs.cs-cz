---
title: Přehled projektů Azure DevOps | Dokumenty společnosti Microsoft
description: Principy hodnoty pro projekty Azure DevOps
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "71971475"
---
# <a name="overview-of-azure-devops-projects"></a>Přehled projektů Azure DevOps

 Azure DevOps Projects usnadňuje schůdky v Azure. Pomůže vám spustit oblíbenou aplikaci ve službě Azure podle vašeho výběru v několika rychlých krocích z webu Azure Portal. 

 DevOps Projects nastavuje vše, co potřebujete pro vývoj, nasazování a monitorování vaší aplikace. Pomocí řídicího panelu DevOps Projects můžete monitorovat potvrzení kódu, sestavení a nasazení, to vše z jednoho zobrazení na webu Azure Portal.

## <a name="why-should-i-use-devops-projects"></a>Proč bych měl používat DevOps projekty?

  DevOps Project automatizuje nastavení celého kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.  Můžete začít s existujícím kódem nebo použít některou z poskytnutých ukázkových aplikací. Pak můžete tuto aplikaci rychle nasadit do různých služeb Azure, jako jsou virtuální počítače, služba app service, služby Azure Kubernetes Services (AKS), Azure SQL Database a Azure Service Fabric.  

  DevOps Projects provádí veškerou práci pro počáteční konfiguraci kanálu DevOps, včetně všeho od nastavení počátečního úložiště Git, konfigurace kanálu CI/CD, vytvoření prostředku Application Insights pro monitorování a poskytnutí jediného zobrazení celého řešení s vytvořením řídicího panelu DevOps Projects na portálu Azure.

Projekty DevOps můžete použít k tomuto:

* Rychle nasadit aplikace do Azure
* Automatizace nastavení kanálu CI/CD
* Zobrazení a pochopení správného nastavení kanálu CI/CD
* Další přizpůsobení kanálů verzí na základě konkrétních scénářů

## <a name="how-do-i-use-devops-projects"></a>Jak se používají devops projekty?

  DevOps Projekty je k dispozici na webu Azure Portal. Můžete vytvořit prostředek DevOps Projekty stejně jako vytvořit jakýkoli jiný prostředek Azure z portálu. Projekty DevOps poskytuje podrobné prostředí podobné průvodcům pro různé možnosti konfigurace.  

V rámci počátečního nastavení zvolíte několik možností konfigurace. K těmto možnostem patří:

* Použití poskytnuté ukázkové aplikace nebo přinášení vlastního kódu
* Výběr jazyka aplikace
* Výběr architektury aplikace na základě jazyka
* Výběr služby Azure (cíl nasazení)
* Vytvoření nové organizace Azure DevOps nebo použití existující organizace 
* Výběr předplatného Azure
* Výběr umístění služeb Azure
* Výběr z různých cenových úrovní pro služby Azure

Po použití DevOps projekty, můžete také odstranit všechny prostředky z jednoho místa z řídicího panelu DevOps projekty na portálu Azure.

## <a name="devops-projects-and-azure-devops-integration"></a>DevOps Projekty a Integrace Azure DevOps

DevOps Projekty je založen na Azure DevOps. DevOps Projekty automatizuje všechny práce, které jsou potřebné v Azure Pipelines nastavit kanál CI/CD. Vytvoří úložiště Git v nové nebo existující organizaci Azure DevOps a pak potvrdí ukázkovou aplikaci nebo váš stávající kód do nového úložiště Git.  

Automatizace také vytvoří aktivační událost CI pro sestavení tak, aby každé potvrzení nového kódu iniciovalo sestavení. DevOps Projects vytvoří aktivační událost CD a nasadí každé nové úspěšné sestavení do služby Azure podle vašeho výběru.  

Kanály buildu a verze se dají přizpůsobit pro další scénáře. Kromě toho můžete klonovat sestavení a uvolnění kanály pro použití v jiných projektech.

Po vytvoření projektu DevOps můžete:

* Přizpůsobit kanál sestavení a verzí
* Pomocí žádostí o přijetí změn spravovat tok kódu a udržovat vysokou kvalitu
* Testovat a sestavovat každé potvrzení před sloučením kódu kvůli zvýšení kvality
* Sledujte nevyřízené položky a problémy spolu s vaší aplikací

## <a name="how-do-i-start-using-devops-projects"></a>Jak začít používat projekty DevOps?

* [Začínáme s projekty DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>Videa devOps Projekty

* [Vytvoření CI/CD s projekty Azure DevOps](https://www.youtube.com/watch?v=NuYDAs3kNV8)
