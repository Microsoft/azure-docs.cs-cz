---
title: Škálování zjišťování a posouzení pomocí Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak posoudit velký počet místních počítačů pomocí služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: raynew
ms.openlocfilehash: b0965d50781ac3bb6c62338a2c6f17317306d249
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835535"
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

Podle počtu virtuálních počítačů, které chcete zjišťovat, můžete vytvořit více projektů a nasadit více zařízení ve vašem prostředí. Zařízení může být připojena k jedné systému vCenter Server a jeden projekt (není-li zastavit zjišťování a znovu spustit).

V případě jednorázového zjišťování (teď zastaralé), zjišťování funguje v spustit a zapomenout modelu, po dokončení zjišťování, můžete použít stejné kolekce shromažďovat data z různých vCenter serveru nebo odeslání do projektu různé migrace.

> [!NOTE]
> Jednorázové zjišťování zařízení je nyní zastaralý a tato metoda spoléhal na vCenter serveru nastavení statistiky dostupnosti bodu dat výkonu shromážděných čítačů průměrný výkon, které umožňují snížení velikosti virtuálních počítačů pro migraci do Azure. Doporučuje se přesunout do tohoto jednorázového zjišťování zařízení.

Plán zjišťování a posouzení podle následující omezení:

| **Entity** | **Limit pro počítače** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Zjišťování  | 1,500             |
| Posouzení | 1,500             |

Mějte tyto aspekty plánování:

- Když použijete zjišťování pomocí Azure Migrate collector, můžete nastavit obor zjišťování do složky na serveru vCenter, datacentrum, cluster nebo hostitele.
- Provést více než jedno zjišťování ze stejného serveru vCenter, zkontrolujte v systému vCenter Server, které jsou virtuální počítače, které chcete zjišťovat složky, datovými centry, clustery nebo hostitele, kteří podporují omezení 1 500 počítačů.
- Doporučujeme, abyste pro účely posouzení ponechat počítačů pomocí vzájemných závislostí v rámci jednoho projektu a posouzení. V systému vCenter Server Ujistěte se, že závislé počítače jsou ve stejném složku, datové centrum nebo cluster pro posouzení.

V závislosti na vašem scénáři můžete rozdělit co jste se dozvěděli podle předepsaného níže:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Více vCenter servery s méně než 1 500 virtuálních počítačů
Pokud máte více servery vCenter ve vašem prostředí a celkový počet virtuálních počítačů je menší než 1 500, můžete použít následující přístup založený na váš scénář:

**Průběžná zjišťování:** v případě průběžná zjišťování, může být jeden zařízení připojena k pouze jeden projekt. Proto budete muset nasadit jeden zařízení pro každou z vCenter servery a pak vytvořte jeden projekt pro každé zařízení a aktivační události zjišťování odpovídajícím způsobem.

**Jednorázové zjišťování (teď zastaralé):** jeden kolektor a jedna migrace projektu můžete použít ke zjišťování všech virtuálních počítačů napříč všemi servery vCenter. Protože jednorázově kolektoru zjistí jednomu vCenter serveru současně, můžete spustit stejnou kolekcí pro všechny servery, vCenter, jeden po druhém a kolektoru přejděte na stejný projekt migrace. Po dokončení všech zjišťování si můžete vytvořit posouzení pro počítače.


### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Více vCenter servery s více než 1 500 virtuálních počítačů

Pokud máte více servery vCenter s méně než 1 500 virtuálních počítačů na vCenter serveru, ale více než 1 500 virtuálních počítačů napříč všemi servery vCenter, musíte vytvořit více projekty migrace (jeden projekt migrace může obsahovat jenom 1 500 virtuálních počítačů). Můžete dosáhnout vytvořením projektu migrace na vCenter Server a rozdělení zjišťování.

**Průběžná zjišťování:** budete muset vytvořit více kolekcí zařízení (jeden pro každý systém vCenter Server) a připojení každého zařízení k projektu a aktivační události zjišťování odpovídajícím způsobem.

