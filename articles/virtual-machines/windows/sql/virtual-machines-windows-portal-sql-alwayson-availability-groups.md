---
title: Nastavením vysoké dostupnosti pro virtuální počítače Azure Resource Manageru | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit skupinu dostupnosti AlwaysOn s virtuálními počítači Azure v režimu Azure Resource Manageru.
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
ms.openlocfilehash: bddc83d55c8909412f7f935a4324a6f316a82cd7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238161"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Ve službě Azure Virtual Machines Automatická konfigurace skupin dostupnosti Always On: Správce prostředků

V tomto kurzu se dozvíte, jak vytvořit skupinu dostupnosti systému SQL Server, který používá virtuální počítače Azure Resource Manageru. Tento kurz používá ke konfiguraci šablony oken webu Azure. Můžete zkontrolovat výchozí nastavení, zadejte požadovaná nastavení a aktualizujte oknech na portálu, jak si projdete tohoto kurzu.

Úplný kurz vytvoří skupinu dostupnosti SQL Server na virtuálních počítačích Azure, které obsahují následující prvky:

* Virtuální síť, která má více podsítí, včetně front-end a back-endové podsítě
* Dva řadiče domény, které máte doménu služby Active Directory
* Dva virtuální počítače, které spustit systém SQL Server a jsou nasazené na podsíť back-endu a připojený k doméně služby Active Directory
* Cluster převzetí služeb při selhání třemi uzly pomocí modelu kvora Většina uzlů
* Skupiny dostupnosti, která má dvě repliky synchronního potvrzování dostupnosti databáze

Následující obrázek představuje kompletní řešení.

