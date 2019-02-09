---
title: Plánování migrace prostředků IaaS z modelu classic na Azure Resource Manager | Dokumentace Microsoftu
description: Plánování migrace prostředků IaaS z modelu classic na Azure Resource Manager
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
ms.openlocfilehash: 540abeed3587959af5ca229f59343774b824547b
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982892"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plánování migrace prostředků IaaS z modelu classic na Azure Resource Manager
Zatímco Azure Resource Manager nabízí spoustu skvělých funkcí, je velmi důležité naplánovat, aby to bylo jistě hladký chod průběhu migrace. Plánování zbavuje čas zajistí, že není narazíte na problémy při provádění aktivity migrace.

> [!NOTE]
> Následující pokyny se výrazně přispěl Azure zákazníka poradní tým a architekty řešení Cloud práce s našimi zákazníky na migraci rozsáhlých prostředích. Jako takové tohoto dokumentu bude pokračovat na aktualizaci jako objeví nová schémata úspěch, proto zkontrolujte, od času na dobu, pokud jsou k dispozici žádná nová doporučení.

Existují čtyři hlavní fáze průběh migrace:<br>

![Fáze migrace](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Plánování

### <a name="technical-considerations-and-tradeoffs"></a>Technické aspekty a nevýhody

V závislosti na vaší velikosti technické požadavky, geografické oblasti a provozní postupy můžete chtít zvážit:

1. Proč se vyžaduje Azure Resource Manageru pro vaši organizaci?  Co jsou obchodní důvody pro migraci?
2. Co jsou technických důvodů pro Azure Resource Manager?  Co (pokud existuje) další služby Azure byste chtěli využívat?
3. Které aplikace (nebo sady virtuálních počítačů) je součástí migrace?
4. Jaké scénáře jsou podporovány s migrací rozhraní API?  Zkontrolujte [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. Provozní týmy nyní podporuje aplikace nebo virtuální počítače v modelu Classic a Azure Resource Manageru?
6. Jak (pokud vůbec) Azure Resource Manageru mění nasazení virtuálního počítače, správu, monitorování a vytváření sestav procesy?  Potřeba aktualizovat skripty nasazení?
7. Co je komunikace v úmyslu upozorňovat účastníky (koncoví uživatelé, počet vlastníků aplikace a vlastníkům infrastruktury)?
8. V závislosti na složitosti prostředí, mělo by být období údržby kde je aplikace není k dispozici koncovým uživatelům a vlastníkům aplikace?  Pokud ano, jak dlouho?
9. Co je plán školení k zajištění, že zúčastněné strany jsou-li hlubší znalosti a zdatní v Azure Resource Manageru?
10. Co je program management nebo řízení plánu projektu migrace?
11. Co jsou časové osy pro migraci Azure Resource Manageru a další související technologie mapy?  Jsou optimálně odpovídají?

### <a name="patterns-of-success"></a>Vzory úspěch

Úspěšné zákazníkům podrobné plány, kde předchozí otázky popsáno, zdokumentované a řídí.  Zajistěte, aby že plány migrace se široce předávají sponzorů a zúčastněnými stranami.  Vybavení sami se znalostí o možnostech migrace; Doporučujeme přečtení dokumentu migrace nastavenou níže.

* [Přehled o platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu classic na Azure Resource Manager pomocí Powershellu](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu classic na Azure Resource Manageru pomocí rozhraní příkazového řádku](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Projděte si nejčastější dotazy o migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Aby se zabránilo nástrahy

- Chyba při plánování.  Jsou prověřené technologie kroků migrace a výsledkem je předvídatelný.
- Předpokládáme, že platforma podporovaná rozhraní API migrace bude účet pro všechny scénáře. Přečtěte si [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) pochopit, jaké postupy se podporují.
- Neplánuje potenciální výpadek aplikace pro koncové uživatele.  Naplánujte dostatek vyrovnávací paměti adekvátní upozornit koncoví uživatelé čas potenciálně není k dispozici aplikace.


## <a name="lab-test"></a>Testovací laboratoře

**Replikovat vaše prostředí a proveďte testovací migrace.**
  > [!NOTE]
  > Přesné replikace do stávajícího prostředí provádí pomocí komunitou nástroj, který není oficiálně podporován Microsoft Support. Proto je **volitelné** kroku, ale je nejlepší způsob, jak zjistit problémy bez zásahu do produkčních prostředí. Pokud pomocí nástroje komunitou, není možné zvolit, pak si můžete přečtěte o ověření/přípravy nebo přerušení zkušební spuštění doporučení níže.
  >

  Provádění testu lab vašemu konkrétnímu scénáři (výpočetní, síťové a úložiště) je nejlepší způsob, jak zajistit hladký průběh migrace. To vám pomůže zajistit:

  - Zcela samostatné testovacího prostředí nebo existující neprodukčním prostředí pro testování. Doporučujeme, abyste zcela samostatné testovacího prostředí, které je možné migrovat opakovaně a lze destructively upravit.  Skripty pro shromažďování/hydrát metadata ze skutečných předplatná jsou uvedeny níže.
  - Je vhodné vytvořit testovací prostředí v samostatné předplatné. Důvodem je, že testovací prostředí se deaktivuje opakovaně a mít samostatné, izolované předplatného se sníží pravděpodobnost, že něco reálné získat omylem odstraní.

  To lze provést pomocí nástroje AsmMetadataParser. [Další informace o tomto nástroji zde](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Vzory úspěch

Následující byly problémy zjištěné v mnoha větší migrace. Nejedná se o vyčerpávající seznam a by měla odkazovat na [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) další podrobnosti.  Může nebo nemusí mít tyto technické problémy, ale pokud to uděláte před provedením migrace řešení zajistí plynulost.

- **Proveďte ověření/přípravy nebo přerušení spuštění zkušební** – to je pravděpodobně nejdůležitější krok, kterým zajistíte modelu Classic na úspěšné provedení migrace Azure Resource Manageru. Migrace rozhraní API má tři hlavní kroky: Ověření, připravte a potvrďte. Ověření se načíst stav prostředí classic a vrátí výsledek všechny problémy. Ale vzhledem k tomu, že některé problémy mohou existovat v zásobníku Azure Resource Manageru, ověřit nezachytí všechno. Dalším krokem v procesu migrace Prepare vám pomůže vystavit těchto problémů. Připravte se přesunout metadat z modelu Classic na Azure Resource Manager, ale nebude potvrzení přesunu a se neodebere ani něco na straně Classic změnit. Spustit zkušební zahrnuje Příprava na migraci a přerušení (**není potvrzování**) Příprava na migraci. Cílem ověření/připravit/přerušení zkušební spuštění je chcete zobrazit všechna metadata v zásobníku Azure Resource Manageru, jej prozkoumat (*programově, nebo portál*) a ověřte, že všechno, co migruje správně a seznámení se základními technické problémy.  Také poskytne vám představu o dobou trvání migrace, odpovídajícím způsobem plánovat výpadku.  Ověřením/připravit/přerušení nezpůsobí žádné výpadky uživatele; je tedy nenarušující k použití aplikací.
  - Položky dole potřeba vyřešit před spuštěním suchého, ale zkušební spuštění testu se také bezpečně vyprázdní si tyto přípravné kroky, pokud je provedena. Během migrace enterprise zjistili jsme zkušební spuštění bude bezpečný a neocenitelný při řízení způsob, jak zajistit přípravu migrace.
  - Při přípravě běží, ovládací prvek roviny (operace Azure správy) uzamknou celé virtuální sítě, takže provedeny žádné změny můžou být metadata virtuálního počítače během ověření/připravit/přerušení.  Jinak, ale všechny funkce aplikace (VP, virtuální počítač využití atd.), zůstanou beze změn.  Uživatelé virtuálních počítačů nebude vědět, se provádí zkušební spuštění.

- **Express okruhy směrování a síť VPN**. Aktuálně Express Route brány pomocí autorizačních odkazů se nedají migrovat bez výpadku. Alternativní řešení, najdete v části [migrace okruhů ExpressRoute a přidružených virtuálních sítí z klasického modelu nasazení Resource Manageru](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Rozšíření virtuálních počítačů** – rozšíření virtuálních počítačů jsou potenciálně jedním z největších problémů při migraci spuštěných virtuálních počítačů. Náprava rozšíření virtuálních počítačů může trvat upwards of 1 – 2 dny, tak podle toho naplánujte.  Pracovní agent služby Azure, je potřeba nahlásit stav rozšíření virtuálního počítače z běžících virtuálních počítačů. Pokud se stav vrátí zpět jako chybný ve spuštěném virtuálním počítači, tím se zastaví migrace. Samotný agent nemusí být v pořadí pracovní povolení migrace, ale pokud rozšíření ve virtuálním počítači, pak oba pracovní agenta a odchozí připojení k Internetu (s DNS) bude potřeba k migraci do posunout vpřed.
  - Pokud během migrace, všechna rozšíření virtuálních počítačů s výjimkou BGInfo verze 1 dojde ke ztrátě připojení k serveru DNS. \* musí být nejprve odebrány ze všech virtuálních počítačů před připravit migraci a následně znovu přidat zpět k virtuálnímu počítači po migraci na Azure Resource Manageru.  **Toto je pouze pro virtuální počítače, na kterých běží.**  Pokud virtuální počítače se zastaví uvolnění, není potřeba odebrat rozšíření virtuálních počítačů.

  > [!NOTE]
  > Mnoho rozšíření, jako je Azure diagnostics a security center monitorování bude přeinstalovat sami po migraci, je tak odeberete nepředstavuje žádný problém.

  - Kromě toho, ujistěte se, že skupiny zabezpečení sítě nejsou omezení odchozí internetový přístup. To může nastat několik konfigurací skupiny zabezpečení sítě. Odchozí přístup k Internetu (a DNS) je potřeba pro rozšíření virtuálních počítačů k migraci do Azure Resource Manageru.
  - Dvě verze rozšíření BGInfo neexistuje a se nazývají verze 1 a 2.  

      - Pokud virtuální počítač používá rozšíření BGInfo verze 1, můžete nechat toto rozšíření je. Rozhraní API pro migraci přeskočí toto rozšíření. Je možné přidat rozšíření BGInfo po migraci.
      - Pokud virtuální počítač používá rozšíření BGInfo založenými na JSON verze 2, virtuální počítač vytvořil, pomocí webu Azure portal. Migrace rozhraní API zahrnuje toto rozšíření při migraci do Azure Resource Manageru, předpokladu, že agent funguje a má odchozí přístup k Internetu (a DNS).

  - **1. možnost nápravy**. Pokud víte, že vaše virtuální počítače nebudou mít odchozí přístupem k Internetu, služba DNS práci a práce agenti Azure na virtuálních počítačích, odinstalujte všechny rozšíření virtuálních počítačů v rámci přípravy migraci, přeinstalujte rozšíření virtuálních počítačů po dokončení migrace.
  - **2. možnost nápravy**. Pokud jsou příliš velký mezní rozšíření virtuálních počítačů, Další možností je vypnout a uvolnit všechny virtuální počítače před migrací. Migrace uvolněno virtuálních počítačů a pak je restartujte na straně Azure Resource Manageru. Výhody zde je, rozšíření virtuálních počítačů bude migrovat. Nevýhodou je, že se ztratí všechny veřejné směřující virtuálních IP adres (to může být jiné starter), a samozřejmě virtuální počítače vypne způsobí mnohem větší vliv na pracovní aplikace.

    > [!NOTE]
    > Pokud zásady služby Azure Security Center je nakonfigurovaný na spuštěné virtuální počítače migrované, zásady zabezpečení je potřeba zastavit před odebráním rozšíření, v opačném případě zabezpečení rozšíření monitorování bude automaticky znovu nainstalovány ve virtuálním počítači po odebrání.

- **Skupiny dostupnosti** – pro virtuální síť (vNet) pro migraci do Azure Resource Manageru, virtuální počítače s omezením musí být v jedné skupině dostupnosti nasazení Classic (tj. Cloudová služba) nebo virtuální počítače nesmí být žádné skupiny dostupnosti. S více než jedné skupině dostupnosti v cloudové službě není kompatibilní s Azure Resource Managerem a migrace zastaví.  Kromě toho nemůže existovat několik virtuálních počítačů ve skupině dostupnosti a některé virtuální počítače nejsou ve skupině dostupnosti. Chcete-li tento problém vyřešit, je potřeba opravit nebo změnit pořadí cloudové služby.  Plánování odpovídajícím způsobem, jak to může být časově náročné.

- **Nasazení webové nebo pracovní Role** -obsahující webové a pracovní role Cloud Services nejde migrovat na Azure Resource Manager. Webové a pracovní role musí být nejprve odebrány z virtuální sítě, před zahájením migrace.  Typické řešení je můžete data přesunout instance webové nebo pracovní role na samostatné klasická virtuální síť, která je také propojena k okruhu ExpressRoute, nebo migrovat kód novější PaaS App Services (této diskuse je nad rámec tohoto dokumentu). V předchozí případ znovu nasadit, vytvořit novou virtuální síť modelu Classic, přesunutí nebo opětovného nasazení webové nebo pracovní role, které chcete tuto novou virtuální síť a pak odstranit nasazení z virtuální sítě, který se přesouvá. Žádné požadované změny kódu. Nové [partnerské vztahy virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md) funkce slouží k navázání partnerského vztahu mezi společně klasickou virtuální síť obsahující webové nebo pracovní role a dalším virtuálním sítím ve stejné oblasti Azure, jako je například virtuální síť se migrovat (**po dokončení migrace virtuální sítě jako partnerské virtuální sítě se nedají migrovat**), proto poskytuje stejné funkce bez ztráty výkonu a žádné snížení latence nebo šířka pásma. Zadaný přidání [partnerské vztahy virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md), můžete nyní snadno minimalizovat nasazení webové nebo pracovní role a nedochází k blokování migrace do Azure Resource Manageru.

- **Azure Resource Manageru kvóty** -oblasti Azure, které mají samostatné kvóty a omezení pro Classic a Azure Resource Manageru. I když ve scénáři migrace není spotřebovávanou nový hardware *(jsme už prohození existujících virtuálních počítačů z modelu Classic na Azure Resource Manager)*, stále potřebují být v místě s dostatečnou kapacitou před kvóty správce prostředků Azure migraci můžete začít. Tady jsou hlavní omezení, které jsme viděli způsobit problémy.  Otevření lístku podpory kvótu zvýšit limity.

    > [!NOTE]
    > Tato omezení musí být vyvolána ve stejné oblasti jako vaše aktuální prostředí k migraci.
    >

    - Síťová rozhraní
    - Nástroje pro vyrovnávání zatížení
    - Veřejné IP adresy
    - Statické veřejné IP adresy
    - Jádra
    - Network Security Groups (Skupiny zabezpečení sítě)
    - Směrovací tabulky

    Můžete zkontrolovat aktuální kvóty správce prostředků Azure pomocí následujících příkazů v nejnovější verzi Azure Powershellu.
    
    [!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

    **COMPUTE** *(počet jader, sady dostupnosti)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Síť** *(virtuálních sítí, statických veřejných IP adres, veřejné IP adresy nebo skupiny zabezpečení sítě, síťová rozhraní, nástrojů pro vyrovnávání zatížení, směrovací tabulky)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Úložiště** *(účet služby Storage)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Rozhraní API Azure Resource Manageru limitů omezování** – Pokud máte dostatečně velký prostředí (např.) > 400 virtuálních počítačů ve virtuální síti), pravděpodobně dojde k rozhraní API výchozí omezení pro zápis (aktuálně `1200 writes/hour`) v Azure Resource Manageru. Před zahájením migrace, by měla vyvolat lístku podpory zvýšit tento limit pro vaše předplatné.


- **Došlo k vypršení časového limitu virtuálních počítačů Stav zřizování** – Pokud je jakýkoli virtuální počítač má stav `provisioning timed out`, to je potřeba vyřešit před migrací. Jediný způsob, jak to provést, je s výpadky zrušení zřízení/neukončil virtuálního počítače (delete, zachovat na disku a znovu vytvořte virtuální počítač).

- **Neznámý stav virtuálního počítače** – Pokud migrace zastaví z důvodu `role state unknown` chybová zpráva, zkontrolujte virtuálního počítače pomocí portálu a ujistěte se, je spuštěna. Tato chyba obvykle zmizí své vlastní (nejsou požadována žádná náprava) za pár minut a často přechodný typ často dochází během virtuálního počítače `start`, `stop`, `restart` operace. **Doporučené postupy:** znovu zkuste migrovat znovu za pár minut.

- **Fabric Cluster neexistuje** – v některých případech některé virtuální počítače nejde migrovat z různých důvodů liché. Jedním z těchto případů známé je pokud byl virtuální počítač nedávno vytvořili (během posledního týdne, aby) a stalo objevil clusteru služby Azure, který ještě není vybaven pro úlohy Azure Resource Manageru.  Zobrazí se chybová zpráva `fabric cluster does not exist` a tento virtuální počítač nejde migrovat. Čekání na pár dní obvykle konkrétní problém vyřešíte podle clusteru se brzy zobrazí podporou Azure Resource Manageru. Však jeden okamžité alternativním řešením je `stop-deallocate` virtuální počítač, pak předat dál pokračovat v migraci, a spusťte virtuální počítač zálohovat v Azure Resource Manageru po migraci.

### <a name="pitfalls-to-avoid"></a>Aby se zabránilo nástrahy

- Není klávesové zkratky a vynechat možnost ověřit/připravit/přerušení zkušební spuštění migrace.
- Většina. Pokud ne, potenciálních problémů bude přinášet během kroků ověření/připravit/přerušení.

## <a name="migration"></a>Migrace

### <a name="technical-considerations-and-tradeoffs"></a>Technické aspekty a nevýhody

Teď jste připraveni vzhledem k tomu, že jste už pracovali prostřednictvím známých problémů s vaším prostředím.

Pro skutečné migrace může být vhodné vzít v úvahu:

1. Plánování a virtuální síť (nejmenší jednotka migrace) s zvýšení priority plánování.  Nejprve provést jednoduchý virtuální sítě a průběh s složitější virtuálními sítěmi.
2. Většina zákazníků budou mít testovacím a produkčním prostředí.  Poslední naplánujte produkční.
3. **(VOLITELNÉ)**  Naplánovat výpadek kvůli údržbě s dostatečným vyrovnávací paměti pro případ, neočekávané vzniku.
4. Komunikovat a bylo v souladu s podpůrnými týmy v případě, že vzniknou problémy.

### <a name="patterns-of-success"></a>Vzory úspěch

Technické pokyny z _testovací laboratoře_ oddílu by mělo být považována za a zmírnit před skutečné migrace.  Migrace s odpovídající testování, je ve skutečnosti událostmi.  Pro produkční prostředí může být užitečné mít další podporu, jako je například důvěryhodným partnerem Microsoftu nebo Microsoft Premier Support services.

### <a name="pitfalls-to-avoid"></a>Aby se zabránilo nástrahy

Testování není plně může způsobit problémy a zpoždění při migraci.  

## <a name="beyond-migration"></a>Nad rámec migrace

### <a name="technical-considerations-and-tradeoffs"></a>Technické aspekty a nevýhody

Teď, když jste v Azure Resource Manageru, maximalizujte platformu.  Přečtěte si [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md) najdete informace o další výhody.

Co je třeba zvážit:

- Sdružování migrace s ostatními aktivitami.  Většina zákazníků optimalizované pro okna údržby aplikace.  Pokud ano, můžete chtít povolit další možnosti Azure Resource Manageru, třeba šifrování a migrace na spravované disky pomocí tohoto výpadku.
- Návštěvě technické a obchodní důvody pro Azure Resource Manageru; Povolte další služby k dispozici jenom v Azure Resource Manageru, které platí pro vaše prostředí.
- Modernizace prostředí se službami PaaS.

### <a name="patterns-of-success"></a>Vzory úspěch

Být výstižný, na které služby Chcete teď umožňují v Azure Resource Manageru.  Mnoho zákazníků níže atraktivních jejich prostředí Azure:

- [Řízení přístupu podle rolí](../../role-based-access-control/overview.md).
- [Šablony Azure Resource Manageru pro snazší a víc řízené nasazení](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Značky](../../azure-resource-manager/resource-group-using-tags.md).
- [Aktivity řízení](../../azure-resource-manager/resource-group-audit.md)
- [Zásady Azure](../../azure-policy/azure-policy-introduction.md)

### <a name="pitfalls-to-avoid"></a>Aby se zabránilo nástrahy

Mějte na paměti, proč jste začali tento z modelu Classic do cesty k migraci do Azure Resource Manageru.  Jaké byly obchodních důvodů, proč původní? Dosáhnout obchodní důvod?


## <a name="next-steps"></a>Další postup

* [Přehled o platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu classic na Azure Resource Manager pomocí Powershellu](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu classic na Azure Resource Manageru pomocí rozhraní příkazového řádku](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Brána VPN z modelu classic do Resource Manageru migrace](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migrace okruhů ExpressRoute a přidružených virtuálních sítí z klasického modelu nasazení Resource Manageru](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Projděte si nejčastější dotazy o migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
