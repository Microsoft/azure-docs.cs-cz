---
title: Vyhodnoťte servery pomocí importovaných dat serveru pomocí azure migrate server assessment
description: Popisuje, jak posoudit místní servery pro migraci do Azure s Azure Migrate Server Assessment pomocí importovaných dat.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 2a30222902fd8797908202562a04018209842af2
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115066"
---
# <a name="assess-servers-by-using-imported-data"></a>Vyhodnocení serverů pomocí importovaných dat

Tento článek vysvětluje, jak posoudit místní servery pomocí nástroje [Azure Migrate: Server Assessment,](migrate-services-overview.md#azure-migrate-server-assessment-tool) importem metadat serveru ve formátu CSV (csv) oddělených čárkami. Tato metoda hodnocení nevyžaduje, abyste nastavili zařízení Azure Migrate k vytvoření hodnocení. Je užitečné, pokud:

- Chcete vytvořit rychlé počáteční posouzení před nasazením zařízení.
- Zařízení Azure Migrate nelze nasadit ve vaší organizaci.
- Nelze sdílet přihlašovací údaje, které umožňují přístup k místním serverům.
- Omezení zabezpečení vám brání ve shromažďování a odesílání dat shromážděných zařízením do Azure. Data, která sdílíte v importovaném souboru, můžete ovládat. Velká část dat (například poskytnutí IP adres) je také nepovinná.

## <a name="before-you-start"></a>Než začnete

Uvědomte si tyto body:

- Do jednoho souboru CSV můžete přidat maximálně 20 000 serverů.
- Můžete přidat až 20 000 serverů v projektu Migrace Azure pomocí CSV.
- Informace o serveru můžete nahrát do vyhodnocení serveru vícekrát pomocí csv.
- Shromažďování informací o aplikaci je užitečné při vyhodnocování místního prostředí pro migraci. Posouzení serveru však aktuálně neprovádí hodnocení na úrovni aplikace nebo při vytváření hodnocení nebere v úvahu aplikace.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte projekt Migrace Azure.
> * Vyplňte soubor CSV informacemi o serveru.
> * Importujte soubor a přidejte informace o serveru do vyhodnocení serveru.
> * Vytvořte a zkontrolujte hodnocení.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář, takže můžete rychle nastavit doklad o konceptu. Kurzy používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v návodech k použití.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.

## <a name="set-azure-permissions-for-azure-migrate"></a>Nastavení oprávnění Azure pro migraci Azure

Váš účet Azure potřebuje oprávnění k vytvoření projektu Migrace Azure.

1. Na webu Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM).**
2. V **části Zkontrolovat přístup**vyhledejte příslušný účet a vyberte ho, chcete-li zobrazit oprávnění.
3. Ujistěte se, že máte oprávnění **přispěvatele** nebo **vlastníka.**
    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem předplatného.
    - Pokud nejste vlastníkem předplatného, spolupracujte s vlastníkem a přiřaďte roli.

## <a name="set-up-an-azure-migrate-project"></a>Nastavení projektu migrace Azure

Nastavení nového projektu Migrace Azure:

1. Na webu Azure Portal vyhledejte v **části Všechny služby** **azure migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **části Přehled**vyberte v části Zjišťování **vyhodnocovat a migrovat servery**vyberte **Posuzovat a migrovat servery**.

    ![Zjišťování a posuzování serverů](./media/tutorial-assess-import/assess-migrate.png)

