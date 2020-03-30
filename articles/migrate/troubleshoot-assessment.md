---
title: Poradce při potížích s vyhodnocením a vizualizací závislostí v Azure Migrate
description: Získejte pomoc s řešením potíží s řešením problémů s řešením potíží s řešením potíží s řešením potíží a vizualizací závislostí v Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127678"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Řešení potíží s vyhodnocením a vizualizací závislostí

Tento článek vám pomůže vyřešit problémy s hodnocením a vizualizací závislostí pomocí [Migrace Azure: Vyhodnocení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problémy s připraveností na posouzení

Problémy s připraveností na posouzení opravíte následujícím způsobem:

**Problém** | **Oprava**
--- | ---
Nepodporovaný typ spuštění | Azure nepodporuje virtuální počítače s typem spouštění EFI. Před spuštěním migrace doporučujeme převést typ spouštění do systému BIOS. <br/><br/>Migraci serveru Azure můžete použít ke zpracování migrace těchto virtuálních počítačů. Převede typ spouštění virtuálního soudu do systému BIOS během migrace.
Podmíněně podporovaný operační systém Windows | Operační systém prošel datem ukončení podpory a potřebuje vlastní smlouvu o podpoře (CSA) pro [podporu v Azure](https://aka.ms/WSosstatement). Zvažte upgrade před migrací do Azure.
Nepodporovaný operační systém Windows | Azure podporuje [jenom vybrané verze operačního systému Windows](https://aka.ms/WSosstatement). Zvažte upgrade počítače před migrací do Azure.
Podmíněně potvrzený operační systém Linux | Azure podporuje jenom [vybrané verze operačního systému Linux](../virtual-machines/linux/endorsed-distros.md). Zvažte upgrade počítače před migrací do Azure.
Neschválený operační systém Linux | Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního systému. Před migrací do Azure zvažte upgrade na [schválenou linuxovou verzi.](../virtual-machines/linux/endorsed-distros.md)
Neznámý operační systém | Operační systém virtuálního aplikace byl zadán jako "Ostatní" v serveru vCenter. Toto chování blokuje Azure Migrate z ověření připravenosti Azure virtuálního počítače. Ujistěte se, že operační systém je [podporován](https://aka.ms/azureoslist) Azure před migrací počítače.
Nepodporovaná bitová verze | Virtuální počítače s 32bitovými operačními systémy se mohou v Azure spustit, ale doporučujeme upgradovat na 64bitový upgrade před migrací do Azure.
Vyžaduje předplatné Microsoft Visual Studia. | Počítač používá klientský operační systém Windows, který je podporován pouze prostřednictvím předplatného sady Visual Studio.
Virtuální ms nebyl nalezen pro požadovaný výkon úložiště | Výkon úložiště (vstupní a výstupní operace za sekundu [IOPS] a propustnost) vyžadované pro počítač překračuje podporu virtuálních her Azure. Snižte požadavky na úložiště pro počítač před migrací.
Virtuální ms nebyl nalezen pro požadovaný výkon sítě | Výkon sítě (in/out) vyžadované pro počítač přesahuje podporu virtuálních her Azure. Snižte požadavky na síť pro počítač.
Virtuální uzel nebyl v zadaném umístění nalezen. | Před migrací použijte jiné cílové umístění.
Jeden nebo více nevhodných disků | Jeden nebo více disků připojených k virtuálnímu počítači nesplňují požadavky Azure. A<br/><br/> Migrace Azure: Server Assessment aktuálně nepodporuje disky Ultra SSD a vyhodnocuje disky na základě omezení disků pro disky spravované pro prémie (32 TB).<br/><br/> U každého disku připojeného k virtuálnímu počítači zkontrolujte, zda je velikost disku < 64 TB (podporovaná disky Ultra SSD).<br/><br/> Pokud tomu tak není, zmenšete velikost disku před migrací do Azure nebo použijte více disků v Azure a [proužte je dohromady,](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) abyste získali vyšší limity úložiště. Ujistěte se, že výkon (IOPS a propustnost) potřebné pro každý disk je podporován [disky virtuálních počítačů Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits).
Jeden nebo více nevhodných síťových adaptérů. | Před migrací odeberte z počítače nepoužívané síťové adaptéry.
Počet disků překračuje limit | Před migrací odeberte z počítače nepoužívané disky.
Velikost disku překračuje limit | Migrace Azure: Server Assessment aktuálně nepodporuje disky Ultra SSD a vyhodnocuje disky na základě omezení disků premium (32 TB).<br/><br/> Azure však podporuje disky s velikostí až 64 TB (podporovaná disky Ultra SSD). Zmenšete disky na méně než 64 TB před migrací nebo použijte více disků v Azure a [proužte je dohromady,](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) abyste získali vyšší limity úložiště.
Disk není v zadaném umístění k dispozici. | Před migrací zkontrolujte, zda je disk v cílovém umístění.
Disk není k dispozici pro zadanou redundanci | Disk by měl používat typ úložiště redundance definovaný v nastavení hodnocení (LRS ve výchozím nastavení).
Nelze určit vhodnost disku z důvodu vnitřní chyby. | Zkuste vytvořit nové hodnocení pro skupinu.
Virtuální virtuální ms s požadovanými jádry a pamětí nebyl nalezen. | Azure nemohl najít vhodný typ virtuálního počítače. Před migrací snižte paměť a počet jader místního počítače.
Nelze určit vhodnost virtuálního rozhraní z důvodu vnitřní chyby. | Zkuste vytvořit nové hodnocení pro skupinu.
Nelze určit vhodnost pro jeden nebo více disků z důvodu vnitřní chyby. | Zkuste vytvořit nové hodnocení pro skupinu.
Nelze určit vhodnost pro jeden nebo více síťových adaptérů z důvodu vnitřní chyby. | Zkuste vytvořit nové hodnocení pro skupinu.

## <a name="linux-vms-are-conditionally-ready"></a>Virtuální počítače s Linuxem jsou "podmíněně připravené"

Vyhodnocení serveru označí virtuální počítače SIP jako "Podmíněně připravené" kvůli známé mezery v vyhodnocení serveru.

- Mezera zabraňuje detekci dílčí verze operačního systému Linux nainstalovaného na místních virtuálních počítačích.
- Například pro RHEL 6.10 aktuálně server assessment detekuje pouze RHEL 6 jako verzi operačního systému.
-  Vzhledem k tomu, že Azure podporuje jenom konkrétní verze Linuxu, virtuální počítače s Linuxem jsou aktuálně označeny jako podmíněně připravené v vyhodnocení serveru.
- Můžete určit, jestli operační systém Linux běží na místním virtuálním počítači je potvrzena v Azure kontrolou [azure linux podpory](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Po ověření schválené distribuce můžete toto upozornění ignorovat.

## <a name="azure-skus-bigger-than-on-premises"></a>SKU Azure větší než místní

Azure Migrate Server Assessment může doporučit virtuálních měn Virtuálního počítače Azure s více jádry a pamětí než aktuální místní přidělení na základě typu posouzení:


- Doporučení skladové položky virtuálního počítače závisí na vlastnostech posouzení.
- To je ovlivněno typem hodnocení, které provádíte v hodnocení serveru: *Na základě výkonu*nebo Jako *místní*.
- Pro hodnocení na základě výkonu, vyhodnocení serveru považuje data využití místních virtuálních počítačů (procesor, paměť, disk a využití sítě) k určení správné cílové skladové položky virtuálního počítače pro místní virtuální počítače. Přidává také komfortní faktor při určování efektivního využití.
- Pro místní velikosti data výkonu se nepovažuje a cílová skladová položka se doporučuje na základě místní přidělení.

Chcete-li ukázat, jak to může ovlivnit doporučení, vezměme si příklad:

Máme místní virtuální počítač se čtyřmi jádry a osmi GB paměti, s 50% využitím procesoru a 50% využití paměti a zadaným faktorem pohodlí 1.3.

-  Pokud je hodnocení **jako místní**, doporučuje se skladová položka virtuálního počítače Azure se čtyřmi jádry a 8 GB paměti.
- Pokud je hodnocení založeno na výkonu, založené na efektivním využití procesoru a paměti (50 % ze 4 jader * 1,3 = 2,6 jader a 50 % paměti 8 GB * paměť 1,3 = 5,3 GB), nejlevnější skladová položka virtuálního počítače se čtyřmi jádry (nejbližší podporovaný počet jader) a osm GB paměti (nejbližší podporovaná velikost paměti).
- [Přečtěte si další informace](concepts-assessment-calculation.md#types-of-assessments) o velikosti hodnocení.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Pevné objekty azure disků větší než místní

Azure Migrate Server Assessment může doporučit větší disk na základě typu hodnocení.
- Velikost disku v vyhodnocení serveru závisí na dvou vlastnostech hodnocení: kritéria pro velikost a typ úložiště.
- Pokud jsou kritéria pro velikost **založená na výkonu**a typ úložiště je nastaven na **automatické**, hodnoty VOPS a propustnosti disku se při identifikaci cílového typu disku vezmou v úvahu (standardní pevný disk, standardní ssd nebo premium). Disk ová položka z typu disku se pak doporučuje a doporučení bere v úvahu požadavky na velikost místního disku.
- Pokud jsou kritéria pro výběr velikosti **na základě výkonu**a typ úložiště je **Premium**, doporučuje se skladová položka prémiového disku v Azure na základě požadavků na viposlužby, propustnost a velikost místního disku. Stejná logika se používá k provádění velikosti disku, pokud jsou kritéria pro velikost **jako místní** a typ úložiště je Standardní **pevný disk**, Standardní **SSD**nebo **Premium**.

Například pokud máte místní disk s pamětí 32 GB, ale agregované seznamy VOP pro čtení a zápis pro disk jsou 800 IOPS, vyhodnocení serveru doporučuje disk premium (z důvodu vyšších požadavků na iOPS) a pak doporučuje skladovou položku disku, která podporuje požadovaných IOPS a velikosti. Nejbližší shodou by v tomto případě byla skladová položka P15 (256 GB, 1 100 IOPS). I když velikost požadovaná místním diskem byla 32 GB, vyhodnocení serveru doporučuje větší disk z důvodu vysokého požadavku viops místního disku.

## <a name="utilized-corememory-percentage-missing"></a>Chybí procento jádra/paměti

Vyhodnocení serveru hlásí "PercentageOfCoresUtilizedMissing" nebo "PercentageOfMemoryUtilizedMissing", když zařízení Migrace Azure nemůže shromažďovat data o výkonu pro příslušné místní virtuální počítače.

- Tato situace může nastat, pokud virtuální chod jsou vypnuté během trvání hodnocení. Zařízení nemůže shromažďovat údaje o výkonu pro virtuální hod, když je vypnutý.
- Pokud chybí pouze čítače paměti a pokoušíte se posoudit virtuální ch od pv, zkontrolujte, jestli máte na těchto virtuálních počítačích povolenou dynamickou paměť. Existuje známý problém pouze pro virtuální počítače Hyper-V, ve kterém zařízení Pro migraci Azure nemůže shromažďovat data využití paměti pro virtuální počítače, které nemají povolenou dynamickou paměť.
- Pokud některý z čítačů výkonu chybí, Azure Migrate Server Assessment přejde zpět do přidělených jader a paměti a doporučuje odpovídající velikost virtuálního počítače.
- Pokud chybí všechny čítače výkonu, ujistěte se, že jsou splněny požadavky na přístup k portu pro posouzení. Přečtěte si další informace o požadavcích na přístup k portům pro hodnocení [vsystému VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access), [technologie Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) a [fyzického](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) serveru.

## <a name="is-the-operating-system-license-included"></a>Je zahrnuta licence operačního systému?

Azure Migrate Server Assessment aktuálně bere v úvahu náklady na licenci operačního systému jenom pro počítače s Windows. Licenční náklady pro počítače s Linuxem nejsou v současné době považovány za.

## <a name="how-does-performance-based-sizing-work"></a>Jak funguje velikost založená na výkonu?

Nástroj Hodnocení serverů průběžně shromažďuje data o výkonu místních počítačů a používá je k doporučení skladové položky virtuálního počítače a disku v Azure. [Zjistěte, jak](concepts-assessment-calculation.md#calculate-sizing-performance-based) se shromažďují data založená na výkonu.


## <a name="dependency-visualization-in-azure-government"></a>Vizualizace závislostí ve službě Azure Government

Migrace Azure závisí na mapě služeb pro funkce vizualizace závislostí. Vzhledem k tomu, že mapa služeb je momentálně ve službě Azure Government dostupná, není tato funkce dostupná ve službě Azure Government.

## <a name="dependencies-dont-show-after-agent-install"></a>Závislosti se po instalaci agenta nezobrazují

Po instalaci agentů vizualizace závislostí na místních virtuálních počítačích azure migrate obvykle trvá 15–30 minut, než se zobrazí závislosti na portálu. Pokud jste čekali déle než 30 minut, ujistěte se, že Microsoft Monitoring Agent (MMA) můžete připojit k pracovnímu prostoru Log Analytics.

Pro virtuální aplikace Windows:
1. V Ovládacích panelech spusťte MMA.
2. Ve **vlastnostech** > Agent monitorování Microsoft**Azure Log Analytics (OMS)**, ujistěte se, že **stav** pro pracovní prostor je zelená.
3. Pokud stav není zelený, zkuste pracovní prostor odebrat a znovu jej přidat do MMA.

    ![Stav MMA](./media/troubleshoot-assessment/mma-properties.png)

U virtuálních počítačů s Linuxem se ujistěte, že instalační příkazy pro MMA a agenta závislostí byly úspěšné.

## <a name="supported-operating-systems"></a>Podporované operační systémy

- **MMS agent**: Zkontrolujte podporované operační systémy [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)a [Linux.](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)
- **Dependency Agent**: podporované operační systémy [Windows a Linux.](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)

## <a name="visualize-dependencies-for--hour"></a>Vizualizace závislostí pro > hodinu

Přestože Azure Migrate umožňuje vrátit se k určitému datu v posledním měsíci, maximální doba trvání, po kterou můžete vizualizovat závislosti je jedna hodina.

Můžete například použít funkci trvání času v mapě závislostí k zobrazení závislostí za včerejšek, ale můžete je zobrazit pouze po dobu jedné hodiny.

Můžete však použít protokoly Azure Monitor k [dotazování data závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) po delší dobu trvání.

## <a name="visualized-dependencies-for--10-machines"></a>Vizualizované závislosti pro > 10 počítačů

V Azure Migrate Server Assessment můžete [vizualizovat závislosti pro skupiny](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) s až 10 virtuálními počítači. Pro větší skupiny doporučujeme rozdělit virtuální chod do menších skupin pro vizualizaci závislostí.

## <a name="machines-show-install-agent"></a>Stroje zobrazují "Instalační agent"

Po migraci počítačů s povolenou vizualizací závislostí do Azure mohou počítače zobrazit akci "Instalovat agenta" namísto "Zobrazit závislosti" kvůli následujícímu chování:


- Po migraci do Azure jsou místní počítače vypnuté a ekvivalentní virtuální počítače se v Azure přetaví. Tyto stroje získají jinou MAC adresu.
- Počítače mohou mít také jinou IP adresu podle toho, jestli jste si místní IP adresu uchovali, nebo ne.
- Pokud se mac i IP adresy liší od místních, Azure Migrate nepřidruží místní počítače k žádným datům závislostí na mapě služby. V tomto případě se zobrazí možnost instalace agenta spíše než zobrazení závislostí.
- Po testovací migraci do Azure zůstanou místní počítače zapnuté podle očekávání. Ekvivalentní počítače se točil v Azure získat různé MAC adresu a může získat různé IP adresy. Pokud nezablokujete odchozí provoz protokolu Azure Monitor z těchto počítačů, Azure Migrate nebude přidružit místní počítače s žádnými daty závislostí mapy služeb a proto se zobrazí možnost instalace agentů, nikoli zobrazení závislostí.


## <a name="capture-network-traffic"></a>Zachycení síťového provozu

Shromažďovat protokoly síťového provozu takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Stisknutím klávesy F12 spusťte nástroje pro vývojáře. V případě potřeby zrušte zaškrtnutí možnosti **Vymazat položky v navigaci.**
3. Vyberte kartu **Síť** a začněte zachycovat síťový provoz:
   - V Chromu vyberte **Zachovat protokol**. Nahrávání by se mělo spustit automaticky. Červený kruh označuje, že je zachycen provoz. Pokud se červený kruh nezobrazí, vyberte černý kruh, který chcete spustit.
   - V aplikaci Microsoft Edge a internet exploreru by se nahrávání mělo spouštět automaticky. Pokud tomu tak není, vyberte zelené tlačítko přehrávání.
4. Pokuste se chybu reprodukovat.
5. Po výskytu chyby při nahrávání zastavte nahrávání a uložte kopii zaznamenané aktivity:
   - V Chromu klikněte pravým tlačítkem myši a vyberte **Uložit jako HAR s obsahem**. Tato akce komprimuje a exportuje protokoly jako soubor .har.
   - V aplikaci Microsoft Edge nebo internet explorer vyberte možnost **Exportovat zachycený provoz.** Tato akce komprimuje a exportuje protokol.
6. Výběrem karty **Konzola** zkontrolujte, zda neobsahuje upozornění nebo chyby. Uložení protokolu konzoly:
   - V Chromu klikněte pravým tlačítkem myši na libovolné místo v protokolu konzoly. Vyberte **Uložit jako**, chcete-li exportovat a zazipovat protokol.
   - V aplikaci Microsoft Edge nebo internet explorer klepněte pravým tlačítkem myši na chyby a vyberte příkaz **Kopírovat vše**.
7. Zavřete nástroje pro vývojáře.

## <a name="next-steps"></a>Další kroky

[Vytvořte](how-to-create-assessment.md) nebo [přizpůsobte](how-to-modify-assessment.md) hodnocení.
