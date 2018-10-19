---
title: Analýza provozu Azure | Dokumentace Microsoftu
description: Naučte se analyzovat protokoly toků skupin zabezpečení sítě Azure analýzu provozu.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: yagup;jdial
ms.openlocfilehash: 83f90de11077731ac6532733fb30e610b40e4b2e
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409692"
---
# <a name="traffic-analytics"></a>Analýza provozu

Analýza provozu je cloudové řešení, která poskytuje přehled o aktivitě uživatelů a aplikací v cloudových sítích. Analýza provozu analyzuje protokoly toku Network Watcher sítě zabezpečení skupiny (NSG) poskytnout přehled o toku provozu ve vašem cloudu Azure. Analýza provozu vám umožňuje:

- Vizualizace aktivity sítě ve vašich předplatných Azure a identifikace hotspotů.
- Identifikovat ohrožení zabezpečení a zabezpečte svoji síť, informace, jako je otevření portů, když se aplikace pokoušejí přístup k Internetu a připojení k podvodné sítě virtuálních počítačů (VM).
- Pochopit vzory v toku provozu mezi oblastmi Azure a Internetem pro optimalizaci vašeho nasazení sítě pro výkon a kapacitu.
- Odhalte chybné konfigurace sítě, což vede k selhání připojení ve vaší síti.

## <a name="why-traffic-analytics"></a>Proč traffic analytics?

Je důležité monitorovat, spravovat a vědět, neohrožený zabezpečení, dodržování předpisů a výkonu vaší vlastní sítě. Znalost svého vlastního prostředí je k ochraně a optimalizaci ho prvořadý význam. Často musíte znát aktuální stav sítě, který se připojuje, kde se připojujete, které porty jsou otevřené k Internetu, chování očekávané sítě, sítě nestandardní chování, a i s náhlými úspornou v provozu.

Cloudové sítě jsou jiné než v místním podnikovým sítím, kde máte netflow nebo ekvivalentní protokol podporující směrovače a přepínače, které obsahují schopnost shromažďovat IP síťového provozu, zadá nebo ukončí síťové rozhraní. Díky analýze provozu toku dat, můžete vytvořit analýzu toku provozu sítě a objem.

Virtuální sítě Azure mají protokoly toků NSG, které vám poskytnou informace o příchozího přenosu dat a odchozí provoz IP přes skupinu zabezpečení sítě přidružené k jednotlivým síťovým rozhraním, virtuální počítače nebo podsítí. Analýzy nezpracovaných protokolů toku NSG a vkládání intelligence zabezpečení, topologie a místa, traffic analytics vám může poskytnout přehled o toku provozu ve vašem prostředí. Analýza provozu obsahuje informace, například nejvíce komunikujících hostitelích, nejvíce komunikující protokoly aplikací, páry nejvíce konverzující hostitele, povoleno/zablokováno provoz, odchozího/příchozího provozu, porty otevřené Internetu, nejvíce blokující pravidla, provoz distribuce podle datového centra Azure, virtuální sítě, podsítě, nebo podvodné sítě.

## <a name="key-components"></a>Klíčové komponenty

