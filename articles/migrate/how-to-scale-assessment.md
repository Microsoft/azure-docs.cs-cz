---
title: Škálování zjišťování a posouzení pomocí Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak posoudit velký počet místních počítačů pomocí služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: 4bcb6734c33d70e4045860a2c0f0acfedfa06eff
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215175"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Zkoumání a vyhodnocení rozsáhlých prostředí VMware

Azure Migrate má limit 1 500 počítačů pro každý projekt, tento článek popisuje, jak posoudit velký počet místních virtuálních počítačů (VM) s použitím [Azure Migrate](migrate-overview.md).   

## <a name="prerequisites"></a>Požadavky

- **VMware**: virtuální počítače, které chcete migrovat musí být spravované přes vCenter Server verze 5.5, 6.0 nebo 6.5. Kromě toho potřebujete jednoho hostitele ESXi ve verzi 5.0 nebo novější. Chcete-li nasadit virtuální počítač kolektoru.
- **účet vCenter**: potřebujete účet jen pro čtení pro přístup k systému vCenter Server. Azure Migrate ho použije ke zjištění místních virtuálních počítačů.
- **Oprávnění**: V systému vCenter Server, musíte mít oprávnění k vytvoření virtuálního počítače pomocí importu souboru ve formátu OVA.
- **Nastavení statistiky**: nastavení statistiky systému vCenter Server by měla být nastavená na úroveň 3, před zahájením nasazení. Pokud úroveň je nižší než 3, posouzení bude fungovat, ale nebudou shromažďovat údaje o výkonu pro úložiště a sítě. Doporučené velikosti v tomto případě bude zakládat na údaje o výkonu pro využití procesoru a paměti a konfigurační data pro disk a síťové adaptéry.


### <a name="set-up-permissions"></a>Nastavení oprávnění

Azure Migrate k automatickému zjišťování virtuálních počítačů pro účely posouzení potřebuje přístup k serverům VMware. VMware účet musí mít následující oprávnění:

- Typ uživatele: Alespoň uživatel jen pro čtení
- Oprávnění: Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only
- Podrobnosti: Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v tomto datacentru.
- Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli Žádný přístup s objektem Rozšířit na podřízený objekt.

Pokud nasazujete v prostředí s tenanty, tady je jeden způsob, jak nastavit tuto možnost:

1.  Vytvořit uživatele na klienta a pomocí [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), přiřaďte oprávnění jen pro čtení pro všechny Virtuálního počítače, který patří do konkrétního tenanta. Potom použijte své přihlašovací údaje pro zjišťování. RBAC se zajistí, že odpovídající vCenter uživatel bude mít přístup k tenantovi pouze konkrétní virtuální počítač.
2. Můžete nastavit RBAC pro uživatele jiného tenanta, jak je popsáno v následujícím příkladu pro uživatele č. 1 a 2 uživatele:

    - V **uživatelské jméno** a **heslo**, určete pověření účtu jen pro čtení, který kolektor použije ke zjištění virtuálních počítačů v
    - Datacenter1 – udělení oprávnění jen pro čtení na 1 uživatele a uživatele č. 2. Těmito oprávněními, aby všechny podřízené objekty, není rozšířit, protože budete nastavit oprávnění pro konkrétního virtuálního počítače.

      - VM1 (Tenanta č. 1) (pouze oprávnění ke čtení uživatelů č. 1)
      - VM2 (Tenanta č. 1) (pouze oprávnění ke čtení uživatelů č. 1)
      - VM3 (Tenanta č. 2) (pouze oprávnění ke čtení uživatelů č. 2)
      - VM4 (Tenanta č. 2) (pouze oprávnění ke čtení uživatelů č. 2)

   - Pokud provádíte zjišťování pomocí přihlašovacích údajů uživatele č. 1, budou zjištěny pouze VM1 a VM2.

## <a name="plan-your-migration-projects-and-discoveries"></a>Plánování migrace projektů a zjišťování

