---
title: Vytvoření interního nástroje pro vyrovnávání zatížení – klasický příkazový řádek Azure CLI | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí rozhraní příkazového řádku Azure v modelu nasazení Classic
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: becbbbde-a118-4269-9444-d3153f00bf34
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 8f0ac03ff7b749e47692d03d65502df0a19bb758
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068619"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Začínáme vytvářet interní nástroj pro vyrovnávání zatížení (Classic) pomocí rozhraní příkazového řádku Azure

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloudové služby](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Vytvoření sady interního nástroje pro vyrovnávání zatížení pro virtuální počítače

Pokud chcete vytvořit sadu interního nástroje pro vyrovnávání zatížení a servery, které do ní budou posílat provoz, musíte provést následující:

1. Vytvořte instanci interního vyrovnávání zatížení, která je koncovým bodem příchozího provozu, u kterého se bude vyrovnávat zatížení napříč servery sady s vyrovnáváním zatížení.
2. Přidejte koncové body odpovídající virtuálním počítačům, které můžou přijímat příchozí provoz.
3. Nakonfigurujte servery tak, aby posílaly provoz na virtuální IP adresu instance interního vyrovnávání zatížení.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Vytvoření interního nástroje pro vyrovnávání zatížení pomocí rozhraní příkazového řádku krok za krokem

Tento průvodce ukazuje, jak vytvořit interní nástroj pro vyrovnávání zatížení založený na výše uvedeném scénáři.

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přejděte na téma [Instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md) a postupujte podle pokynů až do chvíle, kdy můžete vybrat svůj účet a předplatné Azure.
2. Spuštěním příkazu **azure config mode** přejděte do režimu Classic, jak vidíte níže.

    ```azurecli
    azure config mode asm
    ```

    Očekávaný výstup:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Vytvoření koncového bodu a sady nástroje pro vyrovnávání zatížení

Tento scénář předpokládá, že máte virtuální počítače DB1 a DB2 v cloudové službě s názvem mytestcloud. Oba virtuální počítače používají virtuální síť s názvem testvnet s podsítí subnet-1.

Tento průvodce vytvoří sadu interního nástroje pro vyrovnávání zatížení používající port 1433 jako privátní port a port 1433 jako místní port.

Jedná se o běžný scénář, kdy máte virtuální počítače systému SQL na back-endu, který pomocí interního nástroje pro vyrovnávání zatížení zaručuje, že databázové servery nebudou přímo přístupné přes veřejnou IP adresu.

### <a name="step-1"></a>Krok 1

Vytvořte sadu interního nástroje pro vyrovnávání zatížení pomocí příkazu `azure network service internal-load-balancer add`.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

Další informace získáte pomocí příkazu `azure service internal-load-balancer --help`.

Vlastnosti interního nástroje pro vyrovnávání zatížení můžete zkontrolovat pomocí příkazu `azure service internal-load-balancer list` *název cloudové služby*.

Následuje příklad výstupu:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>Krok 2

Sadu interního nástroje pro vyrovnávání zatížení konfigurujete při přidání prvního koncového bodu. V tomto kroku můžete přidružit porty koncového bodu, virtuálního počítače a testu k sadě interního nástroje pro vyrovnávání zatížení.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>Krok 3

Ověřte konfiguraci nástroje pro vyrovnávání zatížení pomocí příkazu `azure vm show` *název virtuálního počítače*.

```azurecli
azure vm show DB1
```

Výstup je následující:

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Vytvoření koncového bodu vzdálené plochy pro virtuální počítač

Koncový bod vzdálené plochy k přesměrování síťového provozu z veřejného portu na místní port konkrétního virtuálního počítače můžete vytvořit pomocí příkazu `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Odebrání virtuálního počítače z nástroje pro vyrovnávání zatížení

Odebrat virtuální počítač ze sady interního nástroje pro vyrovnávání zatížení můžete odstraněním přidruženého koncového bodu. Po odebrání koncového bodu již virtuální počítač nebude patřit do sady nástroje pro vyrovnávání zatížení.

Když použijeme výše uvedený příklad, koncový bod vytvořený pro virtuální počítač DB1 můžete odebrat z interního nástroje pro vyrovnávání zatížení ilbset pomocí příkazu `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

Další informace získáte pomocí příkazu `azure vm endpoint --help`.

## <a name="next-steps"></a>Další kroky

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení pomocí spřažení se zdrojovou IP adresou](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
