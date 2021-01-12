---
title: Analýza provozu Azure | Microsoft Docs
description: Naučte se analyzovat protokoly toku skupin zabezpečení sítě Azure pomocí analýzy provozu.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.reviewer: vinigam
ms.custom: references_regions
ms.openlocfilehash: 6cd1965ab51e7a7bbcc65836383000f0773b9b82
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070930"
---
# <a name="traffic-analytics"></a>Analýza provozu

Analýza provozu je cloudové řešení, které poskytuje přehled o aktivitách uživatelů a aplikací v cloudových sítích. Analýza provozu analyzuje protokoly toku Network Watcher NSG (Network Security Group), které poskytují přehled o toku provozu v cloudu Azure. Analýza provozu vám umožní:

- Vizualizujte síťovou aktivitu napříč předplatnými Azure a Identifikujte aktivní body.
- Identifikujte bezpečnostní hrozby a zabezpečte svou síť pomocí informací, jako jsou otevřené porty, aplikace pokoušející se o přístup k Internetu a virtuální počítače, které se připojují k neautorizovaným sítím.
- Pochopení toků toků provozu napříč oblastmi Azure a internetem k optimalizaci nasazení sítě pro výkon a kapacitu
- Pinpoint chybné konfigurace sítě, což vede k neúspěšným připojením ve vaší síti.

> [!NOTE]
> Analýza provozu teď podporuje shromažďování dat protokolů toku NSG s vyšší frekvencí 10 minut.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Proč Analýza provozu?

Je důležité monitorovat, spravovat a znát vaši vlastní síť, aby nedošlo k ohrožení zabezpečení, dodržování předpisů a výkonu. Znalost vlastního prostředí má pro ochranu a optimalizaci nejdůležitější důležitost. Často potřebujete znát aktuální stav sítě, který se připojuje, odkud se připojují, které porty jsou otevřené v Internetu, očekávané chování sítě, nepravidelné síťové chování a náhlé zvýšení provozu.

Cloudové sítě se liší od místních podnikových sítí, kde máte NetFlow nebo ekvivalentní směrovače a přepínače podporující protokol, které poskytují schopnost shromažďovat síťový provoz protokolu IP při vstupu nebo ukončení síťového rozhraní. Analýzou dat toku provozu můžete vytvořit analýzu toku a objemu síťových přenosů.

Virtuální sítě Azure mají protokoly toku NSG, které poskytují informace o příchozím a odchozím provozu IP přes skupinu zabezpečení sítě přidruženou k jednotlivým síťovým rozhraním, virtuálním počítačům nebo podsítím. Díky analýze nezpracovaných protokolů toku NSG a vkládání informací o zabezpečení, topologii a zeměpisné oblasti vám Analýza provozu může poskytovat přehledy o toku provozu ve vašem prostředí. Analýza provozu poskytuje informace, jako jsou například většina komunikujících hostitelů, většina komunikujících aplikačních protokolů, Většina párů hostitelů konverzující, povolených/blokovaných přenosů, příchozí/odchozí provoz, otevřené internetové porty, většina pravidel blokování, distribuce provozu na datové centrum Azure, virtuální síť, podsítě nebo neoprávněné sítě.

## <a name="key-components"></a>Klíčové komponenty

