---
title: Seznámení s Azure Automation
description: Tento článek obsahuje informace o tom, co Azure Automation a jak ho používat k automatizaci životního cyklu infrastruktury a aplikací.
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, PowerShell, konfigurace stavu, Správa aktualizací, sledování změn, DSC, inventář, Runbooky, Python, grafické
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: e3986b7e8fc70f8662bed40b076897caa6165744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182814"
---
# <a name="an-introduction-to-azure-automation"></a>Seznámení s Azure Automation

Azure Automation zajišťuje cloudovou službu pro automatizaci a konfiguraci, která podporuje konzistentní správu napříč prostředími Azure a mimo Azure. Zahrnuje automatizaci procesů, správu konfigurace, správu aktualizací, sdílené funkce a heterogenní funkce. Automatizace poskytuje plnou kontrolu nad nasazením, provozem a vyřazením úloh a prostředků z provozu.

![Možnosti automatizace](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automatizace procesů

Automatizace procesů v Azure Automation umožňuje automatizovat časté úlohy správy cloudu, časově náročné a náchylné k chybám. Tato služba vám pomůže soustředit se na práci, která přidává obchodní hodnotu. Díky menšímu množství chyb a vyšší efektivitě vám také pomůže snížit provozní náklady. Provozní prostředí automatizace procesu je podrobně popsáno v části [spuštění sady Runbook v Azure Automation](automation-runbook-execution.md).

Automatizace procesů podporuje integraci služeb Azure a dalších veřejných systémů potřebných k nasazení, konfiguraci a správě komplexních procesů. Služba umožňuje vytvářet [Runbooky](automation-runbook-types.md) v grafickém prostředí, v PowerShellu nebo pomocí Pythonu. Pomocí [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)můžete sjednotit správu pomocí orchestrace napříč místními prostředími. [Webhooky](automation-webhooks.md) umožňují plnit požadavky a zajišťovat průběžné doručování a provoz tím, že aktivují automatizaci z ITSM, DevOps a monitorovacích systémů. 

## <a name="configuration-management"></a>Správa konfigurace

Správa konfigurace v Azure Automation umožňuje přístup ke dvěma funkcím:

* Change Tracking a Inventory
* Služba Azure Automation State Configuration

### <a name="change-tracking-and-inventory"></a>Change Tracking a Inventory

Change Tracking a inventář kombinuje funkce Change Tracking a Inventory, které vám umožní sledovat změny infrastruktury virtuálních počítačů a serverů. Služba podporuje sledování změn napříč službami, démony, softwarem, Registry a soubory ve vašem prostředí, které vám pomůžou diagnostikovat nechtěné změny a vyvolat výstrahy. Podpora inventáře vám umožní dotazovat se na prostředky v hostovi pro přehlednost v nainstalovaných aplikacích a dalších položkách konfigurace. Podrobnosti o této funkci najdete v tématu [Change Tracking a inventář](change-tracking/overview.md).

### <a name="azure-automation-state-configuration"></a>Služba Azure Automation State Configuration

[Konfigurace stavu Azure Automation](automation-dsc-overview.md) je cloudová funkce pro konfiguraci požadovaného stavu PowerShellu (DSC), která poskytuje služby pro podniková prostředí. Pomocí této funkce můžete spravovat prostředky DSC v Azure Automation a aplikovat konfigurace na virtuální nebo fyzické počítače ze serveru vyžádané replikace DSC v cloudu Azure. 

## <a name="update-management"></a>Správa aktualizací

Azure Automation obsahuje funkci [Update Management](./update-management/overview.md) pro systémy Windows a Linux napříč hybridními prostředími. Update Management vám poskytne přehled o kompatibilitě aktualizací napříč Azure a ostatními cloudy a místním prostředí. Tato funkce umožňuje vytvářet plánovaná nasazení, která orchestrují instalaci aktualizací v rámci definovaného časového období údržby. Pokud by se aktualizace neměla instalovat na počítač, můžete k vyloučení z nasazení použít funkci Update Management.

## <a name="shared-capabilities"></a>Sdílené možnosti

Azure Automation nabízí řadu sdílených možností, včetně sdílených prostředků, řízení přístupu na základě role, flexibilního plánování, integrace správy zdrojového kódu, auditování a označování.

### <a name="shared-resources"></a><a name="shared-resources"></a>Sdílené prostředky

Azure Automation se skládá ze sady sdílených prostředků, které usnadňují automatizaci a konfiguraci prostředí ve velkém měřítku.

* **[Plány](./shared-resources/schedules.md)** – aktivuje operace automatizace v předdefinovaných časech.
* **[Moduly](./shared-resources/modules.md)** – spravujte Azure a další systémy. Moduly můžete importovat do účtu Automation pro rutiny od Microsoftu, dalších výrobců, komunitu a pro vlastní definice a prostředky DSC.
* **[Galerie modulů](automation-runbook-gallery.md)** – podporuje nativní integraci s Galerie prostředí PowerShell, která umožňuje zobrazit Runbooky a importovat je do účtu Automation. Galerie vám umožní rychle začít s integrací a vytvářením procesů z Galerie prostředí PowerShell a Microsoft Script Center.
* **[Balíčky Python 2](python-packages.md)** – podporují Runbooky Python 2 pro váš účet Automation.
* **[Přihlašovací údaje](./shared-resources/credentials.md)** – bezpečně ukládají citlivé informace, které Runbooky a konfigurace můžou použít za běhu.
* **[Connections](automation-connections.md)** – dvojici název-hodnota jsou společné informace pro připojení k systémům. Autor modulu definuje připojení v sadách Runbook a konfiguracích pro použití za běhu.
* **[Certifikáty](./shared-resources/certificates.md)** – definujte informace, které se mají použít při ověřování a zabezpečení nasazených prostředků při použití v sadách Runbook nebo konfiguracích DSC za běhu. 
* **[Proměnné](./shared-resources/variables.md)** – blokovaný obsah, který se dá použít napříč Runbooky a konfiguracemi. Hodnoty proměnných můžete změnit bez nutnosti upravovat žádné Runbooky nebo konfigurace, které na ně odkazují.

### <a name="role-based-access-control"></a>Řízení přístupu na základě role

Azure Automation podporuje řízení přístupu na základě role Azure (Azure RBAC) a řídí přístup k účtu Automation a jeho prostředkům. Další informace o konfiguraci služby Azure RBAC na vašem účtu Automation, sadách Runbook a úlohách najdete v tématu [řízení přístupu na základě rolí pro Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integrace správy zdrojového kódu

Azure Automation podporuje [integraci správy zdrojového kódu](source-control-integration.md). Tato funkce propaguje konfiguraci jako kód, kde mohou být Runbooky nebo konfigurace zkontrolovány do systému správy zdrojového kódu.

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
* **Řízení –** nastavení služby Azure RBAC pro týmy. Obnovte nevyužité prostředky.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Ceny za Azure Automation

Ceny spojené s Azure Automation můžete zkontrolovat na stránce s [cenami](https://azure.microsoft.com/pricing/details/automation/) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření účtu Automation](automation-quickstart-create-account.md)