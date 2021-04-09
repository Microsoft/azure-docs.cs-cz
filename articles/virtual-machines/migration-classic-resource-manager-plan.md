---
title: Plánování migrace z modelu Classic na Azure Resource Manager
description: V tomto článku se dozvíte, jak naplánovat migraci prostředků IaaS z modelu Classic na Azure Resource Manager.
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 30a02a79e5e6e063c3d21e6626ed1a75c0f61133
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676105"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru

> [!IMPORTANT]
> V dnešní době se o 90% virtuálních počítačů IaaS používají [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28. února 2020 se klasické virtuální počítače zastaraly a budou plně vyřazeny od 1. března 2023. [Přečtěte si další informace]( https://aka.ms/classicvmretirement) o této zastaralosti a [o tom, jak vás to ovlivní](classic-vm-deprecation.md#how-does-this-affect-me).

I když Azure Resource Manager nabízí spoustu úžasnéch funkcí, je důležité naplánovat cestu k migraci, abyste měli jistotu, že bude docházet k plynulé práci. Doba útraty při plánování zajistí, že při provádění aktivit migrace dojde k problémům.

> [!NOTE]
> Následující pokyny byly silně přispěly na zákazníka poradenského týmu Azure a architekti cloudových řešení pracují se zákazníky při migraci velkých prostředí. Vzhledem k tomu, že tento dokument bude i nadále aktualizován jako nové vzory úspěchu, vraťte se z času na čas, abyste viděli, zda jsou k dispozici nová doporučení.

Existují čtyři obecné fáze cesty migrace:

![Fáze migrace](./media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Plánování

### <a name="technical-considerations-and-tradeoffs"></a>Technické požadavky a kompromisy

V závislosti na velikosti svých technických požadavků, geografických oblastech a provozních postupech můžete zvážit:

1. Proč je Azure Resource Manager žádoucí pro vaši organizaci?  Jaké jsou obchodní důvody pro migraci?
2. Jaké jsou technické důvody pro Azure Resource Manager?  Co (pokud existují) další služby Azure chcete využít?
3. Která aplikace (nebo sady virtuálních počítačů) je součástí migrace?
4. Které scénáře podporuje rozhraní API pro migraci?  Přečtěte si o [nepodporovaných funkcích a konfiguracích](migration-classic-resource-manager-overview.md).
5. Budou vaše provozní týmy teď podporovat aplikace a virtuální počítače v klasickém i Azure Resource Manager?
6. Jak (pokud vůbec) Azure Resource Manager mění procesy nasazení, správy, monitorování a vytváření sestav virtuálních počítačů?  Je potřeba aktualizovat skripty pro nasazení?
7. Jaký je komunikační plán pro upozornění zúčastněných stran (koncoví uživatelé, vlastníci aplikací a vlastníci infrastruktury)?
8. V závislosti na složitosti prostředí by měla existovat období údržby, ve kterém je aplikace nedostupná pro koncové uživatele a vlastníky aplikací?  Pokud ano, jak dlouho?
9. Co je plán školení, který zajistí, že se účastníci budou vědomi a zdatní v Azure Resource Manager?
10. Co je Správa programu nebo plán řízení projektu pro migraci?
11. Jaké jsou časové osy pro Azure Resource Manager migraci a další související technologie silničních map?  Jsou optimálně zarovnané?

### <a name="patterns-of-success"></a>Vzorce úspěchu

Úspěšná zákazníci mají podrobné plány, kde jsou předchozí otázky diskutovány, dokumentovány a upraveny.  Zajistěte, aby plány migrace byly široce sdělovány sponzorům a zúčastněným stranám.  Seznamte se s poznatky o možnostech migrace; čtení v této sadě dokumentů migrace se důrazně doporučuje.

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](migration-classic-resource-manager-cli.md)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md)

### <a name="pitfalls-to-avoid"></a>Nástrah, abyste se vyhnuli

- Plánování nebylo úspěšné.  Technologické kroky této migrace jsou prověřené a výsledek je předvídatelný.
- Předpokládá se, že rozhraní API pro migraci podporované platformou bude pohlížet na všechny scénáře. Přečtěte si [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md) , které vám pomohou pochopit podporované scénáře.
- Nepovedlo se naplánovat potenciální výpadek aplikace pro koncové uživatele.  Naplánujte dostatek vyrovnávací paměti pro odpovídající upozornění koncovým uživatelům potenciálně nedostupného času aplikace.


## <a name="lab-test"></a>Test testovacího prostředí

**Replikace prostředí a testování migrace**
  > [!NOTE]
  > Přesná replikace vašeho stávajícího prostředí se provádí pomocí nástroje vytvořeného komunitou, který podpora Microsoftu oficiálně nepodporuje. Proto je **volitelný** krok, ale je to nejlepší způsob, jak zjistit problémy bez zásahu do produkčního prostředí. Pokud použití nástroje vytvořeného komunitou není možnost, přečtěte si níže uvedené doporučení pro ověření/přípravu/přerušování za běhu.
  >

  Nejlepším způsobem, jak zajistit bezproblémovou migraci, je testování testovacího prostředí pro přesný scénář (výpočetní prostředky, sítě a úložiště). To vám pomůže zajistit:

- Zcela samostatné testovací prostředí nebo existující neprodukční prostředí, které se má testovat. Doporučujeme zcela samostatné testovací prostředí, které je možné opakovaně migrovat a které může být destruktivním upravováno.  Skripty pro shromažďování nebo Hydrate metadat z reálných předplatných jsou uvedeny níže.
- V samostatném předplatném je vhodné vytvořit testovací prostředí. Důvodem je, že testovací prostředí se opakovaně rozděluje a bude mít samostatný izolovaný odběr snížení pravděpodobnosti, že se nějaké reálné omyly odstraní.

  To lze provést pomocí nástroje AsmMetadataParser. [Další informace o tomto nástroji najdete tady.](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Vzorce úspěchu

Následující byly problémy zjištěné v řadě větších migrací. Nejedná se o vyčerpávající seznam a další informace najdete v tématu [nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md) . K těmto technickým problémům může nebo nemusí dojít, ale pokud je před pokusem o migraci provedete, zajistěte, aby se zajistilo plynulejší prostředí.

- **Provedení operace ověření/přípravy/přerušení suchého běhu** – jedná se například o nejdůležitější krok k tomu, abyste zajistili, že bude migrace Classic Azure Resource Manager úspěšná. Rozhraní API pro migraci má tři hlavní kroky: ověření, příprava a potvrzení. Při ověřování se přečte stav klasického prostředí a vrátí se výsledek všech problémů. Vzhledem k tomu, že některé problémy mohou existovat v sadě Azure Resource Manager Stack, ověřování nebude zachytit vše. Dalším krokem v procesu migrace bude Příprava k vystavení těchto problémů. Příprava přesune metadata z klasického na Azure Resource Manager, ale nepotvrdí ani nemění žádné položky na klasické straně. Tento suchý běh zahrnuje přípravu migrace a pak se přerušuje (**nejedná** se o potvrzení) na Příprava migrace. Cílem ověření/přípravy/přerušení suchého běhu je zobrazit všechna metadata v sadě Azure Resource Manager Stack, prověřit je (*programově nebo na portálu*) a ověřit, zda je vše migrováno správně, a pracovat s technickými problémy.  Také vám poskytne představu o době trvání migrace, abyste mohli odpovídajícím způsobem naplánovat výpadky.  Operace ověřit/Příprava/přerušení nezpůsobí žádné výpadky uživatele; Proto je použití aplikace nerušivé.
  - Níže uvedené položky budou muset být vyřešeny před sušením, ale při suchém testu se tyto přípravné kroky také bezpečně vyprázdní, pokud jsou vynechány. Během migrace do podnikové sítě jsme zjistili, že je suchý běh bezpečný a neužitečný způsob, jak zajistit připravenost na migraci.
  - Když je příprava spuštěná, bude pro celou virtuální síť uzamčena Řídicí rovina (operace správy Azure), takže během operace ověřit/připravit nebo přerušit nebude možné provádět žádné změny v metadatech virtuálních počítačů.  V opačném případě bude neovlivněna žádná funkce aplikace (Vzdálená plocha, využití virtuálních počítačů atd.).  Uživatelé virtuálních počítačů nebudou mít jistotu, že je spuštěný suchý běh.

- **Okruhy Express Route a síť VPN**. Brány Express Route s autorizačními odkazy se nedají migrovat bez výpadků. Alternativní řešení najdete v tématu [migrace okruhů ExpressRoute a přidružených virtuálních sítí z modelu nasazení Classic do modelu nasazení Správce prostředků](../expressroute/expressroute-migration-classic-resource-manager.md).

- **Rozšíření virtuálních počítačů** – rozšíření virtuálních počítačů jsou potenciálně jedním z největších překážek pro migraci spuštěných virtuálních počítačů. Náprava rozšíření virtuálních počítačů může trvat až 1-2 dnů, takže si to podle potřeby naplánujte.  K nahlášení stavu virtuálních počítačů na běžícím virtuálním počítači je potřeba pracovní agent Azure. Pokud se stav u spuštěného virtuálního počítače vrátí jako špatný, zastaví se migrace. Aby bylo možné migraci povolit, nemusí být samotný agent v pracovním řádu, pokud ale na virtuálním počítači existují rozšíření, bude potřeba, aby migrace přesunula vpřed jak funkční, tak i odchozí připojení k Internetu (s DNS).
  - Pokud během migrace dojde ke ztrátě připojení k serveru DNS, všechna rozšíření virtuálních počítačů s výjimkou BGInfo v1. \* před přípravou migrace se musí nejdřív odebrat z každého virtuálního počítače a pak znovu přidat zpátky k virtuálnímu počítači po Azure Resource Manager migrace.  **Toto je jenom pro virtuální počítače, na kterých běží.**  Pokud jsou virtuální počítače zastavené, nemusíte odebírat rozšíření virtuálních počítačů. **Poznámka:** Celá řada rozšíření, jako je Azure Diagnostics a monitoring Security Center, se po migraci znovu nainstaluje, takže jejich odebrání nepředstavuje problém.
  - Kromě toho zajistěte, aby skupiny zabezpečení sítě neomezily odchozí přístup k Internetu. K tomu může dojít v některých konfiguracích skupin zabezpečení sítě. Pro migraci rozšíření virtuálních počítačů do Azure Resource Manager je potřeba odchozí přístup k Internetu (a DNS).
  - Existují dvě verze rozšíření BGInfo: V1 a v2.  Pokud byl virtuální počítač vytvořen pomocí Azure Portal nebo PowerShellu, virtuální počítač bude pravděpodobně mít rozšíření v1. Toto rozšíření není nutné odebrat a rozhraní API pro migraci se přeskočí (nemigruje). Pokud se ale klasický virtuální počítač vytvořil pomocí nového Azure Portal, bude nejspíš mít verzi v2 BGInfo založenou na JSON, která se dá migrovat na Azure Resource Manager za předpokladu, že agent funguje a má odchozí přístup k Internetu (a DNS).
  - **Možnost opravy 1**. Pokud víte, že vaše virtuální počítače nebudou mít odchozí přístup k Internetu, fungující službu DNS a pracují agenty Azure na virtuálních počítačích, před přípravou Odinstalujte všechna rozšíření virtuálních počítačů a potom po migraci přeinstalujte rozšíření virtuálních počítačů.
  - **Možnost opravy 2**. Pokud jsou rozšíření virtuálních počítačů příliš velká, je další možností vypnutí nebo zrušení přidělení všech virtuálních počítačů před migrací. Migrujte navrácené virtuální počítače a pak je restartujte na straně Azure Resource Manager. Výhodou je, že rozšíření virtuálního počítače se migrují. Nevýhodou je to, že se ztratí všechny veřejné virtuální IP adresy (může to být nepočáteční), a zjevně by se virtuální počítače vypnuly, což by způsobilo mnohem větší dopad na funkční aplikace.

    > [!NOTE]
    > Pokud jsou zásady Azure Security Center nakonfigurované proti migrování spuštěných virtuálních počítačů, je potřeba před odebráním rozšíření zastavit zásady zabezpečení. v opačném případě se rozšíření monitorování zabezpečení na virtuálním počítači po jeho odebrání znovu nainstaluje automaticky.

- **Skupiny dostupnosti** – pro virtuální síť (vNet), která se má migrovat na Azure Resource Manager, musí mít nasazení Classic (tj. cloudová služba) všechny virtuální počítače v jedné skupině dostupnosti, jinak musí být virtuální počítače v žádné skupině dostupnosti. Používání více než jedné skupiny dostupnosti v cloudové službě není kompatibilní s Azure Resource Manager a zastaví migraci.  Kromě toho nemůžou být některé virtuální počítače ve skupině dostupnosti a některé virtuální počítače nejsou v sadě dostupnosti. Pokud to chcete vyřešit, budete muset svou cloudovou službu opravit nebo ji můžete znovu vymezit.  Naplánujte odpovídajícím způsobem, protože to může být časově náročné.

- **Nasazení rolí webu nebo pracovní role** – Cloud Services obsahující webové a pracovní role nelze migrovat na Azure Resource Manager. Aby bylo možné spustit migraci, musí být nejprve z virtuální sítě odebrány role Web/Worker.  Typickým řešením je jednoduše přesunout instance rolí web/Worker do samostatné klasické virtuální sítě, která je také propojena s okruhem ExpressRoute, nebo migrovat kód do novějšího PaaS App Services (Tato diskuze je nad rámec tohoto dokumentu). V bývalém případě opětovného nasazení vytvořte novou klasickou virtuální síť, přesuňte nebo znovu nasaďte webové a pracovní role do této nové virtuální sítě a pak odstraňte nasazení z virtuální sítě, která se přesouvá. Nevyžadují se žádné změny kódu. Novou funkci [partnerského vztahu Virtual Network](../virtual-network/virtual-network-peering-overview.md) lze použít pro partnerský vztah mezi klasickými virtuálními sítěmi, které obsahují webové a pracovní role a jiné virtuální sítě ve stejné oblasti Azure, jako je například migrace virtuální sítě (**po dokončení migrace virtuální sítě, jako je třeba migrace** virtuální sítě), a proto poskytují stejné možnosti bez jakýchkoli ztrát výkonu a jejich zpoždění ani pokuty šířky pásma. Vzhledem k tomu, že se přidávají [Virtual Network partnerských vztahů](../virtual-network/virtual-network-peering-overview.md), je teď možné snadno zmírnit omezení nasazení webových nebo pracovních rolí a neblokovat migraci do Azure Resource Manager.

- **Kvóty Azure Resource Manager** – oblasti Azure mají samostatné kvóty a omezení pro klasické i Azure Resource Manager. I když ve scénáři migrace není spotřebován nový hardware *(odstraňujeme stávající virtuální počítače z klasického na Azure Resource Manager)*, Azure Resource Manager kvóty stále musí být na dosah dostatečně vysoké kapacity, aby bylo možné spustit migraci. Níže jsou uvedené hlavní limity, které jsme zjistili, že způsobují problémy.  Otevřete lístek podpory kvóty pro zvýšení limitu.

    > [!NOTE]
    > Tato omezení se musí vyhodnotit ve stejné oblasti jako vaše aktuální prostředí k migraci.
    >

  - Síťová rozhraní
  - Nástroje pro vyrovnávání zatížení
  - Veřejné IP adresy
  - Statické veřejné IP adresy
  - Cores
  - Network Security Groups (Skupiny zabezpečení sítě)
  - Směrovací tabulky

    Aktuální kvóty Azure Resource Manager můžete kontrolovat pomocí následujících příkazů s nejnovější verzí Azure CLI.

    **Výpočetní** prostředky *(jádra, skupiny dostupnosti)*

    ```azurecli
    az vm list-usage -l <azure-region> -o jsonc
    ```

    **Síť** *(virtuální sítě, statické veřejné IP adresy, veřejné IP adresy, skupiny zabezpečení sítě, síťová rozhraní, nástroje pro vyrovnávání zatížení, směrovací tabulky)*

    ```azurecli
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Storage** *(účet úložiště)*

    ```azurecli
    az storage account show-usage
    ```

- **Omezení omezování Azure Resource Manager API** – Pokud máte velké množství prostředí (např. > 400 virtuálních počítačů ve virtuální síti) můžete v Azure Resource Manager dosáhnout výchozích limitů omezování pro zápisy (aktuálně **1200 zápisů za hodinu**). Před zahájením migrace byste měli vyvolat lístek podpory pro zvýšení tohoto limitu vašeho předplatného.

- Při zřizování došlo k **vypršení časového limitu virtuálního počítače** – Pokud má některý virtuální počítač stav **zřizování vypršel**, je nutné tuto možnost vyřešit před migrací. Jediným způsobem, jak to provést, je zrušením zřízení/opětovného zřízení virtuálního počítače (jeho odstraněním, ponechání disku a vytvoření virtuálního počítače) bez výpadků.

- **Stav virtuálního počítače neznámý** – Pokud se migrace zastaví kvůli neznámé chybové zprávě **stavu role** , zkontrolujte virtuální počítač pomocí portálu a ujistěte se, že je spuštěný. Tato chyba obvykle vznikne vlastní (bez potřeby nápravy) po několika minutách a často se jedná o přechodný typ, který se často zobrazuje během operace **spuštění**, **zastavení** a **restartování** virtuálního počítače. **Doporučený postup:** zkuste migraci opakovat znovu za několik minut.

- **Cluster prostředků infrastruktury neexistuje** – v některých případech je nemůžete migrovat některé virtuální počítače z různých lichých důvodů. Jedním z těchto známých případů je, že se virtuální počítač v poslední době vytvořil (během posledního týdne) a že se nacházel cluster Azure, který ještě není vybavený pro Azure Resource Manager úlohy.  Zobrazí se chyba, která říká, že **cluster Fabric neexistuje** a virtuální počítač nejde migrovat. Při čekání na několik dní se obvykle vyřeší tento konkrétní problém, protože cluster brzy získá Azure Resource Manager povolenou. Jediným alternativním řešením je však, že se k virtuálnímu počítači bude dál přenášet `stop-deallocate` migrace a po migraci spustit virtuální počítač znovu v Azure Resource Manager.

### <a name="pitfalls-to-avoid"></a>Nástrah, abyste se vyhnuli

- Nepoužívejte zástupce a vynechejte operace ověřování/Příprava/přerušení za běhu suché.
- Většina, pokud ne všechny, z vašich potenciálních problémů bude při provádění kroků ověřit/Příprava/přerušení na ploše.

## <a name="migration"></a>Migrace

### <a name="technical-considerations-and-tradeoffs"></a>Technické požadavky a kompromisy

Nyní jste připraveni, protože jste pracovali se známými problémy ve vašem prostředí.

Pro skutečné migrace můžete zvážit:

1. Naplánujte a naplánujte virtuální síť (nejmenší Jednotková migrace) s rostoucí prioritou.  Udělejte nejdřív jednoduché virtuální sítě a probíhají se složitějšími virtuálními sítěmi.
2. Většina zákazníků bude mít neprodukční a produkční prostředí.  Naplánujte si produkci jako poslední.
3. **(Volitelné)** Naplánujte prostoje údržby s využitím dostatečné vyrovnávací paměti v případě neočekávaných problémů.
4. V případě problémů komunikujte s týmy podpory a zarovnejte je s nimi.

### <a name="patterns-of-success"></a>Vzorce úspěchu

Technické pokyny z výše uvedeného testovacího prostředí byste měli zvážit a zmírnit před skutečnou migrací.  Při dostatečném testování je migrace vlastně neevent.  V produkčních prostředích může být užitečné mít další podporu, jako je důvěryhodný partner Microsoftu nebo Microsoft Premier Services.

### <a name="pitfalls-to-avoid"></a>Nástrah, abyste se vyhnuli

Neúplné testování může způsobit problémy a zpoždění migrace.  

## <a name="beyond-migration"></a>Rámec migrace

### <a name="technical-considerations-and-tradeoffs"></a>Technické požadavky a kompromisy

Teď, když jste v Azure Resource Manager, maximalizujte platformu.  Přečtěte si [přehled Azure Resource Manager](../azure-resource-manager/management/overview.md) a zjistěte další výhody.

Co je potřeba vzít v úvahu:

- Nasdružování migrace s ostatními aktivitami.  Většina zákazníků si vyžádá časový interval pro správu aplikací.  Pokud ano, můžete toto výpadky použít k povolení jiných Azure Resource Manager možností, jako je šifrování a migrace Managed Disks.
- Přečtěte si technické a obchodní důvody pro Azure Resource Manager; Povolte další služby dostupné jenom pro Azure Resource Manager, které se vztahují k vašemu prostředí.
- Modernizovat své prostředí pomocí služeb PaaS.

### <a name="patterns-of-success"></a>Vzorce úspěchu

Záměrné, jaké služby teď chcete v Azure Resource Manager povolit.  Spousta zákazníků pro svá prostředí Azure vyhledá méně závažná řešení:

- [Řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md).
- [Šablony Azure Resource Manager pro snazší a lepší řízené nasazení](../azure-resource-manager/templates/overview.md).
- [Značky](../azure-resource-manager/management/tag-resources.md).
- [Řízení aktivit](../azure-resource-manager/management/view-activity-logs.md)
- [Zásady Azure](../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Nástrah, abyste se vyhnuli

Pamatujte na to, proč jste tento klasický začátek pro Azure Resource Manager cestu migrace.  Jaké byly původní obchodní důvody? Dosáhli jste obchodní důvod?


## <a name="next-steps"></a>Další kroky

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md)
