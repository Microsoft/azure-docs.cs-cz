---
title: Nastavením vysoké dostupnosti pro virtuální počítače Azure Resource Manager | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit skupiny dostupnosti Always On s virtuálními počítači Azure v režimu Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: a612ffd5a68e34cb0a367a6a883495ef26aeb4bc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401017"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>V Azure Virtual Machines automaticky konfigurovat skupiny dostupnosti Always On: Správce prostředků

V tomto kurzu se dozvíte, jak vytvořit skupinu dostupnosti systému SQL Server, která používá Azure Resource Manager virtuálních počítačů. Tento kurz používá oken Azure konfigurace šablony. Zkontrolujte výchozí nastavení, zadejte požadovaná nastavení a aktualizujte okna na portálu, jak projít tento kurz.

Dokončení kurzu vytvoří skupinu dostupnosti systému SQL Server v Azure Virtual Machines, které obsahují tyto prvky:

* Virtuální síť, která má více podsítí, včetně front-end a back-end podsítě
* Dva řadiče domény, které mají domény služby Active Directory
* Dva virtuální počítače, které se systémem SQL Server a jsou nasazené do podsítě back-end a připojený k doméně služby Active Directory
* Clusteru s podporou převzetí služeb při selhání tři uzly s modelem kvora Většina uzlů
* Skupiny dostupnosti, která má dva replik se synchronním potvrzováním databáze dostupnosti

Následující obrázek ilustruje úplného řešení.

