---
title: Přehled Azure Automation
description: Zjistěte, jako pomocí Azure Automation automatizovat životní cyklus infrastruktury a aplikací.
services: automation
ms.subservice: process-automation
keywords: azure automation, DSC, powershell, konfigurace stavu, správa aktualizací, sledování změn, DSC, inventář, runbooky, python, grafické
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010235"
---
# <a name="an-introduction-to-azure-automation"></a>Seznámení s Azure Automation

Tento článek poskytuje stručný přehled Azure Automation a odpovědi na některé běžné dotazy. Další informace o různých možnostech najdete na odkazech uvedených v tomto přehledu.

## <a name="about-azure-automation"></a>O automatizaci Azure

Azure Automation poskytuje cloudovou automatizační a konfigurační službu, která podporuje konzistentní správu v prostředích Azure i mimo Azure. Zahrnuje automatizaci procesů, správu konfigurace, správu aktualizací, sdílené funkce a heterogenní funkce. Automatizace poskytuje úplnou kontrolu při nasazování, provozu a vyřazování úloh a prostředků z provozu.

![Možnosti automatizace](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automatizace procesů

Automatizace procesů v Azure Automation umožňuje automatizovat časté, časově náročné úlohy správy cloudu náchylné k chybám. Tato služba vám pomůže soustředit se na práci, která přidává obchodní hodnotu. Díky menšímu množství chyb a vyšší efektivitě vám také pomůže snížit provozní náklady. Provozní prostředí automatizace procesů je podrobně popsáno v [spuštění sady Runbook v Azure Automation](automation-runbook-execution.md).

Automatizace procesů podporuje integraci služeb Azure a dalších veřejných systémů potřebných při nasazování, konfiguraci a správě vašich komplexních procesů. Služba umožňuje vytvářet [runbooky](automation-runbook-types.md) graficky, v PowerShellu nebo pomocí Pythonu. Pomocí [hybridního pracovníka runbooku](automation-hybrid-runbook-worker.md)můžete sjednotit správu orchestrací napříč místními prostředími. [Webhooky](automation-webhooks.md) umožňují plnit požadavky a zajistit nepřetržité doručování a provoz spuštěním automatizace z ITSM, DevOps a monitorovacích systémů. 

## <a name="configuration-management"></a>Správa konfigurace

Konfigurace [stavu](automation-dsc-overview.md) Azure Automation je cloudové řešení pro konfiguraci požadovaného stavu PowerShellu (DSC), které poskytuje služby pro podniková prostředí. Pomocí této funkce můžete spravovat prostředky DSC v Azure Automation a použít konfigurace pro virtuální nebo fyzické počítače z serveru pro vyžádat DSC v cloudu Azure. Konfigurace počítačů můžete monitorovat a automaticky aktualizovat napříč fyzickými a virtuálními počítači, ve Windows nebo Linuxu, v cloudu nebo místně. Podpora inventáře umožňuje dotazovat prostředky hosta pro přehled o nainstalovaných aplikacích a dalších položkách konfigurace.
 
Služba konfigurace stavu Azure Automation poskytuje bohaté možnosti vytváření sestav a vyhledávání. Tyto funkce můžete použít k vyhledání podrobných informací o tom, co je konfigurováno v operačním systému. Služba podporuje sledování změn napříč službami, daemony, softwarem, registrem a soubory ve vašem prostředí, které vám pomohou diagnostikovat nežádoucí změny a vyvolat výstrahy. Důležitou související funkcí je vykazování hlavních událostí, například událostí vydaných v případě, že se uzly odchylují od přiřazených konfigurací. 

## <a name="update-management"></a>Správa aktualizací

Azure Automation zahrnuje řešení [pro správu aktualizací](automation-update-management.md) pro systémy Windows a Linux v hybridních prostředích. Díky tomuto řešení získáte přehled o dodržování předpisů pro aktualizace v Azure a dalších cloudech a v místním prostředí. Správa aktualizací umožňuje vytvářet naplánovaná nasazení, která orchestrují instalaci aktualizací v rámci definovaného časového intervalu údržby. Pokud by aktualizace neměla být nainstalována v počítači, můžete ji vyloučit z nasazení pomocí funkcí správy aktualizací.

## <a name="shared-capabilities"></a>Sdílené možnosti

Azure Automation nabízí řadu sdílených funkcí, včetně sdílených prostředků, řízení přístupu na základě rolí, flexibilního plánování, integrace správy zdrojového kódu, auditování a označování.

### <a name="shared-resources"></a><a name="shared-resources"></a>Sdílené prostředky

Azure Automation se skládá ze sady sdílených prostředků, které usnadňují automatizaci a konfiguraci prostředí ve velkém měřítku.

* **[Plány](automation-schedules.md)** - Trigger Automation operace v předdefinovaných časech.
* **[Moduly](automation-integration-modules.md)** – správa Azure a dalších systémů. Moduly můžete importovat do účtu Automatizace pro Microsoft, třetí strany, komunitu a vlastní definované rutiny a prostředky DSC.
* **[Galerie modulů](automation-runbook-gallery.md)** – Podporuje nativní integraci s Galerií prostředí PowerShell, která vám umožní zobrazit runbooky a importovat je do účtu Automatizace. Galerie umožňuje rychle začít s integrací a vytvářením procesů z galerie prostředí PowerShell a z centra Microsoft Script Center.
* **[Balíčky pythonu 2](python-packages.md)** – podpora runbooků pythonu 2 pro váš účet Automation.
* **[Pověření](automation-credentials.md)** – bezpečně ukládat citlivé informace, které runbooky a konfigurace můžete použít za běhu.
* **[Připojení](automation-connections.md)** – uložte dvojice společných informací o hodnotách názvů pro připojení k systémům. Autor modulu definuje připojení v sadách Runbook a konfiguracích pro použití za běhu.
* **[Certifikáty](automation-certificates.md)** – definujte informace, které mají být použity při ověřování a zabezpečení nasazených prostředků při přístupu pomocí runbooků nebo konfigurací DSC za běhu. 
* **[Proměnné](automation-variables.md)** – Podržte obsah, který lze použít v runbookech a konfiguracích. Můžete změnit hodnoty proměnných, aniž byste museli měnit některou z runbooků nebo konfigurací, které na ně odkazují.

### <a name="role-based-access-control"></a>Řízení přístupu na základě role

Azure Automation podporuje řízení přístupu na základě rolí (RBAC) k regulaci přístupu k účtu Automation a jeho prostředků. Další informace o konfiguraci RBAC na vašem účtu automation, runbooky a úlohy, najdete v [tématu řízení přístupu na základě rolí pro Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integrace správy zdrojového kódu

Azure Automation umožňuje [integraci správy zdrojového kódu](source-control-integration.md). Tato funkce podporuje konfiguraci jako kód, kde lze sady Runbook nebo konfigurace rezervovat do systému správy zdrojového kódu.

## <a name="heterogeneous-support-windows-and-linux"></a>Heterogenní podpora (Windows a Linux)

Automatizace je navržena tak, aby fungovala v celém hybridním cloudovém prostředí a také v systémech Windows a Linux. Poskytuje konzistentní způsob automatizace a konfigurace nasazených úloh a operačních systémů, které je spouštějí.

## <a name="common-scenarios-for-automation"></a>Běžné scénáře pro službu Automation

Azure Automation podporuje správu po celou dobu životnosti vaší infrastruktury a aplikací. Mezi obvyklé scénáře patří:

* **Napište runbooky** – Author PowerShell, PowerShell Workflow, grafické, Python 2 a DSC runbooky v běžných jazycích. 
* **Vytváření a nasazování prostředků** – nasazení virtuálních počítačů v hybridním prostředí pomocí runbooků a šablon Azure Resource Manager. Integrujte do vývojových nástrojů, jako jsou Jenkins a Azure DevOps.
* **Konfigurace virtuálních počítačů** – vyhodnocujte a konfigurujte počítače se systémem Windows a Linux s konfiguracemi pro infrastrukturu a aplikaci.
* **Sdílení znalostí** – Přeneste znalosti do systému o tom, jak vaše organizace poskytuje a udržuje úlohy. 
* **Načíst inventář** – získejte kompletní soupis nasazených prostředků pro cílení, vytváření sestav a dodržování předpisů. 
* **Najít změny** – Identifikujte změny, které mohou způsobit nesprávnou konfiguraci, a zlepšit provozní dodržování předpisů.
* **Monitor** – izolujte změny počítače, které způsobují problémy, a napravujte je nebo eskalujte do systémů správy.
* **Chránit** – karanténní počítače, pokud jsou aktivovány výstrahy zabezpečení. Nastavte požadavky na hostovi.
* **Govern** - Nastavte RBAC pro týmy. Obnovte nevyužité prostředky.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Ceny služby Automation

Ceny spojené s Azure Automation si můžete prohlédnout na stránce [s cenami.](https://azure.microsoft.com/pricing/details/automation/)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření účtu automatizace](automation-quickstart-create-account.md)

