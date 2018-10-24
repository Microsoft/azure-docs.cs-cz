---
title: Škálování zjišťování a posouzení pomocí Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak posoudit velký počet místních počítačů pomocí služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: raynew
ms.openlocfilehash: 6809c0e56fe55c7962ae273db0b5ac4335089df1
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945854"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Zkoumání a vyhodnocení rozsáhlých prostředí VMware

Azure Migrate má limit 1 500 počítačů pro každý projekt, tento článek popisuje, jak posoudit velký počet místních virtuálních počítačů (VM) s použitím [Azure Migrate](migrate-overview.md).   

## <a name="prerequisites"></a>Požadavky

- **VMware**: virtuální počítače, které chcete migrovat musí být spravované přes vCenter Server verze 5.5, 6.0 nebo 6.5. Kromě toho potřebujete jednoho hostitele ESXi ve verzi 5.0 nebo novější. Chcete-li nasadit virtuální počítač kolektoru.
- **účet vCenter**: potřebujete účet jen pro čtení pro přístup k systému vCenter Server. Azure Migrate ho použije ke zjištění místních virtuálních počítačů.
- **Oprávnění**: V systému vCenter Server, musíte mít oprávnění k vytvoření virtuálního počítače pomocí importu souboru ve formátu OVA.
- **Nastavení statistiky**: Tento požadavek platí pouze pro [model jednorázového zjišťování](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods). Před zahájením nasazování, by měla pro model jednorázového zjišťování, nastavit nastavení statistiky systému vCenter Server na úroveň 3. Úroveň statistiky má být nastavena na hodnotu 3 pro každý den, týden a měsíc kolekce intervalech. Pokud úroveň je nižší než 3 pro všechny tři sběru dat, posouzení bude fungovat, ale nebudou shromažďovat data o výkonu pro úložiště a sítě. Doporučené velikosti bude potom založena na údaje o výkonu pro využití procesoru a paměti a konfigurační data pro disk a síťové adaptéry.

### <a name="set-up-permissions"></a>Nastavení oprávnění

Azure Migrate k automatickému zjišťování virtuálních počítačů pro účely posouzení potřebuje přístup k serverům VMware. VMware účet musí mít následující oprávnění:

- Typ uživatele: Alespoň uživatel jen pro čtení
- Oprávnění: Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only
- Podrobnosti: Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v tomto datacentru.
- Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli Žádný přístup s objektem Rozšířit na podřízený objekt.

Pokud nasazujete v prostředí s tenanty, tady je jeden způsob, jak nastavit tuto možnost:

1.  Vytvořit uživatele na klienta a pomocí [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), přiřaďte oprávnění jen pro čtení pro všechny virtuální počítače, které patří do konkrétního tenanta. Potom použijte své přihlašovací údaje pro zjišťování. RBAC se zajistí, že odpovídající vCenter uživatel bude mít přístup k virtuálním počítačům pouze specifickým pro tenanta.
2. Můžete nastavit RBAC pro uživatele jiného tenanta, jak je popsáno v následujícím příkladu pro uživatele č. 1 a 2 uživatele:

    - V **uživatelské jméno** a **heslo**, určete pověření účtu jen pro čtení, který kolektor použije ke zjištění virtuálních počítačů v
    - Datacenter1 – udělení oprávnění jen pro čtení na 1 uživatele a uživatele č. 2. Těmito oprávněními, aby všechny podřízené objekty, není rozšířit, protože budete nastavit oprávnění pro jednotlivé virtuální počítače.

      - VM1 (Tenanta č. 1) (oprávnění jen pro čtení pro uživatele č. 1)
      - VM2 (Tenanta č. 1) (oprávnění jen pro čtení pro uživatele č. 1)
      - VM3 (Tenanta č. 2) (oprávnění jen pro čtení pro uživatele č. 2)
      - VM4 (Tenanta č. 2) (oprávnění jen pro čtení pro uživatele č. 2)

   - Pokud provádíte zjišťování pomocí přihlašovacích údajů uživatele č. 1, budou zjištěny pouze VM1 a VM2.

## <a name="plan-your-migration-projects-and-discoveries"></a>Plánování migrace projektů a zjišťování

Jeden kolektor Azure Migrate podporuje zjišťování z více servery vCenter (jednu po druhé) a také podporuje zjišťování tak, aby více projekty migrace (jednu po druhé).

