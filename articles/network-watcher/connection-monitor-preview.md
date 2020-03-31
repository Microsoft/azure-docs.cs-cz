---
title: Monitor připojení (náhled) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí programu Sledování připojení (Preview) sledovat síťovou komunikaci v distribuovaném prostředí.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 8f3a6f002fbebe215699c9b97a6dce63177c446f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599324"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Monitorování připojení k síti pomocí monitoru připojení (náhled)

Sledování připojení (Preview) poskytuje jednotné monitorování připojení od konce v Azure Network Watcher. Funkce Sledování připojení (Preview) podporuje hybridní nasazení a cloudová nasazení Azure. Network Watcher poskytuje nástroje pro monitorování, diagnostiku a zobrazení metrik souvisejících s připojením pro vaše nasazení Azure.

Zde jsou některé případy použití pro sledování připojení (náhled):

- Váš front-end ový virtuální server komunikuje s virtuálním počítačem databázového serveru ve vícevrstvé aplikaci. Chcete zkontrolovat připojení k síti mezi dvěma virtuálními zařízeními.
- Chcete, aby virtuální servery v oblasti USA – východní USA byly příkazem ping na virtuální chod v oblasti Střední USA a chcete porovnat latence sítě mezi oblastmi.
- Máte několik místních kancelářských míst v Seattlu, Washingtonu a v Ashburnu ve Virginii. Vaše kancelářské weby se připojují k adresám URL Office 365. Pro uživatele adres URL Office 365 porovnejte latence mezi Seattlem a Ashburnem.
- Hybridní aplikace potřebuje připojení ke koncovému bodu Azure Storage. Místní web a vaše aplikace Azure se připojují ke stejnému koncovému bodu Azure Storage. Chcete porovnat latence místního webu s latencemi aplikace Azure.
- Chcete zkontrolovat připojení mezi místními nastaveními a virtuálními počítači Azure, které hostují vaši cloudovou aplikaci.

Ve fázi náhledu kombinuje nástroj Sledování připojení to nejlepší ze dvou funkcí: funkci [Sledování připojení](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) sledování sítě a funkci Sledování [připojení služby](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) Sledování výkonu sítě (NPM).

Zde jsou některé výhody sledování připojení (náhled):

* Jednotné a intuitivní prostředí pro potřeby azure a hybridního monitorování
* Monitorování připojení mezi oblastmi a mezi pracovními prostory
* Vyšší sondovací frekvence a lepší přehled o výkonu sítě
* Rychlejší upozorňování na hybridní nasazení
* Podpora kontrol připojení založených na protokolech HTTP, TCP a ICMP 
* Podpora metrik a analýzy protokolů pro testovací nastavení Azure i mimo Azure

![Diagram znázorňující interakci monitoru připojení s virtuálními počítači Azure, hostiteli, koncovými body a umístěními datových úložišť, které nejsou azure.](./media/connection-monitor-2-preview/hero-graphic.png)

Chcete-li začít používat sledování připojení (Preview) pro monitorování, postupujte takto: 

1. Nainstalujte monitorovací agenty.
1. Povolte sledování sítě v rámci předplatného.
1. Vytvořte monitor připojení.
1. Nastavte analýzu dat a výstrahy.
1. Diagnostikujte problémy v síti.

V následujících částech jsou uvedeny podrobnosti pro tyto kroky.

## <a name="install-monitoring-agents"></a>Instalace monitorovacích agentů

Sledování připojení spoléhá na zjednodušené spustitelné soubory pro spuštění kontrolpřipojení.  Podporuje kontroly připojení z prostředí Azure i místních prostředí. Spustitelný soubor, který používáte, závisí na tom, jestli je váš virtuální počítač hostovaný v Azure nebo místně.

### <a name="agents-for-azure-virtual-machines"></a>Agenti pro virtuální počítače Azure

Chcete-li, aby nástroj Sledování připojení rozpoznal vaše virtuální počítače Azure jako zdroje monitorování, nainstalujte na ně rozšíření virtuálního počítače Sledování sítě. Toto rozšíření je také známé jako *rozšíření Sledování sítě*. Virtuální počítače Azure vyžadují rozšíření pro aktivaci monitorování od konce a další pokročilé funkce. 

