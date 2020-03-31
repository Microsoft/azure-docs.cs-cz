---
title: Úvodní příručka – vytvoření privátního koncového bodu Azure pomocí azure cli
description: Další informace o privátním koncovém bodu Azure v tomto rychlém startu
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: dbcb833e6f8b90cebd3d013e58168558bcd96827
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75459974"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Úvodní příručka: Vytvoření privátního koncového bodu pomocí azure cli
Privátní koncový bod je základní stavební blok pro privátní propojení v Azure. Umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikovat s prostředky private link. V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač ve virtuální síti, SQL Database Server s privátním koncovým bodem pomocí Azure CLI. Potom můžete přistupovat k virtuálnímu počítači a bezpečně přistupovat k prostředku privátního propojení (privátní server Azure SQL Database v tomto příkladu). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a použít Azure CLI místně místo, tento rychlý start vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte program `az --version`. Informace o instalaci nebo upgradu [najdete v tématu Instalace příkazového příkazového](/cli/azure/install-azure-cli) příkazu Konzumu Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením libovolného prostředku je třeba vytvořit skupinu prostředků pro hostování virtuální sítě. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *westcentralus:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť s [vytvořením virtuální sítě AZ](/cli/azure/network/vnet). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* s jednou podsítí s názvem *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Zakázání zásad soukromého koncového bodu podsítě 
Azure nasazuje prostředky do podsítě v rámci virtuální sítě, takže je potřeba vytvořit nebo aktualizovat podsíť, abyste zakázali zásady privátní koncové sítě. Aktualizace konfigurace podsítě s názvem *mySubnet* s [aktualizací podsítě sítě AZ](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Vytvořte virtuální počítač. 
Vytvořte virtuální hosti s vytvořením virtuálního va. Po zobrazení výzvy zadejte heslo, které se má použít jako přihlašovací údaje pro virtuální hod. Tento příklad vytvoří virtuální hod s názvem *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Všimněte si veřejné IP adresy virtuálního soudu. Tuto adresu použijete k připojení k virtuálnímu virtuálnímu zařízení z internetu v dalším kroku.

## <a name="create-a-sql-database-server"></a>Vytvoření databázového serveru SQL 
Vytvořte databázový server SQL s příkazem az sql server create. Nezapomeňte, že název serveru SQL Server musí být v azure jedinečný, proto zástupnou hodnotu v závorkách nahraďte vlastní jedinečnou hodnotou: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Všimněte si, že ID serveru SQL Server je podobné, že ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` budete používat ID SERVERU SQL Server v dalším kroku. 

## <a name="create-the-private-endpoint"></a>Vytvoření soukromého koncového bodu 
Vytvořte privátní koncový bod pro databázový server SQL ve virtuální síti: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS 
Vytvořte privátní zónu DNS pro doménu serveru SQL Database a vytvořte přidružení propojení s virtuální sítí. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k *myvm* virtuálního zařízení z internetu takto:

1. Na vyhledávacím panelu portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **Připojit** se otevře **možnost Připojit k virtuálnímu počítači.**

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor rdp (Remote Desktop Protocol *)* a stáhne ho do počítače.

1. Otevřete soubor Downloaded.rdp*.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji a vraťte se na místní plochu.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Přístup k databázovému serveru SQL soukromě z virtuálního serveru

V této části se připojíte k databázovému serveru SQL z virtuálního serveru pomocí soukromého koncového bodu.

 1. Na vzdálené ploše *myVM*otevřete PowerShell.
 2. Zadejte nslookup myserver.database.windows.net  Obdržíte zprávu podobnou této: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Instalace aplikace SQL Server Management Studio 
 4. V pole Připojit k serveru zadejte nebo vyberte tyto informace: Typ serveru: Vyberte databázový stroj.
 Název serveru: Vyberte myserver.database.windows.net Uživatelské jméno: Zadejte uživatelské jméno poskytnuté při vytváření.
 Heslo: Zadejte heslo poskytnuté při vytváření.
 Zapamatovat heslo: Vyberte Ano.
 
 5. Vyberte **Connect** (Připojit).
 6. Procházet **databáze** z levé nabídky.
 7. (Volitelně) Vytvoření nebo dotazování informací z *databáze*
 8. Zavřete připojení ke vzdálené ploše *myVm*.

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Pokud již není potřeba, můžete pomocí odstranění skupiny AZ odebrat skupinu prostředků a všechny prostředky, které má: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Další kroky
- Další informace o [privátním propojení Azure](private-link-overview.md)
