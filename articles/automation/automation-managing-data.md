---
title: Správa dat Azure Automation
description: Tento článek obsahuje několik témat pro správu prostředí Azure Automation.  V současné době zahrnuje uchovávání dat a zálohování Azure Automation zotavení po havárii v Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984653"
---
# <a name="managing-azure-automation-data"></a>Správa dat Azure Automation

Tento článek obsahuje několik témat pro správu dat v prostředí Azure Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="data-retention"></a>Uchovávání dat

Když odstraníte prostředek v Azure Automation, je zachována po několik dní pro účely auditování před trvalé odebrání. Během této doby nelze zdroj zobrazit ani použít. Tato zásada platí také pro prostředky, které patří do odstraněného účtu Automatizace.

Následující tabulka shrnuje zásady uchovávání informací pro různé prostředky.

| Data | Zásada |
|:--- |:--- |
| Účty |Účet je trvale odebrán 30 dní poté, co jej uživatel odstraní. |
| Prostředky |Datový zdroj je trvale odebrán 30 dní poté, co jej uživatel odstraní, nebo 30 dní poté, co uživatel odstraní účet, který datový zdroj obsahuje. |
| Uzly DSC |Uzel DSC je trvale odebrán30 dní po zrušení registrace z účtu automatizace pomocí portálu Azure nebo [rutiny Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) v prostředí Windows PowerShell. Uzel je také trvale odebrán 30 dní poté, co uživatel odstraní účet, který uchovává uzel. |
| Úlohy |Úloha je odstraněna a trvale odebrána 30 dní po úpravě, například po dokončení úlohy, je zastavena nebo pozastavena. |
| Moduly |Modul je trvale odebrán 30 dní poté, co jej uživatel odstraní, nebo 30 dní poté, co uživatel odstraní účet, který modul obsahuje. |
| Konfigurace uzlů/soubory MOF |Stará konfigurace uzlu je trvale odebrána 30 dní po vygenerování nové konfigurace uzlu. |
| Sestavy uzlů |Sestava uzlu je trvale odebrána 90 dní po vygenerování nové sestavy pro tento uzel. |
| Runbooky |Runbook je trvale odebrán 30 dní poté, co uživatel odstraní prostředek, nebo 30 dní poté, co uživatel odstraní účet, který je držitelem prostředku. |

Zásady uchovávání informací platí pro všechny uživatele a momentálně je nelze přizpůsobit. Pokud však potřebujete uchovávat data po delší dobu, můžete [předat data úloh Azure Automation do protokolů Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Zálohování dat

Když odstraníte účet Automation v Azure, odstraní se všechny objekty v účtu. Mezi objekty patří sady Runbook, moduly, konfigurace, nastavení, úlohy a datové zdroje. Po odstranění účtu je nelze obnovit. Můžete použít následující informace k zálohování obsahu účtu automatizace před jeho odstraněním.

### <a name="runbooks"></a>Runbooky

Runbooky můžete exportovat do souborů skriptů pomocí portálu Azure nebo rutiny [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) v prostředí Windows PowerShell. Tyto soubory skriptů můžete importovat do jiného účtu Automation, jak je popsáno v [části Správa runbooků v Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integrační moduly

Integrační moduly nelze exportovat z Azure Automation. Je nutné je zpřístupnit mimo účet automatizace.

### <a name="assets"></a>Prostředky

Nelze exportovat prostředky Azure Automation: certifikáty, připojení, přihlašovací údaje, plány a proměnné. Místo toho můžete použít portál Azure a rutiny Azure k poznámce podrobnosti o těchto prostředků. Potom tyto podrobnosti použijte k vytvoření všech datových zdrojů používaných runbooky, které importujete do jiného účtu Automatizace.

Nelze načíst hodnoty pro šifrované proměnné nebo hesla polí pověření pomocí rutiny. Pokud tyto hodnoty neznáte, můžete je načíst v runbooku. Informace o načtení hodnot proměnných najdete [v tématu Proměnná v Azure Automation](shared-resources/variables.md). Další informace o načítání hodnot pověření najdete [v tématu Prostředky pověření v Azure Automation](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>Konfigurace DSC

Konfigurace DSC můžete exportovat do souborů skriptů pomocí portálu Azure nebo [rutiny Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) v prostředí Windows PowerShell. Tyto konfigurace můžete importovat a používat v jiném účtu automatizace.

## <a name="geo-replication-in-azure-automation"></a>Geografická replikace v Azure Automation

Geografická replikace je standardní v účtech Azure Automation. Při nastavování účtu zvolíte primární oblast. Interní služba geografické replikace automatizace automaticky přiřadí sekundární oblast k účtu. Služba pak průběžně zálohuje data účtu z primární oblasti do sekundární oblasti. Úplný seznam primárních a sekundárních oblastí najdete na [webu Kontinuita podnikání a zotavení po havárii (BCDR): Spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

Záloha vytvořená službou geografické replikace Automatizace je úplnou kopií prostředků automatizace, konfigurací a podobně. Tuto zálohu lze použít, pokud primární oblast přejde dolů a ztratí data. V nepravděpodobném případě, že data pro primární oblast je ztracena, Microsoft pokusí obnovit. Pokud společnost nemůže obnovit primární data, použije automatické převzetí služeb při selhání a informuje vás o situaci prostřednictvím předplatného Azure. 

Služba geografické replikace automatizace není přístupná přímo externím zákazníkům, pokud dojde k regionální chybě. Pokud chcete udržovat konfiguraci automatizace a sady Runbook během místních selhání:

1. Vyberte sekundární oblast, kterou chcete spárovat se zeměpisnou oblastí primárního účtu automatizace.

2. Vytvořte účet automatizace v sekundární oblasti.

3. V primárním účtu exportujte sady Runbook jako soubory skriptů.

4. Importujte sady Runbook do účtu Automation v sekundární oblasti.

## <a name="next-steps"></a>Další kroky

* Další informace o zabezpečených datových místech v Azure Automation najdete [v tématu Šifrování zabezpečených prostředků v Azure Automation](automation-secure-asset-encryption.md).

* Další informace o geografické replikaci najdete v [tématu Vytváření a používání aktivní geografické replikace](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).