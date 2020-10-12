---
title: Řešení potíží s připojením k privátnímu koncovému bodu Azure
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
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77538530"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Řešení potíží s připojením k privátnímu koncovému bodu Azure

Tento článek poskytuje podrobné pokyny k ověření a diagnostikování nastavení privátního koncového bodu připojení Azure.

Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě privátního propojení. Toto řešení vám pomůže zabezpečit vaše úlohy v Azure tím, že poskytuje privátní připojení k prostředkům služby Azure z vaší virtuální sítě. Toto řešení tyto služby efektivně přináší do vaší virtuální sítě.

Tady jsou scénáře připojení, které jsou k dispozici s privátním koncovým bodem:

- Virtuální síť ze stejné oblasti
- Oblastní virtuální sítě s partnerským vztahem
- Globálně partnerské virtuální sítě
- Místní zákazník přes VPN nebo okruhy Azure ExpressRoute

## <a name="diagnose-connectivity-problems"></a>Diagnostika problémů s připojením 

Zkontrolujte tyto kroky, abyste se ujistili, že všechny běžné konfigurace jsou očekávané pro řešení problémů s připojením pomocí nastavení privátního koncového bodu.

1. Projděte si konfiguraci privátního koncového bodu, a to procházením prostředku.

    a. Přejít na **centrum privátních odkazů**.

      ![Centrum privátních odkazů](./media/private-endpoint-tsg/private-link-center.png)

    b. V levém podokně vyberte **soukromé koncové body**.
    
      ![Soukromé koncové body](./media/private-endpoint-tsg/private-endpoints.png)

    c. Vyfiltrujte a vyberte privátní koncový bod, který chcete diagnostikovat.

    d. Zkontrolujte informace o virtuální síti a DNS.
     - Ověřte, zda je stav připojení **schválen**.
     - Ujistěte se, že virtuální počítač má připojení k virtuální síti, která je hostitelem privátních koncových bodů.
     - Ověřte, zda jsou přiřazeny informace o plně kvalifikovaném názvu domény (kopie) a privátní IP adresa.
    
       ![Konfigurace virtuální sítě a DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Použijte [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) k zobrazení toku dat.

    a. V prostředku privátního koncového bodu vyberte **monitor**.
     - Vyberte **data v** nebo **mimo data**. 
     - Podívejte se, jestli při pokusu o připojení k privátnímu koncovému bodu přecházejí data. Očekává se zpoždění přibližně 10 minut.
    
       ![Ověření telemetrie privátních koncových bodů](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Použijte **řešení potíží s připojením k virtuálnímu počítači** z Azure Network Watcher.

    a. Vyberte virtuální počítač klienta.

    b. Vyberte možnost **řešení potíží s připojením**a pak vyberte kartu **odchozí připojení** .
    
      ![Network Watcher – testování odchozích připojení](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. **Pro podrobné trasování připojení vyberte použít Network Watcher**.
    
      ![Network Watcher řešení potíží s připojením](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Vyberte **test podle plně kvalifikovaného názvu domény**.
     - Vložte plně kvalifikovaný název domény z prostředku privátního koncového bodu.
     - Zadejte port. Obvykle použijte 443 pro Azure Storage nebo Azure Cosmos DB a 1336 pro SQL.

    e. Vyberte **test**a ověřte výsledky testu.
    
      ![Výsledky Network Watcher-test](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. Překlad názvů DNS z výsledků testu musí mít přiřazenou stejnou privátní IP adresu jako soukromý koncový bod.

    a. Pokud není nastavení DNS správné, postupujte takto:
     - Používáte-li privátní zónu: 
       - Ujistěte se, že je virtuální síť virtuálních počítačů klienta přidružená k privátní zóně.
       - Zkontrolujte, zda existuje záznam privátní zóny DNS. Pokud neexistuje, vytvořte ji.
     - Pokud používáte vlastní DNS:
       - Zkontrolujte vlastní nastavení DNS a ověřte, jestli je konfigurace DNS správná.
       Pokyny najdete v tématu [Přehled privátního koncového bodu: Konfigurace DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Pokud se připojení nedaří kvůli skupinám zabezpečení sítě (skupin zabezpečení sítě) nebo uživatelem definovaným trasám:
     - Zkontrolujte odchozí pravidla NSG a vytvořte vhodná odchozí pravidla pro povolení provozu.
    
       ![NSG odchozí pravidla](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Pokud připojení obsahuje ověřené výsledky, může se problém s připojením vztahovat k jiným aspektům, jako jsou tajné klíče, tokeny a hesla na vrstvě aplikace.
   - V takovém případě zkontrolujte konfiguraci prostředku privátního propojení přidruženého k privátnímu koncovému bodu. Další informace najdete v [Průvodci odstraňováním potíží s privátními odkazy Azure](troubleshoot-private-link-connectivity.md).

1. Pokud je problém stále nevyřešený a stále existuje problém s připojením, obraťte se na tým [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) .

## <a name="next-steps"></a>Další kroky

 * [Vytvoření privátního koncového bodu v aktualizované podsíti (Azure Portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Průvodce odstraňováním potíží s privátními odkazy Azure](troubleshoot-private-link-connectivity.md)
