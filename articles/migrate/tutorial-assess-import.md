---
title: 'Posouzení serverů na základě importovaných dat serverů s využitím nástroje Azure Migrate: Hodnocení serverů'
description: 'Popisuje postup posouzení vhodnosti místních serverů k migraci do Azure s využitím nástroje Azure Migrate: Hodnocení serverů na základě importovaných dat.'
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: ff7e423063859a6cdc1a4362fb030c0deb75eb32
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658705"
---
# <a name="assess-servers-by-using-imported-data"></a>Posouzení serverů na základě importovaných dat

Tento článek popisuje postup posouzení místních serverů s využitím nástroje [Azure Migrate: Hodnocení serverů](migrate-services-overview.md#azure-migrate-server-assessment-tool) na základě importovaných metadat serverů ve formátu CSV. Tato metoda posouzení k vytvoření posouzení nevyžaduje nastavení zařízení Azure Migrate. Je užitečná v následujících případech:

- Chcete vytvořit rychlé počáteční posouzení před nasazením zařízení.
- Nemůžete ve vaší organizaci nasadit zařízení Azure Migrate.
- Nemůžete sdílet přihlašovací údaje umožňující přístup k místním serverům.
- Kvůli omezením zabezpečení nemůžete shromažďovat a odesílat data shromážděná zařízením do Azure. Můžete řídit, která data budete sdílet v importovaném souboru. Velká část těchto dat (například IP adresy) je navíc volitelná.

## <a name="before-you-start"></a>Než začnete

Mějte na paměti následující body:

- V jednom souboru CSV můžete přidat maximálně 20 000 serverů.
- Pomocí souboru CSV můžete do projektu Azure Migrate přidat až 20 000 serverů.
- Pomocí souboru CSV můžete do nástroje Hodnocení serverů nahrát informace o serverech vícekrát.
- Při vyhodnocování vhodnosti místního prostředí k migraci je užitečné shromáždit informace o aplikacích. Nástroj Hodnocení serverů však v současné době neprovádí posouzení na úrovni aplikací a při vytváření posouzení nebere aplikace v úvahu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavit projekt Azure Migrate.
> * Vyplnit do souboru CSV informace o serverech.
> * Importem tohoto souboru přidat informace o serverech do nástroje Hodnocení serverů.
> * Vytvořit a zkontrolovat posouzení.

> [!NOTE]
> Tyto kurzy demonstrují nejjednodušší způsob nasazení určitého scénáře, abyste mohli rychle nastavit testování konceptu. V těchto kurzech se v rámci možností používají jen výchozí možnosti a neuvádějí se všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v praktických průvodcích.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="set-azure-permissions-for-azure-migrate"></a>Nastavení oprávnění Azure pro službu Azure Migrate

Váš účet Azure musí mít oprávnění k vytvoření projektu Azure Migrate.

1. Na webu Azure Portal otevřete předplatné a vyberte **Řízení přístupu (IAM)** .
2. Na stránce **Zkontrolovat přístup** vyhledejte příslušný účet a vyberte ho, aby se zobrazila oprávnění.
3. Ujistěte se, že máte oprávnění **přispěvatele** nebo **vlastníka**.
    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného.
    - Pokud nejste vlastníkem předplatného, přidělte odpovídající roli ve spolupráci s vlastníkem.

## <a name="set-up-an-azure-migrate-project"></a>Nastavení projektu Azure Migrate

Nastavení nového projektu Azure Migrate:

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. Na stránce **Přehled** v části **Zjistit, posoudit a migrovat servery** vyberte **Posoudit a migrovat servery**.

    ![Zjištění a posouzení serverů](./media/tutorial-assess-import/assess-migrate.png)

4. V části **Začínáme** vyberte **Přidat nástroje**.
5. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.
6. V části **Podrobnosti o projektu** zadejte název projektu a zeměpisnou oblast, ve které chcete projekt vytvořit. Další informace najdete tady:

    - Projděte si podporované oblasti pro [veřejný cloud](migrate-support-matrix.md#supported-geographies-public-cloud) a [cloud pro státní správu](migrate-support-matrix.md#supported-geographies-azure-government).
    - Při spouštění migrace můžete vybrat jakoukoli cílovou oblast.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-import/migrate-project.png)

7. Vyberte **Další**.
8. V části **Vybrat nástroj pro posouzení** vyberte **Azure Migrate: Server Assessment** > **Další**.

    ![Vytvoření posouzení Azure Migrate](./media/tutorial-assess-import/assessment-tool.png)

9. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
10. V části **Kontrola a přidání nástrojů** zkontrolujte nastavení a potom vyberte **Přidat nástroje**.
11. Počkejte několik minut, než se projekt Azure Migrate nasadí. Pak budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.

## <a name="prepare-the-csv"></a>Příprava souboru CSV

Stáhněte si šablonu CSV a přidejte do ní informace o serverech.

### <a name="download-the-template"></a>Stažení šablony

1. V části **Cíle migrace** > **Servery** > **Azure Migrate: Hodnocení serverů** vyberte **Zjistit**.
2. Na stránce **Zjistit počítače** vyberte **Importovat pomocí souboru CSV**.
3. Vyberte **Stáhnout** a stáhněte si šablonu CSV. Případně si ji můžete [stáhnout přímo](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Stažení šablony CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Přidání informací o serverech

Shromážděte data serverů a přidejte je do souboru CSV.

- Data můžete shromáždit tak, že je exportujete z nástrojů, které používáte ke správě místních serverů, jako jsou VMware vSphere nebo vaše databáze CMDB (Configuration Management Database).
- Pokud si chcete projít ukázková data, stáhněte si náš [ukázkový soubor](https://go.microsoft.com/fwlink/?linkid=2108405).

Následující tabulka shrnuje pole v souboru, která se mají vyplnit:

**Název pole** | **Povinné** | **Podrobnosti**
--- | --- | ---
**Název serveru** | Yes | Doporučujeme zadat plně kvalifikovaný název domény (FQDN).
**IP adresa** | No | Adresa serveru
**Cores** | Yes | Počet jader procesoru přidělených serveru
**Memory (Paměť)** | Yes | Celková velikost paměti RAM v MB přidělené serveru
**Název operačního systému** | Yes | Operační systém serveru. <br/> Posouzení rozpoznává názvy operačních systémů uvedené v [tomto](#supported-operating-system-names) seznamu (nebo názvy, které je obsahují).
**Verze operačního systému** | No | Verze operačního systému serveru
**Architektura operačního systému** | No | Architektura operačního systému serveru. <br/> Platné hodnoty: x64, x86, amd64, 32bitová nebo 64bitová
**Počet disků** | No | Není potřeba, pokud zadáte podrobnosti o jednotlivých discích.
**Velikost disku 1**  | No | Maximální velikost disku v GB.<br/>Podrobnosti o dalších discích můžete přidat tak, že do šablony [přidáte sloupce](#add-multiple-disks). Můžete přidat až osm disků.
**Operace čtení z disku 1** | No | Operace čtení z disku za sekundu
**Operace zápisu na disk 1** | No | Operace zápisu na disk za sekundu
**Propustnost čtení z disku 1** | No | Data načtená z disku za sekundu v MB/s
**Propustnost zápisu na disk 1** | No | Data zapsaná na disk za sekundu v MB/s
**Procento využití procesoru** | No | Procento využitého procesoru
**Procento využití paměti** | No | Procento využité paměti RAM
**Celkový počet operací čtení z disků** | No | Celkový počet operací čtení ze všech disků za sekundu. <br/> Toto pole použijte, pokud nemůžete zadat data na úrovni disku. 
**Celkový počet operací zápisu na disky** | No | Celkový počet operací zápisu na všechny disky za sekundu. <br/> Toto pole použijte, pokud nemůžete zadat data na úrovni disku.
**Celková propustnost čtení z disků** | No | Data načtená ze všech disků v MB/s. <br/> Toto pole použijte, pokud nemůžete zadat data na úrovni disku. 
**Celková propustnost zápisu na disky** | No | Data zapsaná na všechny disky v MB/s. <br/> Toto pole použijte, pokud nemůžete zadat data na úrovni disku.
**Propustnost síťových vstupů** | No | Data přijatá serverem v MB/s
**Propustnost síťových výstupů** | No | Data odeslaná serverem v MB/s
**Typ firmwaru** | No | Firmware serveru. Možné hodnoty: BIOS nebo UEFI.
**Adresa MAC**| No | Adresa MAC serveru


### <a name="add-operating-systems"></a>Přidání operačních systémů

Posouzení rozpoznává konkrétní názvy operačních systémů. Všechny názvy, které zadáte, se musí přesně shodovat s některým z řetězců v [seznamu podporovaných názvů](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Přidání více disků

Šablona obsahuje výchozí pole pro první disk. Můžete přidat podobné sloupce až pro osm disků.

Například pokud chcete zadat všechna pole pro druhý disk, přidejte tyto sloupce:

- Velikost disku 2
- Operace čtení z disku 2
- Operace zápisu na disk 2
- Propustnost čtení z disku 2
- Propustnost zápisu na disk 2

Pokud nemůžete zadat data na úrovni disku, můžete pomocí následujících polí zadat údaje o výkonu disků na jednom serveru. Podrobnosti o jednotlivých polích najdete v [této](#add-server-information) části.
- Celkový počet operací čtení z disků
- Celkový počet operací zápisu na disky
- Celková propustnost čtení z disků
- Celková propustnost zápisu na disky

## <a name="import-the-server-information"></a>Import informací o serverech

Po přidání informací do šablony CSV importujte servery do nástroje Hodnocení serverů.

1. Ve službě Azure Migrate na stránce **Zjistit počítače** přejděte k dokončené šabloně.
2. Vyberte **Importovat**.
3. Zobrazí se stav importu.
    - Pokud se ve stavu zobrazí upozornění, můžete je vyřešit nebo pokračovat bez jejich vyřešení.
    - Pokud chcete zvýšit přesnost posouzení, zlepšete informace o serverech podle pokynů v upozorněních.
    - Pokud chcete zobrazit a vyřešit upozornění, vyberte **Stáhnout soubor CSV s podrobnostmi o upozorněních**. Tato operace stáhne soubor CSV s upozorněními. Zkontrolujte upozornění a podle potřeby vyřešte problémy.
    - Pokud se ve stavu zobrazí chyby a stav importu je **Selhání**, musíte tyto chyby opravit, abyste mohli pokračovat v importu:
        1. Stáhněte si soubor CSV, který teď obsahuje podrobnosti o chybách.
        1. Zkontrolujte tyto chyby a podle potřeby je opravte. 
        1. Znovu nahrajte upravený soubor.
4. Když je stav importu **Dokončeno**, znamená to, že se informace o serverech importovaly.

## <a name="update-server-information"></a>Aktualizace informací o serverech

Informace o serveru můžete aktualizovat opětovným importem dat o serveru s použitím stejného **názvu serveru**. Pole **Název serveru** nemůžete upravit. Odstraňování serverů se v současné době nepodporuje.

## <a name="verify-servers-in-the-portal"></a>Ověření serverů na portálu

Ověření zobrazování serverů na webu Azure Portal po zjišťování:

1. Otevřete řídicí panel služby Azure Migrate.
2. Na stránce **Azure Migrate – Servery** > **Azure Migrate: Hodnocení serverů** vyberte ikonu s počtem **zjištěných serverů**.
3. Vyberte kartu **Na základě importu**.

## <a name="set-up-and-run-an-assessment"></a>Nastavení a spuštění posouzení

Pomocí nástroje Hodnocení serverů můžete vytvořit dva typy posouzení.


**Typ posouzení** | **Podrobnosti**
--- | --- 
**Virtuální počítač Azure** | Posouzení vhodnosti místních serverů k migraci na virtuální počítače Azure. <br/><br/> Pomocí tohoto typu posouzení můžete posoudit vhodnost místních [virtuálních počítačů VMware](how-to-set-up-appliance-vmware.md), [virtuálních počítačů Hyper-V](how-to-set-up-appliance-hyper-v.md) a [fyzických serverů](how-to-set-up-appliance-physical.md) k migraci do Azure.
**Azure VMware Solution (AVS)** | Posouzení vhodnosti místních serverů k migraci do služby [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pomocí tohoto typu posouzení můžete posoudit vhodnost místních [virtuálních počítačů VMware](how-to-set-up-appliance-vmware.md) k migraci do služby Azure VMware Solution (AVS). [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)

### <a name="sizing-criteria"></a>Kritéria stanovení velikosti

Nástroj Hodnocení serverů nabízí dvě možnosti kritérií stanovení velikosti:

**Kritéria stanovení velikosti** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení, která poskytují doporučení na základě shromážděných údajů o výkonu | **Posouzení virtuálních počítačů Azure:** Doporučení velikosti virtuálních počítačů vychází z dat o využití procesoru a paměti.<br/><br/> Doporučení typu disků (disky HDD nebo SSD úrovně Standard nebo spravované disky úrovně Premium) vychází z IOPS a propustnosti místních disků.<br/><br/> **Posouzení služby Azure VMware Solution (AVS):** Doporučení uzlů AVS vychází z dat o využití procesoru a paměti.
**Jako v místním prostředí** | Posouzení, která k poskytování doporučení nepoužívají údaje o výkonu. | **Posouzení virtuálních počítačů Azure:** Doporučení velikosti virtuálních počítačů vychází z velikosti místních virtuálních počítačů.<br/><br> Doporučení typu disků vychází z výběru nastavení typu úložiště pro posouzení.<br/><br/> **Posouzení služby Azure VMware Solution (AVS):** Doporučení uzlů AVS vychází z velikosti místních virtuálních počítačů.


Spuštění posouzení:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření posouzení.
2. Na kartě **Servery** na dlaždici **Azure Migrate: Hodnocení serverů** vyberte **Posoudit**.

    ![Posouzení](./media/tutorial-assess-physical/assess.png)

3. V části **Posoudit servery** zadejte název posouzení a jako **typ posouzení** vyberte *Virtuální počítač Azure*, pokud chcete provést posouzení virtuálního počítače Azure, nebo *Azure VMware Solution (AVS)* , pokud chcete provést posouzení AVS.

    ![Základní informace o posouzení](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. V části **Zdroj zjišťování** vyberte **Počítače přidané importem do služby Azure Migrate**.

5. Pokud chcete zobrazit vlastnosti posouzení, vyberte **Zobrazit vše**.

    ![Vlastnosti posouzení](./media/tutorial-assess-physical/view-all.png)

6. Kliknutím na **Další** přejděte do části **Vybrat počítače, které se mají posoudit**. V části **Vybrat nebo vytvořit skupinu** vyberte **Vytvořit novou** a zadejte název skupiny. Skupina sdružuje jeden nebo více virtuálních počítačů pro posouzení.
7. V části **Přidat počítače do skupiny** vyberte servery, které chcete do skupiny přidat.
8. Kliknutím na **Další** přejděte do části **Zkontrolovat a vytvořit posouzení** a zkontrolujte podrobnosti o posouzení.
9. Kliknutím na **Vytvořit posouzení** vytvořte skupinu a pak spusťte posouzení.

    ![Vytvoření posouzení](./media/tutorial-assess-physical/assessment-create.png)

9. Po vytvoření můžete posouzení zobrazit v části **Servery** > **Azure Migrate: Hodnocení serverů** > **Posouzení**.
10. Vyberte **Exportovat posouzení** a stáhněte ho jako soubor aplikace Microsoft Excel.

Další podrobnosti o posouzení služby **Azure VMware Solution (AVS)** najdete [tady](how-to-create-azure-vmware-solution-assessment.md). 

## <a name="review-an-azure-vm-assessment"></a>Kontrola posouzení virtuálních počítačů Azure

Posouzení virtuálních počítačů Azure popisuje:

- **Připravenost pro Azure:** Určuje, jestli jsou servery vhodné k migraci do Azure.
- **Odhad měsíčních nákladů:** Odhadované měsíční náklady na výpočetní prostředky a úložiště při provozu serverů v Azure
- **Odhad měsíčních nákladů na úložiště:** Odhadované náklady na diskové úložiště po migraci

### <a name="view-an-assessment"></a>Zobrazení posouzení

1. V části **Cíle migrace** > **Servery** vyberte **Posouzení** v nástroji **Azure Migrate: Hodnocení serverů**.
2. Výběrem posouzení v části **Posouzení** ho otevřete.

    ![Souhrn posouzení](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti pro Azure

1. V části **Připravenost pro Azure** zjistěte, jestli jsou servery připravené na migraci do Azure.
2. Kontrola stavu:
    - **Připraveno pro Azure:** Azure Migrate pro virtuální počítače v posouzení doporučí velikost virtuálního počítače a odhady nákladů.
    - **Připraveno s podmínkami:** Zobrazí se problémy a doporučená náprava.
    - **Nepřipraveno pro Azure:** Zobrazí se problémy a doporučená náprava.
    - **Připravenost neznámá:** Azure Migrate kvůli problémům s dostupností dat nemůže posoudit připravenost.

3. Vyberte stav **připravenosti pro Azure**. Můžete zobrazit podrobnosti o připravenosti serveru a procházením hierarchie můžete zobrazit podrobnosti o serveru, včetně podrobností o výpočetních prostředcích, úložišti a nastavení sítě.

### <a name="review-cost-details"></a>Kontrola podrobností o nákladech

Toto zobrazení informuje o odhadovaných nákladech na výpočetní prostředky a úložiště při provozu virtuálních počítačů v Azure. Další možnosti:

- Projděte si měsíční náklady na výpočetní prostředky a úložiště. Náklady se agregují pro všechny servery v posuzované skupině.

    - Odhady nákladů vycházejí z doporučení velikosti počítače a z jeho disků a vlastností.
    - Zobrazí se odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů se vztahuje na provoz místních serverů jako virtuálních počítačů v modelu IaaS (infrastruktura jako služba). Hodnocení serverů se nezabývá náklady na modely PaaS (platforma jako služba) ani SaaS (software jako služba).

- Projděte si odhady měsíčních nákladů na úložiště. Toto zobrazení informuje o agregovaných nákladech na úložiště pro posuzovanou skupinu rozdělených mezi různé typy disků úložiště.
- Procházením hierarchie můžete zobrazit podrobnosti o konkrétních virtuálních počítačích.

> [!NOTE]
> K posouzením serverů importovaných do nástroje Hodnocení serverů pomocí souboru CSV se nepřiřazuje míra spolehlivosti.

## <a name="supported-operating-system-names"></a>Podporované názvy operačních systémů

Názvy operačních systémů zadané v souboru CSV se musí shodovat s názvy v tomto seznamu (nebo je musí obsahovat). To je nutné k tomu, aby posouzení rozpoznalo zadané názvy jako platné.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A–H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I–R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S–T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U–Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Importovali servery do nástroje Azure Migrate: Hodnocení serverů pomocí souboru CSV.
> * Vytvořili a zkontrolovali posouzení.

Teď [nasaďte zařízení](./migrate-appliance.md), abyste zvýšili přesnost posouzení, a sdružte servery do skupin pro účely podrobnějšího posouzení pomocí [analýzy závislostí](./concepts-dependency-visualization.md).
