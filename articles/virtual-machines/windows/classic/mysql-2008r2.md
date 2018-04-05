---
title: Vytvoření klasického virtuálního počítače Azure s MySQL | Microsoft Docs
description: Vytvořte virtuální počítač Azure se systémem Windows Server 2012 R2 a databáze MySQL pomocí modelu nasazení classic.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.openlocfilehash: e4135f96027cc25e1e0a149857ace1672570fc4b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>Instalace databáze MySQL na virtuální počítač vytvořený s modelem nasazení classic se systémem Windows Server 2016
[MySQL](https://www.mysql.com) je populární open source, databáze SQL. V tomto kurzu se dozvíte, jak nainstalovat a spustit **komunitní verzi MySQL 5.7.18** jako Server databáze MySQL na virtuálním počítači s **systému Windows Server 2016**. Prostředí může být mírně lišit pro jiné verze MySQL nebo Windows Server.

Pokyny k instalaci MySQL v systému Linux, najdete v části: [instalace databáze MySQL na Azure](../../linux/mysql-install.md).

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Vytvoření virtuálního počítače se systémem Windows Server 2016
Pokud ještě nemáte virtuálního počítače s Windows Server 2016, můžete to [kurzu](./tutorial.md) k vytvoření virtuálního počítače.

## <a name="attach-a-data-disk"></a>Připojení datového disku
Po vytvoření virtuálního počítače, můžete volitelně připojit datový disk. Přidání že datový disk se doporučuje pro úlohy v produkčním prostředí a aby se zabránilo nedostatku místa na jednotce operačního systému (C:), který obsahuje operační systém.

V tématu [jak připojit datový disk k virtuálnímu počítači Windows](../attach-managed-disk-portal.md) a postupujte podle pokynů pro prázdný disk se připojuje. Nastavení mezipaměti hostitele **žádné** nebo **jen pro čtení**.

## <a name="log-on-to-the-virtual-machine"></a>Přihlášení k virtuálnímu počítači
Dále budete [Přihlaste se k virtuálnímu počítači](./connect-logon.md) , můžete nainstalovat MySQL.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Nainstalujte a spusťte MySQL Community Server na virtuálním počítači
Postupujte podle těchto kroků k instalaci, konfiguraci a spuštění komunitní verzi MySQL serveru:

> [!NOTE]
> Při stahování položek pomocí aplikace Internet Explorer, můžete nastavit aplikaci Internet Explorer **konfigurace rozšířeného zabezpečení aplikace** k vypnout a zjednodušit proces stahování. Z nabídky Start, klikněte na správce nebo místního serveru pro správu nástroje/Server a pak klikněte na aplikaci Internet Explorer **konfigurace rozšířeného zabezpečení** a nastavte konfiguraci na vypnuto).
>
>

1. Po připojení k virtuálnímu počítači pomocí vzdálené plochy, klikněte na tlačítko **Internet Explorer** z obrazovky start.
2. Vyberte **nástroje** tlačítko v pravém horním rohu (ikona kolečka cogged) a potom klikněte na **Možnosti Internetu**. Klikněte na tlačítko **zabezpečení** , klikněte na **Důvěryhodné servery** ikonu a pak klikněte na tlačítko **lokality** tlačítko. Http://*.mysql.com přidáte do seznamu důvěryhodných serverů. Klikněte na tlačítko **Zavřít**a potom klikněte na **OK**.
3. Zadejte v na adresu aplikace Internet Explorer, https://dev.mysql.com/downloads/mysql/.
4. Pomocí serveru MySQL vyhledat a stáhnout nejnovější verzi Instalační služby MySQL pro systém Windows. Při výběru instalační program MySQL, stáhněte si verzi, která má úplný soubor sady (například mysql – instalační program komunity 5.7.18.0.msi a velikost souboru 352.8 MB) a uložte instalační služby.
5. Když instalační program dokončí stahování, klikněte na tlačítko **spustit** spustíte instalační program.
6. Na **licenční smlouvy** , přijměte licenční smlouvu a klikněte na tlačítko **Další**.
7. Na **výběr typu instalace** klikněte na typ instalace, který a pak klikněte na tlačítko **Další**. Následující postup předpokládá výběr **pouze Server** instalace typu.
8. Pokud **Zkontrolujte požadavky** zobrazí, klikněte na tlačítko **Execute** umožníte nainstalovat všechny chybějící součásti Instalační služby. Postupujte podle pokynů zobrazit, například C++ Redistributable runtime.
9. Na **instalace** klikněte na tlačítko **Execute**. Po dokončení instalace klikněte na tlačítko **Další**.

10. Na **konfiguraci produktu** klikněte na tlačítko **Další**.

