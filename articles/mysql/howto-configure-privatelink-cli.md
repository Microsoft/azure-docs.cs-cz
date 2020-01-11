---
title: Privátní odkaz pro instalační metodu rozhraní příkazového řádku pro Azure Database for MySQL (Preview)
description: Postup konfigurace privátního odkazu pro Azure Database for MySQL z Azure CLI
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: b3162486740ca8cadbe8610c0ddfe5053955bb59
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897894"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-cli"></a>Vytvoření a správa privátního odkazu pro Azure Database for MySQL (ve verzi Preview) pomocí rozhraní příkazového řádku

Privátní koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako je Virtual Machines (virtuální počítače), komunikovat soukromě s prostředky privátního propojení. V tomto článku se dozvíte, jak pomocí Azure CLI vytvořit virtuální počítač v Virtual Network Azure a Azure Database for MySQL server s privátním koncovým bodem Azure.

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for MySQL podporuje cenové úrovně optimalizované pro Pro obecné účely a paměť.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním `az --version`. Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) .

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
Azure nasadí prostředky do podsítě v rámci virtuální sítě, takže musíte vytvořit nebo aktualizovat podsíť, aby se zakázaly zásady sítě privátního koncového bodu. Aktualizujte konfiguraci podsítě s názvem *mySubnet* pomocí [AZ Network VNet Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Vytvořte virtuální počítač. 
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
# Create a logical server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Všimněte si, že ID serveru MySQL je podobné ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.``` v dalším kroku použijete ID serveru MySQL. 

## <a name="create-the-private-endpoint"></a>Vytvoření privátního koncového bodu 
Vytvořte v Virtual Network privátní koncový bod pro server MySQL: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MySQL Server ID>" \  
    --group-ids mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurovat zónu Privátní DNS 
Vytvořte zónu Privátní DNS pro doménu serveru MySQL a vytvořte odkaz na přidružení s Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači *myVm* z Internetu následujícím způsobem:

1. Na panelu hledání na portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) ( *. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor. RDP *.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby** > **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Privátní přístup k serveru MySQL z virtuálního počítače

1. Ve vzdálené ploše *myVM*otevřete PowerShell.

2. Zadejte  `nslookup mydemomysqlserver.mysql.privatelink.database.azure.com`. 

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.mysql.privatelink.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MySQL server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/wb-installing-windows.html) to do the operation.


4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Connection Name| Select the connection name of your choice.|
    | Hostname | Select *mydemoserver.mysql.privatelink.database.azure.com* |
    | Username | Enter username as *username@servername* which is provided during the MySQL server creation. |
    | Password | Enter a password provided during the MySQL server creation. |
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the MySQL database.

8. Close the remote desktop connection to myVm.

## Clean up resources 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Další kroky
- Další informace o [tom, co je privátní koncový bod Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)