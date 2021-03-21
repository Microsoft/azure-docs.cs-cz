---
title: 'Kurz: připojení k serveru SQL Azure pomocí privátního koncového bodu Azure – Azure CLI'
description: V tomto kurzu se dozvíte, jak pomocí Azure CLI vytvořit server SQL Azure s privátním koncovým bodem.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 998b52a23894dcb7fa08b2c0fd42e4ef8e3678ae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554985"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Kurz: připojení k serveru SQL Azure pomocí privátního koncového bodu Azure – Azure CLI

Privátní koncový bod Azure je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě s prostředky privátního propojení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte virtuální síť a hostitele bastionu.
> * Vytvoří virtuální počítač.
> * Vytvoření serveru SQL Azure a privátního koncového bodu.
> * Otestujte připojení k privátnímu koncovému bodu SQL serveru.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Přihlaste se k Azure Portal a ověřte, že je vaše předplatné aktivní spuštěním `az login` .
* Podívejte se na verzi rozhraní příkazového řádku Azure CLI v terminálu nebo příkazovém okně spuštěním příkazu `az --version` . Nejnovější verzi najdete v [poznámkách k nejnovější verzi](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Pokud nemáte nejnovější verzi, aktualizujte instalaci pomocí [instalační příručky pro váš operační systém nebo platformu](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az_group_create):

* Název **CreateSQLEndpointTutorial-RG**. 
* V umístění **eastus** .

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Vytvoření virtuální sítě a hostitele bastionu

V této části vytvoříte virtuální síť, podsíť a hostitele bastionu. 

Hostitel bastionu se bude používat k zabezpečenému připojení k virtuálnímu počítači za účelem testování privátního koncového bodu.

Vytvoření virtuální sítě pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az_network_vnet_create)

* S názvem **myVNet**.
* Předpona adresy **10.0.0.0/16**.
* Podsíť s názvem **myBackendSubnet**.
* Předpona podsítě **10.0.0.0/24**.
* Ve skupině prostředků **CreateSQLEndpointTutorial-RG** .
* Umístění **eastus**

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Aktualizace podsítě pro zakázání zásad privátního koncového bodu pro privátní koncový bod pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

K vytvoření veřejné IP adresy pro hostitele bastionu použijte [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) :

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myBastionIP**.
* V **CreateSQLEndpointTutorial-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) vytvořte podsíť bastionu:

* S názvem **AzureBastionSubnet**.
* Předpona adresy **10.0.1.0/24**.
* Ve virtuální síti **myVNet**.
* Ve skupině prostředků **CreateSQLEndpointTutorial-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Pomocí [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) Vytvořte hostitele bastionu:

* S názvem **myBastionHost**.
* V **CreateSQLEndpointTutorial-RG**.
* Přidruženo k veřejné IP **myBastionIP**.
* Přidruženo k virtuální síti **myVNet**.
* V umístění **eastus** .

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Nasazení hostitele Azure bastionu může trvat několik minut.

## <a name="create-test-virtual-machine"></a>Vytvořit testovací virtuální počítač

V této části vytvoříte virtuální počítač, který se použije k otestování privátního koncového bodu.

Vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az_vm_create). Po zobrazení výzvy zadejte heslo, které se má použít jako přihlašovací údaje pro virtuální počítač:

* S názvem **myVM**.
* V **CreateSQLEndpointTutorial-RG**.
* V Network **myVNet**.
* V **myBackendSubnet** podsíti.
* **Win2019Datacenter** image serveru.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-an-azure-sql-server"></a>Vytvoření serveru SQL Azure

V této části vytvoříte SQL Server a databázi.