Kolekce, v případě jednorázového zjišťování funguje v spustit a zapomenout modelu, po dokončení zjišťování můžete použít stejné kolekce shromažďovat data z různých vCenter serveru nebo odeslání do projektu různé migrace. V případě průběžná zjišťování je připojen jeden zařízení do jednoho projektu, proto stejné kolekce nelze použít k aktivaci druhý zjišťování.

Plán zjišťování a posouzení podle následující omezení:

| **Entity** | **Limit pro počítače** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Zjišťování  | 1,500             |
| Posouzení | 1,500             |

Mějte tyto aspekty plánování:

- Když použijete zjišťování pomocí Azure Migrate collector, můžete nastavit obor zjišťování do složky na serveru vCenter, datacentrum, cluster nebo hostitele.
- Více než jedno zjišťování proveďte ověření v systému vCenter Server, které jsou virtuální počítače, které chcete zjišťovat složky, datovými centry, clustery nebo hostitele, kteří podporují omezení 1 500 počítačů.
- Doporučujeme, abyste pro účely posouzení ponechat počítačů pomocí vzájemných závislostí v rámci jednoho projektu a posouzení. V systému vCenter Server Ujistěte se, že závislé počítače jsou ve stejném složku, datové centrum nebo cluster pro posouzení.

V závislosti na vašem scénáři můžete rozdělit co jste se dozvěděli podle předepsaného níže:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Více vCenter servery s méně než 1 500 virtuálních počítačů
Pokud máte více servery vCenter ve vašem prostředí a celkový počet virtuálních počítačů je menší než 1 500, můžete použít následující přístup založený na váš scénář:

**Jednorázově:** jeden kolektor a jedna migrace projektu můžete použít ke zjišťování všech virtuálních počítačů napříč všemi servery vCenter. Protože jednorázově kolektoru zjistí jednomu vCenter serveru současně, můžete spustit stejnou kolekcí pro všechny servery, vCenter, jeden po druhém a kolektoru přejděte na stejný projekt migrace. Po dokončení všech zjišťování si můžete vytvořit posouzení pro počítače.

**Průběžná zjišťování:** v případě průběžná zjišťování, může být jeden zařízení připojena k pouze jeden projekt. Proto budete muset nasadit jeden zařízení pro každou z vCenter servery a pak vytvořte jeden projekt pro každé zařízení a aktivační události zjišťování odpovídajícím způsobem.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Více vCenter servery s více než 1 500 virtuálních počítačů

Pokud máte více servery vCenter s méně než 1 500 virtuálních počítačů na vCenter serveru, ale více než 1 500 virtuálních počítačů napříč všemi servery vCenter, musíte vytvořit více projekty migrace (jeden projekt migrace může obsahovat jenom 1 500 virtuálních počítačů). Můžete dosáhnout vytvořením projektu migrace na vCenter Server a rozdělení zjišťování.

**Jednorázově:** jeden kolektor můžete použít ke zjištění každého systému vCenter Server (jednu po druhé). Pokud chcete zjišťování spuštění ve stejnou dobu, můžete také nasadit více zařízení a při paralelním spuštění zjišťování.

**Průběžná zjišťování:** budete muset vytvořit více kolekcí zařízení (jeden pro každý systém vCenter Server) a připojení každého zařízení k projektu a aktivační události zjišťování odpovídajícím způsobem.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Více než 1 500 počítačů v jedné systému vCenter Server

Pokud máte víc než 1500 virtuálních počítačů v jedné systému vCenter Server, budete muset zjišťování rozdělit do několika projekty migrace. Pokud chcete rozdělit zjišťování, můžete využít pole oboru v zařízení a zadejte hostitele, cluster, složka nebo datovém centru, které jste chtěli vyhledat. Například, pokud máte dvě složky v systému vCenter Server, jeden s 1000 virtuálních počítačů (složku1) a druhý s 800 virtuálních počítačů (slozka2), rozsah pole můžete použít k rozdělení zjišťování mezi tyto složky.

**Jednorázově:** stejné kolekce můžete použít k aktivaci obou zjišťování. V první zjišťování, můžete zadat složku1 jako obor a nasměrovat ho na první projekt migrace po dokončení první zjišťování můžete používat stejné kolekce, změnit její obor podrobností projektu slozka2 a migrace na druhý projekt migrace a Proveďte druhou zjišťování.

