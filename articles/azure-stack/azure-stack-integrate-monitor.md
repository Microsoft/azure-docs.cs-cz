---
title: Integrace externí řešení pro monitorování pomocí služby Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak Azure Stack integrujte externí řešení monitorování ve vašem datovém centru.
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
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: ff28fbb52b33308967051a37fdaa3c6c273fc282
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816083"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrace externí řešení pro monitorování pomocí služby Azure Stack

Pro externí monitorování infrastruktury Azure stacku, budete muset monitorovat software Azure Stack, fyzické počítače a fyzické síťové přepínače. Každá z těchto oblastí poskytuje metodu pro načtení informací o stavu a výstrahy:

- Azure Stack software nabízí rozhraní API založené na protokolu REST pro získání stavu a výstrahy. Použití technologie softwarově definovaných jako prostory úložiště – přímé, stav úložiště a výstrahy jsou součástí softwaru monitorování.
- Fyzické počítače zpřístupnit stavu a informace o výstrahách pomocí řadiče pro správu základní desky (BMC).
- Fyzická síťová zařízení můžete zpřístupnit stavu a informace o výstrahách prostřednictvím protokolu SNMP.

Každé řešení Azure Stack se dodává s hostiteli životního cyklu hardwaru. Tento hostitel spouští monitorování softwaru výrobce OEM dodavatele hardwaru pro fyzické servery a síťová zařízení. V případě potřeby můžete obejít tato řešení pro monitorování a přímo integrovat se stávajícími řešeními pro monitorování ve vašem datovém centru.

> [!IMPORTANT]
> Externí řešení pro monitorování, které používáte, musí být bez agentů. Nemůžete instalovat agenty třetích stran v Azure stacku komponenty.

Následující diagram znázorňuje tok přenosů mezi systémech pro Azure Stack integrované, životní cyklus hostitelský hardware, externí řešení monitorování a systém kolekce externí zpracování lístků nebo data.

![Diagram znázorňující provoz mezi Azure Stack, monitorování a vytváření tiketů řešení.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Externí integraci přímo s fyzickými servery a síťová zařízení monitorování není povolené a blokované aktivně pomocí seznamů řízení přístupu (ACL). 

Tento článek vysvětluje, jak Azure Stack integrujte externí monitorovacích řešení, jako je System Center Operations Manager a Nagios. Zahrnuje také tom, jak pracovat s výstrahami prostřednictvím kódu programu pomocí Powershellu nebo pomocí volání rozhraní REST API.

## <a name="integrate-with-operations-manager"></a>Integrace s Operations Managerem

Nástroj Operations Manager můžete použít pro externí monitorování služby Azure Stack. System Center Management Pack for Microsoft Azure Stack umožňuje monitorovat více nasazení Azure Stack s jednou instancí nástroje Operations Manager. Sada management pack používá ke komunikaci s Azure Stack stav poskytovatele prostředků a aktualizace poskytovatele prostředků rozhraní REST API. Pokud chcete obejít OEM monitorování software, který běží na hostiteli životního cyklu hardwaru, můžete nainstalovat sady management Pack dodavatele monitorovat fyzické servery. Zjišťování síťových zařízení nástroje Operations Manager můžete použít také k monitorování síťových přepínačů.

Sada management pack pro službu Azure Stack nabízí následující možnosti:

- Může spravovat více nasazení Azure Stack
- Je dostupná podpora pro Azure Active Directory (Azure AD) a Active Directory Federation Services (AD FS)
- Můžete načíst a zavřít upozornění
- Stavu a kapacitu řídicího panelu
- Zahrnuje režim údržby automatického zjišťování pro při opravy a aktualizace (P & U) je v průběhu
- Zahrnuje vynucení aktualizace úlohy pro nasazení a oblast
- Můžete přidat vlastní informace do oblasti
- Podporuje oznámení a vytváření sestav

System Center Management Pack si můžete stáhnout pro Microsoft Azure Stack a přidružené [uživatelská příručka](https://www.microsoft.com/en-us/download/details.aspx?id=55184), nebo přímo z nástroje Operations Manager.

Vytváření tiketů řešení můžete integrovat nástroje Operations Manager pomocí System Center Service Manager. Produkt integrovaný konektor umožňuje obousměrnou komunikaci, která umožňuje zavření výstrahy ve službě Azure Stack a nástroje Operations Manager po vyřešení žádosti o službu na portálu Service Manager.

Následující diagram znázorňuje integrace služby Azure Stack s existujícím nasazením nástroje System Center. Můžete automatizovat další Service Manageru pomocí nástroje System Center Orchestrator nebo Service Management Automation (SMA) pro spouštění operací ve službě Azure Stack.

![Diagram znázorňující integrace s Operations MANAGERU, portálu Service Manager a služby SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrace s Nagios

Nagios monitorování modulů plug-in byla vyvinuta spolu s Cloudbase partnerských řešení, která je k dispozici v rámci licence MIT bezplatný software – MIT (Massachusetts Institute of Technology).

Modul plug-in je napsaný v Pythonu a využívá rozhraní REST API poskytovatele prostředků stavu. Poskytuje základní funkce pro načtení a zavírání výstrah ve službě Azure Stack. Jako je System Center management pack umožňuje vám přidat více nasazení Azure Stack a odesílání oznámení.

Modul plug-in funguje s Nagios Enterprise a Nagios Core. Můžete ji stáhnout [tady](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Server pro stahování obsahuje také podrobnosti o instalaci a konfiguraci.

### <a name="plugin-parameters"></a>Parametry modulu plug-in

Soubor modulu plug-in "Azurestack_plugin.py" konfigurace s následujícími parametry:

| Parametr | Popis | Příklad: |
|---------|---------|---------|
| *arm_endpoint* | Koncový bod Azure Resource Manager (správce) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Koncový bod Azure Resource Manager (správce)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID správce předplatného | Načíst prostřednictvím portálu správce nebo prostředí PowerShell |
| *User_name* | Uživatelské jméno předplatné – operátor | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Hesla předplatného – operátor | méheslo |
| *Client_id* | Klient | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Název oblasti Azure Stack | místní |
|  |  |

* Powershellu identifikátor GUID, který je k dispozici je univerzální. Můžete ho použít pro každé nasazení.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Použití Powershellu k monitorování stavu a výstrahy

Pokud nepoužíváte nástroj Operations Manager, Nagios nebo řešení Nagios, můžete použít PowerShell umožňuje širokou škálu řešení pro integraci s Azure Stackem monitorování.

1. Jak pomocí prostředí PowerShell, ujistěte se, že máte [PowerShell nainstalovaný a nakonfigurovaný](azure-stack-powershell-configure-quickstart.md) pro operátor prostředí Azure Stack. Instalace prostředí PowerShell na místní počítač, který můžete dosáhnout koncového bodu Resource Manager (správce) (https://adminmanagement. [ Oblast]. [External_FQDN]).

2. Spusťte následující příkazy pro připojení k prostředí Azure Stack jako operátory Azure stacku:

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

Informace o předdefinovaných stavu monitorování najdete v tématu [monitorovat stav a výstrahy ve službě Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Další postup

[Integrace zabezpečení](azure-stack-integrate-security.md)