**Jednorázové zjišťování (teď zastaralé):** jeden kolektor můžete použít ke zjištění každého systému vCenter Server (jednu po druhé). Pokud chcete zjišťování spuštění ve stejnou dobu, můžete také nasadit více zařízení a při paralelním spuštění zjišťování.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Více než 1 500 počítačů v jedné systému vCenter Server

Pokud máte víc než 1500 virtuálních počítačů v jedné systému vCenter Server, budete muset zjišťování rozdělit do několika projekty migrace. Pokud chcete rozdělit zjišťování, můžete využít pole oboru v zařízení a zadejte hostitele, cluster, složka nebo datovém centru, které jste chtěli vyhledat. Například, pokud máte dvě složky v systému vCenter Server, jeden s 1000 virtuálních počítačů (složku1) a druhý s 800 virtuálních počítačů (slozka2), rozsah pole můžete použít k rozdělení zjišťování mezi tyto složky.

**Průběžná zjišťování:** v takovém případě budete muset vytvořit dvě kolekce zařízení, což se pro první kolekce, zadejte rozsah jako složku1 a připojte ho k první projekt migrace. Můžete paralelně spustit zjišťování slozka2 použití druhého zařízení kolektoru a jejím připojení k druhé projekt migrace.

**Jednorázové zjišťování (teď zastaralé):** stejné kolekce můžete použít k aktivaci obou zjišťování. V první zjišťování, můžete zadat složku1 jako obor a nasměrovat ho na první projekt migrace po dokončení první zjišťování můžete používat stejné kolekce, změnit její obor podrobností projektu slozka2 a migrace na druhý projekt migrace a Proveďte druhou zjišťování.

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
2. V **zjistit počítače**, klikněte na tlačítko **Stáhnout** ke stažení na zařízení.

    Zařízení Azure Migrate komunikuje pomocí systému vCenter Server a průběžně profily v místním prostředí pro shromažďování dat o využití v reálném čase pro každý virtuální počítač. Shromažďuje ve špičce čítače pro jednotlivé metriky (využití procesoru, využití paměti atd.). Tento model není závislý na nastavení statistiky vCenter Server pro shromažďování dat o výkonu. Kdykoli ze zařízení můžete zastavit průběžnou profilaci.

    > [!NOTE]
    > Jednorázové zjišťování zařízení je nyní zastaralý a tato metoda spoléhal na vCenter serveru nastavení statistiky dostupnosti bodu dat výkonu shromážděných čítačů průměrný výkon, které umožňují snížení velikosti virtuálních počítačů pro migraci do Azure.

    **Rychlé provést synchronizaci dříve:** s průběžná zjišťování zařízení, jakmile zjišťování je dokončí (trvá několik hodin v závislosti na počtu virtuálních počítačů), můžete okamžitě vytvořit posouzení. Protože shromažďování dat výkonu spustí, když zahájíte zjišťování, pokud chcete pro rychlé provést synchronizaci dříve, měli vybrat kritérium určení velikosti v posouzení jako *jako místní*. Vyhodnocení na základě výkonu doporučujeme počkejte alespoň jeden den a poté rutinního zjišťování tak, aby Získejte doporučení k reliable velikosti.

    Mějte na paměti, že zařízení shromažďuje data o výkonu pouze průběžně a nezjistí žádné změny konfigurace v místním prostředí (tj. přidání nebo odstranění virtuálního počítače, přidání disku atd.). Pokud dojde ke změně konfigurace v místním prostředí, následujícím způsobem můžete zajistit, že se změny projeví na portálu:

    - Přidání položek (virtuální počítače, disky, jádra atd.): Pokud chcete, aby se tyto změny projevily na webu Azure Portal, můžete na zařízení zastavit zjišťování a pak ho spustit znovu. Tím se zajistí, že se změny aktualizují v projektu Azure Migrate.

    - Odstranění virtuálních počítačů: Vzhledem ke způsobu, jakým je zařízení navržené, se odstranění virtuálních počítačů neprojeví ani v případě, že zastavíte a znovu spustíte zjišťování. Důvodem je, že se data z dalších zjišťování připojují ke starším zjišťováním, a nepřepisují se. V takovém případě můžete virtuální počítač na portálu jednoduše ignorovat tak, že ho odeberete ze své skupiny a přepočítáte posouzení.

