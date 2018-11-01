---
title: Zjištění místních virtuálních počítačů VMware a posouzení vhodnosti jejich migrace do Azure pomocí služby Azure Migrate | Microsoft Docs
description: Toto téma popisuje, jak zjistit místní virtuální počítače VMware a posoudit vhodnost jejich migrace do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 18e1ecd4896277f0dd0dfc2ceac2185cbdd09b93
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241102"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Zjištění místních virtuálních počítačů VMware a posouzení vhodnosti jejich migrace do Azure

Služba [Azure Migrate](migrate-overview.md) posuzuje místní úlohy pro migraci do Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Jak vytvořit účet, který Azure Migrate použije ke zjištění místních virtuálních počítačů.
> * Jak vytvořit projekt Azure Migrate.
> * Jak nastavit místní virtuální počítač kolektoru ke zjištění a posouzení místních virtuálních počítačů VMware.
> * Jak seskupit virtuální počítače a posoudit, jestli jsou vhodné pro migraci.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

- **VMware:** Virtuální počítače, které chcete migrovat, musí být spravované přes vCenter Server verze 5.5, 6.0, nebo 6.5. Pro vytvoření virtuálního počítače kolektoru budete také potřebovat jednoho hostitele ESXi ve verzi 5.0 nebo vyšší.
- **Účet vCenter Serveru**: Abyste měli přístup k vCenter Serveru, potřebujete účet jen pro čtení. Azure Migrate ho použije ke zjištění místních virtuálních počítačů.
- **Oprávnění**: Na vCenter Serveru potřebujete oprávnění k vytvoření virtuálního počítače pomocí importu souboru ve formátu .OVA.
- **Nastavení statistiky**: Tento požadavek platí pouze pro model jednorázového zjišťování. Aby jednorázové zjišťování fungovalo, tak než začnete s nasazením, statistika pro vCenter Server by byla být nastavená na úrovni 3. Pokud bude nižší než na úrovni 3, posouzení bude fungovat, ale neshromáždí se údaje o výkonu úložišť a sítí. Kapacitní doporučení budou v tomto případě vycházet z údajů o výkonu procesoru a paměti a z údajů o konfiguraci disku a síťových adaptérů.

## <a name="create-an-account-for-vm-discovery"></a>Vytvoření účtu pro účely zjišťování virtuálních počítačů

Azure Migrate k automatickému zjišťování virtuálních počítačů pro účely posouzení potřebuje přístup k serverům VMware. Vytvořte účet VMware s následujícími vlastnostmi. Tento účet zadáte při nastavování služby Azure Migrate.

- Typ uživatele: Alespoň uživatel jen pro čtení
- Oprávnění: Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only
- Podrobnosti: Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v tomto datacentru.
- Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli Žádný přístup s objektem Rozšířit na podřízený objekt.


## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-a-project"></a>Vytvoření projektu

1. Na portálu Azure Portal klikněte na **Vytvořit prostředek**.
2. Vyhledejte **Azure Migrate** a ve výsledcích hledání vyberte službu **Azure Migrate**. Poté klikněte na **Vytvořit**.
3. Zadejte název projektu a předplatné Azure, do kterého spadá.
4. Vytvořte novou skupinu prostředků.
5. Zadejte umístění, ve kterém chcete projekt vytvořit, a pak klikněte na **Vytvořit**. Projekt Azure Migrate můžete vytvořit pouze v zeměpisné oblasti USA. Přesto ale můžete naplánovat migraci do libovolného cílového umístění Azure. Zeměpisné umístění vybrané pro tento projekt slouží jen k uložení metadat získaných z místních virtuálních počítačů.

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Azure Migrate vytvoří místní virtuální počítač, kterému se říká zařízení kolektoru. Tento virtuální počítač vyhledá místní virtuální počítače VMware a odešle jejich metadata do služby Azure Migrate. Pro nastavení zařízení kolektoru je nutné stáhnout soubor .OVA, importovat ho do místního vCenter Serveru a vytvořit tak virtuální počítač.

