---
title: Škálování zjišťování a hodnocení pomocí Azure migrovat | Microsoft Docs
description: Popisuje, jak k vyhodnocení velký počet počítačů místně pomocí služby Azure migrovat.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: 89c9cfd4bdc1c483764983c886ba9f96cc75c69e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736826"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Zkoumání a vyhodnocení rozsáhlých prostředí VMware

Azure migrací může obsahovat maximálně 1 500 počítačů pro každý projekt, tento článek popisuje, jak k vyhodnocení velkého počtu místní virtuální počítače (VM) pomocí [Azure migrovat](migrate-overview.md).   

## <a name="prerequisites"></a>Požadavky

- **VMware**: virtuálních počítačů, která chcete migrovat se musí spravovat přes vCenter Server 5.5, 6.0 nebo 6.5 verze. Kromě toho musíte jedna ESXi hostitele spuštěné verze 5.0 nebo novější k nasazení kolekcí virtuálních počítačů.
- **účet vCenter**: je třeba účet jen pro čtení pro přístup k systému vCenter Server. Azure Migrate ho použije ke zjištění místních virtuálních počítačů.
- **Oprávnění**: V systému vCenter Server, potřebujete oprávnění k vytvoření virtuálního počítače importováním souboru ve formátu vajíčka.
- **Nastavení statistiky**: nastavení statistiky pro vCenter Server musí být nastavená na úroveň 3 před zahájením nasazení. Pokud úroveň je nižší než 3, hodnocení bude fungovat, ale nebudou shromažďovány údaje o výkonu pro úložiště a sítě. Velikost doporučení v tomto případě budou založeny na údaje o výkonu pro procesor a paměť a konfigurační data pro disk a síťové adaptéry.

## <a name="plan-your-migration-projects-and-discoveries"></a>Plánování migrace projekty a zjišťování

Jeden kolektor migrovat Azure podporuje zjišťování z více servery vCenter (jedna po druhé) a také podporuje zjišťování tak, aby více projektů migrace (jedna po druhé). Kolekce pracuje v ještě efektivněji a zapomněli modelu, jakmile se provádí zjišťování, můžete použít stejné kolekce shromažďovat data z různých vCenter Server nebo odeslat do projektu různé migrace.

Plánování zjišťování a vyhodnocování podle následující omezení:

| **Entity** | **Limit pro počítače** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Zjišťování  | 1,500             |
| Posouzení | 1,500             |

Tyto aspekty plánování mějte na paměti:

- Při zjišťování pomocí Azure migraci kolekce, můžete nastavit obor zjišťování pro složku na serveru vCenter, datacenter, cluster nebo hostitele.
- Více než jedno zjišťování provedete ověřte v systému vCenter Server, které jsou virtuální počítače, které chcete zjistit v složek, datových center, clusterům nebo hostitele, kteří podporují omezení 1 500 počítačů.
- Doporučujeme, aby pro účely hodnocení, byl počítače s vzájemné závislosti v rámci stejného projektu a hodnocení. V systému vCenter Server Ujistěte se, že závislé počítače jsou ve stejné složce, datacenter nebo clusteru pro hodnocení.

V závislosti na vašem scénáři můžete rozdělit vaší zjišťování souladu s níže:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>VCenter více serverů s menší než 1 500 virtuální počítače

Pokud máte více servery vCenter ve vašem prostředí, a celkový počet virtuálních počítačů je menší než 1 500, můžete použít jeden kolektor a projekt jeden migrace se zjistit všechny virtuální počítače na všechny servery vCenter. Vzhledem k tomu, že kolekce zjistí jeden Server vCenter současně, můžete spustit stejné kolekce pro všechny servery, vCenter, jedna po druhé a přejděte na stejném projektu migrace kolekce. Po dokončení všech zjišťování se potom můžete vytvořit posuzování pro počítače.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>VCenter více servery s více než 1500 virtuální počítače

Pokud máte více servery vCenter s menší než 1 500 virtuálních počítačů na systému vCenter Server, ale více než 1500 virtuální počítače mezi všechny slouží vCenter, budete muset vytvořit více projektů migrace (jeden migrace projektu lze uložit pouze 1500 virtuálních počítačů). Můžete dosáhnout vytvořením projektu migrace na serveru vCenter a rozdělení zjišťování. Jeden kolektor můžete použít ke zjištění každého systému vCenter Server (jedna po druhé). Pokud chcete zjišťování pro spuštění ve stejnou dobu, můžete také nasadit více zařízení a spustit zjišťováními paralelně.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Více než 1500 počítačů v jednom systému vCenter Server

Pokud máte více než 1500 virtuálních počítačů v jednom systému vCenter Server, je třeba rozdělit do více projektů migrace zjišťování. Rozdělení zjišťování, můžete využít pole obor v zařízení a zadejte hostitele, cluster, složku nebo datacenter, která chcete zjišťovat. Například, pokud máte dvě složky v systému vCenter Server, jeden s 1 000 virtuálních počítačů (složku1) a ostatní 800 virtuálních počítačů (složka2), můžete použít jeden kolektor a provést dvě zjišťování. V první zjišťování, můžete zadat složku1 jako obor a nasměrovat ho na první projekt migrace po dokončení prvního zjišťování můžete použít stejné kolekce, změňte její obor podrobností projektu složka2 a migrace do druhé migrace projektu a Proveďte druhý zjišťování.

