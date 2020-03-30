---
title: Analýza provozu Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak analyzovat protokoly toku skupiny zabezpečení sítě Azure pomocí analýzy provozu.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: damendo
ms.reviewer: vinigam
ms.openlocfilehash: 83164a615cacc067e5f1ea6a1dd6ce0f0fd9d540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298855"
---
# <a name="traffic-analytics"></a>Analýza provozu

Traffic Analytics je cloudové řešení, které poskytuje přehled o aktivitě uživatelů a aplikací v cloudových sítích. Analýza provozu analyzuje protokoly toku skupiny network watcher (NSG) a poskytuje přehled o toku provozu ve vašem cloudu Azure. S analýzou provozu můžete:

- Vizualizujte síťové aktivity napříč předplatnými Azure a identifikujte aktivní body.
- Identifikujte bezpečnostní hrozby pro síť a zabezpečte ji pomocí informací, jako jsou otevřené porty, aplikace, které se pokoušejí o přístup k internetu, a virtuální počítače (VM) připojující se k neautorizovaným sítím.
- Seznamte se se vzory toku provozu napříč oblastmi Azure a internetem a optimalizujte nasazení sítě z výkonu a kapacity.
- Určit chybné konfigurace sítě vedoucí k selhání připojení v síti.

> [!NOTE]
> Služba Traffic Analytics nyní podporuje shromažďování dat protokolů toku NSG s vyšší frekvencí 10 minut

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Proč analýza provozu?

Je důležité sledovat, spravovat a znát vlastní síť pro nekompromisní zabezpečení, dodržování předpisů a výkon. Znalost vlastního prostředí má prvořadý význam pro jeho ochranu a optimalizaci. Často potřebujete znát aktuální stav sítě, kdo se připojuje, odkud se připojuje, které porty jsou otevřené k internetu, očekávané chování sítě, nepravidelné chování sítě a náhlý nárůst provozu.

Cloudové sítě se liší od místních podnikových sítí, kde máte směrovače a přepínače podporující netflow nebo ekvivalentní protokoly, které umožňují shromažďovat síťové přenosy IP při vstupu nebo ukončení síťového rozhraní. Analýzou dat toku provozu můžete vytvořit analýzu toku a objemu síťového provozu.

Virtuální sítě Azure mají protokoly toku skupiny NSG, které poskytují informace o příchozím a odchozím přenosem IP prostřednictvím skupiny zabezpečení sítě přidružené k jednotlivým síťovým rozhraním, virtuálním počítačům nebo podsítím. Analýzou nezpracovaných protokolů toku nsg a vložením inteligence zabezpečení, topologie a geografie vám analýza provozu může poskytnout přehled o toku provozu ve vašem prostředí. Traffic Analytics poskytuje informace, jako je většina komunikujících hostitelů, většina komunikujících aplikačních protokolů, většina komunikujících párů hostitelů, povolený/blokovaný provoz, příchozí/odchozí provoz, otevřené internetové porty, většina pravidel blokování, distribuce provozu podle datového centra Azure, virtuální sítě, podsítí nebo neautorizovaných sítí.

## <a name="key-components"></a>Klíčové komponenty