1. V projektu služby Azure Migrate klikněte na **Začínáme** > **Zjistit a posoudit** > **Zjistit počítače**.
2. V části **Zjistit počítače**, existují dvě možnosti k dispozici pro zařízení, kliknutím na tlačítko **Stáhnout** stáhnete odpovídající zařízení založené na dáváte přednost.

    a. **Jednorázové zjišťování:** Zařízení pro tento model komunikuje s vCenter Server, aby shromažďovalo metadata o virtuálních počítačích. Pro shromažďování dat o výkonu virtuálních počítačů závisí na historických datech o výkonu uložených ve vCenter Server a shromažďuje historii výkonu za poslední měsíc. V tomto modelu Azure Migrate shromažďuje průměrný čítač (oproti čítači ve špičce) pro jednotlivé metriky – [další informace](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected). Protože jde o jednorázové zjišťování, změny v místním prostředí se neprojeví, dokud nebude zjišťování dokončeno. Pokud chcete, aby se změny projevily, budete muset provést opakované zjišťování stejného prostředí stejného projektu.

    b. **Průběžné zjišťování:** Zařízení pro tento model průběžně profiluje místní prostředí pro shromažďování dat o využití v reálném čase pro každý virtuální počítač. V tomto modelu se shromažďují čítače ve špičce pro každou metriku (využití procesoru, využití paměti atd.). Tento model není závislý na nastavení statistiky vCenter Server pro shromažďování dat o výkonu. Kdykoli ze zařízení můžete zastavit průběžnou profilaci.

    Mějte na paměti, že zařízení shromažďuje data o výkonu pouze průběžně a nezjistí žádné změny konfigurace v místním prostředí (tj. přidání nebo odstranění virtuálního počítače, přidání disku atd.). Pokud dojde ke změně konfigurace v místním prostředí, následujícím způsobem můžete zajistit, že se změny projeví na portálu:

    1. Přidání položek (virtuální počítače, disky, jádra atd.): Pokud chcete, aby se tyto změny projevily na webu Azure Portal, můžete na zařízení zastavit zjišťování a pak ho spustit znovu. Tím se zajistí, že se změny aktualizují v projektu Azure Migrate.

    2. Odstranění virtuálních počítačů: Vzhledem ke způsobu, jakým je zařízení navržené, se odstranění virtuálních počítačů neprojeví ani v případě, že zastavíte a znovu spustíte zjišťování. Důvodem je, že se data z dalších zjišťování připojují ke starším zjišťováním, a nepřepisují se. V takovém případě můžete virtuální počítač na portálu jednoduše ignorovat tak, že ho odeberete ze své skupiny a přepočítáte posouzení.

    > [!NOTE]
    > Průběžné zjišťování funkce je ve verzi Preview. Doporučujeme používat tuto metodu, protože shromažďuje podrobná data o výkonu a ve výsledku poskytuje přesné určení správné velikosti.