- **Skupina zabezpečení sítě (NSG)**: obsahuje seznam pravidel zabezpečení, která povolují nebo odpírají síťový provoz prostředků připojených k virtuální síti Azure. Skupiny zabezpečení sítě můžou být přidružené k podsítím, jednotlivým virtuálním počítačům (klasický model) nebo jednotlivým síťovým rozhraním (síťovým kartám) připojeným k virtuálním počítačům (Resource Manager). Další informace najdete v tématu [přehled skupin zabezpečení sítě](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Zabezpečení skupiny (NSG) protokolů síťového toku**: umožňuje zobrazit informace o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě. NSG flow protokoly jsou napsané ve formátu json a zobrazení odchozí a příchozí toků na základě pravidel na že nic toku se vztahuje na pět řazené kolekce členů informace o toku (zdrojová a cílová IP adresa, zdrojový/cílový port a protokol) a pokud byl povolený přenos nebo zakázaná. Další informace o protokoly toků NSG najdete v tématu [protokolů toku NSG](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: Služba Azure, která shromažďuje data monitorování a ukládá data v centrálním úložišti. Tato data můžou obsahovat události, údaje o výkonu nebo vlastní data poskytovaná prostřednictvím rozhraní API služby Azure. Po získání jsou data dostupná pro výstrahy, analýzu a export. Monitorování aplikací, jako je monitorování a provoz Analýza výkonu sítě se vytvářejí pomocí Log Analytics jako základ. Další informace najdete v tématu [Log analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Pracovní prostor log analytics**: instance služby log analytics, které jsou uložena data vztahující se k účtu Azure. Další informace o pracovních prostorech log analytics najdete v tématu [vytvořit pracovní prostor Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v Azure. Můžete zapnout protokolů toku NSG a vypnout pomocí služby Network Watcher. Další informace najdete v tématu [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Jak funguje traffic analytics

Analýza provozu prozkoumá nezpracovaných protokolů toku NSG a zachytí sníženou protokoly na základě agregace běžných toků mezi stejné zdrojové IP adresy, cílová IP adresa, cílový port a protokol. Například hostitel 1 (IP adresa:. 10.10.10.10) komunikaci s hostiteli 2 (IP adresa: 10.10.20.10), než 100krát po dobu 1 hodinu používání portu (například 80) a protokolu (třeba http). Snížení protokol má jednu položku, které hostitele 1 a 2 hostitele předávány 100krát po dobu 1 hodiny pomocí portu *80* a protokol *HTTP*, namísto nutnosti 100 položek. Snížení protokoly jsou navíc zeměpisné oblasti, zabezpečení a informace o topologii a pak uloženy v pracovním prostoru log analytics. Následující obrázek znázorňuje tok dat:

![Tok dat pro zpracování protokolů toku NSG](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Podporované oblasti

Analýza provozu můžete použít pro skupiny zabezpečení sítě v některém z následujících oblastí: střed USA – Západ, USA – východ, USA – východ 2, střed USA – sever, střed USA – Jih, střed USA, západní USA, západní USA 2, západní Evropa, Severní Evropa, Velká Británie – Západ, Velká Británie – Jih, Austrálie – východ, Austrálie – jihovýchod, a Jihovýchodní Asie. Pracovní prostor log analytics, musí existovat v na západní USA, střed USA, východní USA, západní Evropa, Velká Británie – Jih, Austrálie – jihovýchod nebo jihovýchodní Asie.

## <a name="prerequisites"></a>Požadavky

### <a name="user-access-requirements"></a>Požadavky na přístup uživatele

Váš účet musí být členem jedné z následujících Azure [předdefinované role](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Model nasazení   | Role                   |
|---------          |---------               |
|Resource Manager   | Vlastník                  |
|                   | Přispěvatel            |
|                   | Čtenář                 |
|                   | Přispěvatel sítě    |
|Classic            | Správce účtu  |
|                   | Správce služeb  |
|                   | Spolusprávce       |

Pokud váš účet nemá přiřazenou do jedné z předdefinovaných rolí, musíte být přiřazeni k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , který je přiřazen následující akce na úrovni předplatného:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"

Informace o tom, jak kontrolovat přístupová oprávnění uživatelů najdete v tématu [nejčastější dotazy k analýze provozu](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Povolení Network Watcheru

Analýza provozu, musíte mít existující network watcheru nebo [povolit network watcher](network-watcher-create.md) v jednotlivých oblastech, které mají skupiny zabezpečení sítě, které chcete analyzovat provoz pro. Analýza provozu se dá nastavit pro skupiny zabezpečení sítě hostovaná v některém z [podporované oblasti](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Znovu zaregistrujte poskytovatele prostředků sítě

Než budete moct použít analýzu provozu, je nutné znovu zaregistrovat poskytovatele prostředků sítě. Klikněte na tlačítko **vyzkoušet** do následujícího okna kódu a otevřete Azure Cloud Shell. Cloud Shell automaticky zaznamenává je do ke svému předplatnému Azure. Jakmile službě Cloud Shell se otevře, zadejte následující příkaz pro opětovné zaregistrování poskytovatel síťových prostředků:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Vyberte skupinu zabezpečení sítě

Před povolením protokolování toku NSG, musí mít k protokolování toků pro skupiny zabezpečení sítě. Pokud nemáte k dispozici skupinu zabezpečení sítě, přečtěte si téma [vytvořte skupinu zabezpečení sítě](../virtual-network/manage-network-security-group.md#create-a-network-security-group) k jejímu vytvoření.

Na levé straně webu Azure portal, vyberte **monitorování**, pak **Network watcher**a pak vyberte **protokolů toku NSG**. Vyberte skupinu zabezpečení sítě, které chcete povolit protokolu toku NSG, jak je znázorněno na následujícím obrázku:

![Výběr skupin zabezpečení sítě, které vyžadují povolení protokolů toku NSG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Pokud se pokusíte k povolení analýzy provozu pro NSG, která je hostována v libovolné oblasti, než [podporované oblasti](#supported-regions), se zobrazí chyba "Nebyl nalezen".

## <a name="enable-flow-log-settings"></a>Povolit nastavení protokolů toku

Než povolíte nastavení protokolu toku, je nutné dokončit následující úkoly:

Zaregistrujte poskytovatele Azure Insights, když je ještě není zaregistrované pro vaše předplatné:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Pokud ještě nemáte účet Azure Storage k ukládání toku NSG přihlásí, musíte vytvořit účet úložiště. Pomocí následujícího příkazu můžete vytvořit účet úložiště. Před spuštěním příkazu, nahraďte `<replace-with-your-unique-storage-account-name>` s názvem, který je jedinečný ve všech umístěních Azure, mezi 3 až 24 znaků a používat pouze číslice a malá písmena. V případě potřeby můžete také změnit název skupiny prostředků.

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Vyberte následující možnosti, jak je znázorněno na obrázku:

1. Vyberte *na* pro **stav**
2. Vyberte existující účet úložiště pro ukládání protokolů toku v. Pokud chcete uložit data navždy, nastavte hodnotu na *0*. Se vám účtovat žádné poplatky za úložiště Azure pro účet úložiště.
3. Nastavte **uchování** na počet dní, které chcete uložit data.
4. Vyberte *na* pro **Traffic Analytics stav**.
5. Vyberte existující pracovní prostor Log Analytics nebo **vytvořit nový pracovní prostor** vytvořit nový certifikát. Pracovní prostor Log Analytics používá k ukládání agregované a indexaci dat, která se pak použije k vygenerování analýzy analýzu provozu. Pokud vyberete existující pracovní prostor, musí existovat v jednom z [podporované oblasti](#traffic-analytics-supported-regions) a se upgradovaly na nový dotazovací jazyk. Pokud nechcete, aby existující pracovní prostor, nebo nemáte pracovní prostor v podporované oblasti, vytvořte novou. Další informace o dotazovací jazyky, naleznete v tématu [Azure Log Analytics upgradovat na nové prohledávání protokolů](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    Pracovní prostor log analytics hostování řešení pro analýzu provozu a skupiny zabezpečení sítě, nemusí být ve stejné oblasti. Například možná analýza provozu v pracovním prostoru v oblasti západní Evropa, dokud máte skupiny zabezpečení sítě v oblastech východní USA a západní USA. Více skupin zabezpečení sítě je možné nakonfigurovat ve stejném pracovním prostoru.
6. Vyberte **Uložit**.

    ![Výběr účtu úložiště, pracovního prostoru Log Analytics a povolení analýzy provozu](./media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

Opakujte předchozí kroky pro žádné jiné skupiny zabezpečení sítě pro které chcete povolit analýzu provozu pro. Data z protokolů toku se odesílají do pracovního prostoru, zajistěte proto, že místní zákony a předpisy ve vaší zemi povolit ukládání dat v oblasti, ve kterém existuje pracovní prostor.

Můžete taky nakonfigurovat pomocí analýzy provozu [Set-AzureRmNetworkWatcherConfigFlowLog](/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog) rutiny prostředí PowerShell v modul AzureRm PowerShell verze 6.2.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` nainstalovanou verzi zjistíte. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="view-traffic-analytics"></a>Zobrazení analýzy provozu

Na levé straně portálu, vyberte **všechny služby**, zadejte *monitorování* v **filtr** pole. Když **monitorování** se zobrazí ve výsledcích hledání vyberte ji. Chcete-li začít prozkoumávat analýzu provozu a její možnosti, vyberte **Network watcher**, pak **analýzu provozu**.

![Přístup k řídicím panelu analýzy provozu](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Řídicím panelu může trvat až 30 minut se zobrazí poprvé, protože analýza provozu musí nejprve agregovat dostatek dat, aby se odvodit smysluplné přehledy, než může také generovat žádné sestavy.

## <a name="usage-scenarios"></a>Scénáře použití

Některé z informací, které můžete chtít získat po plně je nakonfigurovaná Analýza provozu, jsou následující:

### <a name="find-traffic-hotspots"></a>Vyhledání provozu hotspotům

**Hledat**

- Které hostitelé, podsítí a virtuálních sítí jsou odesílání nebo přijímání největším provozem, procházení maximální škodlivý provoz a blokování významné toky?
    - Zkontrolujte srovnávací grafu pro hostitele, podsíť a virtuální sítě. Principy, které hostitele, podsítí a virtuálních sítí se odesílá nebo přijímá nejvíce provozu mohou pomoci při identifikaci hostitele, které zpracovávají nejvíce provoz a určuje, zda je správně proveden distribuce provozu.
    - Můžete vyhodnotit, jestli objem provozu je vhodné pro hostitele. Je objem přenosů normálního chování nebo se ho si zasloužila další šetření?
- Kolik odchozího/příchozího provozu je k dispozici?
    -   Hostitel má více příchozí provoz než odchozí nebo naopak?
- Statistiky provozu blokované.
    - Proč hostitele blokuje významný objem neškodný provoz? Toto chování vyžaduje další šetření a pravděpodobně optimalizace konfigurace
- Statistiky škodlivého provozu, kterým povoleno/zablokováno
    - Proč hostitele přijímá škodlivý provoz a proč je povolené toky ze škodlivých zdroje? Toto chování vyžaduje další šetření a pravděpodobně optimalizace konfigurace.

    Vyberte **zobrazit všechny**v části **hostitele**, jak je znázorněno na následujícím obrázku:

    ![Hostitel s podrobnostmi o většině provoz připraveném řídicím panelu](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Následující obrázek ukazuje čas vzroste zájem o pět talking hostitelé a podrobnosti týkající se flow (povolené – příchozí/odchozí a odepření – příchozí/odchozí toky) pro hostitele:

    ![Pěti hlavních komunikujících většinu hostitele trend](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Hledat**

- Které jsou nejvíce konverzující hostitele páry?
    - Očekávané chování jako front-end nebo back-end komunikaci nebo nestandardní chování, stejně jako back-end internetový provoz.
- Statistika provozu povoleno/zablokováno
    - Proč je hostitele povolení nebo blokování následek výrazné navýšení provozu svazku
- Nejčastěji se využívá protokol aplikace mezi většina dvojice konverzující hostitele:
    - Jsou povolené tyto aplikace v této síti?
    - Jsou aplikace správně nakonfigurovaná? Používají se pro komunikaci o vhodném protokolu? Vyberte **zobrazit všechny** pod **časté konverzace**, jak vidět na následujícím obrázku:

        ![Nejčastější konverzace připraveném řídicím panelu](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Následující obrázek ukazuje čas trendy pro hlavní pět konverzace a tok související podrobnosti, jako povolené a zakázané příchozí a odchozí toky pro pár konverzace:

    ![Podrobnosti o výřečných konverzace pěti hlavních a trendů](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Hledat**

- Protokol, který aplikace se nejčastěji používá ve vašem prostředí a které konverzující hostitele páry používáte nejvíce aplikačního protokolu?
    - Jsou povolené tyto aplikace v této síti?
    - Jsou aplikace správně nakonfigurovaná? Používají se pro komunikaci o vhodném protokolu? Očekávané chování je běžné porty, jako je například 80 a 443. Pokud se nezobrazí žádné neobvyklé porty, se pro standard komunikaci, můžou vyžadovat změnu konfigurace. Vyberte **zobrazit všechny** pod **port aplikace**, na následujícím obrázku:

        ![Hlavní protokoly aplikací připraveném řídicím panelu](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Následující obrázky zobrazit čas vytvoření trendu pro hlavních 5 protokolů L7 a podrobnosti související s toku (třeba povolené a zakázané toky) pro protokol L7:

    ![Začátek pět vrstvy 7 podrobnosti protokoly a trendů](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Tok podrobností protokolu aplikace v prohledávání protokolu](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Hledat**

- Trendy využití kapacity brány sítě VPN ve vašem prostředí.
    - Každý skladové položky VPN umožňuje určité velikosti šířky pásma. Málo využitá bran VPN Gateway?
    - Jsou vaše brány dosažení kapacity? By měl upgradovat na další vyšší skladovou Položku?
- Které jsou nejvíce konverzující hostitelé, přes které bránu VPN přes port, který?
    - Tento model je normální? Vyberte **zobrazit všechny** pod **VPN gateway**, jak je znázorněno na následujícím obrázku:

        ![Hlavní aktivní připojení VPN připraveném řídicím panelu](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Následující obrázek ukazuje čas sledování trendů využití kapacity služby Azure VPN Gateway a podrobnosti týkající se flow (například povolenými toky a porty):

    ![VPN gateway trendů a flow podrobnosti o využití](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Vizualizace provozu distribuce podle zeměpisné oblasti

**Hledat**

- Distribuce provozu na datové centrum, jako je hlavní zdroje přenosů do datového centra, horní podvodný sítě rozhovory s datového centra a horní rozhovory protokoly aplikací.
    - Pokud zjistíte další zatížení datového centra, můžete naplánovat pro distribuci efektivní provoz.
    - Pokud podvodný sítě jsou rozhovory v datovém centru, potom pravidla skupiny zabezpečení sítě k blokování, je opravte.

    Vyberte **zobrazení mapy** pod **prostředí**, jak je znázorněno na následujícím obrázku:

    ![Distribuce provozu předvádí řídicí panel](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Geografické mapy ukazuje na horním pásu karet pro výběr parametrů, jako je například datová centra (s povolenou analýzou nasazeno/No nasazení/aktivní/neaktivní/provozu/není s povolenou analýzou provozu) a země nepřispívají k provozu Benign/škodlivé na aktivní nasazení:

    ![Zobrazení geografické mapy připraveném aktivní nasazení](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Geografické mapy ukazuje distribuce provozu do datového centra z jiných zemí a kontinentech komunikace k tomu modrý (neškodný provoz) a červený (škodlivý provoz) vybarvenými řádky:

    ![Zobrazení geografické mapy připraveném distribuce provozu do jiných zemí a kontinenty](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Podrobností o distribuci provozu při prohledávání protokolů toku](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Vizualizujte distribuce provozu virtuálních sítí

**Hledat**

- Distribuce provozu na virtuální síť, topologie, hlavní zdroje přenosů do virtuální sítě, sítě nejvyšší podvodný rozhovory do virtuální sítě a horní rozhovory protokoly aplikací.
    - Znalost, virtuální síť, ve které je rozhovory do virtuální sítě. Pokud se očekává, konverzace, můžete opravit.
    - Pokud podvodný sítě jsou rozhovory s virtuální sítí, můžete opravit pravidla NSG pro blokování podvodný sítě.
 
    Vyberte **zobrazení virtuálních sítí** pod **prostředí**, jak je znázorněno na následujícím obrázku:

    ![Distribuce virtuální sítě připraveném řídicím panelu](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Virtuální síťové topologie ukazuje na horním pásu karet pro výběr parametrů, jako je virtuální sítě (přenos mezi virtuálními sítěmi virtuální síť připojení/aktivní nebo neaktivní), externí připojení, aktivní toky a škodlivé toky ve virtuální síti.
- Virtuální síťové topologie znázorňuje distribuce provozu do virtuální sítě s ohledem na toků (povolené nebo blokované/příchozí/odchozí/Benign/škodlivé), aplikační protokol a skupin zabezpečení sítě, například:

    ![Virtuální síťové topologie, která ukazuje podrobnosti o distribuci a tok provozu](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)

    ![Tok podrobnosti pro distribuci provozu virtuální sítě v prohledávání protokolu](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Hledat**

- Distribuce na podsíť, topologie, hlavní zdroje přenosů pro podsíť, provoz sítě nejvyšší podvodný rozhovory podsítě a horní rozhovory protokoly aplikací.
    - Znalost, které podsíť je rozhovory které podsíti. Pokud se zobrazí neočekávaná konverzace, můžete opravit konfiguraci.
    - Pokud podvodný sítě jsou rozhovory s podsítí, budete moct opravit tím, že nakonfigurujete pravidla NSG pro blokování podvodný sítě.
- Topologie podsítě zobrazuje horním pásu karet pro výběr parametry, například podsítě, externí připojení, aktivní toky a škodlivé toky podsítě aktivní nebo neaktivní.
- Topologie podsítě zobrazuje distribuce provozu do virtuální sítě s ohledem na toků (povolené nebo blokované/příchozí/odchozí/Benign/škodlivé), aplikační protokol a skupin zabezpečení sítě, například:

    ![Topologie podsítě připraveném distribuce provozu podsítě virtuální sítě s ohledem na toků](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Hledat**

Distribuce provozu na aplikační brány a nástroje pro vyrovnávání zatížení, topologie, hlavní zdroje přenosů, horní podvodné sítě rozhovory Application gateway a nástroje pro vyrovnávání zatížení a horní rozhovory protokoly aplikací. 
    
 - Znalost, které podsíť je rozhovory pro které služba Application gateway nebo nástroje pro vyrovnávání zatížení. Pokud zjistíte neočekávané konverzace, můžete opravit konfiguraci.
 - Pokud podvodný sítě jsou rozhovory s Application gateway nebo nástroje pro vyrovnávání zatížení, budete moct opravit tím, že nakonfigurujete pravidla NSG pro blokování podvodný sítě. 

    ![Subnet-Topology-showcasing-Traffic-Distribution-to-a-Application-Gateway-Subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Zobrazení portů a virtuální počítače přijímají data z Internetu

**Hledat**

- Otevřít porty jsou rozhovory přes internet?
    - Pokud jsou nalezeny neočekávané porty otevřít, můžete opravit konfiguraci:

    ![Připraveném řídicím panelu porty příjem a odesílání provozu do Internetu](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Podrobnosti o hostitelů a cílové porty Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Hledat**

Máte ve svém prostředí škodlivý provoz? Kde je pocházející z? Pokud je určena k?

![Škodlivý provoz toky podrobností v prohledávání protokolu](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Vizualizace trendů v přístupů pravidla skupiny zabezpečení sítě nebo NSG

**Hledat**

- Která pravidla skupiny zabezpečení sítě nebo NSG obsahovat většina přístupů srovnávací grafu rozdělení toky?
- Jaké jsou hlavní dvojice konverzace zdrojových a cílových podle pravidel skupiny zabezpečení sítě nebo NSG?

    ![Skupina zabezpečení sítě připraveném řídicím panelu narazí statistiky](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Následující obrázky zobrazit čas vytvoření trendu pro uplatnění pravidel NSG a podrobnosti o toku zdroj cíl pro skupinu zabezpečení sítě:

    - Rychle zjistit, které skupiny zabezpečení sítě a skupiny zabezpečení sítě jsou pravidla procházení škodlivé toky a které jsou hlavní škodlivé IP adresy, přístup k vašemu cloudovému prostředí
    - Identifikace, která pravidla skupiny zabezpečení sítě nebo NSG se povolení/blokování významné síťového provozu
    - Vyberte horní filtry pro detailní kontrolu skupiny zabezpečení sítě nebo NSG pravidla

    ![Která ukazuje čas vytvoření trendu pro uplatnění pravidla NSG a hlavní pravidla NSG](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Začátek NSG pravidla statistiky podrobností v prohledávání protokolu](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Pokud chcete získat odpovědi na nejčastější dotazy, naleznete v tématu [nejčastější dotazy k analýze provozu](traffic-analytics-faq.md).
