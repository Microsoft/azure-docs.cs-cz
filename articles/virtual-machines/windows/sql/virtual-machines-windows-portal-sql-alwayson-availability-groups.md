---
title: Nastavení vysoké dostupnosti pro virtuální počítače Azure Resource Manager | Dokumenty společnosti Microsoft
description: Tento kurz ukazuje, jak vytvořit skupinu dostupnosti vždy na s virtuálními počítači Azure v režimu Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d7c88e500886453fbfb53655748ccf7025ab7d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374247"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Konfigurace skupin dostupnosti always on ve virtuálních počítačích Azure automaticky: Správce prostředků

Tento kurz ukazuje, jak vytvořit skupinu dostupnosti serveru SQL Server, která používá virtuální počítače Azure Resource Manager. Kurz používá azure blades ke konfiguraci šablony. Můžete zkontrolovat výchozí nastavení, zadejte požadovaná nastavení a aktualizovat okna na portálu při procházení tohoto kurzu.

Kompletní kurz vytvoří skupinu dostupnosti sql serveru na virtuálních počítačích Azure, které obsahují následující prvky:

* Virtuální síť, která má více podsítí, včetně front-endu a podsítě back-endu
* Dva řadiče domény s doménou služby Active Directory
* Dva virtuální počítače, se kterými běží SQL Server a jsou nasazeny do podsítě back-endu a připojeny k doméně služby Active Directory
* Cluster s podporou převzetí služeb při selhání se třemi uzlami s modelem kvora většina uzlů
* Skupina dostupnosti, která má dvě repliky synchronního potvrzení databáze dostupnosti

Následující obrázek představuje úplné řešení.

