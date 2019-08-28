---
title: Nastavení vysoké dostupnosti pro virtuální počítače s Azure Resource Manager | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit skupinu dostupnosti Always On s virtuálními počítači Azure v režimu Azure Resource Manager.
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
ms.openlocfilehash: a06ea59af0776fe3decb0b56a3ef886f08b2dfda
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100717"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Automatické konfigurace skupin dostupnosti Always On v Azure Virtual Machines: Resource Manager

V tomto kurzu se dozvíte, jak vytvořit SQL Server skupinu dostupnosti, která používá Azure Resource Manager virtuálních počítačů. Tento kurz používá ke konfiguraci šablony Azure Blade. Můžete zkontrolovat výchozí nastavení, zadat požadovaná nastavení a aktualizovat okna na portálu při procházení tohoto kurzu.

Úplný kurz vytvoří ve službě Azure Virtual Machines skupinu dostupnosti SQL Server, která zahrnuje následující prvky:

* Virtuální síť s více podsítěmi, včetně front-endové a back-endové podsítě
* Dva řadiče domény, které mají doménu služby Active Directory
* Dva virtuální počítače, které používají SQL Server a jsou nasazeny do podsítě back-endu a připojeny k doméně služby Active Directory
* Cluster s podporou převzetí služeb při selhání se třemi uzly s modelem kvora s většinou uzlů
* Skupina dostupnosti, která má dvě repliky se synchronním potvrzováním databáze dostupnosti

Následující obrázek představuje kompletní řešení.

