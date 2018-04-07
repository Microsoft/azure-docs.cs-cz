---
title: Plánování migrace prostředky infrastruktury z klasického do Azure Resource Manageru | Microsoft Docs
description: Plánování migrace z klasického do Azure Resource Manageru prostředků IaaS
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 32e595b6ae01e77289a81734f61b9036835dd2fd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plánování migrace z klasického do Azure Resource Manageru prostředků IaaS
Zatímco Azure Resource Manager nabízí mnoho funkcí úžasné, je důležité k naplánování vaší cesty migrace zkontrolujte, zda věcí bezproblémové. Výdaje čas na plánování zajistí nedochází chybám při provádění migrace aktivity.

> [!NOTE]
> Následující pokyny se výraznou přispěli k Azure zákazníka poradní tým a řešení cloudové architekty práce s zákazníků v rozsáhlých prostředích migrace. Jako takový tohoto dokumentu bude nadále získat aktualizovat, protože vznikat nové vzorce úspěšné, tak to zkuste od času na dobu, pokud jsou k dispozici žádná nová doporučení.

Existují čtyři obecné fáze migrace cesty:<br>

![Migrace fáze](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Plánování

### <a name="technical-considerations-and-tradeoffs"></a>Technické aspekty a kompromisy

V závislosti na velikosti technické požadavky, zeměpisných a provozní postupy je vhodné zvážit:

1. Proč se Azure Resource Manager požaduje pro vaši organizaci?  Jaké jsou obchodní důvody pro migraci?
2. Jaké jsou technické důvody pro Azure Resource Manager?  Co (pokud existuje) další služby Azure Pokud chcete využít?
3. Které aplikace (nebo sady virtuálních počítačů) je součástí migrace?
4. Jaké scénáře jsou podporovány migrace rozhraní API?  Zkontrolujte [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. Týmům provozní nyní podporuje aplikace nebo virtuální počítače v Classic a Azure Resource Manager?
6. Jak (pokud vůbec) Azure Resource Manager mění nasazení virtuálního počítače, správu, monitorování a vytváření sestav procesy?  Skripty nasazení potřebujete aktualizovat?
7. Co je komunikace plánu výstrahy zúčastněným stranám (koncovým uživatelům, vlastníci aplikace a vlastníkům infrastruktury)?
8. V závislosti na složitosti prostředí mělo by být období údržby kde aplikace je k dispozici koncovým uživatelům a vlastníkům aplikace?  Pokud ano, jak dlouho?
9. Co je plán školení zajistit zúčastněným stranám znalostmi a znalosti ve službě Správce prostředků Azure?
10. Co je program správy nebo správy plánu projektu pro migraci?
11. Jaké jsou časové osy pro migraci správce prostředků Azure a další související technologie silniční mapy?  Je jejich optimálně zarovnán?

### <a name="patterns-of-success"></a>Vzory úspěch

Úspěšné zákazníkům obsahují podrobné plány, kde jsou popsané, zdokumentované a řídí předchozí dotazy.  Zajistěte, aby že plány migrace se široce předávají u sponzorů a zúčastněnými stranami.  Vybavit sami s informací o možnostech migrace; čtení tímto dokumentem migrace nastavit níže důrazně doporučujeme.

* [Přehled platformy podporované migrace z klasického do Azure Resource Manageru prostředků IaaS](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z klasického do Azure Resource Manageru pomocí prostředí PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Pomocí rozhraní příkazového řádku můžete migrovat prostředky infrastruktury z classic do Azure Resource Manageru](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Komunita nástroje asistence s migrace z klasického do Azure Resource Manageru prostředků IaaS](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Přečtěte si nejčastější dotazy o migraci prostředky infrastruktury jako služby z klasického do Azure Resource Manageru](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Aby se zabránilo nástrahy

- Chyba při plánování.  Technologie kroky pro migraci jsou ověřené a výsledek je předvídatelný.
- Předpokládá, že platforma podporovaná migrace rozhraní API bude účet pro všechny scénáře. Pro čtení [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) pochopit, jaké postupy se podporují.
- Není plánování potenciální výpadek aplikace pro koncové uživatele.  Naplánujte nedostatek vyrovnávací paměti adekvátní varování před koncovým uživatelům doba potenciálně není k dispozici aplikace.


## <a name="lab-test"></a>Testovací laboratoře

**Replikovat vaše prostředí a provést migraci testu**
  > [!NOTE]
  > Přesný replikace vaše stávající prostředí se spustí pomocí nástroje podílí komunity, který není oficiálně podporován Microsoft Support. Proto je **volitelné** krok, ale je nejlepší způsob, jak zjistit problémy bez zásahu do provozní prostředí. Pokud pomocí nástroje podílí komunity není možné, přečtěte si o ověření a příprava nebo zrušit vyzkoušet vysílání doporučení níže.
  >

  Provádění testu testovacího prostředí vaší přesný scénář (výpočty, síť a úložiště) je nejlepší způsob, jak zajistit bezproblémovou migraci. To pomůže zajistit:

  - Zcela samostatné testovacího prostředí nebo stávajícího mimo produkční prostředí pro testování. Doporučujeme, abyste zcela samostatné testovacím prostředí, které mohou být migrovány opakovaně a nelze jej destructively změnit.  Skripty za účelem shromažďování nebo hydrát metadata z reálného odběry jsou uvedeny níže.
  - Je vhodné vytvořit testovací prostředí v samostatné předplatné. Důvodem je, že testovací prostředí se opakovaně deaktivuje a s samostatné, izolované předplatné snížíte pravděpodobnost, že něco skutečných získat omylem odstraní.

  To můžete udělat pomocí nástroje AsmMetadataParser. [Další informace o tomto nástroji sem](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Vzory úspěch

Následující byly problémy zjištěné v mnoha větší migrace. Nejedná se o vyčerpávající seznam a by měla odkazovat na [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) další podrobnosti.  Může nebo nemusí dojde tyto technické problémy, ale v takovém případě tyto před pokusem o migraci řešení zajistí plynulejší práci.

- **Proveďte vyzkoušet vysílání ověřením a příprava nebo zrušit** -Toto je možná nejdůležitější krok zajistit Classic na úspěšné provedení migrace správce prostředků Azure. Migrace rozhraní API obsahuje tři hlavní kroky: ověření, přípravě a provést. Ověření se načíst stav prostředí classic a vrátit výsledek všechny problémy. Ale vzhledem k tomu, že některé problémy mohou existovat v zásobníku Azure Resource Manager, ověřit nebude catch vše. Dalším krokem v procesu migrace, Příprava vám pomůže zveřejnění těchto problémů. Příprava bude přesunout metadata z Classic do Azure Resource Manager, ale nebude Potvrdit přesunutí a není odebrat ani změnit nic na straně Classic. Vyzkoušet vysílání zahrnuje přípravy migrace, pak přerušení (**není potvrzení**) Příprava na migraci. Cílem ověření a příprava nebo zrušit vyzkoušet vysílání je chcete zobrazit všechny metadat v zásobníku Azure Resource Manager, jej prozkoumat (*prostřednictvím kódu programu nebo portálu*) a ověřte, že všechno migruje správně a fungovat prostřednictvím technical problémy.  Je také získáte představu o dobou trvání migrace, podle toho naplánovat výpadek.  Ověření a příprava nebo zrušit nezpůsobí žádné výpadky uživatele; je proto pro použití aplikací omezovaly.
  - Níže uvedených položek bude muset být vyřešeny před vyzkoušet vysílání, ale testu vyzkoušet vysílání se taky bezpečně vyprázdní se tyto přípravné kroky, pokud jejich provedena. Během migrace enterprise jsme zjistili vyzkoušet vysílání zajistit bezpečnost a neocenitelnou pomocí způsob, jak zajistit přípravu migrace.
  - Při přípravě běží ovládacího prvku se uzamkne roviny (Azure management operace) pro celý virtuální síť, tak na metadata virtuálního počítače můžete provedeny žádné změny během ověření a příprava nebo zrušit.  Jinak, ale všechny funkce aplikací (VP, virtuálních počítačů využití, atd.), zůstanou beze změn.  Uživatelé virtuální počítače nebude vědět, že vyzkoušet vysílání je spouštěna.

- **Express okruhy směrování a síť VPN**. Aktuálně Express trasy brány s autorizace odkazy se nedají migrovat bez výpadků. Alternativní řešení, najdete v části [okruhy ExpressRoute migrovat a přidružené virtuální sítě z klasického modelu nasazení Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Rozšíření virtuálního počítače** -rozšíření virtuálního počítače jsou potenciálně jeden z největších roadblocks migrace spuštěné virtuální počítače. Náprava rozšíření virtuálního počítače může trvat upwards of 1 – 2 dny, takže Plánujte odpovídajícím způsobem.  Funkční Azure agent je potřeba k hlášení zpět stav rozšíření virtuálního počítače spuštěných virtuálních počítačů. Pokud stav, je vrácen chybný pro spuštění virtuálního počítače, to se zastaví migrace. Vlastní agent není nutné, aby pracovní povolte migraci, ale pokud rozšíření ve virtuálním počítači, pak oba pracovní agenta a odchozí připojení k Internetu (pomocí DNS) bude potřeba k migraci přejdete na následující.
  - Pokud během migrace, všechna rozšíření virtuálních počítačů s výjimkou BGInfo verze 1 se ztratí připojení k serveru DNS. \* nutné nejprve odebrat z každý virtuální počítač před Příprava migrace a následně znovu přidat zpět do virtuálního počítače po migraci správce prostředků Azure.  **Toto je pouze pro virtuální počítače, které jsou spuštěné.**  Pokud virtuální počítače jsou zastaveny deallocated, rozšíření virtuálního počítače není třeba je odebrat.

  > [!NOTE]
  > Mnoho rozšíření, jako jsou Azure diagnostics a security center monitorování bude přeinstalovat sami po migraci, odebrání, takže se nejedná o problém.

  - Kromě toho se ujistěte, skupiny zabezpečení sítě nejsou omezení odchozí přístup k Internetu. K tomu dochází s některé konfigurace skupin zabezpečení sítě. Odchozí přístup k Internetu (a DNS) je potřeba pro rozšíření virtuálního počítače, který se má migrovat na Azure Resource Manager.
  - Dvě verze rozšíření BGInfo neexistuje a se nazývají verze 1 a 2.  

      - Pokud virtuální počítač používá rozšíření BGInfo verze 1, můžete ponechat toto rozšíření, jako je. Migrace API přeskočí toto rozšíření. Rozšíření BGInfo mohou být přidány po migraci.
      - Pokud virtuální počítač používá rozšíření verze 2 na základě JSON BGInfo, virtuální počítač byl vytvořen pomocí portálu Azure. Migrace rozhraní API obsahuje toto rozšíření při migraci na Azure Resource Manager, agent funguje a má odchozí přístup k Internetu (a DNS).

  - **Možnost nápravy 1**. Pokud víte, že virtuální počítače nebudou mít odchozí přístupem k Internetu, funkční službu DNS a práci agenty Azure na virtuálních počítačích, pak odinstalovat všechny rozšíření virtuálního počítače jako součást migrace před Příprava, znovu nainstalujte rozšíření virtuálního počítače po dokončení migrace.
  - **Možnost nápravy 2**. Pokud rozšíření virtuálního počítače z mezní příliš velký, Další možností je vypnutí nebo navrácení všechny virtuální počítače před migrací. Migrace deallocated virtuálních počítačů a potom je znovu spustit na straně Azure Resource Manager. Zde výhodou je, že bude migrovat rozšíření virtuálního počítače. Nevýhodou je, že budou ztraceny všechny veřejných virtuálních IP adres (to může být jiný starter), a samozřejmě se zastaví virtuální počítače způsobuje mnohem větší vliv na pracovní aplikace.

    > [!NOTE]
    > Pokud zásady služby Azure Security Center je nakonfigurovaná pro virtuální počítače běžící migrovaného, zásady zabezpečení musí být zastaven před odebráním rozšíření, jinak zabezpečení rozšíření monitorování bude nutné přeinstalovat automaticky na virtuální počítač po odebrání.

- **Skupiny dostupnosti** – pro virtuální síť (vNet) migrovat na Azure Resource Manager, obsahují virtuální počítače musí být v jedné sadě dostupnosti nasazení Classic (tj. Cloudová služba) nebo virtuální počítače nesmí být v žádné sadě dostupnosti. S více než jeden dostupnosti v rámci cloudové služby není kompatibilní s Azure Resource Manager a migrace se zastaví.  Kromě toho nemůže být některé virtuální počítače v nastavení dostupnosti a některé virtuální počítače není v nastavení dostupnosti. Chcete-li tento problém vyřešili, musíte k nápravě nebo změnit pořadí cloudové služby.  Naplánujte podle toho, jak to může být časově náročná.

- **Nasazení Role web nebo Worker** -obsahující webové a pracovní role cloudové služby nelze provést migraci na Azure Resource Manager. Role web nebo worker musí být nejprve odebrány z virtuální sítě, aby bylo možné spustit migraci.  Typické řešením je právě instance role web nebo worker přesunout do samostatných klasické virtuální sítě, která souvisí s okruhem ExpressRoute, nebo k migraci kód na novější PaaS App Services (Toto pojednání je nad rámec tohoto dokumentu). V první znovu nasaďte případu, vytvořit novou virtuální síť v klasickém, přesunutí nebo opětovném nasazení role web nebo worker do této nové virtuální sítě a pak odstranit nasazení z virtuální sítě přesouvání. Bez nutnosti změn kódu. Nové [partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md) funkce slouží k peer společně klasickou virtuální síť obsahuje role pro web nebo worker a jiných virtuálních sítí ve stejné oblasti Azure, jako je například virtuální sítě se migrovat (**po dokončení migrace virtuální sítě, jako peered virtuální sítě se nedají migrovat**), proto poskytuje stejné funkce s nedošlo ke ztrátě výkonu a žádné postihy latenci nebo šířky pásma. Zadané přidání [partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md), nasazení role web nebo worker lze nyní snadno zmírnit a nejsou blokovány migrace do Azure Resource Manageru.

- **Azure kvóty správce prostředků** -oblastí Azure mají samostatné kvóty nebo limity pro Classic a Azure Resource Manager. I když ve scénáři migrace není spotřebovávanou nový hardware *(jsme se vzájemná záměna existujících virtuálních počítačů z klasického do Azure Resource Manageru)*, stále musí být v místě s dostatečnou kapacitu než kvóty správce prostředků Azure migrace můžete začít. V následujícím seznamu jsou, že hlavní omezení, které jsme viděli způsobit problémy.  Otevřete lístek podpory kvótu zvýšit limity.

    > [!NOTE]
    > Tyto limity potřebovat má být aktivována ve stejné oblasti jako vaše aktuální prostředí a migrovat.
    >

    - Síťová rozhraní
    - Služby vyrovnávání zatížení
    - Veřejné IP adresy
    - Statické veřejné IP adresy
    - Jádra
    - Network Security Groups (Skupiny zabezpečení sítě)
    - Směrovací tabulky

    Můžete zkontrolovat vaše aktuální kvóty správce prostředků Azure pomocí následujících příkazů s nejnovější verzi prostředí Azure PowerShell.

    **Výpočetní** *(jádra, skupiny dostupnosti)*

    ```powershell
    Get-AzureRmVMUsage -Location <azure-region>
    ```

    **Síť** *(virtuální sítě, statické veřejné IP adresy, veřejné IP adresy, skupin zabezpečení sítě, síťové rozhraní, nástroje pro vyrovnávání zatížení, směrovací tabulky)*

    ```powershell
    Get-AzureRmUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Úložiště** *(účet úložiště)*

    ```powershell
    Get-AzureRmStorageUsage
    ```

- **Azure Resource Manager API omezení** – Pokud máte prostředí dostatečně velký (např. > 400 virtuální počítače ve virtuální síti), může dosáhl rozhraní API výchozí omezení pro zápis (aktuálně `1200 writes/hour`) ve službě Správce prostředků Azure. Před zahájením migrace, by měla vyvolat lístek podpory zvýšit tento limit pro vaše předplatné.


- **Došlo k vypršení časového limitu virtuálních počítačů Stav zřizování** – v případě, že všechny virtuální počítač má stav `provisioning timed out`, to je potřeba vyřešit před migrací. Jediným způsobem, jak to udělat, je s výpadky zrušení zřízení nebo reprovisioning virtuálního počítače (delete, zachovat disku a znovu vytvořte virtuální počítač).

- **Stav virtuálního počítače RoleStateUnknown** – Pokud migrace zastaví z důvodu `role state unknown` chybová zpráva, zkontrolujte virtuálního počítače pomocí portálu a ujistěte se, jestli běží. Tato chyba obvykle zmizí jeho vlastní (žádná náprava vyžaduje) po několika minutách a je často typu přechodný často můžou vyskytnout během virtuálního počítače `start`, `stop`, `restart` operace. **Doporučená praxe:** znovu zkuste migrovat znovu za několik minut.

- **Fabric Cluster neexistuje** – v některých případech některé virtuální počítače nelze migrovat z různých důvodů liché. Jednu z těchto známé případech je pokud virtuální počítač byl nedávno vytvořili (v rámci poslední týden nebo tak) a přispěly ke clusteru služby Azure, který ještě není vybaven pro úlohy Azure Resource Manager nebude zobrazovat.  Obdržíte chybu, která uvádí, že `fabric cluster does not exist` a virtuální počítač nelze migrovat. Čeká se na několik dní se obvykle konkrétní problém vyřešit jako cluster bude brzy získat Azure Resource Manager povolena. Ale jeden okamžitou řešením je `stop-deallocate` virtuální počítač, pak pokračovat dál migrace a spusťte virtuální počítač zálohování ve službě Správce prostředků Azure po migraci.

### <a name="pitfalls-to-avoid"></a>Aby se zabránilo nástrahy

- Nezadávejte klávesové zkratky a migrace ověřením a příprava nebo zrušit vyzkoušet vysílání vynechejte.
- Většina, pokud nejsou všechny surface bude vaše potenciální problémy během ověření a příprava nebo zrušit.

## <a name="migration"></a>Migrace

### <a name="technical-considerations-and-tradeoffs"></a>Technické aspekty a kompromisy

Nyní jste připraveni vzhledem k tomu, že jste již dříve pracovali prostřednictvím známé problémy s vaším prostředím.

Pro skutečné migrace můžete chtít zvážit:

1. Plánování a plánování virtuální sítě (nejmenší unit migrace) s zvýšení priority.  Nejprve proveďte jednoduchý virtuální sítě a průběhu s složitější virtuální sítě.
2. Většina zákazníků bude mít mimo produkční a provozní prostředí.  Poslední naplánujte produkční.
3. **(VOLITELNÉ)**  Plánu údržby výpadek s dostatkem vyrovnávací paměti v případě, že se vyskytnout potíže se neočekávané.
4. Komunikovat s a zarovnané s vaší týmy podpory v případě, že se vyskytnout potíže.

### <a name="patterns-of-success"></a>Vzory úspěch

Technické pokyny z _testovací laboratoře_ část by měla být považovány za a omezeny před skutečné migrace.  S odpovídající testování, je migrace ve skutečnosti událostmi.  Pro produkční prostředí může být užitečné získat další podporu, jako je například důvěryhodné partnera společnosti Microsoft nebo služby Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Aby se zabránilo nástrahy

Testování není plně může způsobit problémy a zpoždění při migraci.  

## <a name="beyond-migration"></a>Nad rámec migrace

### <a name="technical-considerations-and-tradeoffs"></a>Technické aspekty a kompromisy

Teď, když jste ve službě Správce prostředků Azure, maximalizujte platformu.  Pro čtení [přehled nástroje Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) informace o další výhody.

Co je třeba zvážit:

- Sdružování migrace s ostatními aktivitami.  Většina zákazníků zvolit okna údržby aplikace.  Pokud ano, můžete chtít povolit další možnosti Azure Resource Manager například šifrování a migrace na discích spravovaných pomocí tento výpadek.
- Pokroku technické a podnikové důvody pro Azure Resource Manager; Povolte další k dispozici pouze na Azure Resource Manager služby, které se vztahují k vašemu prostředí.
- Modernizovat prostředí s PaaS služby.

### <a name="patterns-of-success"></a>Vzory úspěch

Být záměrné na jaké služby, které teď chcete povolit ve službě Správce prostředků Azure.  Mnoho zákazníků najít níže atraktivní, kterou pro jejich prostředí Azure:

- [Řízení přístupu podle rolí](../../azure-resource-manager/resource-group-overview.md#access-control).
- [Šablony Azure Resource Manageru pro snazší a víc řízené nasazení](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Značky](../../azure-resource-manager/resource-group-using-tags.md).
- [Ovládací prvek aktivity](../../azure-resource-manager/resource-group-audit.md)
- [Azure zásady](../../azure-policy/azure-policy-introduction.md)

### <a name="pitfalls-to-avoid"></a>Aby se zabránilo nástrahy

Mějte na paměti, proč jste spustili tento Classic do cesty migrace správce prostředků Azure.  Jaké byly obchodních důvodů, proč původní? Dosáhnout obchodního důvodu?


## <a name="next-steps"></a>Další postup

* [Přehled platformy podporované migrace z klasického do Azure Resource Manageru prostředků IaaS](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z klasického do Azure Resource Manageru pomocí prostředí PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Pomocí rozhraní příkazového řádku můžete migrovat prostředky infrastruktury z classic do Azure Resource Manageru](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Komunita nástroje asistence s migrace z klasického do Azure Resource Manageru prostředků IaaS](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Přečtěte si nejčastější dotazy o migraci prostředky infrastruktury jako služby z klasického do Azure Resource Manageru](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
