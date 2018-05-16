---
title: Vytvoření internetového nástroje pro vyrovnávání zatížení pro cloudové služby Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit internetový nástroj pro vyrovnávání zatížení pro cloudové služby v modelu nasazení Classic
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.assetid: 0bb16f96-56a6-429f-88f5-0de2d0136756
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: b292739f5f4b7184a24ac9b14d40ed98d4921bde
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Začínáme vytvářet internetový nástroj pro vyrovnávání zatížení pro cloudové služby

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Než začnete pracovat s prostředky Azure, je potřeba si uvědomit, že Azure má v současné době dva modely nasazení: Azure Resource Manager a klasický. Před zahájením práce s jakýmikoli prostředky Azure se ujistěte, že rozumíte [modelům nasazení a příslušným nástrojům](../azure-classic-rm.md). Dokumentaci k různým nástrojům můžete zobrazit kliknutím na karty v horní části tohoto článku. Tento článek se týká modelu nasazení Classic. Případně [zjistěte, jak vytvořit internetový nástroj pro vyrovnávání zatížení pomocí Azure Resource Manageru](load-balancer-get-started-internet-arm-ps.md).

Cloudové služby jsou automaticky nakonfigurované s nástrojem pro vyrovnávání zatížení a lze je přizpůsobit prostřednictvím modelu služby.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Vytvoření nástroje pro vyrovnávání zatížení pomocí definičního souboru služby

K aktualizaci svojí cloudové služby můžete využít sadu Azure SDK pro .NET 2.5. Nastavení koncového bodu pro cloudové služby se provádí v [definičním souboru (.csdef) služby](https://msdn.microsoft.com/library/azure/gg557553.aspx).

Následující příklad ukazuje, jak je nakonfigurován soubor servicedefinition.csdef pro nasazení v cloudu:

Kontrolou fragmentu souboru .csdef vygenerovaného nasazením v cloudu zjistíte, že je externí koncový bod nakonfigurován pro používání protokolu HTTP na portech 10000, 10001 a 10002.

```xml
<ServiceDefinition name="Tenant">
    <WorkerRole name="FERole" vmsize="Small">
        <Endpoints>
            <InputEndpoint name="FE_External_Http" protocol="http" port="10000" />
            <InputEndpoint name="FE_External_Tcp"  protocol="tcp"  port="10001" />
            <InputEndpoint name="FE_External_Udp"  protocol="udp"  port="10002" />

            <InputEndpoint name="HTTP_Probe" protocol="http" port="80" loadBalancerProbe="MyProbe" />

            <InstanceInputEndpoint name="InstanceEP" protocol="tcp" localPort="80">
                <AllocatePublicPortFrom>
                    <FixedPortRange min="10110" max="10120"  />
                </AllocatePublicPortFrom>
            </InstanceInputEndpoint>
            <InternalEndpoint name="FE_InternalEP_Tcp" protocol="tcp" />
        </Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Kontrola stavu nástroje pro vyrovnávání zatížení pro cloudové služby

Následuje příklad testu stavu:

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name="MyProbe" protocol="http" path="Probe.aspx" intervalInSeconds="5" timeoutInSeconds="100" />
</LoadBalancerProbes>
```

Nástroj pro vyrovnávání zatížení zkombinuje informace koncového bodu a informace testu a vytvoří adresu URL ve tvaru `http://{DIP of VM}:80/Probe.aspx`, kterou lze použít k dotazování na stav služby.

Služba detekuje pravidelné testy ze stejné IP adresy. To je požadavek testu stavu přicházející z hostitele uzlu, ve kterém běží virtuální počítač. Služba musí odpovědět stavovým kódem HTTP 200, aby nástroj pro vyrovnávání zatížení předpokládal, že je služba v pořádku. Jakýkoli jiný stavový kód (například 503) přímo vyřadí virtuální počítač ze smyčky.

Definice testu také řídí frekvenci testu. V našem výše uvedeném případě provádí nástroj pro vyrovnávání zatížení testování koncového bodu každých 5 sekund. Pokud nedojde k obdržení kladné odpovědi po dobu 10 sekund (dva intervaly testu), předpokládá se, že je test mimo provoz a virtuální počítač je vyřazen ze smyčky. Podobně pokud je služba vyřazena ze smyčky a obdrží se kladná odpověď, služba se ihned zařadí zpět do smyčky. Pokud stav služby kolísá mezi V pořádku a Není v pořádku, nástroj pro vyrovnávání zatížení může rozhodnout, že se se zařazením služby zpět do smyčky počká, než bude v pořádku po dobu několika testů.

Další informace najdete ve schématu definice služby pro [test stavu](https://msdn.microsoft.com/library/azure/jj151530.aspx).

## <a name="next-steps"></a>Další kroky

[Začínáme s konfigurací interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)