![Architektura testovací laboratoře pro skupiny dostupnosti v Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Všechny prostředky v tomto řešení patří do jedné skupiny prostředků.

Než začnete s tímto kurzem, zkontrolujte následující:

* Už máte účet Azure. Pokud ho ještě nemáte, zaregistrujte [si zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
* Už víte, jak pomocí grafického uživatelského rozhraní zřídit SQL Server virtuální počítač z Galerie virtuálních počítačů. Další informace najdete v tématu [zřízení virtuálního počítače s SQL Server v Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Už máte plnou znalosti skupin dostupnosti. Další informace najdete v tématu [skupiny dostupnosti Always On (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Pokud vás zajímá používání skupin dostupnosti se službou SharePoint, přečtěte si také téma [Konfigurace skupin dostupnosti Always On SQL Server 2012 pro sharepoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

V tomto kurzu použijte Azure Portal pro:

* Na portálu vyberte šablonu Always On.
* Zkontrolujte nastavení šablony a aktualizujte několik konfiguračních nastavení pro vaše prostředí.
* Monitorujte Azure, protože vytváří celé prostředí.
* Připojte se k řadiči domény a potom k serveru, na kterém běží SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Zřízení clusteru z Galerie
Azure poskytuje image galerie pro celé řešení. Vyhledání šablony:

1. Přihlaste se k Azure Portal pomocí svého účtu.
2. V Azure Portal kliknutím na **vytvořit prostředek** otevřete **nové** podokno.
3. V podokně **Nový** vyhledejte **AlwaysOn**.
   ![Najít šablonu AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Ve výsledcích hledání vyhledejte **SQL Server clusteru AlwaysOn**.
   ![Šablona AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. V nabídce **Vybrat model nasazení**zvolte možnost **Správce prostředků**.

### <a name="basics"></a>Základní informace
Klikněte na **základy** a nakonfigurujte následující nastavení:

* **Uživatelské jméno správce** je uživatelský účet, který má oprávnění správce domény a je členem role pevného serveru SQL Server sysadmin v obou instancích SQL Server. Pro tento kurz použijte **DomainAdmin**.
* **Heslo** je heslo pro účet správce domény. Použijte složité heslo. Potvrďte heslo.
* **Předplatné** je předplatné, které Azure účtuje na spouštění všech nasazených prostředků pro skupinu dostupnosti. Pokud má váš účet více předplatných, můžete zadat jiné předplatné.
* **Skupina prostředků** je název skupiny, do které patří všechny prostředky Azure vytvořené touto šablonou. Pro tento kurz použijte **SQL-ha-RG**. Další informace naleznete v tématu [Přehled Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Umístění** je oblast Azure, ve které kurz vytváří prostředky. Vyberte oblast Azure.

Následující snímek obrazovky je kompletní okno **základní** :

![Základní informace](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klikněte na **OK**.

### <a name="domain-and-network-settings"></a>Nastavení domény a sítě
Tato šablona Galerie Azure vytvoří domény a řadiče domény. Vytvoří také síť a dvě podsítě. Šablona nemůže vytvořit servery v existující doméně nebo virtuální síti. V dalším kroku se nakonfiguruje nastavení domény a sítě.

V okně **nastavení domény a sítě** zkontrolujte přednastavené hodnoty nastavení doména a síť:

* **Název kořenové domény doménové struktury** je název domény pro doménu služby Active Directory, která je hostitelem clusteru. Pro kurz použijte **contoso.com**.
* **Název Virtual Network** je název sítě pro virtuální síť Azure. Pro kurz použijte **autohaVNET**.
* **Název podsítě řadiče domény** je název části virtuální sítě, která je hostitelem řadiče domény. Použijte **podsíť-1**. Tato podsíť používá předponu adresy **10.0.0.0/24**.
* **SQL Server název podsítě** je název části virtuální sítě, která je hostitelem serverů se spuštěnou SQL Server a určující sdílenou složku. Použijte **podsíť-2**. Tato podsíť používá předponu adresy **10.0.1.0/26**.

Další informace o virtuálních sítích v Azure najdete v tématu [Přehled virtuálních sítí](../../../virtual-network/virtual-networks-overview.md).  

**Nastavení domény a sítě** by mělo vypadat jako na následujícím snímku obrazovky:

![Nastavení domény a sítě](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

V případě potřeby můžete tyto hodnoty změnit. Pro tento kurz použijte přednastavené hodnoty.

Zkontrolujte nastavení a klikněte na tlačítko **OK**.

### <a name="availability-group-settings"></a>Nastavení skupiny dostupnosti
V části **Nastavení skupiny dostupnosti**zkontrolujte přednastavené hodnoty pro skupinu dostupnosti a naslouchací proces.

* **Název skupiny dostupnosti** je clusterovaný název prostředku pro skupinu dostupnosti. Pro tento kurz použijte **Contoso-AG**.
* **Název naslouchacího procesu skupiny dostupnosti** je používán clusterem a interním nástrojem pro vyrovnávání zatížení. Klienti, kteří se připojují k SQL Server, můžou tento název použít pro připojení k příslušné replice databáze. Pro tento kurz použijte službu **Contoso-Listener**.
* **Port naslouchacího procesu skupiny dostupnosti** Určuje port TCP naslouchacího procesu SQL Server. Pro tento kurz použijte výchozí port **1433**.

V případě potřeby můžete tyto hodnoty změnit. Pro tento kurz použijte přednastavené hodnoty.  

![nastavení skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klikněte na **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Velikost virtuálního počítače, nastavení úložiště
V části **Velikost virtuálního počítače, nastavení úložiště**vyberte velikost virtuálního počítače SQL Server a zkontrolujte další nastavení.

* **Velikost virtuálního počítače SQL Server** je velikost pro virtuální počítače, které spouští SQL Server. Vyberte vhodnou velikost virtuálního počítače pro vaše zatížení. Pokud pro tento kurz sestavíte toto prostředí, použijte **DS2**. V případě produkčních úloh vyberte velikost virtuálního počítače, která může podporovat zatížení. Mnoho produkčních úloh vyžaduje **DS4** nebo větší. Šablona sestaví dva virtuální počítače této velikosti a nainstaluje je SQL Server. Další informace najdete v tématu [velikosti virtuálních počítačů](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure nainstaluje edici Enterprise SQL Server. Náklady závisí na edici a velikosti virtuálního počítače. Podrobné informace o aktuálních nákladech najdete v tématu [ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Velikost virtuálního počítače řadiče domény** je velikost virtuálního počítače pro řadiče domény. V tomto kurzu použijete **D2**.
* **Velikost virtuálního počítače určující sdílené složky** je velikost virtuálního počítače určující sdílené složky. Pro tento kurz použijte **a1**.
* **Účet úložiště SQL** je název účtu úložiště, který obsahuje SQL Server dat a disků s operačním systémem. Pro tento kurz použijte **alwaysonsql01**.
* **Účet úložiště DC** je název účtu úložiště pro řadiče domény. Pro tento kurz použijte **alwaysondc01**.
* **Velikost datového disku SQL Server** v TB je velikost datového disku SQL Server v TB. Zadejte číslo od 1 do 4. Pro tento kurz použijte **1**.
* **Optimalizace úložiště** nastavuje konkrétní nastavení konfigurace úložiště pro SQL Server virtuálních počítačů na základě typu úlohy. Všechny virtuální počítače s SQL Server v tomto scénáři používají Storage úrovně Premium s Azure disk Host cache nastavenou na jen pro čtení. Kromě toho můžete optimalizovat nastavení SQL Server pro úlohy výběrem jednoho z těchto tří nastavení:

  * **Obecné úlohy** nastaví žádná konkrétní nastavení konfigurace.
  * Příznak trasování pro **zpracování transakcí** nastaví 1117 a 1118.
  * Příznak trasování **datových skladů** nastaví 1117 a 610.

Pro tento kurz použijte **Obecné úlohy**.

![Nastavení úložiště velikosti virtuálního počítače](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Zkontrolujte nastavení a klikněte na tlačítko **OK**.

#### <a name="a-note-about-storage"></a>Poznámka k úložišti
Další optimalizace závisí na velikosti SQL Server datových disků. Pro každou terabajty datového disku Azure přidá dalších 1 TB Premium úložiště. Když server vyžaduje 2 TB nebo více, vytvoří šablona fond úložiště na každém virtuálním počítači s SQL Server. Fond úložiště je forma virtualizace úložiště, kde je nakonfigurováno více disků, aby poskytovaly vyšší kapacitu, odolnost a výkon.  Šablona pak vytvoří prostor úložiště ve fondu úložiště a v operačním systému prezentuje jeden datový disk. Tato šablona označuje tento disk jako datový disk pro SQL Server. Šablona vyladí fond úložiště pro SQL Server pomocí následujících nastavení:

* Velikost proložení je nastavení prokládání pro virtuální disk. Transakční úlohy využívají 64 KB. Úlohy datového skladu využívají 256 KB.
* Odolnost proti chybám je jednoduchá (bez odolnosti).

> [!NOTE]
> Azure Premium Storage je místně redundantní a udržuje tři kopie dat v rámci jedné oblasti, takže se u fondu úložiště nevyžaduje další odolnost.
>
>

* Počet sloupců se rovná počtu disků ve fondu úložiště.

Další informace o prostoru úložiště a fondech úložiště najdete v těchto tématech:

* [Prostory úložiště – přehled](https://technet.microsoft.com/library/hh831739.aspx)
* [Zálohování Windows Serveru a fondy úložiště](https://technet.microsoft.com/library/dn390929.aspx)

Další informace o osvědčených postupech konfigurace SQL Server najdete v tématu [osvědčené postupy výkonu pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Nastavení SQL Serveru
V **nastavení SQL Server**zkontrolujte a upravte SQL Server název virtuálního počítače, SQL Server verzi, účet služby SQL Server a heslo a plán údržby automatických oprav SQL.

* **SQL Server Předpona názvu** se používá k vytvoření názvu pro každý SQL Server virtuální počítač. Pro tento kurz použijte **SQLServer**. Šablona vyjmenovává SQL Server virtuálních počítačů *SQLServer-0* a *SQLServer-1*.
* Verze SQL Server **SQL Server** . Pro tento kurz použijte **SQL Server 2014**. Můžete také zvolit **SQL Server 2012** nebo **SQL Server 2016**.
* **Uživatelské jméno účtu služby SQL Server** je název účtu domény služby SQL Server. Pro tento kurz použijte **SqlService**.
* **Heslo** je heslo pro účet služby SQL Server.  Použijte složité heslo. Potvrďte heslo.
* **Plán údržby automatických oprav SQL** Určuje den v týdnu, kdy Azure automaticky opraví SQL servery. Pro tento kurz zadejte **neděli**.
* Doba **údržby automatických oprav SQL je hodina** času pro oblast Azure při zahájení automatických oprav.

> [!NOTE]
> Okno oprav pro jednotlivé virtuální počítače se rozdělují o jednu hodinu. Jenom jeden virtuální počítač se dá opravit současně, aby nedošlo k přerušení služeb.
>
>

![Nastavení SQL Serveru](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Zkontrolujte nastavení a klikněte na tlačítko **OK**.

### <a name="summary"></a>Souhrn
Na stránce Souhrn Azure ověří nastavení. Můžete si také stáhnout šablonu. Zkontrolujte souhrnné informace. Klikněte na **OK**.

### <a name="buy"></a>Koupit
Toto závěrečné okno obsahuje **podmínkami použití**a **Zásady ochrany osobních údajů**. Projděte si tyto informace. Až budete připraveni, aby Azure začal vytvářet virtuální počítače a všechny další požadované prostředky pro skupinu dostupnosti, klikněte na **vytvořit**.

Azure Portal vytvoří skupinu prostředků a všechny prostředky.

## <a name="monitor-deployment"></a>Monitorování nasazení
Monitorujte průběh nasazení z Azure Portal. Ikona, která představuje nasazení, se automaticky připnula k řídicímu panelu Azure Portal.

![Řídicí panel Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru
Nové instance SQL Server jsou spuštěny na virtuálních počítačích, které mají IP adresy připojené k Internetu. Vzdálenou plochu (RDP) můžete připojit přímo ke každému SQL Servermu virtuálnímu počítači.

K protokolu RDP na SQL Server použijte následující postup:

1. Z řídicího panelu Azure Portal ověřte, že nasazení proběhlo úspěšně.
2. Klikněte na **prostředky**.
3. V okně **prostředky** klikněte na **SQLServer-0**, což je název počítače jednoho z virtuálních počítačů, na kterých běží SQL Server.
4. V okně pro **SQLServer-0**klikněte na **připojit**. V prohlížeči se zobrazí dotaz, zda chcete objekt vzdáleného připojení otevřít nebo Uložit. Klikněte na tlačítko **otevřít**.
5. **Připojení ke vzdálené ploše** vás může upozornit, že nelze identifikovat vydavatele tohoto vzdáleného připojení. Klikněte na **Připojit**.
6. Systém Windows Security vyzve k zadání přihlašovacích údajů pro připojení k IP adrese primárního řadiče domény. Klikněte na **použít jiný účet**. Do tohoto **uživatelského jména**zadejte **contoso\DomainAdmin**. Tento účet jste nakonfigurovali při nastavování uživatelského jména správce v šabloně. Použijte složitá hesla, která jste zvolili při konfiguraci šablony.
7. **Vzdálená plocha** vás může upozorňovat na to, že vzdálený počítač nelze ověřit z důvodu problémů s jeho certifikátem zabezpečení. Zobrazuje název certifikátu zabezpečení. Pokud jste postupovali podle tohoto kurzu, název je **SQLServer-0.contoso.com**. Klikněte na **Ano**.

Nyní jste připojeni pomocí protokolu RDP k virtuálnímu počítači s SQL Server. Můžete otevřít SQL Server Management Studio, připojit se k výchozí instanci SQL Server a ověřit, jestli je skupina dostupnosti nakonfigurovaná.
