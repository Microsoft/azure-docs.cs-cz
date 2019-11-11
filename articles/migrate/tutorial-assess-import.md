---
title: Vyhodnocování serverů pomocí importovaných dat serveru pomocí posouzení serveru Azure Migrate
description: Popisuje, jak vyhodnotit místní servery pro migraci do Azure pomocí Azure Migrateho posouzení serveru pomocí importovaných dat.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 856f7f7735435579ac14918ee8026f27b222773e
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715513"
---
# <a name="assess-servers-using-imported-data"></a>Vyhodnocování serverů pomocí importovaných dat

> [!NOTE]
> Pokud tuto funkci na portále Azure Migrate ještě nevidíte, počkejte prosím. Zobrazí se za příští týden.

Tento článek vysvětluje, jak vyhodnotit místní servery s [Azure Migrate: posouzení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool)pomocí importu metadat serveru pomocí CSV. Pomocí této metody vyhodnocení není nutné nastavovat zařízení Azure Migrate, abyste mohli vytvořit posouzení. To je užitečné v případě, že: 

- Před nasazením zařízení chcete vytvořit rychlé počáteční posouzení.
- Zařízení Azure Migrate ve vaší organizaci nemůžete nasadit.
- Nemůžete sdílet přihlašovací údaje, které povolují přístup k místním serverům.
- Omezení zabezpečení brání v shromažďování a odesílání dat shromážděných zařízením do Azure. Pomocí importovaného souboru můžete řídit data, která sdílíte, a celá data (například poskytování IP adres) jsou volitelná.


## <a name="before-you-start"></a>Než začnete

Poznámky:

- V jednom souboru CSV můžete přidat maximálně 20000 serverů.
- Do Azure Migrate projektu můžete přidat až 20000 serverů pomocí CSV.
- Můžete nahrát informace o serveru pomocí souboru CSV vícekrát Azure Migrate posouzení serveru.
- I když shromažďování informací o aplikaci je užitečné při vyhodnocování místního prostředí pro migraci, Azure Migrate posouzení serveru aktuálně neprovede hodnocení na úrovni aplikace a nebere v úvahu aplikace, když vytváření posouzení.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte Azure Migrate projekt.
> * Do souboru CSV zadejte informace o serveru.
> * Importujte soubor a přidejte informace o serveru do posouzení serveru Azure Migrate.
> * Vytvoření a kontrola posouzení.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v článcích s postupy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="set-azure-permissions-for-azure-migrate"></a>Nastavení oprávnění Azure pro Azure Migrate 

Váš účet Azure potřebuje oprávnění k vytvoření projektu Azure Migrate.

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.


## <a name="set-up-an-azure-migrate-project"></a>Nastavení Azure Migrateho projektu

Následujícím způsobem nastavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. Na stránce **Přehled** v části **Zjistit, posoudit a migrovat servery** klikněte na **Posoudit a migrovat servery**.

    ![Zjišťování a vyhodnocení serverů](./media/tutorial-assess-import/assess-migrate.png)

4. V části **Začínáme** klikněte na **Přidat nástroje**.
5. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.     
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt.

    - [Kontrola](migrate-support-matrix.md#supported-geographies) podporovaných geografických oblastí. Zeměpisná oblast projektu slouží pouze k ukládání metadat shromážděných z místních virtuálních počítačů.
    - Při spouštění migrace můžete vybrat jakoukoli cílovou oblast.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-import/migrate-project.png)


7. Klikněte na **Další**.
8. V **nástroji vybrat nástroj pro posouzení**vyberte **Azure Migrate: posouzení serveru** > **Další**.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-import/assessment-tool.png)

9. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
10. V části **Kontrola a přidání nástrojů** zkontrolujte nastavení a klikněte na **Přidat nástroje**.
11. Počkejte několik minut, než se projekt Azure Migrate nasadí. Budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.


## <a name="prepare-the-csv"></a>Příprava sdíleného svazku clusteru

Stáhněte šablonu sdíleného svazku clusteru a přidejte do ní informace o serveru.


### <a name="download-the-template"></a>Stažení šablony

