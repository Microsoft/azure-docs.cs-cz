---
title: Správa dat služby Azure Automation
description: Tento článek obsahuje koncepty správy dat v Azure Automation, včetně uchovávání a zálohování dat.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 44fe626abd71c13b7f16aa07c4ddf261ff3cacea
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715422"
---
# <a name="management-of-azure-automation-data"></a>Správa dat služby Azure Automation

Tento článek obsahuje několik témat pro správu dat v prostředí Azure Automation.

## <a name="data-retention"></a>Uchovávání dat

Když prostředek odstraníte z Azure Automation, uchová se po několik dní pro účely auditování před trvalým odebráním. Během této doby nemůžete tento prostředek zobrazit ani použít. Tyto zásady platí taky pro prostředky, které patří k odstraněnému účtu Automation.

Následující tabulka shrnuje zásady uchovávání informací pro různé prostředky.

| Data | Zásada |
|:--- |:--- |
| Účty |Účet se trvale odebere 30 dní poté, co ho uživatel odstraní. |
| Prostředky |Asset se trvale odebere 30 dní poté, co ho uživatel odstraní nebo 30 dní poté, co uživatel odstraní účet, který má Asset. |
| Uzly DSC |Uzel DSC se trvale odebere 30 dnů po zrušení registrace účtu Automation pomocí Azure Portal nebo rutiny [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) ve Windows PowerShellu. Uzel je také trvale odebrán 30 dní poté, co uživatel odstraní účet, který obsahuje uzel. |
| Úlohy |Úloha je odstraněna a trvale odebrána 30 dní po provedení změny, například po dokončení úlohy, zastavení nebo pozastavení. |
| Moduly |Modul se trvale odebere 30 dní poté, co ho uživatel odstraní nebo 30 dní poté, co uživatel odstraní účet, který obsahuje daný modul. |
| Konfigurace uzlů/soubory MOF |Stará konfigurace uzlu je trvale odebrána 30 dní po vygenerování nové konfigurace uzlu. |
| Sestavy uzlů |Po vygenerování nové sestavy pro tento uzel je sestava uzlu trvale odebrána 90 dní. |
| Runbooky |Sada Runbook je trvale odebrána 30 dní poté, co uživatel odstraní prostředek, nebo 30 dní poté, co uživatel odstraní účet, který obsahuje daný prostředek. |

Zásady uchovávání informací platí pro všechny uživatele a aktuálně se nedají přizpůsobit. Pokud ale potřebujete zachovat data po delší dobu, můžete [data Azure Automation úlohy přeposílat do Azure monitor protokolů](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Zálohování dat

Když odstraníte účet služby Automation v Azure, odstraní se všechny objekty v účtu. Mezi tyto objekty patří Runbooky, moduly, konfigurace, nastavení, úlohy a prostředky. Po odstranění účtu už je nepůjde obnovit. Následující informace můžete použít k zálohování obsahu svého účtu Automation před jeho odstraněním.

### <a name="runbooks"></a>Runbooky

Runbooky můžete exportovat do souborů skriptu pomocí rutiny Azure Portal nebo rutiny [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) ve Windows PowerShellu. Tyto soubory skriptu můžete importovat do jiného účtu Automation, jak je popsáno v tématu [Správa runbooků v Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integrační moduly

Nemůžete exportovat moduly integrace z Azure Automation. Je nutné, aby byly dostupné mimo účet Automation.

### <a name="assets"></a>Prostředky

Nemůžete exportovat Azure Automation assety: certifikáty, připojení, přihlašovací údaje, plány a proměnné. Místo toho můžete použít rutiny Azure Portal a Azure a poznamenat si podrobnosti o těchto prostředcích. Pak pomocí těchto podrobností vytvořte všechny prostředky, které používají Runbooky, které importujete do jiného účtu Automation.

Pomocí rutin se nedají načíst hodnoty šifrovaných proměnných nebo polí s hesly přihlašovacích údajů. Pokud tyto hodnoty neznáte, můžete je načíst v Runbooku. Informace o načítání hodnot proměnných naleznete [v tématu Variable assets in Azure Automation](shared-resources/variables.md). Další informace o načítání hodnot přihlašovacích údajů najdete v tématu [assety přihlašovacích údajů v Azure Automation](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>Konfigurace DSC

Konfigurace DSC můžete exportovat do souborů skriptu pomocí Azure Portal nebo rutiny [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) ve Windows PowerShellu. Tyto konfigurace můžete importovat a používat v jiném účtu Automation.

## <a name="geo-replication-in-azure-automation"></a>Geografická replikace v Azure Automation

Geografická replikace je v účtech Azure Automation Standard. Při nastavování účtu se volí primární oblast. Interní služba pro geografickou replikaci služby Automation přiřadí k účtu sekundární oblast automaticky. Služba pak průběžně zálohuje data účtu z primární oblasti do sekundární oblasti. Úplný seznam primárních a sekundárních oblastí najdete v části [provozní kontinuita a zotavení po havárii (BCDR): spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

Záloha vytvořená geografickou replikací služby Automation je kompletní kopie automatizačních prostředků, konfigurací a podobně. Tato záloha se dá použít, pokud primární oblast rozroste a ztratí data. V nepravděpodobném případě, že dojde ke ztrátě dat pro primární oblast, se Microsoft pokusí ji obnovit. Pokud společnost nemůže obnovit primární data, použije automatické převzetí služeb při selhání a informuje vás o situaci prostřednictvím předplatného Azure. 

Služba geografického replikace pro automatizaci není dostupná přímo pro externí zákazníky, pokud dojde k místnímu selhání. Pokud chcete zachovat konfiguraci a runbooky automatizace během regionálních selhání:

1. Vyberte sekundární oblast, která se má spárovat s geografickou oblastí vašeho primárního účtu Automation.

2. Vytvořte účet Automation v sekundární oblasti.

3. V primárním účtu exportujte Runbooky jako soubory skriptu.

4. Importujte Runbooky do svého účtu Automation v sekundární oblasti.

## <a name="next-steps"></a>Další kroky

* Další informace o zabezpečených assetech v Azure Automation najdete v tématu [šifrování zabezpečených prostředků v Azure Automation](automation-secure-asset-encryption.md).

* Další informace o geografické replikaci najdete v tématu [vytváření a používání aktivní geografické replikace](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).