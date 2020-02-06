---
title: Řešení problémů s připojením ke soukromým koncovým bodem Azure
description: Podrobné pokyny k diagnostice připojení privátního koncového bodu
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 1c3fc4ebaffe2347a9c46b626b94042d1c9cfe11
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031852"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>Řešení problémů s připojením privátního koncového bodu

Tato příručka poskytuje podrobné pokyny, jak ověřit a diagnostikovat nastavení privátního koncového bodu připojení. 

Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě privátního propojení. Toto řešení vám pomůže zabezpečit vaše úlohy v Azure tím, že poskytuje privátní připojení k prostředkům služby Azure z vaší virtuální sítě. Tím se tyto služby efektivně přinášejí do vaší virtuální sítě. 

Tady jsou scénáře připojení, které jsou k dispozici s privátními koncovými body. 
- virtuální síť ze stejné oblasti 
- oblastní virtuální sítě s partnerským vztahem
- globálně partnerské virtuální sítě
- místní zákazník přes síť VPN nebo okruhy Express Route

## <a name="diagnosing-connectivity-problems"></a>Diagnostikování problémů s připojením 
Přečtěte si níže uvedené kroky, abyste se ujistili, že všechny běžné konfigurace jsou očekávané pro řešení problémů s připojením při instalaci privátního koncového bodu.

1. Kontrola konfigurace privátního koncového bodu tím, že prochází prostředek 

    a) přejít na **centrum privátních odkazů**

      ![Centrum privátních odkazů](./media/private-endpoint-tsg/private-link-center.png)

    b) výběr privátních koncových bodů v levém navigačním podokně
    
      ![Soukromé koncové body](./media/private-endpoint-tsg/private-endpoints.png)

    c) filtr a výběr privátního koncového bodu, který chcete diagnostikovat

    d) zkontrolujte informace o virtuální síti a DNS.
    
     - Ověření stavu připojení je **schváleno** .
     - Ujistěte se, že virtuální počítač má připojení k virtuální síti, která je hostitelem privátních koncových bodů.
     - Informace o plně kvalifikovaném názvu domény (kopie) a přiřazená privátní IP adresa
    
       ![Konfigurace virtuální sítě a DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. Použití [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) ke kontrole toku dat

    a) na prostředku privátního koncového bodu vyberte **monitor** .
     - Vyberte data nebo data a zkontrolujte, jestli při pokusu o připojení k privátnímu koncovému bodu přecházejí data. Očekává se zpoždění přibližně 10 minut.
    
       ![Ověření telemetrie privátních koncových bodů](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. Použití řešení potíží s připojením k virtuálnímu počítači z **Network Watcher**

    a) výběr virtuálního počítače klienta

    b) výběr oddílu **řešení potíží s připojením** , karta **odchozí připojení**
    
      ![Network Watcher – testování odchozích připojení](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) **pro podrobné trasování připojení vyberte použít Network Watcher** .
    
      ![Network Watcher řešení potíží s připojením](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) vyberte **test podle plně kvalifikovaného názvu domény**
     - Vložte plně kvalifikovaný název domény z prostředku privátního koncového bodu.
     - Zadejte port (*obvykle 443 pro Azure Storage nebo Cosmos, 1336 pro SQL...* )

    e) klikněte na **test** a ověřte výsledky testu.
    
      ![Výsledky Network Watcher-test](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. Překlad DNS z výsledků testu musí mít přiřazenou stejnou privátní IP adresu jako soukromý koncový bod.

    a) Pokud je nastavení DNS nesprávné, udělejte toto:
     - Používá se privátní zóna: 
       - Ujistěte se, že virtuální síť virtuálních počítačů je přidružená k privátní zóně
       - Zkontrolujte, jestli existuje záznam Privátní DNS zóny, vytvořit, pokud není existující.
    
     - Používání vlastního DNS:
       - Zkontrolujte nastavení DNS zákazníka a ověřte, jestli je konfigurace DNS správná.
       Přehled najdete v tématu [Přehled privátního koncového bodu – konfigurace DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration) pro doprovodné materiály.

    b) Pokud se připojení nedaří kvůli NSG/udr
     - Zkontrolujte odchozí pravidla NSG a vytvořte vhodná odchozí pravidla pro povolení provozu.
    
       ![NSG odchozí pravidla](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. Pokud připojení obsahuje ověřené výsledky, může se problém s připojením vztahovat k jiným aspektům, jako jsou tajné klíče, tokeny a hesla na vrstvě aplikace.
   - V takovém případě zkontrolujte konfiguraci prostředku privátního propojení přidruženého k privátnímu koncovému bodu. Přečtěte si [příručku pro odstraňování potíží s privátní linkou](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting). 

6. Pokud je problém stále nevyřešený a stále existuje problém s připojením, obraťte se na tým [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) . 

## <a name="next-steps"></a>Další kroky

 * [Vytvoření privátního koncového bodu v aktualizované podsíti (Azure Portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Průvodce odstraňováním potíží privátních odkazů](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting)
