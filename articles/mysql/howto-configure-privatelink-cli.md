---
title: Privátní odkaz – Azure CLI – Azure Database for MySQL
description: Postup konfigurace privátního odkazu pro Azure Database for MySQL z Azure CLI
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: da6a2b97d2656d56fa2aa0e7259fba433bd7b81e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998579"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>Vytvoření a správa privátního odkazu pro Azure Database for MySQL pomocí rozhraní příkazového řádku

Privátní koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako je Virtual Machines (virtuální počítače), komunikovat soukromě s prostředky privátního propojení. V tomto článku se dozvíte, jak pomocí Azure CLI vytvořit virtuální počítač v Virtual Network Azure a Azure Database for MySQL server s privátním koncovým bodem Azure.

> [!NOTE]
> Funkce privátního odkazu je dostupná jenom pro Azure Database for MySQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je databázový server v jedné z těchto cenových úrovní.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než budete moct vytvořit libovolný prostředek, musíte vytvořit skupinu prostředků, která bude hostovat Virtual Network. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *westeurope* :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte Virtual Network pomocí [AZ Network VNet Create](/cli/azure/network/vnet). Tento příklad vytvoří výchozí Virtual Network s názvem *myVirtualNetwork* s jednou podsítí s názvem *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Zakázat zásady privátního koncového bodu podsítě 
Azure nasadí prostředky do podsítě v rámci virtuální sítě, takže musíte vytvořit nebo aktualizovat podsíť, aby se zakázaly [zásady sítě](../private-link/disable-private-endpoint-network-policy.md)privátního koncového bodu. Aktualizujte konfiguraci podsítě s názvem *mySubnet* pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Vytvoření virtuálního počítače 
Vytvořte virtuální počítač pomocí AZ VM Create. Po zobrazení výzvy zadejte heslo, které se použije jako přihlašovací údaje pro virtuální počítač. Tento příklad vytvoří virtuální počítač s názvem *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
Poznamenejte si veřejnou IP adresu virtuálního počítače. Tato adresa se použije k připojení k virtuálnímu počítači z Internetu v dalším kroku.

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL 
Pomocí příkazu AZ MySQL server Create Vytvořte Azure Database for MySQL. Mějte na paměti, že název serveru MySQL musí být v rámci Azure jedinečný, proto nahraďte hodnotu zástupného symbolu v závorkách vlastní jedinečnou hodnotou: 

```azurecli-interactive
# Create a server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> V některých případech jsou Azure Database for MySQL a VNet-Subnet v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Ujistěte se, že oba odběry mají zaregistrovaný poskytovatel prostředků **Microsoft. DBforMySQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

## <a name="create-the-private-endpoint"></a>Vytvoření privátního koncového bodu 
Vytvořte v Virtual Network privátní koncový bod pro server MySQL: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMySQL/servers" --query "id" -o tsv) \    
    --group-id mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurovat zónu Privátní DNS 
Vytvořte zónu Privátní DNS pro doménu serveru MySQL a vytvořte odkaz na přidružení s Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Plně kvalifikovaný název domény v nastavení DNS zákazníka se nepřekladuje na nakonfigurovanou soukromou IP adresu. Bude nutné nastavit zónu DNS pro nakonfigurovaný plně kvalifikovaný název domény, jak je znázorněno [zde](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači *myVm* z Internetu následujícím způsobem:

1. Na portálu zadejte na panelu hledání *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (*. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete *stažený soubor. RDP* .

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby**  >  **použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **Ano** nebo **Pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Privátní přístup k serveru MySQL z virtuálního počítače

1. Ve vzdálené ploše *myVM* otevřete PowerShell.

2. Zadejte  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`. 

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Otestujte připojení k privátnímu propojení pro server MySQL pomocí libovolného dostupného klienta. V následujícím příkladu jsem k provedení operace použili aplikaci [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) .


4. V **nové připojení** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název připojení| Vyberte název připojení, který chcete zvolit.|
    | Název hostitele | Vybrat *mydemoserver.privatelink.MySQL.Database.Azure.com* |
    | Uživatelské jméno | Zadejte uživatelské jméno, *username@servername* které je k dispozici během vytváření serveru MySQL. |
    | Heslo | Zadejte heslo, které jste zadali během vytváření serveru MySQL. |
    ||

5. Vyberte Připojit.

6. Procházet databáze z levé nabídky

7. Volitelně Vytvoří nebo dotazuje informace z databáze MySQL.

8. Zavřete připojení ke vzdálené ploše pro myVm.

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz AZ Group Delete: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Další kroky
- Další informace o [tom, co je privátní koncový bod Azure](../private-link/private-endpoint-overview.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md