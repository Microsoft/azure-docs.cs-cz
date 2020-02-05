---
title: Monitorování připojení (Preview) | Microsoft Docs
description: Naučte se používat monitorování připojení (Preview) k monitorování síťové komunikace v distribuovaném prostředí.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: c993a08a4163d50a9632055da355e39b5bdde004
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026884"
---
# <a name="overview"></a>Přehled

Monitor připojení (Preview) poskytuje sjednocené možnosti monitorování připojení v Azure Network Watcher pro hybridní a cloudová nasazení Azure. Azure Network Watcher poskytuje nástroje pro monitorování, diagnostiku a zobrazení metrik souvisejících s připojením pro vaše nasazení Azure.

Případy použití klíče:

- Máte virtuální počítač s front-end webovým serverem, který komunikuje s virtuálním počítačem s databázovým serverem v vícevrstvé aplikaci. Chcete kontrolovat síťové připojení mezi těmito dvěma virtuálními počítači.
- Chcete, aby se virtuální počítače v Východní USA oblasti pro virtuální počítače s příkazy otestujte v Střed USA oblasti a porovnávají latence sítě mezi oblastmi.
- Máte několik místních webů Office v městech, jako je Praha. připojování k adresám URL sady Office 365. Chcete porovnat latence uživatelů pomocí adres URL Office 365 z Seattle a Ashburn.
- Máte nastavenou hybridní aplikaci, která potřebuje připojení ke koncovému bodu Azure Storage. Chcete porovnat latence mezi místní lokalitou a aplikací Azure, které se připojují ke stejnému koncovému bodu Azure Storage.
- Chcete kontrolovat připojení z virtuálních počítačů Azure, které hostují vaši cloudovou aplikaci, do místních instalačních nastavení.

V této fázi Preview řešení přináší dohromady nejlepší ze dvou klíčových funkcí – Network Watcher monitorování připojení a Network Performance Monitor (NPM) monitorování [připojení](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) [služby](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity).

Růžov

* Jednotné a intuitivní prostředí pro potřeby Azure a hybridní monitorování
* Monitorování připojení mezi oblastmi a mezi jednotlivými pracovními prostory
* Vyšší četnosti zjišťování a lepší přehled o výkonu sítě
* Rychlejší upozorňování pro hybridní nasazení
* Podpora pro kontroly připojení na základě protokolu HTTP, TCP a ICMP
* Metriky a Log Analytics podporu pro nastavení testů pro Azure i pro jiné platformy než Azure

![Monitorování připojení](./media/connection-monitor-2-preview/hero-graphic.png)

Pomocí níže uvedeného postupu spusťte monitorování pomocí monitorování připojení (Preview).

## <a name="step-1-install-monitoring-agents"></a>Krok 1: instalace agentů monitorování

Monitorování připojení spoléhá na zjednodušené spustitelné soubory, aby se spouštěly kontroly připojení.  Podporujeme kontroly připojení z Azure i z místního prostředí. Konkrétní spustitelný soubor, který se má použít, závisí na tom, jestli je váš virtuální počítač hostovaný v Azure nebo v místním prostředí.

### <a name="agents-for-azure-virtual-machines"></a>Agenti pro virtuální počítače Azure

Aby mohl monitor připojení rozpoznat vaše virtuální počítače Azure jako zdroj pro monitorování, musíte na ně nainstalovat rozšíření virtuálního počítače Network Watcher agenta (označované také jako Network Watcher rozšíření). Rozšíření agenta Network Watcher je požadavek, který aktivuje koncové monitorování a další pokročilé funkce na virtuálních počítačích Azure. Můžete [vytvořit virtuální počítač a nainstalovat Network Watcher rozšíření](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[.](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)  Můžete také nainstalovat, nakonfigurovat a řešit potíže s rozšířením Network Watcher pro systémy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) a [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) samostatně.

Pokud NSG nebo pravidla brány firewall blokují komunikaci mezi zdrojem a cílem, monitorování připojení zjistí problém a zobrazí ho jako diagnostickou zprávu v topologii. Pokud chcete povolit monitorování připojení, zajistěte, aby NSG a pravidla brány firewall povolovaly pakety přes TCP nebo ICMP mezi zdrojem a cílem.

### <a name="agents-for-on-premise-machines"></a>Agenti pro místní počítače