3. V části **Kopírování přihlašovacích údajů projektu** zkopírujte ID a klíč projektu. Budete je potřebovat při konfiguraci kolektoru.

    ![Stažení souboru .OVA](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Než nasadíte soubor .OVA, zkontrolujte, jestli je bezpečný.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Vygenerovaná hodnota hash by měla odpovídat následujícímu nastavení.

#### <a name="one-time-discovery"></a>Jednorázové zjišťování

  Pro soubory OVA verze 1.0.9.15

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

  Pro soubory OVA verze 1.0.9.14

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
  SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
  SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  Pro soubory OVA verze 1.0.9.12

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | d0363e5d1b377a8eb08843cf034ac28a
  SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
  SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

  Pro soubory OVA verze 1.0.9.8:

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | b5d9f0caf15ca357ac0563468c2e6251
  SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
  SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff


  Pro soubory OVA verze 1.0.9.7:

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | d5b6a03701203ff556fa78694d6d7c35
  SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
  SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

#### <a name="continuous-discovery"></a>Průběžné zjišťování

  Pro soubory OVA verze 1.0.10.4

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

## <a name="create-the-collector-vm"></a>Vytvoření virtuálního počítače kolektoru

Importujte stažený soubor do vCenter Serveru.

1. V konzoli vSphere Client klikněte na **File** (Soubor) > **Deploy OVF Template** (Nasadit šablonu OVF).

    ![Nasazení šablony OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. V nabídce Deploy OVF Template Wizard (Průvodce nasazením šablony OVF) > **Source** (Zdroj) vyberte umístění souboru .OVA.
3. V části **Name** (Název) a **Location** (Umístění) zadejte popisný název virtuálního počítače kolektoru a objekt inventáře, ve kterém bude daný virtuální počítač hostovaný.
5. V části **Host/Cluster** (Hostitel/cluster) zadejte hostitele nebo cluster, na kterém se bude virtuální počítač kolektoru spouštět.
7. V části Storage (Úložiště) zadejte cílové úložiště pro virtuální počítač kolektoru.
8. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
9. V části **Network Mapping** (Mapování sítě) zadejte síť, ke které se bude virtuální počítač kolektoru připojovat. Aby mohla síť odesílat metadata do Azure, potřebuje připojení k internetu.
10. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).

## <a name="run-the-collector-to-discover-vms"></a>Spuštění kolektoru pro vyhledání virtuálních počítačů

1. V konzoli vSphere Client klikněte pravým tlačítkem na daný virtuální počítač a pak na **Open Console** (Otevřít konzolu).
2. Nastavte pro zařízení preferovaný jazyk, časové pásmo a heslo.
3. Na ploše klikněte na zástupce **Spustit kolektor**.
4. Klikněte na **Vyhledat aktualizace** v horním panelu uživatelského rozhraní kolektoru a ověřte, že používáte nejnovější verzi kolektoru. Pokud ne, můžete stáhnout balíček s nejnovějším upgradem z tohoto odkazu a kolektor aktualizovat.
5. Ve službě Azure Migrate Collector otevřete nabídku **Nastavit požadavky**.
    - Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - Kolektor zkontrolujte, jestli má virtuální počítač přístup k internetu.
    - Pokud má virtuální počítač přístup k internetu přes proxy server, klikněte na **Nastavení proxy**, zadejte adresu proxy serveru a nastavte naslouchající port. Pokud proxy server potřebuje přihlašovací údaje, zadejte je. Přečtěte si [další informace](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity) o požadavcích na internetové připojení a o seznamu adres URL, ke kterým má kolektor přístup.

    > [!NOTE]
    > Adresu proxy serveru je potřeba zadat ve formátu http://ProxyIPAddress nebo http://ProxyFQDN. Podporuje se jen proxy protokolu HTTP. Pokud máte záchytný proxy server, připojení k internetu může zpočátku selhat v případě, že jste nenaimportovali certifikát proxy serveru. [Další informace](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity-with-intercepting-proxy) o řešení tohoto problému importováním certifikátu proxy serveru jako důvěryhodného certifikátu na virtuální počítač kolektoru.

    - Kolektor zkontroluje, jestli je spuštěná služba kolektoru. Ta je ve výchozím nastavení nainstalovaná na virtuálním počítači kolektoru.
    - Stáhněte a nainstalujte VMware PowerCLI.

6. V části **Zadejte podrobnosti vCenter Serveru** udělejte toto:
    - Zadejte název (plně kvalifikovaný název domény) nebo IP adresu vCenter Serveru.
    - V části **Uživatelské jméno** a **Heslo** zadejte přihlašovací údaje k účtu pouze pro čtení, který kolektor použije ke zjištění virtuálních počítačů na vCenter Serveru.
    - V části **Rozsah kolekce** vyberte kolekci pro zjišťování virtuálních počítačů. Kolektor může vyhledat jen virtuální počítače v rámci zadaného rozsahu. Jako rozsah můžete vybrat konkrétní složku, datové centrum nebo cluster. Neměl by obsahovat víc než 1500 virtuálních počítačů. Přečtěte si [další informace](how-to-scale-assessment.md) o zjišťování větších prostředí.

7. V části **Zadejte projekt migrace** zadejte ID projektu služby Azure Migrate a klíč, který jste zkopírovali z portálu. Pokud jste ho nezkopírovali, na virtuálním počítači kolektoru otevřete Azure Portal. Na stránce **Přehled** projektu klikněte na **Zjistit počítače** a zkopírujte příslušné hodnoty.  
8. V části **Zobrazit průběh shromažďování** sledujete stav zjišťování. Přečtěte si [další informace](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected) o datech, která shromažďuje kolektor Azure Migrate.

> [!NOTE]
> Kolektor podporuje jako jazyk operačního systému a jazyk rozhraní kolektoru jen angličtinu (Spojené státy).
> Pokud změníte nastavení na počítači, který chcete posoudit, aktivujte znovu zjišťování ještě před spuštěním posouzení. V kolektoru k tomu použijte možnost **Spustit shromažďování znovu**. Jakmile se shromažďování dokončí, vyberte možnost **Přepočítat** pro posouzení na portálu. Zobrazíte tak aktualizované výsledky posouzení.



### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Pro jednorázové zjišťování doba zjišťování závisí na tom, kolik virtuálních počítačů vyhledáváte. Obvykle u 100 virtuálních počítačů po dokončení spuštění kolektoru trvá přibližně hodinu, než se dokončí konfigurace a shromažďování dat o výkonu. Okamžitě po dokončení zjišťování můžete vytvořit interní hodnocení (na základě výkonu a nebo na základě místa).

Z důvodu průběžného zjišťování (ve verzi preview), bude kolektor průběžně profilovat místní prostředí a bude posílat data o výkonu v hodinovém intervalu. Za hodinu po zahájení zjišťování můžete zkontrolovat počítače na portálu. Před vytvořením interního hodnocení založeného na výkonu virtuálních počítačů důrazně doporučujeme počkat aspoň jeden den.

1. V projektu migrace klikněte na **Spravovat** > **počítače**.
2. Zkontrolujte, jestli se virtuální počítače, které jste chtěli vyhledat, zobrazí na portálu.


## <a name="create-and-view-an-assessment"></a>Vytvoření a zobrazení posouzení

Jakmile vyhledáte požadované virtuální počítače, můžete je seskupit a posoudit, jestli jsou vhodné pro migraci.

1. Na stránce **Přehled** projektu klikněte na **Vytvořit posouzení**.
2. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.
3. Vytvořte skupinu a pojmenujte ji.
4. Vyberte virtuální počítače, které do ní chcete přidat.
5. Kliknutím na tlačítko **Vytvořit posouzení** vytvoříte skupinu a posouzení.
6. Jakmile bude posouzení hotové, zobrazíte ho v části **Přehled** > **Řídicí panel**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.

> [!NOTE]
> Před vytvořením interního hodnocení při průběžném zjišťování důrazně doporučujeme počkat aspoň jeden den po spuštění zjišťování. Pokud chcete aktualizovat existující interní hodnocení o nejnovější data o výkonu, můžete použít příkaz **Přepočítat** a hodnocení aktualizovat.

### <a name="assessment-details"></a>Podrobnosti o posouzení

Posouzení obsahuje informace o kompatibilitě místních virtuálních počítačů s Azure, správné velikosti virtuálního počítače pro provoz virtuálního počítače v Azure a odhadovaných měsíčních nákladech na Azure.

![Sestava posouzení](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Připravenost pro Azure

Zobrazení Připravenost pro Azure v posouzení zobrazuje stav připravenosti jednotlivých virtuálních počítačů. V závislosti na svých vlastnostech můžou mít jednotlivé virtuální počítače následující označení:
- Připraveno pro Azure
- Připraveno pro Azure s podmínkou
- Nepřipraveno pro Azure
- Připravenost neznámá

U virtuálních počítačů, které jsou pro migraci vhodné, doporučí Azure Migrate velikost virtuálního počítače v Azure. Velikost doporučená službou Azure Migrate vychází z kritéria určení velikosti zadaného ve vlastnostech posouzení. Pokud je kritérium velikosti založené na výkonu, doporučí se velikost zohledněním historie výkonu virtuálních počítačů (procesor a paměť) a disků (IOPS a propustnost). Pokud se používá kritérium velikosti „jako místní“, nebude Azure Migrate pro virtuální počítač a disky zohledňovat údaje o výkonu. Velikost virtuálního počítače v Azure se doporučí zjištěním velikosti místního virtuálního počítače a velikost disku se určí na základě typu úložiště určeného ve vlastnostech posouzení (výchozí jsou disky Premium). [Další informace](concepts-assessment-calculation.md) o určování velikosti službou Azure Migrate.

U virtuálních počítačů, které pro Azure nejsou připravené nebo jsou připravené s podmínkou, vysvětlí Azure Migrate problémy s připraveností a nabídne kroky pro odstranění problémů.

Virtuální počítače, u kterých Azure Migrate nemůže určit připravenost pro Azure (kvůli nedostatku dat), se označí značkou Připravenost neznámá.

Azure Migrate kromě určování připravenosti pro Azure a velikosti také navrhuje nástroje, které můžete použít k migraci virtuálních počítačů. To vyžaduje hlubší zkoumání místního prostředí. [Další informace](how-to-get-migration-tool.md) o provádění hlubšího zjišťování prostřednictvím instalace agentů v místních počítačích. Pokud na místních počítačích nejsou nainstalovaní agenti, navrhne se migrace metodou „lift and shift“ pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Pokud jsou na místních počítačích nainstalovaní agenti, Azure Migrate prozkoumá spuštěné procesy v rámci počítače a určí, jestli daný počítač hostuje databázi, nebo ne. Pokud počítač hostuje databázi, navrhne se služba [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview), jinak se jako nástroj pro migraci navrhne Azure Site Recovery.

  ![Posouzení připravenosti](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Odhad měsíčních nákladů

Toto zobrazení informuje o celkových nákladech na výpočetní kapacitu a úložiště, které s sebou nese provoz virtuálních počítačů v Azure. Také nabízí podrobné údaje o jednotlivých počítačích. Odhadované náklady se počítají s ohledem na službou Azure Migrate doporučené velikosti jednotlivých počítačů a jejich disků a také zohledňují vlastnosti posouzení.

> [!NOTE]
> Služba Azure Migrate odhaduje náklady, které by s sebou přinesla migrace místních virtuálních počítačů na virtuální počítače zajišťované službou Azure v modelu IaaS (infrastruktura jako služba). Azure Migrate se nezabývá náklady modelů PaaS (platforma jako služba) ani SaaS (software jako služba).

Odhadované měsíční náklady na výpočetní kapacitu a úložiště jsou agregované pro všechny virtuální počítače dané skupiny.

![Odhadované náklady na virtuální počítače](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Hodnocení spolehlivosti

Ke každému posouzení na základě výkonu ve službě Azure Migrate se přidruží hodnocení spolehlivosti v rozsahu od jedné do pěti hvězdiček (jedna hvězdička znamená nejnižší a pět hvězdiček nejvyšší spolehlivost). Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení. Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate. Hodnocení spolehlivosti se netýká místních posouzení.

K určení velikosti na základě výkonu potřebuje Azure Migrate data o využití procesoru a paměti virtuálního počítače. Pro každý disk připojený k virtuálnímu počítači navíc potřebuje IOPS a propustnost disku. Podobně u každého síťového adaptéru připojeného k virtuálnímu počítači potřebuje Azure Migrate k určení velikosti na základě výkonu informace o síťových vstupech a výstupech. Pokud některá z výše uvedených čísel o využití nejsou v systému vCenter Server k dispozici, doporučení velikosti provedené službou Azure Migrate nemusí být spolehlivé. Tady je poskytnuté hodnocení spolehlivosti posouzení v závislosti na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček

Posouzení nemusí mít k dispozici všechny datové body z některého z následujících důvodů:

**Jednorázové zjišťování**

- Statistika v systému vCenter Server není nastavena na úroveň 3. Vzhledem k tomu, že model jednorázového zjišťování závisí na statiscice nastavení VCenter Serveru, pokud je nastavení statistiky v systému vCenter Server nižší než úroveň 3, data o výkonu disku a sítě se ze systému vCenter Server neshromažďují. V takovém případě není doporučení služby Azure Migrate týkající se disku a sítě založené na využití. Bez zohlednění vstupně-výstupních operacích za sekundu a propustnosti disku nemůže Azure Migrate určit, jestli disk bude v Azure potřebovat disk Premium, a proto v tomto případě Azure Migrate doporučí pro všechny disky použít disky Standard.
- Nastavení statistiky v systému vCenter Server bylo před zahájením zjišťování nastavené na úroveň 3 po kratší dobu. Zvažme například scénář, kdy dnes změníte nastavení statistiky na úroveň 3 a zítra (za 24 hodin) zahájíte zjišťování pomocí zařízení kolektoru. Pokud vytváříte posouzení za jeden den, máte všechny datové body a hodnocení spolehlivosti posouzení bude 5 hvězdiček. Pokud však změníte dobu trvání výkonu ve vlastnostech posouzení na jeden měsíc, hodnocení spolehlivosti se sníží, protože data o výkonu disku a sítě za poslední měsíc nebudou k dispozici. Pokud chcete zohlednit data o výkonu za poslední měsíc, doporučujeme ponechat nastavení statistiky systému vCenter Server na úrovni 3 po dobu jednoho měsíce před zahájením zjišťování.

**Průběžné zjišťování**

- Neprofilovali jste své prostředí po dobu trvání, pro kterou vytváříte interní hodnocení. Například pokud vytváříte interní hodnocení s dobou výkonu nastavenou na 1 den, budete muset počkat aspoň jeden den po spuštění zjišťování, aby se shromáždily všechny datové body.

**Obecné důvody**  

- Během období, pro které se posouzení počítá, se několik virtuálních počítačů vypnulo. Pokud po nějakou dobu byly některé virtuální počítače vypnuté, nebudeme schopni za toto období shromáždit data o výkonu.
- Během období, pro které se posouzení počítá, se vytvořilo několik virtuálních počítačů. Například pokud vytváříte posouzení historie výkonu za poslední měsíc, ale před týdnem se v prostředí vytvořilo několik virtuálních počítačů. V takových případech nebude k dispozici historie výkonu nových virtuálních počítačů za celé období.

> [!NOTE]
> Pokud je u modelu jednorázového zjišťování hodnocení spolehlivosti nějakého posouzení nižší než 4 hvězdičky, doporučujeme změnit nastavení statistiky systému vCenter Server na úroveň 3, počkat po dobu, kterou chcete v posouzení zohlednit (1 den, 1 týden, 1 měsíc), a pak provést zjišťování a posouzení. U modelu průběžného zjišťování počkejte aspoň jeden den na profilování prostředí zařízením a potom interní hodnocení nechte *Přepočítat*. Pokud to není možné, určení velikosti na základě výkonu nemusí být spolehlivé a doporučuje se změnou vlastností posouzení přepnout na *určování stejné velikosti jako v místním prostředí*.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si](how-to-modify-assessment.md), jak můžete přizpůsobit posouzení na základě svých požadavků.
- Zjistěte, jak vytvořit vysoce spolehlivé skupiny posouzení pomocí [mapování závislosti počítačů](how-to-create-group-machine-dependencies.md).
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
- [Přečtěte si](how-to-scale-assessment.md), jak zjistit a posoudit virtuální počítače v rozsáhlém prostředí VMware.
- [Přečtěte si další informace](resources-faq.md) o nejčastějších dotazech k Azure Migrate
