---
title: Analýza protokolů diagnostiky virtuální plochy Windows – Azure
description: Použití analýzy protokolů pomocí funkce diagnostiky virtuální plochy systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127970"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Použití analýzy protokolů pro funkci diagnostiky

Windows Virtual Desktop nabízí diagnostickou funkci, která umožňuje správci identifikovat problémy prostřednictvím jediného rozhraní. Tato funkce protokoluje diagnostické informace vždy, když někdo přiřadil roli Virtuální plocha systému Windows používá službu. Každý protokol obsahuje informace o roli virtuální plochy systému Windows, která byla zapojena do aktivity, všechny chybové zprávy, které se zobrazí během relace, informace o klientovi a informace o uživateli. Funkce diagnostiky vytváří protokoly aktivit pro akce uživatele i pro správu. Každý protokol aktivit spadá do tří hlavních kategorií: 

- Aktivity odběru informačního kanálu: když se uživatel pokusí připojit ke svému kanálu prostřednictvím aplikací vzdálené plochy společnosti Microsoft.
- Aktivity připojení: když se uživatel pokusí připojit k ploše nebo aplikaci RemoteApp prostřednictvím aplikací vzdálené plochy společnosti Microsoft.
- Aktivity správy: když správce provádí operace správy v systému, jako je vytváření fondů hostitelů, přiřazování uživatelů ke skupinám aplikací a vytváření přiřazení rolí.

Připojení, která nedosáhnou windows virtuální plochy se nezobrazí ve výsledcích diagnostiky, protože samotná služba role diagnostiky je součástí virtuální plochy systému Windows. Problémy s připojením k virtuální ploše systému Windows mohou nastat, když se uživateli vyskytnou problémy s připojením k síti.

## <a name="why-you-should-use-log-analytics"></a>Proč byste měli používat Log Analytics

Doporučujeme použít Log Analytics k analýze diagnostických dat v klientovi Azure, který přesahuje řešení problémů pro jednoho uživatele. Jak můžete vytáhnout čítače výkonu virtuálních počítače do Log Analytics máte jeden nástroj pro shromažďování informací pro vaše nasazení.

## <a name="before-you-get-started"></a>Než začnete

Před použitím analýzy protokolů s funkcí diagnostiky budete muset [vytvořit pracovní prostor](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace).

Po vytvoření pracovního prostoru postupujte podle pokynů v [části Připojení počítačů s Windows k Azure Monitoru](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) a získejte následující informace: 

- ID pracovního prostoru
- Primární klíč pracovního prostoru

Tyto informace budete potřebovat později v procesu instalace.

## <a name="push-diagnostics-data-to-your-workspace"></a>Nabízení diagnostických dat do pracovního prostoru 

Diagnostická data z klienta virtuální plochy Windows můžete zasouvat do analýzy protokolů pro váš pracovní prostor. Tuto funkci můžete nastavit hned při prvním vytvoření tenanta propojením pracovního prostoru s tenantem, nebo ji můžete nastavit později s existujícím tenantem.

Chcete-li propojit svého klienta s pracovním prostorem Log Analytics při nastavování nového tenanta, spusťte následující rutinu pro přihlášení k virtuální ploše Windows pomocí uživatelského účtu TenantCreator: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Pokud chcete propojit existujícího klienta místo nového tenanta, spusťte místo toho tuto rutinu: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Budete muset spustit tyto rutiny pro každého klienta, který chcete propojit s Log Analytics. 

>[!NOTE]
>Pokud nechcete propojit pracovní prostor Log Analytics při vytváření klienta, spusťte rutinu `New-RdsTenant` místo. 

## <a name="cadence-for-sending-diagnostic-events"></a>Kadence pro odesílání diagnostických událostí

Diagnostické události jsou odesílány do analýzy protokolů po dokončení.  

## <a name="example-queries"></a>Ukázkové dotazy

Následující ukázkové dotazy ukazují, jak funkce diagnostiky generuje sestavu pro nejčastější aktivity v systému:

Tento první příklad ukazuje aktivity připojení iniciované uživateli s podporovanými klienty vzdálené plochy:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

Následující příklad dotazu ukazuje aktivity správy podle správců v tenantech:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Ukončení odesílání dat do analýzy protokolů 

Chcete-li zastavit odesílání dat z existujícího klienta do služby Log Analytics, spusťte následující rutinu a nastavte prázdné řetězce:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Tuto rutinu budete muset spustit pro každého klienta, ze kterého chcete přestat odesílat data. 

## <a name="next-steps"></a>Další kroky 

Chcete-li zkontrolovat běžné scénáře chyb, které vám může diagnostikovat, přečtěte si téma [Identifikace a diagnostika problémů](diagnostics-role-service.md#common-error-scenarios).
