---
title: Rychlý Start – vytvoření privátního koncového bodu Azure pomocí Azure CLI
description: Seznamte se s privátním koncovým bodem Azure v tomto rychlém startu
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e7c098ba06086781306960f76978aac9e4fa06bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87502660"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Rychlý Start: Vytvoření privátního koncového bodu pomocí Azure CLI

Soukromý koncový bod je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače (VM), komunikovat soukromě s prostředky privátního propojení. V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač ve virtuální síti, server v SQL Database s privátním koncovým bodem pomocí Azure CLI. Pak můžete k virtuálnímu počítači přistupovat a získat zabezpečený přístup k prostředku privátního odkazu (v tomto příkladu je to privátní server v SQL Database).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Pokud chcete najít nainstalovanou verzi, spusťte příkaz `az --version` . Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) .

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než budete moct vytvořit libovolný prostředek, musíte vytvořit skupinu prostředků, která bude hostovat Virtual Network. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
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

## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí AZ VM Create. Po zobrazení výzvy zadejte heslo, které se použije jako přihlašovací údaje pro virtuální počítač. Tento příklad vytvoří virtuální počítač s názvem *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

Poznamenejte si veřejnou IP adresu virtuálního počítače. Tato adresa se použije k připojení k virtuálnímu počítači z Internetu v dalším kroku.

## <a name="create-a-server-in-sql-database"></a>Vytvoření serveru v SQL Database

Pomocí příkazu AZ SQL Server Create vytvořte server v SQL Database. Pamatujte, že název vašeho serveru musí být v rámci Azure jedinečný, proto nahraďte hodnotu zástupného symbolu v závorkách vlastní jedinečnou hodnotou:

```azurecli-interactive
# Create a server in the resource group
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

ID serveru se podobá tomu, že použijete  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` ID serveru v dalším kroku.

## <a name="create-the-private-endpoint"></a>Vytvoření privátního koncového bodu

Vytvořte privátní koncový bod pro logický SQL Server ve vašem Virtual Network:

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurovat zónu Privátní DNS

Vytvořte zónu Privátní DNS pro SQL Database doménu, vytvořte propojení přidružení s Virtual Network a vytvořte skupinu zón DNS pro přidružení privátního koncového bodu k Privátní DNS zóně. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \
   --name  "privatelink.database.windows.net"
az network private-dns link vnet create --resource-group myResourceGroup \
   --zone-name  "privatelink.database.windows.net"\
   --name MyDNSLink \
   --virtual-network myVirtualNetwork \
   --registration-enabled false
az network private-endpoint dns-zone-group create \
   --resource-group myResourceGroup \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači *myVm* z Internetu následujícím způsobem:

1. Na portálu zadejte na panelu hledání *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (*. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor. RDP *.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby**  >  **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **Ano** nebo **Pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  

## <a name="access-sql-database-privately-from-the-vm"></a>Přístup k SQL Database soukromě z virtuálního počítače

V této části se připojíte k SQL Database z virtuálního počítače pomocí privátního koncového bodu.

1. Ve vzdálené ploše *myVM*otevřete PowerShell.
2. Zadejte nslookup myserver.database.windows.net

   Zobrazí se zpráva podobná této:

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. Nainstalovat SQL Server Management Studio
4. V Connect to Server (připojit k serveru) zadejte nebo vyberte tyto informace:

   - Typ serveru: vyberte možnost databázový stroj.
   - Název serveru: vyberte myserver.database.windows.net
   - Uživatelské jméno: zadejte uživatelské jméno, které jste zadali při vytváření.
   - Heslo: zadejte heslo, které jste zadali při vytváření.
   - Pamatovat heslo: vyberte Ano.

5. Vyberte **Připojit**.
6. Procházet **databáze** z levé nabídky
7. Volitelně Vytvoření nebo dotazování informací z *MyDatabase*
8. Zavřete připojení ke vzdálené ploše pro *myVm*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz AZ Group Delete:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

Další informace o [privátním propojení Azure](private-link-overview.md)
