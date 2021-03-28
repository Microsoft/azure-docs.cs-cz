---
title: Privátní odkaz – Azure CLI – Azure Database for PostgreSQL – jeden server
description: Přečtěte si, jak nakonfigurovat privátní odkaz pro Azure Database for PostgreSQL jeden server z Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3452bfee1e9228926bb687d1b9dc7fb26dfff85a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642173"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Vytvoření a správa privátního odkazu pro Azure Database for PostgreSQL s jedním serverem pomocí rozhraní příkazového řádku

Privátní koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako je Virtual Machines (virtuální počítače), komunikovat soukromě s prostředky privátního propojení. V tomto článku se dozvíte, jak pomocí Azure CLI vytvořit virtuální počítač v Virtual Network Azure a Azure Database for PostgreSQL jeden server s privátním koncovým bodem Azure.

> [!NOTE]
> Funkce privátního odkazu je dostupná jenom pro Azure Database for PostgreSQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je databázový server v jedné z těchto cenových úrovní.

## <a name="prerequisites"></a>Požadavky

Pokud chcete projít tento průvodce, budete potřebovat:

- [Server a databáze Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Pokud chcete najít nainstalovanou verzi, spusťte příkaz `az --version` . Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) .

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

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Vytvoření serveru Azure Database for PostgreSQL-Single 
Pomocí příkazu AZ Postgres Server Create Vytvořte Azure Database for PostgreSQL. Mějte na paměti, že název vašeho serveru PostgreSQL musí být v rámci Azure jedinečný, takže nahraďte hodnotu zástupného symbolu vlastními jedinečnými hodnotami, které jste použili výše: 

```azurecli-interactive
# Create a server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

## <a name="create-the-private-endpoint"></a>Vytvoření privátního koncového bodu 
Vytvořte v Virtual Network privátní koncový bod pro server PostgreSQL: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforPostgreSQL/servers" --query "id" -o tsv) \    
    --group-id postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurovat zónu Privátní DNS 
Vytvořte zónu Privátní DNS pro doménu serveru PostgreSQL a vytvořte odkaz na přidružení s Virtual Network. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Plně kvalifikovaný název domény v nastavení DNS zákazníka se nepřekládá na nakonfigurovanou privátní IP adresu. Bude nutné nastavit zónu DNS pro nakonfigurovaný plně kvalifikovaný název domény, jak je znázorněno [zde](../dns/dns-operations-recordsets-portal.md).

> [!NOTE]
> V některých případech jsou Azure Database for PostgreSQL a VNet-Subnet v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Ujistěte se, že oba odběry mají zaregistrovaný poskytovatel prostředků **Microsoft. DBforPostgreSQL** . Další informace najdete v tématu [poskytovatelé prostředků](../azure-resource-manager/management/resource-providers-and-types.md).

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

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Přístup k serveru PostgreSQL soukromě z virtuálního počítače

1. Ve vzdálené ploše *myVM* otevřete PowerShell.

2. Zadejte  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

   Zobrazí se zpráva podobná této:

   ```azurepowershell
   Server:  UnKnown
   Address:  168.63.129.16
   Non-authoritative answer:
   Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
   Address:  10.1.3.4
   ```

3. Otestujte připojení privátního propojení pro server PostgreSQL pomocí libovolného dostupného klienta. Následující příklad používá k provedení operace [Azure Data Studio](/sql/azure-data-studio/download) .

4. V **nové připojení** zadejte nebo vyberte tyto informace:

   | Nastavení | Hodnota |
   | ------- | ----- |
   | Typ serveru| Vyberte **PostgreSQL**.|
   | Název serveru| Vybrat *mydemopostgresserver.privatelink.Postgres.Database.Azure.com* |
   | Uživatelské jméno | Zadejte uživatelské jméno, username@servername které je k dispozici během vytváření PostgreSQL serveru. |
   |Heslo |Zadejte heslo, které jste zadali během vytváření PostgreSQL serveru. |
   |SSL|Vyberte možnost **požadováno**.|
   ||

5. Vyberte Připojit.

6. Procházet databáze z levé nabídky

7. Volitelně Vytvoření nebo dotazování informací ze serveru postgreSQL

8. Zavřete připojení ke vzdálené ploše pro myVm.

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz AZ Group Delete: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Další kroky
- Další informace o [tom, co je privátní koncový bod Azure](../private-link/private-endpoint-overview.md)
