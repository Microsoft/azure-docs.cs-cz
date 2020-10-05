---
title: Práce s předchozí verzí Azure Migrate
description: Popisuje, jak pracovat s předchozí verzí Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 71fbd56c2566f008a096482755abbcdb174a987e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89001633"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Práce s předchozí verzí Azure Migrate

Tento článek poskytuje informace o práci s předchozí verzí Azure Migrate. 


Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Tato verze slouží k vytváření Azure Migrate projektů, zjišťování místních počítačů a orchestraci posouzení a migrace. [Přečtěte si další](whats-new.md) informace o novinkách v této verzi.
- **Předchozí verze**: Pokud používáte předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. V tomto článku se v projektech předchozí verze označují jako klasické projekty. Pokud stále potřebujete použít Azure Migrate projekty vytvořené v předchozí verzi, můžete to dělat a nemůžete udělat:
    - Už nemůžete vytvářet projekty migrace.
    - Doporučujeme, abyste neprováděli nové zjišťování.
    - Ke stávajícím projektům můžete pořád přistupovat.
    - Přesto můžete spustit posouzení.
    

## <a name="upgrade-between-versions"></a>Upgrade mezi verzemi

Nemůžete upgradovat projekty nebo součásti v předchozí verzi na novou verzi. Musíte [vytvořit nový Azure Migrate projekt](create-manage-projects.md)a přidat do něj [Nástroje pro posouzení a migraci](how-to-add-tool-first-time.md) . Pomocí kurzů můžete pochopit, jak jsou dostupné nástroje pro posouzení a migraci. Pokud byl pracovní prostor Log Analytics připojený k klasickému projektu, můžete ho po odstranění klasického projektu připojit k projektu aktuální verze.

## <a name="find-projects-from-previous-version"></a>Najít projekty z předchozí verze

Vyhledat projekty z předchozí verze následujícím způsobem:

1. V Azure Portal > **všechny služby**vyhledejte a vyberte **Azure Migrate**. 
2. Na řídicím panelu Azure Migrate existuje oznámení a odkaz pro přístup k původním Azure Migratem projektům.
3. Klikněte na odkaz pro otevření klasických projektů.

## <a name="delete-projects-from-previous-version"></a>Odstranit projekty z předchozí verze

Vyhledejte a odstraňte projekty z předchozí verze následujícím způsobem:

1. V Azure Portal > **všechny služby**vyhledejte a vyberte **Azure Migrate**. 
2. Na řídicím panelu Azure Migrate existuje oznámení a odkaz pro přístup k původním Azure Migratem projektům.
3. Klikněte na odkaz pro otevření klasických projektů.
4. Vyberte projekt, který chcete odstranit, a odstraňte ho. 


## <a name="create-an-assessment"></a>Vytvoření posouzení

Po zjištění virtuálních počítačů na portálu je můžete seskupit a vytvořit posouzení.

- Hned po zjištění virtuálních počítačů na portálu můžete okamžitě vytvořit místní posouzení.
- Pro posouzení na základě výkonu doporučujeme, abyste před vytvořením posouzení na základě výkonu čekali aspoň jeden den, abyste získali doporučení spolehlivé velikosti.

Vyhodnocování vytvořte následujícím způsobem:

1. Na stránce **Přehled** projektu klikněte na **Vytvořit posouzení**.
2. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.
3. Vytvořte skupinu a pojmenujte ji.
4. Vyberte virtuální počítače, které do ní chcete přidat.
5. Kliknutím na tlačítko **Vytvořit posouzení** vytvoříte skupinu a posouzení.
6. Jakmile bude posouzení hotové, zobrazíte ho v části **Přehled** > **Řídicí panel**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.

Pokud chcete aktualizovat existující interní hodnocení o nejnovější data o výkonu, můžete použít příkaz **Přepočítat** a hodnocení aktualizovat.

## <a name="review-an-assessment"></a>Kontrola posouzení 

Posouzení má tři fáze:

- Posouzení začíná analýzou vhodnosti, aby bylo možné zjistit, jestli jsou počítače v Azure kompatibilní.
- Odhady velikosti.
- Odhad měsíčních nákladů

Počítač se přesune pouze do pozdější fáze, pokud předá předchozí. Například pokud počítač nepovede kontrolu vhodnosti, je označený jako nevhodný pro Azure a změna velikosti a oceňování se neprovádí.


