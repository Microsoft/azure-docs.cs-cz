---
title: Horizontální navýšení kapacity Azure Analysis Services | Dokumentace Microsoftu
description: Replikace služby Azure Analysis Services serverů se Škálováním
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f89a6bdbe906d490231725cf528396928faebe47
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092090"
---
# <a name="azure-analysis-services-scale-out"></a>Horizontální navýšení kapacity Azure Analysis Services

S horizontálním navýšením dotazy klientů můžou distribuovat mezi více *replikami dotazů* ve fondu dotazů, snížení doby odezvy během dotazu vysoké zatížení. Můžete oddělit i zpracování od fondu dotazů, zajistit, že dotazy klientů neměly nepříznivý vliv na zpracování. Horizontální navýšení kapacity se dá nakonfigurovat na webu Azure portal nebo pomocí rozhraní REST API pro Analysis Services.

## <a name="how-it-works"></a>Jak to funguje

V typické server nasazení jednoho serveru slouží jako server pro zpracování a dotazu serveru. Počet klientů dotazy na modely na vašem serveru překračuje dotaz zpracování jednotky (QPU) pro váš server plán nebo zpracování modelu nastane ve stejnou dobu jako dotaz vysoké zatížení, může se snížit výkon. 

S horizontální navýšení kapacity můžete vytvořit fond dotazů s až sedmi dalšími replikami dotazů (celkově osmi, včetně vašeho serveru). Můžete škálovat počet replik dotazu s cílem splnit požadavky QPU v kritické dobu a server pro zpracování od fondu dotazů můžete oddělit kdykoli. Všechny repliky dotazu se vytvoří ve stejné oblasti jako váš server.

Bez ohledu na počet replik dotazu, které máte ve fondu dotazů zpracování úloh nejsou distribuovány mezi replikami dotazu. Jeden server slouží jako server pro zpracování. Repliky dotazů sloužit pouze dotazy na modely synchronizovány mezi každou repliku ve fondu dotazů. 

Po dokončení operace zpracování, je nutné provést synchronizaci mezi serverem pro zpracování a server repliky dotazů. Při automatizaci operace zpracování, je potřeba nakonfigurovat operace synchronizace po úspěšném dokončení operace zpracování. Synchronizace lze ručně provést na portálu nebo pomocí Powershellu nebo rozhraní REST API.

> [!NOTE]
> Horizontální navýšení kapacity je k dispozici pro servery v cenovou úroveň Standard. Každé repliky dotazů se účtuje stejná sazba jako váš server.

> [!NOTE]
> Horizontální navýšení kapacity nezvyšuje velikost dostupné paměti pro váš server. Pro zvýšení paměti, budete muset upgradovat svůj plán.

## <a name="region-limits"></a>Omezení oblasti

Počet replik dotazu, které můžete nakonfigurovat se uplatňuje limit vycházející oblast, kterou je server v. Další informace najdete v tématu [dostupnost podle oblasti](analysis-services-overview.md#availability-by-region).

## <a name="monitor-qpu-usage"></a>Monitorování využití QPU

 Chcete-li zjistit, zda horizontální navýšení kapacity pro váš server, je nezbytné, sledování serveru na webu Azure portal pomocí metrik. Pokud vaše QPU pravidelně navyšuje navýšení kapacity, znamená to, že počet dotazů vůči vašich modelů je překročení limitu QPU pro váš plán. Délka metrika dotazu fondu úlohy fronty také zvýší v případě, že k dispozici QPU překračuje počet dotazů ve frontě fondu vláken dotazů. Další informace najdete v tématu [Monitorování metrik serveru](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Konfigurace horizontální navýšení kapacity

### <a name="in-azure-portal"></a>Na webu Azure portal

1. Na portálu klikněte na tlačítko **horizontální navýšení kapacity**. Pomocí posuvníku vyberte počet serverů replik dotazu. Počet replik, které zvolíte, je kromě existující server.

2. V **oddělte server pro zpracování od fondu dotazů**, vyberte Ano. Pokud chcete vyloučit ze serverů dotazu serveru zpracování.

   ![Horizontální navýšení kapacity posuvníku](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klikněte na tlačítko **Uložit** ke zřízení nových serverů repliky dotazu. 

Tabulkové modely na primárním serveru jsou synchronizovány se serverem repliky. Po dokončení synchronizace fondu dotazů začne distribuci příchozích dotazů mezi serverem repliky. 


## <a name="synchronization"></a>Synchronizace 

Při zřizování nové repliky dotazů Azure Analysis Services automaticky replikuje vaše modely přes všechny repliky. Můžete provést také ruční synchronizaci pomocí portálu nebo rozhraní REST API. Při zpracování vašich modelů, měli byste provést synchronizaci tak synchronizaci aktualizací mezi repliky dotazů.

### <a name="in-azure-portal"></a>Na webu Azure portal

V **přehled** > model > **synchronizovat model**.

![Horizontální navýšení kapacity posuvníku](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API
Použití **synchronizace** operace.

#### <a name="synchronize-a-model"></a>Synchronizovat model   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Získat stav synchronizace  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Před použitím prostředí PowerShell, [nainstalovat nebo aktualizovat nejnovější modul AzureRM](https://github.com/Azure/azure-powershell/releases). 

Pokud chcete nastavit počet replik dotazu, použijte [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Zadejte nepovinný `-ReadonlyReplicaCount` parametru.

Chcete-li spustit synchronizaci, použijte [synchronizace AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).



## <a name="connections"></a>Připojení

Na stránce přehled vašeho serveru jsou dva názvy serverů. Pokud jste ještě nenakonfigurovali horizontální navýšení kapacity pro server, oba názvy serverů fungují stejně. Jakmile nakonfigurujete horizontální navýšení kapacity pro server, musíte zadat název příslušného serveru v závislosti na typu připojení. 

Pro připojení klienta koncového uživatele, jako jsou Power BI Desktopu, Excelu a vlastní aplikace, použijte **název serveru**. 

Pro aplikace SSMS, SSDT a připojovací řetězce v prostředí PowerShell, aplikace Azure Function App a nástroji AMO, použijte **název serveru pro správu**. Název serveru pro správu obsahuje speciální `:rw` kvalifikátor (čtení a zápis). Všechny operace zpracování dojít na serveru pro správu.

![Názvy serverů](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Související informace

[Monitorování metrik serveru](analysis-services-monitor.md)   
[Správa služby Azure Analysis Services](analysis-services-manage.md) 

