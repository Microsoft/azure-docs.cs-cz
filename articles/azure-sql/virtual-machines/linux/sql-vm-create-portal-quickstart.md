---
title: 'Rychlý Start: Vytvoření virtuálního počítače se systémem Linux SQL Server v Azure'
description: Díky tomuto kurzu se dozvíte, jak vytvořit virtuální počítač s Linuxem a SQL Serverem 2017 na webu Azure Portal.
services: virtual-machines-linux
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e1a9d2722987464b1bb3c8b1489a2d1258a41d15
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273076"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Zřízení virtuálního počítače se systémem Linux běžícího SQL Server v Azure Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

V tomto kurzu rychlý Start použijete Azure Portal k vytvoření virtuálního počítače se systémem Linux s nainstalovanou SQL Server 2017. Naučíte se toto: 


* [Vytvoření virtuálního počítače se systémem Linux běžícího SQL Server z Galerie](#create)
* [Připojení k novému virtuálnímu počítači přes SSH](#connect)
* [Změna hesla SA](#password)
* [Konfigurace pro vzdálená připojení](#remote)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a>Vytvoření virtuálního počítače s Linuxem a nainstalovaným SQL Serverem

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V levém podokně vyberte **Vytvořit prostředek**.

1. V podokně **vytvořit prostředek** vyberte **COMPUTE**.

1. Vyberte **Zobrazit vše** vedle **doporučeného** nadpisu.

   ![Zobrazení všech imagí virtuálních počítačů](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. Do vyhledávacího pole zadejte **SQL Server 2019**a výběrem **ENTER** spusťte hledání.

1. Omezte výsledky hledání tak, že vyberete **operační systém**  >  **RedHat**.

    ![Vyhledávací filtr pro image virtuálních počítačů s SQL Server 2019](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. Z výsledků hledání vyberte bitovou kopii SQL Server 2019 Linux. V tomto kurzu se **na RHEL74 používá SQL Server 2019**.

   > [!TIP]
   > Vývojářská edice vám umožní testovat nebo vyvíjet s funkcemi edice Enterprise, ale bez SQL Server licenčních nákladů. Platíte jenom náklady na provozování virtuálního počítače s Linuxem.

1. Vyberte **Vytvořit**. 


### <a name="set-up-your-linux-vm"></a>Nastavení virtuálního počítače se systémem Linux

1. Na kartě **základy** vyberte své **předplatné** a **skupinu prostředků**. 

    ![Okno Základy](./media/sql-vm-create-portal-quickstart/basics.png)

1. Do **název virtuálního počítače**zadejte název nového virtuálního počítače se systémem Linux.
1. Pak zadejte nebo vyberte následující hodnoty:
   * **Oblast**: Vyberte oblast Azure, která je pro vás nejvhodnější.
   * **Možnosti dostupnosti**: vyberte možnost dostupnost a redundance, která je pro vaše aplikace a data nejvhodnější.
   * **Změnit velikost**: tuto možnost vyberte, pokud chcete vybrat velikost počítače a po dokončení klikněte na **Vybrat**. Další informace o velikostech počítačů virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů](../../../virtual-machines/sizes.md).

     ![Výběr velikosti virtuálního počítače](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > Pro vývoj a funkční testování použijte velikost virtuálního počítače **DS2** nebo vyšší. Pro testování výkonnosti použijte **DS13** nebo vyšší.

   * **Typ ověřování**: vyberte **veřejný klíč SSH**.

     > [!Note]
     > U ověřování máte na výběr mezi používáním veřejného klíče SSH nebo hesla. SSH je bezpečnější. Pokyny k vygenerování klíče SSH najdete v tématu [Vytvoření klíčů SSH v Linuxu a na Macu pro virtuální počítače s Linuxem v Azure](../../../virtual-machines/linux/mac-create-ssh-keys.md).

   * **Uživatelské jméno**: Zadejte jméno správce pro virtuální počítač.
   * **Veřejný klíč SSH**: Zadejte svůj veřejný klíč RSA.
   * **Veřejné příchozí porty**: zvolte **Povolit vybrané porty** a vyberte port **SSH (22)** v seznamu **Vybrat veřejné příchozí porty** . V tomto rychlém startu je tento krok nezbytný pro připojení a dokončení konfigurace SQL Server. Pokud se chcete vzdáleně připojit k SQL Server, budete muset ručně povolený provoz na výchozí port (1433), který používá Microsoft SQL Server pro připojení přes Internet po vytvoření virtuálního počítače.

     ![Příchozí porty](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. Proveďte všechny požadované změny nastavení na následujících dalších kartách nebo ponechte výchozí nastavení.
    * **Disky**
    * **Sítě**
    * **správy**
    * **Konfigurace hosta**
    * **Značky**

1. Vyberte **Zkontrolovat a vytvořit**.
1. V podokně **Revize + vytvořit** vyberte **vytvořit**.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a>Připojení k virtuálnímu počítači s Linuxem

Pokud už používáte prostředí BASH, připojte se k virtuálnímu počítači Azure pomocí příkazu **ssh**. V následujícím příkazu nahraďte uživatelské jméno a IP adresu virtuálního počítače pro připojení k vašemu virtuálnímu počítači s Linuxem.

```bash
ssh azureadmin@40.55.55.555
```

IP adresu svého virtuálního počítače najdete na webu Azure Portal.

![IP adresa na webu Azure Portal](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Pokud používáte Windows a nemáte prostředí BASH, nainstalujte klienta SSH, jako je například.

1. [Stáhněte a nainstalujte PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Spusťte PuTTY.

1. Na obrazovce konfigurace PuTTY zadejte veřejnou IP adresu vašeho virtuálního počítače.

1. Vyberte **otevřít** a na výzvy zadejte své uživatelské jméno a heslo.

Další informace o připojení k virtuálním počítačům s Linuxem najdete v tématu [Vytvoření virtuálního počítače s Linuxem v Azure pomocí portálu](../../../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Pokud se zobrazí výstraha zabezpečení výstupů na klíč hostitele serveru, který není uložen do mezipaměti v registru, vyberte z následujících možností. Pokud tomuto hostiteli důvěřujete, vyberte **Ano** a přidejte klíč do mezipaměti pro výstup a pokračujte v připojování. Chcete-li provést připojení pouze jednou, aniž byste museli přidat klíč do mezipaměti, vyberte možnost **ne**. Pokud tomuto hostiteli nedůvěřujete, vyberte **Zrušit** , aby se připojení zrušilo.

## <a name="change-the-sa-password"></a><a id="password"></a>Změna hesla SA

Nový virtuální počítač nainstaluje SQL Server s náhodným heslem SA. Resetujte toto heslo předtím, než se připojíte k SQL Server s přihlášením SA.

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

Ve výchozím nastavení se nainstaluje několik [balíčků](sql-server-on-linux-vm-what-is-iaas-overview.md#packages) SQL Serveru, včetně balíčku nástrojů příkazového řádku pro SQL Server. Balíček nástrojů obsahuje nástroje **sqlcmd** a **bcp**. Pro usnadnění práce můžete volitelně přidat cestu k nástrojům `/opt/mssql-tools/bin/` do proměnné prostředí **PATH**.

1. Spuštěním následujících příkazů upravte **PATH** pro relace přihlášení i interaktivní relace a relace bez přihlášení:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a>Konfigurace pro vzdálená připojení

Pokud se potřebujete vzdáleně připojit k SQL Serveru na virtuálním počítači Azure, musíte ve skupině zabezpečení sítě nakonfigurovat pravidlo pro příchozí provoz. Toto pravidlo povoluje provoz na portu, na kterém SQL Server naslouchá (výchozí je 1433). Následující kroky ukazují, jak tento krok provést pomocí webu Azure Portal.

> [!TIP]
> Pokud jste během zřizování v nastavení vybrali příchozí port **MS SQL (1433)**, tyto změny už se pro vás provedly. Můžete přejít k další části věnované konfiguraci brány firewall.

1. Na portálu vyberte **Virtuální počítače** a pak vyberte váš virtuální počítač s SQL Serverem.
1. V levém navigačním podokně v části **Nastavení**vyberte **sítě**.
1. V okně sítě vyberte v části **pravidla portů pro příchozí spojení** **Přidat port pro příchozí** spojení.

   ![Pravidla portů pro příchozí provoz](./media/sql-vm-create-portal-quickstart/networking.png)

1. V seznamu **Služba** vyberte **MS SQL**.

    ![Pravidlo skupiny zabezpečení MS SQL](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

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

Pokud jste virtuální počítač Azure nakonfigurovali pro připojení vzdáleného SQL Server, měli byste být schopni se připojit vzdáleně. Příklad vzdáleného připojení k SQL Serveru v Linuxu z Windows najdete v tématu [Použití SSMS ve Windows k připojení k SQL Serveru v Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Informace o připojení pomocí Visual Studio Code najdete v tématu [Použití nástroje Visual Studio Code k vytváření a spouštění skriptů Transact-SQL pro SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode).

Obecnější informace o SQL Server on Linux najdete v tématu [přehled SQL Server 2017 na Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Další informace o používání virtuálních počítačů s Linuxem a SQL Serverem 2017 najdete v tématu [Přehled virtuálních počítačů s SQL Serverem 2017 v Azure](sql-server-on-linux-vm-what-is-iaas-overview.md).