**Průběžná zjišťování:** v takovém případě budete muset vytvořit dvě kolekce zařízení, což se pro první kolekce, zadejte rozsah jako složku1 a připojte ho k první projekt migrace. Můžete paralelně spustit zjišťování slozka2 použití druhého zařízení kolektoru a jejím připojení k druhé projekt migrace.

### <a name="multi-tenant-environment"></a>Prostředí s více tenanty

Pokud máte prostředí, které se sdílejí napříč tenanty a nechcete ke zjištění virtuálních počítačů z jednoho tenanta v jiném tenantovi předplatné, můžete použít pole oboru v zařízení kolektoru k určení rozsahu zjišťování. Pokud klienti sdílejí hostitele, vytvoření přihlašovacích údajů, který má přístup jen pro čtení na pouze virtuální počítače patří do konkrétního tenanta a použít tyto přihlašovací údaje v zařízení kolektoru a zadejte rozsah jako hostitele, které chcete zjišťování.

## <a name="discover-on-premises-environment"></a>Zjišťování místního prostředí

Jakmile budete připraveni s plánem, potom můžete spustit zjišťování místních virtuálních počítačů:

### <a name="create-a-project"></a>Vytvoření projektu

Vytvořte projekt Azure Migrate v souladu s vaší požadavky:

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.
2. Vyhledejte **Azure Migrate** a ve výsledcích hledání vyberte službu **Azure Migrate**. Potom vyberte **Vytvořit**.
3. Zadejte název projektu a předplatné Azure pro projekt.
4. Vytvořte novou skupinu prostředků.
5. Zadejte umístění, ve kterém chcete vytvořit projekt a pak vyberte **vytvořit**. Všimněte si, že můžete přesto posoudit vašich virtuálních počítačů pro jiné cílové umístění. Umístění vybrané pro tento projekt slouží k uložení metadat shromážděných z místních virtuálních počítačů.

### <a name="set-up-the-collector-appliance"></a>Nastavení zařízení kolektoru

Azure Migrate vytvoří místní virtuální počítač, kterému se říká zařízení kolektoru. Tento virtuální počítač vyhledá místní virtuální počítače VMware a odešle jejich metadata do služby Azure Migrate. Nastavení zařízení kolektoru, stáhnout soubor OVA a importovat ho do místní instanci serveru vCenter.

#### <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Pokud máte více projektů, budete muset stažení zařízení kolektoru pouze jednou k vCenter serveru. Po stažení a nastavení na zařízení, spusťte pro každý projekt a zadáte jedinečné ID a klíč projektu.

1. V projektu služby Azure Migrate klikněte na **Začínáme** > **Zjistit a posoudit** > **Zjistit počítače**.
2. V části **Zjistit počítače**, existují dvě možnosti k dispozici pro zařízení, kliknutím na tlačítko **Stáhnout** stáhnete odpovídající zařízení založené na dáváte přednost.

    a. **Jednorázové zjišťování:** Zařízení pro tento model komunikuje s vCenter Server, aby shromažďovalo metadata o virtuálních počítačích. Pro shromažďování dat o výkonu virtuálních počítačů závisí na historických datech o výkonu uložených ve vCenter Server a shromažďuje historii výkonu za poslední měsíc. V tomto modelu Azure Migrate shromažďuje průměrný čítač (oproti čítači ve špičce) pro jednotlivé metriky, [více informací] (https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected). Protože jde o jednorázovou zjišťování, změny v místním prostředí se neprojeví, po dokončení zjišťování. Pokud chcete, aby se změny projevily, budete muset provést opakované zjišťování stejného prostředí stejného projektu.

    b. **Průběžné zjišťování:** Zařízení pro tento model průběžně profiluje místní prostředí pro shromažďování dat o využití v reálném čase pro každý virtuální počítač. V tomto modelu se shromažďují čítače ve špičce pro každou metriku (využití procesoru, využití paměti atd.). Tento model není závislý na nastavení statistiky vCenter Server pro shromažďování dat o výkonu. Kdykoli ze zařízení můžete zastavit průběžnou profilaci.

    > [!NOTE]
    > Průběžné zjišťování funkce je ve verzi Preview.

3. V **kopírování přihlašovacích údajů projektu**, zkopírujte ID a klíč projektu. Budete je potřebovat při konfiguraci kolektoru.


#### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Zkontrolujte, zda soubor OVA zabezpečené před jejím nasazením:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.

2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Ujistěte se, že generované hodnoty hash shoduje s následujícím nastavením.

#### <a name="one-time-discovery"></a>Jednorázové zjišťování

<<<<<<< HEAD pro soubory OVA verze 1.0.9.15 (vydaná 10/23/2018)

 <a name="algorithm--hash-value"></a>**Algoritmus** | **hodnoty Hash**
=======
Pro soubory OVA verze 1.0.9.15

**Algoritmus** | **hodnoty Hash**
>>>>>>> 20dc93529e7c0a4d17f2f4524752b5e2bead4e37---| ---MD5 | e9ef16b0c837638c506b5fc0ef75ebfa SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864 SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

<<<<<<< HEAD pro soubory OVA verze 1.0.9.14 (vydaná 8/24/2018) === pro soubory OVA verze 1.0.9.14
>>>>>>> 20dc93529e7c0a4d17f2f4524752b5e2bead4e37

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

### <a name="create-the-collector-vm"></a>Vytvoření virtuálního počítače kolektoru

Importujte stažený soubor do vCenter serveru:

1. V konzoli vSphere Client vyberte **souboru** > **Deploy OVF Template**.

    ![Nasazení šablony OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. V Průvodci nasazením šablony OVF > **zdroj**, zadejte umístění souboru pro soubory OVA.
3. V části **Name** (Název) a **Location** (Umístění) zadejte popisný název virtuálního počítače kolektoru a objekt inventáře, ve kterém bude daný virtuální počítač hostovaný.
4. V části **Host/Cluster** (Hostitel/cluster) zadejte hostitele nebo cluster, na kterém se bude virtuální počítač kolektoru spouštět.
5. V části Storage (Úložiště) zadejte cílové úložiště pro virtuální počítač kolektoru.
6. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
7. V části **Network Mapping** (Mapování sítě) zadejte síť, ke které se bude virtuální počítač kolektoru připojovat. Síť vyžaduje připojení k Internetu na odesílat metadata do Azure.
8. Zkontrolujte a potvrďte nastavení a pak vyberte **Dokončit**.

### <a name="identify-the-id-and-key-for-each-project"></a>Určit ID a klíč pro každý projekt

Pokud máte více projektů, je potřeba určit ID a klíč pro každý z nich. Budete potřebovat klíč při spuštění kolektoru pro vyhledání virtuálních počítačů.

1. V projektu, vyberte **Začínáme** > **zjistit a posoudit** > **zjistit počítače**.
2. V **kopírování přihlašovacích údajů projektu**, zkopírujte ID a klíč projektu.
    ![Kopírování přihlašovacích údajů projektu](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>Nastavit úroveň statistiky vCenter

Zařízení kolektoru zjistí následující statické metadata o vybrané virtuální počítače.

1. Název zobrazení virtuálních počítačů (na serveru vCenter)
2. Cesta inventáře Virtuálního počítače (hostitele nebo složku v systému vCenter)
3. IP adresa
4. Adresa MAC
5. Operační systém
5. Počet jader, disků, síťových adaptérů
6. Velikost paměti, velikosti disků
7. A z čítačů výkonu virtuálních počítačů, disku a sítě, jak je uvedeno v následující tabulce.

Jednorázové zjišťování následující tabulka uvádí čítače přesné údaje o výkonu, které se budou shromažďovat a také výsledky posouzení, které jsou by to vliv na konkrétní čítač nejsou shromažďována.

Průběžná zjišťování, čítače se shromažďují v reálném čase (intervalu 20 sekund), takže není žádná závislost na úroveň statistiky vCenter. Zařízení potom zobrazí – až 20 sekund ukázky vytvořte jeden datový bod pro každých 15 minut tak, že vyberete hodnotu ve špičce ukázky 20 sekund a odesílá je do Azure.

|Čítač                                  |Úroveň    |Úroveň podle zařízení  |Hodnocení dopadu                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.average                        | 1       |Není k dispozici                |Doporučené velikosti virtuálních počítačů a náklady                    |
|mem.usage.average                        | 1       |Není k dispozici                |Doporučené velikosti virtuálních počítačů a náklady                    |
|virtualDisk.read.average                 | 2       |2                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|virtualDisk.write.average                | 2       |2                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|net.received.average                     | 2       |3                 |Cena za virtuální počítače velikosti a sítě                        |
|net.transmitted.average                  | 2       |3                 |Cena za virtuální počítače velikosti a sítě                        |

> [!WARNING]
> Jednorázové zjišťování Pokud jste právě nastavili vyšší úroveň statistiky, ji bude trvat až jeden den generovat čítače výkonu. Proto doporučujeme spustit zjišťování po jednom dni. Průběžná zjišťování modelu počkejte aspoň jeden den po spuštění zjišťování pro zařízení profilu prostředí a pak vytvořit posouzení.

### <a name="run-the-collector-to-discover-vms"></a>Spuštění kolektoru pro vyhledání virtuálních počítačů

Pro každého zjišťování, které je třeba provést spuštění kolektoru pro vyhledání virtuálních počítačů v požadovaný rozsah. Spusťte zjišťování jednu po druhé. Souběžné zjišťování nejsou podporovány a každého zjišťování musí mít jiný obor.

1.  V konzoli vSphere Client klikněte pravým tlačítkem na daný virtuální počítač a pak na **Open Console** (Otevřít konzolu).
2.  Nastavte pro zařízení preferovaný jazyk, časové pásmo a heslo.
3.  Na ploše, vyberte **spustit kolektor** zástupce.
4.  V Azure Migrate collector, otevřete **nastavit požadavky** a pak:

    a. Přijměte licenční podmínky a přečtěte si informace třetích stran.

    Kolektor zkontrolujte, jestli má virtuální počítač přístup k internetu.

    b. Pokud virtuální počítač přístup k Internetu přes proxy server, vyberte **nastavení proxy serveru**a zadejte adresu proxy serveru a port pro naslouchání. Pokud proxy server potřebuje přihlašovací údaje, zadejte je.

    Kolektor zkontroluje, jestli je spuštěná služba kolektoru. Ta je ve výchozím nastavení nainstalovaná na virtuálním počítači kolektoru.

    c. Stáhněte a nainstalujte VMware PowerCLI.

5.  V části **Zadejte podrobnosti vCenter Serveru** udělejte toto:
    - Zadejte název (FQDN) nebo IP adresu vCenter serveru.
    - V **uživatelské jméno** a **heslo**, určete pověření účtu jen pro čtení, který kolektor použije ke zjištění virtuálních počítačů v systému vCenter Server.
    - V části **Vyberte rozsah** vyberte rozsah zjišťování virtuálních počítačů. Kolektor může vyhledat pouze virtuální počítače v rámci zadaného oboru. Jako rozsah můžete vybrat konkrétní složku, datové centrum nebo cluster. Neměl by obsahovat víc než 1 000 virtuálních počítačů.

6.  V **zadejte projekt migrace**, zadejte ID a klíč projektu. Pokud jste je nezkopírovali, otevřete na webu Azure portal virtuálním počítači kolektoru. V projektu **přehled** stránce **zjistit počítače** a zkopírujte hodnoty.  
7.  V **zobrazit průběh shromažďování**, monitorovat proces zjišťování a zkontrolujte, jestli metadata shromážděná z virtuálních počítačů je v oboru. Kolektor vás informuje o tom, jak dlouho bude zjišťování přibližně trvat.


#### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Pro jednorázové zjišťování doba zjišťování závisí na tom, kolik virtuálních počítačů vyhledáváte. Obvykle pro 100 virtuálních počítačů po kolektor spustí trvá přibližně hodinu konfigurace a výkonu shromažďování dat pro dokončení. Okamžitě po dokončení zjišťování můžete vytvořit interní hodnocení (na základě výkonu a nebo na základě místa).

Z důvodu průběžného zjišťování (ve verzi preview), bude kolektor průběžně profilovat místní prostředí a bude posílat data o výkonu v hodinovém intervalu. Za hodinu po zahájení zjišťování můžete zkontrolovat počítače na portálu. Před vytvořením interního hodnocení založeného na výkonu virtuálních počítačů důrazně doporučujeme počkat aspoň jeden den.

1. V projektu migrace klikněte na **Spravovat** > **počítače**.
2. Zkontrolujte, jestli se virtuální počítače, které jste chtěli vyhledat, zobrazí na portálu.


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořte skupinu](how-to-create-a-group.md) pro posouzení.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
