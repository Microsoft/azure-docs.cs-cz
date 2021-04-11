---
title: Správa virtuálních sítí – Azure CLI-Azure Database for PostgreSQL-flexibilní Server
description: Vytvoření a správa virtuálních sítí pro Azure Database for PostgreSQL flexibilní Server pomocí Azure CLI
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2bc7bbd7a50b5771d794fbf35844311e3deddbbd
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226969"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Vytvoření a správa virtuálních sítí pro Azure Database for PostgreSQL flexibilní Server pomocí Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Azure Database for PostgreSQL – flexibilní Server podporuje dva typy vzájemně se vylučujících metod síťového připojení, které se připojují k flexibilnímu serveru. Tyto dvě možnosti jsou následující:

* Veřejný přístup (povolené IP adresy)
* Privátní přístup (integrace virtuální sítě)

V tomto článku se zaměříme na vytváření PostgreSQL serveru pomocí **privátního přístupu (Integration VNET)** pomocí Azure CLI. Pomocí *privátního přístupu (Integration VNET)* můžete nasadit flexibilní Server do svého prostředí [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuální sítě Azure poskytují soukromou a zabezpečenou síťovou komunikaci. V privátních přístupech se připojení k serveru PostgreSQL omezují jenom na virtuální síť. Další informace o této službě najdete v tématu [privátní přístup (Integration VNET)](./concepts-networking.md#private-access-vnet-integration).

V Azure Database for PostgreSQLm flexibilním serveru můžete nasadit server do virtuální sítě a podsítě během vytváření serveru. Po nasazení flexibilního serveru do virtuální sítě a podsítě ho nemůžete přesunout do jiné virtuální sítě, podsítě ani *veřejného přístupu (povolené IP adresy)*.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Můžete také otevřít Cloud Shell na samostatné kartě prohlížeče, a to tak, že kliknete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a vyberte **ENTER** pro spuštění.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Požadavky

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure.

```azurecli-interactive
az login
```

Pomocí příkazu [AZ Account set](/cli/azure/account#az-account-set) vyberte konkrétní předplatné ve vašem účtu. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat veškeré předplatné, použijte příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>Vytvoření Azure Database for PostgreSQL-flexibilního serveru pomocí rozhraní příkazového řádku
Pomocí `az postgres flexible-server` příkazu můžete vytvořit flexibilní Server s *privátním přístupem (Integration VNET)*. Tento příkaz jako výchozí metodu připojení používá privátní přístup (Integration VNet). Pokud není zadaná žádná virtuální síť a podsíť, vytvoří se za vás. Existující virtuální síť a podsíť můžete také poskytnout pomocí ID podsítě. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> K dispozici jsou různé možnosti vytvoření flexibilního serveru pomocí rozhraní příkazového řádku, jak je znázorněno v níže uvedených příkladech.

>[!Important]
> Pomocí tohoto příkazu se přenese podsíť do **Microsoft. DBforPostgreSQL/flexibleServers**. Toto delegování znamená, že tuto podsíť můžou používat jenom Azure Database for PostgreSQL flexibilní servery. V delegované podsíti nemůžou být žádné jiné typy prostředků Azure.
>
Informace najdete v referenční dokumentaci k Azure CLI. <!--FIXME --> Úplný seznam konfigurovatelných parametrů rozhraní příkazového řádku. V následujících příkazech můžete například volitelně zadat skupinu prostředků.

- Vytvoření flexibilního serveru pomocí výchozí virtuální sítě, podsítě s výchozí předponou adresy
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- Vytvořte flexibilní Server pomocí již existující virtuální sítě a podsítě. Pokud zadaná virtuální síť a podsíť neexistují, vytvoří se virtuální síť a podsíť s výchozí předponou adresy.
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- Vytvořte flexibilní Server pomocí již existující virtuální sítě, podsítě a s použitím ID podsítě. V zadané podsíti by neměl být nasazen žádný jiný prostředek a tato podsíť bude delegována na **Microsoft. DBforPostgreSQL/flexibleServers**(Pokud již není delegovaná).
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Virtuální síť a podsíť by měly být ve stejné oblasti a předplatném jako flexibilní Server.

- Vytvoření flexibilního serveru pomocí nové virtuální sítě, podsítě s předponou jiné než výchozí adresy
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Úplný seznam konfigurovatelných parametrů rozhraní příkazového řádku najdete v [referenční dokumentaci](/cli/azure/postgres/flexible-server) k rozhraní příkazového řádku Azure CLI.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [sítích v Azure Database for PostgreSQL flexibilním serveru](./concepts-networking.md).
- [Vytvoření a správa Azure Database for PostgreSQLově flexibilní serverové virtuální sítě pomocí Azure Portal](./how-to-manage-virtual-network-portal.md).
- Další informace o [Azure Database for PostgreSQL flexibilní serverovou virtuální síť](./concepts-networking.md#private-access-vnet-integration).