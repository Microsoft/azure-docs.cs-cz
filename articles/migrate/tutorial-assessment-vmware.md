---
title: "Zjištění místních virtuálních počítačů VMware a posouzení vhodnosti jejich migrace do Azure pomocí služby Azure Migrate | Microsoft Docs"
description: "Toto téma popisuje, jak zjistit místní virtuální počítače VMware a posoudit vhodnost jejich migrace do Azure pomocí služby Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bbd08637894c43c543aeb8236f515e5ed9c5fc19
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
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
 
> [!NOTE]
> Podpora Hyper-V se plánuje a bude brzy povolená. 

- **Účet vCenter Serveru**: Abyste měli přístup k vCenter Serveru, potřebujete účet jen pro čtení. Azure Migrate ho použije ke zjištění místních virtuálních počítačů.
- **Oprávnění**: Na vCenter Serveru potřebujete oprávnění k vytvoření virtuálního počítače pomocí importu souboru ve formátu .OVA. 
- **Nastavení statistiky**: Než začnete s nasazením, statistika pro vCenter Server by byla být nastavená na úrovni 3. Pokud bude nižší než na úrovni 3, posouzení bude fungovat, ale neshromáždí se údaje o výkonu úložišť a sítí. Kapacitní doporučení budou v tomto případě vycházet z údajů o výkonu procesoru a paměti a z údajů o konfiguraci disku a síťových adaptérů. 

## <a name="create-an-account-for-vm-discovery"></a>Vytvoření účtu pro účely zjišťování virtuálních počítačů

Azure Migrate k automatickému zjišťování virtuálních počítačů pro účely posouzení potřebuje přístup k serverům VMware. Vytvořte účet VMware s následujícími vlastnostmi. Tento účet zadáte při nastavování služby Azure Migrate.

- Typ uživatele: Alespoň uživatel jen pro čtení
- Oprávnění: Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only
- Podrobnosti: Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v tomto datacentru.
- Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli Žádný přístup s objektem Rozšířit na podřízený objekt.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

## <a name="create-a-project"></a>Vytvoření projektu

1. Na portálu Azure Portal klikněte na **Vytvořit prostředek**.
2. Vyhledejte **Azure Migrate** a ve výsledcích hledání vyberte službu **Azure Migrate**. Poté klikněte na **Vytvořit**.
3. Zadejte název projektu a předplatné Azure, do kterého spadá.
4. Vytvořte novou skupinu prostředků.
5. Zadejte umístění, ve kterém chcete projekt vytvořit, a pak klikněte na **Vytvořit**. Projekt Azure Migrate můžete vytvořit pouze v oblasti Střed USA – západ nebo USA – východ. Přesto ale můžete naplánovat migraci do libovolného cílového umístění Azure. Umístění vybrané pro tento projekt slouží jen k uložení metadat získaných z místních virtuálních počítačů. 

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Azure Migrate vytvoří místní virtuální počítač, kterému se říká zařízení kolektoru. Tento virtuální počítač vyhledá místní virtuální počítače VMware a odešle jejich metadata do služby Azure Migrate. Pro nastavení zařízení kolektoru je nutné stáhnout soubor .OVA, importovat ho do místního vCenter Serveru a vytvořit tak virtuální počítač.

