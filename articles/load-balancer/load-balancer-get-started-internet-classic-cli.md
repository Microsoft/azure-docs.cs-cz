---
title: Vytvoření internetového nástroje pro vyrovnávání zatížení – Azure classic CLI
titlesuffix: Azure Load Balancer
description: Zjistěte, jak vytvořit internetový nástroj pro vyrovnávání zatížení v modelu nasazení Classic pomocí Azure Classic CLI.
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fa89117e85bc3d3c9664e6aa037fac923b7432ce
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135742"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-cli"></a>Začínáme vytvářet internetový nástroj pro vyrovnávání zatížení (classic) v Azure classic CLI

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure Classic CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Než začnete pracovat s prostředky Azure, je potřeba si uvědomit, že Azure má v současné době dva modely nasazení: Azure Resource Manager a klasický. Před zahájením práce s jakýmikoli prostředky Azure se ujistěte, že rozumíte [modelům nasazení a příslušným nástrojům](../azure-classic-rm.md). Dokumentaci k různým nástrojům můžete zobrazit kliknutím na karty v horní části tohoto článku. Tento článek se týká modelu nasazení Classic. Případně [zjistěte, jak vytvořit internetový nástroj pro vyrovnávání zatížení pomocí Azure Resource Manageru](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [requires-classic-cli](../../includes/contains-classic-cli-content.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>Vytvoření internetového nástroje pro vyrovnávání zatížení pomocí rozhraní příkazového řádku

Tento průvodce ukazuje, jak vytvořit internetový nástroj pro vyrovnávání zatížení založený na výše uvedeném scénáři.

1. Pokud jste Azure Classic CLI nikdy nepoužívali, přečtěte si téma [Instalace a konfigurace Azure Classic CLI](../cli-install-nodejs.md) a postupujte podle pokynů až do chvíle, kdy můžete vybrat svůj účet a předplatné Azure.
2. Spuštěním příkazu **azure config mode** přejděte do režimu Classic, jak vidíte níže.

    ```azurecli
    azure config mode asm
    ```

    Očekávaný výstup:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Vytvoření koncového bodu a sady nástroje pro vyrovnávání zatížení

Tento scénář předpokládá, že byly vytvořeny virtuální počítače web1 a web2.
Tento průvodce vytvoří sadu nástroje pro vyrovnávání zatížení používající port 80 jako veřejný port a port 80 jako místní port. Port testu je také nakonfigurován na port 80 a název sady nástroje pro vyrovnávání zatížení je lbset.

### <a name="step-1"></a>Krok 1

Vytvořte první koncový bod a sadu nástroje pro vyrovnávání zatížení pro virtuální počítač web1 pomocí příkazu `azure network vm endpoint create`.

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>Krok 2

Přidejte do sady nástroje pro vyrovnávání zatížení druhý virtuální počítač web2.

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>Krok 3

Ověřte konfiguraci nástroje pro vyrovnávání zatížení pomocí příkazu `azure vm show`.

```azurecli
azure vm show web1
```

Výstup bude:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Vytvoření koncového bodu vzdálené plochy pro virtuální počítač

Koncový bod vzdálené plochy k přesměrování síťového provozu z veřejného portu na místní port konkrétního virtuálního počítače můžete vytvořit pomocí příkazu `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Odebrání virtuálního počítače z nástroje pro vyrovnávání zatížení

Je třeba odstranit koncový bod přidružený k sadě nástroje pro vyrovnávání zatížení z virtuálního počítače. Po odebrání koncového bodu již virtuální počítač nebude patřit do sady nástroje pro vyrovnávání zatížení.

Když použijeme výše uvedený příklad, koncový bod vytvořený pro virtuální počítač web1 můžete odebrat z nástroje pro vyrovnávání zatížení lbset pomocí příkazu `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> Další možnosti správy koncových bodů můžete prozkoumat pomocí příkazu `azure vm endpoint --help`.

## <a name="next-steps"></a>Další postup

[Začínáme s konfigurací interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