11. Na **typu a sítě** stránky, zadejte požadované konfigurace typu a připojení možnosti, včetně TCP port, v případě potřeby. Vyberte **zobrazit rozšířené možnosti**a potom klikněte na **Další**.
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. Na **účtů a rolí** stránky, zadejte silné heslo kořenové MySQL. Podle potřeby přidat další uživatelské účty MySQL a potom klikněte na **Další**.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. Na **služba systému Windows** zadejte změny do výchozího nastavení pro práci serveru MySQL jako služby systému Windows, podle potřeby a pak klikněte na tlačítko **Další**.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. Možnosti na **modulů plug-in a rozšíření** stránky jsou volitelné. Klikněte na tlačítko **Další** pokračujte.
15. Na **pokročilé možnosti** podle potřeby zadejte změny možnosti protokolování a pak klikněte na tlačítko **Další**.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. Na **použít konfiguraci serveru** klikněte na tlačítko **Execute**. Po dokončení kroků konfigurace, klikněte na tlačítko **Dokončit**.
17. Na **konfiguraci produktu** klikněte na tlačítko **Další**.
18. Na **dokončení instalace** klikněte na tlačítko **protokolu kopírování do schránky** Pokud budete chtít později jej prozkoumat a potom klikněte na **Dokončit**.
19. Na obrazovce start zadejte **mysql**a potom klikněte na **MySQL 5.7 příkazového řádku klienta**.
20. Zadejte kořenové heslo, které jste zadali v kroku 12 a zobrazovat jsou s výzvou, kde můžete vydávat příkazy ke konfiguraci databáze MySQL. Podrobnosti příkazů a syntaxe najdete v tématu [MySQL v příručkách](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html).

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. Můžete také nakonfigurovat nastavení výchozí konfigurace serveru, například základní a data adresáře a jednotky. Další informace najdete v tématu [6.1.2 výchozí nastavení konfigurace serveru](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Pro službu MySQL být k dispozici pro klientské počítače na Internetu musí konfigurace koncového bodu pro TCP port a vytvořte pravidlo brány Firewall systému Windows. Výchozí hodnota port na kterém naslouchá službu MySQL Server pro klienty MySQL je 3306. Můžete zadat jiný port, tak dlouho, dokud je konzistentní s hodnotou dodána na port **typu a sítě** (krok 11 v předchozím postupu).

> [!NOTE]
> Pro použití v provozním prostředí vezměte v úvahu bezpečnostních důsledcích zpřístupnění službu MySQL Server do všech počítačů v síti Internet. Můžete definovat sadu zdrojové IP adresy, které mají povoleno používání koncový bod s seznamu řízení přístupu (ACL). Další informace najdete v tématu [jak nastavit koncové body k virtuálnímu počítači](setup-endpoints.md).
>
>

Konfigurace koncového bodu pro službu MySQL serveru:

1. Na portálu Azure klikněte na tlačítko **virtuálních počítačů (klasické)**, klikněte na název virtuálního počítače MySQL a pak klikněte na tlačítko **koncové body**.
2. Na panelu příkazů klikněte na tlačítko **přidat**.
3. Na **přidání koncového bodu** zadejte jedinečný název pro **název**.
4. Vyberte **TCP** jako protokol.
5. Zadejte číslo portu například **3306**, v obou **veřejný Port** a **privátní Port**a potom klikněte na **OK**.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Přidat pravidlo brány Windows Firewall umožňuje provoz MySQL
Chcete-li přidat pravidlo brány Windows Firewall, které umožní MySQL přenosy z Internetu, spusťte následující příkaz _příkazového řádku se zvýšenými oprávněními prostředí Windows PowerShell_ ve virtuálním počítači MySQL serveru.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>Testování vzdáleného připojení
K testování vzdálené připojení k virtuálnímu počítači Azure spuštěna služba MySQL Server, je nutné zadat název DNS obsahující VN cloudové služby.

1. Na portálu Azure klikněte na tlačítko **virtuálních počítačů (klasické)**, klikněte na název virtuálního počítače server MySQL a pak klikněte na tlačítko **přehled**.
2. Na řídicím panelu virtuální počítač, Upozorňujeme **název DNS** hodnotu. Zde naleznete příklad:

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. Z místního počítače se systémem MySQL nebo MySQL klienta spusťte následující příkaz k přihlášení jako uživatel MySQL.

     mysql -u <yourMysqlUsername> -p -h <yourDNSname>

   Například pomocí uživatelského jména MySQL _dbadmin3_ a _testmysql.cloudapp.net_ na názvy DNS pro virtuální počítač, můžete začít MySQL pomocí následujícího příkazu:

     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>Další postup
Další informace o spouštění MySQL naleznete v tématu [MySQL dokumentaci](http://dev.mysql.com/doc/).
