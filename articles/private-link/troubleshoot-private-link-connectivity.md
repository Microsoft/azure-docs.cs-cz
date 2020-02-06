---
title: Řešení potíží s připojením k privátním linkám Azure
description: Podrobné pokyny pro diagnostiku připojení pomocí privátního propojení
services: private-link
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
ms.openlocfilehash: 667fa1c85c63ffb87e49c4bf99112f57d0c85a72
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031917"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Řešení potíží s připojením služby Private Link

Tato příručka poskytuje podrobné pokyny, jak ověřit a diagnostikovat připojení pro instalaci služby privátních odkazů. 

Privátní odkaz Azure vám umožní přístup ke službám Azure PaaS (například Azure Storage, Azure Cosmos DB a SQL Database) a hostovaným zákazníkům a partnerským službám Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Ve své virtuální síti můžete také vytvořit vlastní službu privátního propojení a poskytnout ji soukromým uživatelům. 

Službu spuštěnou za službou Azure Standard Load Balancer můžete povolit pro přístup přes soukromé odkazy. Spotřebitelé vaší služby můžou vytvořit privátní koncový bod uvnitř své virtuální sítě a namapovat ho na tuto službu, aby k nim měli přístup soukromě.

Tady jsou scénáře připojení, které jsou k dispozici ve službě privátního propojení.
- virtuální síť ze stejné oblasti 
- oblastní virtuální sítě s partnerským vztahem
- globálně partnerské virtuální sítě
- místní zákazník přes síť VPN nebo okruhy Express Route

## <a name="deployment-troubleshooting"></a>Řešení potíží s nasazením

Přečtěte si informace o [zakázání zásad sítě ve službě privátního propojení](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) pro případy, kdy nemůžete vybrat zdrojovou IP adresu z podsítě dle vašeho výběru pro službu privátního propojení.

Ujistěte se, že nastavení **privateLinkServiceNetworkPolicies** je pro podsíť, ze které vybíráte zdrojovou IP adresu, zakázané.

## <a name="diagnosing-connectivity-problems"></a>Diagnostikování problémů s připojením

Pokud dochází k problémům s připojením k nastavení privátního propojení, přečtěte si níže uvedené kroky a ujistěte se, že všechny běžné konfigurace jsou očekávané.

1. Kontrola konfigurace služby privátního propojení procházením prostředku 

    a) přejít na **centrum privátních odkazů**

      ![Centrum privátních odkazů](./media/private-link-tsg/private-link-center.png)

    b) v levém navigačním podokně vyberte **služby privátního propojení** .

      ![Služby privátního propojení](./media/private-link-tsg/private-link-service.png)

    c) filtr a výběr služby privátního propojení, kterou chcete diagnostikovat

    d) Kontrola připojení privátního koncového bodu
     - Ujistěte se, že se soukromý koncový bod, ze kterého se snažíte připojit, nachází v seznamu se **schváleným** stavem připojení. 
     - Pokud **čeká na vyřízení**, vyberte ho a schvalte. 

       ![Připojení privátního koncového bodu](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Kliknutím na název přejděte do privátního koncového bodu, ze kterého se připojujete. Ujistěte se, že se stav připojení zobrazuje jako **schváleno**.

       ![Přehled připojení privátního koncového bodu](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Po schválení obou stran zkuste připojení znovu.

    e) Zkontrolujte **alias** z karty Přehled a **ID prostředku** z karty Vlastnosti. 
     - Ujistěte se, že **ID aliasu nebo prostředku** se shoduje s **ID aliasu nebo prostředku** , který používáte k vytvoření privátního koncového bodu pro tuto službu. 

       ![Ověřit alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Ověřit ID prostředku](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) kontrola viditelnosti (oddíl přehledu) informace
     - Ujistěte se, že vaše předplatné spadá do oboru **viditelnosti** .

       ![Ověřit viditelnost](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) Kontrola informací Load Balancer (přehled)
     - Kliknutím na odkaz služby Vyrovnávání zatížení můžete přejít na nástroj pro vyrovnávání zatížení.

       ![Ověřit Load Balancer](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Ujistěte se, že nastavení Load Balancer jsou nakonfigurovaná podle vašich očekávání.
       - Kontrola konfigurace IP adresy front-endu
       - Kontrola back-end fondů
       - Zkontrolovat pravidla vyrovnávání zatížení

       ![Ověřit vlastnosti Load Balancer](./media/private-link-tsg/pls-ilb-properties.png)

     - Ujistěte se, že Load Balancer pracuje jako na výše uvedeném nastavení.
       - Vyberte virtuální počítač v jiné podsíti, než je podsíť, ve které je dostupný Load Balancer fond back-endu.
       - Zkuste získat přístup k front-endu nástroje pro vyrovnávání zatížení z virtuálního počítače výše.
       - Pokud se připojení nastaví do back-endu na základě pravidel vyrovnávání zatížení, je váš nástroj pro vyrovnávání zatížení funkční.
       - Můžete také zkontrolovat metriku Load Balancer prostřednictvím Azure Monitor a zjistit, jestli data přecházejí přes Nástroj pro vyrovnávání zatížení.

2. Použití [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) ke kontrole toku dat

    a) v prostředku služby privátního propojení vyberte **metriky** .
     - Výběr **bajtů-in** nebo **bajtů na** více instancí
     - Kontrola toku dat probíhá při pokusu o připojení ke službě privátního propojení. Očekává se zpoždění přibližně 10 minut.

       ![Ověřit metriky služby privátního propojení](./media/private-link-tsg/pls-metrics.png)

3. Pokud je problém stále nevyřešený a stále existuje problém s připojením, obraťte se na tým [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) . 

## <a name="next-steps"></a>Další kroky

 * [Vytvoření služby privátního propojení (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [Průvodce odstraňováním potíží privátního koncového bodu](https://docs.microsoft.com/azure/private-link/private-endpoint-connectivity-troubleshooting)
