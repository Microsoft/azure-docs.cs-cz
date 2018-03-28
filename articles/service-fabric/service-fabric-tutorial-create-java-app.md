---
title: Vytvoření aplikace v Javě pro Service Fabric | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit aplikaci spolehlivé služby v Javě s front-endem a stavovým back-endem spolehlivé služby a jak tuto aplikaci nasadit do clusteru.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: dc67de00abb2eac2eeb6e2b6bf3798e3aa210152
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-create-and-deploy-an-application-with-a-java-web-api-front-end-service-and-a-stateful-back-end-service"></a>Kurz: Vytvoření a nasazení aplikace s front-end službou webového rozhraní API pro Javu a stavovou back-end službou
Tento kurz je první částí série. Až budete hotovi, budete mít hlasovací aplikaci s webovým front-endem v Javě, která ukládá výsledky hlasování do stavové back-end služby v clusteru. Tato série kurzů vyžaduje funkční vývojový počítač se systémem Mac OSX nebo Linuxem. Pokud nechcete hlasovací aplikaci vytvářet ručně, můžete si [stáhnout zdrojový kód dokončené aplikace](https://github.com/Azure-Samples/service-fabric-java-quickstart) a přeskočit k části [Prohlídka ukázkové hlasovací aplikace](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application).

![Místně spuštěná hlasovací aplikace](./media/service-fabric-tutorial-create-java-app/votingjavalocal.png)

V první části této série se naučíte:

> [!div class="checklist"]
> * Vytvoření spolehlivé stavové služby v Javě
> * Vytvoření bezstavové služby webové aplikace v Javě
> * Použití vzdálené komunikace služby ke komunikaci se stavovou službou
> * Nasazení aplikace do místního clusteru Service Fabric

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> *  Sestavení aplikace Service Fabric Reliable Services v Javě
> * [Nasazení a ladění aplikace v místním clusteru](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Nasazení aplikace do clusteru Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-java-elk.md)
> * [Nastavení CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Požadavky
Než začnete s tímto kurzem:
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nastavení vývojového prostředí pro [Mac](service-fabric-get-started-mac.md) nebo [Linux](service-fabric-get-started-linux.md). Postupujte podle pokynů k instalaci modulu plug-in Eclipse, Gradle, sady Service Fabric SDK a Service Fabric CLI (sfctl).

## <a name="create-the-front-end-java-stateless-service"></a>Vytvoření bezstavové front-end služby v Javě
Nejprve vytvořte webový front-end hlasovací aplikace. Bezstavová služba v Javě představuje jednoduchý server HTTP, který je hostitelem webového uživatelského rozhraní využívajícího AngularJS. Požadavky od uživatele se zpracovávají v této bezstavové službě a odesílají jako vzdálené volání procedury do stavové služby, která ukládá hlasy. 

1. Spuštění Eclipse

2. Vytvořte projekt postupným výběrem **File** (Soubor) -> **New** (Nový) -> **Other** (Jiné) -> **Service Fabric** -> **Service Fabric Project** (Projekt Service Fabric).

    ![Dialogové okno New Project (Nový projekt) v Eclipse](./media/service-fabric-tutorial-create-java-app/create-sf-proj-wizard.png)

3. V dialogovém okně **ServiceFabric Project Wizard** (Průvodce projektem Service Fabric) pojmenujte projekt **Voting** a stiskněte **Next** (Další).

    ![Výběr bezstavové služby v Javě dialogovém okně New Service (Nová služba)](./media/service-fabric-tutorial-create-java-app/name-sf-proj-wizard.png) 

4. Na stránce **Add Service** (Přidat službu) zvolte **Stateless Service** (Bezstavová služba) a pojmenujte službu **VotingWeb**. Kliknutím na **Finish** (Dokončit) vytvořte projekt.
   
    ![Vytvoření bezstavové služby]( ./media/service-fabric-tutorial-create-java-app/createvotingweb.png)

    Eclipse vytvoří aplikaci a projekt služby a zobrazí je v průzkumníku balíčků.

    ![Průzkumník balíčků v Eclipse po vytvoření aplikace]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

Následující tabulka obsahuje stručný popis jednotlivých položek v průzkumníku balíčků z předchozího snímku obrazovky. 
| **Položka průzkumníka balíčků** | **Popis** |
| --- | --- |
| PublishProfiles | Obsahuje soubory JSON popisující podrobnosti o profilu místního clusteru a clusteru Azure Service Fabric. Obsah těchto souborů používá modul plug-in při nasazování aplikace. |
| Scripts | Obsahuje pomocné skripty, které je možné použít z příkazového řádku k rychlé správě aplikace s clusterem. |
| VotingApplication | Obsahuje aplikaci Service Fabric, která se nasdílí do clusteru Service Fabric. |
| VotingWeb | Obsahuje zdrojové soubory bezstavové front-end služby spolu se souvisejícím souborem gradle pro sestavení. |
| build.gradle | Soubor Gradle sloužící ke správě projektu. |
| settings.gradle | Obsahuje názvy projektů Gradle v této složce. |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>Přidání kódu HTML a Javascriptu do služby VotingWeb
Pokud chcete přidat uživatelské rozhraní, které může bezstavová služba vykreslovat, přidejte do adresáře *VotingApplication/VotingWebPkg/Code* soubor HTML. Tento soubor HTML se pak vykreslí pomocí jednoduchého serveru HTTP vloženého do bezstavové služby v Javě. 

1. Rozbalte adresář *VotingApplication* a přejděte do adresáře *VotingApplication/VotingWebPkg/Code*. 

2. Klikněte pravým tlačítkem na adresář *Code* a pak klikněte na **New** (Nový) -> **Other** (Jiné).

3. Vytvořte složku *wwwroot* a klikněte na **Finish** (Dokončit).

    ![Vytvoření složky wwwroot v Eclipse](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. Do složky **wwwroot** přidejte soubor **index.html** a do tohoto souboru vložte následující obsah.

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="http://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']); 
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
          {},
          ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {            
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}        
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });        
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebservicejava-file"></a>Aktualizace souboru VotingWebService.java
V podprojektu **VotingWeb** otevřete soubor *VotingWeb/src/statelessservice/VotingWebService.java*. Soubor **VotingWebService** představuje bránu k bezstavové službě a je zodpovědný za nastavení naslouchacího procesu komunikace pro front-end rozhraní API. 

V souboru nahraďte obsah metody **createServiceInstanceListeners** následujícím kódem a uložte provedené změny.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();
    
    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>Přidání souboru HTTPCommunicationListener.java
Naslouchací proces komunikace protokolu HTTP funguje jako kontroler, který nastaví server HTTP a zveřejní rozhraní API definující akce hlasování. Klikněte pravým tlačítkem na balíček *statelessservice* ve složce *VotingWeb/src/statelessservice*, vyberte **New –> Other... –> General –> File** (Nový –> Jiné... –> Obecné –> Soubor) a klikněte na **Next** (Další).  Pojmenujte soubor *HttpCommunicationListener.java* a klikněte na **Finish** (Dokončit).  

Nahraďte obsah souboru následujícím kódem a pak uložte provedené změny.  V pozdější části [Aktualizace souboru HttpCommunicationListener.java](#updatelistener_anchor) se tento soubor upraví tak, aby vykresloval, načítal a zapisoval data hlasování z back-end služby.  Prozatím naslouchací událost jednoduše vrací statický kód HTML hlasovací aplikace.

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());
    
    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404; 
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";  
    private static final String ENCODING = "UTF-8";
    
    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0); 
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated 
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }
        
        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();
    
                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {    
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);              
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }
                      
                      fs.close();
                      os.close();
                    }  
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */
        
        server.setExecutor(null);
        server.start();
    }
    
    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>Konfigurace portu pro naslouchání
