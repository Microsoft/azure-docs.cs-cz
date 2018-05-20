---
title: Škálování zjišťování a hodnocení pomocí Azure migrovat | Microsoft Docs
description: Popisuje, jak k vyhodnocení velký počet počítačů místně pomocí služby Azure migrovat.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: e0bd62710c47cfdf81535470ef96bad2ab675bb0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Zkoumání a vyhodnocení rozsáhlých prostředí VMware

Tento článek popisuje, jak k vyhodnocení velkého počtu místní virtuální počítače (VM) pomocí [Azure migrovat](migrate-overview.md). Azure migrací vyhodnocuje počítače, které chcete zkontrolovat, zda jsou vhodné pro migraci na Azure. Služba poskytuje velikost a cenově odhady pro spouštění počítačů v Azure.

## <a name="prerequisites"></a>Požadavky

- **VMware**: virtuálních počítačů, která chcete migrovat se musí spravovat přes vCenter Server 5.5, 6.0 nebo 6.5 verze. Kromě toho musíte jedna ESXi hostitele spuštěné verze 5.0 nebo novější k nasazení kolekcí virtuálních počítačů.
- **účet vCenter**: je třeba účet jen pro čtení pro přístup k systému vCenter Server. Azure Migrate ho použije ke zjištění místních virtuálních počítačů.
- **Oprávnění**: V systému vCenter Server, potřebujete oprávnění k vytvoření virtuálního počítače importováním souboru ve formátu vajíčka.
- **Nastavení statistiky**: nastavení statistiky pro vCenter Server musí být nastavená na úroveň 3 před zahájením nasazení. Pokud úroveň je nižší než 3, hodnocení bude fungovat, ale nebudou shromažďovány údaje o výkonu pro úložiště a sítě. Velikost doporučení v tomto případě budou založeny na údaje o výkonu pro procesor a paměť a konfigurační data pro disk a síťové adaptéry.

## <a name="plan-azure-migrate-projects"></a>Plánování migrace Azure projekty

Plánování zjišťování a vyhodnocování podle následující omezení:

| **Entity** | **Limit pro počítače** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Zjišťování  | 1,500             |
| Posouzení | 1,500             |

<!-- 
- If you have fewer than 400 machines to discover and assess, you need a single project and a single discovery. Depending on your requirements, you can either assess all the machines in a single assessment or split the machines into multiple assessments. 
- If you have 400 to 1,000 machines to discover, you need a single project with a single discovery. But you will need multiple assessments to assess these machines, because a single assessment can hold up to 400 machines.
- If you have 1,001 to 1,500 machines, you need a single project with two discoveries in it.
- If you have more than 1,500 machines, you need to create multiple projects, and perform multiple discoveries, according to your requirements. For example:
    - If you have 3,000 machines, you can set up two projects with two discoveries, or three projects with a single discovery.
    - If you have 5,000 machines, you can set up four projects: three with a discovery of 1,500 machines, and one with a discovery of 500 machines. Alternatively, you can set up five projects with a single discovery in each one. 
      -->

## <a name="plan-multiple-discoveries"></a>Plánování více zjišťování

Stejné kolekce Azure migraci můžete udělat několik zjišťování na jeden nebo více projekty. Tyto aspekty plánování mějte na paměti:

- Při zjišťování pomocí Azure migraci kolekce, můžete nastavit obor zjišťování pro složku na serveru vCenter, datacenter, cluster nebo hostitele.
- Více než jedno zjišťování provedete ověřte v systému vCenter Server, které jsou virtuální počítače, které chcete zjistit v složek, datových center, clusterům nebo hostitele, kteří podporují omezení 1 500 počítačů.
- Doporučujeme, aby pro účely hodnocení, byl počítače s vzájemné závislosti v rámci stejného projektu a hodnocení. V systému vCenter Server Ujistěte se, že závislé počítače jsou ve stejné složce, datacenter nebo clusteru pro hodnocení.


## <a name="create-a-project"></a>Vytvoření projektu

Vytvoření projektu Azure migraci v souladu s vaší požadavky:

1. Na portálu Azure vyberte **vytvořit prostředek**.
2. Vyhledejte **Azure Migrate** a ve výsledcích hledání vyberte službu **Azure Migrate (Preview)**. Potom vyberte **Vytvořit**.
3. Zadejte název projektu a předplatné Azure pro projekt.
4. Vytvořte novou skupinu prostředků.
5. Zadejte umístění, ve kterém chcete vytvořit projekt a potom vyberte **vytvořit**. Všimněte si, že můžete stále vyhodnotit virtuálních počítačů pro jiné cílové umístění. K umístění zadanému pro projekt se používá k ukládání metadat získané z virtuálních počítačů na místě.

## <a name="set-up-the-collector-appliance"></a>Nastavení kolekce zařízení

Azure Migrate vytvoří místní virtuální počítač, kterému se říká zařízení kolektoru. Tento virtuální počítač vyhledá virtuální počítače VMware na místě a metadata o nich odešle do služby Azure migrovat. Pokud chcete nastavit zařízení kolekce, stáhněte si soubor vajíčka, importujte je do místní instance serveru vCenter.

### <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Pokud máte více projektů, budete muset stáhnout kolekce zařízení pouze jednou k systému vCenter Server. Po stažení a nastavení zařízení, můžete spustit pro každý projekt, a zadáte projektu jedinečné ID a klíč.

1. V projektu Azure migrovat vyberte **Začínáme** > **Discover & hodnocení** > **zjišťovat počítače**.
2. V **zjistit počítače**, vyberte **Stáhnout**, chcete-li stáhnout soubor vajíčka.
3. V **zkopírujte projektu pověření**, zkopírujte ID a klíč pro projekt. Budete je potřebovat při konfiguraci kolektoru.


### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Zkontrolujte, jestli soubor vajíčka zabezpečené před nasazením:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.

2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Ujistěte se, že generované hodnoty hash odpovídá následující nastavení.

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
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Vytvoření virtuálního počítače kolektoru

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

## <a name="identify-the-id-and-key-for-each-project"></a>Určit ID a klíč pro každý projekt

Pokud máte více projektů, nezapomeňte určit ID a klíč pro každé z nich. Klíč musíte při spuštění kolekce ke zjišťování virtuálních počítačů.

1. V projektu, vyberte **Začínáme** > **Discover & hodnocení** > **zjišťovat počítače**.
2. V **zkopírujte projektu pověření**, zkopírujte ID a klíč pro projekt. 
    ![Zkopírujte pověření projektu](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>Nastavení úrovně statistiky vCenter
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

## <a name="run-the-collector-to-discover-vms"></a>Spuštění kolektoru pro vyhledání virtuálních počítačů

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


### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Doba zjišťování závisí na tom, kolik virtuálních počítačů vyhledáváte. U 100 virtuálních počítačů obvykle zjišťování dokončení kolem hodinu po dokončení spuštění kolekce. 

1. V projektu migrace Planner vyberte **spravovat** > **počítače**.
2. Zkontrolujte, jestli se virtuální počítače, které jste chtěli vyhledat, zobrazí na portálu.


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořte skupinu](how-to-create-a-group.md) pro vyhodnocení.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
