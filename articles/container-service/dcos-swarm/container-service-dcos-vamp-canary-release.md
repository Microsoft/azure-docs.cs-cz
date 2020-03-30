---
title: (ZASTARALÉ) Kanárské vydání s vampem v clusteru Azure DC/OS
description: Jak používat Vamp k kanárské verze služby a použít inteligentní filtrování provozu v clusteru Azure Container Service DC/OS
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 2af20a1ddf4239b7eec6cceabf2ff9711959c128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77189104"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(ZASTARALÉ) Mikroslužby kanárských verzí s vampem v clusteru Azure Container Service DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto návodu jsme nastavili Vamp na Azure Container Service s clusterem DC/OS. My canary vydání Vamp demo služby "sava", a pak vyřešit nekompatibilitu služby s Firefoxem použitím inteligentního provozu filtrování. 

> [!TIP] 
> V tomto návodu vamp běží na clusteru DC/OS, ale můžete také použít Vamp s Kubernetes jako orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>O kanárských verzích a Vampu


[Uvolnění kanárské](https://martinfowler.com/bliki/CanaryRelease.html) je inteligentní strategie nasazení přijatá inovativními organizacemi, jako je Netflix, Facebook a Spotify. Je to přístup, který dává smysl, protože snižuje problémy, zavádí záchranné sítě a zvyšuje inovace. Tak proč ho nepoužívají všechny společnosti? Rozšíření kanálu CI/CD tak, aby zahrnoval kanárské strategie, zvyšuje složitost a vyžaduje rozsáhlé znalosti a zkušenosti devops. To je dost blokovat menší společnosti a podniky, než se dokonce začít. 

[Vamp](https://vamp.io/) je open-source systém určený k usnadnění tohoto přechodu a přinést kanárské uvolnění funkce do preferovaného plánovače kontejnerů. Kanárská funkce vampu přesahuje zavedení na základě procenta. Provoz lze filtrovat a rozdělit v široké škále podmínek, například za účelem cílení na konkrétní uživatele, rozsahy IP adres nebo zařízení. Vamp sleduje a analyzuje metriky výkonu, což umožňuje automatizaci založenou na reálných datech. Můžete nastavit automatické vrácení zpět na chyby nebo škálování jednotlivých variant služby na základě zatížení nebo latence.

## <a name="set-up-azure-container-service-with-dcos"></a>Nastavení služby Azure Container Service pomocí řadiče domény/operačního služby



1. [Nasaďte cluster řadiče domény/operačního systému](container-service-deployment.md) s jedním hlavním serverem a dvěma agenty výchozí velikosti. 

2. [Vytvořte tunel SSH](../container-service-connect.md) pro připojení ke clusteru DC/OS. Tento článek předpokládá, že tunelové propojení do clusteru na místním portu 80.


## <a name="set-up-vamp"></a>Nastavit Vamp

Nyní, když máte spuštěný cluster DC/OS, můžete nainstalovat Vamp z dc/OS UI (http:\//localhost:80). 

![Uživatelské rozhraní DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Instalace se provádí ve dvou fázích:

1. **Nasazení elastického hledání**.

2. Pak **nasaďte Vamp** instalací balíčku Vamp DC/OS universe.

### <a name="deploy-elasticsearch"></a>Nasazení elastického vyhledávání

Vamp vyžaduje Elasticsearch pro shromažďování metrik a agregace. Můžete použít [image magneticio Docker](https://hub.docker.com/r/magneticio/elastic/) k nasazení kompatibilního zásobníku Vamp Elasticsearch.

1. V uzdu dc/os přejděte na **služby** a klikněte na **nasadit službu**.

2. V automaticky **otevíraném lícování Nasadit novou službu** vyberte **režim JSON.**

   ![Vybrat režim JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Vložte do následujícího JSON. Tato konfigurace spustí kontejner s 1 GB paměti RAM a základní kontrolu stavu na portu Elasticsearch.
  
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
  

3. Klepněte na tlačítko **Nasadit**.

   DC/OS nasazuje kontejner Elasticsearch. Průběh můžete sledovat na stránce **Služby.**  

   ![nasadit e? Elastické vyhledávání](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Nasazení vampu

Jakmile elastické vyhledávání hlásí jako **spuštěné**, můžete přidat balíček Vamp DC/OS Universe. 

1. Přejít na **vesmír** a hledat **upíra**. 
   ![Vamp na DC / OS vesmíru](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klepněte na **tlačítko Nainstalovat** vedle balíčku vamp a zvolte **Upřesnit instalaci**.

3. Posuňte se dolů a `http://elasticsearch.marathon.mesos:9200`zadejte následující elasticsearch-url: . 

   ![Zadat adresu URL elastického vyhledávání](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klikněte na **Zkontrolovat a nainstalovat**a potom kliknutím na **Instalovat** spusťte nasazení.  

   DC/OS nasazuje všechny požadované komponenty Vampu. Průběh můžete sledovat na stránce **Služby.**
  
   ![Nasazení Vamp jako vesmír balíček](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Po dokončení nasazení máte přístup k inamp ui:

   ![Vamp servis na DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Inamp UI](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Nasazení první služby

Teď, když je Vamp v provozu, nasaďte službu z podrobného plánu. 

V nejjednodušší podobě [podrobný plán Vamp](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) popisuje koncové body (brány), clustery a služby k nasazení. Vamp používá clustery k seskupení různých variant stejné služby do logických skupin pro uvolnění kanárků nebo testování A/B.  

Tento scénář používá ukázkovou monolitickou aplikaci nazvanou [**sava**](https://github.com/magneticio/sava), která je ve verzi 1.0. Monolit je zabalen v kontejneru Dockeru, který je v Docker Hubu pod magneticio/sava:1.0.0. Aplikace obvykle běží na portu 8080, ale chcete vystavit pod portem 9050 v tomto případě. Nasaďte aplikaci prostřednictvím Vamp pomocí jednoduchého podrobného plánu.

1. Přejděte na **nasazení**.

2. Klikněte na **Přidat**.

3. Vložte do následujícího podrobného plánu YAML. Tento podrobný plán obsahuje jeden cluster s jedinou variantou služby, kterou změníme v pozdějším kroku:

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

4. Klikněte na **Uložit**. Vamp zahájí nasazení.

Nasazení je uvedeno na stránce **Nasazení.** Chcete-li sledovat jeho stav, klepněte na nasazení.

![Vamp UI - nasazení sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![sava služba v Inamp UI](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Jsou vytvořeny dvě brány, které jsou uvedeny na stránce **Brány:**

* stabilní koncový bod pro přístup k běžící službě (port 9050) 
* interní brána spravovaná (více o této bráně později). 

![Vamp UI - sava brány](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Služba sava se teď nasadila, ale nemáte k ní přístup externě, protože Azure Load Balancer ještě neví, že by do ní měl předávat provoz. Chcete-li získat přístup ke službě, aktualizujte konfiguraci sítě Azure.


## <a name="update-the-azure-network-configuration"></a>Aktualizace konfigurace sítě Azure

Vamp nasadil službu sava na uzly agenta DC/OS a odhalil stabilní koncový bod na portu 9050. Chcete-li získat přístup ke službě mimo cluster řadiče domény/operačního systému, proveďte v nasazení clusteru následující změny konfigurace sítě Azure: 

1. **Nakonfigurujte Azure Balancer** pro agenty (prostředek s názvem **dcos-agent-lb-xxxx)** se sondou stavu a pravidlem pro předávání provozu na portu 9050 do instancí sava. 

2. **Aktualizujte skupinu zabezpečení sítě** pro veřejné agenty (prostředek s názvem **XXXX-agent-public-nsg-XXXX)** tak, aby povoluje přenos na portu 9050.

Podrobné kroky k dokončení těchto úkolů pomocí portálu Azure najdete v tématu [Povolení veřejného přístupu k aplikaci Služby kontejnerů Azure](container-service-enable-public-access.md). Zadejte port 9050 pro všechna nastavení portu.


Po vytvoření vše, co bylo vytvořeno, přejděte k **přehled** u okna nástroje pro vyrovnávání zatížení agenta DC/OS (prostředek s názvem **dcos-agent-lb-xxxx).** Najděte **veřejnou IP adresu**a použijte ji pro přístup k sava na portu 9050.

![Portál Azure – získání veřejné IP adresy](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Spuštění kanárské verze

Předpokládejme, že máte novou verzi této aplikace, kterou chcete kanárské vydání do produkčního prostředí. Máte to kontejnerizované jako magneticio /sava:1.1.0 a jsou připraveni jít. Vamp umožňuje snadno přidat nové služby pro běžící nasazení. Tyto "sloučené" služby jsou nasazeny společně se stávajícími službami v clusteru a přiřazují se jim váha 0 %. Do nově sloučené služby není směrován žádný provoz, dokud neupravíte distribuci provozu. Jezdec hmotnosti v inamp ui vám dává úplnou kontrolu nad distribucí, což umožňuje přírůstkové úpravy (kanárské vydání) nebo okamžité vrácení zpět.

### <a name="merge-a-new-service-variant"></a>Sloučení nové varianty služby

Sloučení nové služby sava 1.1 s spuštěným nasazením:

1. V uzdu Vamp klikněte na **Podrobné plány**.

2. Klikněte na **Přidat** a vložit v následujícím podrobném plánu YAML: Tento podrobný plán popisuje novou variantu služby (sava:1.1.0) pro nasazení v rámci existujícího clusteru (sava_cluster).

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
  
3. Klikněte na **Uložit**. Podrobný plán je uložen a uveden na stránce **Podrobné plány.**

4. Otevřete nabídku akcí v podrobném plánu sava:1.1 a klepněte na **tlačítko Sloučit do aplikace**.

   ![Vamp UI - plány](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Vyberte nasazení **sava** a klepněte na tlačítko **Sloučit**.

   ![Vamp UI – sloučit podrobný plán nasazení](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp nasazuje novou variantu služby sava:1.1.0 popsanou v podrobném plánu spolu s sava:1.0.0 v **sava_cluster** spuštěného nasazení. 

![Vamp UI - aktualizované nasazení sava](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

Brána **sava/sava_cluster/webport** (koncový bod clusteru) je také aktualizována a přidá trasu k nově nasazené sava:1.1.0. V tomto okamžiku je zde směrován žádný provoz **(HMOTNOST** je nastavena na 0 %).

![Vamp UI - clusterová brána](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Kanárské uvolnění

U obou verzí sava nasazených ve stejném clusteru upravte rozložení provozu mezi nimi přesunutím jezdce **HMOTNOST.**

1. Klikněte na ![Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - upravit vedle **HMOTNOST**.

2. Nastavte rozložení hmotnosti na 50 %/50 % a klepněte na **uložit**.

   ![Vamp UI - jezdec hmotnosti brány](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Vraťte se do prohlížeče a aktualizujte stránku sava ještě několikrát. Aplikace sava nyní přepíná mezi stránkou sava:1.0 a stránkou sava:1.1.

   ![střídavé služby sava1.0 a sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Toto střídání stránky funguje nejlépe s režimem "Inkognito" nebo "Anonymní" vašeho prohlížeče z důvodu ukládání statických datových zdrojů do mezipaměti.
  >

### <a name="filter-traffic"></a>Filtrovat provoz

Předpokládejme, že po nasazení jste zjistili nekompatibilitu v sava:1.1.0, která způsobuje problémy se zobrazením v prohlížečích Firefox. Můžete nastavit Vamp filtrovat příchozí provoz a nasměrovat všechny uživatele Firefoxu zpět na známou stabilní sava:1.0.0. Tento filtr okamžitě vyřeší narušení pro uživatele Firefoxu, zatímco všichni ostatní i nadále využívat výhod lepší sava:1.1.0.

Vamp používá **podmínky** pro filtrování provozu mezi trasami v bráně. Provoz je nejprve filtrován a směrován podle podmínek použitých pro každou trasu. Veškerý zbývající provoz je distribuován podle nastavení hmotnosti brány.

Můžete vytvořit podmínku pro filtrování všech uživatelů Firefoxu a nasměrovat je na staré sava:1.0.0:

1. Na stránce sava/sava_cluster/webport **Gateways** klikněte na ![](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Vamp UI - edit, chcete-li přidat **podmínku** trasy sava/sava_cluster/sava:1.0.0/webport. 

2. Zadejte podmínku **user-agent == Firefox** a klikněte na ![Vamp UI - save](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   Vamp přidá podmínku s výchozí pevností 0%. Chcete-li spustit filtrování provozu, je třeba upravit sílu stavu.

3. Klikněte na ![Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - upravit změnit **sílu** použitou na podmínku.
 
4. Nastavte **SÍLU** na 100% ![a klikněte](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) na Vamp UI - uložit pro uložení.

   Vamp nyní odesílá veškerý provoz odpovídající podmínce (všichni uživatelé Firefoxu) do sava:1.0.0.

   ![Vamp UI - platí podmínka pro bránu](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Nakonec upravte váhu brány tak, aby byl veškerý zbývající provoz (všichni uživatelé mimo Firefox) odeslán do nového sava:1.1.0. Klikněte na ![Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - upravit vedle **HMOTNOSTA** a nastavte rozložení hmotnosti tak, aby 100% je směrováno na trasu sava/sava_cluster/sava:1.1.0/webport.

   Veškerý provoz, který není filtrován podle podmínky, je nyní směrován na novou sava:1.1.0.

6. Chcete-li filtr zobrazit v akci, otevřete dva různé prohlížeče (jeden Firefox a jeden další prohlížeč) a získejte přístup ke službě sava z obou. Všechny požadavky Firefoxu jsou zasílány na sava:1.0.0, zatímco všechny ostatní prohlížeče jsou směrovány na sava:1.1.0.

   ![Vamp UI - filtrační provoz](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Sečtením

Tento článek byl rychlý úvod do Vamp na clusteru DC/OS. Pro začátek máte Vamp zprovozněn v clusteru Azure Container Service DC/OS, nasadíte službu s podrobným plánem Vamp a získáte k němu přístup v exponovaném koncovém bodu (bráně).

Také jsme se dotkli některých výkonných funkcí vampu: sloučení nové varianty služby do spuštěného nasazení a postupné zavádění a následné filtrování provozu, abychom vyřešili známou nekompatibilitu.


## <a name="next-steps"></a>Další kroky

* Další informace o správě akcí Vamp prostřednictvím [rozhraní Vamp REST API](https://docs.vamp.io/how-vamp-works/events-and-metrics#events).

* Vytvářejte skripty automatizace Vamp v souboru Node.js a spouštějte je jako [pracovní postupy Vamp](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows).

* Viz další [kurzy VAMP](https://docs.vamp.io/tutorials/).