Jeden kolektor Azure Migrate podporuje zjišťování z více servery vCenter (jednu po druhé) a také podporuje zjišťování tak, aby více projekty migrace (jednu po druhé). Kolektor funguje v spustit a zapomenout modelu, po dokončení zjišťování můžete použít stejné kolekce shromažďovat data z různých vCenter serveru nebo odeslání do projektu různé migrace.

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

Pokud máte více servery vCenter ve vašem prostředí a celkový počet virtuálních počítačů je menší než 1 500, můžete použít jeden kolektor a jedna migrace projektu ke zjišťování všech virtuálních počítačů napříč všemi servery vCenter. Protože kolektoru zjistí jednomu vCenter serveru současně, můžete spustit stejnou kolekcí pro všechny servery, vCenter, jeden po druhém a kolektoru přejděte na stejný projekt migrace. Po dokončení všech zjišťování si můžete vytvořit posouzení pro počítače.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Více vCenter servery s více než 1 500 virtuálních počítačů

Pokud máte více servery vCenter s méně než 1 500 virtuálních počítačů na vCenter serveru, ale více než 1 500 virtuálních počítačů přes všechny slouží vCenter, musíte vytvořit více projekty migrace (jeden projekt migrace může obsahovat jenom 1 500 virtuálních počítačů). Můžete dosáhnout vytvořením projektu migrace na vCenter Server a rozdělení zjišťování. Jeden kolektor můžete použít ke zjištění každého systému vCenter Server (jednu po druhé). Pokud chcete zjišťování spuštění ve stejnou dobu, můžete také nasadit více zařízení a při paralelním spuštění zjišťování.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Více než 1 500 počítačů v jedné systému vCenter Server

Pokud máte víc než 1500 virtuálních počítačů v jedné systému vCenter Server, budete muset zjišťování rozdělit do několika projekty migrace. Pokud chcete rozdělit zjišťování, můžete využít pole oboru v zařízení a zadejte hostitele, cluster, složka nebo datového centra, který jste chtěli vyhledat. Například, pokud máte dvě složky v systému vCenter Server, jeden s 1000 virtuálních počítačů (složku1) a druhý s 800 virtuálních počítačů (slozka2), můžete použít jeden kolektor a provést dvě zjišťování. V první zjišťování, můžete zadat složku1 jako obor a nasměrovat ho na první projekt migrace po dokončení první zjišťování můžete používat stejné kolekce, změnit její obor podrobností projektu slozka2 a migrace na druhý projekt migrace a Proveďte druhou zjišťování.

### <a name="multi-tenant-environment"></a>Prostředí s více tenanty

Pokud máte prostředí, které se sdílejí napříč tenanty a nechcete ke zjištění virtuálních počítačů z jednoho tenanta v jiném tenantovi předplatné, můžete použít pole oboru v zařízení kolektoru k určení rozsahu zjišťování. Pokud klienti sdílejí hostitele, vytvoření přihlašovacích údajů, který má přístup jen pro čtení na pouze virtuální počítače patří do konkrétního tenanta a použít tyto přihlašovací údaje v zařízení kolektoru a zadejte rozsah jako hostitele, které chcete zjišťování. Alternativně můžete také vytvořit složky v systému vCenter Server (Řekněme složku1 pro tenant1 a slozka2 pro tenant2), v rámci sdílené hostitele, virtuální počítače pro tenant1 do složku1 a tenant2 přesunout do slozka2 a odpovídajícím způsobem určit obor zjišťování v kolektoru zadáním příslušné složky.

## <a name="discover-on-premises-environment"></a>Zjišťování místního prostředí

Jakmile budete připraveni s plánem, potom můžete spustit zjišťování místních virtuálních počítačů:

### <a name="create-a-project"></a>Vytvoření projektu

Vytvořte projekt Azure Migrate v souladu s vaší požadavky:

1. Na webu Azure Portal, vyberte **vytvořit prostředek**.
2. Vyhledejte **Azure Migrate** a ve výsledcích hledání vyberte službu **Azure Migrate (Preview)**. Potom vyberte **Vytvořit**.
3. Zadejte název projektu a předplatné Azure pro projekt.
4. Vytvořte novou skupinu prostředků.
5. Zadejte umístění, ve kterém chcete vytvořit projekt a pak vyberte **vytvořit**. Všimněte si, že můžete přesto posoudit vašich virtuálních počítačů pro jiné cílové umístění. Umístění vybrané pro tento projekt slouží k uložení metadat shromážděných z místních virtuálních počítačů.

### <a name="set-up-the-collector-appliance"></a>Nastavení zařízení kolektoru

Azure Migrate vytvoří místní virtuální počítač, kterému se říká zařízení kolektoru. Tento virtuální počítač vyhledá místní virtuální počítače VMware a odešle jejich metadata do služby Azure Migrate. Nastavení zařízení kolektoru, stáhnout soubor OVA a importovat ho do místní instanci serveru vCenter.

#### <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Pokud máte více projektů, budete muset stažení zařízení kolektoru pouze jednou k vCenter serveru. Po stažení a nastavení na zařízení, spusťte pro každý projekt a zadáte jedinečné ID a klíč projektu.

1. V projektu Azure Migrate vyberte **Začínáme** > **zjistit a posoudit** > **zjistit počítače**.
2. V **zjistit počítače**vyberte **Stáhnout**, chcete-li stáhnout soubor OVA.
3. V **kopírování přihlašovacích údajů projektu**, zkopírujte ID a klíč projektu. Budete je potřebovat při konfiguraci kolektoru.


#### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Zkontrolujte, zda soubor OVA zabezpečené před jejím nasazením:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.

2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Ujistěte se, že generované hodnoty hash shoduje s následujícím nastavením.

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
Tady je seznam čítačů výkonu, které byly shromážděny během zjišťování. Tyto čítače jsou ve výchozím nastavení k dispozici na různých úrovních v systému vCenter Server.

Doporučujeme nastavit nejvyšší běžné úroveň (3) pro úroveň statistiky tak, aby všechny čítače se shromažďují správně. Je nutné nastavit na nižší úrovni vCenter, může se zbytkem nastavena na hodnotu 0 úplně, shromažďují pouze několik čítače. Posouzení pak může zobrazit, neúplná data.

Následující tabulka obsahuje také výsledky posouzení, které budou mít vliv na konkrétní čítač, pokud nejsou zjištěny.

| Čítač                                 | Úroveň | Úroveň podle zařízení | Hodnocení dopadu                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| CPU.Usage.average                       | 1     | Není k dispozici               | Doporučené velikosti virtuálních počítačů a náklady         |
| mem.usage.average                       | 1     | Není k dispozici               | Doporučené velikosti virtuálních počítačů a náklady         |
| virtualDisk.read.average                | 2     | 2                | Velikost disku, náklady na úložiště a velikost virtuálního počítače |
| virtualDisk.write.average               | 2     | 2                | Velikost disku, náklady na úložiště a velikost virtuálního počítače |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | Velikost disku, náklady na úložiště a velikost virtuálního počítače |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | Velikost disku, náklady na úložiště a velikost virtuálního počítače |
| net.received.average                    | 2     | 3                | Cena za virtuální počítače velikosti a sítě             |
| net.transmitted.average                 | 2     | 3                | Cena za virtuální počítače velikosti a sítě             |

> [!WARNING]
> Pokud jste právě nastavili vyšší úroveň statistiky, ji budou trvat až jeden den generovat čítače výkonu. Proto doporučujeme spustit zjišťování po jednom dni.

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

Doba zjišťování závisí na tom, kolik virtuálních počítačů vyhledáváte. Obvykle pro 100 virtuálních počítačů, zjišťování přibližně hodinu po doběhnutí kolektoru dokončí.

1. V projektu Azure Migrate, vyberte **spravovat** > **počítače**.
2. Zkontrolujte, jestli se virtuální počítače, které jste chtěli vyhledat, zobrazí na portálu.


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořte skupinu](how-to-create-a-group.md) pro posouzení.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