### <a name="multi-tenant-environment"></a>Víceklientské prostředí

Pokud máte prostředí, který je sdílen mezi klienty a nechcete zjistit virtuální počítače jednoho klienta v rámci jiného klienta předplatného, pole obor v kolekci zařízení můžete použít k určení rozsahu zjišťování. Pokud klienti sdílejí hostitele, vytvoření přihlašovacích údajů, který má přístup jen pro čtení k jenom virtuální počítače patřící do určité klienta a použít tento přihlašovací údaj v zařízení kolekce a zadejte rozsah jako hostitel udělat zjišťování. Alternativně můžete také vytvořit složky v systému vCenter Server (Řekněme složku1 pro tenant1 a složka2 pro tenant2), pod sdílené hostitele, přesunutí virtuálních počítačů pro tenant1 do složku1 a tenant2 do složka2 a odpovídajícím způsobem určit obor zjišťování v kolekci zadáním příslušné složky.

## <a name="discover-on-premises-environment"></a>Zjistit místní prostředí

Až budete připravení s plánu, potom můžete spustit zjišťování místní virtuální počítače:

### <a name="create-a-project"></a>Vytvoření projektu

Vytvoření projektu Azure migraci v souladu s vaší požadavky:

1. Na portálu Azure vyberte **vytvořit prostředek**.
2. Vyhledejte **Azure Migrate** a ve výsledcích hledání vyberte službu **Azure Migrate (Preview)**. Potom vyberte **Vytvořit**.
3. Zadejte název projektu a předplatné Azure pro projekt.
4. Vytvořte novou skupinu prostředků.
5. Zadejte umístění, ve kterém chcete vytvořit projekt a potom vyberte **vytvořit**. Všimněte si, že můžete stále vyhodnotit virtuálních počítačů pro jiné cílové umístění. K umístění zadanému pro projekt se používá k ukládání metadat získané z virtuálních počítačů na místě.

### <a name="set-up-the-collector-appliance"></a>Nastavení kolekce zařízení

Azure Migrate vytvoří místní virtuální počítač, kterému se říká zařízení kolektoru. Tento virtuální počítač vyhledá virtuální počítače VMware na místě a metadata o nich odešle do služby Azure migrovat. Pokud chcete nastavit zařízení kolekce, stáhněte si soubor vajíčka, importujte je do místní instance serveru vCenter.

#### <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Pokud máte více projektů, budete muset stáhnout kolekce zařízení pouze jednou k systému vCenter Server. Po stažení a nastavení zařízení, můžete spustit pro každý projekt, a zadáte projektu jedinečné ID a klíč.

1. V projektu Azure migrovat vyberte **Začínáme** > **Discover & hodnocení** > **zjišťovat počítače**.
2. V **zjistit počítače**, vyberte **Stáhnout**, chcete-li stáhnout soubor vajíčka.
3. V **zkopírujte projektu pověření**, zkopírujte ID a klíč pro projekt. Budete je potřebovat při konfiguraci kolektoru.


#### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Zkontrolujte, jestli soubor vajíčka zabezpečené před nasazením:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.

2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Ujistěte se, že generované hodnoty hash odpovídá následující nastavení.

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

### <a name="create-the-collector-vm"></a>Vytvoření virtuálního počítače kolektoru

Stažený soubor importujte do systému vCenter Server:

1. V konzole klienta vSphere vyberte **soubor** > **nasazení šablony OVF**.

    ![Nasazení šablony OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. V Průvodci nasazením šablony OVF > **zdroj**, zadejte umístění souboru vajíčka.
3. V části **Name** (Název) a **Location** (Umístění) zadejte popisný název virtuálního počítače kolektoru a objekt inventáře, ve kterém bude daný virtuální počítač hostovaný.
4. V části **Host/Cluster** (Hostitel/cluster) zadejte hostitele nebo cluster, na kterém se bude virtuální počítač kolektoru spouštět.
5. V části Storage (Úložiště) zadejte cílové úložiště pro virtuální počítač kolektoru.
6. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
7. V části **Network Mapping** (Mapování sítě) zadejte síť, ke které se bude virtuální počítač kolektoru připojovat. Síť musí připojení k Internetu k odeslání metadata do Azure.
8. Zkontrolujte a potvrďte nastavení a potom vyberte **Dokončit**.

### <a name="identify-the-id-and-key-for-each-project"></a>Určit ID a klíč pro každý projekt

Pokud máte více projektů, nezapomeňte určit ID a klíč pro každé z nich. Klíč musíte při spuštění kolekce ke zjišťování virtuálních počítačů.

1. V projektu, vyberte **Začínáme** > **Discover & hodnocení** > **zjišťovat počítače**.
2. V **zkopírujte projektu pověření**, zkopírujte ID a klíč pro projekt.
    ![Zkopírujte pověření projektu](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>Nastavení úrovně statistiky vCenter
Následuje seznam čítačů výkonu, které byly shromážděny během zjišťování. Tyto čítače jsou ve výchozím nastavení k dispozici na různých úrovních v systému vCenter Server.

Doporučujeme nastavit nejvyšší běžné úroveň (3) pro úroveň statistiky tak, aby všechny čítače jsou shromažďovány správně. Pokud máte vCenter nastavit na nižší úrovni, mohou být pouze několik čítače shromažďovány úplně, se zbytkem nastaven na hodnotu 0. Posouzení pak může zobrazovat neúplná data.

Následující tabulka uvádí také výsledky hodnocení, které bude mít vliv, pokud nejsou zjištěny jednotlivých čítačů.

| Čítač                                 | Úroveň | Úroveň za zařízení | Dopad hodnocení                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| CPU.Usage.average                       | 1     | Není k dispozici               | Doporučená velikost virtuálního počítače a náklady         |
| mem.usage.average                       | 1     | Není k dispozici               | Doporučená velikost virtuálního počítače a náklady         |
| virtualDisk.read.average                | 2     | 2                | Velikost disku, náklady na úložiště a velikost virtuálního počítače |
| virtualDisk.write.average               | 2     | 2                | Velikost disku, náklady na úložiště a velikost virtuálního počítače |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | Velikost disku, náklady na úložiště a velikost virtuálního počítače |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | Velikost disku, náklady na úložiště a velikost virtuálního počítače |
| net.received.average                    | 2     | 3                | Náklady na velikost a sítě virtuálních počítačů             |
| net.transmitted.average                 | 2     | 3                | Náklady na velikost a sítě virtuálních počítačů             |

> [!WARNING]
> Pokud jste právě nastavili na vyšší úrovni statistiky, bude trvat jeden den ke generování čítače výkonu. Ano doporučujeme spustit zjišťování po uplynutí jednoho dne.

### <a name="run-the-collector-to-discover-vms"></a>Spuštění kolektoru pro vyhledání virtuálních počítačů

U každého zjišťování, které je třeba provést spusťte kolekce k vyhledání virtuálních počítačů v oboru vyžaduje. Spusťte zjišťování jedna po druhé. Nejsou podporovány souběžných zjišťování a každého zjišťování musí mít jiný rozsah.

1.  V konzoli vSphere Client klikněte pravým tlačítkem na daný virtuální počítač a pak na **Open Console** (Otevřít konzolu).
2.  Nastavte pro zařízení preferovaný jazyk, časové pásmo a heslo.
3.  Na ploše, vyberte **spustit kolekce** zástupce.
4.  V kolekci Azure migrovat otevřete **nastavit požadavky** a pak:

    a. Přijměte licenční podmínky a přečtěte si informace třetích stran.

    Kolektor zkontrolujte, jestli má virtuální počítač přístup k internetu.

    b. Pokud virtuální počítač získá přístup k Internetu prostřednictvím proxy serveru, vyberte **nastavení proxy serveru**a zadat adresu proxy serveru a port naslouchání. Pokud proxy server potřebuje přihlašovací údaje, zadejte je.

    Kolektor zkontroluje, jestli je spuštěná služba kolektoru. Ta je ve výchozím nastavení nainstalovaná na virtuálním počítači kolektoru.

    c. Stáhněte a nainstalujte VMware PowerCLI.

5.  V části **Zadejte podrobnosti vCenter Serveru** udělejte toto:
    - Zadejte název (FQDN) nebo IP adresa serveru vCenter.
    - V **uživatelské jméno** a **heslo**, zadejte pověření účtu jen pro čtení, které kolekce použije k vyhledání virtuálních počítačů v systému vCenter Server.
    - V části **Vyberte rozsah** vyberte rozsah zjišťování virtuálních počítačů. Kolekce může zjišťovat pouze virtuální počítače v zadaném oboru. Jako rozsah můžete vybrat konkrétní složku, datové centrum nebo cluster. Měl by neměl obsahovat více než 1 000 virtuálních počítačů.

6.  V **zadejte migrace projektu**, zadejte ID a klíč pro projekt. Pokud zkopírujete nebyla je, otevřete portál Azure z kolekce virtuálních počítačů. V projektu **přehled** vyberte **zjišťovat počítače** a zkopírujte hodnoty.  
7.  V **sledovat průběh kolekce**, monitorovat proces zjišťování a zkontrolujte, že metadata shromážděných z virtuálních počítačů jsou v oboru. Kolektor vás informuje o tom, jak dlouho bude zjišťování přibližně trvat.


#### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Doba zjišťování závisí na tom, kolik virtuálních počítačů vyhledáváte. U 100 virtuálních počítačů obvykle zjišťování dokončení kolem hodinu po dokončení spuštění kolekce.

1. V projektu migrace Planner vyberte **spravovat** > **počítače**.
2. Zkontrolujte, jestli se virtuální počítače, které jste chtěli vyhledat, zobrazí na portálu.


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořte skupinu](how-to-create-a-group.md) pro vyhodnocení.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