![Architektura testovací laboratoře pro skupiny dostupnosti v Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Všechny prostředky v tomto řešení patří do jedné skupiny prostředků.

Před zahájením tohoto kurzu potvrďte následující:

* Účet Azure už máte. Pokud ho nemáte, [zaregistrujte si zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
* Už víte, jak pomocí grafického uživatelského rozhraní zřídit virtuální počítač SQL Server z galerie virtuálních počítačů. Další informace najdete [v tématu Zřizování virtuálního počítače SQL Server v Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Již máte solidní znalosti o skupinách dostupnosti. Další informace naleznete [v tématu Vždy na skupiny dostupnosti (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Pokud máte zájem o použití skupin dostupnosti se SharePointem, přečtěte si taky [část Konfigurace skupin dostupnosti SQL Serveru 2012 Always On pro SharePoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

V tomto kurzu použijte portál Azure k:

* Zvolte šablonu Always On z portálu.
* Zkontrolujte nastavení šablony a aktualizujte několik nastavení konfigurace pro vaše prostředí.
* Sledujte Azure tak, jak vytváří celé prostředí.
* Připojte se k řadiči domény a potom k serveru se systémem SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Zřízení clusteru z galerie
Azure poskytuje image galerie pro celé řešení. Chcete-li najít šablonu:

1. Přihlaste se k portálu Azure pomocí svého účtu.
2. Na webu Azure Portal klikněte na **Vytvořit prostředek** a otevřete **podokno Nový.**
3. V podokně **Nový** vyhledejte **možnost AlwaysOn**.
   ![Šablona Najít Alwayson](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Ve výsledcích hledání vyhledejte **cluster SQL Server AlwaysOn**.
   ![Šablona AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. V **nabídce Vyberte model nasazení**zvolte Správce **prostředků**.

### <a name="basics"></a>Základy
Klikněte na **Základy** a nakonfigurujte následující nastavení:

* **Uživatelské jméno správce** je uživatelský účet, který má oprávnění správce domény a je členem pevné role serveru SQL Server sysadmin v obou instancích serveru SQL Server. Pro účely tohoto kurzu použijte **DomainAdmin**.
* **Heslo** je heslo pro účet správce domény. Použijte složité heslo. Potvrďte heslo.
* **Předplatné** je předplatné, které Azure účtuje ke spuštění všech nasazených prostředků pro skupinu dostupnosti. Pokud má váš účet více předplatných, můžete zadat jiné předplatné.
* **Skupina prostředků** je název skupiny, do které patří všechny prostředky Azure vytvořené touto šablonou. Pro účely tohoto kurzu použijte **SQL-HA-RG**. Další informace naleznete v tématu [Přehled Azure Resource Manager](../../../azure-resource-manager/management/overview.md#resource-groups).
* **Umístění** je oblast Azure, kde kurz vytváří prostředky. Zvolte oblast Azure.

Následující snímek obrazovky je dokončené **základy** čepel:

![Základy](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klikněte na tlačítko **OK**.

### <a name="domain-and-network-settings"></a>Nastavení domény a sítě
Tato šablona galerie Azure vytvoří doménu a řadiče domény. Vytvoří také síť a dvě podsítě. Šablona nemůže vytvořit servery v existující doméně nebo virtuální síti. Další krok konfiguruje nastavení domény a sítě.

V okně **Nastavení domény a sítě** zkontrolujte přednastavené hodnoty pro nastavení domény a sítě:

* **Název kořenové domény doménové struktury** je název domény služby Active Directory, která je hostitelem clusteru. Pro výuku použijte **contoso.com**.
* **Název virtuální sítě** je název sítě pro virtuální síť Azure. Pro výuku použijte **autohaVNET**.
* **Název podsítě řadiče domény** je název části virtuální sítě, která je hostitelem řadiče domény. Použijte **podsíť-1**. Tato podsíť používá předponu adresy **10.0.0.0/24**.
* **Název podsítě serveru SQL Server** je název části virtuální sítě, která je hostitelem serverů se systémem SQL Server a důkaz čehož připojili se důkaz. Použijte **podsíť-2**. Tato podsíť používá předponu adresy **10.0.1.0/26**.

Další informace o virtuálních sítích v Azure najdete v [tématu Přehled virtuálních sítí](../../../virtual-network/virtual-networks-overview.md).  

Nastavení **domény a sítě** by mělo vypadat jako následující snímek obrazovky:

![Nastavení domény a sítě](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

V případě potřeby můžete tyto hodnoty změnit. Pro účely tohoto kurzu použijte přednastavené hodnoty.

Zkontrolujte nastavení a klepněte na tlačítko **OK**.

### <a name="availability-group-settings"></a>Nastavení skupiny dostupnosti
V **nastavení skupiny Dostupnost**zkontrolujte přednastavené hodnoty pro skupinu dostupnosti a naslouchací proces.

* **Název skupiny dostupnosti** je název clusterovaného prostředku pro skupinu dostupnosti. V tomto kurzu použijte **Contoso-ag**.
* **Název posluchače skupiny dostupnosti** používá cluster a interní správce zatížení. Klienti, kteří se připojují k serveru SQL Server, se mohou pomocí tohoto názvu připojit k příslušné replice databáze. Pro účely tohoto kurzu použijte **naslouchací proces Contoso**.
* **Port posluchače skupiny dostupnosti** určuje port TCP naslouchací proces serveru SQL Server. Pro účely tohoto kurzu použijte výchozí port **1433**.

V případě potřeby můžete tyto hodnoty změnit. Pro účely tohoto kurzu použijte přednastavené hodnoty.  

![nastavení skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klikněte na tlačítko **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Velikost virtuálního počítače, nastavení úložiště
Na **velikosti virtuálního počítače, nastavení úložiště**, zvolte velikost virtuálního počítače SQL Server a zkontrolujte další nastavení.

* **Velikost virtuálního počítače SQL Serveru** je velikost pro oba virtuální počítače, ve kterých je sql server spuštěn. Zvolte vhodnou velikost virtuálního počítače pro vaši úlohu. Pokud vytváříte toto prostředí pro kurz, použijte **DS2**. Pro produkční úlohy zvolte velikost virtuálního počítače, který podporuje úlohy. Mnoho produkčních úloh vyžaduje **DS4** nebo větší. Šablona vytvoří dva virtuální počítače této velikosti a nainstaluje SQL Server na každý z nich. Další informace najdete [v tématu Velikosti pro virtuální počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure nainstaluje edici Enterprise sql serveru. Náklady závisí na edici a velikosti virtuálního počítače. Podrobné informace o aktuálních nákladech najdete v [tématu stanovení cen virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Velikost virtuálního počítače řadiče domény** je velikost virtuálního počítače pro řadiče domény. Pro tento kurz použijte **D2**.
* **Velikost virtuálního počítače Share Witness** je velikost virtuálního počítače pro důkaz čehož it share. Pro účely tohoto kurzu použijte **A1**.
* **Sql Storage účet** je název účtu úložiště, který obsahuje data serveru SQL Server a disky operačního systému. Pro účely tohoto kurzu použijte **alwaysonsql01**.
* **Účet úložiště řadiče** domény je název účtu úložiště pro řadiče domény. Pro účely tohoto kurzu použijte **alwaysondc01**.
* **Velikost datového disku serveru SQL Server** v TB je velikost datového disku serveru SQL Server v TB. Zadejte číslo od 1 do 4. Pro účely tohoto kurzu použijte **1**.
* **Optimalizace úložiště** nastaví konkrétní nastavení konfigurace úložiště pro virtuální počítače SQL Serveru na základě typu pracovního vytížení. Všechny virtuální počítače SQL Serveru v tomto scénáři používají úložiště premium s mezipamětí hostitele disku Azure nastavenou jen pro čtení. Kromě toho můžete optimalizovat nastavení serveru SQL Server pro úlohy výběrem jednoho z těchto tří nastavení:

  * **Obecné zatížení** nastaví žádné konkrétní nastavení konfigurace.
  * **Transakční zpracování** nastaví příznak trasování 1117 a 1118.
  * **Datové sklady** nastaví příznak trasování 1117 a 610.

V tomto kurzu použijte **obecné zatížení**.

![Nastavení úložiště velikosti virtuálního počítače](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Zkontrolujte nastavení a klepněte na tlačítko **OK**.

#### <a name="a-note-about-storage"></a>Poznámka o úložišti
Další optimalizace závisí na velikosti datových disků serveru SQL Server. Pro každý terabajt datového disku Azure přidá další úložiště premium 1 TB. Pokud server vyžaduje 2 TB nebo více, šablona vytvoří fond úložiště na každém virtuálním počítači SQL Server. Fond úložiště je forma virtualizace úložiště, kde je více disků nakonfigurováno tak, aby poskytovalo vyšší kapacitu, odolnost proti chybám a výkon.  Šablona pak vytvoří úložný prostor ve fondu úložiště a zobrazí jeden datový disk do operačního systému. Šablona označuje tento disk jako datový disk pro SQL Server. Šablona ladí fond úložiště pro SQL Server pomocí následujících nastavení:

* Velikost prokládání je nastavení prokládání virtuálního disku. Transakční úlohy používají 64 KB. Úlohy datových skladů využívají 256 kB.
* Odolnost proti chybám je jednoduchá (bez odolnosti proti chybám).

> [!NOTE]
> Azure premium úložiště je místně redundantní a uchovává tři kopie dat v rámci jedné oblasti, takže další odolnost proti chybám ve fondu úložiště není vyžadováno.
>
>

* Počet sloupců se rovná počtu disků ve fondu úložiště.

Další informace o úložném prostoru a fondech úložiště naleznete v tématu:

* [Prostory úložiště – přehled](https://technet.microsoft.com/library/hh831739.aspx)
* [Fondy zálohování a úložiště systému Windows Server](https://technet.microsoft.com/library/dn390929.aspx)

Další informace o doporučených postupech konfigurace serveru SQL Server naleznete v [tématu Doporučené postupy výkonu pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Nastavení SQL Serveru
V **nastavení serveru SQL Server**zkontrolujte a upravte předponu názvu virtuálního počítače serveru SQL Server, verzi serveru SQL Server, účet a heslo služby serveru SQL Server a plán údržby automatické opravy SQL.

* **Předpona názvu serveru SQL Server** se používá k vytvoření názvu pro každý virtuální počítač SQL Server. Pro účely tohoto kurzu použijte **sqlserver**. Šablona pojmenuje virtuální počítače SQL Server *sqlserver-0* a *sqlserver-1*.
* **Verze serveru SQL Server** je verze serveru SQL Server. Pro tento kurz použijte **SQL Server 2014**. Můžete také zvolit **SQL Server 2012** nebo **SQL Server 2016**.
* **Uživatelské jméno účtu služby SQL Server** je název účtu domény pro službu SQL Server. Pro účely tohoto kurzu použijte **sqlservice**.
* **Heslo** je heslo pro účet služby serveru SQL Server.  Použijte složité heslo. Potvrďte heslo.
* **Plán údržby automatické opravy SQL** určuje den v týdnu, kdy Azure automaticky opravuje servery SQL. Pro tento kurz zadejte **neděle**.
* **Sql Auto Patching údržba počáteční hodina** je denní doba pro oblast Azure při zahájení automatické opravy.

> [!NOTE]
> Okno opravy pro každý virtuální počítač je rozloženo o jednu hodinu. Pouze jeden virtuální počítač je oprava najednou, aby se zabránilo přerušení služeb.
>
>

![Nastavení SQL Serveru](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Zkontrolujte nastavení a klepněte na tlačítko **OK**.

### <a name="summary"></a>Souhrn
Na stránce souhrnu Azure ověřuje nastavení. Můžete si také stáhnout šablonu. Zkontrolujte souhrnné informace. Klikněte na tlačítko **OK**.

### <a name="buy"></a>Koupit
Tato poslední čepel obsahuje podmínky použití a **zásady ochrany** **osobních**údajů . Zkontrolujte tyto informace. Až budete připraveni, že Azure začne vytvářet virtuální počítače a všechny ostatní požadované prostředky pro skupinu dostupnosti, klikněte na **Vytvořit**.

Portál Azure vytvoří skupinu prostředků a všechny prostředky.

## <a name="monitor-deployment"></a>Monitorování nasazení
Sledujte průběh nasazení z webu Azure Portal. Ikona, která představuje nasazení, se automaticky připne na řídicí panel portálu Azure.

![Řídicí panel Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru
Nové instance SQL Serveru jsou spuštěny na virtuálních počítačích, které mají IP adresy připojené k internetu. Vzdálenou plochu (RDP) můžete přímo ke každému virtuálnímu počítači serveru SQL Server.

Chcete-li RDP na SQL Server, postupujte takto:

1. Z řídicího panelu portálu Azure ověřte, že nasazení proběhlo úspěšně.
2. Klepněte na **položku Zdroje**.
3. V okně **Prostředky** klikněte na **sqlserver-0**, což je název počítače jednoho z virtuálních počítačů, na kterém je spuštěn SQL Server.
4. V okně pro **sqlserver-0**klepněte na tlačítko **Připojit**. Prohlížeč se zeptá, zda chcete otevřít nebo uložit objekt vzdáleného připojení. Klikněte na **Otevřít**.
5. **Připojení ke vzdálené ploše** vás může upozornit, že nelze identifikovat vydavatele tohoto vzdáleného připojení. Klikněte na **Připojit**.
6. Zabezpečení systému Windows vás vyzve k zadání přihlašovacích údajů pro připojení k adrese IP primárního řadiče domény. Klepněte na **tlačítko Použít jiný účet**. Do **pole Uživatelské jméno**zadejte příkaz **contoso\DomainAdmin**. Tento účet jste nakonfigurovali při nastavení uživatelského jména správce v šabloně. Použijte složité heslo, které jste zvolili při konfiguraci šablony.
7. **Vzdálená plocha** vás může upozornit, že vzdálený počítač nelze ověřit z důvodu problémů s certifikátem zabezpečení. Zobrazuje název certifikátu zabezpečení. Pokud jste postupovali podle výukového programu, název je **sqlserver-0.contoso.com**. Klepněte na tlačítko **Ano**.

Nyní jste připojeni k virtuálnímu počítači serveru SQL Server. Můžete otevřít SQL Server Management Studio, připojit se k výchozí instanci serveru SQL Server a ověřit, zda je nakonfigurovaná skupina dostupnosti.
