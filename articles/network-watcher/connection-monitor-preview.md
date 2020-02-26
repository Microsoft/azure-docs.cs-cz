---
title: Monitorování připojení (Preview) | Microsoft Docs
description: Naučte se používat monitorování připojení (Preview) k monitorování síťové komunikace v distribuovaném prostředí.
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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599324"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Monitorování připojení k síti pomocí monitorování připojení (Preview)

Monitorování připojení (Preview) nabízí sjednocené monitorování připojení v Azure Network Watcher. Funkce monitorování připojení (Preview) podporuje hybridní nasazení v cloudu a Azure. Network Watcher poskytuje nástroje pro monitorování, diagnostiku a zobrazení metrik souvisejících s připojením pro vaše nasazení Azure.

Tady jsou některé případy použití pro monitorování připojení (Preview):

- Virtuální počítač s front-end webovým serverem komunikuje s virtuálním počítačem s databázovým serverem v vícevrstvé aplikaci. Chcete kontrolovat síťové připojení mezi těmito dvěma virtuálními počítači.
- Chcete, aby se virtuální počítače v oblasti Východní USA v oblasti Střed USA vyy na virtuální počítače s příkazy pro otestování a chcete porovnat latence sítě mezi oblastmi.
- Máte několik místních webů Office v Seattlu, Washington a v Ashburn,) – Virginia. Vaše weby Office se připojují k adresám URL Office 365. Pro uživatele adres URL sady Office 365 Porovnejte latence mezi Seattle a Ashburn.
- Vaše hybridní aplikace potřebuje připojení ke koncovému bodu Azure Storage. Vaše místní lokalita a vaše aplikace Azure se připojí ke stejnému koncovému bodu Azure Storage. Chcete porovnat latence místního serveru s latencí aplikace Azure.
- Chcete kontrolovat konektivitu mezi místními nastaveními a virtuálními počítači Azure, které hostují vaši cloudovou aplikaci.

V této fázi Preview spojuje monitorování připojení nejlepší ze dvou funkcí: funkci [monitorování připojení](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) Network Watcher a funkci [monitorování připojení služby](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) Network Performance Monitor (npm).

Zde jsou některé výhody monitorování připojení (Preview):

* Jednotné a intuitivní prostředí pro potřeby Azure a hybridní monitorování
* Monitorování připojení mezi jednotlivými oblastmi a mezi jednotlivými pracovními prostory
* Vyšší četnosti zjišťování a lepší přehled o výkonu sítě
* Rychlejší upozorňování pro hybridní nasazení
* Podpora kontrol připojení, které jsou založené na protokolech HTTP, TCP a ICMP 
* Metriky a Log Analytics podporu pro nastavení testů pro Azure i pro jiné platformy než Azure

![Diagram znázorňující, jak monitorování připojení spolupracuje s virtuálními počítači Azure, hostiteli mimo Azure, koncovými body a umístěními úložiště dat](./media/connection-monitor-2-preview/hero-graphic.png)

Pokud chcete začít používat monitorování připojení (Preview) ke sledování, postupujte podle těchto kroků: 

1. Nainstalujte agenty monitorování.
1. Povolte Network Watcher v předplatném.
1. Vytvořte monitorování připojení.
1. Nastavte analýzu dat a výstrahy.
1. Diagnostikujte problémy ve vaší síti.

Následující části obsahují podrobné informace o těchto krocích.

## <a name="install-monitoring-agents"></a>Instalace agentů monitorování

Monitorování připojení spoléhá na zjednodušené spustitelné soubory, aby se spouštěly kontroly připojení.  Podporuje kontroly připojení z prostředí Azure i místních prostředí. Spustitelný soubor, který použijete, závisí na tom, jestli je váš virtuální počítač hostovaný v Azure nebo v místním prostředí.

### <a name="agents-for-azure-virtual-machines"></a>Agenti pro virtuální počítače Azure

Pokud chcete, aby monitorování připojení rozpoznalo vaše virtuální počítače Azure jako zdroje monitorování, nainstalujte na ně rozšíření Network Watcher agenta virtuálního počítače. Toto rozšíření se označuje také jako *rozšíření Network Watcher*. Virtuální počítače Azure vyžadují, aby rozšíření aktivovalo komplexní monitorování a další pokročilé funkce. 