### <a name="review-azure-readiness"></a>Kontrola připravenosti pro Azure

Zobrazení Připravenost pro Azure v posouzení zobrazuje stav připravenosti jednotlivých virtuálních počítačů.

**Připravenosti** | **Stav** | **Podrobnosti**
--- | --- | ---
Připraveno pro Azure | Žádné problémy s kompatibilitou Počítač se dá migrovat tak, jak je, do Azure, a spustí se v Azure s plnou podporou Azure. | U virtuálních počítačů, které jsou pro migraci vhodné, doporučí Azure Migrate velikost virtuálního počítače v Azure.
Připraveno pro Azure s podmínkou | Počítač se může spustit v Azure, ale nemusí mít plnou podporu Azure. Například počítač se starší verzí Windows serveru, který se v Azure nepodporuje. | Azure Migrate vysvětluje problémy s připraveností a poskytuje kroky k nápravě.
Nepřipraveno pro Azure |  Virtuální počítač se nespustí v Azure. Pokud má virtuální počítač například disk, který je větší než 4 TB, nejde ho hostovat v Azure. | Azure Migrate vysvětluje problémy s připraveností a poskytuje kroky k nápravě.
Připravenost neznámá | Azure Migrate nemůžou identifikovat připravenost Azure, obvykle proto, že data nejsou k dispozici.


#### <a name="azure-vm-properties"></a>Vlastnosti virtuálního počítače Azure
Připravenost přihlíží k několika vlastnostem virtuálních počítačů, aby bylo možné zjistit, jestli virtuální počítač může běžet v Azure.


**Vlastnost** | **Podrobnosti** | **Připravenosti**
--- | --- | ---
**Typ spouštění** | Systém BIOS je podporován. Rozhraní UEFI se nepodporuje. | Podmíněně připravený, pokud je typ spouštění UEFI.
**Cores** | Počítače Core <= maximální počet jader (128) podporovaných virtuálním počítačem Azure.<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate bere v úvahu využité jádra.<br/>Pokud je v nastavení hodnocení určen faktor komfortu, je počet využitých jader vynásoben faktorem pohodlí.<br/><br/> Pokud není k dispozici žádná historie výkonu, Azure Migrate používá přidělená jádra bez použití faktoru pohodlí. | Připraveno, pokud je omezení menší nebo rovno.
**Memory (Paměť)** | Velikost paměti počítače <= maximální paměť (3892 GB v Azure M Series Standard_M128m &nbsp; <sup>2</sup>) pro virtuální počítač Azure. [Další informace](../virtual-machines/sizes.md).<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate bere v úvahu využitou paměť.<br/><br/>Pokud je určen faktor komfortu, vyhodnotí se využitá paměť podle faktoru pohodlí.<br/><br/> Pokud není k dispozici žádná historie, je přidělená paměť použita bez použití faktoru pohodlí.<br/><br/> | V rámci omezení je připravený.
**Disk úložiště** | Přidělená velikost disku musí být 4 TB (4096 GB) nebo méně.<br/><br/> Počet disků připojených k počítači musí být 65 nebo méně, včetně disku s operačním systémem. | V rámci omezení je připravený.
**Sítě** | K počítači musí být připojen 32 nebo méně síťových adaptérů. | V rámci omezení je připravený.

#### <a name="guest-operating-system"></a>Hostovaný operační systém

Spolu s vlastnostmi virtuálního počítače Azure Migrate taky v hostovaném operačním systému místního virtuálního počítače zjistit, jestli se virtuální počítač může spustit v Azure.

- Azure Migrate se považuje za operační systém určený v vCenter Server.
- Vzhledem k tomu, že zjišťování, které provádí Azure Migrate, je založené na zařízení, nemá způsob, jak ověřit, jestli je operační systém běžící ve virtuálním počítači stejný jako ten, který je zadaný v vCenter Server.

Použije se následující logika.

