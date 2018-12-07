---
title: (NEPOUŽÍVANÉ) Canary release s využitím Vampu v clusteru Azure DC/OS
description: Jak použít využitím Vampu testovací verze služby a použít inteligentní provoz filtrování v clusteru Azure Container Service DC/OS
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 7ab63b869d9cd8a5b1f2b60429c5b54d0da5761f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53002075"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(NEPOUŽÍVANÉ) Testovací verze mikroslužeb s využitím Vampu v clusteru Azure Container Service DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto názorném postupu nastavíme využitím Vampu ve službě Azure Container Service s clusterem DC/OS. Jsme testovací vydání ukázka service využitím Vampu "Sávy" a potom vyřešte nekompatibility služby s prohlížečem Firefox použitím filtrování inteligentního přenosu. 

> [!TIP] 
> V tomto názorném postupu využitím Vampu běží na clusteru DC/OS, ale můžete použít také využitím Vampu s využitím Kubernetes jako orchestrátor.
>

## <a name="about-canary-releases-and-vamp"></a>O testovací verze a využitím Vampu


[Testovací uvolnění](https://martinfowler.com/bliki/CanaryRelease.html) je přijatý inovativní organizacím, jako jsou Netflix, Facebook a Spotify strategie inteligentní nasazení. Je přístup, který dává smysl, protože se omezuje problémy, představuje bezpečný přístup z více sítí a zvyšuje inovace. Tak proč nejsou všechny cloudy využívají společnosti ho? Rozšíření kanálu CI/CD pro zahrnují testovací strategie zvyšuje složitost a vyžaduje devops rozsáhlé znalosti a zkušenosti. To je dost informací k blokování menší firem a podniků nabídne před tím i začít. 

[Vamp](http://vamp.io/) vydává systém open source navržené pro usnadnění tohoto přechodu a přiřaďte testovací funkce, které váš Plánovač upřednostňované kontejneru. Využitím vampu vaší testovací funkce jde nad rámec založený na procentech uvedení. Provoz můžete filtrovat a rozdělit na širokou škálu podmínky, třeba pro cílové konkrétní uživatele, rozsahy IP adres nebo zařízení. Využitím vampu sleduje a analyzuje metrik výkonu, povolení pro službu automation na základě dat v reálném světě. Můžete nastavit automatické vrácení zpět na chyby, nebo varianty jednotlivých služeb na základě vytížení nebo latence.

## <a name="set-up-azure-container-service-with-dcos"></a>Nastavení služby Azure Container Service s DC/OS



1. [Nasazení clusteru DC/OS](container-service-deployment.md) s jedním hlavním uzlem a dvěma agenty výchozí velikost. 

2. [Vytvoření tunelu SSH](../container-service-connect.md) pro připojení ke clusteru DC/OS. Tento článek předpokládá, které vám Vytvoří tunel ke clusteru na místní port 80.


## <a name="set-up-vamp"></a>Nastavit využitím Vampu

Teď, když máte spuštěného clusteru DC/OS, můžete nainstalovat využitím Vampu v uživatelském rozhraní DC/OS (http://localhost:80). 

![Uživatelské rozhraní DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Instalace probíhá ve dvou fázích:

1. **Nasazení Elasticsearch**.

2. Potom **nasazení využitím Vampu** nainstalováním balíček Vamp DC/OS universe.

### <a name="deploy-elasticsearch"></a>Nasazení Elasticsearch

Využitím vampu vyžaduje Elasticsearch pro shromažďování metrik a agregaci. Můžete použít [imagí Dockeru magneticio](https://hub.docker.com/r/magneticio/elastic/) nasazení kompatibilní Vamp Elasticsearch zásobníku.

1. V uživatelském rozhraní DC/OS, přejděte na **služby** a klikněte na tlačítko **nasadit službu**.

2. Vyberte **režim JSON** z **nasadit novou službu** automaticky.

  ![Vyberte režim JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Vložte následující kód JSON. Tato konfigurace spuštění kontejneru s 1 GB paměti RAM a základní kontrolu na portu Elasticsearch.
  
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

  DC/OS nasadíte kontejner Elasticsearch. Průběh můžete sledovat na **služby** stránky.  

  ![nasadit e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Nasazení s využitím Vampu

Jakmile Elasticsearch sestavy jako **systémem**, můžete přidat balíček Vamp DC/OS Universe. 

1. Přejděte na **Universe** a vyhledejte **vamp**. 
  ![Vamp na DC/OS universe](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klikněte na tlačítko **nainstalovat** vedle využitím vampu balení a zvolte **rozšířené instalace**.

3. Posuňte se dolů a zadejte následující adresu url elasticsearch: `http://elasticsearch.marathon.mesos:9200`. 

  ![Zadejte adresu URL Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klikněte na tlačítko **zkontrolovat a nainstalovat**, pak klikněte na tlačítko **nainstalovat** ke spuštění nasazení.  

  DC/OS se nasazuje všechny požadované komponenty využitím Vampu. Průběh můžete sledovat na **služby** stránky.
  
  ![Nasazení jako balíčku universe využitím Vampu](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Po dokončení nasazení se můžete dostat Vamp uživatelského rozhraní:

  ![Služba s využitím vampu na DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Vamp uživatelského rozhraní](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Nasazení první služby

Nyní tento využitím Vampu je vytvořená a spuštěná, nasazení služby z podrobný plán. 

Ve své nejjednodušší podobě [podrobného plánu využitím Vampu](http://vamp.io/documentation/using-vamp/blueprints/) popisuje koncové body (brány), clusterů a služeb pro nasazení. Využitím vampu využívá clustery seskupit různé varianty stejné služby do logických skupin pro testovací uvolněním nebo A / B testování.  

Tento scénář používá ukázkovou monolitické aplikaci s názvem [ **Sávy**](https://github.com/magneticio/sava), což je ve verzi 1.0. V kontejneru Dockeru, který se nachází v Docker Hubu v rámci magneticio/sava:1.0.0 je zabalená monolitu. Aplikace obvykle běží na portu 8080, ale chcete v tomto případě vystavit na portu 9050. Nasazení aplikace prostřednictvím využitím Vampu pomocí jednoduchého podrobného plánu.

1. Přejděte na **nasazení**.

2. Klikněte na tlačítko **Add** (Přidat).

3. Vložte následující podrobný plán YAML. Tento podrobný plán obsahuje jeden cluster s pouze jedna služba varianty, které můžeme změnit v pozdějším kroku:

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

4. Klikněte na **Uložit**. Využitím vampu zahájí nasazení.

Nasazení je uveden na **nasazení** stránky. Klikněte na tlačítko nasazení do monitorování jejího stavu.

![Uživatelské rozhraní – nasazení Sávy vamp](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Služba Sávy v Vamp uživatelského rozhraní](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Dvě brány se vytvářejí, které jsou uvedeny na **brány** stránky:

* stabilní koncový bod pro přístup ke spuštěné službě (port 9050) 
* využitím Vampu spravované interní bránu (Další informace o tuto bránu později). 

![Vamp uživatelské rozhraní – Sávy brány](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Sávy služba má nyní nasadit, ale protože neví, nástroje pro vyrovnávání zatížení Azure směrovat provoz do něj ještě ji nelze přístup externě. Přístup k této službě, aktualizujte konfiguraci sítě Azure.


## <a name="update-the-azure-network-configuration"></a>Aktualizace konfigurace sítě Azure

Využitím vampu nasadit službu Sávy uzlů agentů DC/OS, vystavení stabilní koncový bod na port 9050. Přístup ke službě z mimo cluster DC/OS, proveďte následující změny v konfiguraci sítě Azure ve vašem nasazení clusteru: 

1. **Konfigurace nástroje pro vyrovnávání zatížení Azure** pro agenty (prostředek s názvem **DC/OS agent-lb-xxxx**) s sondu stavu a pravidlo, které směrují provoz na portu 9050 Sávy instance. 

2. **Aktualizovat skupinu zabezpečení sítě** pro veřejní agenti (prostředek s názvem **XXXX-agent-public-nsg-XXXX**) pro povolení provozu na portu 9050.

Podrobné pokyny k provedení těchto úloh pomocí webu Azure portal najdete v tématu [povolení veřejného přístupu k aplikaci Azure Container Service](container-service-enable-public-access.md). Určete port 9050 všechna nastavení portu.


Když se všechno, co byl vytvořen, přejděte do **přehled** okno nástroje pro vyrovnávání zatížení agenta DC/OS (prostředek s názvem **DC/OS agent-lb-xxxx**). Najít **veřejnou IP adresu**a použijte adresu pro přístup k Sávy na portu 9050.

![Azure portal – získat veřejnou IP adresu](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Sávy](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Spustit testovací verze

Předpokládejme, že máte novou verzi této aplikace, které mají na testovací verzi do produkčního prostředí. Jeho kontejnerizovaných jako magneticio/sava:1.1.0 a jste připravení přejít. Využitím vampu umožňuje snadno přidávat nové služby na běžícím nasazení. Tyto služby "sloučené" se nasazují společně s existující služby v clusteru a přiřazována váha 0 %. Žádný provoz se směruje do nově sloučeného dokud upravit distribuce provozu. Váha posuvník v Uživatelském rozhraní Vamp vám plnou kontrolu nad distribuce, umožňující úpravy přírůstkové (testovací verze) nebo okamžité vrácení zpět.

### <a name="merge-a-new-service-variant"></a>Sloučit novou variantu služby

Chcete-li sloučit novou službu Sávy 1.1 s běžícím nasazení:

1. V Uživatelském rozhraní Vamp klikněte na tlačítko **plány**.

2. Klikněte na tlačítko **přidat** a vložte následující podrobný plán YAML: podrobný plán popisuje novou variantu service (Sávy: 1.1.0) k nasazení do existujícího clusteru (sava_cluster).

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
  
3. Klikněte na **Uložit**. Podrobný plán se ukládají a uvedená na **plány** stránky.

4. Otevření nabídky akce na podrobný plán Sávy: 1.1 a klikněte na tlačítko **sloučit**.

  ![Vamp uživatelské rozhraní – podrobné plány.](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Vyberte **Sávy** nasazení a klikněte na tlačítko **sloučit**.

  ![Vamp uživatelského rozhraní - merge podrobný plán nasazení](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Využitím vampu nasadí novou variantu Sávy: 1.1.0 služby podle podrobného plánu společně s Sávy: 1.0.0 v **sava_cluster** spuštěné nasazení. 

![Vamp uživatelské rozhraní – aktualizace Sávy nasazení](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

**Sávy/sava_cluster/webport** brány (koncový bod clusteru) je aktualizované taky, přidání trasy do nově nasazené Sávy: 1.1.0. V tomto okamžiku žádný provoz se směruje tady ( **váha** je nastavena na 0 %).

![Vamp uživatelské rozhraní – clusteru brány](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Testovací verze

V obou verzích Sávy nasazené ve stejném clusteru upravit distribuci provozu mezi nimi přechodem **váha** posuvníku.

1. Klikněte na tlačítko ![Vamp uživatelské rozhraní – upravit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) vedle **váha**.

2. Nastavení distribuce váhy 50 % nebo 50 % a kliknutím na tlačítko **Uložit**.

  ![Vamp uživatelské rozhraní – brána váha posuvníku](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Přejděte zpět do prohlížeče a aktualizujte stránku Sávy ještě několikrát. Nyní aplikaci Sávy Přepne mezi Sávy: 1.0 stránku a stránku Sávy: 1.1.

  ![Alternující sava1.0 a sava1.1 služby](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Tato alternace stránce funguje nejlépe s prohlížečem "Incognito" nebo "Anonymní" režimu prohlížeče z důvodu ukládání do mezipaměti statické prostředky.
  >

### <a name="filter-traffic"></a>Filtrování provozu

Předpokládejme, že po nasazení, že zjišťuje k nekompatibilitě v Sávy: 1.1.0, způsobí, že se zobrazují v prohlížečích Firefox problémy. Můžete nastavit využitím Vampu a filtrovat příchozí provoz směrovat všechny uživatele aplikace Firefox zpátky na známé stabilní Sávy: 1.0.0. Tento filtr okamžitě řeší přerušení pro uživatele aplikace Firefox, zatímco ostatní uživatelé i nadále využívat výhod vylepšené Sávy: 1.1.0.

Vamp používá **podmínky** k filtrování provozu mezi trasy do brány. Provoz je nejprve filtrovat a přesměruje podle podmínek použitý pro každý postup. Veškerý zbývající provoz je distribuován podle nastavení váhy brány.

Můžete vytvořit podmínku filtrování všechny uživatele aplikace Firefox a přesměrování na staré Sávy: 1.0.0:

1. Na Sávy/sava_cluster/webport **brány** klikněte na ![Vamp uživatelské rozhraní – upravit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) přidáte **PODMÍNKU** k sava/sava_cluster/sava:1.0.0/webport trasy. 

2. Zadejte podmínku **uživatelský agent == Firefox** a klikněte na tlačítko ![Vamp uživatelské rozhraní – Uložit](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Využitím vampu přidá podmínku s použitím výchozí sílu 0 %. Spustí se filtrování provozu, budete muset upravit podmínku síly.

3. Klikněte na tlačítko ![Vamp uživatelské rozhraní – upravit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) změnit **SÍLU** použitý pro podmínku.
 
4. Nastavte **SÍLU** 100 % a kliknutím na tlačítko ![Vamp uživatelské rozhraní – Uložit](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) uložte.

  Využitím vampu nyní odesílá veškerý provoz odpovídajících podmínce (všechny uživatele aplikace Firefox) k Sávy: 1.0.0.

  ![Vamp uživatelského rozhraní – použít podmínku do brány](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Nakonec můžete upravte bránu váha odesílat veškerý přenos v zbývající (všechny uživatele bez Firefox) k nové Sávy: 1.1.0. Klikněte na tlačítko ![Vamp uživatelské rozhraní – upravit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) vedle **váha** a nastavit je distribuce váhy, aby se 100 % směřuje na sava/sava_cluster/sava:1.1.0/webport trasy.

  Veškerý provoz není filtrovaná podle podmínka je nyní přesměrováni na novou Sávy: 1.1.0.

6. Filtr v akci najdete otevřete dvěma různými prohlížeči (jeden Firefox a jeden jiný prohlížeč) a přístup ke službě Sávy z obou. Všechny požadavky Firefox jsou odesílány Sávy: 1.0.0, zatímco všechny ostatní prohlížeče jsou směrované na Sávy: 1.1.0.

  ![Vamp uživatelské rozhraní – filtrování provozu](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Shrnutí

Tento článek byl rychlý úvod k využitím Vampu na clusteru DC/OS. Začínáte jste získali využitím Vampu a spuštěná na vaše Azure Container Service DC/OS clusteru, nasazení služby s využitím Vampu podrobný plán a přístup v zveřejněné koncový bod (brány).

Můžeme také nakousli některé výkonné funkce využitím Vampu: sloučení novou variantu služby spuštěné nasazení a představení postupně a potom filtrování provozu do vyřešení známých nekompatibility.


## <a name="next-steps"></a>Další postup

* Další informace o správě akcí s využitím Vampu prostřednictvím [Vamp rozhraní REST API](http://vamp.io/documentation/api/api-reference/).

* Vytváření skriptů pro automatizaci využitím Vampu v Node.js a spouštět je jako [Vamp pracovních postupů](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow).

* Zjistěte víc [využitím VAMPU kurzy](http://vamp.io/documentation/tutorials/).