Aby mohl monitor připojení rozpoznat místní počítače jako zdroje pro monitorování, je nutné nainstalovat agenta Log Analytics do počítačů a povolit řešení pro monitorování výkonu sítě. Tito agenti jsou propojeni s Log Analytics pracovními prostory a musí mít nastavené ID pracovního prostoru a primární klíč, aby mohli začít sledovat.

Chcete-li nainstalovat agenta Log Analytics pro počítače se systémem Windows, postupujte podle pokynů uvedených v [tomto odkazu](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows) .

Ujistěte se, že cíl je dosažitelný, pokud v cestě existují brány firewall nebo zařízení virtuální sítě (síťové virtuální zařízení).

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>Krok 2: povolení Network Watcher v předplatném

Všechna předplatná s virtuální sítí jsou povolená pomocí Network Watcher. Když ve svém předplatném vytvoříte virtuální síť, Network Watcher se automaticky povolí v této oblasti a předplatném Virtual Network. Pro automatické povolování Network Watcher neexistuje žádný vliv na vaše prostředky ani související poplatky. Ujistěte se, že ve vašem předplatném není explicitně zakázaná Network Watcher. Další informace najdete v tématu [povolení Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="step-3-create-connection-monitor"></a>Krok 3: vytvoření monitorování připojení 

_Monitorování připojení_ monitoruje komunikaci v pravidelných intervalech a informuje o změnách dostupnosti, latence a síťové topologie mezi zdrojovými agenty a cílovými koncovými body. Zdroji můžou být virtuální počítače Azure nebo místní počítače, které mají nainstalovaného agenta monitorování. Cílovými koncovými body můžou být adresy URL pro Office 365, adresy URL Dynamics 365, vlastní adresy URL, ID prostředků virtuálních počítačů Azure, IPv4, IPv6, plně kvalifikovaný název domény nebo libovolný název domény.

### <a name="accessing-connection-monitor-preview"></a>Přístup k monitorování připojení (Preview)

1. Přístup k Network Watcher pomocí následujícího odkazu:[https://ms.portal.azure.com/?Microsoft\_Azure\_Network\_connectionmonitorpreview=true#blade/Microsoft\_Azure\_Network/NetworkWatcherMenuBlade/connectionMonitorPreview](https://ms.portal.azure.com/?Microsoft_Azure_Network_connectionmonitorpreview=true#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/connectionMonitorPreview)
2. V levém podokně Network Watcher v části monitorování klikněte na kartu monitorování připojení (Preview). Tato karta bude viditelná pouze v případě, že k Network Watcher přistupovali pomocí odkazu uvedeného v kroku 1.
3. Můžete zobrazit všechna monitorování připojení, která jsou vytvořena pomocí prostředí monitorování připojení (Preview). Všechna monitorování připojení vytvořená pomocí klasického prostředí na kartě monitorování připojení se zobrazí na kartě monitorování připojení.

    ![Vytvoření monitorování připojení](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Vytvoření monitorování připojení

Monitory připojení vytvořené pomocí monitorování připojení (Preview) poskytují možnost přidat místní virtuální počítače i virtuální počítače Azure jako zdroje a monitorovat připojení ke koncovým bodům, což může zahrnovat Azure nebo jinou adresu URL/IP.

Níže jsou uvedené entity v monitorování připojení:

* Prostředek sledování připojení – prostředek Azure specifický pro oblast. Všechny níže uvedené entity jsou vlastnosti prostředku monitorování připojení.
* Koncové body – všechny zdroje a cíle, které se účastní kontrol připojení, se nazývají koncové body. Příklady koncového bodu – virtuální počítače Azure, místní agenti, adresy URL a IP adresy
* Konfigurace testu – Každá konfigurace testu je specifická pro protokol. Na základě zvoleného protokolu můžete definovat port, prahové hodnoty, frekvenci testování a další parametry.
* Testovací skupina – každá testovací skupina obsahuje zdrojové koncové body, cílové koncové body a konfigurace testu. Každé monitorování připojení může obsahovat více než jednu skupinu testů.
* Test – kombinace zdrojového koncového bodu, cílového koncového bodu a konfigurace testu vytvoří jeden test. Test má nejnižší úroveň, při které jsou k dispozici data monitorování (kontroly, které selhaly, a čas RTT).

 ![Vytvoření monitorování připojení](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>Z portálu

Chcete-li vytvořit monitorování připojení, postupujte podle následujících kroků uvedených níže:

1. V řídicím panelu Monitor připojení (Preview) klikněte na vytvořit v levém horním rohu.
2. Na kartě Basic zadejte informace pro monitorování připojení.
   1. Název sledování připojení – název monitoru připojení. Tady se vztahují standardní pravidla pojmenování pro prostředky Azure.
   2. Předplatné – vyberte předplatné pro monitorování připojení.
   3. Oblast – vyberte oblast pro prostředek monitorování připojení. Můžete vybrat jenom zdrojové virtuální počítače, které jsou vytvořené v této oblasti.
   4. Konfigurace pracovního prostoru – k uložení dat monitorování můžete použít výchozí pracovní prostor vytvořený monitorováním připojení, a to kliknutím na výchozí zaškrtávací políčko. Pokud chcete zvolit vlastní pracovní prostor, zrušte toto políčko. Zvolte předplatné a oblast a zvolte pracovní prostor, který bude obsahovat data monitorování.
   5. Kliknutím na další: testovací skupiny přidejte testovací skupiny.

      ![Vytvoření monitorování připojení](./media/connection-monitor-2-preview/create-cm-basics.png)

3. Na kartě skupiny testů klikněte na + testovací skupina a přidejte testovací skupinu. Pomocí _vytváření testovacích skupin v monitorování připojení_ můžete přidat testovací skupiny. Kliknutím na zkontrolovat + vytvořit zkontrolujte monitorování připojení.

   ![Vytvoření monitorování připojení](./media/connection-monitor-2-preview/create-tg.png)

4. Na kartě Revize + vytvořit zkontrolujte základní informace a skupiny testů před vytvořením monitorování připojení. Úprava monitorování připojení ze zobrazení "revize + vytvořit":
   1. Chcete-li upravit základní podrobnosti, použijte ikonu tužky, jak je uvedeno v poli 1 v obrázku 2.
   2. Chcete-li upravit jednotlivé testovací skupiny, klikněte na testovací skupinu, kterou chcete upravit, a otevřete testovací skupinu v režimu úprav.
   3. Aktuální náklady za měsíc ukazují náklady ve verzi Preview. Za použití monitorování připojení se momentálně neúčtují žádné poplatky, takže v tomto sloupci se zobrazí nula. Skutečné náklady za měsíc ukazují cenu, která se bude účtovat po všeobecné dostupnosti. Poznámka: poplatky za ingestování Log Analytics se použijí i ve verzi Preview.

5. Na kartě Revize + vytvořit kliknutím na tlačítko vytvořit vytvořte monitorování připojení.

   ![Vytvoření monitorování připojení](./media/connection-monitor-2-preview/review-create-cm.png)

6.  Monitorování připojení (Preview) vytvoří prostředek monitorování připojení na pozadí.

#### <a name="from-armclient"></a>Z Armclient

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Příkaz nasazení:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Vytváření testovacích skupin v monitorování připojení

Každá testovací skupina v monitorování připojení zahrnuje zdroje a cíle, které se testují v síťových parametrech kontrol, které selhaly, a čas od konfigurace testu.

#### <a name="from-portal"></a>Z portálu

Chcete-li vytvořit testovací skupinu v monitorování připojení, zadejte hodnotu pro následující uvedená pole:

1. Zakázat testovací skupinu – zaškrtnutím tohoto políčka zakážete monitorování pro všechny zdroje a cíle zadané ve skupině testů. Tato možnost se ve výchozím nastavení nekontroluje.
2. Název – název testovací skupiny
3. Zdroje – pokud jsou agenti nainstalováni, můžete zadat jak virtuální počítače Azure, tak i místní počítače jako zdroje. Pokud chcete nainstalovat agenta specifického pro váš zdroj, podívejte se na krok 1.
   1. Kliknutím na kartu agenti Azure vyberte agenty Azure. Zobrazí se jenom ty virtuální počítače, které jsou svázané s oblastí, kterou jste zadali v době vytváření monitorování připojení. Virtuální počítače jsou ve výchozím nastavení seskupené do předplatného, ke kterému patří, a skupiny se sbalí. Můžete přejít k podrobnostem z úrovně předplatného na jiné úrovně v hierarchii:

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      Můžete také změnit hodnotu pole Seskupit podle, aby se strom spouštěl z jiné úrovně. Například: Group by – VNET zobrazí virtuální počítače s agenty v hierarchii virtuální sítě-\&gt; Podsítě –\&gt; Virtuální počítače s agenty.

      ![Přidat zdroje](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Pokud chcete vybrat místní agenty, klikněte na kartu non-Azure agenti. Ve výchozím nastavení vidíte, že se agenti v oblasti seskupují do pracovních prostorů. V seznamu se zobrazí jenom ty pracovní prostory, které mají nakonfigurované řešení Network Performance Monitor. Přidejte řešení Network Performance Monitor do svého pracovního prostoru z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Můžete také použít proces popsaný v tématu [přidání Azure monitor řešení z galerie řešení](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) . Ve výchozím nastavení uvidíte oblast vybranou na kartě Základní informace v zobrazení sledování připojení. Můžete změnit oblast a zvolit možnost agenti z pracovních prostorů z nově vybrané oblasti. Můžete také změnit hodnotu pole Seskupit podle na skupiny v podsítích.

      ![Zdroje mimo Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Pokud chcete zkontrolovat, že jste vybrali Azure a agenty mimo Azure, klikněte na zkontrolovat.

      ![Kontrola zdrojů](./media/connection-monitor-2-preview/review-sources.png)

   4. Až skončíte s výběrem zdrojů, klikněte na Hotovo.

4. Cílová umístění – můžete sledovat připojení k virtuálním počítačům Azure nebo libovolnému koncovému bodu (veřejné IP adresy, adresu URL, plně kvalifikovaný název domény) jejich zadáním jako cíle. V jedné skupině testů můžete přidat virtuální počítače Azure, adresy URL O365, adresy URL D365 nebo vlastní koncové body.

   1. Klikněte na kartu virtuální počítače Azure a vyberte virtuální počítače Azure jako cíle. Ve výchozím nastavení se virtuální počítače Azure seskupují do hierarchie předplatných ve stejné oblasti, která byla vybrána na kartě Základní informace v zobrazení sledování připojení. Můžete změnit oblast a zvolit virtuální počítače Azure z nově vybrané oblasti. Můžete přejít k podrobnostem z úrovně předplatného na jiné úrovně v hierarchii, jako jsou třeba agenti Azure.

      ![Přidat cíle](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![Přidat cíle 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Kliknutím na kartu koncové body vyberte virtuální počítače Azure jako cíle. Seznam koncových bodů se naplní pomocí adres URL testů O365 a D365, seskupených podle názvu.  Můžete také zvolit koncový bod vytvořený v jiných testovacích skupinách ve stejném monitorování připojení. Pokud chcete přidat nový koncový bod, klikněte na + koncový bod z pravého horního rohu obrazovky a zadejte adresu URL/název koncového bodu/IP adresy/plně kvalifikovaný název domény a název.

      ![Přidat koncové body](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Pokud chcete zkontrolovat, že jste vybrali Azure a agenty mimo Azure, klikněte na zkontrolovat.
   4. Až skončíte s výběrem zdrojů, klikněte na Hotovo.

5. Konfigurace testu – můžete přidružit libovolný počet testovacích konfigurací v dané testovací skupině. Portál ho omezuje na jednu konfiguraci testu na skupinu testů, ale k přidání dalších použijte Armclient.
   1. Název – název pro konfiguraci testu
   2. Protokol – můžete si vybrat mezi TCP, ICMP nebo HTTP. Pokud chcete změnit protokol HTTP na HTTPS, vyberte HTTP as Protocol a 443 as port.
   3. Vytvořit konfiguraci testu sítě – toto zaškrtávací políčko se zobrazí jenom v případě, že v poli protokol vyberete HTTP. Povolením tohoto pole vytvoříte další konfiguraci testu pomocí stejných zdrojů a cílů, které jsou zadány v kroku 3 a 4 přes protokol TCP/ICMP. Nově vytvořená konfigurace testu se jmenuje "\&lt; název zadaný v 5. a\&gt;\_networkTestConfig "
   4. Disable traceroute – toto pole bude platit pro testovací skupiny s TCP nebo ICMP jako protokol.  Toto pole můžete zaškrtnout, pokud chcete zastavit zdroje z zjišťování topologie a doby odezvy směrování na segment směrování.
   5. Cílový port – toto pole můžete přizpůsobit tak, aby se umístilo do cílového portu dle vašeho výběru.
   6. Frekvence testování – toto pole určuje, jak často se budou zdroje testovat na základě protokolu a portu uvedeného výše. Můžete si vybrat v rozmezí 30 sekund, 1 minuta, 5 minut, 15 minut a 30 minut. Zdroje budou testovat připojení k cílům na základě hodnoty, kterou zvolíte.  Pokud například vyberete 30 sekund, zdroje budou kontrolovat připojení k cílovému umístění aspoň jednou za 30 sekund – období.
   7. Prahové hodnoty stavu – můžete nastavit prahové hodnoty pro parametry sítě uvedené níže.
      1. Kontroly se nezdařily v%-procento kontrol se nezdařila, pokud zdroje kontrolují připojení k cíli přes kritéria uvedená výše. U protokolu TCP/ICMP můžou být kontroly, které selhaly v%, stejné jako procento ztráty paketů. V případě protokolu HTTP toto pole představuje počet požadavků HTTP, které neobdržely odpověď.
      2. Čas RTT v milisekundách – doba odezvy v milisekundách, kdy se zdroje připojí k cíli přes konfiguraci testu zadanou výše.

      ![Přidat TG](./media/connection-monitor-2-preview/add-test-config.png)

Všechny zdroje a cíle přidané do testovací skupiny se zadaným testem konfigurace se rozdělují na jednotlivé testy. Příklad:

* Testovací skupina: TG1
* Zdroje: 3 (A, B, C)
* Cílová umístění: 2 (D, E)
* Konfigurace testu: 2 (konfigurace 1, konfigurace 2)
* Vytvořené testy: celkem = 12

| **Testovací číslo** | **Zdroj** | **Tabulka** | **Název konfigurace testu** |
| --- | --- | --- | --- |
| 1\. místo | A | D | Konfigurace 1 |
| 2 | A | D | Konfigurace 2 |
| 3 | A | E | Konfigurace 1 |
| 4 | A | E | Konfigurace 2 |
| 5 | B | D | Konfigurace 1 |
| 6 | B | D | Konfigurace 2 |
| 7 | B | E | Konfigurace 1 |
| 8 | B | E | Konfigurace 2 |
| 9 | C | D | Konfigurace 1 |
| 10 | C | D | Konfigurace 2 |
| 11 | C | E | Konfigurace 1 |
| 12 | C | E | Konfigurace 2 |

### <a name="scale-limits"></a>Omezení škálování

* Maximální počet monitorování připojení na předplatné na oblast – 100
* Maximální počet testovacích skupin na monitorování připojení – 20
* Maximální počet zdrojů + cíle na monitorování připojení – 100
* Maximální počet konfigurací testu na monitorování připojení – 20 přes Armclient. 2 přes portál.

## <a name="step-4--data-analysis-and-alerts"></a>Krok 4: Analýza dat a výstrahy

Po vytvoření monitoru připojení budou zdroje kontrolovat připojení k cílům na základě zadané konfigurace testu.

### <a name="checks-in-a-test"></a>Zkontroluje test.

Na základě protokolu vybraného uživatelem v konfiguraci testu spouští nástroj Connection monitor (Preview) sérii kontrol pro dvojici zdrojového cíle v rámci zvolené frekvence testování.

Pokud je vybrána možnost HTTP, služba vypočítá počet odpovědí HTTP, které vrátily kód odpovědi, aby bylo možné zjistit, zda došlo k chybě%.  Chcete-li vypočítat dobu odezvy, změřte čas potřebný k přijetí odpovědi na volání HTTP.

Pokud je vybrána možnost TCP nebo ICMP, služba vypočítá paket% a určí, zda došlo k chybě%. Pokud chcete vypočítat dobu odezvy, změřte čas potřebný k přijetí potvrzení k odeslání paketů. Pokud jste povolili traceroute data pro testy sítě, můžete zobrazit ztrátu směrování a latenci pro vaši místní síť.

### <a name="states-of-a-test"></a>Stavy testu

Na základě dat vrácených kontrolou v testu může každý test mít následující stavy:

* Pass = když skutečné hodnoty pro kontroly selhaly% a čas RTT je v rámci zadaných prahových hodnot.
* Selhání = Pokud skutečné hodnoty pro kontroly nevyhověly hodnotě% nebo hodnoty RTT po zadaném překročení prahových hodnot. Pokud není zadána žádná prahová hodnota, je test označen jako neúspěšný, pokud se kontroly nezdaří% = 100%
* Upozornění – Pokud nejsou zadaná kritéria pro kontroly, které selhaly:%. V takovém případě monitorování připojení (Preview) používá kritérium automatické sady jako prahovou hodnotu a pokud je tato prahová hodnota porušení stavu testu nastavená na "Warning".

### <a name="data-collection-analysis-and-alerts"></a>Shromažďování, analýza a výstrahy dat

Všechna data shromážděná monitorováním připojení (Preview) se ukládají v pracovním prostoru Log Analytics nakonfigurovaném v době vytváření monitorování připojení. Data monitorování jsou také k dispozici v Azure Monitor metriky. Pomocí Log Analytics můžete uchovávat data o monitorování, pokud budete chtít, ale Azure Monitor ukládá metriky standardně po dobu 30 dnů **.** Pak můžete [pro data nastavit výstrahy založené na metrikách](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Monitorování řídicích panelů v řešení monitorování připojení

Zobrazí se seznam monitorů připojení, ke kterému máte přístup, pro určitý výběr předplatných, oblastí, časových razítek, zdrojů a cílových typů.

Když přejdete na monitorování připojení (Preview) z Network Watcher služby, můžete si **Zobrazit**:

* Monitorování připojení (výchozí) – seznam všech monitorování připojení vytvořených pro vybraná předplatná, oblasti, časová razítka, zdrojové a cílové typy
* Testovací skupiny – seznam všech testovacích skupin vytvořených pro vybraná předplatná, oblasti, časová razítka, zdrojové a cílové typy. Tyto testovací skupiny nejsou filtrovány při monitorování připojení.
* Testy – seznam všech testů spuštěných pro vybraná předplatná, oblasti, časová razítka, zdrojové a cílové typy. Tyto testy nejsou filtrovány na monitoru připojení nebo ve skupinách testů.

Každé monitorování připojení můžete rozbalit do skupin testů a každé testovací skupiny do různých jednotlivých testů, které jsou v řídicím panelu spuštěny. Označeno jako [1] na obrázku níže.

Tento seznam můžete filtrovat podle těchto:

* Filtry nejvyšší úrovně – odběry, oblasti, zdroj časových razítek a cílové typy. Označeno jako [2] na obrázku níže.
* Filtry založené na stavu – filtr druhé úrovně na stav monitorování připojení/skupina testů/test. Označeno jako [3] na obrázku níže.
* Vyhledávací pole – pro obecné vyhledávání vyberte možnost vše. Chcete-li vyhledat konkrétní entitu, použijte rozevírací seznam k zúžení výsledků hledání. Označeno jako [4] na obrázku níže.

![Filtrovat testy](./media/connection-monitor-2-preview/cm-view.png)

Příklad:

1. Chcete-li zobrazit všechny testy v rámci monitorování připojení (Preview), kde source IP = 10.192.64.56:
   1. Změnit zobrazení na "testy"
   2. Hledání zařazeno = 10.192.64.56
   3. Pomocí rozevíracího seznamu vedle hodnoty vyberte zdroje.
2. Chcete-li odfiltrovat pouze neúspěšné testy v rámci monitorování připojení (Preview), kde source IP = 10.192.64.56
   1. Změnit zobrazení na "testy"
   2. Z filtrů založených na stavu vyberte "selhání".
   3. Vyhledávací pole = 10.192.64.56
   4. Pomocí rozevíracího seznamu vedle hodnoty vyberte zdroje.
3. Odfiltrování neúspěšných testů ve všech monitorech připojení (Preview), kde je cíl outlook.office365.com
   1. Změnit zobrazení na "testy"
   2. Z filtrů založených na stavu vyberte "selhání".
   3. Vyhledávací pole = outlook.office365.com
   4. Pomocí rozevíracího seznamu vedle hodnoty vyberte "cíle".

   ![Neúspěšné testy](./media/connection-monitor-2-preview/tests-view.png)

Pro zobrazení trendů kontrol se nezdařila% a RTT pro:

1. Monitorování připojení
   1. Klikněte na monitorování připojení, které chcete prozkoumat podrobněji.
   2. Ve výchozím nastavení zobrazíte data monitorování podle "testovacích skupin".

      ![Zobrazit metriky podle](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Vyberte testovací skupinu, kterou chcete prozkoumat podrobněji.

      ![Metriky podle TG](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Pro testovací skupinu, kterou jste zvolili v předchozím kroku, se zobrazí prvních 5 neúspěšných testů při kontrolách% nebo RTT MS. U každého testu se zobrazí spojnice trendu pro kontroly, které selhaly% a čas RTT MS.
   5. Vyberte test z výše uvedeného seznamu nebo zvolte jiný test, abyste prošetřili podrobnosti.
   6. Pro vybrané časové období se pro kontroly, které selhaly%, zobrazí prahová hodnota a skutečné hodnoty. Pro čas RTT MS se zobrazí hodnoty prahová hodnota, průměr, minimum a maximum.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Změna časového intervalu pro zobrazení více dat
  8. Můžete změnit zobrazení v kroku b a zvolit zobrazení podle zdrojů, cílů nebo konfigurací testů. Pak zvolte zdroj založený na neúspěšných testech a prozkoumejte prvních 5 neúspěšných testů.  Příklad: vyberte možnost Zobrazit podle: zdroje a cíle a prozkoumejte všechny testy, které jsou spouštěny mezi touto kombinací v rámci vybraného monitorování připojení.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Testovací skupina
   1. Klikněte na testovací skupinu, kterou chcete prozkoumat podrobněji.
   2. Ve výchozím nastavení zobrazíte data monitorování pomocí "zdroj + cíl + konfigurace testu (test)"

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Vyberte test, který chcete prozkoumat podrobněji.
   4. Pro vybrané časové období se pro kontroly, které selhaly%, zobrazí prahová hodnota a skutečné hodnoty. Pro čas RTT MS se zobrazí hodnoty prahová hodnota, průměr, minimum a maximum. Zobrazí se také aktivované výstrahy, které jsou specifické pro vybraný test.
   5. Změna časového intervalu pro zobrazení více dat
   6. Můžete změnit zobrazení v kroku b a zvolit zobrazení podle zdrojů, cílů nebo konfigurací testů. Pak zvolte entitu k prozkoumání prvních 5 neúspěšných testů.  Příklad: vyberte možnost Zobrazit podle: zdroje a cíle a prozkoumejte všechny testy, které jsou spouštěny mezi touto kombinací v rámci vybraného monitorování připojení.

3. Testování
   1. Klikněte na zdroj + cíl + konfigurace testu, které chcete prozkoumat podrobněji.
   2. Pro vybrané časové období se pro kontroly, které selhaly%, zobrazí prahová hodnota a skutečné hodnoty. Pro čas RTT MS se zobrazí hodnoty prahová hodnota, průměr, minimum a maximum. Zobrazí se také aktivované výstrahy, které jsou specifické pro vybraný test.

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. Můžete také kliknout na topologie a zobrazit topologii sítě v jakémkoli okamžiku.

      ![Test2](./media/connection-monitor-2-preview/test-topo.png)

   4. Kliknutím na libovolný segment směrování v síti Azure můžete zobrazit problémy zjištěné monitorováním připojení. Tato možnost není v současnosti k dispozici pro místní sítě.

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Dotazy protokolu v Azure Monitor Log Analytics

Pomocí Log Analytics můžete vytvořit vlastní zobrazení dat monitorování. Všechna data zobrazená v uživatelském rozhraní jsou vyplněna z Log Analytics. Můžete provádět interaktivní analýzu dat v úložišti a korelovat data z různých zdrojů, jako jsou stav agenta a jiné aplikace založené na Log Analytics. Data můžete také exportovat do aplikace Excel, Power BI nebo odkaz na sdílení.

#### <a name="metrics-in-azure-monitor"></a>Metriky na platformě Azure Monitor

Pro monitorování připojení, které bylo vytvořeno před prostředím monitorování připojení (Preview), budou k dispozici všechny 4 metriky. Pro monitorování připojení vytvořené prostřednictvím prostředí monitorování připojení (Preview) budou data dostupná jenom pro metriky označené jako (Preview).

Typ prostředku – Microsoft. Network/networkWatchers/connectionMonitors

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % PROBE selhalo | Procento | Průměr | % sond monitorování připojení selhalo. | Žádné dimenze |
| AverageRoundtripMs | Průměrná doba odezvy (MS) | Milisekund | Průměr | Průměrná doba odezvy sítě (MS) pro testy monitorování připojení odesílané mezi zdrojem a cílem |             Žádné dimenze |
| ChecksFailedPercent (Preview) | % Kontroly selhaly (Preview) | Procento | Průměr | % kontrol se pro test nezdařila. |Seznam:-ConnectionMonitorResourceId-SourceAddress-source-parametr sourceresourceid-SourceType-Protocol-DestinationAddress-Destination-DestinationResourceId-destinationType-DestinationPort-TestGroupName-TestConfigurationName- Věřitel |
| RoundTripTimeMs (Preview) | Doba odezvy (MS) (Preview) | Milisekundy | Průměr | Doba odezvy (MS) pro kontroly odeslané mezi zdrojem a cílem. Tato hodnota není Průměrná | Seznam:-ConnectionMonitorResourceId-SourceAddress-source-parametr sourceresourceid-SourceType-Protocol-DestinationAddress-Destination-DestinationResourceId-destinationType-DestinationPort-TestGroupName-TestConfigurationName- Věřitel |

 ![Monitorovat metriky](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Výstrahy metriky v Azure Monitor

Vytvoření výstrahy:

1. Vyberte prostředek monitoru připojení vytvořený pomocí monitorování připojení (Preview).
2. Zajistěte, aby se metrika zobrazila jako typ signálu pro prostředek vybraný v předchozím kroku.
3. V možnosti Přidat podmínku vyberte název signálu jako ChecksFailedPercent (Preview) nebo RoundTripTimeMs (Preview) a typ signálu jako metriky. Například: Choose ChecksFailedPercent (Preview)
4. V seznamu se zobrazí všechny dimenze, které platí pro jednotlivé metriky.  Vyberte název dimenze a hodnotu dimenze. Např.: vyberte zdrojovou adresu a zadejte IP adresu veškerého zdroje zahrnutého v prostředku sledování připojení, který jste zvolili v kroku 1.
5. V logice výstrahy vyberte:
   1. Typ podmínky – statický
   2. Podmínka a prahová hodnota
   3. Členitost agregace a frekvence hodnocení – monitorování připojení (Preview) aktualizuje data každé 1 minuty.
6.  V akcích vyberte skupinu akcí.
7. Zadat podrobnosti výstrahy
8. Vytvořit pravidlo výstrahy

   ![Výstrahy](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>Krok 5: Diagnostika problémů ve vaší síti

Monitorování připojení vám pomůže diagnostikovat problémy, které odpovídají prostředku monitorování připojení a ve vaší síti. Problémy ve vaší hybridní síti zjistí agenti Log Analytics, které jste nainstalovali v kroku 1, a rozšíření Network Watcher zjistí problémy v Azure.  Problémy v hybridní síti se zobrazí na stránce Diagnostika a problémy v síti Azure budou viditelné v topologii sítě.

Pro sítě s místními virtuálními počítači jako zdroji detekujeme:

* Vypršel časový limit žádosti.
* Koncový bod nevyřešila služba DNS – dočasné nebo trvalé. Adresa URL je neplatná.
* Nenašly se žádní hostitelé.
* Zdroj se nemůže připojit k cíli. Cíl není dosažitelný pomocí protokolu ICMP.
* Problém související s certifikátem – klientský certifikát vyžadovaný k ověření agenta, seznamu přemístění certifikátu není přístupný, název hostitele koncového bodu neodpovídá názvu předmětu nebo subjektu certifikátu. kořenový certifikát chybí v úložišti důvěryhodných certifikačních autorit místního počítače zdroje, vypršela platnost certifikátu SSL, neplatný/odvolaný, nekompatibilní.

Pro sítě s virtuálními počítači Azure se zjišťují tyto zdroje:

* Problémy agenta – Agent se zastavil, selhalo překlad DNS, na cílovém portu nenaslouchá žádná aplikace nebo naslouchací proces, takže nejde otevřít soket.
* Problémy s stavem virtuálního počítače – spuštění, zastavení, zastavení, zrušení přidělení, zrušení přidělení, restartování, nepřidělené
* Chybí položka tabulky ARP.
* Provoz blokovaný kvůli problémům s místní bránou firewall, NSG pravidly
* Brána virtuální sítě – chybějící trasy, tunelové propojení dvou bran je odpojené nebo chybí, nebo druhá brána nenalezena tunelem, nenašly se žádné informace o partnerském vztahu.
* Chybějící trasa v MS Edge.
* Provoz se zastavil kvůli systémovým trasám nebo UDR
* Protokol BGP není v připojení brány povolený.
* Sonda DIP dolů na Load Balancer
