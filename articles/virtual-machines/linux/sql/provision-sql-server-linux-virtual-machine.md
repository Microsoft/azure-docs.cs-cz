---
title: Vytvoření virtuálního počítače s Linuxem a SQL Serverem 2017 v Azure | Dokumentace Microsoftu
description: Díky tomuto kurzu se dozvíte, jak vytvořit virtuální počítač s Linuxem a SQL Serverem 2017 na webu Azure Portal.
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 05/11/2018
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: b86dd47c112c38bc65c045158787d19b470899a0
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Zřízení virtuálního počítače s Linuxem a SQL Serverem na webu Azure Portal

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

V tomto úvodním kurzu pomocí webu Azure Portal vytvoříte virtuální počítač s Linuxem a nainstalovaným SQL Serverem 2017.

V tomto kurzu provedete následující:

* [Vytvoření virtuálního počítače s Linuxem a SQL Serverem z galerie](#create)
* [Připojení k novému virtuálnímu počítači přes SSH](#connect)
* [Změna hesla SA](#password)
* [Konfigurace pro vzdálená připojení](#remote)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a id="create"></a>Vytvoření virtuálního počítače s Linuxem a nainstalovaným SQL Serverem

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. V levém podokně klikněte na **Vytvořit prostředek**.

1. V podokně **Vytvořit prostředek** klikněte na **Compute**.

1. Klikněte na **Zobrazit vše** vedle nadpisu **Vybrané**.

   ![Zobrazení všech imagí virtuálních počítačů](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Do pole hledání zadejte **SQL Server 2017** a stisknutím klávesy **Enter** spusťte vyhledávání.

1. Klikněte na ikonu **Filtr**, omezte vyhledávání na image **Založené na Linuxu** a od **Microsoftu** a pak klikněte na **Hotovo**.

    ![Vyhledávací filtr pro image virtuálních počítačů s SQL Serverem 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Z výsledků hledání vyberte image Linuxu s SQL Serverem 2017. Tento kurz používá image **Bezplatná licence SQL Serveru: SQL Server 2017 Developer v Red Hat Enterprise Linuxu 7.4**.

   > [!TIP]
   > Edice Developer umožňuje testovat nebo vyvíjet s využitím funkcí edice Enterprise, ale bez licenčních nákladů na SQL Server. Platíte jenom náklady na provozování virtuálního počítače s Linuxem.

1. Klikněte na možnost **Vytvořit**.

1. V okně **Základy** vyplňte podrobnosti pro váš virtuální počítač s Linuxem. 

    ![Okno Základy](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > U ověřování máte na výběr mezi používáním veřejného klíče SSH nebo hesla. SSH je bezpečnější. Pokyny k vygenerování klíče SSH najdete v tématu [Vytvoření klíčů SSH v Linuxu a na Macu pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

1. Klikněte na **OK**.

1. V okně **Velikost** zvolte velikost počítače. Další informace o velikostech virtuálních počítačů Azure najdete v tématu [Velikosti virtuálních počítačů s Linuxem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

    ![Výběr velikosti virtuálního počítače](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Pro vývoj a funkční testování doporučujeme velikost virtuálního počítače **DS2** nebo vyšší. Pro testování výkonnosti použijte **DS13** nebo vyšší.

1. Klikněte na **Vybrat**.

1. V okně **Nastavení** v seznamu **Vybrat veřejné příchozí porty** vyberte port **SSH (22)**. V tomto rychlém startu je to potřeba pro připojení k SQL Serveru a dokončení jeho konfigurace. Pokud se chcete k SQL Serveru připojit vzdáleně, vyberte také **MS SQL (1433)**, aby se otevřel port 1433 pro připojení přes internet.

   ![Příchozí porty](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. U ostatních nastavení můžete provést změny nebo ponechat výchozí nastavení. Pak klikněte na **OK**.

1. Na stránce **Souhrn** kliknutím na **Koupit** vytvořte virtuální počítač.

## <a id="connect"></a>Připojení k virtuálnímu počítači s Linuxem

Pokud už používáte prostředí BASH, připojte se k virtuálnímu počítači Azure pomocí příkazu **ssh**. V následujícím příkazu nahraďte uživatelské jméno a IP adresu virtuálního počítače pro připojení k vašemu virtuálnímu počítači s Linuxem.

```bash
ssh azureadmin@40.55.55.555
```

IP adresu svého virtuálního počítače najdete na webu Azure Portal.

![IP adresa na webu Azure Portal](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Pokud používáte Windows a nemáte prostředí BASH, můžete si nainstalovat klienta SSH, například PuTTY.

1. [Stáhněte a nainstalujte PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Spusťte PuTTY.

1. Na obrazovce konfigurace PuTTY zadejte veřejnou IP adresu vašeho virtuálního počítače.

1. Klikněte na Open (Otevřít) a na vyzvání zadejte uživatelské jméno a heslo.

Další informace o připojení k virtuálním počítačům s Linuxem najdete v tématu [Vytvoření virtuálního počítače s Linuxem v Azure pomocí portálu](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

## <a id="password"></a>Změna hesla SA

Nový virtuální počítač nainstaluje SQL Server s náhodným heslem SA. Před připojením k SQL Serveru přes přihlášení SA musíte toto heslo resetovat.

1. Po připojení k vašemu virtuálnímu počítači s Linuxem otevřete nový příkazový terminál.

1. Změňte heslo SA pomocí následujících příkazů:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Po zobrazení výzvy zadejte nové heslo SA a potvrzení hesla.

1. Restartujte službu SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Přidání nástrojů do cesty (volitelné)

Ve výchozím nastavení se nainstaluje několik [balíčků](sql-server-linux-virtual-machines-overview.md#packages) SQL Serveru, včetně balíčku nástrojů příkazového řádku pro SQL Server. Balíček nástrojů obsahuje nástroje **sqlcmd** a **bcp**. Pro usnadnění práce můžete volitelně přidat cestu k nástrojům `/opt/mssql-tools/bin/` do proměnné prostředí **PATH**.

1. Spuštěním následujících příkazů upravte **PATH** pro relace přihlášení i interaktivní relace a relace bez přihlášení:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a>Konfigurace pro vzdálená připojení

Pokud se potřebujete vzdáleně připojit k SQL Serveru na virtuálním počítači Azure, musíte ve skupině zabezpečení sítě nakonfigurovat pravidlo pro příchozí provoz. Toto pravidlo povoluje provoz na portu, na kterém SQL Server naslouchá (výchozí je 1433). Následující kroky ukazují, jak tento krok provést pomocí webu Azure Portal.

> [!TIP]
> Pokud jste během zřizování v nastavení vybrali příchozí port **MS SQL (1433)**, tyto změny už se pro vás provedly. Můžete přejít k další části věnované konfiguraci brány firewall.

1. Na portálu vyberte **Virtuální počítače** a pak vyberte váš virtuální počítač s SQL Serverem.

1. V seznamu vlastností vyberte **Sítě**.

1. V okně **Sítě** klikněte na tlačítko **Přidat** v části **Pravidla portů pro příchozí provoz**.

   ![Pravidla portů pro příchozí provoz](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. V seznamu **Služba** vyberte **MS SQL**.

    ![Pravidlo skupiny zabezpečení MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Kliknutím na **OK** uložte pravidlo pro virtuální počítač.

### <a name="open-the-firewall-on-rhel"></a>Otevření brány firewall v RHEL

Tento kurz vám ukázal vytvoření virtuálního počítače s Red Hat Enterprise Linuxem (RHEL). Pokud se chcete vzdáleně připojit k virtuálním počítačům s RHEL, je potřeba otevřít port 1433 i v bráně firewall v Linuxu.

1. [Připojte se](#connect) ke svému virtuálnímu počítači s RHEL.

1. V prostředí BASH spusťte následující příkazy:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Další kroky

Když teď máte v Azure virtuální počítač s SQL Serverem 2017, můžete se k němu místně připojit pomocí nástroje **sqlcmd** a spouštět dotazy Transact-SQL.

Pokud jste virtuální počítač Azure nakonfigurovali pro vzdálená připojení k SQL Serveru, měli byste být schopni připojit se i vzdáleně. Příklad vzdáleného připojení k SQL Serveru v Linuxu z Windows najdete v tématu [Použití SSMS ve Windows k připojení k SQL Serveru v Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Informace o připojení pomocí Visual Studio Code najdete v tématu [Použití nástroje Visual Studio Code k vytváření a spouštění skriptů Transact-SQL pro SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode).

Další obecné informace o SQL Serveru v Linuxu najdete v tématu [Přehled SQL Serveru 2017 v Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Další informace o používání virtuálních počítačů s Linuxem a SQL Serverem 2017 najdete v tématu [Přehled virtuálních počítačů s SQL Serverem 2017 v Azure](sql-server-linux-virtual-machines-overview.md).
