---
title: Správa dat Azure Automation
description: Tento článek obsahuje několik témat pro správu Azure Automationho prostředí.  V současné době zahrnuje uchovávání dat a zálohování Azure Automation zotavení po havárii v Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: daa5bab7c8d4cbe98ffe9a8a8a4b66da029fef5c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421893"
---
# <a name="managing-azure-automation-data"></a>Správa dat Azure Automation
Tento článek obsahuje několik témat pro správu Azure Automationho prostředí.

## <a name="data-retention"></a>Uchovávání dat
Když prostředek odstraníte z Azure Automation, bude před tím, než se trvale odebere, uchovávat 90 dní pro účely auditování.  Během této doby nemůžete tento prostředek zobrazit ani použít.  Tato zásada platí i pro prostředky, které patří k účtu Automation, který je odstraněný.

Azure Automation automaticky odstraní a trvale odebere úlohy starší než 90 dní.

Následující tabulka shrnuje zásady uchovávání informací pro různé prostředky.

| Data | Zásada |
|:--- |:--- |
| Účty |Trvale odebrané 90 dní poté, co uživatel odstraní účet. |
| Prostředky |Trvale odebrané 90 dní po odstranění prostředku uživatelem nebo 90 dnů po účtu, který je držitelem majetku, je odstraněn uživatelem. |
| Moduly |Trvale odebrané 90 dní po odstranění modulu uživatelem nebo 90 dnů po účtu, který modul obsahuje, je odstraněn uživatelem. |
| Runbooky |Trvale odebrané 90 dní po odstranění prostředku uživatelem nebo 90 dnů po účtu, který je držitelem daného prostředku, je odstraněn uživatelem. |
| Úlohy |Odstraní a trvale odeberou 90 dnů od poslední změny. To může být po dokončení úlohy, zastavení nebo pozastavení. |
| Konfigurace uzlů/soubory MOF |Stará konfigurace uzlu je trvale odebrána 90 dní po vygenerování nové konfigurace uzlu. |
| Uzly DSC |Trvale odebrané 90 dní po zrušení registrace uzlu z účtu Automation pomocí Azure Portal nebo rutiny [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) ve Windows PowerShellu. V uzlech se také trvale odeberou 90 dní od účtu, který je držitelem uzlu odstraněn uživatelem. |
| Sestavy uzlů |Trvale odebrané 90 dní po vygenerování nové sestavy pro tento uzel |

Zásady uchovávání informací se vztahují na všechny uživatele a aktuálně se nedají přizpůsobit.

Pokud ale potřebujete zachovat data po delší dobu, můžete protokoly úloh Runbooku přeslat do Azure Monitor protokolů.  Pokud chcete získat další informace, přečtěte si téma [Azure Automation data úlohy do protokolů Azure monitor](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Zálohování Azure Automation
Když odstraníte účet Automation v Microsoft Azure, odstraní se všechny objekty v účtu včetně runbooků, modulů, konfigurací, nastavení, úloh a prostředků. Po odstranění účtu nelze objekty obnovit.  Pomocí následujících informací můžete zálohovat obsah svého účtu Automation před jeho odstraněním. 

### <a name="runbooks"></a>Runbooky
Runbooky můžete exportovat do souborů skriptu pomocí rutiny Azure Portal nebo rutiny [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) ve Windows PowerShellu.  Tyto soubory skriptu se dají importovat do jiného účtu Automation, jak je popsáno v tématu [Vytvoření nebo import Runbooku](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Integrační moduly
Nelze exportovat moduly integrace z Azure Automation.  Musíte zajistit, aby byly dostupné mimo účet Automation.

### <a name="assets"></a>Prostředky
Nemůžete exportovat [prostředky](/previous-versions/azure/dn939988(v=azure.100)) z Azure Automation.  Pomocí Azure Portal je třeba si uvědomit podrobnosti o proměnných, přihlašovacích údajích, certifikátech, připojeních a plánech.  Pak musíte ručně vytvořit všechny prostředky, které používají Runbooky, které importujete do jiné automatizace.

[Rutiny Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) můžete použít k načtení podrobností o nešifrovaných assetech a jejich uložení pro budoucí použití nebo k vytvoření ekvivalentních prostředků v jiném účtu Automation.

Nemůžete načíst hodnotu šifrovaných proměnných nebo pole hesla pro přihlašovací údaje pomocí rutin.  Pokud tyto hodnoty neznáte, můžete je načíst z Runbooku pomocí aktivit [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) a [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) .

Certifikáty z Azure Automation nemůžete exportovat.  Musíte zajistit, aby všechny certifikáty byly dostupné mimo Azure.

### <a name="dsc-configurations"></a>Konfigurace DSC
Konfigurace můžete exportovat do souborů skriptu pomocí Azure Portal nebo rutiny [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) ve Windows PowerShellu. Tyto konfigurace je možné importovat a používat v jiném účtu Automation.

## <a name="geo-replication-in-azure-automation"></a>Geografická replikace v Azure Automation
Geografická replikace, Standard v Azure Automationch účtech, zálohuje data účtu do jiné geografické oblasti pro zajištění redundance. Při nastavování účtu můžete zvolit primární oblast a k ní je automaticky přiřazená sekundární oblast. Sekundární data zkopírovaná z primární oblasti se průběžně aktualizují v případě ztráty dat.  

V následující tabulce jsou uvedeny dostupné dvojice primární a sekundární oblasti.

| Primární | Sekundární |
| --- | --- |
| Střed USA – jih |Střed USA – sever |
| USA – východ 2 |Střed USA |
| Západní Evropa |Severní Evropa |
| Jihovýchodní Asie |Východní Asie |
| Japonsko – východ |Japonsko – západ |

V nepravděpodobném případě, že dojde ke ztrátě dat primární oblasti, se Microsoft pokusí ji obnovit. Pokud primární data obnovit nemůžete, pak se provede geografické převzetí služeb při selhání a ovlivnění zákazníci budou mít v rámci svého předplatného oznámení.