Pomocí [AZ SQL Server Create](/cli/azure/sql/server#az_sql_server_create) vytvořte SQL Server:

* Nahraďte **\<sql-server-name>** jedinečným názvem serveru.
* Nahraďte **\<your-password>** heslem.
* V **CreateSQLEndpointTutorial-RG**.
* V oblasti **eastus**

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Vytvoření databáze pomocí [AZ SQL DB Create](/cli/azure/sql/db#az_sql_db_create) :

* S názvem **myDataBase**.
* V **CreateSQLEndpointTutorial-RG**.
* Nahraďte **\<sql-server-name>** jedinečným názvem serveru.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Vytvořit privátní koncový bod

V této části vytvoříte privátní koncový bod.

Pomocí [AZ SQL Server list](/cli/azure/sql/server#az_sql_server_list) umístěte ID prostředku SQL serveru na proměnnou prostředí.

Pomocí [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) vytvořte koncový bod a připojení:

* S názvem **myPrivateEndpoint**.
* Ve skupině prostředků **CreateSQLEndpointTutorial-RG**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet** podsíti.
* Připojení s názvem **mojepripojeni**.

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS

V této části vytvoříte a nakonfigurujete privátní zónu DNS pomocí [AZ Network Private-DNS Zone Create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Pomocí [AZ Network Private-DNS Link VNet Create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) vytvoříte propojení virtuální sítě k zóně DNS.

Vytvoříte skupinu zón DNS pomocí [AZ Network Private-Endpoint DNS-Zone-Group Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zóna s názvem **privatelink.Database.Windows.NET**
* Ve virtuální síti **myVNet**.
* Ve skupině prostředků **CreateSQLEndpointTutorial-RG**.
* Odkaz DNS s názvem **myDNSLink**.
* Přidruženo k **myPrivateEndpoint**.
* Skupina zón s názvem **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Test připojení k privátnímu koncovému bodu

V této části použijete virtuální počítač, který jste vytvořili v předchozím kroku, abyste se připojili k SQL serveru přes soukromý koncový bod.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). 
 
2. V levém navigačním podokně vyberte **skupiny prostředků** .

3. Vyberte **CreateSQLEndpointTutorial-RG**.

4. Vyberte **myVM**.

5. Na stránce Přehled pro **myVM** vyberte **připojit** a pak **bastionu**.

6. Vyberte tlačítko modrého **použití bastionu** .

7. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

8. Po připojení otevřete Windows PowerShell na serveru.

9. Zadejte `nslookup <sqlserver-name>.database.windows.net`. Nahraďte **\<sqlserver-name>** názvem SQL serveru, který jste vytvořili v předchozích krocích.  Zobrazí se zpráva podobná tomu, co se zobrazuje níže:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Pro název SQL serveru se vrátí privátní IP adresa **10.0.0.5** .  Tato adresa je v podsíti virtuální sítě, kterou jste vytvořili dříve.


10. Nainstalujte [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) v **myVM**.

11. Otevřete **SQL Server Management Studio**.

12. V **Connect to Server (připojit k serveru**) zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Typ serveru | Vyberte **Databázový stroj**.|
    | Název serveru | Zadejte **\<sql-server-name> . Database.Windows.NET** |
    | Ověřování | Vyberte **Ověřování SQL Serveru**. |
    | Uživatelské jméno | Zadejte uživatelské jméno, které jste zadali při vytváření serveru. |
    | Heslo | Zadejte heslo, které jste zadali při vytváření serveru. |
    | Zapamatovat heslo | Vyberte **Ano**. |

13. Vyberte **Connect** (Připojit).

14. V nabídce vlevo vyberte databáze.

15. Volitelně Vytvoření nebo dotazování informací z **mysqldatabase**.

16. Zavřete připojení bastionu k **myVM**. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Až budete s použitím privátního koncového bodu, SQL serveru a virtuálního počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili:

* Virtuální síť a hostitel bastionu
* Virtuální počítač.
* Azure SQL Server s privátním koncovým bodem.

Virtuální počítač jste použili k zabezpečenému otestování připojení k SQL serveru v rámci privátního koncového bodu.

V dalším kroku se může také zajímat **Webová aplikace s privátním připojením ke scénáři architektury Azure SQL Database** , která připojuje webovou aplikaci mimo virtuální síť k privátnímu koncovému bodu databáze.
> [!div class="nextstepaction"]
> [Webová aplikace s privátním připojením ke službě Azure SQL Database](/azure/architecture/example-scenario/private-web-app/private-web-app)