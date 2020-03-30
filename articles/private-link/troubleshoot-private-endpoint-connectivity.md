---
title: Řešení potíží s připojením k privátnímu koncovému bodu Azure
description: Podrobné pokyny pro diagnostiku připojení privátního koncového bodu
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538530"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Řešení potíží s připojením k privátnímu koncovému bodu Azure

Tento článek obsahuje podrobné pokyny k ověření a diagnostice nastavení připojení Azure Private Endpoint.

Azure Private Endpoint je síťové rozhraní, které vás soukromě a bezpečně propojuje se službou privátního propojení. Toto řešení vám pomůže zabezpečit vaše úlohy v Azure tím, že poskytuje privátní připojení k prostředkům služeb Azure z vaší virtuální sítě. Toto řešení efektivně přináší tyto služby do vaší virtuální sítě.

Tady jsou scénáře připojení, které jsou k dispozici s privátním koncovým bodem:

- Virtuální síť ze stejné oblasti
- Regionálně partnerské virtuální sítě
- Globálně partnerských virtuálních sítí
- Místní zákazník přes vpn nebo okruhy Azure ExpressRoute

## <a name="diagnose-connectivity-problems"></a>Diagnostika problémů s připojením 

Zkontrolujte tyto kroky a ujistěte se, že všechny obvyklé konfigurace jsou podle očekávání k vyřešení problémů s připojením s nastavením soukromého koncového bodu.

1. Zkontrolujte konfiguraci soukromého koncového bodu procházením prostředku.

    a. Přejděte do **centra soukromých spojnic**.

      ![Soukromé centrum odkazů](./media/private-endpoint-tsg/private-link-center.png)

    b. V levém podokně vyberte **Soukromé koncové body**.
    
      ![Soukromé koncové body](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtrujte a vyberte soukromý koncový bod, který chcete diagnostikovat.

    d. Zkontrolujte virtuální síť a informace DNS.
     - Ověřte, zda je stav připojení **schválen**.
     - Ujistěte se, že virtuální počítač má připojení k virtuální síti, která hostuje privátní koncové body.
     - Zkontrolujte, zda jsou přiřazeny informace (kopie) a privátní IP adresy.
    
       ![Konfigurace virtuální sítě a DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Pomocí [Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/overview) zjistěte, jestli data tečou.

    a. U prostředku privátního koncového bodu vyberte **sledovat**.
     - Vyberte **Data dovnitř** nebo **Data Out**. 
     - Zjistěte, zda data proudí při pokusu o připojení k privátnímu koncovému bodu. Očekávejte zpoždění přibližně 10 minut.
    
       ![Ověření privátní telemetrie koncového bodu](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Použijte **řešení potíží s připojením virtuálního** počítače z Azure Network Watcher.

    a. Vyberte virtuální virtuální ms klienta.

    b. Vyberte **Poradce při potížích s připojením**a pak vyberte kartu **Odchozí připojení.**
    
      ![Sledování sítě – testování odchozích připojení](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Vyberte **použít sledovací modul sítě pro podrobné trasování připojení**.
    
      ![Sledování sítě – poradce při potížích s připojením](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Vyberte **možnost Testovat podle funkce ReQDN**.
     - Vložte hlavní název souboru z prostředku privátního koncového bodu.
     - Poskytněte port. Obvykle použijte 443 pro Azure Storage nebo Azure Cosmos DB a 1336 pro SQL.

    e. Vyberte **test**a ověřte výsledky testů.
    
      ![Sledování sítě – výsledky testů](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. Rozlišení DNS z výsledků testu musí mít stejnou privátní IP adresu přiřazenou privátnímu koncovému bodu.

    a. Pokud jsou nastavení DNS nesprávná, postupujte takto:
     - Pokud používáte soukromou zónu: 
       - Ujistěte se, že virtuální síť virtuálního počítače klienta je přidružena k privátní zóně.
       - Zkontrolujte, zda existuje záznam soukromé zóny DNS. Pokud neexistuje, vytvořte ji.
     - Pokud používáte vlastní DNS:
       - Zkontrolujte vlastní nastavení DNS a ověřte správnou konfiguraci DNS.
       Pokyny naleznete v [tématu Přehled soukromého koncového bodu: Konfigurace DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Pokud připojení selhává z důvodu skupin zabezpečení sítě (NSG) nebo uživatelem definovaných tras:
     - Zkontrolujte odchozí pravidla sítě nsg a vytvořte příslušná odchozí pravidla, která povolí provoz.
    
       ![Odchozí pravidla nsg](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Pokud připojení má ověřené výsledky, problém s připojením může souviset s jinými aspekty, jako jsou tajné klíče, tokeny a hesla ve vrstvě aplikace.
   - V takovém případě zkontrolujte konfiguraci prostředku privátního propojení přidruženého k privátnímu koncovému bodu. Další informace naleznete v [průvodci odstraňováním potíží azure private link](troubleshoot-private-link-connectivity.md).

1. Pokud váš problém stále není vyřešen a problém s připojením stále existuje, obraťte se na tým [podpory Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="next-steps"></a>Další kroky

 * [Vytvoření privátního koncového bodu v aktualizované podsíti (portál Azure)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Průvodce odstraňováním potíží azure private link](troubleshoot-private-link-connectivity.md)
