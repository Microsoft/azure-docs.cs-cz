---
title: Správa dat Azure Automation
description: Tento článek obsahuje více témat pro správu prostředí Azure Automation.  Nyní zahrnuje uchování dat a zálohování zotavení po havárii Azure Automation ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 05da900e9ddf4cbb99df5c6d62ddb569059e2c4b
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054615"
---
# <a name="managing-azure-automation-data"></a>Správa dat Azure Automation
Tento článek obsahuje více témat pro správu prostředí Azure Automation.

## <a name="data-retention"></a>Uchovávání dat
Při odstranění prostředků ve službě Azure Automation se uchovávají 90 dnů pro auditování, než jsou odstraněny trvale.  Nelze zobrazit ani použít na prostředek během této doby.  Tyto zásady platí také pro prostředky, které patří k účtu automation, který je odstraněn.

Azure Automation, automaticky odstraní a trvale odebere úlohy, které jsou starší než 90 dní.

Následující tabulka shrnuje zásady uchovávání informací pro různé prostředky.

| Data | Zásada |
|:--- |:--- |
| Účty |Trvale odebrat 90 dnů po odstranění účtu uživatele. |
| Prostředky |Trvale odebrat 90 dnů po odstranění prostředku uživatelem nebo 90 dnů po účet, který obsahuje že Asset se odstraní uživatel. |
| Moduly |Trvale odebrat 90 dnů po odstranění modulu uživatelem nebo 90 dnů po účet, který obsahuje že modul je odstraněn uživatelem. |
| Runbooky |Trvale odebrat 90 dnů po odstranění prostředku uživatelem nebo 90 dnů po účet, který obsahuje že odstranění prostředku uživatelem. |
| Úlohy |Odstraněné a trvale odebrané 90 dní od posledního právě upravuje. To může být po úlohy dokončí, je zastavená nebo je pozastavená. |
| Soubory konfigurace/MOF uzlu |Staré konfigurace uzlu bude trvale odstraněn 90 dnů po vygenerování nové konfigurace uzlu. |
| Uzly DSC |Trvale odebrat 90 dnů po uzlu se registrace z účtu Automation pomocí webu Azure portal nebo [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) rutiny v prostředí Windows PowerShell. Uzly se také trvale odeberou 90 dnů po účet, který obsahuje že uzel je odstraněn uživatelem. |
| Sestavy uzlu |Trvale odebrat 90 dnů po vygenerování nové sestavy pro tento uzel |

Zásady uchovávání informací se vztahuje na všechny uživatele a aktuálně nejde přizpůsobit.

Nicméně pokud je potřeba data uchovávat po delší dobu, můžete dál runbook protokoly úlohy ke službě Log Analytics.  Další informace najdete v tématu [předávání dat úloh Azure Automation do Log Analytics](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Zálohování Azure Automation
Když odstraníte účet automation v Microsoft Azure, jsou odstraněny všechny objekty v účtu včetně runbooky, moduly, konfigurace, nastavení, úlohy a prostředky. Objekty nelze obnovit, po odstranění účtu.  Před odstraněním zálohovat obsah vašeho účtu automation můžete použít následující informace. 

### <a name="runbooks"></a>Runbooky
Vaše sady runbook můžete exportovat do souborů skriptu pomocí portálu Azure nebo [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) rutiny v prostředí Windows PowerShell.  Tyto soubory skriptu můžete importovat do jiného účtu automation, jak je popsáno v [vytvoření nebo import Runbooku](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Integrační moduly
Integrační moduly nelze exportovat ze služby Azure Automation.  Ujistěte se, že jsou k dispozici mimo účet automation.

### <a name="assets"></a>Prostředky
Nejde exportovat [prostředky](https://msdn.microsoft.com/library/dn939988.aspx) ze služby Azure Automation.  Pomocí webu Azure portal, musíte zaznamenat podrobnosti proměnné, přihlašovací údaje, certifikátů, připojení a plány.  Potom musíte ručně vytvořit všechny prostředky, které jsou používány sady runbook, který importujete do jiné služby automation.

Můžete použít [rutiny Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) načíst podrobnosti o nešifrované prostředky a buď je uložit pro pozdější použití nebo vytvoření ekvivalentní prostředky v jiném účtu automation.

Nelze načíst hodnotu zašifrovaných proměnných nebo pole pro heslo přihlašovacích údajů pomocí rutin.  Pokud si nejste jisti, tyto hodnoty, pak je můžete vyvolat z runbooku pomocí [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) a [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) aktivity.

Certifikáty nelze exportovat ze služby Azure Automation.  Ujistěte se, že všechny certifikáty jsou k dispozici mimo Azure.

### <a name="dsc-configurations"></a>Konfigurace DSC
Můžete exportovat konfiguraci do souborů skriptu pomocí portálu Azure nebo [Export AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) rutiny v prostředí Windows PowerShell. Tyto konfigurace lze importovat a používat v jiném účtu automation.

## <a name="geo-replication-in-azure-automation"></a>Geografická replikace ve službě Azure Automation
Geografická replikace, standard v účtech Azure Automation, zálohuje data účtu do jiné geografické oblasti za účelem zajištění redundance. Můžete použít primární oblasti při nastavování účtu a pak sekundární oblasti je k němu přiřazen automaticky. V případě ztráty dat se průběžně aktualizuje sekundární daty zkopírovanými z primární oblasti.  

V následující tabulce jsou uvedeny párování k dispozici primární a sekundární oblastí.

| Primární | Sekundární |
| --- | --- |
| Středojižní USA |Středoseverní USA |
| USA – východ 2 |USA – střed |
| Západní Evropa |Severní Evropa |
| Jihovýchodní Asie |Východní Asie |
| Japonsko – východ |Japonsko – západ |

V nepravděpodobném případě, že dojde ke ztrátě dat primární oblasti Microsoft se pokusí obnovit. Pokud není možné obnovit primární data, pak je provedena geo-převzetí služeb při selhání a ovlivněných bude zákazníky informovat o tom prostřednictvím svého předplatného.

