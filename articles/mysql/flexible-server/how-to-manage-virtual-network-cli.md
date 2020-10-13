---
title: Správa virtuálních sítí – Azure CLI-Azure Database for MySQL-flexibilní Server
description: Vytvoření a správa virtuálních sítí pro Azure Database for MySQL flexibilní Server pomocí Azure CLI
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 5cd35b896419dd30a8a4a18056ac1ccd48d7df6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331705"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Vytvoření a správa virtuálních sítí pro Azure Database for MySQL flexibilní Server pomocí Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL flexibilní Server je momentálně ve verzi Public Preview.

Flexibilní server Azure Database for MySQL podporuje dva typy vzájemně se vylučujících metod síťového připojení k flexibilnímu serveru. Tyto dvě možnosti jsou následující:

- Veřejný přístup (povolené IP adresy)
- Privátní přístup (integrace virtuální sítě)

V tomto článku se zaměříme na vytvoření serveru MySQL s **privátním přístupem (Integration VNET) pomocí rozhraní** příkazového řádku Azure CLI. Pomocí *privátního přístupu (Integration VNET)* můžete nasadit flexibilní Server do svého prostředí [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuální sítě Azure poskytují soukromou a zabezpečenou síťovou komunikaci. V privátních přístupech se připojení k serveru MySQL omezují jenom na virtuální síť. Další informace o této službě najdete v tématu [privátní přístup (Integration VNET)](./concepts-networking.md#private-access-vnet-integration).

V Azure Database for MySQL flexibilním serveru můžete nasadit server do virtuální sítě a podsítě během vytváření serveru. Po nasazení flexibilního serveru do virtuální sítě a podsítě ho nemůžete přesunout do jiné virtuální sítě, podsítě ani *veřejného přístupu (povolené IP adresy)*.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Můžete také otevřít Cloud Shell na samostatné kartě prohlížeče, a to tak, že kliknete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a vyberte **ENTER** pro spuštění.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Požadavky

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](https://docs.microsoft.com/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure.

```azurecli-interactive
az login
```

Pomocí příkazu [AZ Account set](https://docs.microsoft.com/cli/azure/account#az-account-set) vyberte konkrétní předplatné ve vašem účtu. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat veškeré předplatné, použijte příkaz [AZ Account list](https://docs.microsoft.com/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Vytvoření Azure Database for MySQL flexibilního serveru pomocí rozhraní příkazového řádku
Pomocí `az mysql flexible-server` příkazu můžete vytvořit flexibilní Server s *privátním přístupem (Integration VNET)*. Tento příkaz jako výchozí metodu připojení používá privátní přístup (Integration VNet). Pokud není zadaná žádná virtuální síť a podsíť, vytvoří se za vás. Existující virtuální síť a podsíť můžete také poskytnout pomocí ID podsítě. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> K dispozici jsou různé možnosti vytvoření flexibilního serveru pomocí rozhraní příkazového řádku, jak je znázorněno v níže uvedených příkladech.

>[!Important]
> Pomocí tohoto příkazu se přenese podsíť do **Microsoft. DBforMySQL/flexibleServers**. Toto delegování znamená, že danou podsíť můžou využívat pouze flexibilní servery Azure Database for MySQL. V delegované podsíti nemůžou být žádné jiné typy prostředků Azure.
>

Úplný seznam konfigurovatelných parametrů rozhraní příkazového řádku najdete v [referenční dokumentaci](/cli/azure/mysql/flexible-server) k rozhraní příkazového řádku Azure CLI. V následujících příkazech můžete například volitelně zadat skupinu prostředků.

- Vytvoření flexibilního serveru pomocí výchozí virtuální sítě, podsítě s výchozí předponou adresy
    ```azurecli-interactive
    az mysql flexible-server create
    ```
<!--- Create a flexible server using already existing virtual network and subnet
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```-->
- Vytvořte flexibilní Server pomocí již existující virtuální sítě, podsítě a s použitím ID podsítě. V zadané podsíti by neměl být nasazen žádný jiný prostředek a tato podsíť bude delegována na **Microsoft. DBforMySQL/flexibleServers**(Pokud již není delegovaná).
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Virtuální síť a podsíť by měly být ve stejné oblasti a předplatném jako flexibilní Server.
<!--
- Create a flexible server using new virtual network, subnet with non-default address prefix
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --vnet-address-prefix 10.0.0.0/24 --subnet mySubnet --subnet-address-prefix 10.0.0.0/24
    ```-->
Úplný seznam konfigurovatelných parametrů rozhraní příkazového řádku najdete v [referenční dokumentaci](/cli/azure/mysql/flexible-server) k rozhraní příkazového řádku Azure CLI.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [sítích v Azure Database for MySQL flexibilním serveru](./concepts-networking.md).
- [Vytvářejte a spravujte Azure Database for MySQL flexibilní serverovou virtuální síť pomocí Azure Portal](./how-to-manage-virtual-network-portal.md).
- Další informace o [Azure Database for MySQL flexibilní serverové virtuální síti](./concepts-networking.md#private-access-vnet-integration).
