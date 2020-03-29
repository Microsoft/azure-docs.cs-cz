---
title: Nasazení šablony řešení konsorcia Ethereum Proof-of-Authority v Azure
description: Použití řešení konsorcia Ethereum Proof-of-Authority k nasazení a konfiguraci vícečlenné sítě konsorcia Ethereum v Azure
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387505"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Nasazení šablony řešení konsorcia ethereum pro of authority v Azure

Pomocí [šablony řešení Azure preview konsorcia Ethereum Proof-of-Authority Consortium](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) můžete nasadit, nakonfigurovat a řídit vícečlennou síť Ethereum s minimálními znalostmi Azure a Ethereum.

Šablonu řešení může každý člen konsorcia použít k zajištění nároku na blockchainovou síť pomocí výpočetních, síťových a úložných služeb Microsoft Azure. Síťová stopa každého člena konsorcia se skládá ze sady uzlů validátoru s vyrovnáváním zatížení, se kterými může aplikace nebo uživatel pracovat a odesílat transakce Ethereum.

## <a name="choose-an-azure-blockchain-solution"></a>Výběr řešení Azure Blockchain

Než se rozhodnete použít šablonu řešení konsorcia ethereum pro of authority, porovnejte svůj scénář s běžnými případy použití dostupných možností Azure Blockchain.

Možnost | Model služby | Běžný případ použití
-------|---------------|-----------------
Šablony řešení | IaaS | Šablony řešení jsou šablony Azure Resource Manageru, které můžete použít ke zřízení plně nakonfigurované topologie blockchainové sítě. Šablony nasazují a konfigurují výpočetní, síťové a úložné služby Microsoft Azure pro daný typ blockchainové sítě.
[Služba Azure Blockchain](../service/overview.md) | PaaS | Azure Blockchain Service Preview zjednodušuje vytváření, správu a řízení blockchainových sítí konsorcia. Azure Blockchain Service použijte pro řešení vyžadující PaaS, správu konsorcia nebo ochranu osobních údajů a transakcí.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS a PaaS | Azure Blockchain Workbench Preview je kolekce služeb a funkcí Azure, které vám pomůžou vytvářet a nasazovat blockchainové aplikace pro sdílení obchodních procesů a dat s jinými organizacemi. Azure Blockchain Workbench můžete použít k vytváření prototypů blockchainového řešení nebo konceptu blockchainové aplikace.

## <a name="solution-architecture"></a>Architektura řešení

Pomocí šablony řešení Ethereum můžete nasadit vícečlennou síť konsorcia ethereum založené na jedné nebo více oblastech.

![architektura nasazení](./media/ethereum-poa-deployment/deployment-architecture.png)

Každé nasazení členů konsorcia zahrnuje:

* Virtuální počítače pro spuštění validátorů PoA
* Azure Balance Balancer pro distribuci vzdáleného volání procedur, partnerského vztahu a požadavků dapp zásad správného řízení
* Azure Key Vault pro zabezpečení identit validátoru
* Azure Storage pro hostování informací o trvalé síti a koordinaci leasingu
* Azure Monitor pro agregaci protokolů a statistiky výkonu
* Brána virtuální sítě (volitelná) pro povolení připojení VPN napříč privátními virtuálními sítěmi

