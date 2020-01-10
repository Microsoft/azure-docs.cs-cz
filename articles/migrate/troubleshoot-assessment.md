---
title: Řešení potíží s vizualizací a vizualizací závislostí v Azure Migrate
description: Získejte pomoc s řešením potíží a vizualizace závislostí v Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 3098e85fd21b6185defc4bbcf0a71d412846ab25
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725738"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Řešení potíží s vizualizací a vizualizací závislostí

Tento článek vám pomůže řešit problémy s vizualizací a vizualizací závislostí s [Azure Migrate: posouzení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problémy s připraveností na posouzení

Opravte problémy připravenosti vyhodnocování následujícím způsobem:

**Problém** | **Opravit**
--- | ---
Nepodporovaný typ spuštění | Azure nepodporuje virtuální počítače s typem spouštění EFI. Před spuštěním migrace doporučujeme převést typ spouštění na systém BIOS. <br/><br/>K obsluze migrace takových virtuálních počítačů můžete použít migraci Azure Migrate serveru. Při migraci převede typ spouštění virtuálního počítače na systém BIOS.
Podmíněně podporované operační systémy Windows | Operační systém předal své datum ukončení podpory a pro [podporu v Azure](https://aka.ms/WSosstatement)potřebuje vlastní smlouvu o podpoře (CSA). Před migrací do Azure zvažte možnost upgradovat.
Nepodporovaný operační systém Windows | Azure podporuje jenom [vybrané verze operačního systému Windows](https://aka.ms/WSosstatement). Před migrací do Azure zvažte možnost upgradovat počítač.
Podmíněně schválené operační systémy Linux | Azure schválí jenom [vybrané verze operačního systému Linux](../virtual-machines/linux/endorsed-distros.md). Před migrací do Azure zvažte možnost upgradovat počítač.
Neschválený operační systém Linux | Počítač se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. Před migrací do Azure zvažte možnost upgradovat na [schválenou verzi systému Linux](../virtual-machines/linux/endorsed-distros.md) .
Neznámý operační systém | Operační systém virtuálního počítače byl ve vCenter Server zadán jako jiný. Toto chování blokuje Azure Migrate Ověření připravenosti na Azure pro virtuální počítač. Než počítač migrujete, ujistěte se, že Azure [podporuje](https://aka.ms/azureoslist) operační systém.
Nepodporovaná bitová verze | Virtuální počítače s 32 operačním systémem se můžou spouštět v Azure, ale před migrací do Azure doporučujeme upgradovat na 64.
Vyžaduje předplatné Microsoft Visual Studio. | Počítač používá klientský operační systém Windows, který je podporovaný jenom prostřednictvím předplatného sady Visual Studio.
Pro požadovaný výkon úložiště se nenašel virtuální počítač. | Výkon úložiště (vstupně-výstupní operace za sekundu [IOPS] a propustnost) požadovaná pro tento počítač překračuje podporu virtuálních počítačů Azure. Před migrací snižte požadavky na úložiště pro daný počítač.
Pro požadovaný výkon sítě se nenašel virtuální počítač. | Výkon sítě (v/v) vyžadovaný pro tento počítač překračuje podporu virtuálních počítačů Azure. Snižte požadavky na síť pro daný počítač.
Virtuální počítač se nenašel v zadaném umístění. | Před migrací použijte jiné cílové umístění.
Jeden nebo více nevhodných disků | Jeden nebo více disků připojených k virtuálnímu počítači nesplňuje požadavky Azure. Určitého<br/><br/> Azure Migrate: posouzení serveru aktuálně nepodporuje SSD úrovně Ultra disky a posuzuje disky na základě limitů disku pro spravované disky úrovně Premium (32 TB).<br/><br/> U každého disku připojeného k virtuálnímu počítači se ujistěte, že je velikost disku < 64 TB (podporuje disky SSD úrovně Ultra).<br/><br/> Pokud ne, snižte velikost disku před migrací na Azure nebo použijte více disků v Azure a propojte [je dohromady](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) , abyste dosáhli vyšších limitů úložiště. Ujistěte se, že [disky virtuálních počítačů spravované](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)službou Azure podporují výkon (IOPS a propustnost), které potřebuje každý disk.
Jeden nebo více nevhodných síťových adaptérů. | Před migrací odeberte nepoužívané síťové adaptéry z počítače.
Počet disků překračuje limit. | Před migrací z počítače odeberte nepoužívané disky.
Velikost disku překračuje limit. | Azure Migrate: posouzení serveru aktuálně nepodporuje SSD úrovně Ultra disky a posuzuje disky na základě limitů disků Premium (32 TB).<br/><br/> Azure ale podporuje disky s velikostí až 64 TB (Podporované disky SSD úrovně Ultra). Zmenšete disky na méně než 64 TB před migrací nebo použijte více disků v Azure a propojte [je dohromady](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) , abyste dosáhli vyšších limitů úložiště.
Disk není v zadaném umístění k dispozici. | Před migrací zajistěte, aby byl disk v cílovém umístění.
Disk není pro zadanou redundanci k dispozici. | Disk by měl používat typ úložiště redundance definovaný v nastavení hodnocení (ve výchozím nastavení LRS).
Nebylo možné určit vhodnost disku z důvodu vnitřní chyby. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Virtuální počítač s požadovanými jádry a pamětí se nenašel. | Azure nemohl najít vhodný typ virtuálního počítače. Před migrací Snižte velikost paměti a počtu jader místního počítače.
Kvůli vnitřní chybě se nepovedlo určit vhodnost virtuálního počítače. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo víc disků. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo víc síťových adaptérů. | Zkuste vytvořit nové vyhodnocení pro skupinu.

## <a name="linux-vms-are-conditionally-ready"></a>Virtuální počítače se systémem Linux jsou připravené pro přípravu.

Posouzení serveru označí virtuální počítače se systémem Linux jako podmíněně připravené z důvodu známé mezery v posuzování serveru.

- Tato mezera brání tomu, aby zjistila podverzi operačního systému Linux nainstalovaného na místních virtuálních počítačích.
- Například pro RHEL 6,10 aktuálně vyhodnocování serveru detekuje jako verzi operačního systému pouze RHEL 6.
-  Vzhledem k tomu, že Azure schválí pouze konkrétní verze systému Linux, jsou virtuální počítače se systémem Linux v současnosti označeny jako podmíněně připravené v rámci posouzení serveru.
- Můžete zjistit, jestli operační systém Linux běžící na místním virtuálním počítači je v Azure schválený, a to kontrolou [podpory Azure Linux](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Po ověření schválené distribuce můžete toto upozornění ignorovat.

## <a name="azure-skus-bigger-than-on-premises"></a>Skladové jednotky Azure větší než místní

Posouzení serveru Azure Migrate může doporučit skladové položky Azure s více jádry a pamětí než aktuální místní přidělení na základě typu posouzení:


- Doporučení SKU virtuálního počítače závisí na vlastnostech posouzení.
- To je ovlivněno typem posouzení, které provádíte při posuzování serveru: na *základě výkonu*nebo *jako v*místním prostředí.
- U posouzení na základě výkonu považuje server hodnocení za využití dat z místních virtuálních počítačů (CPU, paměti, disku a sítě) k určení správné cílové SKU virtuálního počítače pro vaše místní virtuální počítače. Také přináší faktor pohodlí při určování efektivního využití.
- Pro místní velikost se data o výkonu neberou v úvahu a cílová skladová jednotka se doporučuje na základě místního přidělení.

Pokud chcete vidět, jak to může ovlivnit doporučení, Podívejme se na příklad:

Máme místní virtuální počítač se čtyřmi jádry a 8 GB paměti s 50% využitím procesoru a 50% využití paměti a stanovený faktor komfortu 1,3.

-  Pokud je posouzení **místní**, doporučuje se skladová položka virtuálního počítače Azure se čtyřmi jádry a 8 GB paměti.
- Pokud je hodnocení založené na výkonu, vychází z efektivního využití procesoru a paměti (50% ze 4 jader × 1,3 = 2,6 jader a 50% z 8 GB paměti * 1,3 = 5,3-GB paměti), SKU nejlevnější virtuálních počítačů se čtyřmi jádry (nejbližší podporovaný počet jader) a osmi GB paměti (s nejbližším podporovaným velikost paměti) se doporučuje.
- [Přečtěte si další informace](concepts-assessment-calculation.md#assessments-in-server-assessment) o velikosti hodnocení.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Diskové jednotky Azure větší než místní

Posouzení serveru Azure Migrate může doporučit větší disk na základě typu posouzení.
- Velikost disku v posuzování serveru závisí na dvou vlastnostech posouzení: kritéria změny velikosti a typ úložiště.
- Pokud jsou kritéria změny velikosti **založená na výkonu**a typ úložiště je nastaven na hodnotu **automaticky**, při určení typu cílového disku (HDD úrovně Standard, SSD úrovně Standard nebo Premium) se bere v úvahu hodnota vstupně-výstupních operací a propustnosti disku. Pak se doporučuje disková jednotka disku z typu disku a doporučení posuzuje požadavky na velikost místního disku.
- Pokud jsou kritéria změny velikosti v **závislosti na výkonu**a typ úložiště je **Premium**, doporučuje se SKU na disku Premium v Azure na základě požadavků na vstupně-výstupní operace, propustnost a velikost na místním disku. Stejná logika se používá k provedení velikosti disku, pokud jsou kritéria změny velikosti **místní** a typ úložiště je **HDD úrovně Standard**, **SSD úrovně Standard**nebo **Premium**.

Příklad: Pokud máte místní disk s 32 GB paměti, ale agregované vstupně-výstupní operace čtení a zápisu pro disk je 800 IOPS, vyhodnocování serveru doporučuje prémiový disk (kvůli vyššímu počtu požadavků IOPS) a potom doporučuje SKU disku, které může podporovat r. ezdrátovou IOPS a velikost. Nejbližší shodou by v tomto případě byla skladová položka P15 (256 GB, 1 100 IOPS). I když velikost požadovaná místním diskem byla 32 GB, vyhodnocování serveru doporučuje větší disk kvůli vysokému počtu požadavků na požadavky na místní disk.

## <a name="utilized-corememory-percentage-missing"></a>Chybějící procento využité jádra/paměti

Sestavy posouzení serveru "PercentageOfCoresUtilizedMissing" nebo "PercentageOfMemoryUtilizedMissing", když Azure Migrate zařízení nemůžou shromažďovat údaje o výkonu pro příslušné místní virtuální počítače.

- Tato situace může nastat, pokud jsou virtuální počítače vypnuté během doby hodnocení. Zařízení nemůže shromažďovat údaje o výkonu pro virtuální počítač, když je vypnutý.
- Pokud nechybí pouze čítače paměti a snažíte se vyhodnotit virtuální počítače Hyper-V, ověřte, zda je na těchto virtuálních počítačích povolena dynamická paměť. K dispozici je známý problém jenom pro virtuální počítače Hyper-V, ve kterém Azure Migrate zařízení nemůže shromažďovat data o využití paměti pro virtuální počítače, které nemají povolenou dynamickou paměť.
- Pokud některý z čítačů výkonu chybí, Azure Migrate posouzení serveru se vrátí k přiděleným jádrům a paměti a doporučí odpovídající velikost virtuálního počítače.

## <a name="is-the-operating-system-license-included"></a>Je zahrnutá licence k operačnímu systému?

Posouzení Azure Migrate serveru v současné době zohledňuje náklady na licenci operačního systému jenom pro počítače s Windows. Náklady na licenci pro počítače se systémem Linux se momentálně nepovažují za.

## <a name="how-does-performance-based-sizing-work"></a>Jak funguje Změna velikosti na základě výkonu?

Nástroj Hodnocení serverů průběžně shromažďuje data o výkonu místních počítačů a používá je k doporučení skladové položky virtuálního počítače a disku v Azure. [Přečtěte si, jak](concepts-assessment-calculation.md#calculate-sizing-performance-based) se shromažďují data založená na výkonu.


## <a name="dependency-visualization-in-azure-government"></a>Vizualizace závislostí v Azure Government

Azure Migrate závisí na Service Map funkce Vizualizace závislostí. Protože Service Map v tuto chvíli není v Azure Government k dispozici, tato funkce není v Azure Government dostupná.

## <a name="dependencies-dont-show-after-agent-install"></a>Po instalaci agenta se nezobrazují závislosti

Po instalaci agentů Vizualizace závislostí na místní virtuální počítače Azure Migrate k zobrazení závislostí na portálu obvykle trvá 15-30 minut. Pokud jste čekali na více než 30 minut, ujistěte se, že Microsoft Monitoring Agent (MMA) se může připojit k pracovnímu prostoru Log Analytics.

Pro virtuální počítače s Windows:
1. V Ovládacích panelech spusťte MMA.
2. Ve **vlastnostech Microsoft Monitoring Agent** > **Azure Log Analytics (OMS)** zkontrolujte, že je **stav** pracovního prostoru zelený.
3. Pokud stav není zelený, zkuste odebrat pracovní prostor a znovu ho přidat do MMA.

    ![Stav MMA](./media/troubleshoot-assessment/mma-properties.png)

Pro virtuální počítače se systémem Linux se ujistěte, že instalační příkazy pro MMA a agenta závislostí byly úspěšné.

## <a name="supported-operating-systems"></a>Podporované operační systémy

- **Agent MMS**: Zkontrolujte podporované operační systémy [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)a [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) .
- **Agent závislostí**: podporované operační systémy [Windows a Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) .

## <a name="visualize-dependencies-for--hour"></a>Vizualizace závislostí pro > hodinu

I když Azure Migrate umožňuje přejít zpět k určitému datu za poslední měsíc, maximální doba, po kterou můžete vizualizovat závislosti, je jedna hodina.

Například můžete použít funkci časového trvání na mapě závislostí k zobrazení závislostí včera, ale můžete je zobrazit pouze v jednom hodinovém období.

Můžete ale použít protokoly Azure Monitor k [dotazování dat závislosti](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) v delší době trvání.

## <a name="visualized-dependencies-for--10-machines"></a>Vizuální závislosti pro počítače s > 10

V Azure Migrate hodnocení serveru můžete [vizualizovat závislosti pro skupiny](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) s až 10 virtuálními počítači. U větších skupin doporučujeme, abyste virtuální počítače rozdělili do menších skupin pro vizualizaci závislostí.

## <a name="machines-show-install-agent"></a>Počítače zobrazují "instalovat agenta"

Po migraci počítačů s povolenou vizualizací závislostí do Azure se můžou počítače místo příkazu "Zobrazit závislosti" Zobrazit akce "instalovat agenta", a to kvůli následujícímu chování:


- Po migraci do Azure jsou místní počítače vypnuté a s ekvivalentními virtuálními počítači se prosazuje v Azure. Tyto počítače získají jinou adresu MAC.
- Počítače mohou mít také jinou IP adresu, a to na základě toho, zda jste zachovali místní IP adresu nebo ne.
- Pokud se adresy MAC i IP liší od místních, Azure Migrate nepřidruží místní počítače k žádným Service Mapm datům závislostí. V takovém případě se zobrazí možnost nainstalovat agenta místo zobrazení závislostí.
- Po otestování migrace do Azure zůstanou místní počítače zapnuté podle očekávání. Ekvivalentní počítače propracované v Azure získají jinou adresu MAC a můžou získat různé IP adresy. Pokud neblokujete odchozí Azure Monitor přenosů protokolů z těchto počítačů, Azure Migrate nebude přidružit místní počítače k žádným Service Map datům závislostí, a proto se zobrazí možnost nainstalovat agenty, nikoli zobrazit závislosti.


## <a name="capture-network-traffic"></a>Zaznamenání síťového provozu

Shromážděte protokoly síťového provozu následujícím způsobem:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Stisknutím klávesy F12 spusťte Vývojářské nástroje. V případě potřeby zrušte zaškrtnutí **políčka vymazat položky u nastavení navigace** .
3. Vyberte kartu **síť** a zahajte zachytávání síťového provozu:
   - V části Chrome vyberte **zachovat protokol**. Záznam by se měl spustit automaticky. Červený kroužek indikuje, že se zaznamenává provoz. Pokud se červené kolečko nezobrazí, vyberte černý kroužek, který chcete spustit.
   - V aplikaci Microsoft Edge a Internet Explorer by se měl záznam spustit automaticky. Pokud tomu tak není, vyberte zelené tlačítko Přehrát.
4. Zkuste reprodukování chyby.
5. Po zjištění chyby při nahrávání, zastavení záznamu a uložení kopie zaznamenané aktivity:
   - V Chrome klikněte pravým tlačítkem a vyberte **Uložit jako Har s obsahem**. Tato akce zkomprimuje a exportuje protokoly jako soubor. har.
   - V Microsoft Edge nebo Internet Exploreru vyberte možnost **exportovat zachycené přenosy** . Tato akce komprimuje a exportuje protokol.
6. Vyberte kartu **Konzola** a vyhledejte všechna upozornění a chyby. Uložení protokolu konzoly:
   - V prohlížeči Chrome klikněte pravým tlačítkem myši kdekoli v protokolu konzoly. Vyberte **Uložit jako**, exportovat a protokol zip.
   - V Microsoft Edge nebo Internet Exploreru klikněte pravým tlačítkem na chyby a vyberte **Kopírovat vše**.
7. Zavřete Vývojářské nástroje.

## <a name="next-steps"></a>Další kroky

[Vytvoření](how-to-create-assessment.md) nebo [přizpůsobení](how-to-modify-assessment.md) posouzení.