1. V ** > ** **cíli migrace** > **Azure Migrate: posouzení serveru**, klikněte na **zjistit**.
2. V možnosti **zjistit počítače**vyberte **importovat pomocí. Sdílený svazek clusteru**.
3. Kliknutím na **Stáhnout** Stáhněte. Šablona sdíleného svazku clusteru. Případně si ho můžete [stáhnout přímo](https://go.microsoft.com/fwlink/?linkid=2108404).

    ![Stáhnout. Šablona sdíleného svazku clusteru](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>Přidat informace o serveru

Shromážděte data serveru a přidejte je do souboru CSV.

- Pokud chcete shromažďovat data, můžete je exportovat z nástrojů, které používáte pro správu místního serveru, jako je například VMware vSphere nebo vaše databáze správy konfigurace (CMDB).
- Pokud chcete zkontrolovat ukázková data, Stáhněte si náš [ukázkový soubor](https://go.microsoft.com/fwlink/?linkid=2108405).


Následující tabulka shrnuje pole souborů k vyplnění.

**Název pole** | **Závaznou** | **Podrobnosti**
--- | --- | ---
**Název serveru** | Ano | Doporučujeme zadat plně kvalifikovaný název domény. 
**IP adresa** | Ne | Adresa serveru.
**Počet jader** | Ano | Počet jader procesoru přidělených serveru.
**Rezident** | Ano | Celková velikost paměti RAM (MB) přidělená serveru.
**Název operačního systému** | Ano | Serverový operační systém.
**Verze operačního systému** | Ne | Verze operačního systému serveru.
**Počet disků** | Ne | Není nutné, pokud jsou k dispozici podrobnosti o jednotlivých discích.
**Velikost disku 1**  | Ne | Maximální velikost disku (GB)<br/> [Přidáním sloupců](#add-multiple-disks) do šablony můžete přidat podrobnosti o dalších discích. Můžete přidat až osm disků.
**Disk 1 operace čtení** | Ne | Operace čtení z disku za sekundu
**Operace zápisu na disk 1** | Ne | Operace zápisu na disk za sekundu
**Propustnost čtení disku 1** | Ne | Data načtená z disku za sekundu v MB za sekundu.
**Propustnost zápisu disku 1** | Ne | Data zapsaná na disk za sekundu v MB za sekundu.
**Procento využití procesoru** | Ne | Procento využití procesoru.
**Procento využití paměti** | Ne | Procento využití paměti RAM.
**Operace čtení z celkového počtu disků** | Ne | Operace čtení z disku za sekundu
**Operace zápisu z celkového počtu disků** | Ne | Operace zápisu na disk za sekundu
**Propustnost čtení celkem disků** | Ne | Data načtená z disku v MB za sekundu.
**Propustnost zápisu celkem disků** | Ne | Data zapsaná na disk v MB za sekundu.
**Síťové vstupy propustnost** | Ne | Data přijatá serverem v MB za sekundu.
**Síťové výstupy propustnost** | Ne | Data přenášená serverem v MB za sekundu.
**Typ firmwaru** | Ne | Firmware serveru. Hodnoty mohou být "BIOS" nebo "UEFI"
**Typ serveru** | Ne | Hodnoty můžou být fyzické nebo virtuální.
**Hypervisoru** | Ne | Hypervisor, na kterém je spuštěný počítač. <br/> Hodnoty mohou být "VMware", "Hyper-V", "Xen", "AWS", "GCP" nebo "jiné".
**Číslo verze hypervisoru** | Ne | Verze hypervisoru
**ID virtuálního počítače** | Ne | Identifikátor virtuálního počítače Toto je **InstanceUUid** pro virtuální počítač VMware VCENTER nebo **ID virtuálního počítače Hyper-v** pro Hyper-v.
**ID nástroje Virtual Machine Manager** | Ne | Toto je **InstanceUUid** pro VMware vCenter. Nepotřebujete pro Hyper-V.
**Adresa MAC**| Ne | Adresa MAC serveru.
**ID SYSTÉMU BIOS** | Ne | ID systému BIOS serveru.
**ID vlastního serveru**| Ne | Místní jedinečná ID serveru v místním prostředí. <br/> Užitečné pro sledování importovaného serveru podle místního ID. 
**Název aplikace 1** | Ne | Názvy úloh, které běží na serveru.<br/> [Přidáním sloupců](#add-multiple-applications) do šablony můžete přidat podrobnosti pro další aplikace. Můžete přidat až pět aplikací.
**Typ aplikace 1** | Ne | Typ úloh spuštěných na serveru
**Verze aplikace 1** | Ne | Verze úloh spuštěných na serveru.
**Vypršení platnosti licence pro aplikaci 1** | Ne | Vyprší platnost licence pro úlohu (Pokud je k dispozici).
**Organizační jednotka** | Ne | Organizační jednotka, do které server patří.
**Vlastník firmy** | Ne | Vlastník obchodní jednotky.
**Název obchodní aplikace** | Ne | Název aplikace, ke které patří aplikace
**Umístění** | Ne | Datacenter, ve kterém se server nachází.
**Datum vyřazení z provozu serveru** | Ne | Datum vyřazení fyzického serveru nebo základního fyzického serveru virtuálního serveru z provozu

### <a name="add-operating-systems"></a>Přidat operační systémy

Posouzení rozpoznává konkrétní názvy operačních systémů. Libovolný název operačního systému, který zadáte, musí odpovídat jedné z možností v seznamu [podporovaných názvů](#supported-operating-system-names) .


### <a name="add-multiple-disks"></a>Přidat více disků

Šablona poskytuje výchozí pole pro první disk.  Podobné sloupce můžete přidat až pro 8 disků. 

Pokud například chcete zadat všechna pole pro druhý disk, přidejte sloupce:

Disk 2 velikost disk 2 přečtení operace disk 2 pro zápis operace zápisu na disk 2 čtení propustnosti disk 2 – propustnost zápisu

Volitelně můžete přidat konkrétní pole pouze pro disk.


### <a name="add-multiple-applications"></a>Přidání více aplikací

Šablona poskytuje pole pro jednu aplikaci. Podobné sloupce můžete přidat až do pěti aplikací.  

Pokud například chcete zadat všechna pole pro druhou aplikaci, přidejte sloupce:

Aplikace 2 – název aplikace 2 typ verze Application 2 verze 2 platnost licence


Volitelně můžete přidat konkrétní pole pouze pro aplikaci.

> [!NOTE]
> Informace o aplikaci jsou užitečné při vyhodnocování vašeho místního prostředí pro migraci. Posouzení Azure Migrate serveru teď ale neprovede hodnocení na úrovni aplikace a při vytváření posouzení nebere v úvahu aplikace.


## <a name="upload-the-server-information"></a>Odeslat informace o serveru

Po přidání informací do šablony sdíleného svazku clusteru importujte servery do Azure Migrate: posouzení serveru.

1. V Azure Migrate > Vyhledat **počítače**, přejděte k vyplněné šabloně.
2. Klikněte na **Importovat**.
3. Zobrazí se stav importu. 
    - Pokud se ve stavu zobrazí upozornění, můžete je buď opravit, nebo pokračovat bez jejich adresování.
    - Vylepšení informací o serveru, jak je navrženo v upozorněních, vylepšuje přesnost posouzení.
    - Chcete-li zobrazit a opravit upozornění, pokud se zobrazí, klikněte na tlačítko **Stáhnout podrobnosti upozornění. Sdílený svazek clusteru**. Tím se stáhne sdílený svazek clusteru s přidanými upozorněními. Můžete zkontrolovat upozornění a opravit problémy podle potřeby. 
    Pokud se ve stavu objeví chyby (stav importu se **nezdařil**), je nutné je opravit, aby bylo možné pokračovat v importu. Provedete to tak, že si stáhnete sdílený svazek clusteru, který má teď přidané podrobnosti o chybě. Zkontrolujte a podle potřeby vyřešte chyby. Pak znovu nahrajte změněný soubor.
4. Po **dokončení**importu se informace o serveru importují.


> [!NOTE]
> Chcete-li aktualizovat informace o serveru nahraných na Azure Migrate, odešlete znovu data pro server pomocí stejného **názvu serveru**. Všimněte si, že pole **název serveru** se po importování šablony nedá změnit. Odstraňování serverů není momentálně podporované.

## <a name="updating-server-information"></a>Aktualizace informací o serveru

Informace o serveru můžete aktualizovat tak, že znovu nahrajete data pro server se stejným **názvem serveru**. Pole **název serveru** nemůžete změnit. 

Odstraňování serverů se v tuto chvíli nepodporuje.

### <a name="verify-servers-in-the-portal"></a>Ověřit servery na portálu

Po zjištění můžete ověřit, že se servery zobrazují v Azure Portal.

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate-servery** > **Azure Migrate: na stránce posouzení serveru** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.
3. Klikněte na kartu **Import na základě** .

## <a name="set-up-an-assessment"></a>Nastavení posouzení

Existují dva typy hodnocení, které můžete vytvořit pomocí Azure Migrate: posouzení serveru.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě zadaných hodnot dat výkonu | **Doporučená velikost virtuálního počítače**: na základě dat využití procesoru a paměti.<br/><br/> **Doporučený typ disku (spravovaný disk Standard nebo Premium)** : na základě vstupně-výstupních operací a propustnosti místních disků.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost virtuálního počítače**: na základě zadané velikosti serveru<br/><br> **Doporučený typ disku**: na základě nastavení typu úložiště, které jste vybrali pro posouzení.


### <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
2. Na kartě **servery** na dlaždici **Azure Migrate: vyhodnocování serveru** klikněte na možnost **vyhodnotit**.

    ![Posouzení](./media/tutorial-assess-physical/assess.png)

2. V poli **vyhodnotit servery**zadejte název posouzení.
3. Ve **zdroji zjišťování**vyberte **počítače přidané prostřednictvím importu do Azure Migrate**
3. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/tutorial-assess-physical/view-all.png)

3. V **Vyberte nebo vytvořte skupinu**vyberte **vytvořit novou**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních počítačů dohromady pro posouzení.
4. V části **přidat počítače do skupiny**vyberte servery, které chcete přidat do skupiny.
5. Kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

    ![Vytvoření posouzení](./media/tutorial-assess-physical/assessment-create.png)

6. Po vytvoření posouzení ho zobrazte na stránce **servery** > **Azure Migrate: posouzení serveru** > **posouzení**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.



## <a name="review-an-assessment"></a>Kontrola posouzení

Posouzení popisuje:

- **Připravenost na Azure**: jestli jsou servery vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz serverů v Azure.
- **Odhad měsíčních nákladů na úložiště**: Odhadované náklady na diskové úložiště po migraci.

### <a name="view-an-assessment"></a>Zobrazit posouzení

1. V ** >  ** **cíli migrace** klikněte na **posouzení** v **Azure Migrate: posouzení serveru**.
2. V **posouzení**klikněte na posouzení a otevřete ho.

    ![Souhrn posouzení](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti na Azure

1. V části **připravenost na Azure**ověřte, jestli jsou servery připravené na migraci do Azure.
2. Zkontrolujte stav:
    - **Připraveno pro Azure**: Azure Migrate doporučuje velikost virtuálního počítače a odhad nákladů pro virtuální počítače ve vyhodnocování.
    - **Připraveno s podmínkami**: zobrazuje problémy a navrhovanou nápravu.
    - **Nepřipraveno pro Azure**: zobrazuje problémy a navrhovanou nápravu.
    - **Připravenost neznámá**: používá se, když Azure Migrate nedokáže vyhodnotit připravenost kvůli problémům s dostupností dat.

2. Klikněte na stav **připravenosti na Azure** . Můžete zobrazit podrobnosti o připravenosti serveru a přejít k podrobnostem a zobrazit podrobnosti o serveru, včetně výpočetních prostředků, úložiště a nastavení sítě.

### <a name="review-cost-details"></a>Podrobnosti o kontrole nákladů

Toto zobrazení ukazuje odhadované náklady na výpočetní prostředky a úložiště pro provozování virtuálních počítačů v Azure.

1. Projděte si měsíční náklady na výpočetní prostředky a úložiště. Náklady se sčítají pro všechny servery v hodnocené skupině.

    - Odhad nákladů vychází z doporučení na velikost počítače a jeho disků a vlastností.
    - Zobrazí se Odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů slouží ke spuštění místních serverů jako virtuálních počítačů IaaS. Posouzení Azure Migrate serveru nebere v úvahu náklady na PaaS nebo SaaS.

2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení ukazuje agregované náklady na úložiště pro vyhodnocenou skupinu rozdělené přes různé typy úložných disků.
3. Můžete přejít k podrobnostem a zobrazit podrobnosti pro konkrétní virtuální počítače.

> [!NOTE]
> Hodnocení spolehlivosti není přiřazeno k posouzení serverů importovaných do Azure Migrateho vyhodnocení serveru pomocí sdíleného svazku clusteru.


## <a name="supported-operating-system-names"></a>Podporované názvy operačních systémů

Název | Název
--- | ---
**A-H** | 
Apple Mac OS X 10 | Asianux 3<br/>Asianux 4<br/>Asianux 5
CentOS<br/>CentOS 4/5 | CoreOS Linux 
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD 
**I-R** | 
IBM OS/2 | systémem |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11 
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | 
**S-T** | 
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7 | Serenity systémy eComStation 1<br/>Serenity systémy eComStation 2
Systém Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** | 
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Systém Windows 2000<br/>Systém Windows 3<br/>Windows 7<br/>Windows 8<br/>Systém Windows 95<br/>Systém Windows 98<br/>Systém Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Prahová hodnota pro Windows Server<br/>Windows Vista<br/>Webový server Windows 2008 R2<br/>Systém Windows XP Professional
    

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importované servery do Azure Migrate: posouzení serveru pomocí sdíleného svazku clusteru.
> * Vytvoření a přezkoumání posouzení

Nyní [Nasaďte zařízení](./migrate-appliance.md) pro přesnější posouzení a Shromážděte servery dohromady do skupin pro hlubší hodnocení pomocí [analýzy závislostí](./concepts-dependency-visualization.md).
