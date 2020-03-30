---
title: Práce s předchozí verzí Migrace Azure
description: Popisuje, jak pracovat s předchozí verzí Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c9cea52e04a991e6e3ac64426f0443939f8aaa3a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77914377"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Práce s předchozí verzí Migrace Azure

Tento článek obsahuje informace o práci s předchozí verzí Migrace Azure.


Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Tato verze slouží k vytváření projektů Migrace Azure, zjišťování místních počítačů a orchestraci hodnocení a migrace. [Přečtěte si další informace](whats-new.md) o novince v této verzi.
- **Předchozí verze**: Pokud používáte předchozí verzi Migrace Azure (bylo podporováno jenom hodnocení místních virtuálních počítačích VMware), měli byste teď použít aktuální verzi. Pokud stále potřebujete používat projekty Migrace Azure vytvořené v předchozí verzi, můžete a nemůžete dělat toto:
    - Již nelze vytvářet projekty migrace.
    - Doporučujeme neprovádět nové objevy.
    - Stále můžete přistupovat k existujícím projektům.
    - Stále můžete spustit hodnocení.
    

## <a name="upgrade-between-versions"></a>Upgrade mezi verzemi

Projekty nebo součásti v předchozí verzi nelze upgradovat na novou verzi. Musíte [vytvořit nový projekt Migrace Azure](how-to-add-tool-first-time.md)a přidat do něj nástroje pro hodnocení a migraci.

## <a name="find-projects-from-previous-version"></a>Hledání projektů z předchozí verze

Najděte projekty z předchozí verze takto:

1. Na portálu Azure > **Všechny služby**vyhledejte a vyberte **Azure Migrate**. 
2. Na řídicím panelu Azure Migrate je oznámení a odkaz pro přístup ke starým projektům Migrace Azure.
3. Kliknutím na odkaz otevřete projekty v1.


## <a name="create-an-assessment"></a>Vytvoření posouzení

Po zjištění virtuálních jevů na portálu je seskupíte a vytvoříte hodnocení.

- Můžete okamžitě vytvořit jako místní hodnocení ihned po virtuálních počítačích jsou zjištěny na portálu.
- Pro hodnocení na základě výkonu doporučujeme počkat alespoň jeden den před vytvořením hodnocení na základě výkonu, chcete-li získat doporučení spolehlivé velikosti.

Vytvořte hodnocení takto:

1. Na stránce **Přehled** projektu klikněte na **Vytvořit posouzení**.
2. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.
3. Vytvořte skupinu a pojmenujte ji.
4. Vyberte virtuální počítače, které do ní chcete přidat.
5. Kliknutím na tlačítko **Vytvořit posouzení** vytvoříte skupinu a posouzení.
6. Po vytvoření hodnocení jej zobrazte v **panelu přehled** > **.**
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.

Pokud chcete aktualizovat existující interní hodnocení o nejnovější data o výkonu, můžete použít příkaz **Přepočítat** a hodnocení aktualizovat.

## <a name="review-an-assessment"></a>Přezkoumat posouzení 

Hodnocení má tři fáze:

- Posouzení začíná analýzou vhodnosti, abyste zjistili, jestli jsou počítače kompatibilní v Azure.
- Odhady velikosti.
- Měsíční odhad nákladů.

Stroj se přesune do pozdější fáze pouze v případě, že projde předchozí fází. Například pokud počítač selže kontrolu vhodnosti, je označen jako nevhodné pro Azure a velikosti a náklady se neprovádí.


### <a name="review-azure-readiness"></a>Kontrola připravenosti Azure

Zobrazení Připravenost pro Azure v posouzení zobrazuje stav připravenosti jednotlivých virtuálních počítačů.