- **Skupina zabezpečení sítě (NSG)**: obsahuje seznam pravidel zabezpečení, která povolují nebo zakazují síťový provoz pro prostředky připojené k Azure Virtual Network. Skupiny zabezpečení sítě můžou být přidružené k podsítím, jednotlivým virtuálním počítačům (klasický model) nebo jednotlivým síťovým rozhraním (síťovým kartám) připojeným k virtuálním počítačům (Resource Manager). Další informace najdete v tématu [Přehled skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Protokoly toku pro skupinu zabezpečení sítě (NSG)**: umožňuje zobrazit informace o příchozím a odchozím provozu IP přes skupinu zabezpečení sítě. Protokoly toku NSG jsou zapsané ve formátu JSON a zobrazují odchozí a příchozí toky na základě jednotlivých pravidel. síťové rozhraní, ke kterému se tok vztahuje, obsahuje pět informací o toku (zdrojová nebo cílová IP adresa, zdrojový nebo cílový port a protokol) a pokud byl provoz povolený nebo zakázaný. Další informace o protokolech toku NSG najdete v tématu [protokoly toků NSG](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: Služba Azure, která shromažďuje data monitorování a ukládá je do centrálního úložiště. Tato data můžou zahrnovat události, data o výkonu nebo vlastní data poskytovaná prostřednictvím rozhraní API Azure. Po získání jsou data dostupná pro výstrahy, analýzu a export. Monitorování aplikací, jako je například sledování výkonu sítě a analýza provozu, je sestaveno pomocí protokolu Azure Monitor jako základní. Další informace najdete v tématu [protokoly Azure monitor](../azure-monitor/log-query/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics pracovní prostor**: instance Azure monitor protokolů, kde se ukládají data týkající se účtu Azure. Další informace o Log Analytics pracovních prostorech najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v Azure. Protokoly NSG Flow můžete zapnout a vypnout pomocí Network Watcher. Další informace najdete v tématu [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Jak funguje Analýza provozu

Analýza provozu prověřuje protokoly nezpracovaných toků NSG Flow a zachycuje omezené protokoly agregací běžných toků mezi stejnou zdrojovou IP adresou, cílovou IP adresou, cílovým portem a protokolem. Například hostitel 1 (IP adresa: 10.10.10.10), který komunikuje s hostitelem 2 (IP adresa: 10.10.20.10), 100 krát v období 1 hodiny, a to pomocí portu (například 80) a protokolu (například http). Snížený protokol obsahuje jednu položku, kterou hostitel 1 & hostitel 2 komunikoval 100 krát za dobu 1 hodiny pomocí portu *80* a protokolu *http*, místo aby bylo nutné 100 zadávat položky. Menší protokoly se zvyšují pomocí geografických, bezpečnostních a topologických informací a pak se ukládají do Log Analyticsho pracovního prostoru. Tok dat znázorňuje následující obrázek:

![Tok dat pro zpracování protokolů toku NSG](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Podporované oblasti: NSG 

Analýzu provozu pro skupin zabezpečení sítě můžete použít v kterékoli z následujících podporovaných oblastí:
:::row:::
   :::column span="":::
      Austrálie – střed  
      Austrálie – východ  
      Austrálie – jihovýchod  
      Brazil South  
      Střední Kanada  
      Kanada – východ  
      Indie – střed  
      Střední USA  
      Čína – východ 2  
      Čína – sever 2  
   :::column-end:::
   :::column span="":::
      Východní Asie  
      East US  
      USA – východ 2  
      Východní USA 2 EUAP  
      Francie – střed  
      Japonsko – východ  
      Japonsko – západ  
      Jižní Korea – střed  
      Jižní Korea – jih  
      USA – středosever  
   :::column-end:::
   :::column span="":::
      Severní Evropa  
      Jižní Afrika – sever  
      Středojižní USA  
      Indie – jih  
      Southeast Asia  
      Švýcarsko – sever  
      Švýcarsko – západ  
      Spojené království – jih  
      Spojené království – západ  
      USGov Arizona  
   :::column-end:::
   :::column span="":::
      USGov Texas  
      USGov Virginie  
      USNat východ  
      USNat – západ  
      USSec východ  
      USSec – západ  
      USA – středozápad  
      West Europe  
      USA – západ  
      Západní USA 2  
   :::column-end:::
:::row-end:::

## <a name="supported-regions-log-analytics-workspaces"></a>Podporované oblasti: Log Analytics pracovní prostory

Pracovní prostor Log Analytics musí existovat v následujících oblastech:
:::row:::
   :::column span="":::
      Austrálie – střed  
      Austrálie – východ  
      Austrálie – jihovýchod  
      Brazil South  
      Střední Kanada  
      Indie – střed  
      Střední USA  
      Čína – východ 2  
      Východní Asie  
      East US  
   :::column-end:::
   :::column span="":::
      USA – východ 2  
      Východní USA 2 EUAP  
      Francie – střed  
      Německo – středozápad  
      Japan East  
      Jižní Korea – střed  
      USA – středosever  
      Severní Evropa  
      Jižní Afrika – sever  
      Středojižní USA  
   :::column-end:::
   :::column span="":::
      Southeast Asia  
      Švýcarsko – sever  
      Švýcarsko – západ  
      Spojené arabské emiráty – střed  
      Spojené království – jih  
      Spojené království – západ  
      USGov Arizona  
      USGov Virginie  
      USNat východ  
      USNat – západ  
   :::column-end:::
   :::column span="":::
      USSec východ  
      USSec – západ  
      USA – středozápad  
      West Europe  
      USA – západ  
      Západní USA 2  
   :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Požadavky

### <a name="user-access-requirements"></a>Požadavky na přístup uživatelů

Váš účet musí být členem jedné z následujících [předdefinovaných rolí Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Model nasazení   | Role                   |
|---------          |---------               |
|Resource Manager   | Vlastník                  |
|                   | Přispěvatel            |
|                   | Čtenář                 |
|                   | Přispěvatel sítě    |

Pokud váš účet není přiřazený k jedné z předdefinovaných rolí, musí být přiřazený k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , která je přiřazená k následujícím akcím na úrovni předplatného:

- "Microsoft. Network/applicationGateways/Read"
- "Microsoft. Network/Connections/Read"
- "Microsoft. Network/loadBalancers/Read"
- "Microsoft. Network/localNetworkGateways/Read"
- "Microsoft. Network/networkInterfaces/Read"
- "Microsoft. Network/networkSecurityGroups/Read"
- "Microsoft. Network/publicIPAddresses/Read"
- "Microsoft. Network/routeTables/Read"
- "Microsoft. Network/virtualNetworkGateways/Read"
- "Microsoft. Network/virtualNetworks/Read"
- "Microsoft. Network/expressRouteCircuits/Read"

Informace o tom, jak kontrolovat přístupová oprávnění uživatelů, najdete v tématu [Nejčastější dotazy k analýze provozu](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Povolení Network Watcheru

K analýze provozu potřebujete existující sledovací proces sítě nebo [Povolit sledovací proces sítě](network-watcher-create.md) v každé oblasti, které jste skupin zabezpečení sítě, pro který chcete analyzovat provoz. Pro skupin zabezpečení sítě hostované v některé z [podporovaných oblastí](#supported-regions-nsg)je možné povolit analýzu provozu.

### <a name="select-a-network-security-group"></a>Vybrat skupinu zabezpečení sítě

Než povolíte protokolování toku NSG, musíte mít skupinu zabezpečení sítě, která bude protokolovat toky pro. Pokud nemáte skupinu zabezpečení sítě, přečtěte si téma [Vytvoření skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md#create-a-network-security-group) a vytvořte ji.

V Azure Portal přejít na **sledovací proces sítě** a pak vyberte **protokoly toku NSG**. Vyberte skupinu zabezpečení sítě, pro kterou chcete povolit protokol NSG flow, jak je znázorněno na následujícím obrázku:

![Výběr skupin zabezpečení sítě, který vyžaduje povolení protokolu toku NSG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Pokud se pokusíte povolit analýzu provozu pro NSG, která je hostovaná v jiné oblasti než [podporované oblasti](#supported-regions-nsg), zobrazí se chyba "Nenalezeno".

## <a name="enable-flow-log-settings"></a>Povolit nastavení protokolu toku

Než povolíte nastavení protokolu flow, musíte provést následující úlohy:

Zaregistrujte poskytovatele služby Azure Insights, pokud ještě není u vašeho předplatného zaregistrovaný:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Pokud ještě nemáte účet Azure Storage k uložení protokolů toku NSG v nástroji, musíte vytvořit účet úložiště. Účet úložiště můžete vytvořit pomocí příkazu, který následuje. Před spuštěním příkazu nahraďte `<replace-with-your-unique-storage-account-name>` názvem, který je jedinečný v rámci všech umístění Azure, mezi 3-24 znaků, a to pomocí jenom číslic a malých písmen. V případě potřeby můžete také změnit název skupiny prostředků.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Vyberte následující možnosti, jak je znázorněno na obrázku:

1. Vybrat *pro* **stav**
2. Vyberte *verzi 2* pro **protokol toků verze**. Verze 2 obsahuje statistiku relací toků (bajty a pakety).
3. Vyberte existující účet úložiště, do kterého se budou ukládat protokoly toku. Ujistěte se, že ve vašem úložišti není nastavená možnost Data Lake Storage Gen2 hierarchický obor názvů Enabled na hodnotu true.
4. Nastavte dobu **uchování** na počet dní, pro které chcete ukládat data. Pokud chcete data ukládat trvale, nastavte hodnotu na *0*. Za účet úložiště se účtují Azure Storage poplatky. 
5. Pro **Analýza provozu stav** vyberte *zapnuto* .
6. Vyberte interval zpracování. Na základě vašeho výběru budou protokoly toků shromažďovány z účtu úložiště a zpracovány Analýza provozu. Můžete zvolit interval zpracování každé 1 hodiny nebo každých 10 minut. 
7. Vyberte pracovní prostor existující Log Analytics (OMS) nebo vyberte **vytvořit nový pracovní prostor** a vytvořte nový. Log Analytics pracovní prostor používá Analýza provozu k ukládání agregovaných a indexovaných dat, která se pak používají ke generování analýz. Pokud vyberete existující pracovní prostor, musí existovat v některé z [podporovaných oblastí](#supported-regions-log-analytics-workspaces) a byl upgradován na nový dotazovací jazyk. Pokud nechcete upgradovat existující pracovní prostor nebo nemáte pracovní prostor v podporované oblasti, vytvořte nový. Další informace o jazycích dotazů naleznete v tématu [Azure Log Analytics upgrade na nové prohledávání protokolu](../azure-monitor/log-query/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>Pracovní prostor Log Analytics hostující řešení pro analýzu provozu a skupin zabezpečení sítě nemusí být ve stejné oblasti. Například můžete mít analýzu provozu v pracovním prostoru v oblasti Západní Evropa, zatímco jste si možná skupin zabezpečení sítěi Východní USA a Západní USA. Ve stejném pracovním prostoru se dá nakonfigurovat víc skupin zabezpečení sítě.

8. Vyberte **Uložit**.

    ![Výběr účtu úložiště, Log Analytics pracovního prostoru a povolení Analýza provozu](./media/traffic-analytics/ta-customprocessinginterval.png)

Předchozí kroky opakujte pro všechny ostatní skupin zabezpečení sítě, pro které chcete povolit analýzu provozu pro. Data z protokolů toků se odesílají do pracovního prostoru, takže zajistěte, aby místní zákony a předpisy ve vaší zemi nebo oblasti povolovaly ukládání dat v oblasti, kde pracovní prostor existuje. Pokud jste pro různé skupin zabezpečení sítě nastavili různé intervaly zpracování, budou se data shromažďovat v různých intervalech. Například: můžete povolit interval zpracování 10 minut pro kritické virtuální sítě a 1 hodinu pro Nekritická virtuální sítě.

Analýzu provozu můžete nakonfigurovat taky pomocí rutiny [set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) prostředí PowerShell v Azure PowerShell. Pokud `Get-Module -ListAvailable Az` chcete najít nainstalovanou verzi, spusťte příkaz. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Zobrazit analýzu provozu

Pokud chcete zobrazit Analýza provozu, vyhledejte **Network Watcher** na panelu hledání na portálu. Když jste v Network Watcher prozkoumali analýzu provozu a její možnosti, vyberte v nabídce vlevo možnost **Analýza provozu** . 

![Přístup k řídicímu panelu Analýza provozu](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

První zobrazení řídicího panelu může trvat až 30 minut, protože Analýza provozu musí nejdřív agregovat dostatek dat, aby bylo možné odvodit smysluplné poznatky, než může vygenerovat jakékoli sestavy.

## <a name="usage-scenarios"></a>Scénáře použití

Některé přehledy, které můžete chtít získat po úplné konfiguraci Analýza provozu, jsou následující:

### <a name="find-traffic-hotspots"></a>Najít provozní hotspoty

**Hledat**

- Které hostitele, podsítě a virtuální sítě odesílají nebo dostávají nejvíc přenosů, přecházejí mezi maximálním škodlivým provozem a blokují významné toky?
    - Ověřte srovnávací graf pro hostitele, podsíť a virtuální síť. Porozumění hostitelům, podsítím a virtuálním sítím odesíláte nebo přijímáte většinu přenosů, které vám pomůžou identifikovat hostitele, kteří zpracovávají většinu přenosů, a to, jestli se distribuce provozu správně dokončila.
    - Můžete vyhodnotit, jestli je objem přenosů vhodný pro hostitele. Znamená to, že se jedná o objem normálního provozního provozu nebo o další šetření?
- Kolik příchozích a odchozích přenosů?
    -   Očekává se, že hostitel obdrží více příchozích přenosů, než je odchozí, nebo naopak?
- Statistika blokovaného provozu.
    - Proč hostitel blokuje značný objem neškodných přenosů? Toto chování vyžaduje další šetření a pravděpodobně optimalizaci konfigurace.
- Statistika škodlivého povoleného/blokovaného provozu
  - Proč hostitel přijímá škodlivý provoz a proč jsou povoleny toky ze škodlivých zdrojů? Toto chování vyžaduje další šetření a pravděpodobně optimalizaci konfigurace.

    V části **hostitel** vyberte **Zobrazit vše**, jak je znázorněno na následujícím obrázku:

    ![Hostitel předvádí na řídicím panelu s největším objemem podrobností o provozu](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Následující obrázek znázorňuje časový trend pro prvních pět komunikujících hostitelů a podrobné informace o toku (povolené – příchozí/odchozí a zakázané příchozí nebo odchozí toky) pro hostitele:

    ![Nejoblíbenější trend hostitele s nejčastějšími rozhovory](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Hledat**

- Které jsou konverzující dvojice hostitelů?
    - Očekávalo se chování, jako je například komunikace front-end nebo back-end, nebo nepravidelný způsob, jako je back-end internetový přenos.
- Statistika povoleného a blokovaného provozu
    - Proč hostitel povoluje nebo blokuje významný objem provozu
- Nejčastěji používaný aplikační protokol mezi největší páry hostitelů konverzující:
    - Jsou tyto aplikace v této síti povoleny?
    - Jsou aplikace správně nakonfigurovány? Používají odpovídající protokol pro komunikaci? V části **častá konverzace** vyberte Zobrazit **vše** , jak je znázorněno na následujícím obrázku:

        ![Nejčastější konverzace předvádí na řídicím panelu](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Následující obrázek znázorňuje čas trendu pěti nejoblíbenějších konverzací a podrobné informace o toku, například povolené a zakázané příchozí a odchozí toky pro pár konverzací:

    ![Pět hlavních podrobností konverzace s podrobnostmi a trendem](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Hledat**

- Který aplikační protokol se ve vašem prostředí nejvíce používá a jaké páry hostitelů konverzující používají aplikační protokol nejvíc?
    - Jsou tyto aplikace v této síti povoleny?
    - Jsou aplikace správně nakonfigurovány? Používají odpovídající protokol pro komunikaci? Očekávané chování jsou běžné porty, například 80 a 443. Pokud se pro standardní komunikaci zobrazují nějaké neobvyklé porty, může to vyžadovat změnu konfigurace. V části **port aplikace** vyberte **Zobrazit vše** a na následujícím obrázku:

        ![Hlavní aplikační protokoly předvádí řídicího panelu](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Následující obrázky znázorňují trendy času pro pět protokolů L7 a podrobnosti související s tokem (například povolené a zakázané toky) pro protokol L7:

    ![Podrobnosti a trend hlavních pěti protokolů vrstvy 7](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Podrobnosti o toku pro aplikační protokol v hledání v protokolu](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Hledat**

- Trendy využití kapacity u brány VPN ve vašem prostředí.
    - Každá SKU sítě VPN umožňuje určité množství šířky pásma. Jsou brány VPN nevyužité?
    - Dosahují vaše brány kapacitu? Měli byste upgradovat na další vyšší SKU?
- Které jsou konverzující hostitelé přes kterou bránu VPN, přes který port?
    - Je tento vzor normální? V části **Brána sítě VPN** vyberte **Zobrazit vše** , jak je znázorněno na následujícím obrázku:

        ![Předvádí hlavních aktivních připojení VPN řídicího panelu](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Následující obrázek znázorňuje čas trendu využití kapacity VPN Gateway Azure a podrobností souvisejících s tokem (například povolené toky a porty):

    ![Trend využití brány VPN a podrobnosti toku](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Vizualizace distribuce provozu geograficky

**Hledat**

- Distribuce provozu na datové centrum, jako jsou například hlavní zdroje provozu do datového centra, hlavní neautorizovaných sítí konverzující s datovým centrem a hlavními konverzující protokoly aplikací.
  - Pokud budete v datovém centru sledovat více zátěží, můžete naplánovat efektivní distribuci provozu.
  - Pokud se v datovém centru konverzující podvodné sítě, pak správné pravidla NSG zablokuje jejich blokování.

    Ve **svém prostředí** vyberte **Zobrazit mapu** , jak je znázorněno na následujícím obrázku:

    ![Distribuce provozu předvádí na řídicím panelu](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Geografické mapování zobrazuje horní pás karet pro výběr parametrů, jako jsou datová centra (nasazené/bez nasazení/aktivní/neaktivní/Analýza provozu povoleno Analýza provozu/Nepovoleno), a země nebo oblasti přispívající k aktivnímu nasazení neškodný/škodlivý provoz:

    ![Zobrazení geografického mapování předvádí aktivní nasazení](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Geografické mapování ukazuje distribuci přenosů dat do datového centra ze zemí nebo oblastí a kontinentů, které na něj budou v modré (neškodné přenosy) a červené (škodlivý provoz) barevné linky:

    ![Zobrazení geografického mapování předvádí distribuci provozu do zemí/oblastí a kontinentů](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Podrobnosti o toku pro distribuci provozu v hledání v protokolu](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Vizualizace distribuce provozu pomocí virtuálních sítí

**Hledat**

- Distribuce provozu na virtuální síť, topologii, hlavní zdroje provozu do virtuální sítě, hlavní neautorizovaných sítí konverzující do virtuální sítě a hlavních aplikačních protokolů konverzující.
  - Znalost toho, která virtuální síť je konverzující k virtuální síti. Pokud se konverzace neočekává, můžete ji opravit.
  - Pokud jsou podvodné sítě konverzující s virtuální sítí, můžete opravit pravidla NSG pro blokování neautorizovaných sítí.
 
    Ve **svém prostředí** vyberte **zobrazení virtuální sítě** , jak je znázorněno na následujícím obrázku:

    ![Distribuce virtuální sítě předvádí řídicího panelu](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Virtual Network topologie znázorňuje horní pás karet pro výběr parametrů, jako jsou virtuální síť (mezi virtuálními nebo aktivními a neaktivními), externí připojení, aktivní toky a škodlivé toky virtuální sítě.
- Topologii Virtual Network můžete filtrovat podle předplatných, pracovních prostorů, skupin prostředků a časového intervalu. Další filtry, které vám pomůžou pochopit tok: typ toku (mezi virtuálními sítěmi, IntraVNET atd.), směr toku (příchozí, odchozí), stav toku (povolený, blokovaný), virtuální sítě (cílový a připojený), typ připojení (partnerský vztah nebo brána-P2S a S2S) a NSG. Pomocí těchto filtrů se můžete soustředit na virtuální sítě, které chcete podrobněji prošetřit.
- Virtual Network topologie znázorňuje distribuci provozu do virtuální sítě s ohledem na toky (povolený/blokovaný/příchozí/odchozí/neškodný/škodlivý), aplikační protokol a skupiny zabezpečení sítě, například:

    ![Předvádí distribuce provozu a podrobnosti toku pro topologii virtuální sítě](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologie virtuální sítě předvádí nejvyšší úrovně a další filtry](./media/traffic-analytics/virtual-network-filters.png)

    ![Podrobnosti o toku pro distribuci provozu virtuální sítě v hledání v protokolu](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Hledat**

- Distribuce provozu na podsíť, topologii, hlavní zdroje provozu do podsítě, hlavní neautorizovaných sítí konverzující do podsítě a hlavní aplikační protokoly konverzující.
    - Poznáte, která podsíť se konverzující do které podsítě. Pokud se zobrazí neočekávaná konverzace, můžete konfiguraci opravit.
    - Pokud jsou podvodné sítě konverzující s podsítí, je možné ji opravit konfigurací pravidel NSG pro blokování neautorizovaných sítí.
- Topologie podsítí zobrazuje horní pás karet pro výběr parametrů, jako je aktivní nebo neaktivní podsíť, externí připojení, aktivní toky a škodlivé toky podsítě.
- Topologie podsítě znázorňuje distribuci provozu do virtuální sítě s ohledem na toky (povolený/blokovaný/příchozí/odchozí/neškodný/škodlivý), aplikační protokol a skupin zabezpečení sítě, například:

    ![Předvádí topologie podsítě distribuce provozu virtuální sítě s ohledem na toky](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Hledat**

Distribuce provozu na aplikační bránu & Load Balancer, topologii, hlavních zdrojů přenosů, hlavních neautorizovaných sítích, které se konverzující do aplikační brány & Load Balancer a hlavních aplikačních protokolů konverzující. 
    
 - Poznáte, která podsíť je konverzující, na kterou Aplikační bránu nebo Load Balancer. Pokud si nejste všimnout neočekávaných konverzací, můžete konfiguraci opravit.
 - Pokud se podvodné sítě konverzující s aplikační bránou nebo Load Balancer, můžete je opravit tak, že nakonfigurujete pravidla NSG pro blokování neautorizovaných sítí. 

    ![Snímek obrazovky ukazuje topologii podsítě s distribucí provozu do podsítě aplikační brány s ohledem na toky.](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Zobrazit porty a virtuální počítače, které přijímají provoz z Internetu

**Hledat**

- Které otevřené porty se konverzující přes Internet?
  - Pokud se najde neočekávané porty, můžete konfiguraci opravit:

    ![Porty řídicího panelu předvádí přijímání a posílání provozu na Internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Podrobnosti o cílových portech a hostitelích Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Hledat**

Máte ve svém prostředí škodlivý provoz? Kde to pochází? Kde je určeno?

![Podrobnosti o škodlivých přenosech v hledání v protokolu](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Vizualizace trendů v NSG/NSG – přístupy k pravidlům

**Hledat**

- Která pravidla NSG/NSG mají nejvíc přístupů do srovnávacího grafu s distribucí toků?
- Jaké jsou horní páry zdrojové a cílové konverzace na pravidla NSG/NSG?

    ![Statistika přístupů do řídicího panelu předvádí NSG](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Na následujících obrázcích je znázorněno časové trendy pro přístupy k NSG pravidlům a podrobnostem toku zdroje a cíle pro skupinu zabezpečení sítě:

  - Rychle zjistíte, která pravidla skupin zabezpečení sítě a NSG přecházejí ze škodlivých toků a které jsou nejčastějšími škodlivými IP adresami, které přistupují ke cloudovém prostředí
  - Určení, která pravidla NSG/NSG povolují nebo blokují významný síťový provoz
  - Výběr horních filtrů pro detailní kontrolu pravidel NSG nebo NSG

    ![Předvádí časový trend pro přístupy k pravidlům NSG a nejvyšší pravidla NSG](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Podrobnosti o statistice hlavních pravidel NSG v hledání v protokolu](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Odpovědi na nejčastější dotazy najdete v tématu [Nejčastější dotazy k analýze provozu](traffic-analytics-faq.md).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak povolit protokoly toku, najdete v tématu [Povolení protokolování toku NSG](network-watcher-nsg-flow-logging-portal.md).
- Podrobnosti o schématu a zpracování Analýza provozu najdete v tématu [schéma analýzy provozu](traffic-analytics-schema.md).
