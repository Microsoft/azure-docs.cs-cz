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
ms.openlocfilehash: 992b469803b26a11e7ca26142beba7d238e25452
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952207"
---
# <a name="overview-of-azure-devops-project"></a>Přehled projektu Azure DevOps

Díky projektu Azure DevOps můžete v Azure snadno začít pracovat. Prostředek projektu DevOps umožňuje spustit váš oblíbený typ aplikace ve službě Azure podle vlastního výběru pomocí několika rychlých kroků na portálu Azure Portal. Projekt DevOps nastaví vše potřebné pro vývoj, nasazení a monitorování aplikace.
Řídicí panel projektu DevOps umožňuje monitorovat potvrzení kódu, sestavení a nasazení z jednoho zobrazení na portálu Azure Portal.

## <a name="why-should-i-use-the-azure-devops-project"></a>Proč projekt Azure DevOps používat?

Projekt Azure DevOps automatizuje nastavení celého kanálu kontinuální integrace (CI) a průběžného doručování (CD) do Azure.  Můžete začít se svým stávajícím kódem nebo použít některou z poskytovaných ukázkových aplikací a pak ji rychle nasadit do různých služeb Azure, jako jsou virtuální počítače, App Service, Azure Container Service, Azure SQL Database a Azure Service Fabric.  

Projekt Azure DevOps odvede všechnu práci s počáteční konfigurací kanálu DevOps včetně všeho od nastavení počátečního úložiště Git přes konfiguraci kanálu CI/CD, vytvoření prostředku Application Insights pro monitorování až po zobrazení celého řešení na jednom místě v řídicím panelu projektu Azure DevOps na portálu Azure Portal.

Projekt Azure DevOps vám umožní:

* Rychle nasadit aplikace do Azure
* Automatizovat nastavení kanálu CI/CD VSTS
* Použít projekt DevOps jako šablonu k zobrazení a porozumění, jak správně nastavit CI/CD do Azure pomocí VSTS
* Začít s kanálem CI/CD do Azure a pak kanál verze dál přizpůsobit podle konkrétních scénářů

## <a name="how-do-i-use-the-azure-devops-project"></a>Jak projekt Azure DevOps používat?

Projekt Azure DevOps je k dispozici na portálu Azure Portal.  Vytvoříte prostředek projektu Azure DevOps stejně jako jakýkoli jiný prostředek Azure z portálu.  Projekt DevOps nabízí prostředí podobné průvodci s podrobnými kroky pro různé možnosti konfigurace.  

V rámci počátečního nastavení zvolíte několik možností konfigurace.  K těmto možnostem patří:

* Použití poskytnuté ukázková aplikace nebo vlastního kódu
* Zvolení jazyka aplikace
* Zvolení rozhraní aplikace na základě jazyka
* Zvolení služby Azure (cíle nasazení)
* Účet VSTS (nový nebo existující)
* Zvolení předplatného Azure
* Výběr umístění služeb Azure
* Výběr z různých cenových úrovní služeb Azure

Po použití projektu Azure DevOps Azure můžete také všechny prostředky odstranit z jednoho místa v řídicím panelu projektu Azure DevOps na portálu Azure Portal.

## <a name="azure-devops-project-and-vsts-integration"></a>Integrace projektu DevOps a VSTS

Projekty DevOps používají technologii VSTS.  Projekt DevOps automatizuje všechnu potřebnou práci ve VSTS k nastavení CI/CD do Azure.  V novém nebo existujícím účtu VSTS se vytvoří úložiště Git.  Projekt DevOps potvrdí ukázkovou aplikaci nebo váš stávající kód do nového úložiště Git.  Automatizace také vytvoří aktivační událost CI pro sestavení tak, aby každé nové potvrzení kódu inicializovalo sestavení.  Projekt DevOps také vytvoří aktivační událost CD a nasadí každé nové úspěšné sestavení do služby Azure podle vašeho výběru.  Definice sestavení a verzí se dají přizpůsobit pro další scénáře.  Definice sestavení a verzí můžete také klonovat pro použití v jiných projektech.

Po vytvoření projektu DevOps můžete:

* Přizpůsobit kanál sestavení a verzí
* Pomocí žádostí o přijetí změn spravovat tok kódu a udržovat vysokou kvalitu
* Testovat a sestavovat každé potvrzení před sloučením kódu kvůli zvýšení kvality
* Sledovat backlog a problémy projektu přímo s aplikací

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Jak projekt Azure DevOps začít používat?

* [Začínáme s projektem Azure DevOps](https://docs.microsoft.com/azure/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Videa pro projekt Azure DevOps

* [Vytvoření CI/CD s využitím projektu Azure DevOps](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