1. V projektu služby Azure Migrate klikněte na **Začínáme** > **Zjistit a posoudit** > **Zjistit počítače**.
2. V nabídce **Zjistit počítače** klikněte na **Stáhnout**. Tím stáhnete soubor .OVA.
3. V části **Kopírování přihlašovacích údajů projektu** zkopírujte ID a klíč projektu. Budete je potřebovat při konfiguraci kolektoru.

    ![Stažení souboru .OVA](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Než nasadíte soubor .OVA, zkontrolujte, jestli je bezpečný.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Vygenerovaná hodnota hash by měla odpovídat následujícímu nastavení.

    Pro soubory OVA verze 1.0.9.5:

    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  
    
    Pro soubory OVA verze 1.0.9.2:

    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  
    
    Pro soubory OVA verze 1.0.8.59:

    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | 71139e24a532ca67669260b3062c3dad
    SHA1 | 1bdf0666b3c9c9a97a07255743d7c4a2f06d665e
    SHA256 | 6b886d23b24c543f8fc92ff8426cd782a77efb37750afac397591bda1eab8656  

    Pro soubory OVA verze 1.0.8.49:
    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8 
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    Pro soubory OVA verze 1.0.8.40:

    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

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
4. Ve službě Azure Migrate Collector otevřete nabídku **Nastavit požadavky**.
    - Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - Kolektor zkontrolujte, jestli má virtuální počítač přístup k internetu.
    - Pokud má virtuální počítač přístup k internetu přes proxy server, klikněte na **Nastavení proxy**, zadejte adresu proxy serveru a nastavte naslouchající port. Pokud proxy server potřebuje přihlašovací údaje, zadejte je.

    > [!NOTE]
    > Adresa proxy musí být zadaná ve formátu http://IPAdresaProxyServeru nebo http://PlněKvalifikovanýNázevDoményProxyServeru. Podporuje se jen proxy protokolu HTTP.

    - Kolektor zkontroluje, jestli je spuštěná služba kolektoru. Ta je ve výchozím nastavení nainstalovaná na virtuálním počítači kolektoru.
    - Stáhněte a nainstalujte řešení VMware PowerCLI.

5. V části **Zadejte podrobnosti vCenter Serveru** udělejte toto:
    - Zadejte název (plně kvalifikovaný název domény) nebo IP adresu vCenter Serveru.
    - V části **Uživatelské jméno** a **Heslo** zadejte přihlašovací údaje k účtu pouze pro čtení, který kolektor použije ke zjištění virtuálních počítačů na vCenter Serveru.
    - V části **Rozsah kolekce** vyberte kolekci pro zjišťování virtuálních počítačů. Kolektor může vyhledat jen virtuální počítače v rámci zadaného rozsahu. Jako rozsah můžete vybrat konkrétní složku, datové centrum nebo cluster. Neměl by obsahovat víc než 1000 virtuálních počítačů. 

6. V části **Zadejte projekt migrace** zadejte ID projektu služby Azure Migrate a klíč, který jste zkopírovali z portálu. Pokud jste ho nezkopírovali, na virtuálním počítači kolektoru otevřete Azure Portal. Na stránce **Přehled** projektu klikněte na **Zjistit počítače** a zkopírujte příslušné hodnoty.  
7. V části **Zobrazit průběh shromažďování** můžete sledovat zjišťování a kontrolovat, jestli metadata shromážděná z virtuálních počítačů patří do zadaného rozsahu. Kolektor vás informuje o tom, jak dlouho bude zjišťování přibližně trvat.

> [!NOTE]
> Kolektor podporuje jako jazyk operačního systému a jazyk rozhraní kolektoru jen angličtinu (Spojené státy). Brzy bude k dispozici podpora pro další jazyky.


### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Doba zjišťování závisí na tom, kolik virtuálních počítačů vyhledáváte. Od chvíle, kdy kolektor spustí zjišťování, obvykle trvá vyhledání stovky virtuálních počítačů přibližně hodinu. 

1. V projektu Azure Migrate klikněte na **Spravovat** > **Počítače**.
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

### <a name="assessment-details"></a>Podrobnosti o posouzení

Posouzení obsahuje informace o kompatibilitě místních virtuálních počítačů s Azure, správné velikosti virtuálního počítače pro provoz virtuálního počítače v Azure a odhadovaných měsíčních nákladech na Azure. 

![Sestava posouzení](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Připravenost pro Azure

Zobrazení Připravenost pro Azure v posouzení zobrazuje stav připravenosti jednotlivých virtuálních počítačů. V závislosti na svých vlastnostech můžou mít jednotlivé virtuální počítače následující označení:
- Připraveno pro Azure
- Připraveno pro Azure s podmínkou
- Nepřipraveno pro Azure
- Připravenost neznámá 

U virtuálních počítačů, které jsou pro migraci vhodné, doporučí Azure Migrate velikost virtuálního počítače v Azure. Velikost doporučená službou Azure Migrate vychází z kritéria určení velikosti zadaného ve vlastnostech posouzení. Pokud je kritérium určení velikosti založené na výkonu, doporučená velikost bude vycházet z historie výkonu virtuálních počítačů. Pokud je kritérium určení velikosti „jako u místních“, doporučení se provede na základě velikosti místního virtuálního počítače (určí se stejná velikost). V tomto případě se nezohledňují data o využití. [Další informace](concepts-assessment-calculation.md) o určování velikosti službou Azure Migrate. 

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

Ke každému posouzení ve službě Azure Migrate se přidruží hodnocení spolehlivosti v rozsahu od 1 do 5 hvězdiček (1 hvězdička znamená nejnižší a 5 hvězdiček nejvyšší spolehlivost). Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení. Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate. 

Hodnocení spolehlivosti je užitečné v případě, že provádíte *určování velikosti na základě výkonu*, protože Azure Migrate k určení velikosti na základě výkonu nemusí mít k dispozici dostatek datových bodů. V případě *určování stejné velikosti jako v místním prostředí* je hodnocení spolehlivosti vždy 5 hvězdiček, protože Azure Migrate má k dispozici všechny datové body, které k určení velikosti virtuálního počítače potřebuje. 

K určení velikosti virtuálního počítače na základě výkonu potřebuje Azure Migrate data o využití procesoru a paměti. Zároveň u každého disku připojeného k virtuálnímu počítači potřebuje informace o vstupně-výstupních operacích čtení a zápisu za sekundu a propustnosti. Podobně u každého síťového adaptéru připojeného k virtuálnímu počítači potřebuje Azure Migrate k určení velikosti na základě výkonu informace o síťových vstupech a výstupech. Pokud některá z výše uvedených čísel o využití nejsou v systému vCenter Server k dispozici, doporučení velikosti provedené službou Azure Migrate nemusí být spolehlivé. Tady je poskytnuté hodnocení spolehlivosti posouzení v závislosti na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček

Posouzení nemusí mít k dispozici všechny datové body z některého z následujících důvodů:
- Nastavení statistiky v systému vCenter Server není nastavené na úroveň 3 a posouzení má kritérium určení velikosti na základě výkonu. Pokud je nastavení statistiky v systému vCenter Server nižší než úroveň 3, data o výkonu disku a sítě se ze systému vCenter Server neshromažďují. V takovém případě není doporučení služby Azure Migrate týkající se disku a sítě založené na využití. Pro úložiště doporučí Azure Migrate disky Standard, protože bez zohlednění IOPS a propustnosti disku nemůže Azure Migrate určit, jestli disk bude v Azure potřebovat disk Premium.
- Nastavení statistiky v systému vCenter Server bylo před zahájením zjišťování nastavené na úroveň 3 po kratší dobu. Zvažme například scénář, kdy dnes změníte nastavení statistiky na úroveň 3 a zítra (za 24 hodin) zahájíte zjišťování pomocí zařízení kolektoru. Pokud vytváříte posouzení za jeden den, máte všechny datové body a hodnocení spolehlivosti posouzení bude 5 hvězdiček. Pokud však změníte dobu trvání výkonu ve vlastnostech posouzení na jeden měsíc, hodnocení spolehlivosti se sníží, protože data o výkonu disku a sítě za poslední měsíc nebudou k dispozici. Pokud chcete zohlednit data o výkonu za poslední měsíc, doporučujeme ponechat nastavení statistiky systému vCenter Server na úrovni 3 po dobu jednoho měsíce před zahájením zjišťování. 
- Během období, pro které se posouzení počítá, se několik virtuálních počítačů vypnulo. Pokud po nějakou dobu byly některé virtuální počítače vypnuté, vCenter Server nebude mít za toto období data o výkonu. 
- Během období, pro které se posouzení počítá, se vytvořilo několik virtuálních počítačů. Například pokud vytváříte posouzení historie výkonu za poslední měsíc, ale před týdnem se v prostředí vytvořilo několik virtuálních počítačů. V takových případech nebude k dispozici historie výkonu nových virtuálních počítačů za celé období.

> [!NOTE]
> Pokud je hodnocení spolehlivosti nějakého posouzení nižší než 4 hvězdičky, doporučujeme změnit nastavení statistiky systému vCenter Server na úroveň 3, počkat po dobu, kterou chcete v posouzení zohlednit (1 den, 1 týden, 1 měsíc), a pak provést zjišťování a posouzení. Pokud to není možné, určení velikosti na základě výkonu nemusí být spolehlivé a doporučuje se změnou vlastností posouzení přepnout na *určování stejné velikosti jako v místním prostředí*.
 
## <a name="next-steps"></a>Další kroky

- [Přečtěte si](how-to-scale-assessment.md), jak zjistit a posoudit virtuální počítače v rozsáhlém prostředí VMware.
- Zjistěte, jak vytvořit vysoce spolehlivé skupiny posouzení pomocí [mapování závislosti počítačů](how-to-create-group-machine-dependencies.md).
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
