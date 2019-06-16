---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cd686e1bf62bbd7f37f61ced767e92918edf919c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116911"
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Jak vytvořit klasickou virtuální síť pomocí rozhraní příkazového řádku Azure
Pomocí rozhraní příkazového řádku Azure můžete spravovat svoje prostředky Azure z příkazového řádku jakéhokoliv počítače se systémem Windows, Linux a OS X.

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přejděte na téma [Instalace a konfigurace rozhraní příkazového řádku Azure](../articles/cli-install-nodejs.md) a postupujte podle pokynů až do chvíle, kdy můžete vybrat svůj účet a předplatné Azure.
2. Pokud chcete vytvořit virtuální síť a podsíť, spusťte **sítě azure vnet vytvořit** příkaz:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Očekávaný výstup:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Název sítě VNet, která se má vytvořit. Pro scénář *TestVNet*
   * **-e (nebo--adresního prostoru)** . Adresní prostor virtuální sítě. Pro scénář *192.168.0.0*
   * **-i (nebo - cidr)** . Maska sítě ve formátu CIDR. Pro scénář *16*.
   * **-n (nebo--subnet-name**). Název první podsítě. Pro scénář *front-endu*.
   * **-p (nebo--podsíť start-ip)** . Počáteční IP adresu podsítě a adresní prostor podsítě. Pro scénář *192.168.1.0*.
   * **-r (nebo--podsítě cidr)** . Maska sítě ve formátu CIDR podsítě. Pro scénář *24*.
   * **-l (nebo --location)** . Oblasti Azure, kde se vytvoří virtuální síť. Pro scénář *USA (střed)* .
3. Pokud chcete vytvořit podsítě, spusťte **vytvořit sítě azure vnet podsíť** příkaz:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Očekávaný výstup předchozího příkazu:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (nebo--vnet-name**. Název sítě VNet, ve které se vytvoří podsíť. Pro scénář *TestVNet*.
   * **-n (nebo --name)** . Název nové podsítě. Pro scénář *back-endu*.
   * **-a (nebo --address-prefixes)** . Blok CIDR podsítě. Pro scénář *192.168.2.0/24*.
4. Chcete-li zobrazit vlastnosti nové sítě vnet, spusťte **azure network vnet show** příkaz:
   
            azure network vnet show
   
    Očekávaný výstup předchozího příkazu:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