Ve výchozím nastavení jsou koncové body vzdáleného volání procedur a partnerského vztahu přístupné prostřednictvím veřejné IP adresy, aby bylo možné zjednodušit připojení mezi předplatnými a cloudy. Pro ovládací prvky přístupu na úrovni aplikace můžete použít [smlouvy o oprávnění parity](https://wiki.parity.io/Permissioning). Podporovány jsou sítě nasazené za sítě MINA, které využívají brány virtuální sítě pro připojení mezi předplatnými. Vzhledem k tomu, že nasazení VPN a virtuální sítě jsou složitější, můžete začít s modelem veřejné IP adresy při vytváření prototypů řešení.

Kontejnery Dockeru se používají pro spolehlivost a modularitu. Azure Container Registry se používá k hostování a poskytování bitových kopií s verzí jako součást každého nasazení. Obrázky kontejnerů se skládají z:

* Orchestrator - Generuje identity a smlouvy o správě. Ukládá identity v úložišti identit.
* Parity client - Leases identity from the identity store. Zjišťuje a připojuje se k vrstevníkům.
* EthStats Agent – shromažďuje místní protokoly a statistiky prostřednictvím vzdáleného volání procedur a odesílá informace do Azure Monitoru.
* Zásadsprávné řízení DApp - webové rozhraní pro interakci se smlouvami zásad správného řízení.

### <a name="validator-nodes"></a>Uzly Validátoru

V protokolu proof-of-a-a-uthority, validátor uzly místo tradičních horník uzly. Každý validátor má jedinečnou identitu Ethereum, která mu umožňuje účastnit se procesu vytváření bloků. Každý člen konsorcia může zřídit dva nebo více uzlů validátoru v pěti oblastech pro geografickou redundanci. Uzly Validátoru komunikují s jinými uzly validátoru, aby dospěly ke shodě o stavu podkladové distribuované knihy. Aby byla zajištěna spravedlivá účast v síti, je každému členovi konsorcia zakázáno používat více validátorů než první člen sítě. Například pokud první člen nasadí tři validátory, každý člen může mít pouze tři validátory.

### <a name="identity-store"></a>Úložiště identit

Úložiště identit je nasazeno v předplatném každého člena, které bezpečně obsahuje generované identity Ethereum. Pro každý validátor kontejner orchestrace generuje soukromý klíč Ethereum a ukládá jej do úložiště klíčů Azure.

## <a name="deploy-ethereum-consortium-network"></a>Nasazení sítě konsorcia Ethereum

V této procházce předpokládejme, že vytváříte síť konsorcia ethereum s více stranami. Následující tok je příkladem nasazení více stran:

1. Tři členové každý generovat ethereum účet pomocí MetaMask
1. *Člen A* nasazuje Ethereum PoA, poskytuje jejich ethereum veřejné hod
1. *Člen A* poskytuje adresu URL konsorcia *členu B* a *členu C*
1. *Členské B* a *členské C* nasazení, Ethereum PoA, poskytující jejich Ethereum veřejné adresy a *členské*'s konsorcium URL
1. *Člen A* hlasuje v *členu B* jako administrátor
1. *Člen A* i *člen B* hlasují pro *člena C* jako správce

V dalších částech se zobrazí postup konfigurace stopy prvního člena v síti.

### <a name="create-resource"></a>Vytvoření prostředku

Na [webu Azure Portal](https://portal.azure.com)vyberte **Vytvořit prostředek** v levém horním rohu.

Vyberte **Blockchain** > **Ethereum Proof-of-A-uthority Consortium (preview)**.

### <a name="basics"></a>Základy

V části **Základy**zadejte hodnoty pro standardní parametry pro jakékoli nasazení.

![Základy](./media/ethereum-poa-deployment/basic-blade.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
Vytvoření nové sítě nebo připojení k existující síti | Můžete vytvořit novou síť konsorcia nebo se připojit k již existující síti konsorcia. Připojení k existující síti vyžaduje další parametry. | Vytvořit nový
E-mailová adresa | Po dokončení nasazení obdržíte e-mailové oznámení s informacemi o vašem nasazení. | Platná e-mailová adresa
Uživatelské jméno virtuálního virtuálního uživatele | Uživatelské jméno správce každého nasazeného virtuálního uživatele | 1-64 alfanumerických znaků
Typ ověřování | Metoda ověření na virtuální mši. | Heslo
Heslo | Heslo pro účet správce pro každý z nasazených virtuálních počítačů. Všechny virtuální počítače mají zpočátku stejné heslo. Po zřizování můžete změnit heslo. | 12-72 znaků 
Předplatné | Předplatné, na které se má nasadit síť konsorcia |
Skupina prostředků| Skupina prostředků, do které chcete nasadit síť konsorcia. | myResourceGroup
Umístění | Oblast Azure pro skupinu prostředků. | USA – západ 2

Vyberte **OK**.

### <a name="deployment-regions"></a>Oblasti nasazení

V části *Oblasti nasazení*zadejte počet oblastí a umístění pro každou z nich. Můžete nasadit v maximálně pěti oblastech. První oblast by měla odpovídat umístění skupiny prostředků z části *Základy.* Pro vývojové nebo testovací sítě můžete použít jednu oblast na člena. Pro produkční prostředí nasazujte ve dvou nebo více oblastech pro vysokou dostupnost.

![oblasti nasazení](./media/ethereum-poa-deployment/deployment-regions.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
Počet oblastí|Počet regionů, které mají nasadit síť konsorcia| 2
První region | První oblast pro nasazení sítě konsorcia | USA – západ 2
Druhý region | Druhá oblast pro nasazení sítě konsorcia. Další oblasti jsou viditelné, pokud je počet oblastí dva nebo vyšší. | USA – východ 2

Vyberte **OK**.

### <a name="network-size-and-performance"></a>Velikost a výkon sítě

V části *Velikost a výkon sítě*zadejte vstupy pro velikost sítě konsorcia. Velikost úložiště uzlu validátoru určuje potenciální velikost blockchainu. Velikost lze změnit po nasazení.

![Velikost a výkon sítě](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
Počet uzlů validátoru validátoru s vyrovnáváním zatížení | Počet uzlů validátoru pro zřízení jako součást sítě. | 2
Výkon úložiště uzlů Validator | Typ spravovaného disku pro každý nasazený uzly validátoru. Podrobnosti o cenách najdete v tématu [ceny za úložiště](https://azure.microsoft.com/pricing/details/managed-disks/) | SSD úrovně Standard
Velikost virtuálního počítače uzlu Validator | Velikost virtuálního počítače používaná pro validátorové uzly. Podrobnosti o cenách najdete v tématu [ceny virtuálních strojů](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Standardní D2 v3

Úroveň virtuálního počítače a úložiště ovlivňují výkon sítě.  V následující tabulce můžete vybrat efektivitu nákladů:

SKU virtuálního počítače|Úroveň úložiště|Price|Propustnost|Latence
---|---|---|---|---
F1|SSD úrovně Standard|Nízké|Nízké|high
D2_v3|SSD úrovně Standard|střední|střední|střední
F16s|SSD úrovně Premium|high|high|Nízké

Vyberte **OK**.

### <a name="ethereum-settings"></a>Nastavení etherea

V části *Nastavení etherea*zadejte nastavení konfigurace související s ethereum.

![Nastavení etherea](./media/ethereum-poa-deployment/ethereum-settings.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
ID člena konsorcia | ID přidružené ke každému členovi, který se účastní sítě konsorcia. Používá se ke konfiguraci adresních prostorů IP, aby se zabránilo kolizi. V případě privátní sítě by mělo být ID člena jedinečné v různých organizacích ve stejné síti.  Jedinečné ID členů je potřeba i v případě, že se stejná organizace nasadí do více oblastí. Poznamenejte si hodnotu tohoto parametru, protože je třeba jej sdílet s ostatními členy spojení, abyste zajistili, že nedojde ke kolizi. Platný rozsah je 0 až 255. | 0
ID sítě | ID sítě pro naváděnou síť Ethereum konsorcia. Každá síť Ethereum má své vlastní ID sítě, přičemž 1 je ID pro veřejnou síť. Platný rozsah je 5 až 999 999 999 | 10101010
Adresa správce etherea | Adresa účtu Ethereum používaná pro účast ve správě poa. MetaMask můžete použít ke generování adresy Ethereum. |
Rozšířené možnosti | Rozšířené možnosti pro nastavení etherea | Povolení
Nasazení pomocí veřejné IP adresy | Pokud je vybraná privátní virtuální síť, síť se nasadí za bránu virtuální sítě a odebere přístup k partnerovi. Pro privátní virtuální síť musí všichni členové používat bránu virtuální sítě, aby bylo připojení kompatibilní. | Veřejná IP adresa
Limit blokového plynu | Limit počátečního bloku plynu sítě. | 50000000
Období opětovného zapečetění bloku (s) | Frekvence, s jakou budou vytvořeny prázdné bloky, pokud v síti nejsou žádné transakce. Vyšší frekvence bude mít rychlejší konečnost, ale zvýšené náklady na úložiště. | 15
Smlouva o oprávnění transakce | Bajtkód smlouvy oprávnění k transakci. Omezuje nasazení a provádění inteligentních smluv na povolený seznam účtů Ethereum. |

Vyberte **OK**.

### <a name="monitoring"></a>Monitorování

Monitorování umožňuje konfigurovat prostředek protokolu pro vaši síť. Agent monitorování shromažďuje a zobrazuje užitečné metriky a protokoly z vaší sítě a poskytuje možnost rychle zkontrolovat stav sítě nebo problémy s laděním.

![Azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
Monitorování | Možnost povolení monitorování | Povolení
Připojení k existujícím protokolům Azure Monitoru | Možnost vytvoření nové instance protokolů Azure Monitoru nebo připojení k existující instanci | Vytvořit nový
Umístění | Oblast, kde je nasazena nová instance | USA – východ
Existující ID pracovního prostoru analýzy protokolů (Připojení k existujícím protokolům Azure Monitoru = Připojit se k existujícím)|ID pracovního prostoru existující instance protokolů Azure Monitoru||Není k dispozici
Existující primární klíč analýzy protokolů (Připojení k existujícím protokolům Azure Monitor = Připojit se k existujícímu)|Primární klíč používaný k připojení k existující instanci protokolů Azure Monitoru||Není k dispozici

Vyberte **OK**.

### <a name="summary"></a>Souhrn

Kliknutím na souhrn zkontrolujte zadané vstupy a spusťte základní ověření před nasazením. Před nasazením si můžete stáhnout šablonu a parametry.

Vyberte **Vytvořit** k nasazení.

Pokud nasazení obsahuje brány virtuální sítě, nasazení může trvat 45 až 50 minut.

## <a name="deployment-output"></a>Výstup nasazení

Po dokončení nasazení můžete získat přístup k potřebné parametry pomocí portálu Azure.

### <a name="confirmation-email"></a>Potvrzovací e-mail

Pokud zadáte e-mailovou adresu[(základní část),](#basics)bude odeslán e-mail, který obsahuje informace o nasazení a odkazy na tuto dokumentaci.

![e-mail o nasazení](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portál

Po úspěšném dokončení nasazení a všechny prostředky byly zřízeny, můžete zobrazit výstupní parametry ve skupině prostředků.

1. Přejděte do skupiny prostředků na portálu.
1. Vyberte **možnost Přehled > nasazení**.

    ![Přehled skupiny prostředků](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Vyberte nasazení **microsoft-azure-blockchain.azure-blockchain-ether-....**
1. Vyberte část **Výstupy.**

    ![Výstupy nasazení](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Pěstování konsorcia

Chcete-li rozšířit konsorcium, musíte nejprve připojit fyzickou síť. Pokud nasazujete za VPN, přečtěte si část [Připojení brány virtuální sítě](#connecting-vnet-gateways) nakonfiguruje síťové připojení jako součást nového nasazení členů. Po dokončení nasazení použijte [DApp zásad správného řízení](#governance-dapp) a staňte se správcem sítě.

### <a name="new-member-deployment"></a>Nasazení nového člena

Sdílejte následující informace s členem spojení. Informace se nacházejí v e-mailu po nasazení nebo ve výstupu nasazení portálu.

* Adresa URL dat konsorcia
* Počet nasazených uzlů
* ID prostředku brány virtuální sítě (pokud používáte VPN)

Nasazující člen by měl používat stejnou šablonu řešení konsorcia Ethereum Proof-of-Authority při nasazování jejich přítomnosti v síti pomocí následujících pokynů:

* Vybrat **Spojit existující**
* Zvolte stejný počet uzlů validátoru jako ostatní členové v síti, abyste zajistili spravedlivé zastoupení.
* Použít stejnou adresu Admin Ethereum
* Použití poskytnuté *adresy URL dat konsorcia* v *nastavení etherea*
* Pokud je zbytek sítě za VPN, vyberte **soukromou virtuální síť** v rozšířené části.

### <a name="connecting-vnet-gateways"></a>Připojení bran virtuální sítě

Tato část je vyžadována jenom v případě, že jste nasadili pomocí privátní virtuální sítě. Tuto část můžete přeskočit, pokud používáte veřejné IP adresy.

Pro privátní síť jsou různí členové připojeni přes připojení brány virtuální sítě. Předtím, než se člen může připojit k síti a zobrazit transakční provoz, musí existující člen provést konečnou konfiguraci na své bráně VPN, aby přijal připojení. Uzly etherea spojujícího člena nebudou spuštěny, dokud nebude navázáno připojení. Chcete-li snížit pravděpodobnost jednoho bodu selhání, vytvořte redundantní síťová připojení v konsorciu.

Po nasazení nového člena musí existující člen dokončit obousměrné připojení nastavením připojení brány virtuální sítě k novému členu. Stávající člen potřebuje:

* ResourceID brány virtuální sítě pro připojujícího se člena. Viz [výstup nasazení](#deployment-output).
* Sdílený klíč připojení.

Existující člen musí k dokončení připojení spustit následující skript prostředí PowerShell. Azure Cloud Shell můžete použít na navigačním panelu v pravém horním rohu na portálu.

![cloud prostředí](./media/ethereum-poa-deployment/cloud-shell.png)

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

Portál Azure Monitor můžete vyhledat buď podle odkazu v e-mailu nasazení nebo vyhledáním parametru ve výstupu nasazení [OMS_PORTAL_URL].

Portál nejprve zobrazí statistiku sítě na vysoké úrovni a přehled uzlů.

![Kategorie monitorů](./media/ethereum-poa-deployment/monitor-categories.png)

Výběr **přehledu uzlu** zobrazí statistiky infrastruktury pro uzdy.

![Statistiky uzlů](./media/ethereum-poa-deployment/node-stats.png)

Výběrem **statistiky sítě** zobrazíte statistiky sítě Ethereum.

![Síťové statistiky](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Můžete dotaz protokoly monitorování prozkoumat selhání nebo nastavení prahovou hodnotu výstrahy. Následující dotazy jsou příklady, které lze spustit v nástroji *pro vyhledávání protokolů:*

Seznam bloků, které byly hlášeny více než jeden dotaz validátoru může být užitečné najít vytesávky řetězce.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Získejte průměrný počet rovnocenných partnerů pro zadaný uzel validátoru v průměru za 5 minut.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>Přístup SSH

Z bezpečnostních důvodů je přístup k portu SSH ve výchozím nastavení odepřen pravidlem zabezpečení skupiny sítě. Chcete-li získat přístup k instancím virtuálních strojů v síti PoA, je třeba změnit následující zabezpečení je pravidlo *povolit*.

1. Přejděte do části **Přehled** nasazené skupiny prostředků na webu Azure Portal.

    ![ssh přehled](./media/ethereum-poa-deployment/ssh-overview.png)

1. Vyberte **skupinu zabezpečení sítě** pro oblast virtuálního aplikace, ke které chcete mít přístup.

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Vyberte pravidlo **allow-ssh.**

    ![ssh-povolit](./media/ethereum-poa-deployment/ssh-allow.png)

1. Změnit **akci,** která má **být povolena**

    ![ssh povolit povolit](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Vyberte **Uložit**. Použití změn může trvat několik minut.

Můžete vzdáleně připojit k virtuálním počítačům pro validátor uzly přes SSH s poskytnutým admin uživatelské jméno a heslo / SSH klíč. Příkaz SSH pro přístup k prvnímu uzlu validátoru je uveden ve výstupu nasazení šablony. Například:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Chcete-li se dostat k dalším uzlem transakcí, zintáěte číslo portu o jednu.

Pokud jste nasadili do více než jedné oblasti, změňte příkaz na název DNS nebo IP adresu v ykreli zatížení v této oblasti. Chcete-li najít název DNS nebo adresu IP ostatních oblastí, vyhledejte prostředek s konvencí ** \* \* \* \* \*pojmenování -lbpip-reg\# ** a zobrazte jeho vlastnosti názvu DNS a adresy IP.

## <a name="azure-traffic-manager-load-balancing"></a>Vyrovnávání zatížení Azure Traffic Manageru

Azure Traffic Manager může pomoci snížit prostoje a zlepšit odezvu sítě PoA směrováním příchozího provozu napříč několika nasazeními v různých oblastech. Integrované kontroly stavu a automatické přesměrování pomáhají zajistit vysokou dostupnost koncových bodů vzdáleného volání procedur a aplikace DApp pro zásady správného řízení. Tato funkce je užitečná, pokud jste nasadili do více oblastí a jsou připraveni na produkční prostředí.

Pomocí traffic manageru můžete zlepšit dostupnost sítě PoA pomocí automatického převzetí služeb při selhání. Traffic Manager můžete také použít ke zvýšení odezvy sítí směrováním koncových uživatelů do umístění Azure s nejnižší latencí sítě.

Pokud se rozhodnete vytvořit profil traffic manageru, můžete pro přístup k síti použít název DNS profilu. Jakmile ostatní členové konsorcia byly přidány do sítě, Traffic Manager lze také použít k vyrovnávání zatížení napříč jejich nasazených validátorů.

### <a name="creating-a-traffic-manager-profile"></a>Vytvoření profilu traffic manageru

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Vytvořit prostředek** v levém horním rohu.
1. Vyhledejte **profil traffic manageru**.

    ![Hledání Azure Traffic Manageru](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Pojmenujte profil jedinečným názvem a vyberte skupinu prostředků, která byla použita pro nasazení PoA.

1. Vyberte **Vytvořit** k nasazení.

    ![Vytvořit Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Po nasazení vyberte instanci ve skupině prostředků. Název DNS pro přístup k správci provozu naleznete na kartě Přehled.

    ![Vyhledání dns správce provozu](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Zvolte kartu **Koncové body** a vyberte tlačítko **Přidat.**
1. Zadejte koncový bod jedinečný název.
1. V **popřípadě Cílový typ prostředku**zvolte **Veřejná IP adresa**.
1. Zvolte veřejnou IP adresu prvního vykladače zatížení první oblasti.

    ![Správce provozu směrování](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Opakujte pro každou oblast v nasazené síti. Jakmile jsou koncové body ve **stavu povoleno,** jsou automaticky načíst a oblast vyvážená na název DNS správce provozu. Nyní můžete použít tento název DNS namísto [CONSORTIUM_DATA_URL] parametr v jiných krocích článku.

## <a name="data-api"></a>Data API

Každý člen konsorcia hostuje potřebné informace pro ostatní připojit k síti. Chcete-li povolit snadné připojení, každý člen hostuje sadu informací o připojení na koncovém bodu rozhraní API dat.

Existující člen poskytuje [CONSORTIUM_DATA_URL] před nasazením člena. Po nasazení člen spojení načte informace z rozhraní JSON v následujícím koncovém bodě:

`<CONSORTIUM_DATA_URL>/networkinfo`

Odpověď obsahuje informace užitečné pro připojení členů (Genesis blok, Validator Set smlouvy ABI, bootnodes) a informace užitečné pro stávající člen (adresy validátoru). Tuto standardizaci můžete použít k rozšíření konsorcia napříč poskytovateli cloudu. Toto rozhraní API vrátí odpověď ve formátu JSON s následující strukturou:

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

## <a name="governance-dapp"></a>Řízení DApp

Jádrem důkazu autority je decentralizovaná správa věcí veřejných. Vzhledem k tomu, že proof of authority spoléhá na povolený seznam síťových úřadů, aby byla síť v pořádku, je důležité poskytnout spravedlivý mechanismus pro provádění změn v tomto seznamu oprávnění. Každé nasazení je dodáván o sadu inteligentních smluv a portál pro řízení v řetězci tohoto seznamu povolených. Jakmile navrhovaná změna dosáhne většinového hlasování členů konsorcia, je změna přijata. Hlasování umožňuje, aby byli noví účastníci konsensu přidáni nebo kompromitována, aby byli transparentním způsobem odstraněni, což podporuje poctivou síť.

DApp zásad správného řízení je sada předem nasazených [inteligentních smluv](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) a webové aplikace, které se používají k řízení autorit v síti. Autority jsou rozděleny do identit správce a uzly validátoru.
Správci mají pravomoc delegovat účast na základě konsensu na sadu uzlů validátoru. Správci mohou také hlasovat pro jiné správce do nebo ze sítě.

![Řízení DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Decentralizovaná správa věcí veřejných:** Změny v síťových orgánech jsou spravovány prostřednictvím hlasování v řetězci vybranými správci.
* **Delegace validátoru:** Úřady mohou spravovat své uzly validátoru, které jsou nastaveny v každém nasazení PoA.
* **Auditovatelná historie změn:** Každá změna je zaznamenána na blockchainu a poskytuje transparentnost a auditovatelnost.

### <a name="getting-started-with-governance"></a>Začínáme se zásadou správného řízení

Chcete-li provádět jakýkoli druh transakcí prostřednictvím dapp zásad správného řízení, musíte použít peněženku Ethereum. Nejjednodušší přístup je použít peněženku v prohlížeči, jako je [MetaMask](https://metamask.io); však vzhledem k tomu, že tyto inteligentní smlouvy jsou nasazeny v síti můžete také automatizovat interakce smlouvy zásad správného řízení.

Po instalaci MetaMask, přejděte na zásadsprávné řízení DApp v prohlížeči.  Adresu URL můžete najít prostřednictvím portálu Azure ve výstupu nasazení.  Pokud nemáte nainstalovanou peněženku v prohlížeči, nebudete moci provádět žádné akce. můžete však zobrazit stav správce.  

### <a name="becoming-an-admin"></a>Stát se správcem

Pokud jste první člen, který byl nasazen v síti, pak se automaticky stanete správcem a vaše paritní uzly jsou uvedeny jako validátory. Pokud se připojujete k síti, musíte se nechat získat jako správce většinou (více než 50%) stávající sady správců. Pokud se rozhodnete, že se nestanete správcem, vaše uzly stále synchronizují a ověřují blockchain; však neúčastní procesu vytváření bloků. Chcete-li zahájit proces hlasování a stát se správcem, vyberte **Nominujte** a zadejte adresu a alias etherea.

![Nominace](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Kandidáty

Výběrem karty **Kandidáti** se zobrazí aktuální sada kandidátů administrátorů.  Jakmile kandidát dosáhne většinového hlasování současnými administrátory, kandidát bude povýšen na administrátora.  Chcete-li hlasovat o kandidátovi, vyberte řádek a vyberte **možnost Hlasovat v .** Pokud si to rozmyslíte při hlasování, vyberte **kandidáta**a vyberte Možnost Zrušit hlasování .

![Kandidáty](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Admins

Karta **Správci** zobrazuje aktuální sadu správců a umožňuje hlasovat proti.  Jakmile správce ztratí více než 50% podporu, jsou odebrány jako správce v síti. Všechny uzly validátoru, které vlastní správce ztratí stav validátoru a stanou se transakčními uzly v síti. Správce může být odebrán z libovolného počtu důvodů. je však na konsorciu, aby se předem dohodlo na politice.

![Admins](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validátory

Výběrem karty **Validátory** se zobrazí aktuální nasazené uzly parity pro instanci a jejich aktuální stav (typ uzlu). Každý člen konsorcia má v tomto seznamu jinou sadu validátorů, protože toto zobrazení představuje aktuálního nasazeného člena konsorcia. Pokud je instance nově nasazená a nepřidali jste validátory, získáte možnost **Přidat validátory**. Přidání validátorů automaticky vybere regionálně vyváženou sadu uzlů parity a přiřadí je k vaší sadě validátorů. Pokud jste nasadili více uzlů, než je povolená kapacita, zbývající uzly se stanou transakčními uzly v síti.

Adresa každého validátoru se automaticky přiřazuje prostřednictvím [úložiště identit](#identity-store) v Azure.  Pokud uzel přejde dolů, vzdá se své identity, povolení jiného uzlu ve vašem nasazení převzít jeho místo. Tento proces zajišťuje, že vaše konsensuální účast je vysoce dostupná.

![Validátory](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Název konsorcia

Každý správce může aktualizovat název konsorcia.  Vlevém horním rohu vyberte ikonu ozubeného kola a aktualizujte název konsorcia.

### <a name="account-menu"></a>Nabídka Účet

Vpravo nahoře je váš alias účtu Ethereum a identicon.  Pokud jste správce, máte možnost aktualizovat svůj alias.

![Účet](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Vývoj etherea<a id="tutorials"></a>

Chcete-li zkompilovat, nasadit a otestovat inteligentní smlouvy, zde je několik možností, které můžete zvážit pro vývoj etherea:
* [Lanýžsuite](https://www.trufflesuite.com/docs/truffle/overview) - Klient-založené ethereum vývojové prostředí
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - Vývojové prostředí založené na prohlížeči a místní ethereum

### <a name="compile-deploy-and-execute-smart-contract"></a>Kompilace, nasazení a spuštění inteligentního kontraktu

V následujícím příkladu vytvoříte jednoduchou inteligentní smlouvu. Lanýže používáte ke kompilaci a nasazení inteligentnísmlouvy do vaší blockchainové sítě. Po nasazení zavoláte funkci inteligentní smlouvy prostřednictvím transakce.

#### <a name="prerequisites"></a>Požadavky

* Nainstalujte [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python je potřeba pro Lanýže a Web3. Vyberte možnost instalace, chcete-li zahrnout Python do cesty.
* Nainstalujte Lanýž v5.0.5 `npm install -g truffle@v5.0.5`. Lanýž vyžaduje několik nástrojů, které mají být instalovány, včetně [Node.js](https://nodejs.org), [Git](https://git-scm.com/). Další informace naleznete v [dokumentaci k lanýžům](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Vytvořit projekt Lanýž

Než budete moci sestavit a nasadit inteligentní smlouvu, musíte vytvořit projekt Lanýžů.

1. Otevřete příkazový řádek nebo prostředí.
1. Vytvořte složku s názvem `HelloWorld`.
1. Změňte adresář `HelloWorld` na novou složku.
1. Inicializovat nový projekt Lanýžů pomocí příkazu `truffle init`.

    ![Vytvořte nový projekt Lanýž](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Přidání inteligentní smlouvy

Vytvořte si chytré smlouvy v podadresáři **smluv** vašeho projektu Lanýž.

1. Vytvořte soubor v `postBox.sol` pojmenovaném podadresáři **smluv** projektu Lanýž.
1. Do **postBox.sol**přidejte následující kód Solidity .

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

### <a name="deploy-smart-contract-using-truffle"></a>Nasaďte inteligentní smlouvu pomocí lanýžů

Lanýžové projekty obsahují konfigurační soubor pro podrobnosti o připojení k blockchainové síti. Upravte konfigurační soubor tak, aby obsahoval informace o připojení pro vaši síť.

> [!WARNING]
> Nikdy neposílejte svůj soukromý klíč Ethereum přes síť. Ujistěte se, že každá transakce je podepsána místně první a podepsaná transakce je odeslána po síti.

1. Potřebujete mnemotechnické fráze pro [účet správce Etherea, který se používá při nasazování blockchainové sítě](#ethereum-settings). Pokud jste použili MetaMask k vytvoření účtu, můžete načíst mnemotechnické pomůcky z MetaMask. Vyberte ikonu účtu správce v pravém horním rohu rozšíření MetaMask a vyberte **Nastavení > zabezpečení & ochrana osobních údajů > odhalit zasívací slova**.
1. Nahraďte `truffle-config.js` obsah v projektu Lanýž následujícím obsahem. Nahraďte zástupný koncový bod a mnemotechnické hodnoty.

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

1. Vzhledem k tomu, že používáme poskytovatele Lanýžové HD `npm install truffle-hdwallet-provider --save`Peněženky, nainstalujte modul ve vašem projektu pomocí příkazu .

Lanýž používá migrační skripty k nasazení inteligentních kontraktů do blockchainové sítě. K nasazení nové inteligentní smlouvy potřebujete skript pro migraci.

1. Přidejte novou migraci k nasazení nové smlouvy. Vytvořte `2_deploy_contracts.js` soubor v podadresáři **migrace** projektu Lanýž.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Nasaďte se do sítě PoA pomocí příkazu Lanýž migrate. Na příkazovém řádku v adresáři projektu Lanýž spusťte:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Volání inteligentní smluvní funkce

Teď, když je vaše inteligentní smlouva nasazena, můžete odeslat transakci k volání funkce.

1. V adresáři projektu Lanýž vytvořte `sendtransaction.js`nový soubor s názvem .
1. Přidejte následující obsah **sendtransaction.js**.

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

1. Spusťte skript pomocí příkazu Lanýž spustit.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Spuštění skriptu pro volání funkce prostřednictvím transakce](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Podpora pro WebAssembly (WASM)

Podpora webové sestavy je pro vás již povolena v nově nasazených sítích PoA. Umožňuje vývoj inteligentních kontraktů v libovolném jazyce, který se převádí do webové hospůdky (Rust, C, C++). Další informace naleznete v [tématu: Parity Přehled websestavy](https://wiki.parity.io/WebAssembly-Home) a [výuky z Parity Tech](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>Nejčastější dotazy

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Všiml jsem si, že existuje mnoho transakcí v síti, které jsem neposlal. Odkud se to tu zvát?

Je nejisté odemknout [osobní API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Boti poslouchají odemčené ethereum účty a snaží se vyčerpat finanční prostředky. Bot předpokládá, že tyto účty obsahují real-éter a pokusit se být první, kdo odsát zůstatek. Nepovolujte osobní rozhraní API v síti. Místo toho předem podepište transakce buď ručně pomocí peněženky, jako je MetaMask, nebo programově.

### <a name="how-to-ssh-onto-a-vm"></a>Jak SSH na Virtuální počítač?

Port SSH není z bezpečnostních důvodů vystaven. [Chcete-li povolit port SSH, postupujte podle této příručky](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Jak nastavím člena auditu nebo uzly transakcí?

Transakční uzly jsou sada klientů parity, kteří jsou partnerovití se sítí, ale neúčastní se konsensu. Tyto uzly lze stále použít k odeslání transakcí ethereum a čtení stavu inteligentní smlouvy. Tento mechanismus funguje pro poskytování auditovatelnosti členům konsorcia, kteří nejsou orgány, v síti. Chcete-li toho dosáhnout, postupujte podle kroků v [části Růst konsorcia](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Proč transakce MetaMask trvá dlouho?

Chcete-li zajistit, aby transakce byly přijaty ve správném pořadí, každá transakce Ethereum přichází s přírůstkem nonce. Pokud jste použili účet v MetaMask v jiné síti, musíte obnovit hodnotu nonce. Klikněte na ikonu nastavení (tři pruhy), Nastavení, Obnovit účet. Historie transakcí bude vymazána a nyní můžete transakci znovu odeslat.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Musím v MetaMasku specifikovat poplatek za plyn?

Ether neslouží účelu v konsorciu proof-of-a-autority. Proto není nutné specifikovat poplatek za plyn při odesílání transakcí v MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Co mám dělat, když se moje nasazení nezdaří z důvodu selhání zřídit Azure OMS?

Monitorování je volitelná funkce. V některých výjimečných případech, kdy se vaše nasazení nezdaří z důvodu neschopnosti úspěšně zřídit prostředky Azure Monitor, můžete znovu nasadit bez Azure Monitoru.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Jsou veřejná nasazení IP kompatibilní s nasazením privátní sítě?

Ne. Partnerský vztah vyžaduje obousměrnou komunikaci, takže celá síť musí být veřejná nebo soukromá.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Jaká je očekávaná propustnost transakce proof-of-authority?

Propustnost transakce bude vysoce závislá na typech transakcí a topologii sítě. Pomocí jednoduchých transakcí jsme srovnávají v průměru 400 transakcí za sekundu se sítí nasazenou ve více oblastech.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Jak se přihlásím k odběru chytrých smluvních událostí?

Ethereum Proof-of-Authority nyní podporuje webové zásuvky.  Zkontrolujte výstup nasazení a vyhledejte adresu URL webového soketu a port.

## <a name="next-steps"></a>Další kroky

Další řešení Azure Blockchain najdete v [dokumentaci k Azure Blockchain](https://docs.microsoft.com/azure/blockchain/).