- **Skupina zabezpečení sítě (NSG):** Obsahuje seznam pravidel zabezpečení, která povolují nebo zapírají síťový provoz prostředkům připojeným k virtuální síti Azure. Skupiny zabezpečení sítě můžou být přidružené k podsítím, jednotlivým virtuálním počítačům (klasický model) nebo jednotlivým síťovým rozhraním (síťovým kartám) připojeným k virtuálním počítačům (Resource Manager). Další informace naleznete v tématu [Přehled skupiny zabezpečení sítě](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Protokoly toku skupiny zabezpečení sítě (NSG):** Umožňují zobrazit informace o příchozím přenosu dat a odchozím přenosech IP prostřednictvím skupiny zabezpečení sítě. Protokoly toku nsg jsou zapsány ve formátu json a zobrazit odchozí a příchozí toky na základě pravidla, nic toku se vztahuje na, pět řazené kolekce členů informace o toku (zdrojová/cílová IP adresa, zdrojový/cílový port a protokol) a pokud byl přenos povolen nebo odepřen. Další informace o protokolech toku nsg naleznete v tématu [protokoly toku nsg](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics:** Služba Azure, která shromažďuje data monitorování a ukládá data do centrálního úložiště. Tato data mohou zahrnovat události, data o výkonu nebo vlastní data poskytnutá prostřednictvím rozhraní Azure API. Po získání jsou data dostupná pro výstrahy, analýzu a export. Monitorování aplikací, jako je monitorování výkonu sítě a analýzy provozu jsou sestaveny pomocí protokolů Azure Monitor jako základ. Další informace naleznete v tématu [protokoly Azure Monitor .](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- **Pracovní prostor Log Analytics**: Instance protokolů Azure Monitor, kde se ukládají data týkající se účtu Azure. Další informace o pracovních prostorech Analýzy protokolů naleznete [v tématu Vytvoření pracovního prostoru Analýzy protokolů](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Sledování sítě**: Místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v Azure. Protokoly toku nsg můžete zapnout a vypnout pomocí sledování sítě. Další informace naleznete v tématu [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Jak funguje analýza provozu

Analýza provozu zkoumá nezpracované protokoly toku nsg a zachycuje redukované protokoly agregací běžných toků mezi stejnou zdrojovou IP adresou, cílovou IP adresou, cílovým portem a protokolem. Například Host 1 (IP adresa: 10.10.10.10) komunikující s hostitelem 2 (IP adresa: 10.10.20.10), 100krát za období 1 hodiny pomocí portu (například 80) a protokolu (například http). Redukovaný protokol má jednu položku, kterou hostitel 1 & Host 2 komunikoval 100krát za období 1 hodiny pomocí portu *80* a protokolu *HTTP*, namísto 100 položek. Snížené protokoly jsou vylepšeny informacemi o zeměpisné poloze, zabezpečení a topologii a pak uloženy v pracovním prostoru Log Analytics. Následující obrázek znázorňuje tok dat:

![Tok dat pro zpracování protokolů toku nsg](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Podporované oblasti: NSG 

Analýzy provozu pro sítě sítě nsg můžete použít v některé z následujících podporovaných oblastí:

* Střední Kanada
* USA – středozápad
* USA – východ
* USA – východ 2
* USA – středosever
* USA – středojih
* USA – střed
* USA – západ
* USA – západ 2
* Francie – střed
* Západní Evropa
* Severní Evropa
* Brazílie – jih
* Spojené království – západ
* Spojené království – jih
* Austrálie – východ
* Austrálie – jihovýchod
* Východní Asie
* Jihovýchodní Asie
* Jižní Korea – střed
* Indie – střed
* Indie – jih
* Japonsko – východ 
* Japonsko – západ
* USA (Gov) – Virginia
* Čína východ 2

## <a name="supported-regions-log-analytics-workspaces"></a>Podporované oblasti: Pracovní prostory Analýzy protokolů

Pracovní prostor Log Analytics musí existovat v následujících oblastech:
* Střední Kanada
* USA – středozápad
* USA – východ
* USA – východ 2
* USA – středosever
* USA – středojih
* USA – střed
* USA – západ
* USA – západ 2
* USA – střed
* Francie – střed
* Západní Evropa
* Severní Evropa
* Brazílie – jih
* Spojené království – západ
* Spojené království – jih
* Austrálie – východ
* Austrálie – jihovýchod
* Východní Asie
* Jihovýchodní Asie
* Jižní Korea – střed
* Indie – střed
* Japonsko – východ
* USA (Gov) – Virginia
* Čína východ 2

## <a name="prerequisites"></a>Požadavky

### <a name="user-access-requirements"></a>Požadavky na přístup uživatelů

Váš účet musí být členem jedné z následujících [předdefinovaných rolí](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)Azure :

|Model nasazení   | Role                   |
|---------          |---------               |
|Resource Manager   | Vlastník                  |
|                   | Přispěvatel            |
|                   | Čtenář                 |
|                   | Přispěvatel sítě    |

Pokud váš účet není přiřazen k jedné z předdefinovaných rolí, musí být přiřazen [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) které jsou přiřazeny následující akce na úrovni předplatného:

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
- "Microsoft.Network/expressRouteCircuits/read"

Informace o kontrole přístupových oprávnění uživatelů najdete v [tématu Nejčastější dotazy k analýze provozu](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Povolení Network Watcheru

Chcete-li analyzovat provoz, musíte mít existující sledovací proces sítě nebo [povolit sledovací proces sítě](network-watcher-create.md) v každé oblasti, pro kterou máte sítě zabezpečení sítě, pro které chcete analyzovat provoz. Traffic analytics can be enabled for NSGs hosted in any of the [supported regions](#supported-regions-nsg).

### <a name="select-a-network-security-group"></a>Výběr skupiny zabezpečení sítě

Před povolením protokolování toku skupiny nsg, musíte mít skupinu zabezpečení sítě protokolu toky pro. Pokud skupinu zabezpečení sítě nemáte, přečtěte si informace [o vytvoření skupiny zabezpečení sítě.](../virtual-network/manage-network-security-group.md#create-a-network-security-group)

Na webu Azure Portal přejděte na **sledovací proces sítě**a vyberte **protokoly toku nsg**. Vyberte skupinu zabezpečení sítě, pro kterou chcete povolit protokol toku skupiny nsg, jak je znázorněno na následujícím obrázku:

![Výběr nsg, které vyžadují povolení protokolu toku NSG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Pokud se pokusíte povolit analýzu provozu pro soubor nsg, který je hostován v jiné oblasti než [podporované oblasti](#supported-regions-nsg), zobrazí se chyba "Nebyl nalezen" chyba.

## <a name="enable-flow-log-settings"></a>Povolení nastavení protokolu toku

Před povolením nastavení protokolu toku je nutné provést následující úkoly:

Zaregistrujte poskytovatele Azure Insights, pokud ještě není registrovaný pro vaše předplatné:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Pokud ještě nemáte účet Azure Storage pro ukládání protokolů toku NSG, musíte vytvořit účet úložiště. Můžete vytvořit účet úložiště s příkazem, který následuje. Před spuštěním příkazu nahraďte `<replace-with-your-unique-storage-account-name>` názvem, který je jedinečný ve všech umístěních Azure, mezi 3-24 znaky na délku, pomocí pouze čísla a malá písmena. V případě potřeby můžete také změnit název skupiny prostředků.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Vyberte následující volby, jak je znázorněno na obrázku:

1. Vybrat *zapnuto* pro **stav**
2. Vyberte *verzi 2* pro **verzi protokolů toku**. Verze 2 obsahuje statistiku relace toku (bajty a pakety)
3. Vyberte existující účet úložiště pro uložení protokolů toku. Pokud chcete data ukládat navždy, nastavte hodnotu na *hodnotu 0*. Poplatky za úložiště Azure pro účet úložiště. Ujistěte se, že vaše úložiště nemá "Data Lake Storage Gen2 Hierarchical Namespace Enabled" nastavena na hodnotu true.
4. Nastavte **uchovávání informací** na počet dní, pro které chcete data ukládat.
5. Vyberte *možnost Zapnuto* pro **stav analýzy provozu**.
6. Vyberte interval zpracování. Na základě vašeho výběru budou protokoly toků shromažďovány z účtu úložiště a zpracovány službou Traffic Analytics. Můžete si vybrat interval zpracování každých 1 hodinu nebo každých 10 minut. 
7. Vyberte existující pracovní prostor Analýzy protokolů (OMS) nebo vyberte **Vytvořit nový pracovní prostor** a vytvořte nový. Pracovní prostor Analýzy protokolů používá Služba Traffic Analytics k ukládání agregovaných a indexovaných dat, která se pak používají ke generování analýzy. Pokud vyberete existující pracovní prostor, musí existovat v jedné z [podporovaných oblastí](#supported-regions-log-analytics-workspaces) a byly upgradovány na nový dotazovací jazyk. Pokud nechcete upgradovat existující pracovní prostor nebo nemáte pracovní prostor v podporované oblasti, vytvořte nový. Další informace o jazycích dotazů najdete [v tématu Upgrade Azure Log Analytics na nové hledání protokolu](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>Pracovní prostor analýzy protokolů hostující řešení analýzy provozu a sítě sítě nsg nemusí být ve stejné oblasti. Například můžete mít analýzu provozu v pracovním prostoru v oblasti západní Evropa, zatímco můžete mít nsGs ve východní USA a západní USA. Ve stejném pracovním prostoru lze nakonfigurovat více nevládních směrovačů sítě.

8. Vyberte **Uložit**.

    ![Výběr účtu úložiště, pracovního prostoru Log Analytics a povolení analýzy provozu](./media/traffic-analytics/ta-customprocessinginterval.png)

Opakujte předchozí kroky pro všechny ostatní sítě sítě, pro které chcete povolit analýzu provozu. Data z protokolů toku jsou odesílána do pracovního prostoru, takže se ujistěte, že místní zákony a předpisy ve vaší zemi povolují ukládání dat v oblasti, kde pracovní prostor existuje. Pokud jste nastavili různé intervaly zpracování pro různé soubory zabezpečení zabezpečení zabezpečení, budou data shromažďována v různých intervalech. Příklad: Můžete povolit interval zpracování 10 minut pro kritické vnety a 1 hodinu pro nekritické vnets.

Analýzy provozu můžete také nakonfigurovat pomocí rutiny [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell v prostředí Azure PowerShell. Spusťte `Get-Module -ListAvailable Az` a vyhledejte nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Zobrazit analýzu provozu

Chcete-li zobrazit analýzu provozu, vyhledejte **sledovací proces sítě** na panelu vyhledávání na portálu. Jakmile se vdálte v programu Sledování sítě, abyste mohli prozkoumat analýzu provozu a její možnosti, vyberte v levém menu **analýzu provozu.** 

![Přístup k řídicímu panelu Analýzy provozu](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Zobrazení řídicího panelu může trvat až 30 minut, protože služba Traffic Analytics musí nejprve agregovat dostatek dat, aby mohla získat smysluplné přehledy, aby mohla generovat jakékoli sestavy.

## <a name="usage-scenarios"></a>Scénáře použití

Některé přehledy, které byste mohli chtít získat po úplné konfiguraci traffic analytics, jsou následující:

### <a name="find-traffic-hotspots"></a>Hledání dopravních hotspotů

**Hledat**

- Kteří hostitelé, podsítě a virtuální sítě odesílají nebo přijímají největší provoz, procházejí maximálním škodlivým provozem a blokují významné toky?
    - Zkontrolujte srovnávací graf pro hostitele, podsíť a virtuální síť. Pochopení toho, kteří hostitelé, podsítě a virtuální sítě odesílají nebo přijímají největší provoz, vám může pomoci identifikovat hostitele, kteří zpracovávají největší provoz, a zda se distribuce provozu provádí správně.
    - Můžete vyhodnotit, zda je objem provozu vhodný pro hostitele. Je objem provozu normální chování, nebo si zaslouží další šetření?
- Kolik příchozího/odchozího provozu existuje?
    -   Očekává se, že hostitel obdrží více příchozího provozu než odchozí nebo naopak?
- Statistiky blokovaného provozu.
    - Proč hostitel blokuje významný objem neškodného provozu? Toto chování vyžaduje další šetření a pravděpodobně optimalizace konfigurace
- Statistiky škodlivého povoleného/blokovaného provozu
  - Proč hostitel přijímá škodlivý provoz a proč jsou povoleny toky ze škodlivého zdroje? Toto chování vyžaduje další šetření a pravděpodobně optimalizace konfigurace.

    Vyberte **Zobrazit vše**, v části **Host**, jak je znázorněno na následujícím obrázku:

    ![Řídicí panel s hostitelem s většinou podrobností o provozu](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Následující obrázek znázorňuje trendy času pro prvních pět mluvících hostitelů a podrobnosti týkající se toku (povoleno – příchozí/odchozí a odepřené – příchozí/odchozí toky) pro hostitele:

    ![Pět nejmluvících hostitelských trendů](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Hledat**

- Které jsou nejvíce konveblující hostitelské páry?
    - Očekávané chování, jako je front-end nebo back-end komunikace nebo nepravidelné chování, jako je back-end internetový provoz.
- Statistiky povoleného/blokovaného provozu
    - Proč hostitel povoluje nebo blokuje významný objem provozu
- Nejčastěji používaný aplikační protokol mezi většinou konvetejících párů hostitelů:
    - Jsou tyto aplikace v této síti povoleny?
    - Jsou aplikace správně nakonfigurovány? Používají vhodný protokol pro komunikaci? V části **Nejčastější konverzace**vyberte **Zobrazit vše** , jak je vidět na následujícím obrázku:

        ![Řídicí panel představující nejčastější konverzaci](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Následující obrázek znázorňuje trendy času pro prvních pět konverzací a podrobnosti související s tokem, jako jsou povolené a odepřené příchozí a odchozí toky pro dvojici konverzací:

    ![Pět nejlepších upovídaný chutný konverzace podrobnosti a trend](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Hledat**

- Který aplikační protokol se ve vašem prostředí nejpoužívanější a které dvojice hostitelů používají aplikační protokol nejčastěji?
    - Jsou tyto aplikace v této síti povoleny?
    - Jsou aplikace správně nakonfigurovány? Používají vhodný protokol pro komunikaci? Očekávané chování je běžné porty, například 80 a 443. Pro standardní komunikaci, pokud jsou zobrazeny neobvyklé porty, mohou vyžadovat změnu konfigurace. Vyberte **Zobrazit vše** v části **Port aplikace**na následujícím obrázku:

        ![Řídicí panel s nejvyššími aplikačními protokoly](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Následující obrázky ukazují čas trendy pro prvních pět protokolů L7 a podrobnosti související s tokem (například povolené a odepřené toky) pro protokol L7:

    ![Podrobnosti a trend pěti protokolů s pěti vrstvami 7](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Podrobnosti o toku pro protokol aplikace při hledání protokolu](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Hledat**

- Trendy využití kapacity brány VPN ve vašem prostředí.
    - Každá skladová položka VPN umožňuje určitou šířku pásma. Jsou brány VPN nevyužité?
    - Dosahují vaše brány kapacity? Měli byste upgradovat na další vyšší skladovou položku?
- Kteří jsou nejvíce konversing hostitelé, přes které VPN brány, přes který port?
    - Je tento vzor normální? Vyberte **Zobrazit vše** v části **Brána VPN**, jak je znázorněno na následujícím obrázku:

        ![Řídicí panel s nejvyššími aktivními připojeními VPN](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Následující obrázek znázorňuje čas, který ukazuje využití kapacity brány Azure VPN a podrobnosti související s tokem (například povolené toky a porty):

    ![Trend využití brány VPN a podrobnosti o toku](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Vizualizace rozložení provozu podle zeměpisné polohy

**Hledat**

- Distribuce provozu na datové centrum, jako jsou nejlepší zdroje provozu do datového centra, nejlepší nepoctiví sítě, které se s datovým centrem sjednocují, a špičkové protokoly aplikací.
  - Pokud pozorujete větší zatížení datového centra, můžete naplánovat efektivní distribuci provozu.
  - Pokud neautorizovaných sítí jsou conversing v datovém centru, opravte pravidla sítě zabezpečení sítě k jejich zablokování.

    Vyberte **Zobrazit mapu** v části **Vaše prostředí**, jak je znázorněno na následujícím obrázku:

    ![Řídicí panel s rozložením provozu](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Geografická mapa zobrazuje horní pás karet pro výběr parametrů, jako jsou datová centra (Nasazená/Bez nasazení/Aktivní/Neaktivní/Analýza provozu povolená/Analýza provozu není povolena) a země nebo oblasti přispívající k aktivnímu nasazení neškodným/škodlivým provozem:

    ![Zobrazení geografické mapy s aktivním nasazením](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Geomapa zobrazuje distribuci provozu do datového centra ze zemí nebo oblastí a kontinentů, které s ním komunikují modře (neškodný provoz) a červené (škodlivé dopravní) barevné čáry:

    ![Zobrazení geografické mapy představující rozložení provozu do zemí/oblastí a kontinentů](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Podrobnosti o toku pro distribuci přenosů při vyhledávání v protokolu](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Vizualizace distribuce provozu pomocí virtuálních sítí

**Hledat**

- Distribuce provozu na virtuální síť, topologie, nejlepší zdroje provozu do virtuální sítě, top rogue sítě conversing do virtuální sítě, a top konvetní aplikační protokoly.
  - Vědět, která virtuální síť se snimi, které virtuální sítě. Pokud není konverzace očekávána, lze ji opravit.
  - Pokud neautorizační sítě jsou conversing s virtuální sítí, můžete opravit pravidla sítě zabezpečení sítě blokovat neautorizovaných sítí.
 
    Vyberte **Zobrazit virtuální sítě** v části Vaše **prostředí**, jak je znázorněno na následujícím obrázku:

    ![Řídicí panel představující distribuci virtuální sítě](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Topologie virtuální sítě zobrazuje horní pás karet pro výběr parametrů, jako jsou virtuální sítě (Inter virtuální síť Připojení/Aktivní/Neaktivní), Externí připojení, Aktivní toky a Škodlivé toky virtuální sítě.
- Topologii virtuální sítě můžete filtrovat na základě předplatných, pracovních prostorů, skupin prostředků a časového intervalu. Další filtry, které vám pomohou pochopit tok jsou: Typ toku (InterVNet, IntraVNET a tak dále), Směr toku (Příchozí, Odchozí), Stav toku (Povoleno, Blokováno), Virtuální sítě (cílené a připojené), Typ připojení (Partnerský vztah nebo brána - P2S a S2S) a NSG. Pomocí těchto filtrů se můžete zaměřit na virtuální sítě, které chcete podrobně prozkoumat.
- Topologie virtuální sítě zobrazuje distribuci přenosů do virtuální sítě s ohledem na toky (povoleno/blokováno/blokováno/odchozí/neškodné/škodlivé), aplikační protokol a skupiny zabezpečení sítě, například:

    ![Topologie virtuální sítě představující podrobnosti o distribuci provozu a toku](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologie virtuální sítě představuje nejvyšší úroveň a další filtry](./media/traffic-analytics/virtual-network-filters.png)

    ![Podrobnosti o toku pro distribuci provozu virtuální sítě při hledání protokolu](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Hledat**

- Distribuce provozu na podsíť, topologie, hlavní zdroje provozu do podsítě, nejlepší sítě pro příchozí do podsítě a protokoly aplikací s nejvyššími konversingem.
    - Vědět, která podsíť se s nimi sbližuje. Pokud se zobrazí neočekávané konverzace, můžete opravit konfiguraci.
    - Pokud sítě neautorizovaných sítí probíjejí podsíť, můžete ji opravit konfigurací pravidel skupiny nsg tak, aby blokovala sítě.
- Topologie podsítí zobrazuje horní pás karet pro výběr parametrů, jako je aktivní/neaktivní podsíť, externí připojení, aktivní toky a škodlivé toky podsítě.
- Topologie podsítě zobrazuje distribuci přenosů do virtuální sítě s ohledem na toky (povoleno/blokováno/blokováno/odchozí/neškodné/škodlivé), aplikační protokol a skupiny zabezpečení sítě, například:

    ![Topologie podsítě představující distribuci provozu virtuální sítě s ohledem na toky](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Hledat**

Distribuce provozu podle aplikační brány & Vyrovnávání zatížení, topologie, nejlepší zdroje provozu, nejlepší sítě neautorizovaných, které se připojují k aplikační bráně & Vyrovnávání zatížení a hlavní konvebilní aplikační protokoly. 
    
 - Vědět, která podsíť se sděluje které aplikační brány nebo vyrovnávání zatížení. Pokud pozorujete neočekávané konverzace, můžete opravit konfiguraci.
 - Pokud neautorizační sítě jsou ve správě s bránou aplikace nebo vyrovnávání zatížení, můžete opravit konfigurací pravidel skupiny nsg blokovat neautorizovaných sítí. 

    ![subsíť-topologie-předvádění-provoz-distribuce-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Zobrazení portů a virtuálních počítačů přijímajících provoz z internetu

**Hledat**

- Které otevřené porty se přes internet rozmlouvají?
  - Pokud jsou otevřené neočekávané porty, můžete konfiguraci opravit:

    ![Řídicí panel s porty, které přijímají a posílají provoz na internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Podrobnosti o cílových portech Azure a hostitelích](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Hledat**

Máte škodlivý provoz ve vašem prostředí? Odkud pochází? Kam je to předurčeno?

![Podrobnosti o škodlivých přenosových tocích při hledání v protokolu](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Vizualizujte trendy v přístupech k pravidlům nsg/nsg

**Hledat**

- Která pravidla nsg/nsg mají nejvíce přístupů ve srovnávacím grafu s rozdělením toků?
- Jaké jsou dvojice hlavních zdrojových a cílových konverzačních párů podle pravidel nsg/nsg?

    ![Řídicí panel představující statistiku přístupů k nsg](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Následující obrázky ukazují čas trendy pro požadavky na server nsg pravidla a podrobnosti o toku zdroj-cíl pro skupinu zabezpečení sítě:

  - Rychle zjistěte, která pravidla nsg a nsg procházejí škodlivými toky a které jsou nejlepšími škodlivými IP adresami, které přistupují k vašemu cloudovému prostředí
  - Určení, která pravidla nsg/nsg umožňují/blokují významný síťový provoz
  - Výběr horních filtrů pro podrobnou kontrolu pravidel nsg nebo nsg

    ![Představení trendu času pro požadavky na server NSG a nejvyšší pravidla nsg](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Nejlepší podrobnosti statistiky pravidel skupiny nsg při vyhledávání protokolů](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Odpovědi na nejčastější dotazy najdete v [tématu Nejčastější dotazy k analýze provozu](traffic-analytics-faq.md).

## <a name="next-steps"></a>Další kroky

- Informace o povolení protokolů toku naleznete [v tématu Povolení protokolování toku toku nsg](network-watcher-nsg-flow-logging-portal.md).
- Informace o schématu a podrobnostech zpracování služby Traffic Analytics najdete v [tématu Schéma analýzy provozu](traffic-analytics-schema.md).
