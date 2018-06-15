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
ms.openlocfilehash: 749cc5a5e5b8417abe602b7e37c103a26cc4dc03
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805178"
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Postup vytvoření klasické virtuální sítě pomocí rozhraní příkazového řádku Azure
Pomocí rozhraní příkazového řádku Azure můžete spravovat svoje prostředky Azure z příkazového řádku jakéhokoliv počítače se systémem Windows, Linux a OS X.

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přejděte na téma [Instalace a konfigurace rozhraní příkazového řádku Azure](../articles/cli-install-nodejs.md) a postupujte podle pokynů až do chvíle, kdy můžete vybrat svůj účet a předplatné Azure.
2. Chcete-li vytvořit virtuální síť a podsíť, spusťte **vytvořit virtuální síť azure sítě** příkaz:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Očekávaný výstup:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Název sítě VNet, která se má vytvořit. Scénář *TestVNet*
   * **-e (nebo--adresní prostor)**. Adresní prostor sítě VNet. Scénář *192.168.0.0*
   * **-i (nebo - cidr)**. Maska sítě ve formátu CIDR. Scénář *16*.
   * **-n (nebo--název podsítě**). Název první podsíť. Scénář *front-endu*.
   * **-p (nebo--IP adresu podsítě start)**. Počáteční IP adresa pro podsíť nebo adresního prostoru podsítě. Scénář *192.168.1.0*.
   * **-r (nebo--podsítě cidr)**. Maska sítě ve formátu CIDR podsítě. Scénář *24*.
   * **-l (nebo --location)**. Oblast Azure, kde se má vytvořit síť VNet. Scénář *střed USA*.
3. Chcete-li vytvořit podsíť, spusťte **sítě azure vnet podsíť vytváření** příkaz:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Očekávaný výstup předchozí příkaz:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (nebo--vnet-name**. Název sítě VNet, ve které se vytvoří podsíť. Scénář *TestVNet*.
   * **-n (nebo --name)**. Název nové podsítě. Scénář *back-end*.
   * **-a (nebo --address-prefixes)**. Blok CIDR podsítě. Scénář *192.168.2.0/24*.
4. Chcete-li zobrazit vlastnosti nové sítě vnet, spusťte **sítě azure vnet show** příkaz:
   
            azure network vnet show
   
    Očekávaný výstup předchozí příkaz:
   
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

