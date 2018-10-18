---
title: Přehled projektu Azure DevOps | Microsoft Docs
description: Vysvětlení výhod projektu Azure DevOps
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: 39dffad597b8382dea4df6fa1b0726d9582d67d1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44293622"
---
# <a name="overview-of-azure-devops-project"></a>Přehled projektu Azure DevOps

Díky projektu Azure DevOps můžete v Azure snadno začít pracovat. Prostředek Projekt DevOps umožňuje spustit váš oblíbený typ aplikace ve službě Azure podle vlastního výběru pomocí několika rychlých kroků na webu Azure Portal. Projekt DevOps nastaví vše potřebné pro vývoj, nasazení a monitorování aplikace.
Řídicí panel projektu DevOps umožňuje monitorovat potvrzení kódu, sestavení a nasazení z jednoho zobrazení na portálu Azure Portal.

## <a name="why-should-i-use-the-azure-devops-project"></a>Proč projekt Azure DevOps používat?

Projekt Azure DevOps automatizuje nastavení celého kanálu kontinuální integrace (CI) a průběžného doručování (CD) do Azure.  Můžete začít se svým stávajícím kódem nebo použít některou z poskytovaných ukázkových aplikací a pak ji rychle nasadit do různých služeb Azure, jako jsou virtuální počítače, App Service, Azure Container Service, Azure SQL Database a Azure Service Fabric.  

Projekt Azure DevOps odvede všechnu práci s počáteční konfigurací kanálu DevOps včetně všeho od nastavení počátečního úložiště Git přes konfiguraci kanálu CI/CD, vytvoření prostředku Application Insights pro monitorování až po zobrazení celého řešení na jednom místě v řídicím panelu projektu Azure DevOps na webu Azure Portal.

Projekt Azure DevOps vám umožní:

* Rychle nasadit aplikace do Azure
* Automatizovat nastavení kanálu Azure CI/CD
* Použít projekt DevOps jako šablonu, která vám umožní zjistit a pochopit, jak správně nastavit CI/CD do Azure pomocí Azure DevOps
* Začít s kanálem CI/CD do Azure a pak kanál verze dál přizpůsobit podle konkrétních scénářů

## <a name="how-do-i-use-the-azure-devops-project"></a>Jak projekt Azure DevOps používat?

Projekt Azure DevOps je k dispozici na portálu Azure Portal.  Prostředek Projekt Azure DevOps vytvoříte stejně jako jakýkoli jiný prostředek Azure z portálu.  Projekt DevOps nabízí prostředí podobné průvodci s podrobnými kroky pro různé možnosti konfigurace.  

V rámci počátečního nastavení zvolíte několik možností konfigurace.  K těmto možnostem patří:

* Použití poskytnuté ukázková aplikace nebo vlastního kódu
* Zvolení jazyka aplikace
* Zvolení rozhraní aplikace na základě jazyka
* Zvolení služby Azure (cíle nasazení)
* Organizace Azure DevOps (nová nebo existující)
* Zvolte svoje předplatné Azure.
* Výběr umístění služeb Azure
* Výběr z různých cenových úrovní služeb Azure

Po použití projektu Azure DevOps Azure můžete také všechny prostředky odstranit z jednoho místa v řídicím panelu projektu Azure DevOps na portálu Azure Portal.

## <a name="azure-devops-project-and-azure-devops-integration"></a>Integrace projektu Azure DevOps a Azure DevOps

Projekty DevOps využívají Azure DevOps.  Projekt DevOps automatizuje všechnu práci, která se v Azure DevOps vyžaduje k nastavení CI/CD do Azure.  V nové nebo existující organizaci Azure DevOps se vytvoří úložiště Git.  Projekt DevOps potvrdí ukázkovou aplikaci nebo váš stávající kód do nového úložiště Git.  Automatizace také vytvoří aktivační událost CI pro sestavení tak, aby každé nové potvrzení kódu inicializovalo sestavení.  Projekt DevOps také vytvoří trigger CD a nasadí každý nový úspěšný build do služby Azure podle vašeho výběru.  Kanály buildu a verze se dají přizpůsobit pro další scénáře.  Kanály buildu a verze můžete také naklonovat a použít v jiných projektech.

Po vytvoření projektu DevOps můžete:

* Přizpůsobit kanál sestavení a verzí
* Pomocí žádostí o přijetí změn spravovat tok kódu a udržovat vysokou kvalitu
* Testovat a sestavovat každé potvrzení před sloučením kódu kvůli zvýšení kvality
* Sledovat backlog a problémy projektu přímo s aplikací

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Jak projekt Azure DevOps začít používat?

* [Začínáme s projektem Azure DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Videa pro projekt Azure DevOps

* [Vytvoření CI/CD s využitím projektu Azure DevOps](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
