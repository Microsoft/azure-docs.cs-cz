---
title: Nasazení šablony řešení Ethereem pro kontrolu pravopisu pro účely úřadu v Azure
description: Nasazení a konfigurace Ethereemé sítě konsorcia Ethereem pro více členů v Azure pomocí řešení pro kontrolu pravopisu
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: devx-track-js
ms.openlocfilehash: d1d3ad94957e791b2178b6c60d4c7debdec2b391
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283424"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Nasazení šablony řešení Ethereem pro kontrolu pravopisu pro účely úřadu v Azure

K nasazení, konfiguraci a řízení Ethereemé sítě pro ověření ve více členských sítích konsorcia s minimálními znalostmi Azure a Ethereem můžete použít [šablonu řešení Ethereem pro kontrolu a používání ve verzi Preview](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) .

Šablonu řešení může použít každý člen konsorcia k zřízení blockchainch síťových nároků pomocí Microsoft Azure výpočetní, síťové a úložné služby. Nároky na síť jednotlivých členů konsorcia se skládají ze sady uzlů validátoru s vyrovnáváním zatížení, se kterými může aplikace nebo uživatel s odesláním transakcí Ethereem pracovat.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Výběr řešení Azure blockchain

Než se rozhodnete použít šablonu řešení Ethereem pro kontrolu pravopisu, porovnejte svůj scénář s běžnými případy použití dostupných možností Azure blockchain.