**Operační systém** | **Podrobnosti** | **Připravenosti**
--- | --- | ---
Windows Server 2016 a všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 R2 a všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 a všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2008 R2 a všechny SPs | Azure poskytuje plnou podporu.| Připraveno pro Azure
Windows Server 2008 (32 a 64-bit) | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2003, 2003 R2 | Nepodporovaná a pro podporu v Azure potřebujete [vlastní smlouvu o podpoře (CSA)](https://aka.ms/WSosstatement) . | Podmíněně připravené pro Azure zvažte upgrade operačního systému před migrací do Azure.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Mimo podporu. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure doporučujeme před migrací do Azure upgradovat operační systém.
Klient Windows 7, 8 a 10 | Azure poskytuje podporu [jenom pro předplatné sady Visual Studio.](../virtual-machines/windows/client-images.md) | Připraveno pro Azure s podmínkou
Stolní počítač s Windows 10 pro | Azure podporuje [práva hostování s více klienty.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Připraveno pro Azure s podmínkou
Windows Vista, XP Professional | Mimo podporu. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure doporučujeme před migrací do Azure upgradovat operační systém.
Linux | Azure tyto [operační systémy pro Linux](../virtual-machines/linux/endorsed-distros.md)schválí. Jiné operační systémy Linux můžou být spouštěny v Azure, ale před migrací do Azure doporučujeme upgradovat operační systém na schválenou verzi. | Je připravený pro Azure, pokud je verze schválená.<br/><br/>Podmíněně připravený, pokud verze není schválená.
Jiné operační systémy<br/><br/> Například Oracle Solaris, Apple macOS atd., FreeBSD atd. | Azure tyto operační systémy neschvaluje. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure doporučujeme nainstalovat podporovaný operační systém před migrací do Azure.  
Operační systém určený jako **jiný** v vCenter Server | Azure Migrate nemůže v tomto případě identifikovat operační systém. | Neznámá připravenost. Ujistěte se, že operační systém běžící v rámci virtuálního počítače je podporovaný v Azure.
32 – bitové operační systémy | Počítač se může spustit v Azure, ale Azure možná neposkytne plnou podporu. | Předem připravené na Azure, zvažte upgrade operačního systému počítače z 32 na 64 operačního systému na, než se migruje do Azure.


### <a name="review-sizing"></a>Kontrola velikosti

 Doporučení velikosti Azure Migrate závisí na kritériu určení velikosti určeném ve vlastnostech posouzení.

- Pokud je velikost na základě výkonu, doporučení pro velikost zvažuje historii výkonu virtuálních počítačů (procesor a paměť) a disků (IOPS a propustnost).
- Pokud je kritérium změny velikosti "as-premises", doporučení velikosti v Azure je založené na velikosti místního virtuálního počítače. Velikost disku vychází z typu úložiště určeného ve vlastnostech posouzení (výchozí je prémiové disky). Azure Migrate nebere v úvahu údaje o výkonu pro virtuální počítač a disky.

### <a name="review-cost-estimates"></a>Přehled odhadovaných nákladů

Odhad nákladů ukazuje celkové náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure spolu s podrobnostmi pro každý počítač.

- Odhadované náklady se počítají pomocí doporučení velikosti pro počítač virtuálního počítače a jeho disky a vlastnosti posouzení.
- Odhadované měsíční náklady na výpočetní kapacitu a úložiště jsou agregované pro všechny virtuální počítače dané skupiny.
- Odhad nákladů slouží ke spuštění místního virtuálního počítače jako virtuálních počítačů infrastruktury Azure jako služby (IaaS). Azure Migrate nebere v úvahu náklady na platformu jako službu (PaaS) nebo software jako služba (SaaS).

### <a name="review-confidence-rating-performance-based-assessment"></a>Kontrola hodnocení spolehlivosti (posouzení na základě výkonu)

Každé posouzení na základě výkonu je přidruženo k hodnocení spolehlivosti.

- Hodnocení spolehlivosti je v rozsahu od 1 hvězdičky po 5 hvězdiček (jedna hvězdička je nejnižší a pět hvězdiček nejvyšší).
- Hodnocení spolehlivosti je přiřazeno k posouzení na základě dostupnosti datových bodů potřebných k výpočtu posouzení.
- Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti není k dispozici pro místní posouzení "tak, jak je".

Pro určení velikosti na základě výkonu Azure Migrate potřebovat následující:
- Data o využití procesoru.
- Data paměti virtuálního počítače.
- U každého disku připojeného k virtuálnímu počítači potřebuje diskový IOPS a data propustnosti.
- Pro každý síťový adaptér připojený k virtuálnímu počítači Azure Migrate potřebuje síťový vstup/výstup.
- Pokud některý z výše uvedených možností není dostupný, doporučení pro velikost (a tudíž hodnocení spolehlivosti) nemusí být spolehlivá.


V závislosti na procentu dostupných datových bodů jsou možná hodnocení spolehlivosti shrnuta v tabulce.

**Dostupnost datových bodů** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Problémy hodnocení ovlivňující hodnocení spolehlivosti

Posouzení nemusí mít k dispozici všechny datové body z několika důvodů:

- Nevytvořili jste profil svého prostředí po dobu trvání posouzení. Pokud například vytvoříte hodnocení s trváním výkonu nastaveným na jeden den, musíte počkat alespoň jeden den po spuštění zjišťování nebo všechny datové body, které se mají shromáždit.
- Některé virtuální počítače se vypnuly během období, pro které se hodnocení vypočítalo. Pokud se některé virtuální počítače v rámci doby trvání vypnuly, Azure Migrate nemůžou shromažďovat údaje o výkonu za tuto dobu.
- Některé virtuální počítače se vytvořily mezi během období výpočtu hodnocení. Pokud například vytvoříte posouzení pomocí historie výkonu v posledním měsíci, ale v prostředí před týdenem vytvoříte několik virtuálních počítačů, historie výkonu nových virtuálních počítačů nebude platit pro celou dobu trvání.

> [!NOTE]
> Pokud je hodnocení spolehlivosti nějakého posouzení nižší než pět hvězdiček, počkejte alespoň jeden den, než zařízení profiluje prostředí, a pak hodnocení přepočítejte. Pokud nemůžete měnit velikost na základě výkonu, nemusí být spolehlivá. Pokud se nechcete přepočítat, doporučujeme, abyste přepnuli na jako místní změnu velikosti změnou vlastností posouzení.



## <a name="create-groups-using-dependency-visualization"></a>Vytváření skupin pomocí Vizualizace závislostí

Kromě ručního vytváření skupin můžete vytvořit skupiny pomocí Vizualizace závislostí.
- Tato metoda se obvykle používá, pokud chcete vyhodnotit skupiny s vyšší úrovní spolehlivosti před spuštěním posouzení.
- Vizualizace závislostí vám může pomáhat efektivně naplánovat migraci do Azure. Pomůže vám zajistit, aby nic nezůstalo a při migraci do Azure nedošlo k neočekávanému výpadku.
- Můžete zjistit všechny vzájemně závislé systémy, které potřebují migrovat dohromady, a určit, jestli operační systém stále obsluhuje uživatele, nebo je kandidátem na vyřazení z provozu místo migrace.
- Azure Migrate používá řešení Service Map v Azure Monitor k povolení Vizualizace závislostí.

> [!NOTE]
> Vizualizace závislostí není v Azure Government k dispozici.

K nastavení vizualizace závislosti přidružíte pracovní prostor Log Analytics k projektu Azure Migrate, nainstalujete agenty na počítače, pro které chcete vizualizovat závislosti, a pak vytvoříte skupiny pomocí informací o závislostech. 



### <a name="associate-a-log-analytics-workspace"></a>Přidružit Log Analytics pracovní prostor

Pokud chcete použít vizualizaci závislostí, přidružte pracovní prostor Log Analytics k projektu migrace. Pracovní prostor můžete vytvořit nebo připojit pouze ve stejném předplatném, ve kterém je vytvořen projekt migrace.

1. Pokud chcete k projektu připojit Log Analytics pracovní prostor, klikněte v **přehledu**> **Essentials**na **vyžaduje konfiguraci**.
2. Můžete vytvořit nový pracovní prostor nebo připojit stávající:
  - Chcete-li vytvořit nový pracovní prostor, zadejte název. Pracovní prostor se vytvoří v oblasti ve stejném [geografickém regionu Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migrace.
  - Když připojíte existující pracovní prostor, můžete vybrat ze všech dostupných pracovních prostorů ve stejném předplatném jako projekt migrace. V seznamu jsou uvedeny pouze ty pracovní prostory, které byly vytvořeny v [podporované Service map oblasti](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions). Pokud chcete připojit pracovní prostor, ujistěte se, že máte přístup čtenář k pracovnímu prostoru.

> [!NOTE]
> Pracovní prostor přidružený k projektu migrace nemůžete změnit.

### <a name="download-and-install-vm-agents"></a>Stažení a instalace agentů virtuálních počítačů

Po nakonfigurování pracovního prostoru můžete stáhnout a nainstalovat agenty na každý místní počítač, který chcete vyhodnotit. Kromě toho, pokud máte počítače bez připojení k Internetu, je potřeba stáhnout a nainstalovat [Log Analytics bránu](../azure-monitor/platform/gateway.md) .

1. V části **Přehled**klikněte na možnost **Spravovat**  >  **počítače**a vyberte požadovaný počítač.
2. Ve sloupci **závislosti** klikněte na možnost **instalovat agenty**.
3. Na stránce **závislosti** Stáhněte a nainstalujte Microsoft Monitoring Agent (MMA) a na každý virtuální počítač, který chcete vyhodnotit, agenta závislostí.
4. Zkopírujte ID a klíč pracovního prostoru. Budete je potřebovat při instalaci MMA na místním počítači.

> [!NOTE]
> K automatizaci instalace agentů můžete použít nástroj pro nasazení, jako je například Configuration Manager nebo partnerský nástroj, například [Intigua](https://www.intigua.com/intigua-for-azure-migration), který poskytuje řešení nasazení agenta pro Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Instalace agenta MMA na počítač s Windows

Instalace agenta na počítač s Windows:

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **licenční podmínky** **kliknutím na Souhlasím** přijměte licenci.
3. V části **cílová složka**ponechejte nebo upravte výchozí instalační složku > **Další**.
4. V **Možnosti nastavení agenta**vyberte **Azure Log Analytics**  >  **Další**.
5. Kliknutím na **Přidat** přidejte nový pracovní prostor Log Analytics. Vložte do ID a klíče pracovního prostoru, který jste zkopírovali z portálu. Klikněte na **Next** (Další).

Agenta můžete nainstalovat z příkazového řádku nebo pomocí automatizované metody, jako je Configuration Manager. [Přečtěte si další informace](../azure-monitor/platform/log-analytics-agent.md#installation-options) o použití těchto metod k instalaci agenta MMA.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Instalace agenta MMA do počítače se systémem Linux

Instalace agenta na počítač se systémem Linux:

1. Přeneste příslušný balíček (x86 nebo x64) do počítače se systémem Linux pomocí spojovacího bodu služby a SFTP.
2. Nainstalujte sadu prostředků pomocí argumentu--Install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Přečtěte si další informace](../azure-monitor/platform/agents-overview.md#supported-operating-systems) o seznamu operačních systémů Linux, které podporuje MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Instalace agenta MMA na počítači monitorovaném nástrojem Operations Manager

Pro počítače monitorované nástrojem System Center Operations Manager 2012 R2 nebo novějším není potřeba instalovat agenta MMA. Service Map se integruje s Operations Manager MMA ke shromáždění potřebných dat závislostí. [Další informace](../azure-monitor/insights/service-map-scom.md#prerequisites). Je potřeba nainstalovat agenta závislostí.

### <a name="install-the-dependency-agent"></a>Instalace agenta závislostí

1. Chcete-li nainstalovat agenta závislostí na počítač se systémem Windows, dvakrát klikněte na instalační soubor a postupujte podle pokynů průvodce.
2. Chcete-li nainstalovat agenta závislostí na počítač se systémem Linux, nainstalujte jako kořenový adresář pomocí následujícího příkazu:

    ```sh InstallDependencyAgent-Linux64.bin```

- Přečtěte si další informace o [podpoře agenta závislostí](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) pro operační systémy Windows a Linux.
- [Přečtěte si další informace](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent) o tom, jak můžete pomocí skriptů nainstalovat agenta závislostí.

>[!NOTE]
> Článek Azure Monitor pro virtuální počítače, na který se odkazuje, obsahuje přehled systémových požadavků a metod nasazení agenta závislostí, které se vztahují také na řešení Service Map.

### <a name="create-a-group-with-dependency-mapping"></a>Vytvoření skupiny s mapováním závislostí

1. Po instalaci agentů přejděte na portál a klikněte na **Spravovat**  >  **počítače**.
2. Vyhledejte počítač, do kterého jste nainstalovali agenty.
3. Sloupec **závislosti** pro tento počítač by se teď měl zobrazit jako **závislosti zobrazení**. Kliknutím na sloupec zobrazíte závislosti počítače.
4. Mapa závislostí pro počítač zobrazuje následující podrobnosti:
    - Příchozí (klienti) a odchozí (servery) TCP připojení k/z počítače
        - Závislé počítače, které nemají nainstalovaného agenta MMA a závislostí, se seskupují podle čísel portů.
        - Závislé počítače, které mají nainstalovaného agenta MMA a závislost, se zobrazují jako samostatná pole.
    - Procesy běžící v počítači můžete rozbalit pole každého počítače a zobrazit tak procesy.
    - Zobrazí se vlastnosti počítače, včetně plně kvalifikovaného názvu domény, operačního systému, adresy MAC. Kliknutím na každé pole počítače můžete zobrazit podrobnosti.

4. Závislosti pro různá časová období můžete zobrazit kliknutím na časový interval v popisku časového rozsahu. Ve výchozím nastavení je rozsah hodina. Můžete změnit časový rozsah nebo zadat počáteční a koncové datum a dobu trvání.

   > [!NOTE]
   >    Je podporován časový rozsah až hodinu. Použijte protokoly Azure Monitor k [dotazování na data závislostí](./how-to-create-group-machine-dependencies.md) po delší dobu.

5. Po identifikaci závislých počítačů, které chcete seskupovat, můžete pomocí Ctrl + kliknutí vybrat na mapě více počítačů a kliknout na **Seskupit počítače**.
6. Zadejte název skupiny. Ověřte, zda jsou závislé počítače zjištěny Azure Migrate.

    > [!NOTE]
    > Pokud Azure Migrate nezjistí závislý počítač, nemůžete ho přidat do skupiny. Chcete-li tyto počítače přidat do skupiny, je třeba spustit proces zjišťování znovu se správným oborem v vCenter Server a ověřit, zda je počítač zjištěn Azure Migrate.  

7. Pokud chcete pro tuto skupinu vytvořit vyhodnocení, zaškrtněte políčko pro vytvoření nového vyhodnocení pro skupinu.
8. Kliknutím na tlačítko **OK** skupinu uložte.

Po vytvoření skupiny se doporučuje nainstalovat agenty na všech počítačích skupiny a skupinu Upřesnit tím, že Vizualizujte závislost celé skupiny.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Dotazování dat závislosti z protokolů Azure Monitor

Data závislostí zachycená Service Map jsou k dispozici pro dotazování v pracovním prostoru Log Analytics přidruženém k vašemu projektu Azure Migrate. [Přečtěte si další informace](../azure-monitor/insights/service-map.md#log-analytics-records) o Service map tabulek dat pro dotazování v protokolech Azure monitor. 

Spuštění dotazů Kusto:

1. Po instalaci agentů přejděte na portál a klikněte na **Přehled**.
2. V části **Přehled**přejděte na oddíl **základy** projektu a klikněte na název pracovního prostoru zadaný vedle možnosti **pracovní prostor OMS**.
3. Na stránce Log Analytics pracovní prostor klikněte na **Obecné**  >  **protokoly**.
4. Napište dotaz pro shromažďování dat závislosti pomocí protokolů Azure Monitor. V další části najdete Ukázkové dotazy.
5. Spusťte dotaz kliknutím na spustit. 

[Přečtěte si další informace](../azure-monitor/log-query/get-started-portal.md) o tom, jak zapisovat dotazy Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Ukázka dotazů v protokolu Azure Monitor

Následují ukázkové dotazy, které můžete použít k extrakci dat závislostí. Můžete upravit dotazy pro extrakci preferovaných datových bodů. Vyčerpávající seznam polí v datových záznamech závislostí je k dispozici [zde](../azure-monitor/insights/service-map.md#log-analytics-records). [Tady](../azure-monitor/insights/service-map.md#sample-log-searches)najdete další Ukázkové dotazy.

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Sumarizace příchozích připojení na sadu počítačů

Záznamy v tabulce pro metriky připojení, VMConnection, reprezentují jednotlivá fyzická síťová připojení. Několik fyzických síťových připojení se seskupí do logického připojení. [Přečtěte si další informace](../azure-monitor/insights/service-map.md#connections) o tom, jak se data fyzického připojení k síti agreguje do jednoho logického záznamu v VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Shrnutí objemu dat odesílaných a přijatých při příchozích připojeních mezi sadou počítačů

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Další kroky
[Přečtěte si o](migrate-services-overview.md) nejnovější verzi Azure Migrate.