**Připravenosti** | **Stav** | **Podrobnosti**
--- | --- | ---
Připraveno pro Azure | Žádné problémy s kompatibilitou. Počítač se dá migrovat tak, jak je, do Azure a spustí se v Azure s plnou podporou Azure. | U virtuálních počítačů, které jsou pro migraci vhodné, doporučí Azure Migrate velikost virtuálního počítače v Azure.
Připraveno pro Azure s podmínkou | Počítač se může spustit v Azure, ale nemusí mít plnou podporu Azure. Například počítač se starší verzí Windows Serveru, který není v Azure podporovaný. | Migrace Azure vysvětluje problémy s připraveností a poskytuje nápravné kroky.
Nepřipraveno pro Azure |  Virtuální počítač se v Azure nespustí. Například pokud virtuální počítač má disk, který je více než 4 TB, nemůže být hostované v Azure. | Migrace Azure vysvětluje problémy s připraveností a poskytuje nápravné kroky.
Připravenost neznámá | Azure Migrate nemůže identifikovat připravenost Azure, obvykle proto, že data nejsou k dispozici.


#### <a name="azure-vm-properties"></a>Vlastnosti virtuálních zařízení Azure
Připravenost bere v úvahu řadu vlastností virtuálních počítače k určení, jestli virtuální počítač může běžet v Azure.