> [!IMPORTANT]
> Zvažte použití [služby Azure blockchain](../service/overview.md) namísto šablony řešení Ethereem v Azure. Služba Azure Blockchain je podporovaná spravovaná služba Azure. Paritní Ethereem přecházejí na vývoj a údržbu řízený komunitou. Další informace najdete v tématu [přechod Ethereem parity na OPENETHEREUM DAO](https://www.parity.io/parity-ethereum-openethereum-dao/).

Možnost | Model služby | Běžný případ použití
-------|---------------|-----------------
Šablony řešení | IaaS | Šablony řešení jsou Azure Resource Manager šablony, pomocí kterých můžete zřídit plně nakonfigurovanou topologii sítě blockchain. Šablony nasazují a konfigurují Microsoft Azure COMPUTE, sítě a služby úložiště pro daný typ sítě blockchain. Šablony řešení jsou poskytovány bez smlouvy o úrovni služeb. Pro podporu použijte [stránku s otázkou Microsoft Q&](/answers/topics/azure-blockchain-workbench.html) .
[Služba Azure Blockchain](../service/overview.md) | PaaS | Služba Azure blockchain ve verzi Preview zjednodušuje vytváření, správu a řízení sítí konsorcia blockchain. Využijte Azure blockchain Service pro řešení, která vyžadují PaaS, správu konsorcia nebo jejich soukromí a transakce.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS a PaaS | Azure blockchain Workbench Preview je kolekce služeb a funkcí Azure, které vám pomůžou vytvářet a nasazovat aplikace blockchain pro sdílení obchodních procesů a dat s jinými organizacemi. Využijte Azure blockchain Workbench pro vytváření prototypů řešení blockchain nebo blockchain aplikace pro zkoušku konceptu. Azure Blockchain Workbench se poskytuje bez smlouvy o úrovni služeb. Pro podporu použijte [stránku s otázkou Microsoft Q&](/answers/topics/azure-blockchain-workbench.html) .

## <a name="solution-architecture"></a>Architektura řešení

Pomocí šablony řešení Ethereem můžete nasadit jednu nebo více členů založeného na více než jedné oblasti založené na více členech sítě konsorcia Ethereem pro ověření.

![architektura nasazení](./media/ethereum-poa-deployment/deployment-architecture.png)

Každé nasazení člena konsorcia zahrnuje:

* Virtual Machines pro spouštění validátorů pro PoA
* Azure Load Balancer pro distribuci požadavků RPC, partnerských vztahů a zásad správného řízení DApp
* Azure Key Vault pro zabezpečení ověřovacích identit
* Azure Storage pro hostování trvalých síťových informací a koordinace leasingu
* Azure Monitor pro agregaci protokolů a statistiky výkonu
* Brána virtuální sítě (volitelné) pro povolení připojení VPN v privátních virtuální sítě

Ve výchozím nastavení jsou koncové body RPC a peering přístupné přes veřejnou IP adresu, aby bylo možné zjednodušené připojení mezi předplatnými a cloudy. Pro řízení přístupu na úrovni aplikace můžete použít [kontrakty oprávnění parity](https://wiki.parity.io/Permissioning). Podporují se sítě nasazené za sítě VPN, které využívají brány virtuální sítě pro připojení mezi předplatnými. Vzhledem k tomu, že nasazení VPN a virtuálních sítí je složitější, možná budete chtít při vytváření prototypů řešení začít s modelem veřejné IP adresy.

Kontejnery Docker se používají pro spolehlivost a modularitu. Azure Container Registry slouží jako součást jednotlivých nasazení k hostování a obsluze imagí se správou verzí. Image kontejneru se skládají z těchto:

* Orchestrator – vygeneruje identity a smlouvy zásad správného řízení. Ukládá identity do úložiště identit.
* Paritní klienti – zapůjčení identity z úložiště identit. Zjišťuje a připojuje se k partnerským uzlům.
* Agent EthStats – shromažďuje místní protokoly a statistiky prostřednictvím protokolu RPC a odesílá informace do Azure Monitor.
* Zásady správného řízení DApp-webové rozhraní pro interakci se smlouvami zásad správného řízení.

### <a name="validator-nodes"></a>Uzly validátoru

V protokolu ověření autority vybírají uzly validátory místo tradičních uzlů Miner. Každý validátor má jedinečnou Ethereem identitu, která umožňuje zapojení do procesu vytváření bloků. Každý člen konsorcia může zřídit dva nebo více uzlů validátoru v rámci pěti oblastí pro geografickou redundanci. Uzly validátoru komunikují s jinými uzly validátoru a přijdou do konsensu na stav základní distribuované hlavní knihy. Aby se zajistila poctivá účast v síti, je pro každého člena konsorcia zakázáno používat více validátorů než první člen v síti. Například pokud první člen nasadí tři validátory, může mít každý člen pouze až tři validátory.

### <a name="identity-store"></a>Úložiště identit

Úložiště identit se nasadí v rámci předplatného každého člena, které bezpečně udržuje vygenerované Ethereem identity. Pro každý validátor vygeneruje kontejner Orchestration privátní klíč Ethereem a uloží jej do Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Nasazení sítě Ethereem Consortium

V tomto návodu budeme předpokládat, že vytváříte Ethereemou síť konsorcia pro více stran. Následující postup je příkladem nasazení s více stranami:

1. Tři členové generují účet Ethereem pomocí MetaMask
1. *Člen A* nasadí ethereem POA a poskytne jim svou ethereem veřejnou adresu.
1. *Člen A* poskytuje konsorcium URL pro *členské B* a *členské C* .
1. *Členské B* a *členské C* nasazují ethereem POA a poskytují svou ethereem veřejnou adresu a adresu URL *člena*a 's Consortium.
1. *Člen A* hlasy *člena B* jako správce
1. *Člen a* a *člen B* – hlas *členů C* jako správce

V dalších částech se dozvíte, jak nakonfigurovat nastavení prvního člena v síti.

### <a name="create-resource"></a>Vytvoření prostředku

V [Azure Portal](https://portal.azure.com)v levém horním rohu vyberte **vytvořit prostředek** .

Vyberte **blockchain**  >  **ethereem pro kontrolu-of-Authority Consortium (Preview)**.

### <a name="basics"></a>Základy

V části **základy**zadejte hodnoty pro standardní parametry pro jakékoli nasazení.

![Základy](./media/ethereum-poa-deployment/basic-blade.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
Vytvořit novou síť nebo připojit se k existující síti | Můžete vytvořit novou síť konsorcia nebo se připojit k již existující síti konsorcia. Připojení k existující síti vyžaduje další parametry. | Vytvořit nový
E-mailová adresa | Po dokončení nasazení se zobrazí e-mailové oznámení s informacemi o vašem nasazení. | Platná e-mailová adresa
Uživatelské jméno virtuálního počítače | Uživatelské jméno správce každého nasazeného virtuálního počítače | 1-64 alfanumerických znaků
Typ ověřování | Metoda, která se má ověřit pro virtuální počítač. | Heslo
Heslo | Heslo pro účet správce pro každý nasazený virtuální počítač. Všechny virtuální počítače mají zpočátku stejné heslo. Po zřízení můžete heslo změnit. | 12-72 znaků 
Předplatné | Předplatné, ke kterému se má nasadit síť konsorcia |
Skupina prostředků| Skupina prostředků, do které se má nasadit síť konsorcia | myResourceGroup
Umístění | Oblast Azure pro skupinu prostředků. | Západní USA 2

Vyberte **OK**.

### <a name="deployment-regions"></a>Oblasti nasazení

V části *oblasti nasazení*určete počet oblastí a umístění pro každý z nich. Nasazení můžete nasadit v maximálně pěti oblastech. První oblast by se měla shodovat s umístěním skupiny prostředků z části *základy* . Pro vývojové nebo testovací sítě můžete použít jednu oblast na člena. Pro produkční prostředí nasaďte ve dvou nebo více oblastech pro zajištění vysoké dostupnosti.

![oblasti nasazení](./media/ethereum-poa-deployment/deployment-regions.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
Počet oblastí|Počet oblastí pro nasazení sítě konsorcia| 2
První oblast | První oblast pro nasazení sítě konsorcia | Západní USA 2
Druhá oblast | Druhá oblast pro nasazení sítě konsorcia. Další oblasti jsou viditelné, pokud je počet oblastí 2 nebo vyšší. | USA – východ 2

Vyberte **OK**.

### <a name="network-size-and-performance"></a>Velikost a výkon sítě

V části *velikost a výkon sítě*zadejte vstupy pro velikost sítě konsorcia. Velikost úložiště uzlu validátoru určuje potenciální velikost blockchain. Velikost může být změněna po nasazení.

![Velikost a výkon sítě](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
Počet uzlů validátoru vyrovnávání zatížení | Počet uzlů validátoru, které se mají zřídit v rámci sítě | 2
Výkon úložiště uzlu validátoru | Typ spravovaného disku pro každý z nasazených uzlů validátoru. Podrobnosti o cenách najdete v tématu [ceny služby Storage](https://azure.microsoft.com/pricing/details/managed-disks/) . | SSD úrovně Standard
Velikost virtuálního počítače uzlu validátoru | Velikost virtuálního počítače používaná pro uzly validátoru. Podrobnosti o cenách najdete v tématu [ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) . | D2 V3 úrovně Standard

Virtuální počítač a vrstva úložiště mají vliv na výkon sítě.  Pro výběr cenové efektivity použijte následující tabulku:

SKU virtuálního počítače|Vrstva úložiště|Cena|Propustnost|Latence
---|---|---|---|---
F1|SSD úrovně Standard|slab|slab|high
D2_v3|SSD úrovně Standard|střední|střední|střední
F16s úrovně|SSD úrovně Premium|high|high|slab

Vyberte **OK**.

### <a name="ethereum-settings"></a>Nastavení ethereem

V části *Nastavení ethereem*zadejte nastavení konfigurace související s ethereem.

![Nastavení ethereem](./media/ethereum-poa-deployment/ethereum-settings.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
ID člena konsorcia | ID přidružené ke každému členovi zapojenému do sítě konsorcia. Slouží ke konfiguraci adresních prostorů IP adres, aby se zabránilo kolizi. V případě privátní sítě by ID člena mělo být jedinečné v různých organizacích ve stejné síti.  Jedinečné ID člena je nutné i v případě, že je stejná organizace nasazena do více oblastí. Poznamenejte si hodnotu tohoto parametru, protože ho budete potřebovat sdílet s ostatními členy, abyste se ujistili, že nedochází k žádné kolizi. Platný rozsah je 0 až 255. | 0
ID sítě | ID sítě pro nasazování sítě konsorcia Ethereem Každá Ethereem síť má své vlastní ID sítě, přičemž 1 je identifikátor veřejné sítě. Platný rozsah je 5 až 999 999 999. | 10101010
Adresa Ethereem správce | Adresa účtu Ethereem, která se používá pro účast ve správě PoA. Pomocí MetaMask můžete vygenerovat Ethereem adresu. |
Rozšířené možnosti | Rozšířené možnosti pro nastavení Ethereem | Povolit
Nasazení pomocí veřejné IP adresy | Pokud je vybraná privátní virtuální síť, síť se nasadí za bránu virtuální sítě a odebere přístup partnerských vztahů. U privátní virtuální sítě musí všichni členové používat bránu virtuální sítě, aby připojení bylo kompatibilní. | Veřejná IP adresa
Limit bloku plynů | Počáteční limit bloku plynů sítě. | 50000000
Zablokovat dobu trvání uzávěrky (s) | Frekvence, s níž budou vytvořeny prázdné bloky, pokud v síti nejsou žádné transakce. Vyšší frekvence bude mít rychlejší, ale zvýšené náklady na úložiště. | 15
Kontrakt oprávnění transakce | Kód pro kontrakt oprávnění transakce. Omezí nasazení a provádění inteligentních kontraktů na povolený seznam účtů Ethereem. |

Vyberte **OK**.

### <a name="monitoring"></a>Monitorování

Monitorování umožňuje nakonfigurovat prostředek protokolu pro vaši síť. Agent monitorování shromažďuje a vystavuje užitečné metriky a protokoly z vaší sítě, které poskytují možnost rychle kontrolovat stav sítě nebo problémy s laděním.

![Monitorování Azure](./media/ethereum-poa-deployment/azure-monitor.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
Monitorování | Možnost povolení monitorování | Povolit
Připojit k existujícím protokolům Azure Monitor | Možnost vytvoření nové instance protokolu Azure Monitor nebo připojení k existující instanci | Vytvořit nový
Umístění | Oblast, ve které je nasazena nová instance | East US
Existující ID pracovního prostoru Log Analytics (připojit k existujícím protokolům Azure Monitor = připojit existující)|ID pracovního prostoru existující instance protokolu Azure Monitor||Není k dispozici
Existující primární klíč Log Analytics (Připojte se k existujícím protokolům Azure Monitor = připojit existující)|Primární klíč, který se používá pro připojení ke stávající instanci Azure Monitorch protokolů||Není k dispozici

Vyberte **OK**.

### <a name="summary"></a>Shrnutí

Kliknutím na Souhrn zkontrolujte zadané vstupy a spusťte základní ověřování před nasazením. Před nasazením si můžete stáhnout šablonu a parametry.

Vyberte **vytvořit** k nasazení.

Pokud nasazení zahrnuje brány virtuální sítě, může nasazení trvat 45 až 50 minut.

## <a name="deployment-output"></a>Výstup nasazení

Po dokončení nasazení můžete k potřebným parametrům přistupovat pomocí Azure Portal.

### <a name="confirmation-email"></a>Potvrzovací e-mail

Pokud zadáte e-mailovou adresu ([oddíl základy](#basics)), pošle se e-mail, který obsahuje informace o nasazení a odkazy na tuto dokumentaci.

![e-mail pro nasazení](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portál

Po úspěšném dokončení nasazení a zřízení všech prostředků můžete zobrazit výstupní parametry ve vaší skupině prostředků.

1. Na portálu přejdete na skupinu prostředků.
1. Vyberte **přehled > nasazení**.

    ![Přehled skupiny prostředků](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Vyberte nasazení **Microsoft-Azure-blockchain. Azure-blockchain-ether-...** .
1. Vyberte část **výstupy** .

    ![Výstupy nasazení](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Rozšiřování konsorcia

Chcete-li rozšířit konsorcium, musíte nejprve připojit fyzickou síť. Pokud nasazujete za síť VPN, přečtěte si část [připojení brány virtuální](#connecting-vnet-gateways) sítě v rámci nasazení nového člena. Po dokončení nasazení se pomocí [DApp zásad správného řízení](#governance-dapp) stane správce sítě.

### <a name="new-member-deployment"></a>Nasazení nového člena

Sdílet následující informace s připojujícím se členem. Informace najdete v e-mailu po nasazení nebo ve výstupu nasazení na portálu.

* Adresa URL dat konsorcia
* Počet nasazených uzlů
* ID prostředku brány virtuální sítě (Pokud používáte VPN)

Člen nasazení by měl při nasazení jejich síťové přítomnosti používat stejnou šablonu řešení Ethereem (rememberes), která se bude nasazovat pomocí následujících pokynů:

* Vyberte **připojit existující**
* Vyberte stejný počet uzlů validátoru jako zbytek členů sítě, aby se zajistila korektní reprezentace.
* Použít stejnou Ethereem adresu správce
* V *Nastavení ethereem* použijte *adresu URL poskytnutého datového sdružení* .
* Pokud je zbytek sítě za sítí VPN, vyberte v části Upřesnit možnost **privátní virtuální síť** .

### <a name="connecting-vnet-gateways"></a>Připojení bran virtuální sítě

Tato část je nutná pouze v případě, že jste nasadili pomocí privátní virtuální sítě. Pokud používáte veřejné IP adresy, můžete tuto část přeskočit.

V případě privátní sítě jsou různí členové připojeni prostřednictvím připojení brány virtuální sítě. Předtím, než se člen může připojit k síti a zobrazit transakční provoz, musí stávající člen provést konečnou konfiguraci brány VPN, aby bylo možné připojení přijmout. Uzly Ethereem spojovacího člena se nespustí, dokud nebude navázáno připojení. Chcete-li snížit pravděpodobnost jednoho bodu selhání, vytvořte redundantní síťová připojení v konsorciu.

Po nasazení nového člena musí stávající člen dokončit obousměrné připojení tím, že nastaví připojení brány virtuální sítě k novému členu. Existující požadavky členů:

* Brána virtuální sítě ResourceID připojujícího se člena. Viz [výstup nasazení](#deployment-output).
* Sdílený klíč připojení.

Aby bylo možné dokončit připojení, musí stávající člen spustit následující skript prostředí PowerShell. Můžete použít Azure Cloud Shell umístěnou v pravém horním navigačním panelu na portálu.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Monitorování služeb

Portál Azure Monitor můžete najít buď pomocí odkazu v e-mailu pro nasazení, nebo vyhledáním parametru ve výstupu nasazení [OMS_PORTAL_URL].

Portál nejprve zobrazí statistiku sítě vysoké úrovně a přehled uzlů.

![Kategorie monitorování](./media/ethereum-poa-deployment/monitor-categories.png)

Vybírání **přehledu uzlů**  zobrazuje statistiku infrastruktury pro jednotlivé uzly.

![Statistiky uzlů](./media/ethereum-poa-deployment/node-stats.png)

Výběr statistiky **sítě** vám ukáže ethereem statistiku sítě.

![Statistiky sítě](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Můžete zadat dotaz na protokoly monitorování a prozkoumat chyby nebo upozornění na mezní hodnotu instalace. Následující dotazy jsou příklady, které lze spustit v nástroji pro *prohledávání protokolu* :

Seznamy bloků, které byly hlášeny více než jedním dotazem validátoru, mohou být užitečné při hledání větví zřetězení.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Získejte průměrnou hodnotu počtu partnerských uzlů pro zadaný uzel validátoru na průměr v intervalu 5 minut.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>Přístup přes SSH

Z bezpečnostních důvodů je ve výchozím nastavení odepřený přístup portu SSH pravidlem zabezpečení skupiny sítě. Chcete-li získat přístup k instancím virtuálních počítačů v síti PoA, je nutné změnit následující pravidlo zabezpečení na hodnotu *povoleno*.

1. V Azure Portal přejdete do oddílu **Přehled** nasazené skupiny prostředků.

    ![Přehled SSH](./media/ethereum-poa-deployment/ssh-overview.png)

1. Vyberte **skupinu zabezpečení sítě** pro oblast virtuálního počítače, ke kterému chcete získat přístup.

    ![NSG SSH](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Vyberte pravidlo **Allow-SSH** .

    ![Snímek obrazovky zobrazuje okno s přehledem povoleného výběru SSH.](./media/ethereum-poa-deployment/ssh-allow.png)

1. Změnit **akci** na **povoleno**

    ![Povolit povolení protokolu SSH](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Vyberte **Uložit**. Použití změn může trvat několik minut.

Vzdáleně se můžete připojit k virtuálním počítačům pro uzly validátoru přes SSH s vaším uživatelským jménem správce a heslem/klíčem SSH. Příkaz SSH pro přístup k prvnímu ověřovacímu uzlu je uveden ve výstupu nasazení šablony. Příklad:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Chcete-li získat další uzly transakcí, zvyšte číslo portu o jednu.

Pokud jste nasadili do více než jedné oblasti, změňte příkaz na název DNS nebo IP adresu nástroje pro vyrovnávání zatížení v této oblasti. Pokud chcete najít název DNS nebo IP adresu ostatních oblastí, vyhledejte prostředek pomocí konvence pojmenování ** \* \* \* \* \* – lbpip-reg \# ** a zobrazte jeho název DNS a IP adresu.

## <a name="azure-traffic-manager-load-balancing"></a>Vyrovnávání zatížení Azure Traffic Manager

Služba Azure Traffic Manager může pomoci snižovat prostoje a zlepšit odezvu sítě PoA směrováním příchozího provozu napříč několika nasazeními v různých oblastech. Integrované kontroly stavu a automatické přesměrování vám pomůžou zajistit vysokou dostupnost koncových bodů RPC a DApp zásad správného řízení. Tato funkce je užitečná v případě, že jste nasadili více oblastí a jste připravení pro produkci.

Pomocí Traffic Manager můžete zlepšit dostupnost sítě PoA pomocí automatického převzetí služeb při selhání. Traffic Manager můžete použít také ke zvýšení rychlosti odezvy sítí, a to směrováním koncových uživatelů do umístění Azure s nejnižší latencí sítě.

Pokud se rozhodnete vytvořit profil Traffic Manager, můžete k přístupu k síti použít název DNS daného profilu. Po přidání dalších členů konsorcia do sítě je možné Traffic Manager použít také k vyrovnávání zatížení napříč nasazenými validátory.

### <a name="creating-a-traffic-manager-profile"></a>Vytváří se profil Traffic Manager.

1. V [Azure Portal](https://portal.azure.com)v levém horním rohu vyberte **vytvořit prostředek** .
1. Vyhledejte **profil Traffic Manager**.

    ![Hledání Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Dejte profilu jedinečný název a vyberte skupinu prostředků, která se použila pro nasazení PoA.

1. Vyberte **vytvořit** k nasazení.

    ![Vytvořit Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Po nasazení vyberte instanci ve skupině prostředků. Název DNS pro přístup k Traffic Manageru najdete na kartě Přehled.

    ![Vyhledání služby DNS Traffic Manageru](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Zvolte kartu **koncové body** a vyberte tlačítko **Přidat** .
1. Zadejte jedinečný název koncového bodu.
1. Jako **typ cílového prostředku**vyberte **Veřejná IP adresa**.
1. Vyberte veřejnou IP adresu nástroje pro vyrovnávání zatížení první oblasti.

    ![Směrování Traffic Manageru](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Opakujte pro každou oblast v nasazené síti. Jakmile jsou koncové body v **povoleném** stavu, automaticky se načítají a vyrovnávají oblasti v názvu DNS Traffic Manageru. Tento název DNS teď můžete použít místo parametru [CONSORTIUM_DATA_URL] v dalších krocích tohoto článku.

## <a name="data-api"></a>Rozhraní API pro data

Každý člen konsorcia hostuje nezbytné informace, aby je ostatní mohli připojit k síti. Aby bylo možné snadno připojit, každý člen je hostitelem sady informací o připojení na koncovém bodu rozhraní data API.

Stávající člen poskytuje před nasazením člena [CONSORTIUM_DATA_URL]. Při nasazení bude připojující člen získat informace z rozhraní JSON v následujícím koncovém bodu:

`<CONSORTIUM_DATA_URL>/networkinfo`

Odpověď obsahuje informace, které jsou užitečné pro spojování členů (blok Genesis, ověřovací sada kontraktu ABI, bootnodes), a informace užitečné pro existujícího člena (adresy validátoru). Tuto standardizaci můžete použít k rozšiřování konsorcia napříč poskytovateli cloudu. Toto rozhraní API vrátí odpověď ve formátu JSON s následující strukturou:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>DApp zásad správného řízení

Na základě špičkového kontrolního úřadu je decentralizované řízení. Vzhledem k tomu, že ověření stavu spoléhá na povolený seznam síťových autorit, aby byla síť v pořádku, je důležité zajistit, aby se v tomto seznamu oprávnění provedly úpravy. Každé nasazení se dodává se sadou inteligentních kontraktů a portálu pro zásady správného řízení tohoto seznamu povolených certifikátů. Jakmile navrhovaná změna dosáhne většiny hlasů členů konsorcia, je tato změna určena. Hlasovací právo umožňuje přidat nebo ohrozit nové účastníky, aby je bylo možné odstranit transparentním způsobem, který podporuje bezchybnou síť.

DApp zásad správného řízení je sada předem nasazených [inteligentních kontraktů](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) a webové aplikace, které se používají k řízení autorit v síti. Autority jsou rozděleny do identit správců a uzlů validátorů.
Správci mají oprávnění delegovat účast na sadě ověřovacích uzlů. Správci mohou také hlasovat jiné správce do sítě nebo z ní.

![DApp zásad správného řízení](./media/ethereum-poa-deployment/governance-dapp.png)

* **Decentralizované řízení:** Změny v síťových autoritách se spravují prostřednictvím hlasování v řetězcích, a to výběrem správce.
* **Delegování ověřovacího modulu:** Autority mohou spravovat své uzly validátoru, které jsou nastaveny v každém nasazení PoA.
* Historie změn, které se mají **Auditovat:** Každá změna se zaznamená na blockchain, která poskytuje transparentnost a audit.

### <a name="getting-started-with-governance"></a>Začínáme se zásadami správného řízení

K provedení libovolného druhu transakcí prostřednictvím DApp zásad správného řízení musíte použít Ethereem peněženku. Nejjednodušším přístupem je použití kapesního prohlížeče, jako je [MetaMask](https://metamask.io); vzhledem k tomu, že tyto inteligentní kontrakty jsou nasazeny v síti, můžete také automatizovat interakce se smlouvou zásad správného řízení.

Po instalaci MetaMask přejděte v prohlížeči na DApp zásad správného řízení.  Adresu URL můžete najít prostřednictvím Azure Portal ve výstupu nasazení.  Pokud nemáte nainstalovanou peněženku v prohlížeči, nebudete moct provádět žádné akce. Můžete ale zobrazit stav správce.  

### <a name="becoming-an-admin"></a>Stane se správcem

Pokud jste prvním členem, který je v síti nasazený, automaticky se stanete správcem a vaše paritní uzly jsou uvedené jako validátory. Pokud se připojujete k síti, musíte se k ní přihlasit jako správce pomocí většiny (větší než 50%). existující sady pro správu. Pokud se rozhodnete, že se nestanete správcem, vaše uzly stále synchronizují a ověřují blockchain; neúčastní se ale v procesu vytváření bloků. Chcete-li zahájit hlasovací proces, který se stane správcem, vyberte možnost **název** a zadejte adresu ethereem a alias.

![Nominace](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Kandidáty

Když vyberete kartu **kandidáti** , zobrazí se vám aktuální sada kandidátních správců.  Jakmile kandidát dostane v rámci současných správců většinu hlasů, kandidát se povýší na správce.  Chcete-li hlasovat o kandidátovi, vyberte řádek a vyberte možnost **hlasovat v**. Pokud změníte svůj názor na hlas, vyberte kandidáta a vyberte možnost zrušit **hlasování**.

![Kandidáty](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Admins

Karta **Správci** zobrazuje aktuální sadu správců a poskytuje možnost hlasovat proti.  Když správce ztratí více než 50% podporu, odeberou se jako správce v síti. Všechny uzly validátoru, které správce vlastní, ztratí stav validátoru a stanou se uzly transakce v síti. Správce může být odebrán z libovolného počtu důvodů; je však až konsorcium k tomu, aby předem souhlasila se zásadami.

![Admins](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validátory

Výběr karty **validátory** zobrazuje aktuálně nasazené uzly parity pro instanci a jejich aktuální stav (typ uzlu). Každý člen konsorcia má v tomto seznamu jinou sadu validátorů, protože toto zobrazení představuje stávajícího nasazeného člena konsorcia. Pokud je instance nově nasazena a nepřidali jste vaše validátory, získáte možnost **Přidat validátory**. Přidáním validátorů se automaticky zvolí sada paritních uzlů s vyrovnáváním zatížení a přiřadí se k sadě validátorů. Pokud jste nasadili více uzlů, než je povolená kapacita, zbývající uzly se stanou uzly transakcí v síti.

Adresa každého validátoru se automaticky přiřadí prostřednictvím [úložiště identit](#identity-store) v Azure.  Pokud uzel přestane být funkční, odvolá svoji identitu, což umožňuje, aby se na svém nasazení uskutečnil jiný uzel. Tento proces zajistí vysokou dostupnost vaší účasti v rámci konsensu.

![Validátory](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Název konsorcia

Název konsorcia může aktualizovat libovolný správce.  Vyberte ikonu ozubeného kolečka v levém horním rohu pro aktualizaci názvu konsorcia.

### <a name="account-menu"></a>Nabídka účet

V pravém horním rohu je váš alias účtu Ethereem a identicon.  Pokud jste správce, máte možnost aktualizovat svůj alias.

![Účet](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Vývoj ethereem<a id="tutorials"></a>

Chcete-li kompilovat, nasazovat a testovat inteligentní kontrakty, je zde několik možností, které můžete vzít v úvahu pro Ethereem vývoj:
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) – vývojové prostředí ethereem na bázi klienta
* [Ethereem Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) – vývojové prostředí pro ethereem založené na prohlížeči a místní

### <a name="compile-deploy-and-execute-smart-contract"></a>Zkompilovat, nasadit a spustit inteligentní kontrakt

V následujícím příkladu vytvoříte jednoduchou inteligentní kontrakt. Truffle můžete použít ke kompilaci a nasazení inteligentních kontraktů do vaší sítě blockchain. Po nasazení zavoláte funkci inteligentního kontraktu prostřednictvím transakce.

#### <a name="prerequisites"></a>Požadavky

* Nainstalujte [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python je potřeba pro Truffle a Web3. Vyberte možnost instalovat, pokud chcete zahrnout Python do vaší cesty.
* Nainstalujte Truffle v 5.0.5 `npm install -g truffle@v5.0.5` . Truffle vyžaduje instalaci několika nástrojů, včetně [Node.js](https://nodejs.org), [Gitu](https://git-scm.com/). Další informace najdete v [dokumentaci k Truffle](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Vytvořit projekt Truffle

Předtím, než budete moci zkompilovat a nasadit inteligentní kontrakt, je nutné vytvořit projekt Truffle.

1. Otevřete příkazový řádek nebo prostředí.
1. Vytvořte složku s názvem `HelloWorld`.
1. Změňte adresář na novou `HelloWorld` složku.
1. Inicializujte nový projekt Truffle pomocí příkazu `truffle init` .

    ![Vytvořit nový projekt Truffle](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Přidání inteligentního kontraktu

Vytvořte své inteligentní kontrakty v podadresáři **kontraktů** projektu Truffle.

1. V `postBox.sol` podadresáři **kontrakty** projektu Truffle vytvořte soubor s názvem.
1. Do **postBox. Sol**přidejte následující kód soliding.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Nasazení inteligentních kontraktů pomocí Truffle

Projekty Truffle obsahují konfigurační soubor pro podrobnosti o připojení k síti blockchain. Upravte konfigurační soubor tak, aby obsahoval informace o připojení pro vaši síť.

> [!WARNING]
> Nikdy Neodesílat privátní klíč Ethereem přes síť. Ujistěte se, že je každá transakce podepsaná místně a že se podepsaná transakce posílá přes síť.

1. Pro účet správce Ethereem, který se [používá při nasazování vaší blockchain sítě](#ethereum-settings), budete potřebovat symbolické fráze. Pokud jste k vytvoření účtu použili MetaMask, můžete načíst klávesové zkratky z MetaMask. Vyberte ikonu účtu správce v pravém horním rohu rozšíření MetaMask a vyberte **nastavení > zabezpečení & ochrany osobních údajů > zobrazit slova typu seed**.
1. Nahraďte obsah `truffle-config.js` v projektu Truffle následujícím obsahem. Nahraďte zástupný koncový bod a symbolické hodnoty.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Vzhledem k tomu, že používáme poskytovatele Truffle HD peněženky, nainstalujte modul do projektu pomocí příkazu `npm install truffle-hdwallet-provider --save` .

Truffle používá skripty pro migraci k nasazení inteligentních kontraktů do sítě blockchain. K nasazení nové inteligentní smlouvy potřebujete skript pro migraci.

1. Přidejte novou migraci pro nasazení nové smlouvy. `2_deploy_contracts.js`V podadresáři **migrace** projektu Truffle vytvořte soubor.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Nasaďte na síť PoA pomocí příkazu Truffle migrace. Na příkazovém řádku v adresáři projektu Truffle spusťte příkaz:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Volání funkce inteligentního kontraktu

Teď, když je vaše inteligentní smlouva nasazená, můžete odeslat transakci pro volání funkce.

1. V adresáři projektu Truffle vytvořte nový soubor s názvem `sendtransaction.js` .
1. Přidejte následující obsah do **sendtransaction.js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Spusťte skript pomocí příkazu Truffle Execute.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Spustit skript pro volání funkce přes transakci](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Podpora WebAssembly (WASM)

Podpora WebAssembly je už povolená na nově nasazených sítích PoA. Umožňuje vývoj inteligentních kontraktů v jakémkoli jazyce, který předává do webového sestavení (Rust, C, C++). Další informace najdete v tématech [Přehled parity WebAssembly](https://wiki.parity.io/WebAssembly-Home) a [tutorial z parity tech](https://github.com/paritytech/pwasm-tutorial) .

## <a name="faq"></a>Časté otázky

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Všimněte si, že síť obsahuje mnoho transakcí, které jsem neodeslal. Odkud pocházejí?

Odemknutí [osobního rozhraní API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)je nezabezpečené. Roboty naslouchat odemčeným účtům Ethereem a pokusit se o vyprázdnění prostředků. Robot předpokládá, že tyto účty obsahují reálný a pokus o odsávání zůstatku. Nepovolujte osobní rozhraní API v síti. Místo toho můžete transakce předem podepsat buď ručně, jako je MetaMask nebo programově.

### <a name="how-to-ssh-onto-a-vm"></a>Jak se SSH na virtuální počítač?

Z bezpečnostních důvodů se port SSH nezveřejňuje. Podle [tohoto průvodce povolte port SSH](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Návody nastavit člena auditu nebo uzly transakcí?

Uzly transakcí jsou sadou paritních klientů, které jsou v síti partnerského vztahu, ale nejsou zapojeny do konsensu. Tyto uzly se pořád dají použít k odeslání transakcí Ethereem a přečtení stavu inteligentního kontraktu. Tento mechanismus funguje pro zajištění auditování pro členy v síti, kteří nejsou samosprávi. Chcete-li to dosáhnout, postupujte podle kroků v části [rozvoj konsorcia](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Proč trvá transakce MetaMask dlouhou dobu?

Aby bylo zajištěno, že transakce jsou přijímány ve správném pořadí, každá transakce Ethereem se dostane k přírůstkové hodnotě nonce. Pokud jste účet v MetaMask použili v jiné síti, je potřeba resetovat hodnotu nonce. Klikněte na ikonu nastavení (tři pruhy), nastavení, Resetování účtu. Historie transakcí se vymaže a teď můžete transakci znovu odeslat.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Potřebuji zadat poplatek za plyn v MetaMask?

Ether neslouží k tomu, aby byl v rámci úřadu ověření platnosti. Proto není nutné zadávat poplatky za plyn při odesílání transakcí v MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Co mám dělat, když se moje nasazení nepovede kvůli chybě při zřizování Azure OMS?

Monitorování je volitelná funkce. Ve výjimečných případech, kdy se nasazení nezdaří kvůli nemožnostem úspěšného zřízení Azure Monitor prostředků, můžete znovu nasadit bez Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Jsou nasazení veřejné IP adresy kompatibilní s nasazeními privátních sítí?

No. Partnerský vztah vyžaduje obousměrnou komunikaci, takže celá síť musí být buď veřejná, nebo soukromá.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Jaká je očekávaná propustnost transakce v rámci autority pro ověření?

Propustnost transakce bude vysoce závislá na typech transakcí a topologii sítě. Při použití jednoduchých transakcí jsme provedli průměrně 400 transakcí za sekundu pomocí sítě nasazené napříč několika oblastmi.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Návody se přihlásit k odběru událostí inteligentních kontraktů?

Ethereem Proofing-of-Authority teď podporuje webové sokety.  Ověřte výstup nasazení a vyhledejte adresu URL a port webového soketu.

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Novinky ke službě Azure blockchain News najdete na [blogu Azure blockchain](https://azure.microsoft.com/blog/topics/blockchain/) , abyste měli přehled o nabídkách služeb blockchain a informacích od týmu Azure blockchain Engineering.

Pokud chcete poskytnout zpětnou vazbu k produktu nebo požádat o nové funkce, vystavte nebo Hlasujte nápad prostřednictvím [fóra Azure Feedback pro blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Podpora komunity

Spolupracujte s odborníky z Microsoftu a komunitou Azure blockchain.

* [Microsoft Q&stránku s otázkou](/answers/topics/azure-blockchain-workbench.html). Technická podpora pro šablony Blockchain je omezená na problémy s nasazením.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Další kroky

Další řešení Azure blockchain najdete v dokumentaci ke [službě Azure blockchain](../index.yml).