Rozšíření Sledování sítě můžete nainstalovat při [vytváření virtuálního virtuálního soudu](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). Můžete také samostatně nainstalovat, nakonfigurovat a řešit potíže s rozšířením Network Watcher pro [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) a [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

Pravidla pro skupinu zabezpečení sítě (NSG) nebo bránu firewall mohou blokovat komunikaci mezi zdrojem a cílem. Nástroj Sledování připojení zjistí tento problém a zobrazí jej jako diagnostickou zprávu v topologii. Chcete-li povolit monitorování připojení, ujistěte se, že pravidla nsg a brány firewall umožňují pakety přes Protokol TCP nebo ICMP mezi zdrojem a cílem.

### <a name="agents-for-on-premises-machines"></a>Zástupci místních strojů

Chcete-li, aby nástroj Sledování připojení rozpoznal vaše místní počítače jako zdroje pro monitorování, nainstalujte do počítačů agenta Log Analytics. Poté povolte řešení Sledování výkonu sítě. Tito agenti jsou propojeni s pracovními prostory Log Analytics, takže je třeba nastavit ID pracovního prostoru a primární klíč, než agenti mohou začít monitorování.

Informace o instalaci agenta Log Analytics pro počítače s Windows najdete v [tématu Rozšíření virtuálního počítače Azure Monitor pro Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Pokud cesta obsahuje brány firewall nebo síťová virtuální zařízení (NVA), ujistěte se, že cíl je dosažitelný.

## <a name="enable-network-watcher-on-your-subscription"></a>Povolení sledování sítě v rámci předplatného

Všechna předplatná, která mají virtuální síť jsou povoleny s Network Watcher. Když vytvoříte virtuální síť v předplatném, sledovací modul sítě je automaticky povolen v oblasti a předplatné virtuální sítě. Toto automatické povolení nemá vliv na vaše prostředky ani na ně neúčtujepoplatek. Ujistěte se, že sledovací modul sítě není ve vašem předplatném explicitně zakázán. 

Další informace naleznete v [tématu Enable Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Vytvoření monitorování připojení 

Connection Monitor sleduje komunikaci v pravidelných intervalech. Informuje vás o změnách v dosažitelnosti a latenci. Můžete také zkontrolovat aktuální a historickou topologii sítě mezi zdrojovými agenty a cílovými koncovými body.

Zdroje můžou být virtuální počítače Azure nebo místní počítače, které mají nainstalovaného agenta monitorování. Cílovými koncovými body mohou být adresy URL Office 365, adresy URL Dynamics 365, vlastní adresy URL, ID prostředků virtuálních virtuálních počítače Azure, IPv4, IPv6, hlavní název domény nebo libovolný název domény.

### <a name="access-connection-monitor-preview"></a>Monitor připojení přístupu (náhled)

1. Na domovské stránce portálu Azure přejděte na **Sledování sítě**.
1. Vlevo vyberte v části **Monitorování** **monitor připojení (náhled).**
1. Zobrazí se všechny monitory připojení, které byly vytvořeny v programu Sledování připojení (Náhled). Chcete-li zobrazit monitory připojení, které byly vytvořeny v klasickém prostředí programu Sledování připojení, přejděte na kartu **Sledování připojení.**

    ![Snímek obrazovky s monitory připojení, které byly vytvořeny v programu Sledování připojení (preview)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Vytvoření monitorování připojení

V monitorování připojení, které vytvoříte v programu Sledování připojení (Preview), můžete jako zdroje přidat místní počítače i virtuální počítače Azure. Tyto monitory připojení můžete také sledovat připojení ke koncovým bodům. Koncové body mohou být v Azure nebo na jakékoli jiné adrese URL nebo IP adrese.

Sledování připojení (náhled) zahrnuje následující entity:

* **Prostředek monitorování připojení** – prostředek Azure specifický pro oblast. Všechny následující entity jsou vlastnosti prostředku monitorování připojení.
* **Koncový bod** – zdroj nebo cíl, který se účastní kontrolpřipojení. Příklady koncových bodů zahrnují virtuální počítače Azure, místní agenty, adresy URL a IP adresy.
* **Konfigurace testu** – konfigurace specifická pro protokol pro test. Na základě vybraného protokolu můžete definovat port, prahové hodnoty, frekvenci testu a další parametry.
* **Testovací skupina** – skupina, která obsahuje koncové body zdroje, cílové koncové body a testovací konfigurace. Monitor připojení může obsahovat více než jednu testovací skupinu.
* **Test** – kombinace koncového bodu zdroje, cílového koncového bodu a konfigurace testu. Test je nejpodrobnější úroveň, na které jsou k dispozici data monitorování. Data monitorování zahrnují procento kontrol, které se nezdařily, a dobu odezvy (RTT).

 ![Diagram znázorňující monitor připojení, který definuje vztah mezi testovacími skupinami a testy](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Vytvoření monitoru připojení z webu Azure Portal

Pokud chcete vytvořit monitorování připojení z webu Azure Portal, postupujte takto:

1. Na řídicím panelu **Sledování připojení (náhled)** v levém horním rohu vyberte **Vytvořit**.
1. Na kartě **Základy** zadejte informace pro monitor připojení:
   * **Název monitoru připojení** – přidejte název monitoru připojení. Použijte standardní pravidla pojmenování pro prostředky Azure.
   * **Předplatné** – Zvolte předplatné pro monitor připojení.
   * **Oblast** – Zvolte oblast pro monitor připojení. Můžete vybrat pouze zdrojové virtuální chody, které jsou vytvořeny v této oblasti.
   * **Konfigurace pracovního prostoru** – váš pracovní prostor obsahuje data monitorování. Můžete použít vlastní pracovní prostor nebo výchozí pracovní prostor. 
       * Chcete-li použít výchozí pracovní prostor, zaškrtněte políčko. 
       * Chcete-li vybrat vlastní pracovní plochu, zrušte zaškrtnutí políčka. Pak zvolte předplatné a oblast pro vlastní pracovní prostor. 
1. V dolní části karty vyberte **Další: Testovat skupiny**.

   ![Snímek obrazovky s kartou Základy v programu Sledování připojení](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Na kartě **Testovací skupiny** vyberte **+ Skupina testů**. Informace o nastavení testovacích skupin naleznete [v tématu Vytvoření testovacích skupin v programu Sledování připojení](#create-test-groups-in-a-connection-monitor). 
1. V dolní části karty vyberte **Další: Revize + vytvoření** a zkontrolujte monitor připojení.

   ![Snímek obrazovky s kartou Testovací skupiny a podoknem, do kterého přidáváte podrobnosti o testovací skupině](./media/connection-monitor-2-preview/create-tg.png)

1. Na kartě **Revize + vytvořit** si před vytvořením monitoru připojení prohlédněte základní informace a testovací skupiny. Pokud potřebujete upravit monitor připojení:
   * Chcete-li upravit základní podrobnosti, vyberte ikonu tužky.
   * Chcete-li upravit testovací skupinu, vyberte ji.

   > [!NOTE] 
   > Karta **Revize + vytvoření** zobrazuje náklady za měsíc během fáze náhledu sledování připojení. V současné době sloupec **AKTUÁLNÍ NÁKLADY** zobrazuje bez poplatku. Pokud je monitor připojení obecně dostupný, zobrazí se v tomto sloupci měsíční poplatek. 
   > 
   > I ve fázi náhledu sledování připojení se účtují poplatky za ingestování služby Log Analytics.

1. Až budete připraveni vytvořit monitor připojení, v dolní části karty **Revize + vytvoření** vyberte **Vytvořit**.

   ![Snímek obrazovky monitoru připojení s kartou Revize + vytvoření](./media/connection-monitor-2-preview/review-create-cm.png)

Sledování připojení (Náhled) vytvoří prostředek sledování připojení na pozadí.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Vytvoření monitoru připojení pomocí klienta ARMClient

Pomocí následujícího kódu vytvořte monitorování připojení pomocí příkazu ARMClient.

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

address: '\&lt;FQDN of your on-premises agent'

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

Tady je příkaz nasazení:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Vytvoření testovacích skupin na monitoru připojení

Každá testovací skupina na monitoru připojení obsahuje zdroje a cíle, které jsou testovány na parametrech sítě. Jsou testovány na procento kontrol, které se nezdaří a RTT přes testovací konfigurace.

Na webu Azure Portal můžete vytvořit testovací skupinu v monitoru připojení a zadat hodnoty pro následující pole:

* **Zakázat testovací skupinu** – toto pole můžete vybrat a zakázat monitorování pro všechny zdroje a cíle, které určuje testovací skupina. Tento výběr je ve výchozím nastavení vymazán.
* **Název** – Pojmenujte testovací skupinu.
* **Zdroje** – můžete určit virtuální počítače Azure a místní počítače jako zdroje, pokud agenti jsou nainstalované na nich. Informace o instalaci agenta zdroje naleznete v [tématu Instalace agentů monitorování](#install-monitoring-agents).
   * Pokud chcete vybrat agenty Azure, vyberte kartu **Agenti Azure.** Zde se zobrazí pouze virtuální společnosti, které jsou vázány na oblast, kterou jste zadali při vytváření monitoru připojení. Ve výchozím nastavení jsou virtuální aplikace seskupeny do předplatného, do kterého patří. Tyto skupiny jsou sbaleny. 
   
       Můžete přejít z úrovně předplatného na další úrovně v hierarchii:

      **Skupiny** >  > **prostředků předplatného**Virtuální sítě**VNET** > **s agenty** > **VMs with agents**

      Můžete také změnit hodnotu pole **Seskupit podle** a spustit strom z jakékoli jiné úrovně. Pokud například seskumíte podle virtuální sítě, uvidíte virtuální počítače, které mají agenty v hierarchii**virtuálních sítí** >  **VNETs** > **s agenty**.

      ![Snímek obrazovky monitoru připojení s panelem Přidat zdroje a kartou Agenti Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Pokud chcete vybrat místní agenty, vyberte kartu **Agenti jiného než Azure.** Ve výchozím nastavení jsou agenti seskupeni do pracovních prostorů podle oblasti. Všechny tyto pracovní prostory mají nakonfigurované řešení Sledování výkonu sítě. 
   
       Pokud potřebujete přidat nástroj Sledování výkonu sítě do pracovního prostoru, získejte ho z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Informace o tom, jak přidat sledování výkonu sítě, naleznete [v tématu Monitorování řešení v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       V zobrazení **Vytvořit sledování připojení** je na kartě **Základy** vybrána výchozí oblast. Pokud změníte oblast, můžete vybrat agenty z pracovních prostorů v nové oblasti. Můžete také změnit hodnotu **pole Seskupit** podle pole na seskupit podle podsítí.

      ![Snímek obrazovky monitoru připojení s panelem Přidat zdroje a kartou Agenti jiného než Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Pokud chcete zkontrolovat vybrané agenty Azure a agenty, kteří nejsou azure, přejděte na kartu **Revize.**

      ![Snímek obrazovky monitoru připojení s panelem Přidat zdroje a kartou Revize](./media/connection-monitor-2-preview/review-sources.png)

   * Po dokončení nastavení zdrojů vyberte v dolní části panelu **Přidat zdroje** **možnost Hotovo**.

* **Cíle** – můžete sledovat připojení k virtuálním počítačům Azure nebo libovolnému koncovému bodu (veřejná IP adresa, adresa URL nebo vícenežonisní program) zadáním jako cílů. V jedné testovací skupině můžete přidat virtuální počítače Azure, adresy URL Office 365, adresy URL Dynamics 365 a vlastní koncové body.

    * Pokud chcete jako cíle zvolit virtuální počítače Azure, vyberte kartu **Virtuální počítače Azure.** Ve výchozím nastavení jsou virtuální počítače Azure seskupeny do hierarchie předplatného, která je ve stejné oblasti, kterou jste vybrali v zobrazení **Vytvořit sledování připojení,** na kartě **Základy.** Můžete změnit oblast a zvolit virtuální počítače Azure z nově vybrané oblasti. Pak můžete přejít z úrovně předplatného na jiné úrovně v hierarchii, jako je úroveň Azure Agents.

       ![Snímek obrazovky podokna Přidat cíle s kartou Virtuální počítače Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Snímek obrazovky podokna Přidat cíle s úrovní předplatného](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Chcete-li jako cíle vybrat koncové body, vyberte kartu **Koncové body.** Seznam koncových bodů obsahuje testovací adresy URL Office 365 a testovací adresy URL Dynamics 365 seskupené podle názvu. Kromě těchto koncových bodů můžete zvolit koncový bod, který byl vytvořen v jiných testovacích skupinách ve stejném monitoru připojení. 
    
        Chcete-li přidat nový koncový bod, vyberte v pravém horním rohu **+ Koncové body**. Pak zadejte název koncového bodu a adresu URL, IP nebo fQDN.

       ![Snímek obrazovky znázorňující, kam přidat koncové body jako cíle v programu Sledování připojení](./media/connection-monitor-2-preview/add-endpoints.png)

    * Pokud chcete zkontrolovat virtuální počítače Azure a koncové body, které jste zvolili, vyberte kartu **Revize.**
    * Po dokončení výběru cílů vyberte **Hotovo**.

* **Konfigurace testů** – konfigurace testů můžete přidružit v testovací skupině. Portál Azure umožňuje pouze jednu konfiguraci testu na testovací skupinu, ale můžete použít ARMClient přidat další.

    * **Název** – Pojmenujte konfiguraci testu.
    * **Protokol** – zvolte protokol TCP, ICMP nebo HTTP. Chcete-li změnit protokol HTTP na protokol HTTPS, vyberte jako protokol **protokol protokol protokolu** a jako port vyberte **443.**
        * **Vytvořit konfiguraci síťového testu** – Toto políčko se zobrazí pouze v případě, že v poli Protokol vyberete **protokol HTTP.** **Protocol** Toto políčko zaškrtněte, chcete-li vytvořit jinou testovací konfiguraci, která používá stejné zdroje a cíle, které jste zadali jinde v konfiguraci. Nově vytvořená konfigurace `<the name of your test configuration>_networkTestConfig`testu je pojmenována .
        * **Zakázat trasování** – Toto pole se vztahuje na testovací skupiny, jejichž protokol je TCP nebo ICMP. Zaškrtnutím tohoto políčka zabráníte zdrojům v zjišťování topologie a rtt směrování.
    * **Cílový port** – Toto pole můžete přizpůsobit pomocí vybraného cílového portu.
    * **Frekvence testu** – Toto pole slouží k výběru četnosti cílů ping na zadaném protokolu a portu. Můžete si vybrat 30 sekund, 1 minutu, 5 minut, 15 minut nebo 30 minut. Zdroje otestují připojení k cílům na základě zvolené hodnoty.  Pokud například vyberete 30 sekund, zdroje zkontrolují připojení k cíli alespoň jednou za 30 sekund.
    * **Prahová hodnota úspěchu** – můžete nastavit prahové hodnoty pro následující síťové parametry:
       * **Kontroly se nezdařily** – Nastavte procento kontrol, které mohou selhat, když zdroje kontrolují připojení k cílům pomocí zadaných kritérií. U protokolu TCP nebo ICMP lze procento neúspěšných kontrol přirovnat k procentu ztráty paketů. Pro protokol HTTP toto pole představuje procento požadavků HTTP, které neobdržely žádnou odpověď.
       * **Doba odezvy** – Nastavte RTT v milisekundách, jak dlouho může trvat připojení zdrojů k cíli přes testovací konfiguraci.
    
       ![Snímek obrazovky s informacemi o tom, kde nastavit testovací konfiguraci v programu Sledování připojení](./media/connection-monitor-2-preview/add-test-config.png)

Všechny zdroje, cíle a testovací konfigurace, které přidáte do testovací skupiny, se rozdělí na jednotlivé testy. Zde je příklad toho, jak se zdroje a cíle člení:

* Zkušební skupina: TG1
* Zdroje: 3 (A, B, C)
* Destinace: 2 (D, E)
* Testovací konfigurace: 2 (Konfigurace 1, Konfigurace 2)
* Celkový počet vytvořených testů: 12

| Číslo zkoušky | Zdroj | Cíl | Konfigurace testu |
| --- | --- | --- | --- |
| 1 | A | D | Konfigurace 1 |
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

### <a name="scale-limits"></a>Limity stupnice

Monitory připojení mají následující omezení měřítka:

* Maximální počet monitorů připojení na jedno předplatné na oblast: 100
* Maximální počet testovacích skupin na monitor připojení: 20
* Maximální počet zdrojů a cílů na monitor připojení: 100
* Maximální konfigurace testu na monitor připojení: 
    * 20 přes ARMClient
    * 2 prostřednictvím portálu Azure

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analýza dat monitorování a nastavení výstrah

Po vytvoření monitorování připojení zdroje zkontrolujte připojení k cílům na základě konfigurace testu.

### <a name="checks-in-a-test"></a>Kontroly v testu

Na základě protokolu, který jste zvolili v konfiguraci testu, monitor připojení (Preview) spustí řadu kontrol pro dvojici zdroj-cíl. Kontroly se spustí podle testovací frekvence, kterou jste zvolili.

Pokud používáte protokol HTTP, služba vypočítá počet odpovědí HTTP, které vrátily kód odpovědi. Výsledek určuje procento neúspěšných kontrol. K výpočtu RTT služba měří čas mezi voláním HTTP a odpovědí.

Pokud použijete protokol TCP nebo ICMP, služba vypočítá procento ztráty paketu k určení procenta neúspěšných kontrol. Pro výpočet RTT služba měří čas přijatý k přijetí potvrzení (ACK) pro pakety, které byly odeslány. Pokud jste povolili traceroute data pro vaše testy sítě, můžete vidět hop-by-hop ztráty a latence pro místní síť.

### <a name="states-of-a-test"></a>Stavy zkoušky

Na základě dat, která kontroly vrátit, testy mohou mít následující stavy:

* **Pass** – skutečné hodnoty pro procento neúspěšných kontrol a RTT jsou v rámci zadaných prahových hodnot.
* **Selhání** – skutečné hodnoty pro procento neúspěšných kontrol nebo RTT překročilzadané prahové hodnoty. Pokud není zadána žádná prahová hodnota, pak test dosáhne stavu Selhání, pokud je procento neúspěšných kontrol 100.
* **Upozornění** – pro procento neúspěšných kontrol nebyla zadána žádná kritéria. Při absenci zadaných kritérií program Sledování připojení (náhled) automaticky přiřadí prahovou hodnotu. Při překročení této prahové hodnoty se stav testu změní na Upozornění.

### <a name="data-collection-analysis-and-alerts"></a>Shromažďování dat, analýza a výstrahy

Data, která shromažďuje program Sledování připojení (Preview), jsou uložena v pracovním prostoru Log Analytics. Tento pracovní prostor nastavíte při vytváření monitoru připojení. 

Data monitorování jsou taky dostupná v metrikách Azure Monitoru. Pomocí analýzy protokolů můžete uchovávat data monitorování tak dlouho, jak chcete. Azure Monitor ukládá metriky pro jenom 30 dní ve výchozím nastavení. 

Můžete [nastavit upozornění založená na metrikách na data](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Monitorování řídicích panelů

Na řídicích panelech monitorování se zobrazí seznam monitorů připojení, ke kterým máte přístup pro vaše předplatná, oblasti, časová razítka, zdroje a typy cílů.

Když přejdete na sledování připojení (preview) z sledovacího programu sítě, můžete zobrazit data pomocí:

* **Sledování připojení** – seznam všech monitorů připojení vytvořených pro vaše předplatná, oblasti, časová razítka, zdroje a cílové typy. Toto zobrazení je výchozí.
* **Testovací skupiny** – seznam všech testovacích skupin vytvořených pro vaše předplatná, oblasti, časová razítka, zdroje a typy cílů. Tyto testovací skupiny nejsou filtrovány pomocí monitorování připojení.
* **Test** – seznam všech testů, které běží pro vaše odběry, oblasti, časová razítka, zdroje a cílové typy. Tyto testy nejsou filtrovány pomocí monitorování připojení nebo testovacích skupin.

Na následujícím obrázku jsou tři zobrazení dat označena šipkou 1.

Na řídicím panelu můžete rozbalit každý monitor připojení a zobrazit jeho testovací skupiny. Pak můžete rozbalit každou testovací skupinu a zobrazit testy, které jsou v ní spuštěny. 

Seznam můžete filtrovat na základě:

* **Filtry nejvyšší úrovně** – zvolte předplatná, oblasti, zdroje časových razítek a typy cílů. Viz rámeček 2 na následujícím obrázku.
* **Filtry založené na stavu** – filtruje podle stavu monitoru připojení, testovací skupiny nebo testu. Viz šipka 3 na následujícím obrázku.
* **Vlastní filtry** – Zvolte **Vybrat vše,** chcete-li provést obecné vyhledávání. Chcete-li vyhledávat podle určité entity, vyberte z rozevíracího seznamu. Viz šipka 4 na následujícím obrázku.

![Snímek obrazovky znázorňující filtrování zobrazení monitorů připojení, testovacích skupin a testů v programu Sledování připojení (preview)](./media/connection-monitor-2-preview/cm-view.png)

Chcete-li například zobrazit všechny testy v programu Sledování připojení (preview), kde je zdrojová adresa IP 10.192.64.56:
1. Změňte zobrazení na **Test**.
1. Do vyhledávacího pole zadejte *10.192.64.56*
1. V rozevíracím seznamu vyberte **Zdroje**.

Chcete-li zobrazit pouze neúspěšné testy v programu Sledování připojení (Preview), kde je zdrojová ADRESA IP 10.192.64.56:
1. Změňte zobrazení na **Test**.
1. U filtru založeného na stavu vyberte **příkaz Selhání**.
1. Do vyhledávacího pole zadejte *10.192.64.56*
1. V rozevíracím seznamu vyberte **Zdroje**.

Chcete-li zobrazit pouze neúspěšné testy v programu Sledování připojení (Preview), kde je cíl outlook.office365.com:
1. Změňte zobrazení na **Test**.
1. U filtru založeného na stavu vyberte **příkaz Selhání**.
1. Do vyhledávacího pole zadejte *outlook.office365.com*
1. V rozevíracím seznamu vyberte **Cíle**.

   ![Snímek obrazovky s filtrovaným zobrazením, které zobrazuje pouze neúspěšné testy pro cíl Outlook.Office365.com](./media/connection-monitor-2-preview/tests-view.png)

Chcete-li zobrazit trendy v RTT a procento neúspěšných kontrol pro monitorování připojení:
1. Vyberte monitor připojení, který chcete prozkoumat. Ve výchozím nastavení jsou data monitorování uspořádána podle testovací skupiny.

   ![Snímek obrazovky s metrikami monitoru připojení zobrazenými podle testovací skupiny](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Vyberte testovací skupinu, kterou chcete prozkoumat.

   ![Snímek obrazovky s výběrem testovací skupiny](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Zobrazí se prvních pět prvních testů testovací skupiny, které jsou založeny na RTT nebo procentu neúspěšných kontrol. Pro každý test se zobrazí RTT a trendové řádky pro procento neúspěšných kontrol.
1. Vyberte test ze seznamu nebo zvolte jiný test, který chcete prozkoumat. U časového intervalu a procenta neúspěšných kontrol se zobrazí prahová hodnota a skutečné hodnoty. Pro RTT se zobrazí hodnoty pro prahovou hodnotu, průměr, minimum a maximum.

   ![Snímek obrazovky s výsledky testu pro RTT a procento neúspěšných kontrol](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Změňte časový interval a zobrazte další data.
1. Změňte zobrazení tak, aby zobrazoval zdroje, cíle nebo testovací konfigurace. 
1. Zvolte zdroj na základě neúspěšných testů a prozkoumejte prvních pět neúspěšných testů. Chcete-li například prozkoumat příslušné testy v monitoru připojení, zvolte **Zobrazit podle** > **zdrojů** a **Zobrazit podle** > **cílů.**

   ![Snímek obrazovky s metrikami výkonu pro prvních pět neúspěšných testů](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Chcete-li zobrazit trendy v RTT a procento neúspěšných kontrol pro testovací skupinu:

1. Vyberte testovací skupinu, kterou chcete prozkoumat. 

    Ve výchozím nastavení jsou data monitorování uspořádána podle zdrojů, cílů a konfigurací testů (testů). Později můžete změnit zobrazení z testovacích skupin na zdroje, cíle nebo konfigurace testů. Pak zvolte entitu pro zkoumat prvních pět neúspěšných testů. Můžete například změnit zobrazení na zdroje a cíle a prozkoumat příslušné testy ve vybraném monitoru připojení.
1. Vyberte test, který chcete prozkoumat.

   ![Snímek obrazovky s výběrem testu](./media/connection-monitor-2-preview/tg-drill.png)

    Pro časový interval a procento neúspěšných kontrol se zobrazí prahové hodnoty a skutečné hodnoty. Pro RTT se zobrazí hodnoty prahové hodnoty, průměr, minimum a maximum. Zobrazí se také vypalující výstrahy pro vybraný test.
1. Změňte časový interval a zobrazte další data.

Chcete-li zobrazit trendy v RTT a procento neúspěšných kontrol pro test:
1. Vyberte konfiguraci zdroje, cíle a testu, kterou chcete prozkoumat.

    Pro časový interval a procento neúspěšných kontrol se zobrazí prahové hodnoty a skutečné hodnoty. Pro RTT se zobrazí hodnoty prahové hodnoty, průměr, minimum a maximum. Zobrazí se také vypalující výstrahy pro vybraný test.

   ![Snímek obrazovky s metrikami testu](./media/connection-monitor-2-preview/test-drill.png)

1. Chcete-li zobrazit topologii sítě, vyberte **možnost Topologie**.

   ![Snímek obrazovky s kartou Topologie v síti](./media/connection-monitor-2-preview/test-topo.png)

1. Chcete-li zobrazit identifikované problémy, vyberte v topologii libovolný segment směrování v cestě. (Tyto směrování jsou prostředky Azure.) Tato funkce není momentálně dostupná pro místní sítě.

   ![Snímek obrazovky s vybraným odkazem směrování na kartě Topologie](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Protokolovat dotazy v log Analytics

Pomocí analýzy protokolů můžete vytvářet vlastní zobrazení dat monitorování. Všechna data, která se zobrazí v ui je z Log Analytics. Můžete interaktivně analyzovat data v úložišti. Korelujte data ze stavu agenta nebo jiných řešení, která jsou založena v Log Analytics. Exportujte data do Excelu nebo Power BI nebo vytvořte odkaz, který by se s vámi mohl spoji.

#### <a name="metrics-in-azure-monitor"></a>Metriky na platformě Azure Monitor

V monitorování připojení, které byly vytvořeny před monitorování připojení (Náhled) zkušenosti, všechny čtyři metriky jsou k dispozici: % sondy se nezdařilo, AverageRoundtripMs, ChecksFailedPercent (Preview) a RoundTripTimeMs (Náhled). V monitorování připojení, které byly vytvořeny v prostředí Sledování připojení (Náhled) data jsou k dispozici pouze pro metriky, které jsou označeny *(Náhled)*.

![Snímek obrazovky s metrikami v programu Sledování připojení (náhled)](./media/connection-monitor-2-preview/monitor-metrics.png)

Při použití metrik nastavte typ prostředku jako Microsoft.Network/networkWatchers/connectionMonitors

| Metrika | Zobrazované jméno | Jednotka | Typ agregace | Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| SondaFailedPercent | % sond se nezdařilo. | Procento | Průměr | Procento sond monitorování připojení se nezdařilo. | Žádné rozměry |
| AverageRoundtripMs | Vzdušnou dobu odezvy (ms) | Milisekund | Průměr | Průměrná síťOVÁ RTT pro sondy monitorování připojení odeslaná mezi zdrojem a cílem. |             Žádné rozměry |
| ChecksFailedPercent (náhled) | % kontrol se nezdařilo (náhled) | Procento | Průměr | Procento neúspěšných kontrol testu. | ConnectionMonitorResourceId <br>Zdrojová adresa <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol (Protokol) <br>Cílová adresa <br>Název_cíle <br>Určení ResourceId <br>Destinationtype <br>Cílový port <br>Název testovací skupiny <br>Název konfigurace testu <br>Region (Oblast) |
| RoundTripTimeMs (náhled) | Doba odezvy (ms) (náhled) | Milisekund | Průměr | RTT pro kontroly odeslané mezi zdrojem a místem určení. Tato hodnota není zprůměrována. | ConnectionMonitorResourceId <br>Zdrojová adresa <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol (Protokol) <br>Cílová adresa <br>Název_cíle <br>Určení ResourceId <br>Destinationtype <br>Cílový port <br>Název testovací skupiny <br>Název konfigurace testu <br>Region (Oblast) |

#### <a name="metric-alerts-in-azure-monitor"></a>Upozornění na metriky ve službě Azure Monitor

Vytvoření výstrahy v Azure Monitoru:

1. Zvolte prostředek sledování připojení, který jste vytvořili v programu Sledování připojení (Náhled).
1. Ujistěte se, že **se metrika** zobrazí jako typ signálu pro monitor připojení.
1. V **části Přidat podmínku**vyberte pro **název signálu** **položku ChecksFailedPercent(Preview)** nebo **RoundTripTimeMs(Preview).**
1. V **popřípadě Typ signálu**zvolte **Metriky**. Vyberte například **ChecksFailedPercent(Preview).**
1. Jsou uvedeny všechny dimenze pro metriku. Zvolte název dimenze a hodnotu dimenze. Vyberte například **zdrojovou adresu** a zadejte IP adresu libovolného zdroje do monitoru připojení.
1. V **logice výstrah**vyplňte následující podrobnosti:
   * **Typ podmínky**: **Statický**.
   * **Podmínka** a **práh**.
   * **Agregace Granularity a frekvence hodnocení:** Sledování připojení (Preview) aktualizuje data každou minutu.
1. V **aplikaci Akce**zvolte skupinu akcí.
1. Zadejte podrobnosti výstrahy.
1. Vytvořte pravidlo výstrahy.

   ![Snímek obrazovky zobrazující oblast Vytvořit pravidla v Azure Monitoru; Zvýrazněny jsou "Zdrojová adresa" a "Název koncového bodu zdroje"](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnostika problémů v síti

Program Sledování připojení (Preview) pomáhá diagnostikovat problémy v monitoru připojení a v síti. Problémy v hybridní síti jsou zjištěny agenty Log Analytics, které jste nainstalovali dříve. Problémy v Azure jsou zjištěny rozšíření matný strážce sítě. 

Problémy v síti Azure můžete zobrazit v topologii sítě.

U sítí, jejichž zdroje jsou místní virtuální počítač, lze zjistit následující problémy:

* Požadavek byl vypodován.
* Koncový bod není vyřešen službou DNS – dočasné nebo trvalé. Adresa URL je neplatná.
* Nebyl nalezen žádný hostitel.
* Zdroj se nemůže připojit k cíli. Cíl není dosažitelný prostřednictvím ICMP.
* Problémy související s certifikátem: 
    * K ověření agenta je vyžadován certifikát klienta. 
    * Seznam přemístění certifikátu není přístupný. 
    * Název hostitele koncového bodu neodpovídá alternativnímu názvu předmětu nebo předmětu certifikátu. 
    * V úložišti důvěryhodných certifikačních úřadů místního počítače zdroje chybí kořenový certifikát. 
    * Platnost certifikátu SSL vypršela, je neplatná, odvolána nebo nekompatibilní.

Pro sítě, jejichž zdrojejsou virtuální počítače Azure, lze zjistit následující problémy:

* Problémy s agentem:
    * Agent se zastavil.
    * Neúspěšné překlady DNS.
    * Na cílovém portu nenaslouchá žádná aplikace nebo naslouchací proces.
    * Soket nelze otevřít.
* Problémy se stavem virtuálního montovana: 
    * Spouštění
    * Zastavování
    * Zastaveno
    * Rušení přidělení
    * Přidělení zrušeno
    * Restartu
    * Nepřiděleno
* Položka tabulky ARP chybí.
* Provoz byl zablokován z důvodu místních problémů s bránou firewall nebo pravidel sítě sítě.
* Problémy s bránou virtuální sítě: 
    * Chybí trasy.
    * Tunelové propojení mezi dvěma branami je odpojeno nebo chybí.
    * Druhá brána nebyla nalezena tunelem.
    * Nebyly nalezeny žádné informace o vzájemném vztahu.
* V microsoft edge chyběla trasa.
* Provoz byl zastaven z důvodu systémových tras nebo UDR.
* BGP není povolena na připojení brány.
* Sonda DIP je dole u vykladače zatížení.
