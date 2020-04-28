---
title: ZASTARALÉ Kanárské verze s využitím vampu v clusteru Azure DC/OS
description: Jak používat využitím vampu pro Kanárské služby vydaných verzí a použít filtrování inteligentních přenosů na Azure Container Service clusteru DC/OS
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 2af20a1ddf4239b7eec6cceabf2ff9711959c128
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77189104"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>ZASTARALÉ Využitím vampu v clusteru s Azure Container Service DC/OS vydaných verzí mikroslužeb

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto návodu nastavíme využitím vampu na Azure Container Service s clusterem DC/OS. Vydáme si využitím vampu demo Service "Sava" a pak vyřešíte nekompatibilitu služby pomocí aplikace Firefox, a to použitím filtrování inteligentních přenosů. 

> [!TIP] 
> V tomto návodu využitím vampu běží na clusteru DC/OS, ale můžete také použít využitím vampu s Kubernetes jako Orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>O Kanárských verzích a využitím vampu


Vydaná [kanárskéie](https://martinfowler.com/bliki/CanaryRelease.html) je strategie inteligentního nasazení přijatá inovativními organizacemi, jako je Netflix, Facebook nebo Spotify. Jedná se o přístup, který dává smysl, protože snižuje problémy, představuje bezpečnostní sítě a zvyšuje inovace. Proč to nepoužívají všechny společnosti? Rozšiřování kanálu CI/CD, aby zahrnovalo Kanárské strategie, přináší složitost a vyžaduje rozsáhlé DevOps znalosti a zkušenosti. To je dostatečné pro blokování menších firem a podniků ještě předtím, než začne. 

[Využitím vampu](https://vamp.io/) je open source systém navržený tak, aby usnadnil tento přechod a přináší špičkové uvolňování funkcí vašemu preferovanému plánovači kontejnerů. Využitím vampu funkce pro Kanárské využití překračuje procentní hodnotu uvádění. Provoz je možné filtrovat a rozdělit v rámci široké škály podmínek, například pro cílení na konkrétní uživatele, rozsahy IP adres nebo zařízení. Využitím vampu sleduje a analyzuje metriky výkonu a umožňuje tak automatizaci na základě dat z reálného světa. Můžete nastavit automatické vrácení zpět s chybami nebo škálovat jednotlivé varianty služby na základě zatížení nebo latence.

## <a name="set-up-azure-container-service-with-dcos"></a>Nastavení Azure Container Service s využitím DC/OS



1. [Nasaďte cluster DC/OS](container-service-deployment.md) s jedním hlavním serverem a dvěma agenty výchozí velikosti. 

2. [Vytvořte tunel SSH](../container-service-connect.md) pro připojení ke clusteru DC/OS. V tomto článku se předpokládá, že budete tunelovat na cluster na místním portu 80.


## <a name="set-up-vamp"></a>Nastavení využitím vampu

Teď, když máte spuštěný cluster DC/OS, můžete nainstalovat využitím vampu z uživatelského rozhraní DC/OS (http:\//localhost: 80). 

![Uživatelské rozhraní DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Instalace se provádí ve dvou fázích:

1. **Nasaďte Elasticsearch**.

2. Pak **Nasaďte využitím vampu** instalací balíčku využitím vampu DC/OS Universe.

### <a name="deploy-elasticsearch"></a>Nasazení Elasticsearch

Využitím vampu vyžaduje pro shromažďování a agregaci metriky Elasticsearch. K nasazení kompatibilního zásobníku Elasticsearch využitím vampu můžete použít [Image Docker magneticio](https://hub.docker.com/r/magneticio/elastic/) .

1. V uživatelském rozhraní DC/OS přejděte na **služby** a klikněte na **nasadit službu**.

2. V automaticky otevíraném okně **nasadit novou službu** vyberte **režim JSON** .

   ![Vybrat režim JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Vložte následující kód JSON. Tato konfigurace spustí kontejner s 1 GB paměti RAM a základní kontrolou stavu na portu Elasticsearch.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. Klikněte na **nasadit**.

   DC/OS nasadí kontejner Elasticsearch. Průběh můžete sledovat na stránce **služby** .  

   ![nasadíte e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Nasazení využitím vampu

Jakmile Elasticsearch sestavy jako **spuštěné**, můžete přidat balíček využitím vampu DC/OS Universe. 

1. Přejdete na **Universe** a vyhledejte **využitím vampu**. 
   ![Využitím vampu v universu DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klikněte na **instalovat** vedle balíčku využitím vampu a pak vyberte **Pokročilá instalace**.

3. Posuňte se dolů a zadejte následující Elasticsearch-URL `http://elasticsearch.marathon.mesos:9200`:. 

   ![Zadejte adresu URL Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klikněte na tlačítko **zkontrolovat a nainstalovat a**potom kliknutím na tlačítko **nainstalovat** spusťte nasazení.  

   DC/OS nasadí všechny požadované součásti využitím vampu. Průběh můžete sledovat na stránce **služby** .
  
   ![Nasazení využitím vampu jako balíčku Universe](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Po dokončení nasazení můžete získat přístup k uživatelskému rozhraní využitím vampu:

   ![Služba využitím vampu na DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Uživatelské rozhraní využitím vampu](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Nasazení první služby

Teď, když je využitím vampu v provozu, nasaďte službu z podrobného plánu. 

V nejjednodušším tvaru popisuje [využitím vampu podrobný plán](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) koncových bodů (bran), clusterů a služeb, které se mají nasadit. Využitím vampu využívá clustery k seskupení různých variant stejné služby do logických skupin pro účely zkušebního vydání nebo testování A/B.  

Tento scénář používá ukázkovou aplikaci monolitické s názvem [**Sava**](https://github.com/magneticio/sava), která je ve verzi 1,0. Monolitu je zabalen v kontejneru Docker, který je v Docker Hub v magneticio/Sava: 1.0.0. Aplikace normálně běží na portu 8080, ale v tomto případě ji chcete zpřístupnit v rámci portu 9050. K nasazení aplikace prostřednictvím využitím vampu použijte jednoduchý podrobný plán.

1. Přejít na **nasazení**.

2. Klikněte na tlačítko **Add** (Přidat).

3. Vložte následující YAML podrobného plánu. Tento podrobný plán obsahuje jeden cluster s pouze jednou variantou služby, který se změní v pozdějším kroku:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Klikněte na **Uložit**. Využitím vampu inicializuje nasazení.

Nasazení najdete na stránce **nasazení** . Kliknutím na nasazení monitorujte jeho stav.

![Uživatelské rozhraní využitím vampu – nasazení Sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Služba Sava v uživatelském rozhraní využitím vampu](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Vytvoří se dvě brány, které jsou uvedené na stránce **brány** :

* stabilní koncový bod pro přístup k běžící službě (port 9050) 
* interní brána spravovaná využitím vampu (Další informace o této bráně později). 

![Uživatelské rozhraní využitím vampu – brány Sava](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Služba Sava se teď nasadila, ale nemůžete k ní přistupovat externě, protože Azure Load Balancer ještě nevíte, že do ní ještě předává provoz. Pokud chcete získat přístup ke službě, aktualizujte konfiguraci sítě Azure.


## <a name="update-the-azure-network-configuration"></a>Aktualizace konfigurace sítě Azure

Využitím vampu nasadil službu Sava na uzlech agenta DC/OS a zveřejňuje stabilní koncový bod na portu 9050. Pokud chcete získat přístup ke službě mimo cluster DC/OS, proveďte následující změny konfigurace sítě Azure v nasazení clusteru: 

1. **Nakonfigurujte Azure Load Balancer** pro agenty (prostředek s názvem **DCOS-agent-kg-xxxx**) s sondou stavu a pravidlem pro přeposílání provozu na portu 9050 do instancí Sava. 

2. **Aktualizujte skupinu zabezpečení sítě** pro veřejné agenty (prostředek s názvem **xxxx-agent-Public-NSG-xxxx**), abyste povolili provoz na portu 9050.

Podrobné pokyny k provedení těchto úloh pomocí Azure Portal najdete v tématu [povolení veřejného přístupu k aplikaci Azure Container Service](container-service-enable-public-access.md). Zadejte port 9050 pro všechna nastavení portu.


Až se všechno vytvoří, v okně Přehled nástroje pro vyrovnávání zatížení agenta DC/OS (prostředek s názvem **DCOS-agent-kg-xxxx**) se zobrazí okno s **přehledem** . Vyhledejte **veřejnou IP adresu**a adresu použijte pro přístup k Sava na portu 9050.

![Azure Portal – získat veřejnou IP adresu](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Spuštění testovací verze

Předpokládejme, že máte novou verzi této aplikace, kterou chcete vydávat do produkčního prostředí. Máte je ve svém kontejneru jako magneticio/Sava: 1.1.0 a jsou připravené k přechodu. Využitím vampu umožňuje snadno přidávat nové služby do běžícího nasazení. Tyto "sloučené" služby se nasazují společně se stávajícími službami v clusteru a mají přiřazenou váhu 0%. Do nově sloučené služby se nesměruje žádný provoz, dokud neupravíte distribuci provozu. Posuvník váhy v uživatelském rozhraní využitím vampu vám dává plnou kontrolu nad distribucí, umožňuje přírůstkové úpravy (pro Kanárské vydání) nebo okamžité vrácení zpět.

### <a name="merge-a-new-service-variant"></a>Sloučí novou variantu služby.

Sloučení nové služby Sava 1,1 s běžícím nasazením:

1. V uživatelském rozhraní využitím vampu klikněte na **plány**.

2. Klikněte na **Přidat** a vložte do podrobného plánu YAML: Tento podrobný plán popisuje novou variantu služby (Sava: 1.1.0), která se má nasadit v existujícím clusteru (sava_cluster).

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Klikněte na **Uložit**. Podrobný plán je uložen a uveden na stránce **plány** .

4. Otevřete nabídku Akce v podrobném plánu Sava: 1.1 a klikněte na možnost **Sloučit do**.

   ![Uživatelské rozhraní využitím vampu – modrotisky](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Vyberte nasazení **Sava** a klikněte na **Sloučit**.

   ![Uživatelské rozhraní využitím vampu – sloučení podrobného plánu do nasazení](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Využitím vampu nasadí novou variantu služby Sava: 1.1.0, která je popsána v podrobném plánu vedle Sava: 1.0.0 v **sava_cluster** běžícího nasazení. 

![Uživatelské rozhraní využitím vampu – aktualizované nasazení Sava](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

Také se aktualizuje **Sava/sava_cluster/WebPort** brána (koncový bod clusteru), přidání trasy k nově nasazenému Sava: 1.1.0. V tuto chvíli se sem nesměrují žádné přenosy ( **váha** je nastavená na 0%).

![Uživatelské rozhraní využitím vampu – brána clusteru](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Kanárské verze

V obou verzích Sava nasazených ve stejném clusteru upravte přesunutím posuvníku **váhy** distribuci provozu mezi nimi.

1. Klikněte ![na využitím vampu UI –](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Upravit vedle **váhy**.

2. Nastavte rozdělení váhy na 50%/50% a klikněte na **Uložit**.

   ![Uživatelské rozhraní využitím vampu – posuvník váhy pro bránu](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Vraťte se do prohlížeče a několikrát aktualizujte stránku Sava. Aplikace Sava teď přepíná mezi stránkou Sava: 1.0 a stránkou Sava: 1.1.

   ![střídavé služby Sava 1.0 a Sava 1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Tento alternace stránky funguje nejlépe s režimem "anonymním" nebo "anonymní" v prohlížeči z důvodu ukládání statických prostředků do mezipaměti.
  >

### <a name="filter-traffic"></a>Filtrovat provoz

Předpokládejme, že se po nasazení zjistila nekompatibilita v Sava: 1.1.0, která způsobuje problémy zobrazení v prohlížečích Firefox. Můžete nastavit využitím vampu pro filtrování příchozího provozu a směrování všech uživatelů Firefox zpět na známou stabilní Sava: 1.0.0. Tento filtr okamžitě vyřeší přerušení pro uživatele prohlížeče Firefox, zatímco všichni ostatní nadále využívají výhody vylepšené Sava: 1.1.0.

Využitím vampu používá **podmínky** k filtrování provozu mezi trasami v bráně. Provoz je nejprve filtrován a směrován podle podmínek použitých pro každou trasu. Veškerý zbývající provoz se distribuuje podle nastavení váhy brány.

Můžete vytvořit podmínku pro filtrování všech uživatelů aplikace Firefox a jejich nasměrování na starou Sava: 1.0.0:

1. Na stránce **brány** sava/sava_cluster/WebPort klikněte na ![využitím vampu UI – upravit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) a přidejte **podmínku** do trasy Sava/sava_cluster/Sava: 1.0.0/WebPort. 

2. Zadejte podmínku **User-Agent = Firefox** a klikněte na ![využitím vampu UI – Uložit.](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png)

   Využitím vampu přidá podmínku s výchozí siloou 0%. Chcete-li zahájit filtrování provozu, je nutné upravit sílu podmínky.

3. Klikněte ![na využitím vampu UI –](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) upravit a změňte **sílu** aplikovanou na podmínku.
 
4. Nastavte **sílu** na 100% a klikněte ![na využitím vampu UI – Save](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) (Uložit).

   Využitím vampu nyní odesílá veškerý provoz splňující podmínku (všichni uživatelé prohlížeče Firefox) do Sava: 1.0.0.

   ![Uživatelské rozhraní využitím vampu – použít podmínku pro bránu](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Nakonec upravte váhu brány tak, aby odesílala všechna zbývající přenosová data (všichni uživatelé bez prohlížeče Firefox) do nové Sava: 1.1.0. Klikněte ![na využitím vampu UI –](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Upravit vedle **váhy** a nastavte rozdělení váhy tak, aby 100% bylo přesměrováno na port Sava/sava_cluster/Sava: 1.1.0/Web.

   Veškerý provoz, který není filtrovaný podle podmínky, se teď přesměruje na nový Sava: 1.1.0.

6. Pokud chcete zobrazit filtr v akci, otevřete dva různé prohlížeče (jeden prohlížeč Firefox a jeden další prohlížeč) a přihlaste se ke službě Sava z obou. Všechny požadavky aplikace Firefox jsou odesílány do Sava: 1.0.0, zatímco všechny ostatní prohlížeče jsou směrovány na Sava: 1.1.0.

   ![Uživatelské rozhraní využitím vampu – filtrování provozu](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Sčítání

Tento článek představuje rychlý Úvod k využitím vampu v clusteru DC/OS. V případě starts jste využitím vampui a běželi v clusteru Azure Container Service DC/OS, nasadili jste službu s využitím vampu podrobným plánem a získali k ní přístup v nevystaveném koncovém bodu (bráně).

Provedli jsme také některé výkonné funkce využitím vampu: sloučení nové varianty služby do běžícího nasazení a jejich přírůstkové zavedení a následnému vyfiltrování provozu za účelem vyřešení známé nekompatibility.


## <a name="next-steps"></a>Další kroky

* Přečtěte si o správě akcí využitím vampu prostřednictvím [využitím vampu REST API](https://docs.vamp.io/how-vamp-works/events-and-metrics#events).

* Sestavujte skripty pro automatizaci využitím vampu v Node. js a spusťte je jako [pracovní postupy využitím vampu](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows).

* Podívejte se na další [kurzy využitím vampu](https://docs.vamp.io/tutorials/).

