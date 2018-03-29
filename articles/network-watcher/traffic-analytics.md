---
title: Provoz Azure analytics | Microsoft Docs
description: Zjistěte, jak k analýze protokolů o zabezpečení skupiny toku síť Azure s přenosy analytics.
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
ms.date: 02/22/2018
ms.author: jdial
ms.openlocfilehash: ffb13d1190535dacbe3a0781a1d3b425a970d26e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="traffic-analytics"></a>Analýza provozu

Analýza provozu je cloudové řešení, která poskytuje přehled o činnosti uživatelů a aplikací v cloudových sítích. Analýza provozu analyzuje protokolů toku sledovací proces sítě sítě zabezpečení skupiny (NSG) k poskytování přehledy přenosového toku ve vašem cloudu Azure. Analýza provozu můžete:

- Vizualizovat síťové aktivity ve vašich předplatných Azure a identifikovat aktivní oblasti.
- Identifikujte ohrožení zabezpečení a zabezpečení sítě, informace, jako je otevření portů, pokusu o přístup k Internetu a virtuální počítače (VM), připojení k sítím podvodné aplikace.
- Pochopení vzory tok přenosů dat mezi oblastmi Azure a internet k optimalizaci nasazení sítě pro výkon a kapacitu.
- Přesné nesprávné konfigurace sítě vedoucí k selhání připojení v síti.

## <a name="why-traffic-analytics"></a>Proč provozu analytics?

Je důležité monitorovat, spravovat a znát vlastní síť neohrožený zabezpečení, dodržování předpisů a výkonu. Znalost svého vlastního prostředí je především třeba chránit a optimalizovat ho. Často potřebujete vědět, aktuální stav sítě, který se připojuje, kde, které porty jsou otevřené k Internetu, očekávané chování sítě, síťové nestandardní chování a nečekané roste v provozu.

Cloudové sítě se liší od podnikové sítě na místě, kdy máte Netflow nebo ekvivalentní protokol podporující směrovače a přepínače, které poskytují schopnost shromažďovat IP síťový provoz, jak ho zadá nebo ji opustí síťové rozhraní. Analýzou dat tok přenosů, můžete vytvořit analýzu tok přenosu sítě a svazku.

Virtuální sítě Azure mají NSG toku protokoly, které poskytují informace o příchozí a odchozí provoz IP prostřednictvím skupinu zabezpečení sítě přidružené k jednotlivým síťovým rozhraní, virtuální počítače nebo podsítě. Analýza nezpracovaná toku protokolů NSG a vkládání intelligence provozu, zabezpečení, topologie a geography, analýzy vám může poskytnout přehled o přenosového toku ve vašem prostředí. Provoz Analytics poskytuje informace, jako je nejvíce komunikuje hostitele, většina komunikační protokoly aplikací, nejvíce konverzaci páry hostitele, povolené nebo blokované provoz, příchozí nebo odchozí přenosy, otevřené porty internet, nejvíce blokující pravidla, provoz distribuce pro datové centrum Azure, virtuální sítě, podsítě, nebo podvodné sítě.

## <a name="key-components"></a>Klíčové komponenty 