Po vytvoření front-end služby VotingWebService vybere Service Fabric port, na kterém bude služba naslouchat.  Služba VotingWebService funguje jako front-end této aplikace a přijímá externí provoz, takže tuto službu svážeme s pevným a dobře známým portem. V průzkumníku balíčků otevřete soubor *VotingWebService/VotingWebServicePkg/ServiceManifest.xml*.  V části **Resources** (Prostředky) vyhledejte prostředek **Endpoint** (Koncový bod) a změňte hodnotu **Port** na 8080 nebo jiný port. Pokud chcete aplikaci nasadit a spustit místně, port pro naslouchání aplikace musí být otevřený a dostupný na vašem počítači. Pod značku **ServiceManifest** vložte následující fragment kódu.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```  

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Přidání stavové back-end služby do aplikace
Když je teď hotová kostra služby webového rozhraní API v Javě, můžeme pokračovat a dokončit bezstavovou back-end službu.

Service Fabric umožňuje konzistentně a spolehlivě ukládat data přímo v rámci služby s použitím spolehlivých kolekcí. Spolehlivé kolekce jsou sady vysoce dostupných a spolehlivých tříd kolekcí. Použití těchto tříd budou znát všichni, kteří už někdy používali kolekce v Javě.

1. V průzkumníku balíčků klikněte pravým tlačítkem na **Voting** v rámci projektu aplikace a zvolte **Service Fabric > Add Service Fabric Service** (Service Fabric > Přidat službu Service Fabric).
   
2. V dialogovém okně **Add Service** (Přidat službu) zvolte **Stateful Service** (Bezstavová služba), pojmenujte službu **VotingData** a klikněte na **Add Service** (Přidat službu). 

    ![Přidání nové služby do stávající aplikace](./media/service-fabric-tutorial-create-java-app/addstatefuljava.png)

    Po vytvoření projektu služby budete mít ve své aplikaci dvě služby. V průběhu vytváření aplikace můžete stejným způsobem přidávat další služby. Každou z nich je možné nezávisle označovat verzí a upgradovat.

3. Eclipse vytvoří projekt služby a zobrazí ho v průzkumníku balíčků.

    ![Průzkumník řešení](./media/service-fabric-tutorial-create-java-app/packageexplorercompletejava.png)

### <a name="add-the-votingdataservicejava-file"></a>Přidání souboru VotingDataService.java

Soubor *VotingDataService.java* obsahuje metody obsahující logiku pro načítání, přidávání a odebírání hlasů ze spolehlivých kolekcí. Do třídy **VotingDataService** ve vytvořeném souboru *VotingDataService/src/statefulservice/VotingDataService.java* přidejte následující metody.

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List; 
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext; 

import rpcmethods.VotingRPC; 

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;
    
    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();
        
        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        
        return listeners;
    }
    
    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {                    

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
                        
            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue()); 
            }
            
            tx.close();                    
            

        } catch (Exception e) {
            e.printStackTrace();
        }  
        
        return CompletableFuture.completedFuture(tempMap);
    }
    
    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1); 

        try {
            
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();                    
            
            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;                            
                    return Integer.toString(numVotes);
                }
            }).get(); 
            
            tx.commitAsync().get();
            tx.close(); 

            status.set(1);                            
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1); 
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
            
            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();                    
            
            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
}
```

