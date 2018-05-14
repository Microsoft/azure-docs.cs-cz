---
title: Externí řešení monitorování integrovat Azure zásobníku | Microsoft Docs
description: Zjistěte, jak integrovat Azure zásobníku externí řešení monitorování ve vašem datovém centru.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: d7c8520602132722fd0c7138de4a276b9ac2208a
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Externí řešení monitorování integrovat Azure zásobníku

Pro externí monitorování infrastruktury Azure zásobníku, budete muset monitorování softwaru zásobník Azure, fyzické počítače a fyzické síťové přepínače. Každá z těchto oblastí nabízí metody za účelem načtení informací o stavu a výstrahy:

- Azure zásobníku softwaru nabízí rozhraní API založené na REST pro načtení stavu a výstrahy. Použití technologie softwarově definované jako prostory úložiště – přímé, stav úložiště a výstrahy jsou součástí softwaru monitorování.
- Fyzické počítače můžete zpřístupnit stavu a informace o výstrahách pomocí řadiče pro správu základní desky (BMC).
- Fyzická síťová zařízení můžete zpřístupnit stavu a informace o výstrahách prostřednictvím protokolu SNMP.

Každé řešení Azure zásobníku se dodává s hostiteli životního cyklu hardwaru. Tento hostitel spouští monitorování software výrobce OEM dodavatele hardwaru pro fyzických serverů a síťových zařízení. V případě potřeby můžete vynechat tato řešení ke sledování a přímo integrovat existující řešení pro monitorování ve vašem datovém centru.

> [!IMPORTANT]
> Externí řešení monitorování, které používáte, musí být bez agenta. Nelze instalovat agenty třetích stran uvnitř součásti zásobníku Azure.

Následující diagram znázorňuje tok přenosů mezi s Azure zásobníku integrované systémem, hostitele životního cyklu hardwaru, do externí řešení pro monitorování a systém kolekce externí lístků nebo data.

![Diagram zobrazující provoz mezi Azure zásobníku, monitorování a lístků pro řešení.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Tento článek vysvětluje, jak integrovat externí řešení monitorování, například System Center Operations Manager a Nagios zásobník Azure. Obsahuje také postupy pro práci s výstrahami programově pomocí prostředí PowerShell nebo prostřednictvím volání rozhraní REST API.

## <a name="integrate-with-operations-manager"></a>Integrace s nástrojem Operations Manager

Nástroj Operations Manager můžete použít pro externí sledování zásobníku Azure. System Center Management Pack pro Microsoft Azure zásobníku umožňuje sledovat více nasazení Azure zásobníku se jedna instance nástroje Operations Manager. Sada management pack používá ke komunikaci s Azure zásobníku poskytovatele prostředků stavu a zprostředkovatele prostředků aktualizace rozhraní REST API. Pokud budete chtít vynechat OEM monitorování software, který běží na hostiteli životního cyklu hardwaru, můžete nainstalovat sady management Pack dodavatele monitorování fyzických serverů. Zjišťování síťových zařízení nástroje Operations Manager můžete použít také k monitorování síťových přepínačů.

Sada management pack pro zásobník Azure poskytuje následující možnosti:

- Můžete spravovat více nasazení Azure zásobníku
- Není poskytována podpora pro Azure Active Directory (Azure AD) a služby Active Directory Federation Services (AD FS)
- Můžete načíst a uzavřít výstrahy
- Je stavu a kapacity řídicí panel
- Zahrnuje režimu údržby Automatická detekce při opravy a aktualizace (P & U) je v průběhu
- Obsahuje vynucené aktualizace úlohy pro nasazení a oblasti
- Můžete přidávat vlastní informace do oblasti
- Podporuje oznámení a vytváření sestav

System Center Management Pack si můžete stáhnout pro Microsoft Azure zásobníku a přidružené [uživatelská příručka](https://www.microsoft.com/en-us/download/details.aspx?id=55184), nebo přímo z nástroje Operations Manager.

Pro tvorbu lístků řešení můžete integrovat nástroje Operations Manager pomocí nástroje System Center Service Manager. Konektor produktů integrované umožňuje obousměrnou komunikaci, která umožňuje zavřít výstrahu v zásobníku Azure a nástrojem Operations Manager po vyřešení žádost o službu na portálu Service Manager.

Následující diagram znázorňuje integrace zásobník Azure s existujícím nasazení produktu System Center. Je možné automatizovat Service Manager další s System Center Orchestrator nebo Service Management Automation (SMA), aby se spustila operace v zásobníku Azure.

![Diagram zobrazující integrace s OM, Service Manager a SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrace s Nagios

Nagios, monitorování modulů plug-in byla vyvinuta společně s Cloudbase partnerských řešení, která je k dispozici v rámci licence projektovou bezplatný software – MIT (Massachusetts Institute of Technology).

Modul plug-in je napsána v Pythonu a využívá zprostředkovatele prostředků stavu rozhraní REST API. Nabízí základní funkce načtení a uzavřít výstrahy v zásobníku Azure. Jako sada management pack System Center můžete přidat více nasazení zásobník Azure a k odeslání oznámení.

Tento modul plug-in pracuje s Nagios Enterprise a Nagios jádra. Můžete ho stáhnout [zde](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Na web pro stahování obsahuje také podrobnosti instalace a konfigurace.

### <a name="plugin-parameters"></a>Parametry modulu plug-in

Konfigurace souboru modulu plug-in "Azurestack_plugin.py" s následujícími parametry:

| Parametr | Popis | Příklad: |
|---------|---------|---------|
| *arm_endpoint* | Koncový bod Azure Resource Manager (správce) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Koncový bod Azure Resource Manager (správce)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID předplatného správce | Načíst prostřednictvím portálu správce nebo prostředí PowerShell |
| *User_name* | Uživatelské jméno předplatné – operátor | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Operátor předplatné heslo | heslo |
| *client_id* | Klient | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *Oblast* |  Název oblasti Azure zásobníku | místní |
|  |  |

* Identifikátor GUID prostředí PowerShell, který je k dispozici je univerzální. Můžete ho použít pro každé nasazení.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Použít PowerShell k monitorování stavu a výstrahy

Pokud nepoužíváte Operations Manager, Nagios nebo na základě Nagios řešení, můžete použít PowerShell k povolení širokou škálu sledování řešení pro integraci s Azure zásobníku.

1. Pomocí prostředí PowerShell, ujistěte se, že máte [prostředí PowerShell nainstalovaný a nakonfigurovaný](azure-stack-powershell-configure-quickstart.md) pro prostředí Azure zásobníku operátor. Instalace prostředí PowerShell na místní počítač, který můžete dosáhnout koncového bodu Resource Manager (správce) (https://adminmanagement. [ Oblast]. [External_FQDN]).

2. Spusťte následující příkazy pro připojení k prostředí Azure zásobníku jako operátor zásobník Azure:

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Použití příkazů jako jsou následující příklady pro práci s výstrahami:
   ```PowerShell
    #Retrieve all alerts
    Get-AzsAlert

    #Filter for active alerts
    $Active=Get-AzsAlert | Where {$_.State -eq "active"}
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    Get-AzsRPHealth

    #Retrieve infrastructure role instance health
    $FRPID=Get-AzsRPHealth|Where-Object {$_.DisplayName -eq "Capacity"}
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId

    ```

## <a name="learn-more"></a>Další informace

Informace o předdefinovaných stavu monitorování najdete v tématu [monitorovat stav a výstrahy v zásobníku Azure](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Další postup

[Integrace zabezpečení](azure-stack-integrate-security.md)