**Vlastnost** | **Podrobnosti** | **Připravenosti**
--- | --- | ---
**Typ spouštění** | Systém BIOS podporován. UEFI není podporováno. | Podmíněně připraven, pokud je typ spouštění UEFI.
**Cores** | Jádrové <počítače = maximální počet jader (128) podporovaných pro virtuální počítač Azure.<br/><br/> Pokud je dostupná historie výkonu, Azure Migrate vezme v úvahu využitá jádra.<br/>Pokud je v nastavení hodnocení specifikován faktor komfortu, počet využitých jader se vynásobí faktorem komfortu.<br/><br/> Pokud neexistuje žádná historie výkonu, Azure Migrate používá přidělená jádra bez použití faktoru pohodlí. | Připraven, pokud je menší nebo rovno limitům.
**Paměti** | Velikost paměti počítače <= maximální paměť (3892&nbsp;GB v řadě Azure M Standard_M128m<sup>2</sup>) pro virtuální počítač Azure. [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate bere v úvahu využité paměti.<br/><br/>Je-li zadán komfortní faktor, využitá paměť se vynásobí faktorem komfortu.<br/><br/> Pokud neexistuje žádná historie přidělené paměti se používá, bez použití faktoru pohodlí.<br/><br/> | Připraven, pokud v mezích.
**Disk úložiště** | Přidělená velikost disku musí být 4 TB (4096 GB) nebo méně.<br/><br/> Počet disků připojených k počítači musí být 65 nebo méně, včetně disku operačního systému. | Připraven, pokud v mezích.
**Síťové služby** | K zařízení musí být připojeno 32 nebo méně nic. | Připraven, pokud v mezích.

#### <a name="guest-operating-system"></a>Hostovaný operační systém

Spolu s vlastnostmi virtuálních počítače Azure Migrate také vyhledá hostovaný operační systém místního virtuálního počítače, aby zjistil, jestli virtuální počítač může běžet v Azure.

- Azure Migrate bere v úvahu operační systém zadaný v serveru vCenter.
- Vzhledem k tomu, že zjišťování provedené Azure Migrate je založené na zařízení, nemá způsob, jak ověřit, zda operační systém spuštěný uvnitř virtuálního počítače je stejný jako ten, který je zadán v vCenter Server.

Používá se následující logika.

**Operační systém** | **Podrobnosti** | **Připravenosti**
--- | --- | ---
Windows Server 2016 a všechny sps | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 R2 a všechny sp | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 a všechny sps | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2008 R2 a všechny sps | Azure poskytuje plnou podporu.| Připraveno pro Azure
Windows Server 2008 (32bitový a 64bitový) | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2003, 2003 R2 | Nepotřebujeme podporu a potřebujete [vlastní smlouvu o podpoře (CSA)](https://aka.ms/WSosstatement) pro podporu v Azure. | Podmíněně připraven pro Azure, zvažte upgrade operačního systému před migrací do Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Mimo-of-podpora. Počítač se může spustit v Azure, ale azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure, doporučuje se upgradovat operační systém před migrací do Azure.
Klient Windows 7, 8 a 10 | Azure poskytuje podporu [jenom s předplatným Visual Studia.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Připraveno pro Azure s podmínkou
Plocha Windows 10 Pro | Azure poskytuje podporu s [víceklientských práv hostingu.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Připraveno pro Azure s podmínkou
Windows Vista, XP Professional | Mimo-of-podpora. Počítač se může spustit v Azure, ale azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure, doporučuje se upgradovat operační systém před migrací do Azure.
Linux | Azure podporuje tyto [operační systémy Linuxu](../virtual-machines/linux/endorsed-distros.md). Jiné operační systémy Linuxu se mohou spustit v Azure, ale doporučujeme upgradovat operační systém na schválenou verzi před migrací do Azure. | Připraveno pro Azure, pokud je verze schválena.<br/><br/>Podmíněně připraven, pokud verze není schválena.
Ostatní operační systémy<br/><br/> Například Oracle Solaris, Apple Mac OS atd., FreeBSD atd. | Azure tyto operační systémy neschvaluje. Počítač se může spustit v Azure, ale azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure, doporučuje me instalovat podporovaný operační systém před migrací do Azure.  
Operační systém byl zadán jako **Jiný** na serveru vCenter | Azure Migrate nemůže identifikovat operační systém v tomto případě. | Neznámá připravenost. Ujistěte se, že operační systém spuštěný uvnitř virtuálního počítače je podporovaný v Azure.
32bitové operační systémy | Počítač se může spustit v Azure, ale Azure nemusí poskytovat plnou podporu. | Podmíněně připravené pro Azure, zvažte upgrade operačního systému počítače z 32bitového operačního systému na 64bitový operační systém před migrací do Azure.


### <a name="review-sizing"></a>Změna velikosti recenze

 Doporučení velikosti Migrace Azure závisí na kritériu velikosti určeném ve vlastnostech posouzení.

- Pokud je velikost založená na výkonu, doporučení velikosti bere v úvahu historii výkonu virtuálních počítačů (procesoru a paměti) a disků (VOPS a propustnost).
- Pokud je kritérium velikosti "jako místní", doporučení velikosti v Azure je založeno na velikosti místního virtuálního počítače. Velikost disku je založena na typu úložiště určeném ve vlastnostech vyhodnocení (výchozí je disky premium). Azure Migrate nebere v úvahu data o výkonu pro virtuální počítač a disky.

### <a name="review-cost-estimates"></a>Přehled odhadovaných nákladů

Odhady nákladů ukazují celkové náklady na výpočetní prostředky a úložiště za spouštění virtuálních počítačů v Azure spolu s podrobnostmi o jednotlivých počítačích.

- Odhady nákladů se vypočítají pomocí doporučení velikosti pro počítač s virtuálním počítačem a jeho disky a vlastnosti posouzení.
- Odhadované měsíční náklady na výpočetní kapacitu a úložiště jsou agregované pro všechny virtuální počítače dané skupiny.
- Odhad nákladů je pro spuštění místního virtuálního počítače jako virtuálních počítačích Azure Infrastructure as service (IaaS). Azure Migrate nepovažuje platformu za službu (PaaS) ani náklady na software jako službu (SaaS).

### <a name="review-confidence-rating-performance-based-assessment"></a>Přezkoumat hodnocení spolehlivosti (hodnocení na základě výkonu)

Každé hodnocení založené na výkonu je spojeno s hodnocením spolehlivosti.

- Hodnocení spolehlivosti se pohybuje od jedné hvězdy po pětihvězdičkovou (jedna hvězda je nejnižší a pětihvězdičková nejvyšší).
- Hodnocení spolehlivosti je přiřazeno k posouzení na základě dostupnosti datových bodů potřebných k výpočtu hodnocení.
- Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti není k dispozici pro místní hodnocení "tak, jak je".

Pro velikost na základě výkonu Azure Migrate potřebuje následující:
- Údaje o využití procesoru.
- Data paměti virtuálního zařízení.
- Pro každý disk připojený k virtuálnímu počítače potřebuje data viops disku a propustnosti.
- Pro každý síťový adaptér připojený k virtuálnímu počítači potřebuje Azure Migrate síťový vstup a výstup.
- Pokud některá z výše uvedených skutečností není k dispozici, nemusí být doporučení pro velikost (a tedy i hodnocení spolehlivosti) spolehlivá.


V závislosti na procentu dostupných datových bodů jsou možné hodnocení spolehlivosti shrnuty v tabulce.

**Dostupnost datových bodů** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Otázky hodnocení ovlivňující hodnocení spolehlivosti

Posouzení nemusí mít k dispozici všechny datové body z několika důvodů:

- Po dobu hodnocení jste své prostředí neprofilovat. Pokud například vytvoříte hodnocení s dobou trvání výkonu nastavenou na jeden den, musíte počkat alespoň jeden den po spuštění zjišťování nebo všechny datové body, které mají být shromážděny.
- Některé virtuální společnosti byly vypnuty během období, pro které bylo vypočteno posouzení. Pokud byly některé virtuální počítače vypnuté po část doby trvání, Azure Migrate nemůže shromažďovat údaje o výkonu za toto období.
- Některé virtuální společnosti byly vytvořeny mezi během období výpočtu posouzení. Pokud například vytvoříte hodnocení pomocí historie výkonu za poslední měsíc, ale vytvoříte počet virtuálních virtuálních discích v prostředí před týdnem, historie výkonu nových virtuálních virtuálních společností nebude po celou dobu trvání.

> [!NOTE]
> Pokud je hodnocení spolehlivosti jakéhokoli hodnocení nižší než pětihvězdičkové, počkejte alespoň jeden den, než přístroj vyprofiluje prostředí, a poté přepočítejte hodnocení. Pokud nemáte velikost na základě výkonu nemusí být spolehlivé. Pokud nechcete přepočítat, doporučujeme přepnout na jako místní velikosti změnou vlastností hodnocení.



## <a name="create-groups-using-dependency-visualization"></a>Vytváření skupin pomocí vizualizace závislostí

Kromě ručního vytváření skupin můžete vytvářet skupiny pomocí vizualizace závislostí.
- Tuto metodu obvykle používáte, pokud chcete posoudit skupiny s vyšší úrovní spolehlivosti křížové kontroly závislostí počítače před spuštěním hodnocení.
- Vizualizace závislostí vám pomůže efektivně naplánovat migraci do Azure. Pomáhá zajistit, že nic nezůstane pozadu a že překvapení výpadky nedochází při migraci do Azure.
- Můžete zjistit všechny vzájemně závislé systémy, které je třeba migrovat společně a zjistit, zda spuštěný systém stále slouží uživatelům nebo je kandidátem na vyřazení z provozu namísto migrace.
- Azure Migrate používá řešení mapy služeb v Azure Monitoru k povolení vizualizace závislostí.

> [!NOTE]
> Vizualizace závislostí není v Azure Government dostupná.

Chcete-li nastavit vizualizaci závislostí, přidružíte pracovní prostor Analýzy protokolů k projektu Migrace Azure, nainstalujte agenty na počítačích, pro které chcete vizualizovat závislosti, a pak vytvořte skupiny pomocí informací o závislostech. 



### <a name="associate-a-log-analytics-workspace"></a>Přidružení pracovního prostoru Analýzy protokolů

Chcete-li použít vizualizaci závislostí, přidružíte pracovní prostor Analýzy protokolů k projektu migrace. Pracovní prostor můžete vytvořit nebo připojit pouze ve stejném předplatném, kde je vytvořen projekt migrace.

1. Chcete-li k projektu připojit pracovní prostor Log Analytics, klepněte v **části Přehled**na > **Essentials**na **položku Vyžaduje konfiguraci**.
2. Můžete vytvořit nový pracovní prostor nebo připojit existující pracovní prostor:
  - Chcete-li vytvořit nový pracovní prostor, zadejte název. Pracovní prostor se vytvoří v oblasti ve stejné [geografické oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migrace.
  - Když připojíte existující pracovní prostor, můžete vybrat ze všech dostupných pracovních prostorů ve stejném předplatném jako projekt migrace. V seznamu jsou uvedeny pouze pracovní prostory, které byly vytvořeny v [podporované oblasti mapy služeb](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Chcete-li připojit pracovní prostor, ujistěte se, že máte přístup ke čtenáři k pracovnímu prostoru.

> [!NOTE]
> Pracovní prostor přidružený k projektu migrace nelze změnit.

### <a name="download-and-install-vm-agents"></a>Stažení a instalace agentů virtuálních mís

Po konfiguraci pracovního prostoru stáhnete a nainstalujete agenty do každého místního počítače, který chcete vyhodnotit. Kromě toho, pokud máte počítače bez připojení k internetu, budete muset stáhnout a nainstalovat [bránu Log Analytics](../azure-monitor/platform/gateway.md) na ně.

1. V **části Přehled**klepněte na **položku Spravovat** > **počítače**a vyberte požadovaný počítač.
2. Ve sloupci **Závislosti** klikněte na **Instalovat agenty**.
3. Na stránce **Závislosti** stáhněte a nainstalujte Microsoft Monitoring Agent (MMA) a agent závislostí na každém virtuálním počítači, který chcete posoudit.
4. Zkopírujte ID a klíč pracovního prostoru. Potřebujete je při instalaci MMA v místním počítači.

> [!NOTE]
> Chcete-li automatizovat instalaci agentů, můžete použít nástroj pro nasazení, jako je configuration manager nebo partnerský nástroj, jako je [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), který poskytuje řešení nasazení agenta pro Migraci Azure.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Instalace agenta MMA do počítače se systémem Windows

Instalace agenta do počítače se systémem Windows:

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **Licenční podmínky** klikněte na **Souhlasím** a přijměte licenci.
3. V **cílové složce**uchovávejte nebo upravte výchozí instalační složku > **Next**.
4. V **možnostech nastavení agenta**vyberte **Azure Log Analytics** > **Next**.
5. Kliknutím na **Přidat** přidáte nový pracovní prostor Analýzy protokolů. Vložte do ID pracovního prostoru a klíče, které jste zkopírovali z portálu. Klikněte na **Další**.

Agenta můžete nainstalovat z příkazového řádku nebo pomocí automatizované metody, například Nástroje pro kontrolu konfigurace. [Další informace](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) o použití těchto metod k instalaci agenta MMA.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Instalace agenta MMA na počítač s Linuxem

Instalace agenta na počítač s Linuxem:

1. Přeneste příslušný balíček (x86 nebo x64) do počítače s Linuxem pomocí scp/sftp.
2. Nainstalujte balíček pomocí argumentu --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Přečtěte si další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) o seznamu podpory operačních systémů Linux od MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Instalace agenta MMA do počítače monitorovaného programem Operations Manager

Pro počítače monitorované nástrojem System Center Operations Manager 2012 R2 nebo novějším není potřeba instalovat agenta MMA. Mapa služeb se integruje s MMA nástroje Operations Manager a shromažďuje potřebná data závislostí. [Další informace](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Agent závislostí je třeba nainstalovat.

### <a name="install-the-dependency-agent"></a>Instalace agenta závislostí

1. Chcete-li nainstalovat agenta závislostí do počítače se systémem Windows, poklepejte na instalační soubor a postupujte podle pokynů průvodce.
2. Chcete-li nainstalovat agenta závislostí do počítače s Linuxem, nainstalujte jako root pomocí následujícího příkazu:

    ```sh InstallDependencyAgent-Linux64.bin```

- Další informace o [podpoře agenta závislostí](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) pro operační systémy Windows a Linux.
- [Přečtěte si další informace](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) o tom, jak můžete pomocí skriptů nainstalovat agenta závislostí.

>[!NOTE]
> Azure Monitor pro virtuální počítače článek odkazuje poskytnout přehled systémových předpokladů a metod pro nasazení agenta závislostí jsou také použitelné pro řešení map služeb.

### <a name="create-a-group-with-dependency-mapping"></a>Vytvoření skupiny s mapováním závislostí

1. Po instalaci agentů přejděte na portál a klepněte na tlačítko **Spravovat** > **počítače**.
2. Vyhledejte počítač, ve kterém jste nainstalovali agenty.
3. Sloupec **Závislosti** pro počítač by se nyní měl zobrazit jako **Zobrazit závislosti**. Kliknutím na sloupec zobrazíte závislosti počítače.
4. Mapa závislostí pro počítač zobrazuje následující podrobnosti:
    - Příchozí (klienti) a odchozí (servery) připojení TCP do počítače nebo z počítače
        - Závislé počítače, které nemají nainstalovaný agenta MMA a závislostí, jsou seskupeny podle čísel portů.
        - Závislé počítače, které mají mma a agent závislostí nainstalovány jsou zobrazeny jako samostatné krabice.
    - Procesy spuštěné uvnitř stroje, můžete rozšířit každý stroj box pro zobrazení procesů
    - Vlastnosti počítače, včetně FQDN, operační systém, MAC adresa jsou zobrazeny. Kliknutím na každé pole stroje zobrazíte podrobnosti.

4. Závislosti pro různé doby trvání můžete zobrazit kliknutím na dobu trvání v popisku časového rozsahu. Ve výchozím nastavení je rozsah hodinu. Můžete upravit časový rozsah nebo zadat počáteční a koncové datum a dobu trvání.

   > [!NOTE]
   >    Je podporován časový rozsah až hodinu. Pomocí protokolů Azure Monitor [udávat dotaz na data závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) po delší dobu trvání.

5. Po identifikaci závislých počítačů, které chcete seskupit, vyberte pomocí kombinace kláves Ctrl+Click více počítačů na mapě a klikněte na **Seskupit počítače**.
6. Zadejte název skupiny. Ověřte, že závislé počítače jsou zjištěny pomocí Migrace Azure.

    > [!NOTE]
    > Pokud závislý počítač není zjištěna Migrace Azure, nelze přidat do skupiny. Chcete-li přidat tyto počítače do skupiny, je třeba spustit proces zjišťování znovu se správným oborem v vCenter Server a ujistěte se, že počítač je zjištěna Azure Migrate.  

7. Pokud chcete vytvořit hodnocení pro tuto skupinu, zaškrtněte políčko pro vytvoření nového hodnocení pro skupinu.
8. Chcete-li skupinu uložit, klepněte na tlačítko **OK.**

Po vytvoření skupiny se doporučuje nainstalovat agenty na všechny počítače skupiny a upřesnit skupinu vizualizací závislosti celé skupiny.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Dotazovat se na data závislostí z protokolů Azure Monitoru

Data závislostí zachycená mapou služeb jsou k dispozici pro dotazování v pracovním prostoru Log Analytics přidruženém k projektu Migrace Azure. [Další informace](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) o datových tabulkách map služeb pro dotazování v protokolech Azure Monitoru. 

Spuštění dotazů Kusto:

1. Po instalaci agentů přejděte na portál a klepněte na tlačítko **Přehled**.
2. V **části Přehled**přejděte v části **Essentials** projektu a klikněte na název pracovního prostoru, který je k dispozici vedle **pracovního prostoru OMS**.
3. Na stránce pracovního prostoru Log Analytics klikněte na **Obecné** > **protokoly**.
4. Napište dotaz ke shromažďování dat závislostí pomocí protokolů Azure Monitor. Ukázkové dotazy najdete v další části.
5. Spusťte dotaz kliknutím na Spustit. 

[Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) o tom, jak psát kusto dotazy. 

### <a name="sample-azure-monitor-logs-queries"></a>Ukázkové dotazy protokolů Azure Monitor

Následují ukázkové dotazy, které můžete použít k extrahování dat závislostí. Můžete upravit dotazy extrahovat upřednostňované datové body. Zde je [k](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)dispozici vyčerpávající seznam polí v záznamech dat závislostí . Další ukázkové dotazy [naleznete zde](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Sumarizovat příchozí připojení na sadě počítačů

Záznamy v tabulce pro metriky připojení, VMConnection, nepředstavují jednotlivá fyzická síťová připojení. Více fyzických síťových připojení je seskupeno do logického připojení. [Další informace](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) o tom, jak jsou data fyzického připojení k síti agregována do jednoho logického záznamu v programu VMConnection. 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Sumarizovat objem dat odeslaných a přijatých na příchozích připojeních mezi sadou počítačů

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
[Přečtěte si](migrate-services-overview.md) o nejnovější verzi Azure Migrate.