## <a name="create-the-communication-interface-to-your-application"></a>Vytvoření komunikačního rozhraní pro aplikaci 
Teď už jsou vytvořené kostry bezstavové front-end služby a back-end služby. Dalším krokem je propojení těchto dvou služeb. Front-end i back-end služba využívají rozhraní VotingRPC, které definuje operace hlasovací aplikace. Toto rozhraní se implementuje ve front-end i back-end službě za účelem umožnění vzdálených volání procedur (RPC) mezi těmito dvěma službami. Vzhledem k tomu, že Eclipse nepodporuje přidávání podprojektů Gradle, je potřeba přidat balíček obsahující toto rozhraní ručně. 

1. Klikněte pravým tlačítkem na projekt **Voting** v průzkumníku balíčků a klikněte na **New –> Other...** (Nový –> Jiné...).

2. V průvodci klikněte na **General –> Folder** (Obecné –>Složka) a pojmenujte složku **VotingRPC/src/rpcmethods** 

    ![Vytvoření balíčku VotingRPC](./media/service-fabric-tutorial-create-java-app/createvotingrpcpackage.png)

3. Ve složce *Voting/VotingRPC/src/rpcmethods* vytvořte soubor *VotingRPC.java* a do tohoto souboru **VotingRPC.java** vložte následující kód. 

    ```java
    package rpcmethods; 
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ``` 

