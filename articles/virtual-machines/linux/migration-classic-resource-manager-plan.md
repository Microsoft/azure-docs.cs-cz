---
title: Plánování migrace z klasického do Správce prostředků Azure
description: Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru
services: virtual-machines-linux
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: ff829e9ffbd6d6ae0766998e62634ac873afc748
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066664"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru

> [!IMPORTANT]
> Dnes asi 90 % virtuálních počítačích IaaS používá [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). února 2020, klasické virtuální počítačky byly zastaralé a budou plně vyřazeny v březnu 1, 2023. [Další informace]( https://aka.ms/classicvmretirement) o tomto vyřazení a [o tom, jak vás ovlivňuje](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Zatímco Azure Resource Manager nabízí spoustu úžasných funkcí, je důležité naplánovat cestu migrace, abyste zajistili hladký průběh. Čas na plánování zajistíte, že při provádění aktivit migrace nebudete narazíte na problémy.

> [!NOTE]
> K následujícím pokynům výrazně přispěl tým Azure Customer Advisory a architekti cloudových řešení, kteří spolupracují se zákazníky na migraci velkých prostředí. Jako takový tento dokument bude i nadále aktualizovat jako nové vzory úspěchu objeví, tak zkontrolujte, jak čas od času, zda existují nějaké nové doporučení.

Existují čtyři obecné fáze cesty migrace:

![Fáze migrace](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Plánování

### <a name="technical-considerations-and-tradeoffs"></a>Technické úvahy a kompromisy

V závislosti na velikosti technických požadavků, zeměpisných oblastech a provozních postupech můžete zvážit:

1. Proč je Azure Resource Manager požadovaný pro vaši organizaci?  Jaké jsou obchodní důvody migrace?
2. Jaké jsou technické důvody pro Azure Resource Manager?  Jaké (pokud nějaké) další služby Azure byste chtěli využít?
3. Která aplikace (nebo sady virtuálních počítačů) je součástí migrace?
4. Které scénáře jsou podporovány pomocí rozhraní API pro migraci?  Zkontrolujte [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. Budou vaše provozní týmy teď podporovat aplikace nebo virtuální počítače v klasickém i Azure Resource Manageru?
6. Jak (pokud vůbec) změní Azure Resource Manager vaše procesy nasazení virtuálních počítačových služeb, správy, monitorování a vytváření sestav?  Je třeba aktualizovat skripty nasazení?
7. Jaký je komunikační plán pro upozornění zúčastněných stran (koncových uživatelů, vlastníků aplikací a vlastníků infrastruktury)?
8. V závislosti na složitosti prostředí, by měla existovat období údržby, kdy aplikace není k dispozici pro koncové uživatele a vlastníky aplikací?  Pokud ano, na jak dlouho?
9. Jaký je plán školení, který zajistí, že zúčastněné strany budou mít znalosti a zběhlý ve Správci prostředků Azure?
10. Co je plán řízení programu nebo plánu řízení projektu pro migraci?
11. Jaké jsou časové osy pro migraci Azure Resource Manager a další související technologie silniční mapy?  Jsou optimálně vyrovnané?

### <a name="patterns-of-success"></a>Vzory úspěchu

Úspěšní zákazníci mají podrobné plány, kde jsou projednávány, dokumentovány a řízeny předchozí otázky.  Zajistěte, aby byly plány migrace široce sdělovány sponzorům a zúčastněným stranám.  Vybavte se znalostmi o možnostech migrace; čtení prostřednictvím níže uvedeného dokumentu migrace je vysoce doporučeno.

* [Přehled migrace prostředků IaaS podporovaných platformou z klasického do Správce prostředků Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrace prostředků IaaS z klasického do Správce prostředků Azure pomocí PowerShellu](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Použití příkazového příkazového příkazu k migraci prostředků IaaS z klasických do Správce prostředků Azure](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Nástroje komunity pro usnadnění migrace prostředků IaaS z klasických na Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Projděte si nejčastější dotazy týkající se migrace prostředků IaaS z klasického na Azure Resource Manager.](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Úskalí, aby se zabránilo

- Neplánování.  Technologické kroky této migrace jsou osvědčené a výsledek je předvídatelný.
- Předpoklad, že platforma podporovaná migrace ROZHRANÍ API bude účet pro všechny scénáře. Přečtěte si [nepodporované funkce a konfigurace, abyste pochopili,](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) jaké scénáře jsou podporovány.
- Neplánujete potenciální výpadek aplikace pro koncové uživatele.  Naplánujte dostatek vyrovnávací paměti, abyste odpovídajícím způsobem varovali koncové uživatele před potenciálně nedostupným časem aplikace.


## <a name="lab-test"></a>Laboratorní test

**Replikace prostředí a testovací migrace**
  > [!NOTE]
  > Přesná replikace stávajícího prostředí se provádí pomocí nástroje, který přispěl komunitou a který není oficiálně podporován podporou společnosti Microsoft. Proto je **volitelný** krok, ale je to nejlepší způsob, jak zjistit problémy bez dotyku produkčního prostředí. Pokud použití nástroje, který komunita přispěla, není možné, přečtěte si níže doporučení ověřit/připravit/přerušit běh na sucho.
  >

  Provedení laboratorního testu přesného scénáře (výpočetní prostředky, sítě a úložiště) je nejlepší způsob, jak zajistit hladkou migraci. To pomůže zajistit:

- Zcela samostatné testovací prostředí nebo existující neprodukční prostředí k testování. Doporučujeme zcela samostatnou laboratoř, kterou lze migrovat opakovaně a kterou lze destruktivně upravit.  Skripty pro shromažďování a hydrataci metadat ze skutečných předplatných jsou uvedeny níže.
- Je vhodné vytvořit testovací prostředí v samostatném předplatném. Důvodem je, že laboratoř bude opakovaně stržena a samostatné, izolované předplatné sníží pravděpodobnost, že něco skutečného bude omylem odstraněno.

  Toho lze dosáhnout pomocí nástroje AsmMetadataParser. [Přečtěte si více o tomto nástroji zde](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Vzory úspěchu

Následující byly problémy zjištěné v mnoha většímigrace. Toto není vyčerpávající seznam a měli byste odkazovat na [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) pro více podrobností. Můžete nebo nemusíte narazit na tyto technické problémy, ale pokud je vyřešíte před pokusem o migraci, zajistíte hladší zážitek.

- **Proveďte ověření/přípravu/přerušení na suchu** – toto je pravděpodobně nejdůležitější krok k zajištění úspěchu migrace klasické ho azure resource manageru. Rozhraní API pro migraci má tři hlavní kroky: Ověřit, Připravit a potvrdit. Ověření bude číst stav klasického prostředí a vrátí výsledek všech problémů. Však vzhledem k tomu, že některé problémy mohou existovat v zásobníku Azure Resource Manager, ověření nezachytí všechno. Dalším krokem v procesu migrace, Příprava pomůže odhalit tyto problémy. Příprava přesune metadata z Classic do Správce prostředků Azure, ale nebude potvrdit přesunutí a nebude odebrat nebo změnit nic na klasické straně. Běh na sucho zahrnuje přípravu migrace, pak přerušení **(nepotvrzení)** migrace připravit. Cílem ověření/přípravy/přerušení nasucho je zobrazit všechna metadata v zásobníku Azure Resource Manager, prozkoumat je *(programově nebo na portálu*) a ověřit, že vše migruje správně a pracovat prostřednictvím technických problémů.  To vám také dá pocit trvání migrace, takže můžete plánovat prostoje odpovídajícím způsobem.  Ověření/příprava/přerušení nezpůsobí žádné prostoje uživatele; proto je nenarušující použití aplikace.
  - Níže uvedené položky budou muset být vyřešeny před suchým provozem, ale test na suchý běh bude také bezpečně vypláchnout tyto přípravné kroky, pokud jsou vynechány. Během migrace podniků jsme zjistili, že běh na sucho je bezpečný a neocenitelný způsob, jak zajistit připravenost migrace.
  - Když je spuštěna příprava, rovina řízení (operace správy Azure) se uzamkne pro celou virtuální síť, takže během ověřování/přípravy/přerušení nelze provést žádné změny metadat virtuálního počítače.  Ale jinak nebude ovlivněna žádná funkce aplikace (RD, využití virtuálního počítače atd.).  Uživatelé virtuálních počítačů nebude vědět, že běh na sušiny se provádí.

- **Expresní trasy obvody a VPN**. V současné době nelze brány expresní trasy s autorizačními odkazy migrovat bez prostojů. Řešení najdete v [tématu Migrace okruhů ExpressRoute a přidružených virtuálních sítí z klasického modelu nasazení Resource Manageru](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Rozšíření virtuálních počítačů** – rozšíření virtuálních strojů jsou potenciálně jedním z největších zátarasů pro migraci spuštěných virtuálních počítačů. Sanace rozšíření virtuálních vod může trvat více než 1-2 dny, takže plán odpovídajícím způsobem.  Pracovní agent Azure je potřeba k nahlášení stavu rozšíření virtuálních počítače spuštěných virtuálních počítačích. Pokud se stav vrátí jako špatný pro spuštěný virtuální virtuální ms, zastaví se migrace. Samotný agent nemusí být v provozuschopném stavu, aby umožnil migraci, ale pokud na virtuálním počítači existují rozšíření, bude pro přechod vpřed potřeba jak pracovní agent, tak odchozí připojení k internetu (se službou DNS).
  - Pokud dojde ke ztrátě připojení k serveru DNS během migrace, všechna rozšíření virtuálních zařízení s výjimkou BGInfo v1. \* je třeba nejprve odebrat z každého virtuálního počítače před migrací připravit a následně znovu přidat zpět do virtuálního počítače po migraci Azure Resource Manager.  **To je jenom pro virtuální chod.**  Pokud jsou virtuální chod zastaveny, rozšíření virtuálních společností není nutné odebrat. **Poznámka:** Mnoho rozšíření, jako je diagnostika Azure a monitorování centra zabezpečení, se po migraci přeinstaluje, takže jejich odebrání není problém.
  - Kromě toho se ujistěte, že skupiny zabezpečení sítě neomezují odchozí přístup k Internetu. K tomu může dojít u některých konfigurací skupin zabezpečení sítě. Odchozí přístup k internetu (a DNS) je potřeba pro rozšíření virtuálních aplikací, které mají být migrovány do Správce prostředků Azure.
  - Existují dvě verze rozšíření BGInfo: v1 a v2.  Pokud virtuální počítač byl vytvořen pomocí portálu Azure nebo PowerShell, virtuální počítač bude pravděpodobně mít rozšíření v1 na něm. Toto rozšíření není nutné odebrat a bude přeskočeno (není migrováno) rozhraním API pro migraci. Pokud však klasický virtuální počítač byl vytvořen s novým portálem Azure, bude pravděpodobně mít verzi BGInfo založenou na JSON v2, kterou lze migrovat do Správce prostředků Azure za předpokladu, že agent pracuje a má odchozí přístup k internetu (a DNS).
  - **Možnost nápravy č. 1**. Pokud víte, že vaše virtuální počítače nebudou mít odchozí přístup k internetu, pracovní službu DNS a fungující agenty Azure na virtuálních počítačích, odinstalujte všechna rozšíření virtuálních zařízení jako součást migrace před přípravou a po migraci přeinstalujte rozšíření virtuálních zařízení.
  - **Možnost nápravy č. 2**. Pokud rozšíření virtuálních počítače jsou příliš velké překážky, další možností je vypnutí nebo navrátit všechny virtuální počítače před migrací. Migrujte přemístěné virtuální počítače a restartujte je na straně Správce prostředků Azure. Výhodou je, že rozšíření virtuálních virtuálních mísin se migrují. Nevýhodou je, že všechny veřejné čelí virtuální IP adresy budou ztraceny (to může být non-startér), a samozřejmě virtuální počítače se vypne způsobuje mnohem větší dopad na pracovní aplikace.

    > [!NOTE]
    > Pokud je zásada Azure Security Center nakonfigurovaná proti spuštěným virtuálním počítačům, které se migrují, musí být zásady zabezpečení zastaveny před odebráním rozšíření, jinak se rozšíření monitorování zabezpečení automaticky přeinstaluje na virtuálním počítači po jeho odebrání.

- **Skupiny dostupnosti** – pro virtuální síť (virtuální síť), která se má migrovat do Správce prostředků Azure, klasické nasazení (tj. cloudová služba) obsažené virtuální počítače musí být všechny v jedné sadě dostupnosti, nebo virtuální počítače musí být všechny v žádné sadě dostupnosti. S více než jednu sadu dostupnosti v cloudové službě není kompatibilní s Azure Resource Manager a zastaví migraci.  Navíc nemůže být některé virtuální chody v sadě dostupnosti a některé virtuální chody není v sadě dostupnosti. Chcete-li tento problém vyřešit, budete muset opravit nebo přeorganizovat cloudovou službu.  Plánujte odpovídajícím způsobem, protože by to mohlo být časově náročné.

- **Nasazení rolí webu a pracovního procesu** – cloudové služby obsahující webové a pracovní role se nemohou migrovat do Správce prostředků Azure. Role webu nebo pracovního procesu musí být nejprve odebrány z virtuální sítě před zahájením migrace.  Typickým řešením je pouze přesunout instance webových nebo pracovních rolí do samostatné virtuální sítě Classic, která je také propojena s okruhem ExpressRoute, nebo migrovat kód do novějších služeb aplikace PaaS (tato diskuse je nad rámec tohoto dokumentu). V případě opětovného nasazení vytvořte novou klasickou virtuální síť, přesuňte nebo znovu nasadíte role webu nebo pracovního procesu do této nové virtuální sítě a pak odstraňte nasazení z přesunuté virtuální sítě. Nejsou vyžadovány žádné změny kódu. Nová funkce [partnerského vztahu virtuální sítě](../../virtual-network/virtual-network-peering-overview.md) se dá použít k vzájemnému vzájemnému sousledování klasické virtuální sítě obsahující role webu nebo pracovního procesu a dalších virtuálních sítí ve stejné oblasti Azure, jako je například migrovaná virtuální síť **(po dokončení migrace virtuální sítě, protože nelze migrovat partnerské virtuální sítě),** a poskytuje tak stejné funkce bez ztráty výkonu a bez sankcí za latenci a šířku pásma. Vzhledem k přidání [partnerského vztahu virtuální sítě](../../virtual-network/virtual-network-peering-overview.md), web/pracovní role nasazení lze nyní snadno zmírnit a není blokovat migraci do Správce prostředků Azure.

- **Kvóty Azure Pro správce prostředků** – oblasti Azure mají samostatné kvóty/limity pro klasické i Azure Resource Manager. I když se ve scénáři migrace nespotřebovává nový hardware *(propojujeme stávající virtuální počítače z klasického na Azure Resource Manager),* kvóty Azure Resource Manageru musí být ještě na místě s dostatečnou kapacitou, než bude možné zahájit migraci. Níže jsou uvedeny hlavní limity, které jsme viděli, které způsobují problémy.  Otevřete lístek podpory kvóty pro zvýšení limitů.

    > [!NOTE]
    > Tato omezení je třeba zvýšit ve stejné oblasti jako aktuální prostředí, které mají být migrovány.
    >

  - Síťová rozhraní
  - Nástroje pro vyrovnávání zatížení
  - Veřejné IP adresy
  - Statické veřejné IP adresy
  - Cores
  - Network Security Groups (Skupiny zabezpečení sítě)
  - Směrovací tabulky

    Aktuální kvóty Azure Resource Manageru můžete zkontrolovat pomocí následujících příkazů s nejnovější verzí azure cli.

    **Výpočetní výkon** *(jádra, sady dostupnosti)*

    ```azurecli
    az vm list-usage -l <azure-region> -o jsonc
    ```

    **Síť** *(virtuální sítě, statické veřejné IP adresy, veřejné IP adresy, skupiny zabezpečení sítě, síťová rozhraní, nástroje pro vyrovnávání zatížení, směrové tabulky)*

    ```azurecli
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Úložiště** *(účet úložiště)*

    ```azurecli
    az storage account show-usage
    ```

- **Omezení omezení rozhraní API Azure Resource Manager –** pokud máte dostatečně velké prostředí (např. > 400 virtuálních počítačů ve Virtuální síti) můžete ve Správci prostředků Azure ve Správci prostředků Azure přístup na výchozí limity omezení rozhraní API pro zápisy (aktuálně **1200 zápisů za hodinu).** Před zahájením migrace byste měli zvýšit lístek podpory, abyste tento limit pro vaše předplatné zvýšili.

- **Zřizování časově neoprávněně vyčovávat stav virtuálního provozu** – pokud má některý virtuální virtuální ms stav **časového omezení zřizování**, je třeba to vyřešit před migrací. Jediný způsob, jak to udělat, je s prostoje zrušením zřízení nebo reprovisioning virtuálního počítače (odstranit, zachovat disk a znovu vytvořit virtuální ho).

- **RoleStateUnknown Stav virtuálního provozu** – Pokud se migrace zastaví z důvodu neznámé chybové zprávy **stavu role,** zkontrolujte virtuální ho pomocí portálu a ujistěte se, že je spuštěn. Tato chyba obvykle zmizí sama o sobě (není nutná žádná náprava) po několika minutách a je často přechodný typ často vidět během **spuštění**virtuálního počítače , **stop**, **restart operace.** **Doporučená praxe:** opakujte migraci znovu po několika minutách.

- **Cluster prostředků infrastruktury neexistuje** – v některých případech některé virtuální aplikace nelze migrovat z různých lichých důvodů. Jeden z těchto známých případů je, pokud byl virtuální počítač nedávno vytvořen (v rámci minulého týdne nebo tak nějak) a stalo se přistát clusteru Azure, který ještě není vybaven pro úlohy Azure Resource Manager.  Zobrazí se chyba, která říká, že **cluster prostředků infrastruktury neexistuje** a virtuální ho virtuálního mísu nelze migrovat. Čekání na několik dní obvykle vyřeší tento konkrétní problém, protože cluster brzy získá povolenou službu Azure Resource Manager. Jedno okamžité řešení je `stop-deallocate` však pro virtuální počítač, pak pokračovat vpřed s migrací a spustit virtuální počítač zálohovat ve Správci prostředků Azure po migraci.

### <a name="pitfalls-to-avoid"></a>Úskalí, aby se zabránilo

- Nejeďte zkratky a vynechat validate/prepare/abort dry run migrace.
- Většina, ne-li všechny, z vašich potenciálních problémů se objeví během ověření / připravit / přerušit kroky.

## <a name="migration"></a>Migrace

### <a name="technical-considerations-and-tradeoffs"></a>Technické úvahy a kompromisy

Nyní jste připraveni, protože jste pracovali přes známé problémy s vaším prostředím.

Pro skutečné migrace, možná budete chtít zvážit:

1. Naplánujte a naplánujte virtuální síť (nejmenší jednotku migrace) s rostoucí prioritou.  Do jednoduché virtuální sítě první, a pokrok s složitější virtuální sítě.
2. Většina zákazníků bude mít neprodukční a produkční prostředí.  Naplánovat výrobu jako poslední.
3. **(NEPOVINNÉ)** Naplánujte prostoje údržby s velkým množstvím vyrovnávací paměti v případě neočekávaných problémů.
4. Komunikujte se svými týmy podpory a zarovnejte je s ně v případě, že vzniknou problémy.

### <a name="patterns-of-success"></a>Vzory úspěchu

Technické pokyny z výše uvedené části laboratorního testu by měly být zváženy a zmírněny před skutečnou migrací.  Při odpovídajícím testování migrace je ve skutečnosti non-event.  V produkčních prostředích může být užitečné mít další podporu, například důvěryhodného partnera společnosti Microsoft nebo služby Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Úskalí, aby se zabránilo

Není plně testování může způsobit problémy a zpoždění migrace.  

## <a name="beyond-migration"></a>Mimo migraci

### <a name="technical-considerations-and-tradeoffs"></a>Technické úvahy a kompromisy

Teď, když jste ve Správci prostředků Azure, maximalizujte platformu.  Přečtěte si [přehled Azure Resource Manager u](../../azure-resource-manager/management/overview.md) dozvědět se o dalších výhodách.

Co je třeba zvážit:

- Sdružování migrace s jinými aktivitami.  Většina zákazníků se rozhodne pro okno údržby aplikací.  Pokud ano, můžete použít tento prostoje povolit další funkce Azure Resource Manager, jako je šifrování a migrace na spravované disky.
- Znovu navštivte technické a obchodní důvody pro Azure Resource Manager; povolte další služby dostupné jenom ve Správci prostředků Azure, které se vztahují na vaše prostředí.
- Modernizujte své prostředí pomocí služeb PaaS.

### <a name="patterns-of-success"></a>Vzory úspěchu

Buďte účelní na jaké služby, které teď chcete povolit ve Správci prostředků Azure.  Mnoho zákazníků považuje níže přesvědčivé pro svá prostředí Azure:

- [Řízení přístupu založené na rolích](../../role-based-access-control/overview.md).
- [Šablony Azure Resource Manageru pro jednodušší a lépe řízené nasazení](../../azure-resource-manager/templates/overview.md).
- [Značky](../../azure-resource-manager/management/tag-resources.md).
- [Řízení aktivity](../../azure-resource-manager/management/view-activity-logs.md)
- [Zásady Azure](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Úskalí, aby se zabránilo

Nezapomeňte, proč jste tuto cestu migrace klasického do Azure Resource Manageru zahájili.  Jaké byly původní obchodní důvody? Dosáhli jste obchodního důvodu?


## <a name="next-steps"></a>Další kroky

* [Přehled migrace prostředků IaaS podporovaných platformou z klasického do Správce prostředků Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrace prostředků IaaS z klasického do Správce prostředků Azure pomocí PowerShellu](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Nástroje komunity pro usnadnění migrace prostředků IaaS z klasických na Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Projděte si nejčastější dotazy týkající se migrace prostředků IaaS z klasického na Azure Resource Manager.](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