4. V **možnosti Začínáme**vyberte **Přidat nástroje**.
5. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.
6. V **části PODROBNOSTI PROJEKTU**zadejte název projektu a zeměpisnou polohu, ve které chcete projekt vytvořit. Další informace najdete tady:

    - Zkontrolujte [podporované zeměpisné oblasti](migrate-support-matrix.md#supported-geographies). Zeměpisná oblast projektu slouží pouze k ukládání metadat shromážděných z místních virtuálních počítačů.
    - Při spouštění migrace můžete vybrat jakoukoli cílovou oblast.

    ![Vytvoření projektu migrace Azure](./media/tutorial-assess-import/migrate-project.png)

7. Vyberte **další**.
8. V **nástroji Pro výběr vyberte**možnost Migrace **Azure: Vyhodnocení serveru** > **Další**.

    ![Vytvoření vyhodnocení migrace Azure](./media/tutorial-assess-import/assessment-tool.png)

9. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
10. V **části Revize + přidání nástrojů**zkontrolujte nastavení a vyberte Přidat **nástroje**.
11. Počkejte několik minut, než se projekt Azure Migrate nasadí. Poté budete převedena na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.

## <a name="prepare-the-csv"></a>Připravte CSV

Stáhněte si šablonu CSV a přidejte do ní informace o serveru.

### <a name="download-the-template"></a>Stažení šablony

1. V **serverech cílů** > **migrace, které** > **Azure migruje: Vyhodnocení serveru**, vyberte **Zjistit**.
2. V **počítačích Discover**vyberte **Importovat pomocí csv**.
3. Chcete-li stáhnout šablonu CSV, vyberte **možnost Stáhnout.** Případně si jej můžete [stáhnout přímo](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Stáhnout šablonu CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Přidání informací o serveru

Shromážděte data serveru a přidejte je do souboru CSV.

- Chcete-li shromažďovat data, můžete je exportovat z nástrojů, které používáte pro místní správu serveru, jako je například VMware vSphere nebo databáze správy konfigurace (CMDB).
- Chcete-li zkontrolovat ukázková data, stáhněte si náš [ukázkový soubor](https://go.microsoft.com/fwlink/?linkid=2108405).

Následující tabulka shrnuje pole souborů, která chcete vyplnit:

**Název pole** | **Povinné** | **Podrobnosti**
--- | --- | ---
**Název serveru** | Ano | Doporučujeme zadat plně kvalifikovaný název domény (Plně kvalifikovaný název domény).
**IP adresa** | Ne | Adresa serveru.
**Cores** | Ano | Počet procesorových jader přidělených serveru.
**Paměti** | Ano | Celková paměť RAM v MB přidělená serveru.
**Název operačního serveru** | Ano | Operační systém serveru. <br/> Názvy operačních systémů, které odpovídají nebo obsahují názvy v [tomto](#supported-operating-system-names) seznamu, jsou rozpoznány hodnocením.
**Verze operačního systému** | Ne | Verze operačního systému serveru.
**Počet disků** | Ne | Není potřeba, pokud jsou k dispozici podrobnosti o jednotlivých disku.
**Velikost disku 1**  | Ne | Maximální velikost disku v GB.<br/>Přidáním sloupců do šablony můžete přidat podrobnosti o dalších [discích.](#add-multiple-disks) Můžete přidat až osm disků.
**Operace pro čtení disku 1** | Ne | Operace čtení disku za sekundu.
**Disk 1 zápis ops** | Ne | Operace zápisu disku za sekundu.
**Propustnost čtení disku 1** | Ne | Data čtení z disku za sekundu, v MB za sekundu.
**Propustnost zápisu disku 1** | Ne | Data zapsaná na disk za sekundu v MB za sekundu.
**Procento využití procesoru** | Ne | Procento použitého procesoru.
**Procento využití paměti** | Ne | Procento použité paměti RAM.
**Celkový počet operací čtení disků** | Ne | Operace čtení disku za sekundu.
**Celkový počet disků psát ops** | Ne | Operace zápisu disků za sekundu.
**Propustnost čtení celkový počet disků** | Ne | Data čtení z disku v MB za sekundu.
**Propustnost celkového počtu disků** | Ne | Data zapsaná na disk v MB za sekundu.
**Propustnost sítě** | Ne | Data přijatá serverem v MB za sekundu.
**Propustnost sítě** | Ne | Data přenášená serverem v MB za sekundu.
**Typ firmwaru** | Ne | Firmware serveru. Hodnoty mohou být "BIOS" nebo "UEFI".
**Adresa MAC**| Ne | Adresa MAC serveru.


### <a name="add-operating-systems"></a>Přidání operačních systémů

Assessment rozpozná konkrétní názvy operačních systémů. Každý zadaný název se musí přesně shodovat s jedním z řetězců v [seznamu podporovaných názvů](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Přidání více disků

Šablona poskytuje výchozí pole pro první disk. Můžete přidat podobné sloupce až pro osm disků.

Chcete-li například zadat všechna pole pro druhý disk, přidejte tyto sloupce:

- Velikost disku 2
- Disk 2 číst ops
- Disk 2 zápis ops
- Propustnost čtení disku 2
- Propustnost zápisu disku 2


## <a name="import-the-server-information"></a>Import informací o serveru

Po přidání informací do šablony CSV importujte servery do vyhodnocení serveru.

1. V Azure Migrate, v **počítačích Discover**, přejděte na dokončenou šablonu.
2. Vyberte **Importovat**.
3. Zobrazí se stav importu.
    - Pokud se ve stavu zobrazí upozornění, můžete je buď opravit, nebo pokračovat bez jejich adresování.
    - Chcete-li zlepšit přesnost hodnocení, vylepšete informace o serveru, jak je navrženo v upozorněních.
    - Chcete-li zobrazit a opravit upozornění, vyberte **položku Stáhnout podrobnosti upozornění . CSV**. Tato operace stáhne csv s upozorněním v ceně. Zkontrolujte upozornění a opravte problémy podle potřeby.
    - Pokud se ve stavu zobrazí chyby, takže stav importu se **nezdařil**, je třeba tyto chyby opravit, než budete moci pokračovat v importu:
        1. Stáhněte si csv, který nyní obsahuje podrobnosti o chybě.
        1. Podle potřeby zkontrolujte a řešte chyby. 
        1. Nahrajte upravený soubor znovu.
4. Po dokončení **je**stav importu importován informace o serveru.

## <a name="update-server-information"></a>Aktualizovat informace o serveru

Informace o serveru můžete aktualizovat znovu importem dat pro server se stejným **názvem serveru**. Pole **Název serveru** nelze změnit. Odstranění serverů není aktuálně podporováno.

## <a name="verify-servers-in-the-portal"></a>Ověření serverů na portálu

Ověření, že se servery po zjišťování zobrazují na webu Azure Portal:

1. Otevřete řídicí panel Migrace Azure.
2. Na stránce **Migrace Azure – servery, které** > **Azure migruje: Vyhodnocení serveru,** vyberte ikonu, která zobrazuje počet **zjištěných serverů**.
3. Vyberte kartu **Importovat na základě.**

## <a name="set-up-and-run-an-assessment"></a>Nastavení a spuštění hodnocení

Pomocí vyhodnocení serveru můžete vytvořit dva typy hodnocení.

**Typ posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Hodnocení založená na zadaných hodnotách výkonu a dat. | **Doporučená velikost virtuálního počítače:** Na základě dat o využití procesoru a paměti.<br/><br/> **Doporučený typ disku (standardní nebo prémiový spravovaný disk):** Na základě vstupu a výstupu za sekundu (IOPS) a propustnosti místních disků.
**Jako místní** | Hodnocení založená na místním dimenzování. | **Doporučená velikost virtuálního počítače**: Na základě zadané velikosti serveru.<br/><br> **Doporučený typ disku**: Na základě nastavení typu úložiště, které vyberete pro posouzení.

Spuštění hodnocení:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
2. Na kartě **Servery** vyberte na dlaždici **Azure Migrate: Server Assessment** možnost **Assess**.

    ![Posouzení](./media/tutorial-assess-physical/assess.png)

3. V **poli Posoudit servery**zadejte název hodnocení.
4. Ve **zdroji zjišťování**vyberte Počítače přidané **prostřednictvím importu do Migrace Azure**.
5. Chcete-li zkontrolovat vlastnosti hodnocení, vyberte možnost **Zobrazit vše.**

    ![Vlastnosti posouzení](./media/tutorial-assess-physical/view-all.png)

6. V **poli Vybrat nebo vytvořit skupinu**vyberte Vytvořit **nový**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních virtuálních byl v usa z hodnocení.
7. V **části Přidat počítače do skupiny**vyberte servery, které chcete přidat do skupiny.
8. Vyberte **Vytvořit hodnocení,** chcete-li vytvořit skupinu, a pak hodnocení spusťte.

    ![Vytvoření posouzení](./media/tutorial-assess-physical/assessment-create.png)

9. Po vytvoření hodnocení jej zobrazte v **části Servery, které** > **Azure migruje: Hodnocení hodnocení** > **serveru**.
10. Vyberte **Vyhodnocení exportu,** chcete-li jej stáhnout jako soubor aplikace Microsoft Excel.

## <a name="review-an-assessment"></a>Přezkoumat posouzení

Hodnocení popisuje:

- **Připravenost Azure**: Jestli jsou servery vhodné pro migraci do Azure.
- **Měsíční odhad nákladů**: Odhadované měsíční náklady na výpočetní výkon a úložiště pro spouštění serverů v Azure.
- Měsíční odhad nákladů na úložiště : Odhadované náklady na diskové úložiště po **migraci.**

### <a name="view-an-assessment"></a>Zobrazit hodnocení

1. V**oblasti Serverů** **cílů** > migrace vyberte **Hodnocení** v **Azure Migrate: Server Assessment**.
2. V **posudku**vyberte hodnocení, které ho otevřete.

    ![Shrnutí hodnocení](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti Azure

1. V **připravenosti Azure zjistěte,** jestli jsou servery připravené k migraci do Azure.
2. Zkontrolujte stav:
    - **Připraveno pro Azure**: Azure Migrate doporučuje odhady velikosti virtuálních počítače a nákladů pro virtuální počítače v hodnocení.
    - **Připraveno s podmínkami**: Zobrazuje problémy a navrhovanou nápravu.
    - **Není připraven pro Azure**: Zobrazuje problémy a navrhované nápravy.
    - **Připravenost neznámý**: Azure Migrate nelze posoudit připravenost, z důvodu problémů s dostupností dat.

3. Vyberte stav **připravenosti Azure.** Můžete zobrazit podrobnosti o připravenosti serveru a přejít k podrobnostem a zobrazit podrobnosti o serveru, včetně výpočetních, úložných a síťových nastavení.

### <a name="review-cost-details"></a>Zkontrolovat podrobnosti o nákladech

Toto zobrazení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro spouštění virtuálních počítačů v Azure. Můžete:

- Zkontrolujte měsíční náklady na výpočetní prostředky a úložiště. Náklady jsou agregovány pro všechny servery v posuzované skupině.

    - Odhady nákladů jsou založeny na doporučení velikosti pro počítač a jeho disky a vlastnosti.
    - Jsou zobrazeny odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů je pro spuštění místních serverů jako virtuálních počítačích infrastruktury jako služby (IaaS). Server Assessment nebere v úvahu platformu jako službu (PaaS) nebo software jako služba (SaaS) náklady.

- Zkontrolujte odhady měsíčních nákladů na úložiště. Toto zobrazení zobrazuje agregované náklady na úložiště pro odhadovanou skupinu, rozdělené mezi různé typy disků úložiště.
- Přechodem k podrobnostem zobrazíte podrobnosti o konkrétních virtuálních dětech.

> [!NOTE]
> Hodnocení spolehlivosti nejsou přiřazena hodnocení serverů importovaných do vyhodnocení serveru pomocí csv.

## <a name="supported-operating-system-names"></a>Podporované názvy operačních systémů

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
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
      Centos 4/5
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
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      Ms-dos
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
      Červený klobouk Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Systémy klidu eComStation 1<br/>
      Systémy klidu eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sluneční mikrosystémy Solaris 8<br/>
      Sluneční mikrosystémy Solaris 9
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
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      Společnost VMware ESXi 4<br/>
      Společnost VMware ESXi 5<br/>
      Společnost VMware ESXi 6
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
      Prahová hodnota systému Windows Server<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Importované servery do Migrace Azure: Vyhodnocení serveru pomocí CSV.
> * Vytvořil a přezkoumal hodnocení.

Nyní [nasaďte zařízení](./migrate-appliance.md) pro přesnější hodnocení a shromažďujte servery do skupin pro hlubší posouzení pomocí [analýzy závislostí](./concepts-dependency-visualization.md).