4. V adresáři *Voting/VotingRPC* vytvořte soubor *build.gradle* a vložte do něj následující kód. Tento soubor gradle slouží k sestavení a vytvoření souboru .jar, který importují ostatní služby. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar'
    ```

5. Do souboru *Voting/settings.gradle* přidejte řádek, který do sestavení zahrne nově vytvořený projekt. 

    ```gradle 
    include ':VotingRPC'
    ```

6. V souboru *Voting/VotingWebService/src/statelessservice/HttpCommunicationListener.java* nahraďte blok komentáře následujícím kódem.  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {                    
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
                
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));                   
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();     
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");                    
                
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
                
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");
                
                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. Na začátek souboru *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* přidejte odpovídající příkaz import. 

    ```java
    import rpcmethods.VotingRPC; 
    ```

V této fázi jsou dokončené funkce rozhraní front-endu, back-endu a protokolu RPC. Další fází je odpovídající konfigurace skriptů Gradle před nasazením do clusteru Service Fabric. 

## <a name="walk-through-the-voting-sample-application"></a>Prohlídka ukázkové hlasovací aplikace
Hlasovací aplikace se skládá ze dvou služeb:
- Webová front-end služba (VotingWeb) – Webová front-end služba v Javě, která obsluhuje webovou stránku a zveřejňuje rozhraní API pro komunikaci s back-end službou.
- Back-end služba (VotingDataService) – Webová služba v Javě, která definuje metody pro ukládání hlasů volané přes vzdálená volání procedur (RPC).

![Diagram aplikace](./media/service-fabric-tutorial-create-java-app/walkthroughjavavoting.png)

Když v aplikaci provedete nějakou akci (přidání položky, hlasování, odebrání položky), dojde k následujícím událostem:
1. JavaScript odešle odpovídající požadavek do webového rozhraní API ve webové front-end službě jako požadavek HTTP.

2. Webová front-end služba pomocí integrované funkce vzdálené komunikace služby v Service Fabric vyhledá back-end službu a požadavek do ní přesměruje. 

3. Back-end služba definuje metody, které aktualizují výsledek ve spolehlivém slovníku. Obsah tohoto spolehlivého slovníku se replikuje do několika uzlů v rámci clusteru a ukládá se na disk. Veškerá data aplikace se ukládají v clusteru. 

## <a name="configure-gradle-scripts"></a>Konfigurace skriptů Gradle 

V této části se pro projekt nakonfigurují skripty Gradle. 

1. Nahraďte obsah souboru *Voting/build.gradle* následujícím kódem.

    ```gradle 
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    } 
        
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. Nahraďte obsah souboru *Voting/VotingWeb/build.gradle*.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ``` 

3. Nahraďte obsah souboru *Voting/VotingData/build.gradle*. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>Nasazení aplikace do místního clusteru
V tuto chvíli je aplikace připravená k nasazení do místního clusteru Service Fabric.

1. Klikněte pravým tlačítkem na projekt **Voting** v průzkumníku balíčků a kliknutím na **Service Fabric –> Build Application** (Service Fabric –> Sestavit aplikaci) svou aplikaci sestavte.

2. Spusťte místní cluster Service Fabric. Provedení tohoto kroku závisí na vašem vývojovém prostředí (Mac nebo Linux).

    Pokud používáte počítač Mac, spustíte místní cluster pomocí následujícího příkazu. Příkaz předávaný v parametru **-v** nahraďte cestou k vašemu pracovnímu prostoru.

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox servicefabricoss/service-fabric-onebox
    ``` 

    Pokud používáte počítač s Linuxem, spustíte místní cluster pomocí následujícího příkazu: 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

4. V průzkumníku balíčků pro Eclipse klikněte pravým tlačítkem na projekt **Voting** a pak klikněte na **Service Fabric –> Publish Application...** (Service Fabric –> Publikovat aplikaci...). 
5. V okně **Publish Application** (Publikovat aplikaci) vyberte v rozevíracím seznamu soubor **Local.json** a klikněte na **Publish** (Publikovat).
6. Ve webovém prohlížeči přejděte na adresu **http://localhost:8080** a zobrazte svou spuštěnou aplikaci na místním clusteru Service Fabric. 

## <a name="next-steps"></a>Další kroky
V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření služby v Javě jako spolehlivé stavové služby
> * Vytvoření služby v Javě jako bezstavové webové služby
> * Přidání rozhraní Java pro zpracování vzdálených volání procedur (RPC) mezi službami
> * Konfigurace skriptů Gradle
> * Sestavení a nasazení aplikace do místního clusteru Service Fabric

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Ladění a protokolování aplikací v místním clusteru](service-fabric-tutorial-debug-log-local-cluster.md)
