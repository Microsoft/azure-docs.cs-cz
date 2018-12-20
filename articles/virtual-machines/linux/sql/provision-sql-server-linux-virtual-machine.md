---
title: Vytvoření virtuálního počítače s Linuxem a SQL Serverem 2017 v Azure | Dokumentace Microsoftu
description: Díky tomuto kurzu se dozvíte, jak vytvořit virtuální počítač s Linuxem a SQL Serverem 2017 na webu Azure Portal.
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 12/5/2018
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: cc2f094417d8710a1fdabaf850a8ced185de1ad7
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632751"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Zřízení virtuálního počítače s Linuxem a SQL Serverem na webu Azure Portal

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

V tomto kurzu rychlý start použijete k vytvoření virtuálního počítače s Linuxem s nainstalovaným SQL serverem 2017 na webu Azure portal.

V tomto kurzu se naučíte:

* [Vytvoření virtuálního počítače s Linuxem a SQL Serverem z galerie](#create)
* [Připojení k novému virtuálnímu počítači přes SSH](#connect)
* [Změna hesla SA](#password)
* [Konfigurace pro vzdálená připojení](#remote)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a id="create"></a>Vytvoření virtuálního počítače s Linuxem a nainstalovaným SQL Serverem

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. V levém podokně vyberte **vytvořit prostředek**.

1. V **vytvořit prostředek** vyberte **Compute**.

1. Vyberte **zobrazit všechny** vedle **doporučené** záhlaví.

   ![Zobrazení všech imagí virtuálních počítačů](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Do vyhledávacího pole zadejte **SQL serveru 2017**a vyberte **Enter** má začít prohledávání.

1. Omezit rozsah výsledků vyhledávání tak, že vyberete **operační systém** > **Redhat**. Potom v části **vydavatele**, zvolte **Microsoft**.

    ![Vyhledávací filtr pro image virtuálních počítačů s SQL Serverem 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Z výsledků hledání vyberte image Linuxu s SQL Serverem 2017. Tento kurz používá **bezplatná licence SQL serveru: SQL Server 2017 Developer v Red Hat Enterprise Linux 7.4**.

   > [!TIP]
   > Edice Developer umožňuje testovat nebo vyvíjet s využitím funkcí edice Enterprise, ale žádné náklady na licencování SQL serveru. Platíte jenom náklady na provozování virtuálního počítače s Linuxem.

1. V části **vybrat model nasazení**, vyberte model nasazení, která vyhovuje potřebám vaší úlohy.

    > [!Note]
    > Pro nové úlohy použijte **Resource Manageru**. Pro připojení k existující virtuální síť, vyberte metodu nasazení virtuální sítě pro vaši úlohu. Další informace o modelech nasazení najdete v tématu [Azure Resource Manageru a modely nasazení classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

1. Vyberte **Vytvořit**.

### <a name="set-up-your-linux-vm"></a>Nastavení virtuálního počítače s Linuxem

1. V **Základy** kartu, vyberte možnost vaše **předplatné** a **skupiny prostředků**. 

    ![Okno Základy](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. V **název virtuálního počítače**, zadejte název pro nový virtuální počítač s Linuxem.
1. Potom zadejte nebo vyberte následující hodnoty:
    * **Oblast**: Vyberte oblast Azure, který je pro vás nejvhodnější.
    * **Možnosti dostupnosti**: Vyberte možnost dostupnost a redundance, která je nejvhodnější pro vaše aplikace a data.
    * **Změnit velikost**: Tuto možnost vyberte velikost počítače a až budete hotovi, zvolte **vyberte**. Další informace o velikostech virtuálních počítačů Azure najdete v tématu [Velikosti virtuálních počítačů s Linuxem](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

    ![Výběr velikosti virtuálního počítače](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Pro vývoj a funkční testování, použít velikost virtuálního počítače u **DS2** nebo vyšší. Pro testování výkonnosti použijte **DS13** nebo vyšší.

    * **Typ ověřování**: Vyberte **veřejný klíč SSH**.

    > [!Note]
    > U ověřování máte na výběr mezi používáním veřejného klíče SSH nebo hesla. SSH je bezpečnější. Pokyny k vygenerování klíče SSH najdete v tématu [Vytvoření klíčů SSH v Linuxu a na Macu pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

    * **Uživatelské jméno**: Zadejte jméno správce pro virtuální počítač.
    * **Veřejný klíč SSH**: Zadejte veřejný klíč RSA.
    * **Veřejné příchozí porty**: Zvolte **povolit vybrané porty** a vybrat **SSH (22)** port v **vyberte veřejné příchozí porty** seznamu. V tomto rychlém startu tento krok je nezbytný k připojení a dokončete konfiguraci systému SQL Server. Pokud se chcete k SQL Serveru připojit vzdáleně, vyberte také **MS SQL (1433)**, aby se otevřel port 1433 pro připojení přes internet.

   ![Příchozí porty](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Proveďte změny chcete nastavení na následujících kartách další, nebo ponechat výchozí nastavení.
    * **Disky**
    * **Sítě**
    * **Správa**
    * **Konfigurace typu Host**
    * **Značky**

1. Vyberte **Zkontrolovat a vytvořit**.
1. V **revize + vytvořit** vyberte **vytvořit**.

## <a id="connect"></a>Připojení k virtuálnímu počítači s Linuxem

Pokud už používáte prostředí BASH, připojte se k virtuálnímu počítači Azure pomocí příkazu **ssh**. V následujícím příkazu nahraďte uživatelské jméno a IP adresu virtuálního počítače pro připojení k vašemu virtuálnímu počítači s Linuxem.

```bash
ssh azureadmin@40.55.55.555
```

IP adresu svého virtuálního počítače najdete na webu Azure Portal.

![IP adresa na webu Azure Portal](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Pokud máte spuštěnou na Windows a nemáte prostředí BASH, nainstalujte klienta SSH, jako je například PuTTY.

1. [Stáhněte a nainstalujte PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Spusťte PuTTY.

1. Na obrazovce konfigurace PuTTY zadejte veřejnou IP adresu vašeho virtuálního počítače.

1. Vyberte **otevřít** a zadejte svoje uživatelské jméno a heslo na vyzvání.

Další informace o připojení k virtuálním počítačům s Linuxem najdete v tématu [Vytvoření virtuálního počítače s Linuxem v Azure pomocí portálu](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

> [!Note]
> Pokud se zobrazí výstraha zabezpečení PuTTY o klíč hostitele serveru nejsou uložené v mezipaměti v registru, zvolte jednu z následujících možností. Pokud důvěřujete tomuto hostiteli, vyberte **Ano** přidat klíče PuTTy vaší mezipaměti a pokračovat v připojování. Pokud chcete provozovat připojení pouze jednou, bez přidání klíče do mezipaměti, vyberte **ne**. Pokud tento hostitel nedůvěřuje, vyberte **zrušit** proto ukončil připojení.

## <a id="password"></a>Změna hesla SA

Nový virtuální počítač nainstaluje SQL Server s náhodným heslem SA. Toto heslo resetujte, než se připojit k SQL serveru přes přihlášení SA.

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
1. V levém navigačním podokně v části **nastavení**vyberte **sítě**.
1. V okně sítě vyberte **přidat příchozí port** pod **příchozích pravidel portu**.

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

## <a name="next-steps"></a>Další postup

Když teď máte v Azure virtuální počítač s SQL Serverem 2017, můžete se k němu místně připojit pomocí nástroje **sqlcmd** a spouštět dotazy Transact-SQL.

Pokud jste nakonfigurovali virtuální počítač Azure pro vzdálené připojení k serveru SQL, je třeba připojit vzdáleně. Příklad vzdáleného připojení k SQL Serveru v Linuxu z Windows najdete v tématu [Použití SSMS ve Windows k připojení k SQL Serveru v Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Informace o připojení pomocí Visual Studio Code najdete v tématu [Použití nástroje Visual Studio Code k vytváření a spouštění skriptů Transact-SQL pro SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode).

Další obecné informace o SQL serveru v Linuxu najdete v tématu [přehled SQL serveru 2017 v Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Další informace o používání virtuálních počítačů s Linuxem a SQL Serverem 2017 najdete v tématu [Přehled virtuálních počítačů s SQL Serverem 2017 v Azure](sql-server-linux-virtual-machines-overview.md).