![Testovací prostředí architektury pro skupiny dostupnosti v Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Všechny prostředky v tomto řešení patřit do jedné skupiny prostředků.

Než začnete tento kurz, zkontrolujte následující body:

* Už máte účet Azure. Pokud ho nemáte, [zaregistrovat zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
* Už víte, jak použít grafické uživatelské rozhraní pro zřízení virtuálního počítače s SQL serverem z Galerie virtuálních počítačů. Další informace najdete v tématu [zřizování virtuálního počítače s SQL serverem v Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Už máte důkladného porozumění skupiny dostupnosti. Další informace najdete v tématu [skupiny dostupnosti AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Pokud jste chtěli použít skupiny dostupnosti se Sharepointem, viz také [konfigurace SQL serveru 2012 Always On skupin dostupnosti pro službu SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
>
>

V tomto kurzu pomocí webu Azure portal:

* Výběr šablony Always On na portálu.
* Zkontrolujte nastavení šablony a aktualizovat několik nastavení konfigurace pro vaše prostředí.
* Monitorování Azure při vytváření celé prostředí.
* Připojte se k řadiči domény a potom na serveru se systémem SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Zřízení clusteru z Galerie
Azure poskytuje image Galerie pro celé řešení. Vyhledejte šablonu:

1. Přihlaste se k webu Azure portal pomocí svého účtu.
2. Na webu Azure Portal, klikněte na tlačítko **vytvořit prostředek** otevřít **nový** podokně.
3. Na **nový** podokno, vyhledejte **AlwaysOn**.
   ![Najít šablonu AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Ve výsledcích hledání vyhledejte **clusteru SQL serveru AlwaysOn**.
   ![Šablona AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Na **vybrat model nasazení**, zvolte **Resource Manageru**.

### <a name="basics"></a>Základy
Klikněte na tlačítko **Základy** a nakonfigurujte následující nastavení:

* **Uživatelské jméno správce** je uživatelský účet, který má oprávnění správce domény a je členem role pevného serveru sysadmin SQL serveru v obou instancích systému SQL Server. Pro účely tohoto kurzu použijte **DomainAdmin**.
* **Heslo** je heslo pro účet správce domény. Použijte složité heslo. Potvrďte heslo.
* **Předplatné** je předplatné tohoto Azure účtuje poplatky za spuštění všech nasazených prostředků pro skupinu dostupnosti. Pokud váš účet má několik předplatných, můžete zadat jiné předplatné.
* **Skupina prostředků** je název pro skupinu, do které patří všechny prostředky Azure, které jsou vytvořené pomocí této šablony. Pro účely tohoto kurzu použijte **SQL-HA-RG**. Další informace naleznete v tématu [Přehled Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Umístění** je oblast Azure, ve kterém tento kurz vytvoří prostředky. Vyberte oblast Azure.

Na následujícím snímku obrazovky je a vyplněný **Základy** okno:

![Základy](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klikněte na **OK**.

### <a name="domain-and-network-settings"></a>Nastavení domény a síť
Tato šablona Galerie Azure vytvoří domény a řadiče domény. Vytvoří také síť a dvě podsítě. Šablonu nelze vytvořit servery v existující doméně nebo virtuální sítě. Dalším krokem nakonfiguruje nastavení domény a síť.

Na **nastavení domény a síť** okně zkontrolujte přednastavených hodnoty pro doménu a nastavení sítě:

* **Název kořenové domény doménové struktury** je název domény pro doménu služby Active Directory, který je hostitelem clusteru. Pro tento kurz použít **contoso.com**.
* **Název virtuální sítě** je název sítě pro virtuální síť Azure. Pro tento kurz použít **autohaVNET**.
* **Název podsítě řadiče domény** je název část ve virtuální síti, který je hostitelem řadiče domény. Použití **subnet-1**. Tuto podsíť používá předponu adresy **10.0.0.0/24**.
* **Název podsítě systému SQL Server** je název část virtuální síť, která hostuje servery, že spuštění SQL serveru a soubor sdílet s kopií clusteru. Použití **podsíť 2**. Tuto podsíť používá předponu adresy **10.0.1.0/26**.

Další informace o virtuálních sítích v Azure najdete v tématu [Přehled virtuálních sítí](../../../virtual-network/virtual-networks-overview.md).  

**Nastavení domény a síť** by měl vypadat jako na následujícím snímku obrazovky:

![Nastavení domény a síť](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

V případě potřeby můžete změnit tyto hodnoty. Pro účely tohoto kurzu použijte přednastavených hodnoty.

Zkontrolujte nastavení a potom klikněte na tlačítko **OK**.

### <a name="availability-group-settings"></a>Nastavení skupiny dostupnosti
Na **nastavení skupiny dostupnosti**, zkontrolujte přednastavených hodnoty pro skupinu dostupnosti a naslouchacího procesu.

* **Název skupiny dostupnosti** je název clusteru prostředků pro skupinu dostupnosti. Pro účely tohoto kurzu použijte **Contoso-ag**.
* **Název naslouchacího procesu skupiny dostupnosti** používá cluster a interní služby load balancer. Klienti připojující se k serveru SQL Server můžete použít tento název se připojit k příslušné repliky databáze. Pro účely tohoto kurzu použijte **Contoso-listener**.
* **Port naslouchacího procesu skupiny dostupnosti** Určuje port TCP systému SQL Server naslouchacího procesu. Pro účely tohoto kurzu použijte výchozí port **1433**.

V případě potřeby můžete změnit tyto hodnoty. Pro účely tohoto kurzu použijte přednastavených hodnoty.  

![nastavení skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klikněte na **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Velikost virtuálního počítače, nastavení úložiště
Na **velikost virtuálního počítače, nastavení úložiště**, zvolte velikost virtuálního počítače SQL serveru a zkontrolujte ostatní nastavení.

* **Velikost virtuálního počítače systému SQL Server** velikosti pro virtuální počítače, na kterých běží SQL Server. Zvolte velikost příslušný virtuální počítač pro vaši úlohu. Pokud vytváříte toto prostředí pro tento kurz, použijte **DS2**. Pro produkční úlohy zvolte velikost virtuálního počítače, který může podporovat zatížení. Mnoho úloh v produkčním prostředí vyžadují **DS4** nebo větší. Šablona vytvoří dva virtuální počítače v tomto rozsahu a nainstaluje systém SQL Server na každém z nich. Další informace najdete v tématu [velikosti virtuálních počítačů](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure nainstaluje edici Enterprise systému SQL Server. Cena závisí na edici a velikost virtuálního počítače. Podrobné informace o aktuální náklady, najdete v části [ceny virtual machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Velikost virtuálního počítače řadiče domény** je velikost virtuálního počítače pro řadiče domény. Pro tento kurz používá **D2**.
* **Velikost virtuálního počítače určující sdílené složky souboru** je velikost virtuálního počítače pro určující sdílenou složku. Pro účely tohoto kurzu použijte **A1**.
* **Účet úložiště SQL** je název účtu úložiště, která obsahuje data systému SQL Server a disky operačního systému. Pro účely tohoto kurzu použijte **alwaysonsql01**.
* **Účet úložiště DC** je název účtu úložiště pro řadiče domény. Pro účely tohoto kurzu použijte **alwaysondc01**.
* **Velikost disku serveru SQL Server data** v TB je velikost datového disku serveru SQL Server v TB. Zadejte číslo od 1 do 4. Pro účely tohoto kurzu použijte **1**.
* **Optimalizace úložiště** provádí konfigurační nastavení konkrétní úložiště pro virtuální počítače systému SQL Server na základě typu úlohy. Všechny virtuální počítače systému SQL Server v tomto scénáři používat premium storage s Azure diskové mezipaměti hostitele nastavena jen pro čtení. Kromě toho můžete optimalizovat nastavení systému SQL Server pro pracovní vytížení výběrem jedné z těchto tří nastavení:

  * **Obecné úlohy** nastaví žádné nakonfigurovat specifické nastavení.
  * **Zpracování transakcí** nastaví příznak. 1117 a 1118 trasování.
  * **Datové sklady** nastaví příznak. 1117 trasování a 610.

Pro účely tohoto kurzu použijte **obecné úlohy**.

![Nastavení úložiště velikost virtuálního počítače](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Zkontrolujte nastavení a potom klikněte na tlačítko **OK**.

#### <a name="a-note-about-storage"></a>Všimněte si o službě storage
Další optimalizace, závisí na velikosti datových disků systému SQL Server. Pro každý terabajt datový disk Azure přidá další 1 TB úložiště úrovně premium. Pokud server vyžaduje 2 TB nebo větší, šablona vytvoří fond úložiště na každém virtuálním počítači SQL serveru. Fond úložiště je určitou formu virtualizace úložiště, které jsou nakonfigurované více disků pro zajištění vyšší kapacitu, odolnost a výkon.  Šablonu pak vytvoří prostor úložiště na fond úložiště a představuje jeden datový disk pro operační systém. Šablonu označí tento disk jako datový disk pro SQL Server. Šablona vylaďuje fondu úložiště pro SQL Server pomocí následujících nastavení:

* Velikost stripe je prokládání nastavení pro virtuální disk. Transakčními úlohami použít 64 KB. Úlohy datových skladů pomocí 256 KB.
* Odolnost proti chybám je jednoduché (bez odolnosti).

> [!NOTE]
> Azure premium storage je místně redundantní a udržuje tři kopie dat v rámci jedné oblasti a tak dodatečnou odolnost ve fondu úložiště není potřeba.
>
>

* Počet sloupců se rovná počtu disků ve fondu úložiště.

Další informace o prostor úložiště a fondů úložiště najdete v tématu:

* [Prostory úložiště – přehled](https://technet.microsoft.com/library/hh831739.aspx)
* [Zálohování Windows serveru a fondy úložiště](https://technet.microsoft.com/library/dn390929.aspx)

Další informace o osvědčených postupech pro SQL Server configuration najdete v tématu [osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Nastavení SQL Serveru
Na **nastavení systému SQL Server**, zkontrolovat a upravit prefix názvu virtuálního počítače systému SQL Server, verze systému SQL Server, účet služby SQL Server a hesla a plán údržby SQL automatické opravy.

* **Prefix názvu SQL serveru** slouží k vytvoření názvu pro každý virtuální počítač systému SQL Server. Pro účely tohoto kurzu použijte **sqlserver**. Šablona názvy virtuálních počítačů s SQL serverem *sqlserver 0* a *sqlserver 1*.
* **Verze systému SQL Server** je verzi systému SQL Server. Pro tento kurz používá **SQL Server 2014**. Můžete také zvolit **systému SQL Server 2012** nebo **SQL serveru 2016**.
* **Uživatelské jméno účtu služby SQL Server** je název účtu domény pro službu systému SQL Server. Pro účely tohoto kurzu použijte **svědčí**.
* **Heslo** je heslo pro účet služby SQL Server.  Použijte složité heslo. Potvrďte heslo.
* **Plán údržby SQL automatické opravy** identifikuje den v týdnu, Azure automaticky opravy systémy SQL Server. Pro účely tohoto kurzu zadejte **neděle**.
* **Automatické opravy SQL údržby počáteční hodina** je čas pro oblast Azure, když začne automatické opravy.

> [!NOTE]
> Okno oprav pro každý virtuální počítač je rozloženo o jednu hodinu. Pouze jeden virtuální počítač je opravit v době, aby se zabránilo přerušení služeb.
>
>

![Nastavení SQL Serveru](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Zkontrolujte nastavení a potom klikněte na tlačítko **OK**.

### <a name="summary"></a>Souhrn
Na stránce Souhrn Azure ověří nastavení. Můžete také stáhnout šablony. Zkontrolujte souhrnné informace. Klikněte na **OK**.

### <a name="buy"></a>Koupit
Tento poslední okno obsahuje **podmínky použití**, a **zásady ochrany osobních údajů**. Projděte si tyto informace. Až budete připravení pro Azure začít vytvářet virtuální počítače a všechny ostatní požadované prostředky pro skupinu dostupnosti, klikněte na tlačítko **vytvořit**.

Na webu Azure portal vytvoří skupinu prostředků a všechny prostředky.

## <a name="monitor-deployment"></a>Monitorování nasazení
Sledujte průběh nasazení na webu Azure Portal. Ikona, která představuje nasazení se automaticky Připne na řídicí panel Azure.

![Řídicí panel Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru
Nové instance systému SQL Server běží na virtuálních počítačích, které mají IP adresy připojeného k Internetu. Můžete přímo do každého virtuálního počítače systému SQL Server vzdálené plochy (RDP).

Pro připojení RDP k systému SQL Server postupujte podle těchto kroků:

1. Z řídicího panelu Azure portal ověřte, nasazení proběhlo úspěšně.
2. Klikněte na tlačítko **prostředky**.
3. V **prostředky** okna, klikněte na tlačítko **sqlserver 0**, což je název počítače některého z virtuálních počítačů se systémem SQL Server.
4. V okně pro **sqlserver 0**, klikněte na tlačítko **připojit**. Váš prohlížeč požádá, pokud chcete otevřít nebo uložit objekt vzdáleného připojení. Klikněte na **Otevřít**.
5. **Připojení ke vzdálené ploše** možná by vás varovala, že nelze identifikovat vydavatele tohoto vzdáleného připojení. Klikněte na **Připojit**.
6. Zabezpečení Windows vás vyzve k zadání přihlašovacích údajů pro připojení k IP adresu primárního řadiče domény. Klikněte na tlačítko **použijte jiný účet**. Pro **uživatelské jméno**, typ **contoso\DomainAdmin**. Tento účet jste nakonfigurovali, když nastavíte uživatelské jméno pro správce v šabloně. Použijte složité heslo, které jste zvolili při konfiguraci šablony.
7. **Vzdálená plocha** možná by vás varovala, že vzdáleném počítači nelze ověřit z důvodu problémů s certifikátem zabezpečení. To se dozvíte, název certifikátu zabezpečení. Pokud jste postupovali podle tohoto kurzu, název je **sqlserver 0.contoso.com**. Klikněte na **Ano**.

Nyní jste připojeni přes RDP k virtuálnímu počítači SQL serveru. Otevřete SQL Server Management Studio, připojte se k výchozí instanci systému SQL Server a ověřte, že je nakonfigurovaná skupina dostupnosti.
