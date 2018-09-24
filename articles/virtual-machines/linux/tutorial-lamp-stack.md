---
title: Kurz nasazení LAMP na virtuální počítač s Linuxem v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak nainstalovat stack LAMP na virtuální počítač s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: 5caed6fba607cb93a6168bded7531bc8bf63b9da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970681"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Kurz: Instalace webového serveru LAMP na virtuální počítač s Linuxem v Azure

Tento článek vás provede nasazením webového serveru Apache, MySQL a PHP (stack LAMP) na virtuální počítač s Ubuntu v Azure. Pokud dáváte přednost webovému serveru NGINX, projděte si kurz věnovaný [stacku LEMP](tutorial-lemp-stack.md). Pokud chcete zobrazit server LAMP v akci, můžete volitelně nainstalovat a nakonfigurovat web WordPress. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu (písmeno L ve stacku LAMP)
> * Otevření portu 80 pro webový provoz
> * Instalace Apache, MySQL a PHP
> * Ověření instalace a konfigurace
> * Instalace WordPressu na server LAMP

Toto nastavení je určené pro rychlé testy nebo testování konceptu. Další informace o stacku LAMP, včetně doporučení pro produkční prostředí, najdete v [dokumentaci k Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Instalace Apache, MySQL a PHP

Spuštěním následujícího příkazu aktualizujte zdroje balíčků Ubuntu a nainstalujte Apache, MySQL a PHP. Všimněte si stříšky (^) na konci příkazu, která je součástí názvu balíčku `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```


Zobrazí se výzva k instalaci balíčků a dalších závislostí. Po zobrazení výzvy nastavte kořenové heslo pro MySQL a pokračujte stisknutím klávesy [Enter]. Postupujte podle zbývajících výzev. Tímto postupem se nainstalují minimální požadovaná rozšíření PHP potřebná k používání PHP a MySQL. 

![Stránka kořenového hesla MySQL][1]

## <a name="verify-installation-and-configuration"></a>Ověření instalace a konfigurace


### <a name="apache"></a>Apache

Zkontrolujte verzi Apache pomocí následujícího příkazu:
```bash
apache2 -v
```

Když je teď server Apache nainstalovaný a port 80 k virtuálnímu počítači otevřený, webový server je přístupný z internetu. Pokud chcete zobrazit výchozí stránku Apache2 v Ubuntu, otevřete webový prohlížeč a zadejte veřejnou IP adresu virtuálního počítače. Použijte veřejnou IP adresu, kterou jste použili k připojení přes SSH k virtuálnímu počítači:

![Výchozí stránka Apache][3]


### <a name="mysql"></a>MySQL

Zkontrolujte verzi MySQL pomocí následujícího příkazu (všimněte si parametru velké `V`):

```bash
mysql -V
```

Pokud chcete pomoct se zabezpečením instalace MySQL, spusťte skript `mysql_secure_installation`. Pokud nastavujete pouze dočasný server, můžete tento krok přeskočit.

```bash
mysql_secure_installation
```

Zadejte kořenové heslo pro MySQL a nakonfigurujte nastavení zabezpečení pro vaše prostředí.

Pokud chcete vyzkoušet funkce MySQL (vytvoření databáze MySQL, přidání uživatelů nebo změna nastavení konfigurace), přihlaste se k MySQL. Tento krok není nezbytný k dokončení kurzu.

```bash
mysql -u root -p
```

Jakmile budete hotovi, ukončete příkazový řádek mysql zadáním `\q`.

### <a name="php"></a>PHP

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

V tomto kurzu jste nasadili server LAMP v Azure. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu
> * Otevření portu 80 pro webový provoz
> * Instalace Apache, MySQL a PHP
> * Ověření instalace a konfigurace
> * Instalace WordPressu na server LAMP

V dalším kurzu se dozvíte, jak zabezpečit webové servery pomocí certifikátů SSL.

> [!div class="nextstepaction"]
> [Zabezpečení webového serveru pomocí SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png