---
title: Přehled Azure Automation
description: Zjistěte, jako pomocí Azure Automation automatizovat životní cyklus infrastruktury a aplikací.
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, PowerShell, konfigurace stavu, Správa aktualizací, sledování změn, DSC, inventář, Runbooky, Python, grafické
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81010235"
---
# <a name="an-introduction-to-azure-automation"></a>Seznámení s Azure Automation

Tento článek poskytuje stručný přehled Azure Automation a odpovědi na některé běžné dotazy. Další informace o různých možnostech najdete na odkazech uvedených v tomto přehledu.

## <a name="about-azure-automation"></a>O Azure Automation

Azure Automation zajišťuje cloudovou službu pro automatizaci a konfiguraci, která podporuje konzistentní správu napříč prostředími Azure a mimo Azure. Zahrnuje automatizaci procesů, správu konfigurace, správu aktualizací, sdílené funkce a heterogenní funkce. Automatizace poskytuje plnou kontrolu nad nasazením, provozem a vyřazením úloh a prostředků z provozu.

![Možnosti automatizace](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automatizace procesů

Automatizace procesů v Azure Automation umožňuje automatizovat časté úlohy správy cloudu, časově náročné a náchylné k chybám. Tato služba vám pomůže soustředit se na práci, která přidává obchodní hodnotu. Díky menšímu množství chyb a vyšší efektivitě vám také pomůže snížit provozní náklady. Provozní prostředí automatizace procesu je podrobně popsáno v části [spuštění sady Runbook v Azure Automation](automation-runbook-execution.md).

Automatizace procesů podporuje integraci služeb Azure a dalších veřejných systémů potřebných k nasazení, konfiguraci a správě komplexních procesů. Služba umožňuje vytvářet [Runbooky](automation-runbook-types.md) v grafickém prostředí, v PowerShellu nebo pomocí Pythonu. Pomocí [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)můžete sjednotit správu pomocí orchestrace napříč místními prostředími. [Webhooky](automation-webhooks.md) umožňují plnit požadavky a zajišťovat průběžné doručování a provoz tím, že aktivují automatizaci z ITSM, DevOps a monitorovacích systémů. 

## <a name="configuration-management"></a>Správa konfigurace

[Konfigurace stavu](automation-dsc-overview.md) Azure Automation je cloudové řešení pro konfiguraci požadovaného stavu (DSC) prostředí PowerShell, které poskytuje služby pro podniková prostředí. Pomocí této funkce můžete spravovat prostředky DSC v Azure Automation a aplikovat konfigurace na virtuální nebo fyzické počítače ze serveru vyžádané replikace DSC v cloudu Azure. Můžete monitorovat a automaticky aktualizovat konfigurace počítačů ve fyzických i virtuálních počítačích v systému Windows nebo Linux, v cloudu i v místním prostředí. Podpora inventáře vám umožní dotazovat se na prostředky v hostovi pro přehlednost v nainstalovaných aplikacích a dalších položkách konfigurace.
 
Služba konfigurace stavu Azure Automation poskytuje bohatou možnost vytváření sestav a vyhledávání. Pomocí těchto funkcí můžete najít podrobné informace o tom, co je v operačním systému nakonfigurované. Služba podporuje sledování změn napříč službami, démony, softwarem, Registry a soubory ve vašem prostředí, které vám pomůžou diagnostikovat nechtěné změny a vyvolat výstrahy. Důležitou související funkcí je vytváření sestav o hlavních událostech, například událostech vydaných, když se uzly odchylují od jejich přiřazených konfigurací. 

## <a name="update-management"></a>Správa aktualizací

Azure Automation zahrnuje řešení [Update Management](automation-update-management.md) pro systémy Windows a Linux napříč hybridními prostředími. V tomto řešení získáte přehled o kompatibilitě aktualizací v rámci Azure a dalších cloudech i v místním prostředí. Správa aktualizací umožňuje vytvářet plánovaná nasazení, která orchestrují instalaci aktualizací v rámci definovaného časového období údržby. Pokud by se aktualizace neměla instalovat na počítač, můžete k vyloučení z nasazení použít funkce správy aktualizací.

## <a name="shared-capabilities"></a>Sdílené možnosti

Azure Automation nabízí řadu sdílených možností, včetně sdílených prostředků, řízení přístupu na základě role, flexibilního plánování, integrace správy zdrojového kódu, auditování a označování.

### <a name="shared-resources"></a><a name="shared-resources"></a>Sdílené prostředky

Azure Automation se skládá ze sady sdílených prostředků, které usnadňují automatizaci a konfiguraci prostředí ve velkém měřítku.

* **[Plány](automation-schedules.md)** – aktivuje operace automatizace v předdefinovaných časech.
* **[Moduly](automation-integration-modules.md)** – spravujte Azure a další systémy. Moduly můžete importovat do účtu Automation pro rutiny od Microsoftu, dalších výrobců, komunitu a pro vlastní definice a prostředky DSC.
* **[Galerie modulů](automation-runbook-gallery.md)** – podporuje nativní integraci s Galerie prostředí PowerShell, která umožňuje zobrazit Runbooky a importovat je do účtu Automation. Galerie vám umožní rychle začít s integrací a vytvářením procesů z Galerie prostředí PowerShell a Microsoft Script Center.
* **[Balíčky Python 2](python-packages.md)** – podporují Runbooky Python 2 pro váš účet Automation.
* **[Přihlašovací údaje](automation-credentials.md)** – bezpečně ukládají citlivé informace, které Runbooky a konfigurace můžou použít za běhu.
* **[Connections](automation-connections.md)** – dvojici název-hodnota jsou společné informace pro připojení k systémům. Autor modulu definuje připojení v sadách Runbook a konfiguracích pro použití za běhu.
* **[Certifikáty](automation-certificates.md)** – definujte informace, které se mají použít při ověřování a zabezpečení nasazených prostředků při použití v sadách Runbook nebo konfiguracích DSC za běhu. 
* **[Proměnné](automation-variables.md)** – blokovaný obsah, který se dá použít napříč Runbooky a konfiguracemi. Hodnoty proměnných můžete změnit bez nutnosti upravovat žádné Runbooky nebo konfigurace, které na ně odkazují.

### <a name="role-based-access-control"></a>Řízení přístupu na základě role

Azure Automation podporuje řízení přístupu na základě role (RBAC) k regulaci přístupu k účtu Automation a jeho prostředkům. Další informace o konfiguraci RBAC pro váš účet Automation, Runbooky a úlohy najdete v tématu [řízení přístupu na základě rolí pro Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integrace správy zdrojového kódu

Azure Automation umožňuje [integraci správy zdrojového kódu](source-control-integration.md). Tato funkce propaguje konfiguraci jako kód, kde mohou být Runbooky nebo konfigurace zkontrolovány do systému správy zdrojového kódu.

## <a name="heterogeneous-support-windows-and-linux"></a>Heterogenní podpora (Windows a Linux)

Automatizace je navržená tak, aby fungovala napříč hybridním cloudovým prostředím a zároveň se systémy Windows a Linux. Nabízí konzistentní způsob automatizace a konfigurace nasazených úloh a operačních systémů, které je spouštějí.

## <a name="common-scenarios-for-automation"></a>Běžné scénáře pro službu Automation

Azure Automation podporuje správu v celém životním cyklu vaší infrastruktury a aplikací. Mezi obvyklé scénáře patří:

* Vytváření **runbooků – vytvoření** PowerShellu, pracovního postupu PowerShellu, grafických, Python 2 a sad Runbook DSC v běžných jazycích. 
* **Sestavování a nasazování prostředků** – nasazení virtuálních počítačů napříč hybridním prostředím pomocí runbooků a Azure Resource Manager šablon. Integrujte do vývojářských nástrojů, jako je Jenkinse a Azure DevOps.
* **Konfigurace virtuálních počítačů** – vyhodnocuje a konfigurují počítače s Windows a Linux konfigurací pro infrastrukturu a aplikaci.
* **Sdílejte znalostní bázi znalostní báze** do systému o tom, jak vaše organizace zajišťuje a udržuje úlohy. 
* **Načtení inventáře** – získání kompletního inventáře nasazených prostředků pro cílení, generování sestav a dodržování předpisů. 
* **Najít změny** – Identifikujte změny, které můžou způsobit neshodnou konfiguraci a vylepšit provozní dodržování předpisů.
* **Monitorování** – izolujte změny počítačů, které způsobují problémy a napravují je, nebo je předají do systémů pro správu.
* **Chránit** – počítače v karanténě, pokud jsou vyvolány výstrahy zabezpečení. Nastavte požadavky na hostovi.
* **Řízení –** nastavení RBAC pro týmy. Obnovte nevyužité prostředky.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Ceny služby Automation

Ceny spojené s Azure Automation můžete zkontrolovat na stránce s [cenami](https://azure.microsoft.com/pricing/details/automation/) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření účtu Automation](automation-quickstart-create-account.md)

