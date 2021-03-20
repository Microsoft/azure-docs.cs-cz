---
title: Nasazení šablony řešení Ethereem pro kontrolu pravopisu pro účely úřadu v Azure
description: Nasazení a konfigurace Ethereemé sítě konsorcia Ethereem pro více členů v Azure pomocí řešení pro kontrolu pravopisu
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 70c9498bae9117585963e111bea4f1e127cab232
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097937"
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

Ve výchozím nastavení jsou koncové body RPC a peering přístupné přes veřejnou IP adresu, aby bylo možné zjednodušené připojení mezi předplatnými a cloudy. Pro řízení přístupu na úrovni aplikace můžete použít [kontrakty oprávnění parity](https://openethereum.github.io/Permissioning.html). Podporují se sítě nasazené za sítě VPN, které využívají brány virtuální sítě pro připojení mezi předplatnými. Vzhledem k tomu, že nasazení VPN a virtuálních sítí je složitější, možná budete chtít při vytváření prototypů řešení začít s modelem veřejné IP adresy.

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
1. *Členské B* a *členské C* nasazují ethereem POA a poskytují svou ethereem veřejnou adresu a adresu URL *člena* a 's Consortium.
1. *Člen A* hlasy *člena B* jako správce
1. *Člen a* a *člen B* – hlas *členů C* jako správce

V dalších částech se dozvíte, jak nakonfigurovat nastavení prvního člena v síti.

### <a name="create-resource"></a>Vytvoření prostředku

V [Azure Portal](https://portal.azure.com)v levém horním rohu vyberte **vytvořit prostředek** .

Vyberte **blockchain**  >  **ethereem pro kontrolu-of-Authority Consortium (Preview)**.

### <a name="basics"></a>Základy

V části **základy** zadejte hodnoty pro standardní parametry pro jakékoli nasazení.

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

V části *oblasti nasazení* určete počet oblastí a umístění pro každý z nich. Nasazení můžete nasadit v maximálně pěti oblastech. První oblast by se měla shodovat s umístěním skupiny prostředků z části *základy* . Pro vývojové nebo testovací sítě můžete použít jednu oblast na člena. Pro produkční prostředí nasaďte ve dvou nebo více oblastech pro zajištění vysoké dostupnosti.

![oblasti nasazení](./media/ethereum-poa-deployment/deployment-regions.png)

Parametr | Popis | Příklad hodnoty
----------|-------------|--------------
Počet oblastí|Počet oblastí pro nasazení sítě konsorcia| 2
První oblast | První oblast pro nasazení sítě konsorcia | Západní USA 2
Druhá oblast | Druhá oblast pro nasazení sítě konsorcia. Další oblasti jsou viditelné, pokud je počet oblastí 2 nebo vyšší. | USA – východ 2

Vyberte **OK**.

### <a name="network-size-and-performance"></a>Velikost a výkon sítě

V části *velikost a výkon sítě* zadejte vstupy pro velikost sítě konsorcia. Velikost úložiště uzlu validátoru určuje potenciální velikost blockchain. Velikost může být změněna po nasazení.

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

V části *Nastavení ethereem* zadejte nastavení konfigurace související s ethereem.

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
Existující ID pracovního prostoru Log Analytics (připojit k existujícím protokolům Azure Monitor = připojit existující)|ID pracovního prostoru existující instance protokolu Azure Monitor||NA
Existující primární klíč Log Analytics (Připojte se k existujícím protokolům Azure Monitor = připojit existující)|Primární klíč, který se používá pro připojení ke stávající instanci Azure Monitorch protokolů||NA

Vyberte **OK**.

### <a name="summary"></a>Souhrn

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

## <a name="support-and-feedback"></a>Podpora a zpětná vazba<a id="tutorials"></a>

Novinky ke službě Azure blockchain News najdete na [blogu Azure blockchain](https://azure.microsoft.com/blog/topics/blockchain/) , abyste měli přehled o nabídkách služeb blockchain a informacích od týmu Azure blockchain Engineering.

Pokud chcete poskytnout zpětnou vazbu k produktu nebo požádat o nové funkce, vystavte nebo Hlasujte nápad prostřednictvím [fóra Azure Feedback pro blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Podpora komunity

Spolupracujte s odborníky z Microsoftu a komunitou Azure blockchain.

* [Microsoft Q&stránku s otázkou](/answers/topics/azure-blockchain-workbench.html). Technická podpora pro šablony Blockchain je omezená na problémy s nasazením.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Další kroky

Další řešení Azure blockchain najdete v dokumentaci ke [službě Azure blockchain](../index.yml).