3. V **kopírování přihlašovacích údajů projektu**, zkopírujte ID a klíč projektu. Budete je potřebovat při konfiguraci kolektoru.


#### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Zkontrolujte, zda soubor OVA zabezpečené před jejím nasazením:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.

2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Ujistěte se, že generované hodnoty hash shoduje s následujícím nastavením.

#### <a name="continuous-discovery"></a>Průběžné zjišťování

Pro soubory OVA verze 1.0.10.4

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

#### <a name="one-time-discovery-deprecated-now"></a>Jednorázové zjišťování (teď zastaralé)

Pro soubory OVA verze 1.0.9.15 (vydaná 10/23/2018)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

Pro soubory OVA verze 1.0.9.14 (vydaná 8/24/2018)

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

Kolektor se průběžně profilu v místním prostředí a bude posílat data o výkonu v intervalu hodiny. Za hodinu po zahájení zjišťování můžete zkontrolovat počítače na portálu. Před vytvořením interního hodnocení založeného na výkonu virtuálních počítačů důrazně doporučujeme počkat aspoň jeden den.

1. V projektu migrace klikněte na **Spravovat** > **počítače**.
2. Zkontrolujte, jestli se virtuální počítače, které jste chtěli vyhledat, zobrazí na portálu.

### <a name="data-collected-from-on-premises-environment"></a>Data shromážděná z místního prostředí

Zařízení kolektoru zjistí následující konfigurační data o vybrané virtuální počítače.

1. Název zobrazení virtuálních počítačů (na serveru vCenter)
2. Cesta inventáře Virtuálního počítače (hostitele nebo složku v systému vCenter)
3. IP adresa
4. Adresa MAC
5. Operační systém
5. Počet jader, disků, síťových adaptérů
6. Velikost paměti, velikosti disků
7. A z čítačů výkonu virtuálních počítačů, disku a sítě, jak je uvedeno v následující tabulce.

Zařízení kolektoru shromažďuje následující čítače výkonu pro každý virtuální počítač z hostitele ESXi v intervalech 20 sekund. Tyto čítače jsou čítačů vCenter a i když terminologii říká průměr, 20 sekund ukázky jsou čítačů v reálném čase. Zařízení potom zobrazí – až 20 sekund ukázky vytvořte jeden datový bod pro každých 15 minut tak, že vyberete hodnotu ve špičce ukázky 20 sekund a odesílá je do Azure. Data o výkonu pro virtuální počítače se spustí poté jsou dostupné na portálu, dvě hodiny po mají spustila zjišťování. Důrazně se doporučuje počkejte alespoň den před vytvořením posouzení založená na výkon získat přesné doporučení pro správné velikosti. Pokud chcete pro rychlé provést synchronizaci dříve, můžete vytvořit posouzení s kritérium určení velikosti jako *jako místní* které nebude považovat za data o výkonu pro určení správné velikosti.

**Čítač** |  **Dopad na posouzení**
--- | ---
CPU.Usage.average | Doporučené velikosti virtuálních počítačů a náklady  
mem.usage.average | Doporučené velikosti virtuálních počítačů a náklady  
virtualDisk.read.average | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
virtualDisk.write.average | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
virtualDisk.numberReadAveraged.average | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
virtualDisk.numberWriteAveraged.average | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
net.received.average | Vypočítá velikost virtuálního počítače                          
net.transmitted.average | Vypočítá velikost virtuálního počítače     

> [!WARNING]
> Metoda jednorázově opírající se o nastavení statistiky vCenter serveru pro shromažďování dat výkonu je nyní zastaralá.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořte skupinu](how-to-create-a-group.md) pro posouzení.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
