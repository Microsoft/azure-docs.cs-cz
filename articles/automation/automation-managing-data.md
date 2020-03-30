---
title: Správa dat Azure Automation
description: Tento článek obsahuje několik témat pro správu prostředí Azure Automation.  V současné době zahrnuje uchovávání dat a zálohování Azure Automation zotavení po havárii v Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132897"
---
# <a name="managing-azure-automation-data"></a>Správa dat Azure Automation

Tento článek obsahuje několik témat pro správu prostředí Azure Automation.

## <a name="data-retention"></a>Uchovávání dat

Když odstraníte prostředek v Azure Automation, je zachována po dobu 30 dnů pro účely auditování před odebráním trvale. Během této doby nelze zdroj zobrazit ani použít. Tato zásada platí také pro prostředky, které patří do účtu automatizace, který je odstraněn.

Azure Automation automaticky odstraní a trvale odebere úlohy starší než 30 dní.

Následující tabulka shrnuje zásady uchovávání informací pro různé prostředky.

| Data | Zásada |
|:--- |:--- |
| Účty |Trvale odebráno 30 dní po odstranění účtu uživatelem. |
| Prostředky |Trvale odebráno 30 dní po odstranění datového zdroje uživatelem nebo 30 dní po odstranění účtu, který je držitelem datového zdroje, uživatelem. |
| Moduly |Trvale odebrán30 dnů po odstranění modulu uživatelem nebo 30 dní po odstranění účtu, který modul obsahuje uživatelem. |
| Runbooky |Trvale odebrán30 dní po odstranění prostředku uživatelem nebo 30 dní po odstranění účtu, který je v držení prostředku odstraněn uživatelem. |
| Úlohy |Odstraněno a trvale odebráno 30 dní po poslední úpravě. To může být po dokončení úlohy, je zastavena nebo pozastavena. |
| Konfigurace uzlů/soubory MOF |Konfigurace starého uzlu je trvale odebrána 30 dní po vygenerování nové konfigurace uzlu. |
| Uzly DSC |Trvale odebrána 30 dní po uzlu je unregistered z automation účtu pomocí portálu Azure nebo [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) rutina v prostředí Windows PowerShell. Uzly jsou také trvale odebrány 30 dní po odstranění účtu, který obsahuje uzel uživatelem. |
| Sestavy uzlů |Trvale odebráno 90 dní po vygenerování nové sestavy pro tento uzel |

Zásady uchovávání informací platí pro všechny uživatele a nyní nelze přizpůsobit.

Pokud však potřebujete uchovávat data po delší dobu, můžete předávat protokoly úloh runbooku do protokolů Azure Monitor. Další informace najděte [předávací data úloh Azure Automation protokolům Azure Monitoru](automation-manage-send-joblogs-log-analytics.md).

## <a name="backing-up-azure-automation"></a>Zálohování Azure Automation

Když odstraníte účet automatizace v Microsoft Azure, odstraní se všechny objekty v účtu, včetně runbooků, modulů, konfigurací, nastavení, úloh a prostředků. Objekty nelze obnovit po odstranění účtu.  Následující informace můžete použít k zálohování obsahu účtu automatizace před jeho odstraněním.

### <a name="runbooks"></a>Runbooky

Runbooky můžete exportovat do souborů skriptů pomocí portálu Azure nebo rutiny [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) v prostředí Windows PowerShell. Tyto soubory skriptů lze importovat do jiného účtu automatizace, jak je popsáno v [části Vytvoření nebo import sady Runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Integrační moduly

Integrační moduly nelze exportovat z Azure Automation. Je nutné zajistit, že jsou k dispozici mimo účet automatizace.

### <a name="assets"></a>Prostředky

Prostředky nelze [exportovat](/previous-versions/azure/dn939988(v=azure.100)) z Azure Automation.  Pomocí portálu Azure si musíte poznamenat podrobnosti o proměnných, pověřeních, certifikátech, připojeních a plánech.  Potom je nutné ručně vytvořit všechny datové zdroje, které jsou používány sady Runbook, které importujete do jiné automatizace.

Rutiny [Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) můžete použít k načtení podrobností o nešifrovaných datových zdrojů a buď je uložit pro budoucí použití nebo vytvořit ekvivalentní prostředky v jiném účtu automatizace.

Hodnotu šifrovaných proměnných nebo pole hesla pověření pomocí rutin nelze načíst. Pokud tyto hodnoty neznáte, můžete je načíst z runbooku pomocí aktivit [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) a [Get-AutomationPSCredential.](/previous-versions/azure/dn940015(v=azure.100))

Certifikáty nelze exportovat z Azure Automation. Musíte zajistit, aby všechny certifikáty jsou k dispozici mimo Azure.

### <a name="dsc-configurations"></a>Konfigurace DSC

Konfigurace můžete exportovat do souborů skriptů pomocí portálu Azure nebo [rutiny Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) v prostředí Windows PowerShell. Tyto konfigurace lze importovat a použít v jiném účtu automatizace.

## <a name="geo-replication-in-azure-automation"></a>Geografická replikace v Azure Automation

Geografická replikace, standard v účtech Azure Automation, zálohuje data účtu do jiné geografické oblasti pro redundanci. Při nastavování účtu můžete zvolit primární oblast a pak je k ní automaticky přiřazena sekundární oblast. Sekundární data zkopírovaná z primární oblasti jsou průběžně aktualizována v případě ztráty dat.  

V následující tabulce jsou uvedeny dostupné párování primární a sekundární oblasti.

| Primární | Sekundární |
| --- | --- |
| USA – středojih |USA – středosever |
| USA – východ 2 |USA – střed |
| Západní Evropa |Severní Evropa |
| Jihovýchodní Asie |Východní Asie |
| Japonsko – východ |Japonsko – západ |

V nepravděpodobném případě, že jsou ztracena data primární oblasti, se společnost Microsoft pokusí obnovit. Pokud primární data nelze obnovit, pak geografické převzetí služeb při selhání se provádí a ovlivnění zákazníci budou upozorněni na to prostřednictvím svého předplatného.