![Architektura testovací laboratoř pro skupiny dostupnosti v Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Všechny prostředky v tomto řešení patřit do jedna skupina prostředků.

Než začnete tento kurz, ověřte následující:

* Už máte účet Azure. Pokud nemáte, [si zaregistrovat zkušební účet](http://azure.microsoft.com/pricing/free-trial/).
* Už víte, jak pomocí grafického uživatelského rozhraní pro zřízení virtuálního počítače s SQL serverem z Galerie virtuálního počítače. Další informace najdete v tématu [zřízení virtuálního počítače systému SQL Server na platformě Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Už máte plnou Principy skupin dostupnosti. Další informace najdete v tématu [Always On skupiny dostupnosti (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Pokud vás zajímá pomocí skupin dostupnosti s SharePoint, také zobrazit [nakonfigurovat SQL Server 2012 Always On skupin dostupnosti pro službu SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).
>
>

V tomto kurzu pomocí portálu Azure:

* Zvolte šablonu Always On z portálu.
* Zkontrolujte nastavení šablony a aktualizovat několik nastavení konfigurace pro vaše prostředí.
* Monitorování Azure, jelikož vytvoří celé prostředí.
* Připojte k řadiči domény a pak na serveru se systémem SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Zřízení clusteru z Galerie
Azure poskytuje bitovou kopii Galerie pro celé řešení. Vyhledejte šablonu:

1. Přihlaste se k portálu Azure pomocí účtu.
2. Na portálu Azure klikněte na tlačítko **vytvořit prostředek** otevřete **nový** podokně.
3. Na **nový** podokně, vyhledejte **AlwaysOn**.
   ![Najít šablonu AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Ve výsledcích hledání najít **clusteru serveru SQL Server AlwaysOn**.
   ![Šablona AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Na **vybrat model nasazení**, zvolte **Resource Manager**.

### <a name="basics"></a>Základy
Klikněte na tlačítko **Základy** a nakonfigurujte následující nastavení:

* **Uživatelské jméno správce** je uživatelský účet, který má oprávnění správce domény a je členem role pevného serveru správce systému SQL Server na obou instancí systému SQL Server. V tomto kurzu použít **DomainAdmin**.
* **Heslo** je heslo pro účet správce domény. Použijte složité heslo. Potvrzení hesla.
* **Předplatné** je odběr tohoto Azure účtuje poplatky za spustit všechny nasazené prostředky pro skupinu dostupnosti. Pokud má váš účet více předplatných, můžete zadat jiný odběr.
* **Skupina prostředků** je název pro skupinu, do které patří všechny prostředky Azure, které jsou vytvořené pomocí této šablony. V tomto kurzu použít **SQL-HA-RG**. Další informace naleznete v tématu [Přehled Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Umístění** je oblast Azure, kde kurzu vytvoří prostředky. Vyberte oblast Azure.

Na následujícím snímku obrazovky je dokončené **Základy** okno:

![Základy](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klikněte na **OK**.

### <a name="domain-and-network-settings"></a>Nastavení sítě a domény
Tato šablona Galerie Azure vytvoří domény a řadiče domény. Vytvoří také síť a dvě podsítě. Šablonu nelze vytvořit servery v existující doméně nebo virtuální sítě. Další krok nakonfiguruje nastavení sítě a domény.

Na **nastavení sítě a domény** , zkontrolujte přednastavené hodnoty pro doménu a nastavení síťového:

* **Název kořenové domény doménové struktury** je název domény pro doménu služby Active Directory, který je hostitelem clusteru. Pro tento kurz použijte **contoso.com**.
* **Název virtuální sítě** je název sítě pro virtuální síť Azure. Pro tento kurz použijte **autohaVNET**.
* **Název podsítě řadiče domény** je název část virtuální sítě, který je hostitelem řadiče domény. Použití **podsíť 1**. Tato podsíť používá předpona adresy **10.0.0.0/24**.
* **Název systému SQL Server podsítě** je název část virtuální síť, která hostuje servery, že spuštění systému SQL Server a soubor sdílet s kopií clusteru. Použití **podsíť 2**. Tato podsíť používá předpona adresy **10.0.1.0/26**.

Další informace o virtuálních sítí v Azure najdete v tématu [Přehled virtuálních sítí](../../../virtual-network/virtual-networks-overview.md).  

**Nastavení sítě a domény** by měl vypadat jako na následujícím snímku obrazovky:

![Nastavení sítě a domény](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

V případě potřeby můžete tyto hodnoty změnit. V tomto kurzu použijte přednastavené hodnoty.

Zkontrolujte nastavení a pak klikněte na tlačítko **OK**.

### <a name="availability-group-settings"></a>Nastavení skupiny dostupnosti
Na **nastavení skupiny dostupnosti**, zkontrolujte přednastavené hodnoty pro skupinu dostupnosti a naslouchací proces.

* **Název skupiny dostupnosti** je název clusteru prostředků pro skupiny dostupnosti. V tomto kurzu použít **Contoso-ag**.
* **Název naslouchacího procesu skupiny dostupnosti** je používán clusteru a nástroje pro vyrovnávání zatížení interní. Klienti, kteří připojují k systému SQL Server můžete použít tento název se připojit k příslušné repliky databáze. V tomto kurzu použít **Contoso-naslouchací proces**.
* **Port naslouchacího procesu skupiny dostupnosti** Určuje port TCP systému SQL Server naslouchacího procesu. V tomto kurzu použít výchozí port **1433**.

V případě potřeby můžete tyto hodnoty změnit. V tomto kurzu použijte přednastavené hodnoty.  

![Nastavení skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klikněte na **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Velikost virtuálního počítače, nastavení úložiště
Na **velikost virtuálního počítače, nastavení úložiště**, zvolte velikost virtuálního počítače systému SQL Server a zkontrolujte další nastavení.

* **Velikost virtuálního počítače systému SQL Server** je velikost pro virtuální počítače se systémem SQL Server. Zvolte velikost příslušné virtuální počítač pro úlohy. Pokud vytváříte tuto prostředí pro tento kurz, použijte **DS2**. Pro úlohy v produkčním prostředí zvolte velikost virtuálního počítače, který podporuje zatížení. Vyžadovat mnoho úlohy v produkčním prostředí **DS4** nebo větší. Šablony sestavení dva virtuální počítače této velikosti a nainstaluje systém SQL Server na každém z nich. Další informace najdete v tématu [velikosti virtuálních počítačů](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure nainstaluje edici Enterprise systému SQL Server. Cena závisí na edici a velikost virtuálního počítače. Podrobné informace o aktuální náklady, najdete v části [ceny virtuálních počítačů](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Velikost virtuálního počítače řadiče domény** je velikost virtuálního počítače pro řadiče domény. Pro tento kurz použití **D2**.
* **Velikost virtuálního počítače určující sdílenou složku souboru** je velikost virtuálního počítače pro sdílenou složku. V tomto kurzu použít **A1**.
* **Účet úložiště SQL** je název účtu úložiště, který obsahuje data systému SQL Server a disky operačního systému. V tomto kurzu použít **alwaysonsql01**.
* **Účet řadiče domény úložiště** je název účtu úložiště pro řadiče domény. V tomto kurzu použít **alwaysondc01**.
* **Kapacita disku dat systému SQL Server** v TB je velikost disku TB dat systému SQL Server. Zadejte číslo od 1 do 4. V tomto kurzu použít **1**.
* **Optimalizace úložiště** nastaví nastavení konfigurace konkrétní úložiště pro virtuální počítače systému SQL Server, na základě typu úlohy. Všechny virtuální počítače systému SQL Server v tomto scénáři používat úložiště úrovně premium s mezipamětí Azure disk hostitele nastavit na jen pro čtení. Kromě toho můžete optimalizovat nastavení systému SQL Server pro pracovní vytížení volbou jedné z těchto tří nastavení:

  * **Obecné úlohy** nastaví žádné specifické nastavení.
  * **Zpracování transakcí** nastaví příznak. 1117 a 1118 trasování.
  * **Datové sklady** nastaví příznak. 1117 trasování a 610.

V tomto kurzu použít **obecné zatížení**.

![Nastavení velikosti úložiště pro virtuální počítač](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Zkontrolujte nastavení a pak klikněte na tlačítko **OK**.

#### <a name="a-note-about-storage"></a>Poznámka o úložiště
Další optimalizace záviset na velikosti datových disků systému SQL Server. Pro každý terabajt datový disk Azure přidá další úložiště premium 1 TB. Pokud server vyžaduje 2 TB nebo více, vytvoří šablona dílčí fondu úložiště na každém virtuálním počítači systému SQL Server. Fond úložiště je formu virtualizace úložiště, které jsou nakonfigurované víc disků k zajištění vyšší kapacity, odolnost proti chybám a výkonu.  Šablony potom vytvoří prostor úložiště ve fondu úložiště a představuje jeden datový disk v operačním systému. Šablonu označí tento disk jako datový disk pro SQL Server. Šablona vyladí fondu úložiště pro SQL Server pomocí následujících nastavení:

* Stripe velikost je prokládání nastavení pro virtuální disk. Transakční úlohy používají 64 KB. Datového skladu úlohy použijte 256 KB.
* Odolnost proti chybám je jednoduché (bez odolnosti).

> [!NOTE]
> Azure premium storage je místně redundantní a udržuje tři kopie dat v jedné oblasti, takže další odolnosti ve fondu úložiště není potřeba.
>
>

* Počet sloupců se rovná počet disků ve fondu úložiště.

Další informace o prostor úložiště a fondy úložiště, najdete v tématu:

* [Prostory úložiště – přehled](http://technet.microsoft.com/library/hh831739.aspx)
* [Zálohování serveru a fondy úložiště](http://technet.microsoft.com/library/dn390929.aspx)

Další informace o osvědčenými postupy konfigurace systému SQL Server najdete v tématu [nejlepší postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Nastavení SQL Serveru
Na **nastavení systému SQL Server**, zkontrolovat a upravit předpony názvu virtuálního počítače systému SQL Server, verze systému SQL Server, účet služby SQL Server a hesla a plán údržby automatické opravy SQL.

* **SQL Server název předpony** se používá k vytvoření název pro každý virtuální počítač systému SQL Server. V tomto kurzu použít **sqlserver**. Šablona názvy virtuálních počítačů systému SQL Server *sqlserver-0* a *sqlserver-1*.
* **Verze systému SQL Server** je verze systému SQL Server. Pro tento kurz použití **SQL Server 2014**. Můžete také **SQL Server 2012** nebo **SQL Server 2016**.
* **Uživatelské jméno účtu služby SQL Server** je název účtu domény pro službu systému SQL Server. V tomto kurzu použít **byl**.
* **Heslo** je heslo pro účet služby SQL Server.  Použijte složité heslo. Potvrzení hesla.
* **Plán údržby SQL automatické opravy** identifikuje den v týdnu, Azure automaticky opravy servery SQL Server. V tomto kurzu zadejte **neděle**.
* **Automatické opravy SQL údržby spustit hodinu** je čas během dne pro oblast Azure, automatické opravy zahájení.

> [!NOTE]
> Okno oprav pro každý virtuální počítač rozložena o jednu hodinu. Jenom jeden virtuální počítač je opravit v době, aby se zabránilo přerušení služeb.
>
>

![Nastavení SQL Serveru](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Zkontrolujte nastavení a pak klikněte na tlačítko **OK**.

### <a name="summary"></a>Souhrn
Na stránce Souhrn Azure ověří nastavení. Můžete také stáhnout šablony. Zkontrolujte souhrnné informace. Klikněte na **OK**.

### <a name="buy"></a>Koupit
Tento poslední okno obsahuje **podmínky použití**, a **zásady ochrany osobních údajů**. Přečtěte si tyto informace. Až budete připravení pro Azure. Chcete-li začít vytvářet virtuální počítače a všechny ostatní požadované prostředky pro skupinu dostupnosti, klikněte na tlačítko **vytvořit**.

Portál Azure vytvoří skupinu prostředků a všechny prostředky.

## <a name="monitor-deployment"></a>Monitorování nasazení
Sledujte průběh nasazení z portálu Azure. Ikona, která představuje nasazení je automaticky připnuli k řídicímu panelu portálu Azure.

![Řídicí panel Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru
Nové instance systému SQL Server běží na virtuálních počítačích, které mají připojené k Internetu IP adresy. Můžete vzdálené plochy (RDP) přímo do každého virtuálního počítače systému SQL Server.

Pro připojení RDP k systému SQL Server postupujte takto:

1. Na řídicím panelu Azure portálu ověřte, že nasazení proběhla úspěšně.
2. Klikněte na tlačítko **prostředky**.
3. V **prostředky** okně klikněte na tlačítko **sqlserver-0**, což je název počítače jednoho z virtuálních počítačů, které se systémem SQL Server.
4. V okně **sqlserver-0**, klikněte na tlačítko **Connect**. Prohlížeč zobrazí, pokud chcete otevřít nebo uložit objekt vzdáleného připojení. Klikněte na tlačítko **otevřete**.
5. **Připojení ke vzdálené ploše** může varovat, že nelze identifikovat vydavatele tohoto vzdáleného připojení. Klikněte na **Připojit**.
6. Zabezpečení systému Windows zobrazí výzvu k zadání pověření pro připojení k primární řadič domény na IP adresu. Klikněte na tlačítko **použít jiný účet**. Pro **uživatelské jméno**, typ **contoso\DomainAdmin**. Tento účet jste nakonfigurovali, když nastavíte uživatelské jméno pro správce v šabloně. Použijte složité heslo, které jste zvolili, když jste nakonfigurovali šablonu.
7. **Vzdálená plocha** může varovat, že vzdáleného počítače nelze ověřit z důvodu problémů s certifikátem zabezpečení. Zobrazuje jste název certifikátu zabezpečení. Pokud jste postupovali podle kurzu, je název **sqlserver 0.contoso.com**. Klikněte na tlačítko **Ano**.

Nyní jste připojeni pomocí protokolu RDP pro virtuální počítač systému SQL Server. Otevřete aplikaci SQL Server Management Studio, připojte se k výchozí instanci systému SQL Server a ověřte, zda skupina dostupnosti je nakonfigurován.
