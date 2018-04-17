---
title: Škálování Azure Analysis Services | Microsoft Docs
description: Replikace služby Azure Analysis Services serverů se Škálováním na více systémů
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6a340cb3d73e0aaa86a5b7beb555133daed39d8b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-analysis-services-scale-out"></a>Škálování Azure Analysis Services

Se Škálováním na více systémů, můžete dotazy klienta rozdělena mezi více *dotaz repliky* v rámci fondu dotazu snížení doby odezvy během úloh vysoké dotazu. Můžete také oddělit zpracování z fondu dotazu, zajistíte, že dotazy klienta nemá vliv nepříznivě operace zpracování. Škálováním na více systémů se dá nakonfigurovat na portálu Azure nebo pomocí REST API služby Analysis Services.

## <a name="how-it-works"></a>Jak to funguje

V typické serveru nasazení jednoho serveru slouží jako zpracování server i server dotazu. Počet dotazů klienta na modely na vašem serveru překračuje dotaz zpracování jednotky (QPU) pro váš server plán nebo zpracování modelu nastane ve stejnou dobu jako dotaz vysoké zatížení, může snížit výkon. 

Se Škálováním na více systémů můžete vytvořit fond dotazu s replikami až sedm další dotaz (osm celkem, včetně serveru). Je možné škálovat počet replik dotazu splňovat požadavky na QPU v kritické dobu a kdykoli můžete oddělit zpracování server z fondu dotazu. 

Bez ohledu na počet replik dotazu, který máte ve fondu dotazu nejsou úloh zpracování rozdělené mezi repliky dotazu. Jeden server slouží jako server zpracování. Dotaz repliky sloužit pouze na dotazy na modely synchronizovat mezi každou repliku ve fondu dotazu. 

Po dokončení operace zpracování, je nutné provést synchronizaci mezi serverem zpracování a serverem repliky dotazu. Při automatizaci operace zpracování, je potřeba nakonfigurovat operaci synchronizace po úspěšném dokončení operace zpracování. Synchronizace lze provést ručně na portálu nebo pomocí prostředí PowerShell nebo rozhraní REST API.

> [!NOTE]
> Škálováním na více systémů je k dispozici pro servery v cenová úroveň Standard. Každý dotaz repliky se fakturuje stejnou rychlostí jako váš server.

> [!NOTE]
> Škálováním na více systémů nezvyšuje množství dostupné paměti pro váš server. Chcete-li zvýšit paměti, je potřeba upgradovat plán.

## <a name="monitor-qpu-usage"></a>Monitorování QPU využití

 Chcete-li zjistit, jestli se Škálováním na více systémů pro váš server je nutné, sledovat serverem na portálu Azure pomocí metriky. Pokud vaše QPU pravidelně maxes out, znamená to, že počet dotazů na vaše modely překračuje limit QPU pro plán. Délka metrika dotazu fondu úlohy fronty zvyšuje také k dispozici QPU překračuje počet dotazů ve frontě fondu vláken dotazů. Další informace najdete v tématu [Monitorování metrik serveru](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Konfigurace Škálováním na více systémů

### <a name="in-azure-portal"></a>Na portálu Azure

1. Na portálu, klikněte na tlačítko **Škálováním na více systémů**. Posuvníkem vyberte počet serverů repliky dotazu. Počet replik, které zvolíte je kromě existujícího serveru.

2. V **samostatné zpracování server z fondu dotazování**, vyberte možnost Ano, vyloučit zpracování serveru ze serverů dotazu.

   ![Posuvník Škálováním na více systémů](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klikněte na tlačítko **Uložit** ke zřízení vaší nových serverů repliky dotazu. 

Tabulkové modely na primárním serveru jsou synchronizovány se serverem repliky. Po dokončení synchronizace fondu dotazu začne distribuci příchozích dotazů mezi servery repliky. 


## <a name="synchronization"></a>Synchronizace 

Při zřizování nové repliky dotazu Azure Analysis Services automaticky replikuje modely přes všechny repliky. Můžete také provést ruční synchronizaci pomocí portálu nebo REST API. Když při zpracování vaší modely, měli byste provést synchronizaci tak aktualizace jsou synchronizovány mezi repliky dotazu.

### <a name="in-azure-portal"></a>Na portálu Azure

V **přehled** > model > **synchronizovat model**.

![Posuvník Škálováním na více systémů](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API
Použití **synchronizace** operaci.

#### <a name="synchronize-a-model"></a>Synchronizovat model   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Získat stav synchronizace  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Aby bylo možné spustit synchronizaci z prostředí PowerShell, [aktualizovat na nejnovější](https://github.com/Azure/azure-powershell/releases) modulu AzureRM 5.01 nebo vyšší. Použití [synchronizace AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).

## <a name="connections"></a>Připojení

Na stránce Přehled váš server jsou dva názvy serverů. Pokud jste zatím nenakonfigurovali Škálováním na více systémů pro server, oba názvy serverů fungovat stejně. Jakmile nakonfigurujete Škálováním na více systémů pro server, musíte zadat název serveru vhodné v závislosti na typu připojení. 

Pro připojení klienta koncového uživatele, jako jsou Power BI Desktop, Excel a vlastních aplikací, použijte **název serveru**. 

Pro aplikace SSMS, rozšíření SSDT a připojovací řetězce v prostředí PowerShell Azure funkce aplikací a nástroji AMO, použijte **název serveru pro správu**. Název serveru pro správu obsahuje speciální `:rw` kvalifikátor (pro čtení a zápis). Všechny operace zpracování dojít na serveru pro správu.

![Názvy serverů](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Související informace

[Monitorování serveru metriky](analysis-services-monitor.md)   
[Správa služby Azure Analysis Services](analysis-services-manage.md) 