- **Skupina zabezpečení sítě (NSG)**: obsahuje seznam pravidel zabezpečení, která povolují nebo odpírají síťový provoz na prostředky, které jsou připojené k virtuální síti Azure. Skupiny zabezpečení sítě můžou být přidružené k podsítím, jednotlivým virtuálním počítačům (klasický model) nebo jednotlivým síťovým rozhraním (síťovým kartám) připojeným k virtuálním počítačům (Resource Manager). Další informace najdete v tématu [přehled skupiny zabezpečení sítě](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Síťové protokoly zabezpečení skupiny (NSG) toku**: vám umožní zobrazit informace o příchozí a odchozí provoz IP prostřednictvím skupinu zabezpečení sítě. Skupina NSG toku protokoly jsou zapsány ve formátu json a zobrazit odchozí a příchozí tok na základě pravidla na síťový adaptér toku se vztahuje na 5 řazené kolekce členů informace o toku (zdrojové nebo cílové IP adresy, zdrojový nebo cílový port a protokol), a pokud přenos byl povolen nebo byl odepřen. Další informace o toku protokolů NSG najdete v tématu [protokolů NSG toku](network-watcher-nsg-flow-logging-overview.md).
- **Analýza protokolu**: Služba Azure, která shromažďuje data monitorování a ukládá data v centrálním úložišti. Tato data můžou obsahovat události, údaje o výkonu nebo vlastní data poskytnutá prostřednictvím rozhraní API služby Azure. Po získání jsou data dostupná pro výstrahy, analýzu a export. Monitorování aplikací, jako jsou například analýzy výkonu monitorování a přenosy dat sítě jsou vytvořeny pomocí analýzy protokolů jako základ. Další informace najdete v tématu [protokolu analýzy](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Přihlaste se pracovní prostor analýzy**: instance analýzy protokolů, které jsou uložená data, která se týkají k účtu Azure. Další informace o pracovních prostorech analýzy protokolů, najdete v části [vytvořit pracovní prostor analýzy protokolů](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Sledovací proces sítě**: místní služba, která umožňuje sledovat a diagnostikovat podmínky na úrovni sítě scénář v Azure. Chcete-li toku protokolů NSG zapnout a vypnout s sledovací proces sítě. Další informace najdete v tématu [sledovací proces sítě](network-watcher-monitoring-overview.md#network-watcher).

## <a name="how-traffic-analytics-works"></a>Jak funguje Analýza provozu 

Analýza provozu prozkoumá nezpracovaná toku protokolů NSG a zachytí snížené protokoly agregací běžné toků mezi stejnou zdrojovou adresu IP, cílové IP adresy, cílový port a protokol. Například hostitel 1 (IP adresa: 10.10.10.10) komunikaci s hostiteli 2 (IP adresa: 10.10.20.10), 100krát za období 1 hodina pomocí portu (například 80) a protokol (například http). Snížené protokolu má jeden záznam, který hostitel 1 a 2 hostitele oznamovat 100krát za období 1 hodina pomocí portu *80* a protokol *HTTP*, místo nutnosti 100 záznamů. Snížené protokoly jsou rozšířené geography, zabezpečení a informace o topologii a pak uloženy v pracovním prostoru analýzy protokolů. Následující obrázek znázorňuje tok dat:

![Tok dat protokolů NSG toku zpracování](media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Podporované oblasti

Analýza provozu je dostupná ve verzi preview. Funkce ve verzi preview nemají stejnou úroveň dostupnost a spolehlivost jako funkce v hlavní verze.  Ve verzi preview, můžete provádět analýzy provozu skupin Nsg v některém z následujících oblastí: – Západ střední USA, Východ USA, Východ USA 2, – Sever střední USA, Jižní střední USA, střed USA, západní USA, západní USA 2, západní Evropa, Severní Evropa, západní Spojené království, Spojené království – Jih, Austrálie – východ a Austrálie – jihovýchod. Pracovní prostor log analytics musí existovat v rámci – Západ střední USA, Východ USA, západní Evropa, Austrálie – jihovýchod nebo Spojené království – jih oblasti.

## <a name="prerequisites"></a>Požadavky

### <a name="enable-network-watcher"></a>Povolit sledovací proces sítě 

Analýza provozu, musíte mít existující sledovací proces sítě, nebo [povolit sledovací proces sítě](network-watcher-create.md) v každé oblasti, ke které máte skupiny Nsg, které chcete analyzovat provoz pro. Analýza provozu se dá povolit pro skupiny Nsg hostovaná v některém z [podporované oblasti](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Opakujte registraci poskytovatele síťových prostředků 

Než budete moct použít Analýza provozu ve verzi Preview, je nutné znovu zaregistrovat svým poskytovatelem síťových prostředků. Klikněte na tlačítko **zkuste ho** v následujícím kódu otevřete prostředí cloudové služby Azure. Prostředí cloudu automaticky protokoluje události můžete do vašeho předplatného Azure. Jakmile cloudové prostředí je otevřený, zadejte následující příkaz se znovu registrovat poskytovatele síťových prostředků:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Vyberte skupinu zabezpečení sítě 

Před povolením protokolování toku NSG, musí mít skupina zabezpečení sítě k toky pro přihlášení. Pokud nemáte skupinu zabezpečení sítě, najdete v části [vytvořit skupinu zabezpečení sítě](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) k jeho vytvoření.

Na levé straně na portálu Azure, vyberte **monitorování**, pak **sledovací proces sítě**a potom vyberte **protokolů NSG toku**. Vyberte skupinu zabezpečení sítě, který chcete povolit protokol NSG toku, jak je znázorněno na následujícím obrázku:

![Výběr skupiny Nsg, které vyžadují povolování NSG toku protokolu](media/traffic-analytics/selection-of-nsgs-that-require- enablement-of-nsg-flow-logging.png)

Pokud se pokusíte povolit provoz analytics pro skupinu NSG, který je hostován v libovolné oblasti jiné než [podporované oblasti](#supported-regions), obdržíte chybu "Nebyl nalezen". 

## <a name="enable-flow-log-settings"></a>Povolte nastavení protokolu toku

Než povolíte nastavení protokolu toku, musíte provést následující úlohy:

Zaregistrujte zprostředkovatele Statistika Azure, pokud je již registrován pro vaše předplatné:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Pokud ještě nemáte účet služby Azure Storage k ukládání NSG toku přihlásí, je třeba vytvořit účet úložiště. Účet úložiště můžete vytvořit pomocí příkazu, který následuje dále. Před spuštěním příkazu, nahraďte `<replace-with-your-unique-storage-account-name>` s názvem, který je jedinečné ve všech umístěních Azure, 3 až 24 znaků, pomocí jenom čísla a malá písmena. Název skupiny prostředků, můžete určit také v případě potřeby.

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
2. Vyberte stávající účet úložiště k ukládání protokolů toku v. Pokud chcete data uložit navždy, nastavte hodnotu na *0*. Můžete toho vám být účtovány poplatky za úložiště Azure pro účet úložiště.
3. Nastavit **uchování** počet dní, které chcete uložit data.
4. Vyberte *na* pro **provozu Analytics stav**.
5. Existujícímu pracovnímu prostoru analýzy protokolů (OMS) nebo vyberte **vytvořit nový pracovní prostor** vytvořit nový. Pracovní prostor analýzy protokolů je používán provoz Analytics k ukládání dat agregované a indexované, která se pak používá ke generování analytics. Pokud vyberete existujícímu pracovnímu prostoru, musí existovat v jednom z [podporované oblasti](#traffic-analytics-supported-regions) a které byly upgradované na nový dotazovací jazyk. Pokud nechcete, aby k existujícímu pracovnímu prostoru upgradu nebo nemají pracovního prostoru v podporované oblasti, vytvořte novou. Další informace o jazycích dotazů najdete v tématu [Azure Log Analytics upgradovat na nové hledání protokolu](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    Pracovní prostor log analytics hostování řešení analýzy provozu a skupin Nsg nemusí být ve stejné oblasti. Například může mít Analýza provozu v pracovním prostoru v oblasti západní Evropa, když máte skupin Nsg v Východ USA a západní USA. Více skupin Nsg se dá nakonfigurovat v ve stejném pracovním prostoru.
6. Vyberte **Uložit**.

    ![Výběr účtu úložiště, pracovní prostor analýzy protokolů a povolování Analýza provozu](media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

Opakujte předchozí kroky pro jiné pro které chcete povolit Analýza provozu pro skupiny Nsg. Data z protokolů tok je odeslána do pracovního prostoru, zajistěte proto, že místních zákonů a nařízení ve vaší zemi povolit ukládání dat v oblasti, kde existuje pracovním prostoru.

## <a name="view-traffic-analytics"></a>Zobrazení analýzy provozu

Na levé portálu, vyberte **všechny služby**, pak zadejte *monitorování* v **filtru** pole. Když **monitorování** se zobrazí ve výsledcích hledání, vyberte ho. Chcete-li začít seznamovat se analýza provozu a jeho funkce, vyberte **sledovací proces sítě**, pak **Analýza provozu (Preview)**.

![Přístup k panelu analýzy provozu](media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Řídicí panel může trvat až 30 minut, než se objeví při prvním protože analýza provozu musí nejprve agregovat dostatek dat pro její odvodit smysluplné přehledy, než to může generovat žádné sestavy.

## <a name="usage-scenarios"></a>Scénáře použití

Některé statistiky, které chcete získat konfigurován plně Analýza provozu, jsou následující:

### <a name="find-traffic-hotspots"></a>Vyhledání provozu hotspotům

**Hledat**

- Které hostitele jsou odesílání nebo přijímání nejvíce provozu?
    - Pochopení, které hostitele jsou odesílání nebo přijímání většina provozu můžete identifikovat hostitelů, které jsou zpracování většina provozu.
    - Můžete vyhodnotit, jestli je objem provozu vhodné pro hostitele. Je objem provozu normální chování, nebo ho si zasloužila další šetření?
- Kolik příchozí nebo odchozí provoz je k dispozici?
    -   Je hostitel očekávané k přijetí více příchozí provoz než odchozí nebo naopak?
- Statistika provozu povolené nebo blokované.
    - Proč je hostitel povolení nebo blokování významný objem provozu?

    Vyberte **podrobnosti**v části **hostiteli s většina provozu**, jak je znázorněno na následujícím obrázku:

    ![Řídicí panel ji na hostitele s většina podrobnosti o provozu](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Následující obrázek znázorňuje, čas trendů nejvyšší pět talking hostitelů a podrobnosti související tok (povolené – příchozí nebo odchozí a odepření - příchozí nebo odchozí toky) pro virtuální počítač:

    ![Horní pět rozhovoru většinu hostitele trendu](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Hledat**

- Jsou nejvíce conversing páry hostitele?
    - Očekávané chování jako front end-back-end komunikaci nebo nepravidelné chování jako back-end internetových přenosů.
- Statistiky provozu povolen nebo blokován
    - Proč je hostitel povolení nebo blokování výrazné navýšení provozu svazku
- Nejčastěji používá aplikační protokol mezi většina conversing páry hostitele:
    - Jsou tyto aplikace povoleno v této síti?
    - Jsou aplikace konfigurována správně? Používají se pro komunikaci o vhodném protokolu? Vyberte **podrobnosti** pod **nejvíce častých konverzace**, jak zobrazit na následujícím obrázku:

        ![Řídicí panel předvádění nejčastěji se vyskytující konverzace](media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Následující obrázek znázorňuje čas trendů pro horní pět konverzace a toku související podrobnosti, jako povolených a zakázaných příchozí a odchozí toky pro pár konverzace:

    ![Prvních pět podrobnosti chatty konverzace a trendu](media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Hledat**

- Protokol, který aplikace se nejčastěji používá ve vašem prostředí a který conversing páry hostitele jsou nejvíce pomocí protokolu aplikace?
    - Jsou tyto aplikace povoleno v této síti?
    - Jsou aplikace konfigurována správně? Používají se pro komunikaci o vhodném protokolu? Očekávané chování je běžné porty, jako je například 80 a 443. Pro standardní komunikaci Pokud jsou zobrazeny žádné neobvyklou porty, pravděpodobně budou vyžadovat změnu konfigurace. Vyberte **podrobnosti** pod **Top protokoly aplikací**, na následujícím obrázku:

        ![Řídicí panel předvádění protokoly nejvyšší aplikací](media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Tento čas zobrazit obrázky trendů pro protokoly L7 horní pět a toku související podrobnosti (například povolených a zakázaných toky) pro protokol L7:

    ![Horní pět vrstvy 7 podrobnosti protokoly a trendu](media/traffic-analytics/top five-layer-seven-protocols-details-and-trend.png)

    ![Tok podrobností protokolu aplikace v protokolu vyhledávání](media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Hledat**

- Trendy využití kapacity brána sítě VPN ve vašem prostředí.
    - Každý SKU VPN umožňuje šířku pásma. Byla málo využitá brány sítě VPN?
    - Jsou vašich bran dosažení kapacity? By měli upgradovat na další vyšší skladová položka?
- Většina conversing hostitelů, přes které brány sítě VPN, která jsou přes port, který?
    - Tento vzor je normální? Vyberte **podrobnosti** pod **Top aktivní připojení VPN**, jak je znázorněno na následujícím obrázku:

        ![Řídicí panel předvádění nejvyšší aktivní připojení VPN](media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Následující obrázek znázorňuje, čas trendů pro využití kapacity služby Azure VPN Gateway a související toku (například povolené toky a porty) podrobností:

    ![Podrobnosti trendu a toku brány využití sítě VPN](media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Vizualizace distribuce přenosů podle Geografie

**Hledat**

- Distribuce přenosů na datové centrum třeba hlavní zdroje přenosů dat do datového centra, nejvyšší podvodný sítě konverzaci s datového centra a horní konverzaci protokoly aplikací.
    - Pokud zjistíte další zatížení datového centra, můžete naplánovat pro distribuci efektivní provoz.
    - Pokud podvodný sítě jsou konverzaci v datovém centru, pak opravte pravidla NSG k zablokovat.

    Vyberte **kliknutím sem zobrazíte za provozu geomap** pod **distribuce přenosů mezi datových center Azure**, jak je znázorněno na následujícím obrázku:

  ![Řídicí panel demonstrační distribuce přenosů](media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Geo mapa znázorňuje hlavní pásu karet pro výběr parametrů například datových centrech (nasazen nebo ne nasazení nebo aktivní nebo neaktivní nebo provoz Analytics povoleno/není povolena analýza provozu) a zemích přidání Benign nebo škodlivé provoz aktivní nasazení:

    ![Zobrazení mapy geograficky předvádění aktivní nasazení](media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Geografické mapy ukazuje distribuce přenosů do datového centra z jiných zemí a přes kontinenty až komunikaci se ho v modrý (neškodný provoz) a červený (škodlivého provozu) barevné řádky:

    ![Zobrazení mapy geograficky předvádění distribuce přenosů do jiných zemí a přes kontinenty až](media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Tok podrobnosti distribuce přenosů v hledání protokolů](media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Vizualizovat distribuce přenosů pomocí virtuální sítě

**Hledat**

- Distribuce přenosů na virtuální síť, topologie, nejvyšší zdroje provozu k virtuální síti, sítě nejvyšší podvodný konverzaci k virtuální síti a horní konverzaci protokoly aplikací.
    - Zároveň budete vědět, které virtuální sítě je konverzaci do virtuální sítě. Pokud konverzace není očekáváno, může být vyřešen.
    - Pokud podvodný sítě jsou konverzaci s virtuální síť, můžete opravit NSG pravidla pro blokování podvodný sítě.
 
    Vyberte **kliknutím sem zobrazíte topologie provoz VNet** pod **virtuální sítě distribuční**, jak je znázorněno na následujícím obrázku: 

    ![Řídicí panel předvádění distribuční virtuální sítě](media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Virtuální síťové topologie znázorňuje hlavní pásu karet pro výběr parametrů, jako je virtuální sítě (virtuální sítě Inter připojení nebo aktivní nebo neaktivní), externí připojení, aktivní toky a škodlivý toky virtuální sítě.
- Virtuální síťové topologie znázorňuje distribuce přenosů k virtuální síti s ohledem na toků (povolené nebo blokované nebo příchozí nebo odchozí nebo Benign nebo škodlivé), protokol aplikací a skupin zabezpečení sítě, například:

    ![Virtuální síťové topologie předvádění podrobnosti distribuce a tok provozu](media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)

    ![Tok podrobnosti distribuce přenosů virtuální sítě v hledání protokolů](media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Hledat**

- Distribuce přenosů na podsíť, topologie, hlavní zdroje provoz do podsítě, sítě nejvyšší rouge konverzaci podsítě a horní konverzaci protokoly aplikací.
    - Zároveň budete vědět, které podsíť je konverzaci které podsíti. Pokud se zobrazí neočekávané konverzace, můžete vyřešit konfiguraci.
    - Pokud rouge sítě jsou konverzaci s podsítí, budete moci opravte nakonfigurováním NSG pravidla pro blokování podvodný sítím, například.
- Topologie podsítě zobrazuje nejvyšší pásu karet pro výběr parametry, například podsítě, externí připojení, aktivní toky a škodlivý toky podsítě aktivní nebo neaktivní.
- Topologie podsítě zobrazuje distribuce přenosů k virtuální síti s ohledem na toků (povolené nebo blokované nebo příchozí nebo odchozí nebo Benign nebo škodlivé), protokol aplikací a skupin Nsg, například:

    ![Podsíť topologie předvádění distribuce přenosů podsíť virtuální sítě s ohledem na toky](media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Zobrazení portů a virtuální počítače přijímat přenosy z Internetu

**Hledat**

- Otevřete porty, které jsou konverzaci přes internet?
    - Pokud neočekávané porty otevřít, můžete vyřešit konfiguraci:

        ![Řídicí panel předvádění porty příjem a odesílání provozu do internet](media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

        ![Podrobnosti o Azure cílové porty a hostitele](media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Hledat**

Máte ve vašem prostředí škodlivý přenos? Kde je pocházející z? Kde je určené do?

![Podrobnosti o toky škodlivý provozu v hledání protokolů](media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsg-rule-hits"></a>Vizualizace trendy v přístupů pravidla NSG

**Hledat**

- Skupina NSG nebo pravidlo, které má nejvíce přístupů?
- Jaké jsou hlavní dvojice konverzace zdrojové a cílové na skupinu NSG?

    ![Řídicí panel předvádění NSG dotkne statistiky](media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Tento čas zobrazit obrázky trendů pro přístupů pravidla NSG a zdroj cíl toku podrobnosti skupinu zabezpečení sítě:

    ![Předvádění čas trendů pro přístupů pravidla NSG a nejvyšší pravidla NSG](media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Pravidla NSG nejvyšší statistiky podrobnosti v protokolu hledání](media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Získejte odpovědi na časté otázky, najdete v tématu [provozu analytics – nejčastější dotazy](traffic-analytics-faq.md).