Rozšíření Network Watcher můžete nainstalovat při [vytváření virtuálního počítače](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). Můžete také samostatně instalovat, konfigurovat a řešit potíže s rozšířením Network Watcher pro [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) a [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

Pravidla pro skupinu zabezpečení sítě (NSG) nebo bránu firewall můžou blokovat komunikaci mezi zdrojem a cílem. Monitorování připojení tento problém detekuje a zobrazí se jako diagnostická zpráva v topologii. Pokud chcete povolit monitorování připojení, zajistěte, aby NSG a pravidla brány firewall povolovaly pakety přes TCP nebo ICMP mezi zdrojem a cílem.

### <a name="agents-for-on-premises-machines"></a>Agenti pro místní počítače

Pokud chcete, aby monitorování připojení rozpoznalo vaše místní počítače jako zdroje pro monitorování, nainstalujte na počítače agenta Log Analytics. Pak povolte řešení Network Performance Monitor. Tito agenti jsou propojeni s Log Analytics pracovními prostory, takže musíte nastavit ID a primární klíč pracovního prostoru, aby mohli agenti spustit monitorování.

Chcete-li nainstalovat agenta Log Analytics pro počítače se systémem Windows, přečtěte si téma [Azure monitor rozšíření virtuálních počítačů pro systém Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Pokud cesta obsahuje brány firewall nebo síťová virtuální zařízení (síťová virtuální zařízení), ujistěte se, že cíl je dosažitelný.

## <a name="enable-network-watcher-on-your-subscription"></a>Povolit Network Watcher v předplatném

Všechna předplatná, která mají virtuální síť, jsou povolená s Network Watcher. Když ve svém předplatném vytvoříte virtuální síť, Network Watcher se automaticky povolí v oblasti a předplatném virtuální sítě. Toto automatické povolování nijak neovlivní vaše prostředky ani se neúčtují. Ujistěte se, že ve vašem předplatném není explicitně zakázaná Network Watcher. 

Další informace najdete v tématu [povolení Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Vytvoření monitorování připojení 

Monitorování připojení monitoruje komunikaci v pravidelných intervalech. Informuje vás o změnách v dostupnosti a latenci. Můžete také kontrolovat aktuální a historicky topologii sítě mezi zdrojovými a cílovými koncovými body.

Zdroji můžou být virtuální počítače Azure nebo místní počítače, které mají nainstalovaného agenta monitorování. Cílovými koncovými body můžou být adresy URL pro Office 365, adresy URL Dynamics 365, vlastní adresy URL, ID prostředků virtuálních počítačů Azure, IPv4, IPv6, plně kvalifikovaný název domény nebo libovolný název domény.

### <a name="access-connection-monitor-preview"></a>Monitor připojení pro přístup (Preview)

1. Na domovské stránce Azure Portal přejít na **Network Watcher**.
1. Na levé straně v části **monitorování** vyberte **monitorování připojení (Preview)** .
1. Zobrazí se všechna monitorování připojení, která byla vytvořena v monitorování připojení (Preview). Chcete-li zobrazit monitory připojení vytvořené v klasickém prostředí monitorování připojení, přejděte na kartu **monitorování připojení** .

    ![Snímek obrazovky zobrazující monitory připojení, které byly vytvořeny v monitorování připojení (Preview)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Vytvoření monitorování připojení

Ve sledováních připojení, které vytvoříte v monitorování připojení (Preview), můžete přidat místní počítače a virtuální počítače Azure jako zdroje. Tato sledování připojení také mohou monitorovat připojení k koncovým bodům. Koncové body můžou být na Azure nebo na jakékoli jiné adrese URL nebo IP adrese.

Monitorování připojení (Preview) obsahuje následující entity:

* **Prostředek sledování připojení** – prostředek Azure specifický pro oblast. Všechny následující entity jsou vlastnosti prostředku monitorování připojení.
* **Koncový bod** – zdroj nebo cíl, který se účastní kontrol připojení. Mezi příklady koncových bodů patří virtuální počítače Azure, místní agenti, adresy URL a IP adresy.
* **Konfigurace testu** – konfigurace specifická pro protokol pro test. V závislosti na zvoleném protokolu můžete definovat port, prahové hodnoty, frekvenci testování a další parametry.
* **Testovací skupina** – skupina, která obsahuje zdrojové koncové body, cílové koncové body a konfigurace testu. Monitorování připojení může obsahovat více než jednu skupinu testů.
* **Test** – kombinace zdrojového koncového bodu, cílového koncového bodu a konfigurace testu. Test je nejpřesnější úroveň, při které jsou dostupná data monitorování. Data monitorování zahrnují procento kontrol, které selhaly, a čas odezvy (RTT).

 ![Diagram znázorňující monitorování připojení, definování vztahu mezi testovacími skupinami a testy](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Vytvoření monitorování připojení z Azure Portal

Chcete-li vytvořit monitorování připojení z Azure Portal, postupujte podle následujících kroků:

1. Na řídicím panelu **monitorování připojení (Preview)** v levém horním rohu vyberte **vytvořit**.
1. Na kartě **základy** zadejte informace pro monitor připojení:
   * **Název sledování připojení** – přidejte název monitorování připojení. Použijte standardní pravidla pojmenování pro prostředky Azure.
   * **Předplatné** – vyberte předplatné pro monitorování připojení.
   * **Oblast** – vyberte oblast pro monitorování připojení. Můžete vybrat jenom zdrojové virtuální počítače, které jsou vytvořené v této oblasti.
   * **Konfigurace pracovního prostoru** – pracovní prostor obsahuje data monitorování. Můžete použít vlastní pracovní prostor nebo výchozí pracovní prostor. 
       * Chcete-li použít výchozí pracovní prostor, zaškrtněte políčko. 
       * Chcete-li zvolit vlastní pracovní prostor, zrušte zaškrtnutí políčka. Pak zvolte předplatné a oblast pro vlastní pracovní prostor. 
1. V dolní části karty vyberte **Další: testovací skupiny**.

   ![Snímek obrazovky znázorňující kartu základy v monitorování připojení](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Na kartě **skupiny testů** vyberte **+ testovací skupina**. Chcete-li nastavit testovací skupiny, přečtěte si téma [vytvoření testovacích skupin v monitorování připojení](#create-test-groups-in-a-connection-monitor). 
1. V dolní části karty vyberte **Další: zkontrolovat + vytvořit** a zkontrolujte monitorování připojení.

   ![Snímek obrazovky s kartou testovací skupiny a s podoknem, kde můžete přidat podrobnosti skupiny testů](./media/connection-monitor-2-preview/create-tg.png)

1. Na kartě **Revize + vytvořit** si přečtěte základní informace a skupiny testů před vytvořením monitorování připojení. Pokud potřebujete upravit monitorování připojení:
   * Chcete-li upravit základní podrobnosti, vyberte ikonu tužky.
   * Chcete-li upravit testovací skupinu, vyberte ji.

   > [!NOTE] 
   > Na kartě **Revize + vytvořit** se zobrazují náklady za měsíc během fáze Preview monitorování připojení. V současné době se ve sloupci **aktuální náklady** zobrazí žádné poplatky. Když je monitorování připojení všeobecně dostupné, v tomto sloupci se zobrazí měsíční poplatek. 
   > 
   > I ve fázi Preview monitorování připojení platí Log Analytics poplatky za ingestování.

1. Až budete připraveni vytvořit monitorování připojení, v dolní části karty **Revize + vytvořit** vyberte **vytvořit**.

   ![Snímek obrazovky s monitorováním připojení, na které se zobrazuje karta Revize + vytvořit](./media/connection-monitor-2-preview/review-create-cm.png)

Monitorování připojení (Preview) vytvoří prostředek monitorování připojení na pozadí.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Vytvoření monitorování připojení pomocí ARMClient

Pomocí následujícího kódu vytvořte monitorování připojení pomocí ARMClient.

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

### <a name="create-test-groups-in-a-connection-monitor"></a>Vytvoření testovacích skupin v monitorování připojení

Každá testovací skupina v monitorování připojení zahrnuje zdroje a cíle, které se testují na parametrech sítě. Jsou testovány pro procento kontrol, které selžou, a čas od konfigurace testu.

Z Azure Portal pro vytvoření testovací skupiny v monitorování připojení zadáte hodnoty pro následující pole:

* **Zakázat testovací skupinu** – můžete vybrat toto pole, chcete-li zakázat monitorování pro všechny zdroje a cíle, které určuje skupina testů. Ve výchozím nastavení je tento výběr vymazán.
* **Název** – pojmenujte testovací skupinu.
* **Zdroje** – Pokud jsou agenti nainstalováni, můžete zadat jak virtuální počítače Azure, tak i místní počítače jako zdroje. Chcete-li nainstalovat agenta pro zdroj, přečtěte si téma [Instalace agentů monitorování](#install-monitoring-agents).
   * Pokud chcete vybrat agenty Azure, vyberte kartu **agenti Azure** . Tady vidíte jenom virtuální počítače, které jsou svázané s oblastí, kterou jste zadali při vytváření monitorování připojení. Ve výchozím nastavení jsou virtuální počítače seskupené do předplatného, ke kterému patří. Tyto skupiny jsou sbaleny. 
   
       Můžete přejít k podrobnostem z úrovně předplatného na jiné úrovně v hierarchii:

      **Předplatné** > **skupiny prostředků** > **virtuální sítě** > **podsítí** > **virtuálních počítačů s agenty**

      Můžete také změnit hodnotu pole **Seskupit podle** , aby se strom spouštěl z jiné úrovně. Pokud například vytvoříte seskupení podle virtuální sítě, zobrazí se virtuální počítače, které mají agenty v hierarchii **virtuální sítě** > **podsítě** > **virtuálních počítačů s agenty**.

      ![Snímek obrazovky s monitorováním připojení, který zobrazuje panel Přidat zdroje a kartu agenti Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Pokud chcete vybrat místní agenty, vyberte kartu **agenti, kteří nejsou – Azure** . Ve výchozím nastavení se agenti seskupují do pracovních prostorů podle oblasti. Všechny tyto pracovní prostory mají nakonfigurované řešení Network Performance Monitor. 
   
       Pokud potřebujete přidat Network Performance Monitor do svého pracovního prostoru, Získejte ho od [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Informace o tom, jak přidat Network Performance Monitor, najdete v tématu [monitorování řešení v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       V zobrazení **Sledování připojení** je na kartě **základy** vybraná možnost výchozí oblast. Změníte-li oblast, můžete vybrat možnost agenti z pracovních prostorů v nové oblasti. Můžete také změnit hodnotu pole **Seskupit podle** , aby bylo možné seskupovat podle podsítí.

      ![Snímek obrazovky s monitorováním připojení, zobrazením panelu Přidat zdroje a karty agenti mimo Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Pokud si chcete prohlédnout agenty Azure a mimo Azure, které jste vybrali, jděte na kartu **recenze** .

      ![Snímek obrazovky s monitorováním připojení, na kterém je zobrazen panel Přidat zdroje a karta Revize](./media/connection-monitor-2-preview/review-sources.png)

   * Po dokončení nastavení zdrojů klikněte v dolní části panelu **Přidat zdroje** na možnost **Hotovo**.

* **Cílová umístění** – můžete sledovat připojení k virtuálním počítačům Azure nebo libovolnému koncovému bodu (veřejné IP adresy, adresy URL nebo plně kvalifikovaného názvu domény) jejich zadáním jako cíle. V jedné skupině testů můžete přidat virtuální počítače Azure, adresy URL pro Office 365, adresy URL Dynamics 365 a vlastní koncové body.

    * Pokud chcete zvolit virtuální počítače Azure jako cíle, vyberte kartu **virtuální počítače Azure** . Ve výchozím nastavení se virtuální počítače Azure seskupují do hierarchie předplatného ve stejné oblasti, kterou jste vybrali v zobrazení **Sledování připojení** na kartě **základy** . Můžete změnit oblast a zvolit virtuální počítače Azure z nově vybrané oblasti. Pak můžete přejít k podrobnostem z úrovně předplatného na jiné úrovně v hierarchii, jako je úroveň agentů Azure.

       ![Snímek obrazovky s podoknem přidat umístění, na které se zobrazuje karta virtuální počítače Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Snímek obrazovky s podoknem přidat umístění, kde se zobrazuje úroveň předplatného](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Chcete-li zvolit koncové body jako cíle, vyberte kartu **koncové body** . Seznam koncových bodů zahrnuje testovací adresy URL Office 365 a adresy URL testů Dynamics 365 seskupené podle názvu. Kromě těchto koncových bodů můžete zvolit koncový bod, který byl vytvořen v jiných testovacích skupinách ve stejném monitorování připojení. 
    
        Chcete-li přidat nový koncový bod, v pravém horním rohu vyberte **+ koncové body**. Pak zadejte název koncového bodu a adresu URL, IP adresu nebo plně kvalifikovaný název domény.

       ![Snímek obrazovky s informacemi o tom, kde přidat koncové body jako cíle v monitorování připojení](./media/connection-monitor-2-preview/add-endpoints.png)

    * Pokud chcete zkontrolovat vybrané virtuální počítače Azure a koncové body, vyberte kartu **Revize** .
    * Po dokončení výběru možnosti cíle vyberte **Hotovo**.

* **Konfigurace testů** – ke skupině testů můžete přidružit konfigurace testu. Azure Portal povoluje pouze jednu konfiguraci testu na skupinu testů, ale můžete použít ARMClient k přidání dalších.

    * **Název** – pojmenujte konfiguraci testu.
    * **Protokol** – vyberte TCP, ICMP nebo http. Pokud chcete změnit protokol HTTP na HTTPS, jako protokol vyberte **http** a jako port vyberte **443** .
        * **Vytvořit konfiguraci testu sítě** – toto zaškrtávací políčko se zobrazí jenom v případě, že jste v poli **protokol** vybrali **http** . Toto políčko zaškrtněte, pokud chcete vytvořit další konfiguraci testu, která používá stejné zdroje a cíle, které jste zadali jinde v konfiguraci. Nově vytvořená konfigurace testu má název `<the name of your test configuration>_networkTestConfig`.
        * **Disable traceroute** – toto pole se vztahuje na testovací skupiny, jejichž protokol je TCP nebo ICMP. Toto políčko zaškrtněte, pokud chcete zastavit zdroje z vyhledávání topologie a času směrování po směrování.
    * **Cílový port** – toto pole můžete přizpůsobit podle vybraného cílového portu.
    * **Frekvence testování** – pomocí tohoto pole můžete zvolit, jak často se budou zdroje testovat pomocí příkazu otestovat cílový protokol a port, který jste zadali. Můžete zvolit 30 sekund, 1 minutu, 5 minut, 15 minut nebo 30 minut. Zdroje budou testovat připojení k cílům na základě hodnoty, kterou zvolíte.  Pokud například vyberete 30 sekund, zdroje budou kontrolovat připojení k cíli nejméně jednou za 30 sekund.
    * **Prahová hodnota úspěšnosti** – prahové hodnoty můžete nastavit u následujících síťových parametrů:
       * **Kontroly se nezdařily** – nastaví procento kontrol, které mohou selhat, když zdroje kontrolují připojení k cílům pomocí zadaných kritérií. V případě protokolu TCP nebo ICMP může být Procento neúspěšných kontrol rovno procentům ztráty paketů. V případě protokolu HTTP toto pole představuje procento požadavků HTTP, které neobdržely odpověď.
       * **Doba** odezvy – nastavte čas v milisekundách, po který se můžou dlouhé zdroje připojit k cíli přes konfiguraci testu.
    
       ![Snímek obrazovky s informacemi o tom, kde nastavit konfiguraci testu v monitorování připojení](./media/connection-monitor-2-preview/add-test-config.png)

Všechny zdroje, cíle a konfigurace testu, které přidáte do testovací skupiny, se rozdělují na jednotlivé testy. Tady je příklad rozdělení zdrojů a cílů:

* Testovací skupina: TG1
* Zdroje: 3 (A, B, C)
* Cílová umístění: 2 (D, E)
* Konfigurace testů: 2 (konfigurace 1, konfigurace 2)
* Celkem vytvořených testů: 12

| Testovací číslo | Zdroj | Cíl | Konfigurace testu |
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

### <a name="scale-limits"></a>Omezení škálování

Monitory připojení mají následující meze škálování:

* Maximální počet monitorování připojení na předplatné na oblast: 100
* Maximální počet testovacích skupin na monitorování připojení: 20
* Maximální počet zdrojů a cílů na monitorování připojení: 100
* Maximální počet konfigurací testu na monitorování připojení: 
    * 20 přes ARMClient
    * 2 přes Azure Portal

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analýza dat monitorování a nastavení výstrah

Po vytvoření monitorování připojení zdroje kontrolují připojení k cílům na základě konfigurace testu.

### <a name="checks-in-a-test"></a>Zkontroluje test.

Na základě protokolu, který jste zvolili v konfiguraci testu, se v monitorování připojení (Preview) spustí série kontrol dvojice zdroj-cíl. Kontroly se spustí podle četnosti testů, kterou jste zvolili.

Pokud používáte protokol HTTP, služba vypočítá počet odpovědí HTTP, které vrátily kód odpovědi. Výsledek určuje procento neúspěšných kontrol. Pro výpočet času RTT služba měří čas mezi voláním HTTP a odpovědí.

Pokud používáte protokol TCP nebo ICMP, služba vypočítá procento ztrát paketů a určí Procento neúspěšných kontrol. Pokud chcete Vypočítat čas RTT, služba měří dobu trvání přijetí potvrzení (ACK) pro odeslané pakety. Pokud jste povolili traceroute data pro testy sítě, můžete zobrazit ztrátu směrování po směrování a latenci pro vaši místní síť.

### <a name="states-of-a-test"></a>Stavy testu

Na základě dat, která kontroly vrací, můžou testy obsahovat následující stavy:

* **Pass** – skutečné hodnoty pro Procento neúspěšných kontrol a RTT jsou v rámci zadaných prahových hodnot.
* **Selhání** – skutečné hodnoty pro Procento neúspěšných kontrol nebo RTT překročily zadané prahové hodnoty. Pokud není zadána žádná prahová hodnota, test dosáhne stavu selhání, pokud je procento neúspěšných kontrol 100.
* **Upozornění** – pro Procento neúspěšných kontrol nebyla zadána žádná kritéria. Při nepřítomnosti zadaných kritérií přiřadí monitor připojení (Preview) automaticky prahovou hodnotu. Při překročení této prahové hodnoty se stav testu změní na upozornění.

### <a name="data-collection-analysis-and-alerts"></a>Shromažďování, analýza a výstrahy dat

Data, která jsou shromažďována pro monitorování připojení (Preview), jsou uložena v pracovním prostoru Log Analytics. Tento pracovní prostor nastavíte při vytváření monitorování připojení. 

Data monitorování jsou také k dispozici v Azure Monitor metriky. Pomocí Log Analytics můžete uchovávat data o monitorování, pokud chcete. Ve výchozím nastavení Azure Monitor ukládá metriky pouze 30 dnů. 

[Pro data můžete nastavit výstrahy založené na metrikách](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Monitorování řídicích panelů

Na řídicích panelech monitorování se zobrazí seznam monitorů připojení, ke kterým můžete získat přístup pro vaše předplatná, oblasti, časová razítka, zdroje a cílové typy.

Když přejdete na monitorování připojení (Preview) z Network Watcher, můžete zobrazit data podle těchto údajů:

* **Monitorování připojení** – seznam všech monitorování připojení vytvořených pro vaše předplatná, oblasti, časová razítka, zdroje a cílové typy. Toto zobrazení je výchozí.
* **Testovací skupiny** – seznam všech testovacích skupin vytvořených pro vaše předplatná, oblasti, časová razítka, zdroje a cílové typy. Tyto testovací skupiny nejsou filtrovány podle monitorování připojení.
* **Test** – seznam všech testů, které se spouštějí pro vaše předplatná, oblasti, časová razítka, zdroje a cílové typy. Tyto testy nejsou filtrovány pomocí monitorování připojení nebo skupin testů.

Na následujícím obrázku jsou tři zobrazení dat označena šipkou 1.

Na řídicím panelu můžete rozbalit každé monitorování připojení a zobrazit jeho testovací skupiny. Pak můžete rozbalit každou testovací skupinu a zobrazit tak testy, které jsou v něm spuštěné. 

Seznam můžete filtrovat podle:

* **Filtry nejvyšší úrovně** – vyberte odběry, oblasti, zdroje časových razítek a cílové typy. Viz Box 2 na následujícím obrázku.
* **Filtry založené na stavu** – filtrujte podle stavu monitorování připojení, testovací skupiny nebo testu. Viz šipka 3 na následujícím obrázku.
* **Vlastní filtry** – Chcete-li provést Obecné hledání, zvolte **možnost Vybrat vše** . Pokud chcete hledat podle konkrétní entity, vyberte z rozevíracího seznamu. Viz šipka 4 na následujícím obrázku.

![Snímek obrazovky ukazující, jak filtrovat zobrazení monitorování připojení, testovacích skupin a testů v monitorování připojení (Preview)](./media/connection-monitor-2-preview/cm-view.png)

Pokud například chcete zobrazit všechny testy v monitorování připojení (Preview), kde je zdrojová IP adresa 10.192.64.56:
1. Změňte zobrazení na **test**.
1. Do vyhledávacího pole zadejte *10.192.64.56*
1. V rozevíracím seznamu vyberte možnost **zdroje**.

Chcete-li zobrazit pouze neúspěšné testy v monitorování připojení (Preview), kde je zdrojová adresa IP 10.192.64.56:
1. Změňte zobrazení na **test**.
1. U filtru založeného na stavu vyberte **selhání**.
1. Do vyhledávacího pole zadejte *10.192.64.56*
1. V rozevíracím seznamu vyberte možnost **zdroje**.

Chcete-li zobrazit pouze neúspěšné testy v monitorování připojení (Preview), kde je cíl outlook.office365.com:
1. Změňte zobrazení na **test**.
1. U filtru založeného na stavu vyberte **selhání**.
1. Do vyhledávacího pole zadejte *Outlook.office365.com*
1. V rozevíracím seznamu vyberte **cílová místa**.

   ![Snímek obrazovky znázorňující zobrazení, které se filtruje tak, aby se zobrazily jenom neúspěšné testy pro cíl Outlook.Office365.com](./media/connection-monitor-2-preview/tests-view.png)

Zobrazení trendů v RTT a Procento neúspěšných kontrol pro monitorování připojení:
1. Vyberte monitorování připojení, které chcete prozkoumat. Ve výchozím nastavení jsou data monitorování uspořádána podle testovací skupiny.

   ![Snímek obrazovky zobrazující metriky pro monitorování připojení zobrazené skupinou testů](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Vyberte testovací skupinu, kterou chcete prozkoumat.

   ![Snímek obrazovky znázorňující, kde vybrat testovací skupinu](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    V závislosti na času RTT nebo procentu neúspěšných kontrol se zobrazí vaše testovací skupina s pěti nejoblíbenějšími neúspěšnými testy. Pro každý test vidíte řádky čas a trend pro Procento neúspěšných kontrol.
1. Vyberte test ze seznamu nebo zvolte jiný test, který chcete prozkoumat. Pro váš časový interval a Procento neúspěšných kontrol vidíte prahovou a skutečnou hodnotu. Pro čas RTT vidíte hodnoty pro mezní hodnotu, průměr, minimum a maximum.

   ![Snímek obrazovky znázorňující výsledky testu RTT a Procento neúspěšných kontrol](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Změňte časový interval pro zobrazení více dat.
1. Změňte zobrazení tak, aby se zobrazily zdroje, cíle nebo konfigurace testu. 
1. Vyberte zdroj založený na neúspěšných testech a prozkoumejte pět neúspěšných testů. Můžete například zvolit **zobrazení podle** > **zdrojů** a **zobrazení podle** > **cíle** a prozkoumat příslušné testy v monitorování připojení.

   ![Snímek obrazovky znázorňující metriky výkonu pro pět prvních neúspěšných testů](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Chcete-li zobrazit trendy v času RTT a Procento neúspěšných kontrol pro skupinu testů:

1. Vyberte testovací skupinu, kterou chcete prozkoumat. 

    Ve výchozím nastavení jsou data monitorování uspořádána podle zdrojů, cílů a konfigurací testování (testy). Později můžete změnit zobrazení z testovacích skupin na zdroje, cíle nebo konfigurace testu. Pak vyberte entitu, kterou chcete prozkoumat pět prvních neúspěšných testů. Můžete například změnit zobrazení na zdroje a cíle a prozkoumat příslušné testy ve vybraném monitorování připojení.
1. Vyberte test, který chcete prozkoumat.

   ![Snímek obrazovky znázorňující, kde vybrat test](./media/connection-monitor-2-preview/tg-drill.png)

    Pro váš časový interval a pro Procento neúspěšných kontrol vidíte prahové hodnoty a skutečné hodnoty. Pro čas RTT vidíte hodnoty pro hodnotu prahová hodnota, průměr, minimum a maximum. Také se zobrazí aktivované výstrahy pro vybraný test.
1. Změňte časový interval pro zobrazení více dat.

Zobrazení trendů v RTT a Procento neúspěšných kontrol testu:
1. Vyberte zdroj, cíl a konfiguraci testu, které chcete prozkoumat.

    Pro váš časový interval a Procento neúspěšných kontrol vidíte prahové hodnoty a skutečné hodnoty. Pro čas RTT vidíte hodnoty pro hodnotu prahová hodnota, průměr, minimum a maximum. Také se zobrazí aktivované výstrahy pro vybraný test.

   ![Snímek obrazovky zobrazující metriky pro test](./media/connection-monitor-2-preview/test-drill.png)

1. Chcete-li zobrazit topologii sítě, vyberte možnost **topologie**.

   ![Snímek obrazovky znázorňující kartu síťová topologie](./media/connection-monitor-2-preview/test-topo.png)

1. Zjištěné problémy zobrazíte tak, že v topologii vyberete jakékoli směrování v cestě. (Tyto segmenty jsou prostředky Azure.) Tato funkce není aktuálně k dispozici pro místní sítě.

   ![Snímek obrazovky znázorňující vybraný odkaz na směrování na kartě topologie](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Dotazy protokolu v Log Analytics

Pomocí Log Analytics můžete vytvořit vlastní zobrazení dat monitorování. Všechna data, která jsou zobrazena v uživatelském rozhraní, jsou z Log Analytics. Data v úložišti můžete interaktivně analyzovat. Korelujte data z Agent Health nebo jiných řešení, která jsou založená na Log Analytics. Exportujte data do aplikace Excel nebo Power BI nebo vytvořte odkaz Shared.

#### <a name="metrics-in-azure-monitor"></a>Metriky na platformě Azure Monitor

Ve sledováních připojení, které byly vytvořeny před zobrazením připojení (Preview), jsou k dispozici všechny čtyři metriky:% PROBE selhaly, AverageRoundtripMs, ChecksFailedPercent (Preview) a RoundTripTimeMs (Preview). V zobrazeních připojení, která byla vytvořena v prostředí monitorování připojení (Preview), jsou data k dispozici pouze pro metriky, které jsou označeny pomocí *(Preview)* .

![Snímek obrazovky zobrazující metriky v monitorování připojení (Preview)](./media/connection-monitor-2-preview/monitor-metrics.png)

Když použijete metriky, nastavte typ prostředku jako Microsoft. Network/networkWatchers/connectionMonitors.

| Metrika | Zobrazované jméno | Jednotka | Typ agregace | Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % PROBE selhalo | Procento | Průměr | Procento sond monitorování připojení selhalo. | Žádné dimenze |
| AverageRoundtripMs | Průměrná doba odezvy (MS) | Milisekundy | Průměr | Průměrná doba odezvy sítě pro testy monitorování připojení odesílané mezi zdrojem a cílem |             Žádné dimenze |
| ChecksFailedPercent (Preview) | % Kontroly selhaly (Preview) | Procento | Průměr | Procento neúspěšných kontrol testu | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Parametr sourceresourceid <br>sourceType <br>Protocol (Protokol) <br>DestinationAddress <br>Cílový. <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Oblast |
| RoundTripTimeMs (Preview) | Doba odezvy (MS) (Preview) | Milisekundy | Průměr | Čas RTT pro kontroly odeslané mezi zdrojem a cílem. Tato hodnota není průměrná. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Parametr sourceresourceid <br>sourceType <br>Protocol (Protokol) <br>DestinationAddress <br>Cílový. <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Oblast |

#### <a name="metric-alerts-in-azure-monitor"></a>Výstrahy metriky v Azure Monitor

Chcete-li vytvořit výstrahu v Azure Monitor:

1. Vyberte prostředek sledování připojení, který jste vytvořili v části monitorování připojení (Preview).
1. Zajistěte, aby se **metrika** zobrazila jako typ signálu pro monitorování připojení.
1. V části **Přidat podmínku**pro **Název signálu**vyberte **ChecksFailedPercent (Preview)** nebo **RoundTripTimeMs (Preview)** .
1. Jako **typ signálu**vyberte **metriky**. Vyberte například **ChecksFailedPercent (Preview)** .
1. V seznamu jsou uvedeny všechny dimenze metriky. Vyberte název dimenze a hodnotu dimenze. Vyberte například možnost **zdrojová adresa** a poté zadejte IP adresu libovolného zdroje v monitoru připojení.
1. V **logice výstrahy**zadejte následující podrobnosti:
   * **Typ podmínky**: **static**.
   * **Podmínka** a **prahová hodnota**.
   * **Členitost agregace a frekvence hodnocení**: monitorování připojení (Preview) aktualizuje data každou minutu.
1. V **Možnosti akce**vyberte skupinu akcí.
1. Zadejte podrobnosti výstrahy.
1. Vytvořte pravidlo výstrahy.

   ![Snímek obrazovky znázorňující oblast vytvořit pravidlo v Azure Monitor; Je zvýrazněna zdrojová adresa a název zdrojového koncového bodu.](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnostikujte problémy ve vaší síti

Sledování připojení (Preview) pomáhá diagnostikovat problémy v monitorování připojení a v síti. Problémy ve vaší hybridní síti zjistí agenti Log Analytics, které jste předtím nainstalovali. Problémy v Azure se zjišťují pomocí rozšíření Network Watcher. 

Problémy v síti Azure můžete zobrazit v síťové topologii.

Pro sítě, jejichž zdroje jsou místní virtuální počítače, se dají zjistit tyto problémy:

* Vypršel časový limit žádosti.
* Koncový bod nevyřešila služba DNS – dočasné nebo trvalé. Adresa URL je neplatná.
* Nenašly se žádní hostitelé.
* Zdroj se nemůže připojit k cíli. Cíl není dosažitelný pomocí protokolu ICMP.
* Problémy související s certifikátem: 
    * Je vyžadován klientský certifikát pro ověření agenta. 
    * Seznam přemístění certifikátu není přístupný. 
    * Název hostitele koncového bodu se neshoduje s alternativním názvem předmětu nebo předmětu certifikátu. 
    * V úložišti důvěryhodných certifikačních autorit místního počítače zdroje chybí kořenový certifikát. 
    * Platnost certifikátu SSL vypršela, je neplatný, odvolaný nebo nekompatibilní.

Pro sítě, jejichž zdroje jsou virtuálními počítači Azure, se dají zjistit tyto problémy:

* Problémy agenta:
    * Agent byl zastaven.
    * Překlad DNS se nezdařil.
    * Na cílovém portu nenaslouchá žádná aplikace ani naslouchací proces.
    * Soket nelze otevřít.
* Problémy se stavem virtuálního počítače: 
    * Spouštění
    * Zastavování
    * Zastaveno
    * Rušení přidělení
    * Přidělení zrušeno
    * Restartování
    * Nepřiděleno
* Chybí položka tabulky protokolu ARP.
* Provoz se zablokoval kvůli problémům s místními branami firewall nebo NSGým pravidlům.
* Problémy s bránou virtuální sítě: 
    * Chybějící trasy.
    * Tunelové propojení mezi dvěma branami je odpojené nebo chybí.
    * Druhá brána se nenašla tunelem.
    * Nenašly se žádné informace o partnerském vztahu.
* V Microsoft Edge chyběla trasa.
* Provoz se zastavil kvůli systémovým trasám nebo UDR.
* Protokol BGP není v připojení brány povolen.
* Sonda DIP je mimo provoz nástroje pro vyrovnávání zatížení.
