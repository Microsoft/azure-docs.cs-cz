---
title: Řešení potíží s připojením ke službě Azure Private Link
description: Podrobné pokyny pro diagnostiku připojení soukromých připojení k síti
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
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539463"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Řešení potíží s připojením ke službě Azure Private Link

Tento článek obsahuje podrobné pokyny k ověření a diagnostice připojení pro nastavení Privátní ho spojení Azure.

S Azure Private Link máte přístup k platformě Azure jako ke službám služby (PaaS), jako je Azure Storage, Azure Cosmos DB a Azure SQL Database a služby hostované zákazníky nebo partnery Azure přes privátní koncový bod ve vaší virtuální síti. Přenosmezi virtuální sítí a službou prochází přes páteřní síť Microsoft, což eliminuje expozici z veřejného internetu. Můžete také vytvořit vlastní službu privátního propojení ve virtuální síti a doručit ji soukromě svým zákazníkům.

Můžete povolit službu, která běží za úroveň Standard Azure Load Balancer pro přístup privátní ho spojení. Spotřebitelé vaší služby můžete vytvořit soukromý koncový bod uvnitř své virtuální sítě a mapovat na tuto službu pro přístup k této soukromě.

Zde jsou scénáře připojení, které jsou k dispozici s private link:

- Virtuální síť ze stejné oblasti
- Regionálně partnerské virtuální sítě
- Globálně partnerských virtuálních sítí
- Místní zákazník přes vpn nebo okruhy Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Řešení potíží s nasazením

Projděte si informace o [zakázání zásad sítě ve službě privátního propojení](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) pro případy řešení potíží, kdy nemůžete vybrat zdrojovou IP adresu z podsítě podle vašeho výběru pro službu privátního propojení.

Ujistěte se, že nastavení **privateLinkServiceNetworkPolicies** je zakázáno pro podsíť, ze které vybíráte zdrojovou ADRESU IP.

## <a name="diagnose-connectivity-problems"></a>Diagnostika problémů s připojením

Pokud se setkáte s problémy s připojením s nastavením privátní ho propojení, zkontrolujte tyto kroky a ujistěte se, že všechny obvyklé konfigurace jsou podle očekávání.

1. Zkontrolujte konfiguraci privátního odkazu procházením prostředku.

    a. Přejděte do **centra soukromých spojnic**.

      ![Soukromé centrum odkazů](./media/private-link-tsg/private-link-center.png)

    b. V levém podokně vyberte **možnost Soukromé služby propojení**.

      ![Služby privátního spojení](./media/private-link-tsg/private-link-service.png)

    c. Filtrujte a vyberte službu privátního propojení, kterou chcete diagnostikovat.

    d. Zkontrolujte připojení privátníkoncový bod.
     - Ujistěte se, že soukromý koncový bod, ze kterého hledáte připojení, je uveden se stavem **schváleného** připojení.
     - Pokud je stav **Čeká na vyřízení**, vyberte jej a schvalte.

       ![Připojení privátního koncového bodu](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Výběrem názvu přejděte do soukromého koncového bodu, ze kterého se připojujete. Zkontrolujte, zda se stav připojení zobrazuje jako **Schváleno**.

       ![Přehled připojení privátního koncového bodu](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Po schválení obou stran opakujte připojení.

    e. Zkontrolujte **alias** na kartě **Přehled** a **ID prostředku** na kartě **Vlastnosti.**
     - Ujistěte se, že informace **o Aliasu** a **ID prostředku** odpovídají **Aliasu** a **ID prostředku,** které používáte k vytvoření soukromého koncového bodu pro tuto službu.

       ![Ověřit informace o aliasu](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Ověření informací o ID prostředku](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Zkontrolujte informace o **viditelnosti** na kartě **Přehled.**
     - Ujistěte se, že vaše předplatné spadá do oboru **Viditelnost.**

       ![Ověřit informace o viditelnosti](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Zkontrolujte informace **nástroje pro vyrovnávání zatížení** na kartě **Přehled.**
     - Do ekvizéru zatížení můžete přejít výběrem odkazu pro vyrovnávání zatížení.

       ![Ověření informací o vyrovnávání zatížení](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Ujistěte se, že nastavení vyrovnávání zatížení jsou nakonfigurovány podle vašich očekávání.
       - Zkontrolujte **konfiguraci protokolu IP front-endu**.
       - Zkontrolujte **back-endové fondy**.
       - Zkontrolujte **pravidla vyrovnávání zatížení**.

       ![Ověření vlastností zařízení pro vyrovnávání zatížení](./media/private-link-tsg/pls-ilb-properties.png)

     - Ujistěte se, že zařízení pro vyrovnávání zatížení funguje podle předchozích nastavení.
       - Vyberte virtuální hovado v libovolné podsíti kromě podsítě, kde je k dispozici back-endový fond vyrovnávání zatížení.
       - Zkuste získat přístup k front-endu vykladače zatížení z předchozího virtuálního účtu.
       - Pokud připojení umožňuje připojení do back-endového fondu podle pravidel vyrovnávání zatížení, nástroj pro vyrovnávání zatížení je funkční.
       - Můžete také zkontrolovat metriku vyrovnávání zatížení prostřednictvím Azure Monitoru a zjistit, jestli data proudí prostřednictvím systému vyrovnávání zatížení.

1. Pomocí [Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/overview) zjistěte, jestli data tečou.

    a. V prostředku služby privátního propojení vyberte **metriky**.
     - Vyberte **Možnost Ibajtů v** nebo **Vybajtů ven**.
     - Zjistěte, zda data proudí při pokusu o připojení ke službě privátního propojení. Očekávejte zpoždění přibližně 10 minut.

       ![Ověření metrik služby soukromého propojení](./media/private-link-tsg/pls-metrics.png)

1. Pokud váš problém stále není vyřešen a problém s připojením stále existuje, obraťte se na tým [podpory Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="next-steps"></a>Další kroky

 * [Vytvoření služby privátního propojení (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Průvodce řešením potíží azure private endpoint](troubleshoot-private-endpoint-connectivity.md)
