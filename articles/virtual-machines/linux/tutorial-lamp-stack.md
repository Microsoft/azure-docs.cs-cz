---
title: Kurz – nasazení LAMPy na virtuálním počítači se systémem Linux v Azure
description: V tomto kurzu zjistíte, jak nainstalovat stack LAMP na virtuální počítač s Linuxem v Azure
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.collection: linux
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 3813931f47c110abcfb595065c1415ca9ed84c9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564709"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Kurz: Instalace webového serveru LAMP na virtuální počítač s Linuxem v Azure

Tento článek vás provede nasazením webového serveru Apache, MySQL a PHP (stack LAMP) na virtuální počítač s Ubuntu v Azure. Pokud chcete zobrazit server LAMP v akci, můžete volitelně nainstalovat a nakonfigurovat web WordPress. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu (písmeno L ve stacku LAMP)
> * Otevření portu 80 pro webový provoz
> * Instalace Apache, MySQL a PHP
> * Ověření instalace a konfigurace
> * Instalace WordPressu na server LAMP

Toto nastavení je určené pro rychlé testy nebo testování konceptu. Další informace o stacku LAMP, včetně doporučení pro produkční prostředí, najdete v [dokumentaci k Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

V tomto kurzu se používá CLI v rámci [Azure Cloud Shell](../../cloud-shell/overview.md), který se průběžně aktualizuje na nejnovější verzi. Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Instalace Apache, MySQL a PHP

Spuštěním následujícího příkazu aktualizujte zdroje balíčků Ubuntu a nainstalujte Apache, MySQL a PHP. Všimněte si stříšky (^) na konci příkazu, která je součástí názvu balíčku `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Zobrazí se výzva k instalaci balíčků a dalších závislostí. Tímto postupem se nainstalují minimální požadovaná rozšíření PHP potřebná k používání PHP a MySQL.  

## <a name="verify-installation-and-configuration"></a>Ověření instalace a konfigurace


### <a name="verify-apache"></a>Ověření Apache

Zkontrolujte verzi Apache pomocí následujícího příkazu:
```bash
apache2 -v
```

Když je teď server Apache nainstalovaný a port 80 k virtuálnímu počítači otevřený, webový server je přístupný z internetu. Pokud chcete zobrazit výchozí stránku Apache2 v Ubuntu, otevřete webový prohlížeč a zadejte veřejnou IP adresu virtuálního počítače. Použijte veřejnou IP adresu, kterou jste použili k připojení přes SSH k virtuálnímu počítači:

![Výchozí stránka Apache][3]


### <a name="verify-and-secure-mysql"></a>Ověření a zabezpečení MySQL

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

### <a name="verify-php"></a>Ověřit PHP

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
