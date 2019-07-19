---
title: Skupiny umístění v blízkosti Azure pro optimální latenci sítě s aplikacemi SAP | Microsoft Docs
description: Popisuje scénáře nasazení SAP se skupinami umístění v blízkosti Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f97cce2d1a8b2de5634215629ddb997dc8f7196a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235194"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Skupiny umístění bezkontaktní komunikace Azure pro optimální latenci sítě s aplikacemi SAP
Aplikace SAP založené na architektuře SAP NetWeaver nebo SAP S/4HANA jsou citlivé na latenci sítě mezi aplikační vrstvou SAP a databázovou vrstvou SAP. Důvodem této citlivosti těchto architektur je, že většina obchodní logiky se spouští v aplikační vrstvě. V důsledku provádění obchodní logiky, které bude vrstva aplikace SAP vystavovat dotazy do databázové vrstvy s vysokou četností tisíců a desítkami tisíců za sekundu. Ve většině případů je povaha těchto dotazů jednoduchá. A často lze provádět na úrovni databáze za méně než 500 mikrosekund nebo dokonce méně. Čas strávený v síti, který odešle takový dotaz z aplikační vrstvy do databázové vrstvy a získá výslednou sadu vrácenou z databázové vrstvy, má významný dopad na čas potřebný k provedení obchodních procesů. Tato citlivost na latenci sítě je důvod potřebný k dosažení optimální latence sítě v projektech nasazení SAP. V [tématu SAP Note #1100926 – Nejčastější dotazy: Výkon](https://launchpad.support.sap.com/#/notes/1100926/E)sítě, SAP publikoval některé pokyny, jak klasifikovat latenci sítě.

Na jedné straně, v mnoha oblastech Azure, je počet vyrostlých datových center, který se také aktivuje zavedením Zóny dostupnosti. Na druhé straně zákazníci, zejména pro špičkové systémy SAP, používali další speciální SKU virtuálních počítačů z řady M-Series nebo velkých instancí HANA. Typy virtuálních počítačů Azure, které nejsou k dispozici ve všech datových centrech v konkrétní oblasti Azure. V důsledku těchto dvou Tendencies zákazníci narazili na případy, kdy latence sítě nebyla v optimálním rozsahu a v některých případech způsobila optimální výkon jejich systémů SAP.

Aby se takový problém předešl, Azure nabízí konstrukci, která se nazývá [Skupina umístění blízkosti Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Tato nová funkce se už používá k nasazení různých různých systémů SAP. Podívejte se na článek s odkazem na omezení skupin umístění blízkosti. Tento článek se zabývá různými scénáři SAP, kde se dají použít skupiny umístění pro Proximity v Azure, nebo by se měly používat.

## <a name="what-are-proximity-placement-groups"></a>Co jsou skupiny umístění pro Proximity 
Skupina umístění blízkosti Azure je logická konstrukce, která ve fázi definice je svázána s oblastí Azure a skupinou prostředků Azure. Během nasazování virtuálních počítačů se na skupinu umístění blízkosti odkazuje:

- První nasazený virtuální počítač Azure, který se má vyrovnávat v datacentru První virtuální počítač se může zobrazit jako ukotvený virtuální počítač, který se nasadí v datovém centru založeném na algoritmech přidělení Azure, a to i v kombinaci s definicemi uživatelů pro konkrétní zónu dostupnosti Azure.
- Všemi následnými virtuálními počítači nasazenými odkazem na skupinu umístění blízkosti se umístí všechny následné nasazené virtuální počítače Azure ve stejném datovém centru jako první virtuální počítač.

> [!NOTE]
> Pokud není nasazený žádný hostitelský hardware, který by mohl spustit konkrétní typ virtuálního počítače ve stejném datovém centru jako první virtuální počítač, nasazení typu vyžádaného virtuálního počítače nebude úspěšné a skončí s chybovou zprávou. Můžou to být případy, kdy by se více než běžné virtuální počítače s typy virtuálních počítačů GPU nebo HPC měly na střed, např. virtuální počítač řady M-Series nasazený jako první typ virtuálního počítače.

Jedna [Skupina prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) může mít přiřazených několik skupin umístění blízkosti. Jedna skupina umístění pro Proximity se ale dá přiřadit jenom k jedné skupině prostředků Azure.

Při používání skupin umístění blízkosti byste měli vědět:

- Když zadáte optimální výkon pro váš systém SAP a omezíte si pro tento systém v jednom datacentru Azure pomocí skupin umístění blízkosti, možná nebudete moct v takové skupině umístění blízkosti kombinovat všechny typy rodin virtuálních počítačů. Důvodem je, že určitý hostitelský hardware, který je potřeba ke spuštění určitého typu virtuálního počítače, se nemusí nacházet v datacentru, protože se nasadil virtuální počítač pro ukotvení skupiny umístění.
- V životním cyklu takového systému SAP byste mohli obnutit přesunutí systému do jiného datového centra. Takové přesunutí může být vynucené v případech, kdy jste se rozhodli, že byste měli mít vrstvu systému DBMS s možností horizontálního navýšení kapacity v HANA, například přesun ze čtyř uzlů na 16 uzlů. K získání dalších 12 virtuálních počítačů typu, který jste už použili ve stejném datovém centru, už není dostatečná kapacita.
- Z důvodu vyřazení hardwaru může společnost Microsoft vytvořit kapacitu pro typy virtuálních počítačů, které jste použili v jiném datovém centru místo stejného datacentra. Takový výskyt může znamenat, že budete chtít přesunout všechny virtuální počítače skupiny umístění blízkosti do jiného datového centra.


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Skupiny umístění v blízkosti Azure se systémy SAP, které používají výhradně virtuální počítače Azure
V případě nasazení systémů SAP NetWeaver a S/4HANA v systému Azure se většina [velkých instancí Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)nepoužívá. Pro nasazení takových systémů je důležité zajistit optimální výkon mezi aplikační vrstvou SAP a vrstvou DBMS. Aby to bylo možné, měli byste pro tento systém definovat skupinu umístění blízkosti Azure. Ve většině zákaznických nasazení se zákazníci rozhodli vytvořit jednu [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) pro systémy SAP. V takovém případě by existoval vztah 1:1 mezi například skupinami prostředků systému produkčního prostředí ERP a skupinou umístění blízkosti. V některých dalších případech nasazení zákazníci uspořádali své skupiny prostředků vodorovně a shromáždili všechny produkční systémy v jedné skupině prostředků. V takovém případě byste měli mít mezi skupinou prostředků vztah 1: n pro produkční systémy SAP a skupiny umístění pro Proximity v produkčním prostředí pro SAP ERP, SAP BW atd. Sdružování několika nebo dokonce všech produkčních nebo neprodukčních systémů v jedné skupině umístění pro Proximity by mělo být zabráněno. V případech, kdy je potřeba, aby byl malý počet systémů SAP nebo systému SAP a některých okolních aplikací pro síťovou komunikaci s nízkou latencí, můžete zvážit přesunutí těchto systémů do jedné skupiny umístění Proximity. Důvodem pro doporučení je, že čím více systémů rozcházíte do skupiny umístění blízkosti, tím větší je riziko:

- Vyžadujete typ virtuálního počítače, který se nedá spustit v konkrétním datacentru. skupina umístění blízkosti byla ukotvena v.
- Tyto prostředky některých nestandardních virtuálních počítačů, jako je řady M-Series, by se ještě nedaly dodržet, protože při přidávání dalšího softwaru do existující skupiny umístění blízkosti v průběhu času se požádáte o další software.

Ideální použití, jak je popsáno, by vypadalo takto:

![Skupiny blízkých míst pro všechny virtuální počítače Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

V takovém případě jsou jednotlivé systémy SAP seskupené do jedné skupiny prostředků, každé s jednou skupinou umístění pro Proximity. Neexistuje žádná závislost na tom, jestli používáte konfigurace škálování na více systémů nebo DBMS.


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Skupiny umístění v blízkosti Azure a velké instance HANA
V případech, kdy některé systémy SAP spoléhají na [velké instance Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) jako aplikační vrstvu, je možné dosáhnout vážných vylepšení latence sítě mezi jednotkou velkých instancí Hana a virtuálními počítači Azure, a to při použití velkých jednotek instance Hana, které byly nasazeno v [řádcích nebo razítkech revize 4](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Jednou z vylepšení je, že jednotky pro velké instance HANA, jak byly nasazeny, získají již nasazenou skupinu umístění blízkosti. Tuto skupinu umístění blízkosti můžete použít k nasazení virtuálních počítačů aplikační vrstvy. Výsledkem je, že tyto virtuální počítače budou nasazené ve stejném datovém centru, které hostuje jednotku velkých instancí HANA.

Pokud chcete zjistit, jestli je jednotka velkých instancí HANA nasazená v rámci kontrolního razítka nebo řádku revize 4, Projděte si článek [velké instance služby Azure Hana prostřednictvím Azure Portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). V přehledu atributů jednotky velkých instancí HANA můžete taky zjistit název skupiny umístění pro blízkosti, protože byla vytvořená v době nasazení pro jednotku velkých instancí HANA. Název zobrazený v přehledu atributů je název skupiny umístění blízkosti, kterou byste měli použít k nasazení virtuálních počítačů aplikační vrstvy do nástroje.

V opačném případě systémy SAP, které používají jenom virtuální počítače Azure, je potřeba, abyste při použití velkých instancí HANA od vás využívali, kolik [skupin prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) se má používat. Všechny jednotky velkých instancí služby HANA u [tenanta velké instance](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) se seskupují do jedné skupiny prostředků Azure, jak je popsáno [zde](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). Pokud nechcete, aby nasazení v různých klientech odlišilo, například produkční a neprodukční nebo některé systémy, budou všechny jednotky velkých instancí HANA nasazeny v jednom klientovi velké instance HANA, který má znovu vztah 1:1 s Azure. Skupina prostředků. Vzhledem k tomu, že všechny jednotlivé jednotky budou mít definované samostatné skupiny umístění blízkosti. 

V důsledku toho bude seskupení mezi skupinami prostředků Azure a skupinami umístění blízkosti pro jednoho tenanta vypadat takto:

![Skupiny umístění pro blízkosti všech virtuálních počítačů Azure](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Krátký příklad nasazení se skupinami umístění v blízkosti Azure
Pokud chcete předvést, jak můžete použít skupiny umístění v blízkosti Azure k nasazení virtuálního počítače, najdete seznam příkazů PowerShellu, které předvádějí jeho použití při prvním cvičení se skupinami umístění s blízkými službami Azure.

Prvním krokem po přihlášení pomocí [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) je ověřit, jestli jste ve správném předplatném Azure, které chcete použít k nasazení pomocí příkazu:

<pre><code>
Get-AzureRmContext
</code></pre>

Pokud potřebujete přejít na jiné předplatné, můžete to udělat spuštěním tohoto příkazu:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Jako třetí krok chcete vytvořit novou skupinu prostředků Azure pomocí tohoto příkazu:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Novou skupinu umístění blízkosti můžete vytvořit teď:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Teď můžete začít nasazovat svůj první virtuální počítač do této skupiny umístění blízkosti pomocí příkazu, jako je:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

S výše uvedeným příkazem je nasazený virtuální počítač založený na systému Windows. Po úspěšném nasazení tohoto virtuálního počítače se obor Datacenter skupiny umístění blízkosti definuje v oblasti Azure. Všechna následující nasazení virtuálních počítačů, která odkazují na skupinu umístění blízkosti jako v posledním příkazu, se nasadí ve stejném datovém centru Azure, pokud je možné hostovat typ virtuálního počítače na hardwaru umístěném v tomto datovém centru nebo na kapacitě pro tento typ virtuálního počítače.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Kombinování skupin dostupnosti a Zóny dostupnosti se skupinami umístění pro Proximity 
Pomocí Zóny dostupnosti pro nasazení systému SAP je jednou z nevýhod fakt, že vrstvu aplikace SAP nelze řídit pomocí skupin dostupnosti v rámci konkrétní zóny. Vzhledem k tomu, že chcete, aby byla vrstva aplikace SAP nasazená ve stejných zónách jako vrstva DBMS, a odkazování na zónu dostupnosti a skupinu dostupnosti, když nasazení jednoho virtuálního počítače není podporované, jste nuceni nasadit aplikační vrstvu odkazem na zónu. a tím ztratí schopnost zajistit, aby virtuální počítače vrstvy aplikace byly rozloženy v různých doménách aktualizace a selhání. S potřebami skupinami umístění blízkosti můžete toto omezení překonat. Sekvence nasazení by vypadala takto:

- Vytvoření skupiny umístění bezkontaktní komunikace
- Nasazení "Anchor VM", obvykle serveru DBMS odkazem na určitou zónu dostupnosti Azure
- Vytvoření skupiny dostupnosti, která odkazuje na skupinu Azure Proximity (viz níže)
- Nasazení virtuálních počítačů aplikační vrstvy odkazem na skupinu dostupnosti a skupinu umístění blízkosti

Místo nasazování prvního virtuálního počítače, jak je uvedeno výše, odkazujete na zónu dostupnosti Azure a na skupinu umístění blízkosti při nasazení virtuálního počítače, jako je:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Úspěšné nasazení tohoto virtuálního počítače, které by způsobilo hostování instance databáze v systému SAP v jedné zóně dostupnosti Azure, je rozsah skupiny umístění blízkosti pevně nastaven na jedno z datových center, které představují určenou zónu dostupnosti. .

Předpokládáme, že virtuální počítače služby Central Services nasazujete stejným způsobem jako virtuální počítače DBMs, a to tak, že odkazujete na stejné zóny jako pro virtuální počítače s DBMS a stejné skupiny umístění pro Proximity. V dalším kroku potřebujete vytvořit skupiny dostupnosti, které chcete použít pro aplikační vrstvu systému SAP.
Je potřeba definovat a vytvořit skupinu umístění blízkosti. Příkaz pro vytvoření sady dostupnosti vyžaduje další odkaz na ID skupiny umístění blízkosti (ne název). ID skupiny umístění blízkosti můžete získat takto:



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Když vytvoříte skupinu dostupnosti, musíte zvážit další parametry při použití spravovaných disků (výchozí, pokud je neurčíte jinak) a skupin umístění blízkosti:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

V ideálním případě byste měli použít tři domény selhání. Počet podporovaných domén selhání se ale může lišit od oblasti do oblasti. V takovém případě byla maximální možná počet domén selhání pro konkrétní oblasti dva. Pro nasazení virtuálních počítačů aplikační vrstvy musíte přidat odkaz na název skupiny dostupnosti a název skupiny umístění blízkosti, jak je znázorněno zde:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Konečný výsledek této sekvence bude vrstva DBMS a centrální služby vašeho systému SAP, které se nacházejí v konkrétní zóně dostupnosti a vrstvě aplikace SAP umístěné prostřednictvím skupin dostupnosti ve stejných datacentrech Azure, jako jsou třeba virtuální počítače s DBMS. nainstalována.

> [!NOTE]
> Když nasadíte jeden virtuální počítač s DBMS do jedné zóny a druhý virtuální počítač DBMS do jiné zóny, abyste vytvořili konfiguraci s vysokou dostupností, budete pro každou z těchto zón vyžadovat různé skupiny umístění blízkosti. Stejné jako true pro skupinu dostupnosti, kterou můžete použít

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>Získat existující systém do skupin umístění pro Proximity v blízkosti Azure
I když máte již nasazené systémy SAP, možná budete chtít optimalizovat latenci sítě některých důležitých systémů a umístit vrstvu aplikace a vrstvu DBMS do stejného datacentra. Ve fázi veřejné verze Preview funkce skupiny umístění pro Proximity je potřeba odstranění virtuálních počítačů a nové vytvoření virtuálních počítačů, aby se mohl tento přechod do skupin umístění blízkosti provést. V této fázi funkčnosti není dostatečné pro vypnutí virtuálních počítačů, aby bylo možné přiřadit virtuální počítače pro vypnutí do skupiny umístění pro Proximity.


## <a name="next-steps"></a>Další kroky
Projděte si dokumentaci:

- [Úlohy SAP v kontrolním seznamu pro plánování a nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Tisk Nasazení virtuálních počítačů do skupin umístění pro Proximity pomocí Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Tisk Nasazení virtuálních počítačů do skupin umístění Proximity pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

