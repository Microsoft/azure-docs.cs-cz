---
title: Řešení potíží s připojením ke službě Azure Private Link
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
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544305"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Řešení potíží s připojením ke službě Azure Private Link

Tento článek poskytuje podrobné pokyny k ověření a diagnostice připojení pro nastavení privátního propojení Azure.

Pomocí privátního odkazu Azure získáte přístup ke službám Azure jako služby (PaaS), jako jsou Azure Storage, Azure Cosmos DB a Azure SQL Database a hostované služby zákazníkům nebo partnery v Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti. Přenosy mezi vaší virtuální sítí a službou procházejí přes páteřní síť Microsoftu, která eliminuje expozici veřejného Internetu. Ve své virtuální síti můžete také vytvořit vlastní službu privátního propojení a poskytnout ji soukromým uživatelům.

Můžete povolit službu, která běží za standardní úrovní Azure Load Balancer pro přístup k privátním linkám. Spotřebitelé vaší služby můžou vytvořit privátní koncový bod uvnitř své virtuální sítě a namapovat ho na tuto službu, aby k nim měli přístup soukromě.

Tady jsou scénáře připojení, které jsou k dispozici s privátním odkazem:

- Virtuální síť ze stejné oblasti
- Oblastní virtuální sítě s partnerským vztahem
- Globálně partnerské virtuální sítě
- Místní zákazník přes VPN nebo okruhy Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Řešení potíží s nasazením

Přečtěte si informace o [zakázání zásad sítě ve službě privátního propojení](./disable-private-link-service-network-policy.md) pro případy, kdy nemůžete vybrat zdrojovou IP adresu z podsítě dle vašeho výběru pro službu privátního propojení.

Ujistěte se, že nastavení **privateLinkServiceNetworkPolicies** je pro podsíť, ze které vybíráte zdrojovou IP adresu, zakázané.

## <a name="diagnose-connectivity-problems"></a>Diagnostika problémů s připojením

Pokud dojde k problémům s připojením k nastavení privátních odkazů, zkontrolujte tyto kroky, abyste se ujistili, že všechny běžné konfigurace jsou očekávané.

1. Projděte si konfiguraci privátního odkazu procházením prostředku.

    a. Přejít na **centrum privátních odkazů**.

      ![Centrum privátních odkazů](./media/private-link-tsg/private-link-center.png)

    b. V levém podokně vyberte **služby privátního propojení**.

      ![Služby privátního propojení](./media/private-link-tsg/private-link-service.png)

    c. Vyfiltrujte a vyberte službu privátního propojení, kterou chcete diagnostikovat.

    d. Zkontrolujte připojení privátního koncového bodu.
     - Ujistěte se, že se soukromý koncový bod, ze kterého se snažíte připojit, nachází v seznamu se **schváleným** stavem připojení.
     - Pokud stav čeká na **vyřízení**, vyberte ho a schvalte.

       ![Připojení privátního koncového bodu](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Vyberte název v privátním koncovém bodu, ze kterého se připojujete. Ujistěte se, že se stav připojení zobrazuje jako **schváleno**.

       ![Přehled připojení privátního koncového bodu](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Po schválení obou stran zkuste připojení znovu.

    e. Podívejte se na **alias** na kartě **Přehled** a na **ID prostředku** na kartě **vlastnosti** .
     - Ujistěte se, že informace o **aliasu** a **ID prostředku** se shodují s **aliasem** a **ID prostředku** , které používáte k vytvoření privátního koncového bodu pro tuto službu.

       ![Ověřit informace o aliasu](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Ověřit informace o ID prostředku](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Projděte si informace o **viditelnosti** na kartě **Přehled** .
     - Ujistěte se, že vaše předplatné spadá do oboru **viditelnosti** .

       ![Ověřit informace o viditelnosti](./media/private-link-tsg/pls-overview-pane-visibility.png)

    například Informace o **nástroji pro vyrovnávání zatížení** najdete na kartě **Přehled** .
     - Můžete přejít do nástroje pro vyrovnávání zatížení tak, že vyberete odkaz služby Vyrovnávání zatížení.

       ![Ověření informací o nástroji pro vyrovnávání zatížení](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Ujistěte se, že nastavení nástroje pro vyrovnávání zatížení jsou nakonfigurovaná podle vašich očekávání.
       - Zkontrolujte **konfiguraci protokolu IP front-endu**.
       - Zkontrolujte **fondy back-endu**.
       - Zkontrolujte **pravidla vyrovnávání zatížení**.

       ![Ověření vlastností nástroje pro vyrovnávání zatížení](./media/private-link-tsg/pls-ilb-properties.png)

     - Ujistěte se, že nástroj pro vyrovnávání zatížení funguje podle předchozích nastavení.
       - Vyberte virtuální počítač v jiné podsíti, než je podsíť, ve které je k dispozici fond back-end služby Vyrovnávání zatížení.
       - Zkuste získat přístup k front-endu nástroje pro vyrovnávání zatížení z předchozího virtuálního počítače.
       - Pokud připojení vytvoří fond back-end podle pravidel vyrovnávání zatížení, je váš nástroj pro vyrovnávání zatížení funkční.
       - Můžete také zkontrolovat metriku nástroje pro vyrovnávání zatížení prostřednictvím Azure Monitor a zjistit, jestli data procházejí nástrojem pro vyrovnávání zatížení.

1. Použijte [Azure monitor](../azure-monitor/overview.md) k zobrazení toku dat.

    a. V prostředku služby privátního propojení vyberte **metriky**.
     - Vyberte **bajty v bajtech nebo v** **bajtech**.
     - Podívejte se, jestli data při pokusu o připojení ke službě privátního propojení přecházejí. Očekává se zpoždění přibližně 10 minut.

       ![Ověřit metriky služby privátního propojení](./media/private-link-tsg/pls-metrics.png)

1. Pokud je problém stále nevyřešený a stále existuje problém s připojením, obraťte se na tým [podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) .

## <a name="next-steps"></a>Další kroky

 * [Vytvoření služby privátního propojení (CLI)](./create-private-link-service-cli.md)
 * [Průvodce odstraňováním potíží privátního koncového bodu Azure](troubleshoot-private-endpoint-connectivity.md)