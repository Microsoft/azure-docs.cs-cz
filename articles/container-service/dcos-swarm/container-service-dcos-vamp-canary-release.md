---
title: Lesknice verze s Vamp na clusteru Azure DC/OS
description: Pomocí Vamp lesknice verze služby a použití inteligentního provoz filtrování v clusteru Azure Container Service DC/OS
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 339864f6261d031a21b138f880654bcd6ef51855
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177797"
---
# <a name="canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>Lesknice verze mikroslužeb s Vamp v clusteru Azure Container Service DC/OS

V tomto návodu budeme nastavit Vamp v Azure Container Service se cluster DC/OS. Jsme lesknice verzi služby ukázku Vamp "Sávy" a potom vyřešte nekompatibility službu s Firefox použitím filtrování inteligentního přenosu. 

> [!TIP] 
> V tomto návodu Vamp běží na clusteru DC/OS, ale můžete také použít Vamp s Kubernetes jako orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>O Kanárských uvolní a Vamp


[Lesknice uvolnění](https://martinfowler.com/bliki/CanaryRelease.html) je přijat inovativní organizacím, jako Netflix, Facebook či Spotify strategie inteligentní nasazení. Je postup, který dává smysl, protože snižuje problémy, představuje bezpečnostní sítě a zvyšuje inovace. Proto proč všech společností, které nepoužívají ho? Rozšíření kanálu CI/CD zahrnout lesknice strategie přidá složitost a vyžaduje devops rozsáhlé znalosti a zkušenosti. To je dost blokování menší firem a podniků agentem před jejich i začít. 

[Vamp](http://vamp.io/) vydává systém open source, který je navržený tak, aby usnadňují tento přechod a převeďte Kanárských funkcí do vašeho scheduleru upřednostňované kontejneru. Funkce lesknice na vamp překročí pohledu na základě procenta. Provoz můžete filtrovat a rozdělit na širokou škálu podmínky, například pro cílové konkrétní uživatele, rozsahy IP adres nebo zařízení. Vamp sleduje a analyzuje metrik výkonu, povolení pro automatizaci na základě reálného dat. Můžete nastavit automatické vrácení zpět na chyby, nebo škálování variant jednotlivých služeb na základě zatížení nebo latence.

## <a name="set-up-azure-container-service-with-dcos"></a>Nastavení Azure Container Service s DC/OS



1. [Nasadit cluster DC/OS](container-service-deployment.md) se jeden z nich a dva agenti výchozí velikost. 

2. [Vytvoření tunelu SSH](../container-service-connect.md) se připojit ke clusteru DC/OS. Tento článek předpokládá můžete tunelového propojení do clusteru v místního portu 80.


## <a name="set-up-vamp"></a>Nastavit Vamp

Teď, když máte spuštěného clusteru DC/OS, můžete nainstalovat Vamp z uživatelského rozhraní DC/OS (http://localhost:80). 

![Uživatelské rozhraní DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Instalace probíhá ve dvou fázích:

1. **Nasazení Elasticsearch**.

2. Potom **nasazení Vamp** instalací balíčku universe Vamp DC/OS.

### <a name="deploy-elasticsearch"></a>Nasazení Elasticsearch

Vamp vyžaduje Elasticsearch metriky shromažďování a agregace. Můžete použít [imagí Dockeru magneticio](https://hub.docker.com/r/magneticio/elastic/) nasazení kompatibilní Vamp Elasticsearch zásobníku.

1. V uživatelském rozhraní DC/OS, přejděte do **služby** a klikněte na tlačítko **nasadit službu**.

2. Vyberte **režim JSON** z **nasazení nové služby** automaticky otevírané okno.

  ![Vyberte režim JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Vložte následující kód JSON. Tato konfigurace používá s 1 GB paměti RAM a kontrola základní stavu na portu Elasticsearch kontejneru.
  
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
  

3. Klikněte na tlačítko **nasazení**.

  DC/OS nasadí Elasticsearch kontejneru. Můžete sledovat průběh **služby** stránky.  

  ![nasazení e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Nasazení Vamp

Jakmile Elasticsearch sestavy jako **systémem**, můžete přidat balíček Vamp Universe DC/OS. 

1. Přejděte na **Universe** a vyhledejte **vamp**. 
  ![Vamp na DC/OS universe](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klikněte na tlačítko **nainstalovat** vedle vamp balíčku a vyberte **rozšířený instalace**.

3. Posuňte se dolů a zadejte následující adresu url elasticsearch: `http://elasticsearch.marathon.mesos:9200`. 

  ![Zadejte adresu URL Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klikněte na tlačítko **zkontrolovat a nainstalovat**, pak klikněte na tlačítko **nainstalovat** ke spuštění nasazení.  

  DC/OS nasadí všechny požadované součásti Vamp. Můžete sledovat průběh **služby** stránky.
  
  ![Nasazení Vamp jako balíček universe](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Po dokončení nasazení se můžete dostat Vamp uživatelského rozhraní:

  ![Služba vamp na DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Vamp uživatelského rozhraní](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Nasazení vaší první službě

Nyní tento Vamp je spuštěná, nasazení služby z plán, podle kterého. 

Ve své nejjednodušší podobě [Vamp plán, podle kterého](http://vamp.io/documentation/using-vamp/blueprints/) popisuje koncových bodů (brány), clustery a služby pro nasazení. Vamp používá clustery k seskupení různé varianty stejnou službu do logických skupin pro lesknice uvolněním nebo A / B testování.  

Tento scénář používá ukázkovou aplikaci monolitický názvem [ **Sávy**](https://github.com/magneticio/sava), který je ve verzi 1.0. Monolitu je součástí Docker kontejneru, který je v úložiště Docker Hub v rámci magneticio/sava:1.0.0. Aplikace se za normálních okolností běží na portu 8080, ale chcete v tomto případě vystavit v rámci port 9050. Nasaďte aplikaci prostřednictvím Vamp používá jednoduchý plán, podle kterého.

1. Přejděte na **nasazení**.

2. Klikněte na tlačítko **Add** (Přidat).

3. Vložte následující matrici YAML. Tento plán, podle kterého obsahuje jeden cluster s variant pouze jedna služba, která nám změnit později:

  ```YAML
  name: sava                        # deployment name
  gateways:
    9050: sava_cluster/webport      # stable endpoint
  clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
  ```

4. Klikněte na **Uložit**. Vamp zahájí nasazení.

Nasazení je uvedena na **nasazení** stránky. Klikněte na tlačítko nasazení monitorovat jeho stav.

![Uživatelské rozhraní – nasazení Sávy vamp](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Služba Sávy v Vamp uživatelského rozhraní](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Vytváří dvě brány, které jsou uvedeny na **brány** stránky:

* koncový bod stabilní přístup ke službě spuštěné (port 9050) 
* spravované Vamp interní brány (Další informace o této brány se později). 

![Vamp uživatelské rozhraní – Sávy brány](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Službu Sávy má nyní nasadit, ale protože pro přenos dat do ní ještě není známo nástroje pro vyrovnávání zatížení Azure ho nelze přístup externě. Přístup ke službě, aktualizujte konfiguraci sítí Azure.


## <a name="update-the-azure-network-configuration"></a>Aktualizace konfiguraci sítě Azure

Vamp nasadit službu Sávy na uzlech agenta DC/OS, vystavení stabilní koncový bod na port 9050. Přístup ke službě z mimo cluster DC/OS, proveďte následující změny konfigurace sítě Azure ve vašem nasazení clusteru: 

1. **Konfigurace pro vyrovnávání zatížení Azure** pro agenty (prostředek s názvem **orchestrátoru DC/OS agenta lb xxxx**) s test stavu a pravidla pro přenos dat na portu 9050 na Sávy instance. 

2. **Aktualizovat skupinu zabezpečení sítě** pro veřejné agenty (prostředek s názvem **XXXX-agent veřejný nsg-XXXX**) umožňuje přenosy na portu 9050.

Podrobné pokyny k dokončení těchto úloh pomocí portálu Azure najdete v tématu [povolit veřejný přístup k aplikaci Azure Container Service](container-service-enable-public-access.md). Zadejte port 9050 pro všechna nastavení portu.


Po všechno, co byla vytvořena, přejděte do **přehled** okno nástroje pro vyrovnávání zatížení agenta DC/OS (prostředek s názvem **orchestrátoru DC/OS agenta lb xxxx**). Najít **veřejnou IP adresu**a použijte adresu pro přístup k Sávy na portu 9050.

![Portál Azure – get veřejnou IP adresu](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Sávy](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Spustit lesknice verze

Předpokládejme, že máte novou verzi této aplikace, které chcete lesknice vydání do produkčního prostředí. Ji kontejnerizované jako magneticio/sava:1.1.0 a připraveni. Vamp umožňuje snadno přidat nové služby spuštěné nasazení. Tyto služby "sloučené" nasazení společně se stávající služby v clusteru a přiřazený váhu 0 %. Žádný provoz se směruje na nově sloučený, dokud upravit distribuce přenosů. Posuvník váhy v uživatelském rozhraní Vamp poskytuje úplnou kontrolu nad distribuce, aby vám umožnil přírůstkové úpravy (lesknice vydání) nebo rychlých vrácení zpět.

### <a name="merge-a-new-service-variant"></a>Sloučení nové služby variant

Sloučit novou službu Sávy 1.1 s spuštěného nasazení:

1. V uživatelském rozhraní Vamp klikněte na tlačítko **plány vyfotografovat**.

2. Klikněte na tlačítko **přidat** a vložte následující matrici YAML: Tento plán, podle kterého popisuje hodnotu nové služby typu variant (Sávy: 1.1.0) nasadit v rámci stávajícího clusteru (sava_cluster).

  ```YAML
  name: sava:1.1.0      # blueprint name
  clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
  ```
  
3. Klikněte na **Uložit**. Plán, podle kterého je uložena a uvedené na **plány vyfotografovat** stránky.

4. Otevřete nabídku Akce na plán, podle kterého Sávy: 1.1 a klikněte na tlačítko **sloučí se**.

  ![Vamp uživatelské rozhraní – plány](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Vyberte **Sávy** nasazení a klikněte na tlačítko **sloučení**.

  ![Vamp uživatelské rozhraní – sloučení plán, podle kterého nasazení](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp nasadí nové služby varianty Sávy: 1.1.0 popsané v plán, podle kterého spolu s Sávy: 1.0.0 v **sava_cluster** spuštěné nasazení. 

![Vamp uživatelské rozhraní – aktualizované Sávy nasazení](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

**Sávy/sava_cluster/webport** brány (koncový bod clusteru) je aktualizované taky, přidání do nově nasazené Sávy: 1.1.0 trasu. V tomto okamžiku žádný provoz se směruje zde ( **VÁHY** je nastaven na 0 %).

![Vamp uživatelské rozhraní – clusteru brány](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Lesknice verze

V obou verzích Sávy nasazené ve stejném clusteru upravit distribuce přenosů mezi nimi přesunutím **VÁHY** posuvníku.

1. Klikněte na tlačítko ![Vamp uživatelské rozhraní – upravit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) vedle **VÁHY**.

2. Nastavení distribuce váhy 50 % nebo 50 %, klikněte na **Uložit**.

  ![Vamp uživatelské rozhraní – brána váhy posuvníku](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Přejděte zpět do prohlížeče a aktualizujte stránku Sávy několik vícekrát. Aplikace Sávy přepíná mezi Sávy: 1.0 stránky a stránky Sávy: 1.1.

  ![střídání sava1.0 a sava1.1 služby](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Tato alternace stránky nejvhodnější "Incognito" nebo "Anonymní" režim prohlížeče kvůli ukládání do mezipaměti statické prostředky.
  >

### <a name="filter-traffic"></a>Filtrování provozu

Po nasazení Předpokládejme, že jste zjišťuje nekompatibilitu v Sávy: 1.1.0, že příčiny zobrazit problémy v prohlížečích Firefox. Můžete nastavit Vamp a filtrovat příchozí provoz směrovat, že všichni uživatelé Firefox zpět do známé stabilní Sávy: 1.0.0. Tento filtr okamžitě přeloží přerušení pro uživatele, Firefox, zatímco ostatní uživatelé nadále využívat výhod vylepšené Sávy: 1.1.0.

Vamp používá **podmínky** k filtrování provozu mezi trasy v bránu. Provoz je nejdřív filtrovat a přesměruje podle podmínky použít pro každý postup. Veškerý zbývající provoz je distribuován podle váhy nastavení brány.

Můžete vytvořit podmínku, kterou chcete filtrovat všechny Firefox uživatele a přesměrování je na staré Sávy: 1.0.0:

1. Na Sávy/sava_cluster/webport **brány** klikněte na tlačítko ![Vamp uživatelské rozhraní – upravit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) přidat **PODMÍNKU** k sava/sava_cluster/sava:1.0.0/webport trasy. 

2. Zadejte podmínku **uživatelský agent == Firefox** a klikněte na tlačítko ![Vamp uživatelské rozhraní – Uložit](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp přidá podmínku s výchozí sílu % 0. Pokud chcete spustit, filtrování provozu, potřebujete upravit sílu podmínku.

3. Klikněte na tlačítko ![Vamp uživatelské rozhraní – upravit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) změnit **SÍLU** použít podmínku.
 
4. Nastavte **SÍLU** 100 %, klikněte na tlačítko ![Vamp uživatelské rozhraní – Uložit](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) uložit.

  Vamp teď odešle veškerý provoz odpovídajících podmínce (všichni uživatelé Firefox) k Sávy: 1.0.0.

  ![Vamp uživatelského rozhraní – použít podmínku brány](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Nakonec upravte váhy brány odesílat všechny zbývající provoz (všechny uživatele bez Firefox) do nového Sávy: 1.1.0. Klikněte na tlačítko ![Vamp uživatelské rozhraní – upravit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) vedle **VÁHY** a nastavte je distribuce váhy, 100 % směřuje na sava/sava_cluster/sava:1.1.0/webport trasy.

  Veškerý provoz nejsou filtrovány podle podmínky se teď přesměruje nové Sávy: 1.1.0.

6. Informace o filtru v akci, otevřete dva různé prohlížeče (jeden Firefox a jeden jiný prohlížeč) a přístup ke službě Sávy z obou. Všechny požadavky Firefox jsou odesílány Sávy: 1.0.0, zatímco všech dalších prohlížečích jsou směrované Sávy: 1.1.0.

  ![Vamp uživatelské rozhraní – filtrovat provoz](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Shrnutí

Tento článek byl rychlý úvod do Vamp na cluster DC/OS. Pro začátek jste získali Vamp a spuštěná na vaše Azure Container Service DC/OS clusteru, nasazení služby s Vamp plán, podle kterého a přístup na adrese zveřejněné koncový bod (brány).

Můžeme také dotýkal na některé výkonné funkce Vamp: slučování hodnotu typu variant nové služby pro nasazení spuštěného a jeho zavedení postupně, pak filtrování provozu řešení známých nekompatibilita.


## <a name="next-steps"></a>Další postup

* Další informace o správě Vamp akce prostřednictvím [Vamp REST API](http://vamp.io/documentation/api/api-reference/).

* Skripty pro automatizaci Vamp v Node.js sestavit a spustit je jako [Vamp pracovních](http://vamp.io/documentation/tutorials/create-a-workflow/).

* Další informace najdete v [VAMP kurzy](http://vamp.io/documentation/tutorials/overview/).

