---
title: Skupiny umístění bezkontaktní komunikace Azure pro aplikace SAP | Dokumenty společnosti Microsoft
description: Popisuje scénáře nasazení SAP se skupinami umístění azure.
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
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277593"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Skupiny umístění azure pro optimální latenci sítě s aplikacemi SAP
Aplikace SAP založené na architektuře SAP NetWeaver nebo SAP S/4HANA jsou citlivé na latenci sítě mezi aplikační vrstvou SAP a databázovou vrstvou SAP. Tato citlivost je výsledkem většiny obchodní logiky spuštěné v aplikační vrstvě. Vzhledem k tomu, že aplikační vrstva SAP spouští obchodní logiku, vydává dotazy na databázovou vrstvu s vysokou frekvencí rychlostí tisíců nebo desítek tisíc za sekundu. Ve většině případů je povaha těchto dotazů jednoduchá. Často je lze spustit na databázové vrstvě v 500 mikrosekundách nebo méně.

Čas strávený v síti k odeslání takového dotazu z aplikační vrstvy do databázové vrstvy a přijetí zpětné sady výsledků má zásadní vliv na dobu potřebný ke spuštění obchodních procesů. Tato citlivost na latenci sítě je důvod, proč je třeba dosáhnout optimální latence sítě v projektech nasazení SAP. Informace o klasifikaci latence sítě najdete v tématu [SAP Note #1100926 – nejčastější dotazy: Výkon sítě.](https://launchpad.support.sap.com/#/notes/1100926/E)

V mnoha oblastech Azure se počet datových center zvýšil. Tento růst byl rovněž vyvolán zavedením zón dostupnosti. Současně zákazníci, zejména pro špičkové systémy SAP, používají další speciální virtuální počítače v řadě M nebo velké instance HANA. Tyto typy virtuálních počítačů Azure nejsou dostupné ve všech datových centrech v určité oblasti Azure. Kvůli těmto dvěma tendencím zaznamenali zákazníci latenci sítě, která není v optimálním rozsahu. V některých případech tato latence má za následek neoptimální výkon jejich systémů SAP.

Chcete-li těmto problémům zabránit, Azure nabízí [skupiny umístění bezkontaktní .](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Tato nová funkce již byla použita k nasazení různých systémů SAP. Omezení týkající se skupin umístění bezkontaktní komunikace naleznete v článku uvedeném na začátku tohoto odstavce. Tento článek popisuje scénáře SAP, ve kterých mohou nebo by měly být použity skupiny umístění bezkontaktní komunikace Azure.

## <a name="what-are-proximity-placement-groups"></a>Co jsou skupiny umístění bezkontaktní chýše? 
Skupina umístění bezkontaktní komunikace Azure je logická konstrukce. Když je definován, je vázaný na oblast Azure a skupinu prostředků Azure. Při nasazení virtuálních jevů se na skupinu umístění bezkontaktní komunikace odkazuje:

- První virtuální počítač Azure nasazený v datovém centru. První virtuální počítač si můžete myslet jako "virtuální počítač s oborem", který se nasazuje v datovém centru na základě algoritmů přidělení Azure, které se nakonec zkombinují s definicemi uživatelů pro konkrétní zónu dostupnosti.
- Všechny následné virtuální počítače nasazené, které odkazují na skupinu umístění bezkontaktní umístění, umístit všechny následně nasazené virtuální počítače Azure ve stejném datovém centru jako první virtuální počítač.

> [!NOTE]
> Pokud není nasazen žádný hostitelský hardware, který by mohl spustit určitý typ virtuálního počítače v datovém centru, kde byl umístěn první virtuální modul, nasazení požadovaného typu virtuálního počítače nebude úspěšné. Dostanete zprávu o selhání.

Jedna [skupina prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) může mít přiřazeno více skupin umístění bezkontaktní komunikace. Ale skupinu umístění bezkontaktní umístění lze přiřadit jenom jedné skupině prostředků Azure.

Při použití skupin umístění bezkontaktní komunikace mějte na paměti tyto skutečnosti:

- Když se zaměříte na optimální výkon pro váš systém SAP a omezíte se na jedno datové centrum Azure pro systém pomocí skupin umístění bezkontaktní komunikace, možná nebudete moct kombinovat všechny typy rodin virtuálních počítačů v rámci skupiny umístění. K těmto omezením dochází, protože hostitelský hardware, který je potřeba ke spuštění určitého typu virtuálního zařízení, nemusí být přítomen v datovém centru, do kterého byl nasazen "vymezený virtuální modul" skupiny umístění.
- Během životního cyklu takového systému SAP můžete být nuceni přesunout systém do jiného datového centra. Tento krok může být vyžadován, pokud se rozhodnete, že vrstva HANA DBMS by se měla přesunout například ze čtyř uzlů na 16 uzlů a není dostatečná kapacita k získání dalších 12 virtuálních počítače typu, který jste použili v datovém centru.
- Z důvodu vyřazení hardwaru z provozu může Microsoft vytvářet kapacity pro typ virtuálního zařízení, který jste použili v jiném datovém centru, nikoli pro ten, který jste původně používali. V tomto scénáři možná budete muset přesunout všechny virtuální počítače skupiny bezkontaktní umístění do jiného datového centra.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Skupiny umístění bezkontaktního umístění se systémy SAP, které používají jenom virtuální počítače Azure
Většina nasazení sap netweaver a s/4HANA systému v Azure nepoužívá [velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Pro nasazení, která nepoužívají velké instance HANA, je důležité zajistit optimální výkon mezi aplikační vrstvou SAP a vrstvou DBMS. Chcete-li tak učinit, definujte skupinu umístění bezkontaktní komunikace Azure pouze pro systém.

Ve většině zákaznických nasazení zákazníci vytvoří jednu [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) pro systémy SAP. V takovém případě existuje vztah 1:1 mezi například skupinou provozních prostředků erp systému a její skupinou umístění bez kontaktní ch od stm. V ostatních případech zákazníci uspořádají své skupiny prostředků vodorovně a shromažďují všechny produkční systémy v jedné skupině prostředků. V takovém případě byste měli vztah 1:N mezi skupinou prostředků pro produkční systémy SAP a několika skupinami umístění bezkontaktní komunikace pro produkční SAP ERP, SAP BW a tak dále.

Vyhněte se sdružování několika výrobních nebo neprodukčních systémů SAP do jedné skupiny umístění bez kontaktních míst. Pokud malý počet systémů SAP nebo systémSAP a některé okolní aplikace potřebují mít síťovou komunikaci s nízkou latencí, můžete zvážit přesunutí těchto systémů do jedné skupiny umístění bez kontaktní ch odtéto sítě. Měli byste se vyhnout svazkům systémů, protože čím více systémů seskupujete ve skupině umístění bez kontaktních míst, tím vyšší je pravděpodobnost:

- Že budete potřebovat typ virtuálního soudu, který nelze spustit v konkrétním datovém centru, do kterého byla skupina umístění přiblížení vymezena.
- Prostředky nemainstreamových virtuálních počítače, jako jsou virtuální počítače řady M, mohou být nakonec nenaplněné, když potřebujete víc, protože v průběhu času přidáváte software do skupiny umístění bez kontaktních míst.

Zde je to, co ideální konfigurace, jak je popsáno, vypadá takto:

![Skupiny umístění bezkontaktního umístění pouze s virtuálními počítači Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

V tomto případě jsou jednotlivé systémy SAP seskupeny do jedné skupiny prostředků, přičemž každá z nich je v jedné skupině umístění bez kontaktních míst. Neexistuje žádná závislost na tom, zda používáte konfigurace hana horizontálnínavýšení kapacity nebo konfigurace škálování DBMS.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Skupiny umístění bezkontaktní komunikace a velké instance HANA
Pokud některé z vašich systémů SAP spoléhají na [velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) pro aplikační vrstvu, můžete zaznamenat významné zlepšení latence sítě mezi jednotkou velkých instancí HANA a virtuálními počítači Azure, když používáte jednotky velkých instancí HANA, které jsou nasazeny v [řádcích nebo razítkách revize 4](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Jedním z vylepšení je, že jednotky velkých instancí HANA, jak jsou nasazeny, nasazují se skupinou umístění bez kontaktní chod. Tuto skupinu umístění bezkontaktní komunikace můžete použít k nasazení virtuálních počítačích aplikační vrstvy. V důsledku toho budou tyto virtuální počítače nasazeny ve stejném datovém centru, které hostuje vaši jednotku velkých instancí HANA.

Chcete-li zjistit, zda je vaše jednotka velkých instancí HANA nasazená v razítku nebo řádku revize 4, podívejte se na článek [řízení velkých instancí Azure HANA prostřednictvím portálu Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)Portal . V přehledu atributů jednotky velkých instancí HANA můžete také určit název skupiny umístění bezkontaktní komunikace, protože byla vytvořena při nasazení jednotky velkých instancí HANA. Název, který se zobrazí v přehledu atributů, je název skupiny umístění bezkontaktní komunikace, do které byste měli nasadit virtuální počítače aplikační vrstvy.

Ve srovnání se systémy SAP, které používají jenom virtuální počítače Azure, máte při použití velkých instancí HANA menší flexibilitu při rozhodování o tom, kolik [skupin prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) použít. Všechny jednotky velkých instancí [HANA klienta velkých instancí HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) jsou seskupeny do jedné skupiny prostředků, jak je popsáno v [tomto článku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). Pokud nenasadíte do různých klientů oddělit, například produkční a neprodukční systémy nebo jiné systémy, všechny vaše hana velké instance jednotky budou nasazeny v jednom tenantovi hana velké instance. Tento klient má vztah 1:1 se skupinou prostředků. Pro každou jednotlivou jednotky však bude definována samostatná skupina umístění bezkontaktní.

V důsledku toho vztahy mezi skupinami prostředků Azure a skupiny umístění bezkontaktní umístění pro jednoho klienta bude, jak je znázorněno zde:

![Skupiny umístění bezkontaktní komunikace a velké instance HANA](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Příklad nasazení se skupinami umístění bez kontaktních míst
Následují některé příkazy Prostředí PowerShell, které můžete použít k nasazení virtuálních počítačů se skupinami umístění bezkontaktní komunikace Azure.

Prvním krokem po přihlášení k [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)je zkontrolovat, jestli jste v předplatném Azure, které chcete použít pro nasazení:

<pre><code>
Get-AzureRmContext
</code></pre>

Pokud potřebujete změnit na jiné předplatné, můžete tak učinit spuštěním tohoto příkazu:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Vytvořte novou skupinu prostředků Azure spuštěním tohoto příkazu:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Spouštěním tohoto příkazu vytvořte novou skupinu umístění bezkontaktní komunikace:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Nasaďte svůj první virtuální počítač do skupiny umístění v blízkosti pomocí příkazu, jako je tento:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Předchozí příkaz nasazuje virtuální hosto. Po úspěšném nasazení tohoto virtuálního počítače je obor datového centra skupiny umístění bezkontaktní komunikace definovaný v oblasti Azure. Všechna následná nasazení virtuálních počítačen, která odkazují na skupinu umístění bezkontaktní komunikace, jak je znázorněno v předchozím příkazu, se nasadí ve stejném datovém centru Azure, pokud typ virtuálního počítače může být hostovaný na hardwaru umístěném v tomto datovém centru a kapacita pro daný typ virtuálního počítače je K dispozici.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Kombinujte skupiny dostupnosti a zóny dostupnosti se skupinami umístění bez kontaktních míst
Jednou z nevýhod použití zón dostupnosti pro nasazení systému SAP je, že nelze nasadit aplikační vrstvu SAP pomocí sad dostupnosti v rámci konkrétní zóny. Chcete, aby aplikační vrstva SAP byla nasazena ve stejných zónách jako vrstva DBMS. Odkazování na zónu dostupnosti a sadu dostupnosti při nasazování jednoho virtuálního aplikace není podporováno. Takže dříve jste byli nuceni nasadit aplikační vrstvu odkazem na zónu. Ztratili jste možnost ujistit se, že virtuální servery aplikační vrstvy byly rozloženy do různých domén aktualizací a selhání.

Pomocí skupin umístění bezkontaktní komunikace můžete toto omezení obejít. Tady je pořadí nasazení:

- Vytvořte skupinu umístění bezkontaktní komunikace.
- Nasaďte svůj virtuální počítač kotvy, obvykle server DBMS, odkazem na zónu dostupnosti.
- Vytvořte sadu dostupnosti, která odkazuje na skupinu bezkontaktních řešení Azure. (Viz příkaz dále v tomto článku.)
- Nasaďte virtuální aplikace pomocí odkazování na sadu dostupnosti a skupinu umístění bezkontaktní komunikace.

Místo nasazení prvního virtuálního vztahu, jak je znázorněno v předchozí části, odkazujete na zónu dostupnosti a skupinu umístění bezkontaktní služby při nasazování virtuálního soudu:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Úspěšné nasazení tohoto virtuálního počítače by bylo hostitelem instance databáze systému SAP v jedné zóně dostupnosti. Rozsah skupiny umístění bezkontaktní komunikace je pevně stanoven na jedno z datových center, které představují zónu dostupnosti, kterou jste definovali.

Předpokládejme, že nasazujete virtuální chod centrálních služeb stejným způsobem jako virtuální ms DBMS, s odkazem na stejnou zónu nebo zóny a stejné skupiny umístění bez kontaktních míst. V dalším kroku je třeba vytvořit skupiny dostupnosti, které chcete použít pro aplikační vrstvu vašeho systému SAP.

Je třeba definovat a vytvořit skupinu umístění bezkontaktní komunikace. Příkaz pro vytvoření skupiny dostupnosti vyžaduje další odkaz na ID skupiny umístění bez kontaktní ch utváření (nikoli název). ID skupiny umístění bezkontaktní komunikace můžete získat pomocí tohoto příkazu:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Při vytváření sady dostupnosti je třeba při použití spravovaných disků (výchozí, pokud není uvedeno jinak) a skupiny umístění bez kontaktních míst, zvážit další parametry:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

V ideálním případě byste měli použít tři domény selhání. Počet podporovaných domén selhání se však může v jednotlivých oblastech lišit. V tomto případě je maximální počet domén selhání možný pro konkrétní oblasti dvě. Chcete-li nasadit virtuální počítače aplikační vrstvy, musíte přidat odkaz na název sady dostupnosti a název skupiny umístění bezkontaktní komunikace, jak je znázorněno zde:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Výsledkem tohoto nasazení je:
- Vrstva DBMS a centrální služby pro váš systém SAP, který se nachází v určité zóně dostupnosti nebo zónách dostupnosti.
- Aplikační vrstva SAP, která se nachází prostřednictvím sad dostupnosti ve stejných datových centrech Azure jako virtuální počítač DBMS nebo virtuální počítače.

> [!NOTE]
> Vzhledem k tomu, že nasadíte jeden virtuální virtuální počítače DBMS do jedné zóny a druhý virtuální ms DBMS do jiné zóny k vytvoření konfigurace s vysokou dostupností, budete potřebovat jinou skupinu umístění bezkontaktní komunikace pro každou zónu. Totéž platí pro všechny skupiny dostupnosti, které používáte.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Přesunutí existujícího systému do skupin umístění bez kontaktních míst
Pokud už máte nasazené systémy SAP, můžete optimalizovat latenci sítě některých důležitých systémů a vyhledat aplikační vrstvu a vrstvu DBMS ve stejném datovém centru. Chcete-li přesunout virtuální počítače kompletní sady dostupnosti Azure do existující skupiny umístění bezkontaktní umístění, která je již vymezena, je nutné vypnout všechny virtuální počítače skupiny dostupnosti a přiřadit sadu dostupnosti stávající skupiny umístění bezkontaktní komunikace prostřednictvím portálu Azure, Prostředí PowerShell nebo cli. Pokud chcete přesunout virtuální ho virtuální hod, který není součástí skupiny dostupnosti do existující skupiny umístění bezkontaktní komunikace, stačí vypnout virtuální ho a přiřadit k existující skupině umístění bezkontaktní komunikace. 


## <a name="next-steps"></a>Další kroky
Podívejte se na dokumentaci:

- [Úlohy SAP v Azure: kontrolní seznam plánování a nasazení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Předběžná verze: Nasazení virtuálních počítačů do skupin umístění bezkontaktní komunikace pomocí azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Náhled: Nasazení virtuálních účtů do skupin umístění bezkontaktní komunikace pomocí PowerShellu](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Důležité informace o nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

