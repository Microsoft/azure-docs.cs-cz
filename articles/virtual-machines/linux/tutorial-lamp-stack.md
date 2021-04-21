---
title: Kurz – nasazení LAMP a WordPress na virtuálním počítači
description: V tomto kurzu se naučíte, jak nainstalovat sadu LAMP a WordPress na virtuálním počítači se systémem Linux v Azure.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5365bad5fdea2a8213defc103f0cdd966ebe50a5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816337"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Kurz: Instalace zásobníku LAMP na virtuální počítač Azure Linux

Tento článek vás provede nasazením webového serveru Apache, MySQL a PHP (stack LAMP) na virtuální počítač s Ubuntu v Azure. Pokud chcete zobrazit server LAMP v akci, můžete volitelně nainstalovat a nakonfigurovat web WordPress. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu 
> * Otevření portu 80 pro webový provoz
> * Instalace Apache, MySQL a PHP
> * Ověření instalace a konfigurace
> * Instalace WordPressu 

Toto nastavení je určené pro rychlé testy nebo testování konceptu. Další informace o stacku LAMP, včetně doporučení pro produkční prostředí, najdete v [dokumentaci k Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

V tomto kurzu se používá CLI v rámci [Azure Cloud Shell](../../cloud-shell/overview.md), který se průběžně aktualizuje na nejnovější verzi. Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). 

Následující příklad vytvoří virtuální počítač *myVM*, a pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, vytvoří je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Tento příkaz také jako uživatelské jméno správce nastaví *azureuser*. Toto uživatelské jméno použijete později k připojení k virtuálnímu počítači. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu. Poznamenejte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači v dalších krocích.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz 

Ve výchozím nastavení jsou k virtuálním počítačům s Linuxem, které jsou nasazené v Azure, povolená pouze připojení SSH. Protože tento virtuální počítač bude webovým serverem, budete muset otevřít port 80 z internetu. Požadovaný port otevřete pomocí příkazu [az vm open-port](/cli/azure/vm).  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

Další informace o otevření portů na VIRTUÁLNÍm počítači najdete v tématu [otevření portů](nsg-quickstart.md).

## <a name="ssh-into-your-vm"></a>Připojení SSH k virtuálnímu počítači

Pokud ještě neznáte veřejnou IP adresu svého virtuálního počítače, spusťte příkaz [az network public-ip list](/cli/azure/network/public-ip). Tuto IP adresu budete potřebovat v několika dalších krocích.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Použijte správnou veřejnou IP adresu vašeho virtuálního počítače. V tomto příkladu je IP adresa *40.68.254.142*. *azureuser* je uživatelské jméno správce, které jste nastavili při vytváření virtuálního počítače.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Instalace Apache, MySQL a PHP

Spuštěním následujícího příkazu aktualizujte zdroje balíčků Ubuntu a nainstalujte Apache, MySQL a PHP. Všimněte si stříšky (^) na konci příkazu, která je součástí názvu balíčku `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Zobrazí se výzva k instalaci balíčků a dalších závislostí. Tímto postupem se nainstalují minimální požadovaná rozšíření PHP potřebná k používání PHP a MySQL.  

## <a name="verify-apache"></a>Ověření Apache

Zkontrolujte verzi Apache pomocí následujícího příkazu:
```bash
apache2 -v
```

Když je teď server Apache nainstalovaný a port 80 k virtuálnímu počítači otevřený, webový server je přístupný z internetu. Pokud chcete zobrazit výchozí stránku Apache2 v Ubuntu, otevřete webový prohlížeč a zadejte veřejnou IP adresu virtuálního počítače. Použijte veřejnou IP adresu, kterou jste použili k připojení přes SSH k virtuálnímu počítači:

![Výchozí stránka Apache][3]


## <a name="verify-and-secure-mysql"></a>Ověření a zabezpečení MySQL

Zkontrolujte verzi MySQL pomocí následujícího příkazu (všimněte si parametru velké `V`):

```bash
mysql -V
```

Chcete-li zvýšit zabezpečení instalace MySQL, včetně nastavení kořenového hesla, spusťte `mysql_secure_installation` skript. 

```bash
sudo mysql_secure_installation
```

Volitelně můžete nastavit modul plug-in pro ověřování hesel (doporučeno). Pak nastavte heslo pro kořenového uživatele MySQL a nakonfigurujte zbývající nastavení zabezpečení pro vaše prostředí. Doporučujeme, abyste na všechny otázky odpověděli na Y (Ano).

Pokud chcete vyzkoušet funkce MySQL (vytvoření databáze MySQL, přidání uživatelů nebo změna nastavení konfigurace), přihlaste se k MySQL. Tento krok není nezbytný k dokončení kurzu.

```bash
sudo mysql -u root -p
```

Jakmile budete hotovi, ukončete příkazový řádek mysql zadáním `\q`.

## <a name="verify-php"></a>Ověřit PHP

Zkontrolujte verzi PHP pomocí následujícího příkazu:

```bash
php -v
```

Pokud chcete podrobnější test, vytvořte jednoduchou informační stránku PHP a zobrazte ji v prohlížeči. Následující příkaz vytvoří informační stránku PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Teď můžete zkontrolovat informační stránku PHP, kterou jste vytvořili. Otevřete prohlížeč a přejděte na adresu `http://yourPublicIPAddress/info.php`. Použijte veřejnou IP adresu svého virtuálního počítače. Informační stránka by měla vypadat podobně jako na tomto obrázku.

![Informační stránka PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili server LAMP v Azure. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu
> * Otevření portu 80 pro webový provoz
> * Instalace Apache, MySQL a PHP
> * Ověření instalace a konfigurace
> * Instalace WordPressu na server LAMP

Přejděte k dalšímu kurzu, kde se dozvíte, jak zabezpečit webové servery pomocí certifikátů TLS/SSL.

> [!div class="nextstepaction"]
> [Zabezpečený webový server s protokolem TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
