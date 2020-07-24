---
title: Skupiny umístění blízkosti Azure pro aplikace SAP | Microsoft Docs
description: Popisuje scénáře nasazení SAP se skupinami umístění v blízkosti Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7aa71062c86d57cabe8579e13011956137804f74
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079787"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Skupiny umístění v blízkosti Azure pro optimální latenci sítě s aplikacemi SAP
Aplikace SAP založené na architektuře SAP NetWeaver nebo SAP S/4HANA jsou citlivé na latenci sítě mezi aplikační vrstvou SAP a databázovou vrstvou SAP. Tato citlivost je výsledkem většiny obchodních logiky spuštěných v aplikační vrstvě. Vzhledem k tomu, že aplikační vrstva SAP spouští obchodní logiku, vydává dotazy do databázové vrstvy s vysokou frekvencí v poměru tisíc nebo desítky tisíců za sekundu. Ve většině případů je povaha těchto dotazů jednoduchá. Je často možné je spouštět na úrovni databáze za 500 mikrosekund nebo méně.

Čas strávený v síti, který odešle takový dotaz z aplikační vrstvy do databázové vrstvy a získá zpět sadu výsledků, má zásadní dopad na dobu potřebnou k provozování obchodních procesů. Tato citlivost na latenci sítě je důvod, proč potřebujete dosáhnout optimální latence sítě v projektech nasazení SAP. Pokyny k klasifikaci latence sítě najdete v tématu [SAP – poznámka #1100926 – Nejčastější dotazy: výkon sítě](https://launchpad.support.sap.com/#/notes/1100926/E) .

V mnoha oblastech Azure se dosáhlo množství datových center. Tento nárůst se aktivoval také zavedením Zóny dostupnosti. Zákazníci, zvláště pro systémy s vysokým využitím SAP, navíc používají více speciálních SKU virtuálních počítačů ve skupině M-Series nebo ve velkých instancích HANA. Tyto typy virtuálních počítačů Azure nejsou dostupné ve všech datacentrech v konkrétní oblasti Azure. Z důvodu těchto dvou Tendencies zákazníci narazili na latenci sítě, která není v optimálním rozsahu. V některých případech tato latence vede k zajištění optimálního výkonu svých systémů SAP.

Aby se tyto problémy předešly, Azure nabízí [skupiny umístění pro Proximity](../../linux/co-location.md). Tato nová funkce se už použila k nasazení různých systémů SAP. Omezení pro skupiny umístění blízkosti najdete v článku na začátku tohoto odstavce. Tento článek se zabývá scénáři SAP, ve kterých se můžou používat skupiny umístění v blízkosti Azure.

## <a name="what-are-proximity-placement-groups"></a>Co jsou skupiny umístění pro Proximity? 
Skupina umístění blízkosti Azure je logická konstrukce. Je-li definována jedna, je svázána s oblastí Azure a skupinou prostředků Azure. Po nasazení virtuálních počítačů se na skupinu umístění blízkosti odkazuje:

- První virtuální počítač Azure nasazený v datacentru. První virtuální počítač si můžete představit jako "virtuální počítač oboru", který je nasazený v datacentru na základě alokačních algoritmů Azure, které se nakonec kombinují s definicemi uživatelů pro konkrétní zónu dostupnosti.
- Všechny následné nasazené virtuální počítače, které odkazují na skupinu umístění blízkosti, se umístí na všechny následně nasazené virtuální počítače Azure ve stejném datovém centru jako první virtuální počítač.

> [!NOTE]
> Pokud není nasazený žádný hostitelský hardware, který by mohl spustit konkrétní typ virtuálního počítače v datacentru, kde byl umístěn první virtuální počítač, nasazení požadovaného typu virtuálního počítače nebude úspěšné. Zobrazí se zpráva o selhání.

Jedna [Skupina prostředků Azure](../../../azure-resource-manager/management/manage-resources-portal.md) může mít přiřazených několik skupin umístění blízkosti. Ale skupina umístění blízkosti se dá přiřadit jenom k jedné skupině prostředků Azure.

Pokud používáte skupiny umístění pro Proximity, pamatujte na tyto skutečnosti:

- Když se zaměříte na optimální výkon pro systém SAP a omezíte si ho na jedno datacentrum Azure pro systém pomocí skupin umístění blízkosti, možná nebudete moct kombinovat všechny typy rodin virtuálních počítačů v rámci skupiny umístění. Tato omezení se projeví proto, že hostitelský hardware potřebný ke spuštění určitého typu virtuálního počítače nemusí být v datovém centru, ve kterém je nasazený virtuální počítač s vymezenou skupinou umístění.
- Během životního cyklu takového systému SAP může být vynuceno přesunutí systému do jiného datového centra. Tato změna se může vyžadovat, pokud se rozhodnete, že se má vrstva správy na více systémů HANA, například přesunout ze čtyř uzlů na 16 uzlů, a není dostatečná kapacita pro získání dalších 12 virtuálních počítačů typu, který jste použili v datacentru.
- Z důvodu vyřazení hardwaru může společnost Microsoft vytvořit kapacitu pro typ virtuálního počítače, který jste použili v jiném datovém centru, nikoli tu, kterou jste původně použili. V takovém případě může být nutné přesunout všechny virtuální počítače skupiny umístění blízkosti do jiného datového centra.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Skupiny umístění pro Proximity se systémy SAP, které používají jenom virtuální počítače Azure
Většina nasazení systémů SAP NetWeaver a S/4HANA v Azure nepoužívá [velké instance Hana](./hana-overview-architecture.md). Pro nasazení, která nepoužívají velké instance HANA, je důležité zajistit optimální výkon mezi aplikační vrstvou SAP a vrstvou DBMS. Provedete to tak, že pro systém definujete skupinu umístění blízkosti Azure.

Ve většině zákaznických nasazení vytvářejí zákazníci jednu [skupinu prostředků Azure](../../../azure-resource-manager/management/manage-resources-portal.md) pro systémy SAP. V takovém případě existuje vztah 1:1 mezi například skupinou prostředků systému produkčního prostředí ERP a skupinou umístění blízkosti. V jiných případech zákazníci organizují své skupiny prostředků vodorovně a shromažďují všechny produkční systémy v jedné skupině prostředků. V tomto případě budete mít mezi skupinou prostředků vztah 1: n pro produkční systémy SAP a skupiny umístění pro Proximity pro produkční prostředí SAP ERP, SAP BW a tak dále.

Vyhněte se sdružování produkčních nebo neprodukčních systémů SAP do jedné skupiny umístění pro Proximity. Když malý počet systémů SAP nebo systém SAP a některé okolní aplikace potřebují síťovou komunikaci s nízkou latencí, můžete zvážit přesunutí těchto systémů do jedné skupiny umístění pro Proximity. Měli byste se vyhnout sadě systémů, protože čím více systémů rozcházíte do skupiny umístění blízkosti, tím větší je riziko:

- Vyžadujete typ virtuálního počítače, který se nedá spustit v konkrétním datovém centru, do kterého má skupina umístění blízkosti rozsah.
- Je možné, že prostředky nestandardních virtuálních počítačů, jako jsou virtuální počítače řady M-Series, by mohly být nedodrženy, když budete potřebovat více, protože do skupiny umístění blízkosti přidáváte software v průběhu času.

Tady je popis ideální konfigurace, jak je popsáno v tématu:

![Skupiny umístění pro Proximity jenom s virtuálními počítači Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

V takovém případě jsou jednotlivé systémy SAP seskupené do jedné skupiny prostředků, přičemž každý z nich má každou skupinu umístění v blízkosti. Bez ohledu na to, jestli používáte konfigurace škálování na více systémů nebo DBMS, nemusíte mít žádnou závislost.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Skupiny umístění blízkosti a velké instance HANA
Pokud jsou některé systémy SAP závislé na [velkých instancích](./hana-overview-architecture.md) vrstvy aplikace v Hana, můžete mít významná vylepšení latence sítě mezi velkými instancemi a virtuálními počítači Azure, když používáte jednotky velkých instancí Hana, které jsou nasazeny v [řádcích revize 4](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Jedním z vylepšení je, že velké instance v HANA, jak se nasazují, se nasazují se skupinou umístění blízkosti. Tuto skupinu umístění blízkosti můžete použít k nasazení virtuálních počítačů aplikační vrstvy. V důsledku toho budou tyto virtuální počítače nasazeny ve stejném datovém centru, které je hostitelem jednotky velkých instancí HANA.

Pokud chcete zjistit, jestli je jednotka velkých instancí HANA nasazená v rámci kontrolního razítka nebo řádku revize 4, přečtěte si článek [Správa velkých instancí Azure Hana prostřednictvím Azure Portal](./hana-li-portal.md#look-at-attributes-of-single-hli-unit). V přehledu atributů jednotky velkých instancí HANA můžete také určit název skupiny umístění blízkosti, protože byla vytvořena při nasazení jednotky velkých instancí HANA. Název, který se zobrazí v přehledu atributy, je název skupiny umístění blízkosti, do které byste měli nasadit virtuální počítače vrstvy aplikace.

V porovnání se systémy SAP, které používají jenom virtuální počítače Azure, je při použití velkých instancí HANA méně flexibilita při rozhodování o tom, kolik [skupin prostředků Azure](../../../azure-resource-manager/management/manage-resources-portal.md) se má použít. Všechny jednotky velkých instancí HANA [tenanta rozsáhlých instancí Hana](./hana-know-terms.md) se seskupují do jedné skupiny prostředků, jak je popsáno v [tomto článku](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal). Pokud nebudete nasazovat do různých klientů, aby se odlišili například produkční a neprodukční systémy nebo jiné systémy, všechny jednotky velkých instancí HANA budou nasazeny v klientovi s velkým počtem instancí HANA. Tento tenant má relaci 1:1 se skupinou prostředků. Pro každou jednotlivou jednotku však bude definována samostatná skupina umístění blízkosti.

V důsledku toho budou vztahy mezi skupinami prostředků Azure a skupinami umístění blízkosti pro jednoho tenanta, jak je znázorněno zde:

![Skupiny umístění blízkosti a velké instance HANA](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Příklad nasazení se skupinami umístění pro Proximity
Níže jsou uvedené některé příkazy PowerShellu, které můžete použít k nasazení virtuálních počítačů se skupinami umístění s použitím blízkosti Azure.

Prvním krokem po přihlášení k [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)je ověření, jestli se nacházíte v předplatném Azure, které chcete použít pro nasazení:

<pre><code>
Get-AzureRmContext
</code></pre>

Pokud potřebujete přejít na jiné předplatné, můžete to udělat spuštěním tohoto příkazu:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Spuštěním tohoto příkazu vytvořte novou skupinu prostředků Azure:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Spuštěním tohoto příkazu vytvořte novou skupinu umístění blízkosti:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Nasaďte první virtuální počítač do skupiny umístění pro Proximity pomocí příkazu, jako je tento:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Předchozí příkaz nasadí virtuální počítač se systémem Windows. Po úspěšném nasazení tohoto virtuálního počítače se obor Datacenter skupiny umístění blízkosti definuje v oblasti Azure. Všechna následující nasazení virtuálních počítačů, která odkazují na skupinu umístění Proximity, jak je znázorněno v předchozím příkazu, se nasadí ve stejném datovém centru Azure, pokud je možné hostovat typ virtuálního počítače na hardwaru umístěném v tomto datovém centru a k dispozici je kapacita pro tento typ virtuálního počítače.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Kombinování skupin dostupnosti a Zóny dostupnosti se skupinami umístění pro Proximity
Jednou z výhod používání Zóny dostupnosti pro nasazení systému SAP je to, že vrstvu aplikace SAP nemůžete nasadit pomocí skupin dostupnosti v rámci konkrétní zóny. Chcete, aby byla vrstva aplikace SAP nasazená ve stejných zónách jako vrstva DBMS. Odkazování na zónu dostupnosti a skupinu dostupnosti, když nasazení jednoho virtuálního počítače není podporované. Dříve jste museli nasadit aplikační vrstvu odkazem na zónu. Ztratili jste schopnost zajistit, aby byly virtuální počítače vrstvy aplikace rozdělené mezi různé domény aktualizace a selhání.

Když použijete skupiny umístění blízkosti, můžete toto omezení obejít. Toto je pořadí nasazení:

- Vytvořte skupinu umístění blízkosti.
- Pomocí odkazu na zónu dostupnosti Nasaďte svůj virtuální počítač pro ukotvení, obvykle server DBMS.
- Vytvořte skupinu dostupnosti, která odkazuje na skupinu v blízkosti Azure. (Viz příkaz později v tomto článku.)
- Nasaďte virtuální počítače aplikační vrstvy tak, že odkazujete na skupinu dostupnosti a na skupinu umístění blízkosti.

Místo nasazování prvního virtuálního počítače, jak je znázorněno v předchozí části, se při nasazení virtuálního počítače odkazuje na zónu dostupnosti a na skupinu umístění blízkosti:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Úspěšné nasazení tohoto virtuálního počítače by způsobilo hostování instance databáze systému SAP v jedné zóně dostupnosti. Rozsah skupiny umístění blízkosti je pevně daný pro jedno z datových center, která představuje zónu dostupnosti, kterou jste definovali.

Předpokládejme, že nasadíte virtuální počítače centrální služby stejným způsobem jako virtuální počítače s DBMS a odkazují na stejnou zónu nebo zóny a stejné skupiny umístění pro Proximity. V dalším kroku potřebujete vytvořit skupiny dostupnosti, které chcete použít pro aplikační vrstvu systému SAP.

Musíte definovat a vytvořit skupinu umístění blízkosti. Příkaz pro vytvoření sady dostupnosti vyžaduje další odkaz na ID skupiny umístění blízkosti (ne název). ID skupiny umístění blízkosti můžete získat pomocí tohoto příkazu:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Když vytvoříte skupinu dostupnosti, musíte zvážit další parametry při použití spravovaných disků (výchozí, pokud není uvedeno jinak) a skupin umístění blízkosti:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

V ideálním případě byste měli použít tři domény selhání. Ale počet podporovaných domén selhání se může lišit od oblasti do oblasti. V takovém případě je maximální počet domén selhání možných pro konkrétní oblasti dva. Pokud chcete nasadit virtuální počítače vrstvy aplikace, musíte přidat odkaz na název skupiny dostupnosti a název skupiny umístění blízkosti, jak je znázorněno zde:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Výsledek tohoto nasazení:
- Vrstva DBMS a centrální služby pro váš systém SAP nacházející se v konkrétní zóně dostupnosti nebo Zóny dostupnosti.
- Vrstva aplikace SAP, která se nachází prostřednictvím skupin dostupnosti ve stejných datacentrech Azure jako virtuální počítač DBMS nebo virtuální počítače.

> [!NOTE]
> Vzhledem k tomu, že nasadíte jeden virtuální počítač s DBMS do jedné zóny a druhý virtuální počítač DBMS do jiné zóny, abyste vytvořili konfiguraci s vysokou dostupností, budete pro každou zónu potřebovat jinou skupinu umístění blízkosti. Totéž platí pro všechny skupiny dostupnosti, které používáte.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Přesunout existující systém do skupin umístění pro Proximity
Pokud již máte nasazené systémy SAP, možná budete chtít optimalizovat latenci sítě některých důležitých systémů a umístit vrstvu aplikace a vrstvu DBMS do stejného datacentra. Pokud chcete přesunout virtuální počítače s kompletní sadou dostupnosti Azure do existující skupiny umístění s blízkými místy, která už je vymezená, musíte vypnout všechny virtuální počítače skupiny dostupnosti a přiřadit skupinu dostupnosti k existující skupině umístění blízkosti prostřednictvím Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud chcete přesunout virtuální počítač, který není součástí skupiny dostupnosti, do existující skupiny umístění pro Proximity, stačí virtuální počítač vypnout a přiřadit k existující skupině umístění blízkosti. 


## <a name="next-steps"></a>Další kroky
Projděte si dokumentaci:

- [Úlohy SAP v Azure: kontrolní seznam pro plánování a nasazení](./sap-deployment-checklist.md)
- [Preview: nasazení virtuálních počítačů do skupin umístění blízkosti pomocí Azure CLI](../../linux/proximity-placement-groups.md)
- [Verze Preview: nasazení virtuálních počítačů do skupin umístění s blízkostí pomocí PowerShellu](../../windows/proximity-placement-groups.md)
- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_general.md)
