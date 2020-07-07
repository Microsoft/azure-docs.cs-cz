---
title: AZ jarní Cloud
description: Správa jarního cloudu Azure pomocí Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80298784"
---
# <a name="az-spring-cloud"></a>AZ jaře-Cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Správa jarního cloudu Azure pomocí Azure CLI

>[!Note]
> Jarní cloud Azure je aktuálně ve verzi Preview.  Tyto příkazy se můžou v budoucí verzi změnit nebo odebrat.

| AZ jaře-Cloud |  |
|------|------:|
| [AZ jaře-Cloud Create](#az-spring-cloud-create) | Vytvořte instanci cloudu Azure pro jaře. |
| [AZ jaře-Cloud DELETE](#az-spring-cloud-delete) | Odstraní instanci cloudu Azure jaře. |
| [AZ jarní-Cloud list](#az-spring-cloud-list) | Vypíše všechny instance Azure jaře cloudu v dané skupině prostředků, jinak vypíše ID předplatných. |
| [AZ jaře-Cloud show](#az-spring-cloud-show) | Zobrazuje podrobnosti o jarním cloudu Azure. |

| AZ pružinová cloudová aplikace | Příkazy pro správu aplikací v jarním cloudu Azure.  |
| ---- | ----: |
| [AZ pružinová cloudová aplikace Create](#az-spring-cloud-app-create) | Vytvořte novou aplikaci s výchozím nasazením v jarním cloudu Azure. |
| [AZ jaře-Cloud App DELETE](#az-spring-cloud-app-delete) | Odstranění aplikace v jarním cloudu Azure. |
| [AZ jaře-Cloud App Deploy](#az-spring-cloud-app-deploy) | Nasaďte ze zdrojového kódu nebo předem vytvořeného binárního souboru do aplikace a aktualizujte související konfigurace. |
| [AZ pružiny Cloud Apps list](#az-spring-cloud-app-list) | Vypíše všechny aplikace v rámci jarního cloudu Azure. |
| [AZ pružin-Cloud App restart](#az-spring-cloud-app-restart) | Restartujte instance aplikace pomocí výchozího nastavení produkčního nasazení. |
| [AZ jaře-Cloud App Scale](#az-spring-cloud-app-scale) | Ručně Škálujte aplikaci nebo její nasazení. |
| [AZ jaře-Cloud App set-Deployment](#az-spring-cloud-app-set-deployment) | Nastavte produkční nasazení aplikace. |
| [AZ jaře-Cloud App show](#az-spring-cloud-app-show) | Zobrazit podrobnosti o aplikaci v jarním cloudu Azure. |
| [AZ jaře-Cloud App show-Deploy-log](#az-spring-cloud-app-show-deploy-log) | Zobrazit protokoly sestavení pro nejnovější nasazení ze zdroje Ve výchozím nastavení se jedná o produkční nasazení. |
| [AZ jaře-Cloud App Start](#az-spring-cloud-app-start) | Spustí instance aplikace pomocí výchozích hodnot nasazení v produkčním prostředí. |
| [AZ jaře-Cloud App stop](#az-spring-cloud-app-stop) | Zastavte instance aplikace pomocí výchozího nastavení produkčního nasazení. |
| [AZ jaře-Cloud App Update](#az-spring-cloud-app-update) | Aktualizuje konfiguraci zadané aplikace. |

| AZ jaře-Cloud App Binding | Příkazy pro správu vazeb pomocí Azure Data Services.  Aby se tato nastavení projevila, je nutné aplikaci restartovat. |
| --- | ---: |
| [AZ pružiny Cloud App Binding list](#az-spring-cloud-app-binding-list) | Vypíše všechny vazby služby v aplikaci. |
| [AZ jaře-Cloud App Binding Remove](#az-spring-cloud-app-binding-remove) | Odebere vazbu služby z aplikace. |
| [AZ pružina Cloud App Binding show](#az-spring-cloud-app-binding-show) | Zobrazí podrobnosti vazby služby. |
| [AZ pružina Cloud App Binding Cosmos Add](#az-spring-cloud-app-binding-cosmos-add) | Vytvoření vazby Azure CosmosDB s aplikací |
| [AZ pružiny Cloud App Binding Cosmos Update](#az-spring-cloud-app-binding-cosmos-update) | Aktualizuje vazbu služby Azure CosmosDB. |
| [AZ jaře-Cloud App Binding MySQL Add](#az-spring-cloud-app-binding-mysql-add) | Naváže Azure Database for MySQL k aplikaci. |
| [AZ jarní-Cloud App Binding MySQL Update](#az-spring-cloud-app-binding-mysql-update) | Aktualizuje vazbu služby Azure Database for MySQL. |
| [AZ pružina Cloud App Binding Redis Add](#az-spring-cloud-app-binding-redis-add) | Vytvoření vazby mezipaměti Azure pro Redis s aplikací |
| [AZ pružiny Cloud App Binding Redis Update](#az-spring-cloud-app-binding-redis-update) | Aktualizuje službu Azure cache pro vazbu služby Redis. |

| AZ jaře-Cloud App Deployment | Příkazy pro správu životního cyklu nasazení aplikace v Azure jaře cloudu |
| --- | ---: |
| [AZ jaře-Cloud App Deployment Create](#az-spring-cloud-app-deployment-create) | Vytvořte pracovní nasazení pro aplikaci. |
| [AZ jaře-Cloud App Deployment DELETE](#az-spring-cloud-app-deployment-delete) | Odstraní nasazení aplikace. |
| [AZ jaře-Cloud App Deployment list](#az-spring-cloud-app-deployment-list) | Vypíše všechna nasazení v aplikaci. |
| [AZ jaře-Cloud App Deployment show](#az-spring-cloud-app-deployment-show) | Zobrazit podrobnosti o nasazení. |

| AZ jaře-Cloud config-server | Příkazy pro správu Azure jaře cloudového konfiguračního serveru. |
| --- | ---: |
| [AZ jaře-Cloud config-server Clear](#az-spring-cloud-config-server-clear) | Vymaže všechna nastavení na konfiguračním serveru. |
| [AZ jaře-Cloud config-server set](#az-spring-cloud-config-server-set) | Definujte konfigurační server ze souboru YAML. |
| [AZ jaře-Cloud config-server show](#az-spring-cloud-config-server-show) | Zobrazit konfiguraci konfiguračního serveru. |
| [AZ jaře-Cloud config server Git set](#az-spring-cloud-config-server-git-set) | Definujte vlastnosti Gitu pro konfigurační server.  Předchozí hodnoty budou přepsány. |
| [AZ jaře-Cloud config server Git úložiště přidat](#az-spring-cloud-config-server-git-repo-add) | Přidejte novou konfiguraci úložiště Git do konfiguračního serveru. |
| [AZ jaře-Cloud config server list úložiště Git](#az-spring-cloud-config-server-git-repo-list) | Vypíše všechny konfigurace úložiště Git pro konfigurační server. |
| [AZ jaře-Cloud config server Git Remove](#az-spring-cloud-config-server-git-repo-remove) | Odstraňte zadané úložiště Git z konfiguračního serveru. |

| AZ pružina Cloud test-Endpoint | Příkazy pro správu testování koncových bodů v Azure jaře cloudu |
| --- | ---: |
| [AZ jaře-Cloud test-Endpoint Disable](#az-spring-cloud-test-endpoint-disable) | Vypnutí testovacího koncového bodu. |
| [AZ jaře-Cloud test-Endpoint Enable](#az-spring-cloud-test-endpoint-enable) | Povolte koncový bod testu. |
| [AZ pružiny Cloud test-Endpoint list](#az-spring-cloud-test-endpoint-list) | Vypíše klíče testovacího koncového bodu. |
| [AZ jaře-Cloud test-Endpoint rerenew-Key](#az-spring-cloud-test-endpoint-renew-key) | Znovu vygenerujte klíč testovacího koncového bodu. |

## <a name="az-spring-cloud-create"></a>AZ jaře-Cloud Create

Vytvořte novou aplikaci s výchozím nasazením v jarním cloudu Azure.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Název této instance Azure jaře cloudu |
| --Resource-Group-g | Určuje skupinu prostředků pro tuto aplikaci.  Konfigurace výchozí skupiny pomocí`az configure --defaults group=<name>` |

| Volitelné parametry | |
| --- | ---: |
| --Location-l | Určuje umístění serveru pro tuto aplikaci.  Vyhledat platná umístění pomocí`az account list-locations` |
| --No-Wait | Nespouštějte dlouho probíhající operace.

### <a name="examples"></a>Příklady

Vytvoření nového Azure jarního cloudu v WestUS

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>AZ jaře-Cloud DELETE

Odstraní instanci cloudu Azure jaře.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Název instance jarního cloudu Azure, která se má odstranit |
| --Resource-Group-g | Název skupiny prostředků, do které patří Azure jaře Cloud. |

| Volitelné parametry | |
| --- | ---: |
| -No – čekání | Nečekejte na dokončení dlouho spuštěných operací. |

### <a name="example"></a>Příklad

Odstraní instanci cloudu Azure jaře s názvem "Mojesluzba" z "MyResourceGroup".

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>AZ jarní-Cloud list

Vypíše všechny instance Azure jaře cloudu přidružené k dané skupině prostředků. Pokud není zadaná žádná skupina prostředků, Seznamte se s ID předplatných.

```azurecli
az spring-cloud list --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --Resource-Group-g | Název skupiny prostředků. |

## <a name="az-spring-cloud-show"></a>AZ jaře-Cloud show

Zobrazí podrobnosti zadané instance cloudového cloudu Azure.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Název instance jarního cloudu Azure |
| --Resource-Group-g | Název skupiny prostředků, do které patří instance cloudu služby Azure jaře

## <a name="az-spring-cloud-app-create"></a>AZ pružinová cloudová aplikace Create

Vytvořte novou aplikaci v jarním cloudu Azure.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --CPU | Počet virtuálních jader na instanci.  Výchozí hodnota: 1. |
| --Enable – trvalé úložiště | Logická hodnota.  Pokud má hodnotu true, připojí 50 GB disk s výchozí cestou. |
| --instance-počet | Počet instancí  Výchozí hodnota: 1. |
| --je-veřejné | Logická hodnota.  Pokud má hodnotu true, přiřadí veřejnou doménu. |
| --paměť | Počet GB paměti na instanci.  Výchozí hodnota: 1. |

### <a name="examples"></a>Příklady

Vytvořte aplikaci s výchozí konfigurací.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Vytvořte veřejně dostupnou aplikaci se 3 instancemi.  Každá instance má 3 GB paměti a 2 jádra procesoru.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>AZ jaře-Cloud App DELETE

Odstraní aplikaci v Azure jaře cloudu.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-deploy"></a>AZ jaře-Cloud App Deploy

Nasaďte aplikaci do jarního cloudu Azure ze zdrojového kódu nebo předem vytvořeného binárního souboru a aktualizujte související konfigurace.

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --CPU | Počet virtuálních jader CPI na instanci. |
| --Deployment-d | Název existujícího nasazení aplikace  Pokud není zadaný, použije se výchozí nasazení v produkčním prostředí. |
| --ENV | Proměnné prostředí oddělené mezerou ve formátu klíč [= hodnota]. |
| --instance-počet | Počet instancí. |
| --JAR – cesta | Pokud je tato cesta k dispozici, nasaďte jar z dané cesty. V opačném případě nasaďte aktuální složku jako tar. |
| --JVM – možnosti | Řetězec obsahující možnosti JVM  Chcete-li zabránit chybám při analýze prostředí, použijte místo ' '. Například `--jvm-options='-Xms1024m -Xmx2048m` . |
| --paměť | Počet GB paměti na instanci. |
| --No-Wait | Nečekejte na dokončení dlouho spuštěných operací. |
| --Běhová verze | Běhová verze jazyka používaného v aplikaci  Povolené hodnoty: `Java_11` , `Java_8` . |
| --Target-Module | Podřízený modul, který má být nasazen.  Vyžaduje se, když ze zdrojového kódu sestavíte víc balíčků jar. |
| --verze | Verze nasazení  Nezměněno, pokud není nastaveno. |

### <a name="examples"></a>Příklady

Nasaďte zdrojový kód do aplikace. Tím se zabalí aktuální adresář, sestaví se binární soubor pomocí služby Pivot Build Service a pak se nasadí do aplikace.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Nasaďte předem připravený jar do aplikace pomocí možností JVM a proměnných prostředí.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Nasaďte zdrojový kód do konkrétního nasazení aplikace.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>AZ pružiny Cloud Apps list

Vypíše všechny aplikace v instanci cloudu Azure jaře.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Povinné parametry | |
| --- | ---: |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-restart"></a>AZ pružin-Cloud App restart

Restartujte instance aplikace.  Ve výchozím nastavení se jedná o produkční nasazení.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --Deployment-d | Název existujícího nasazení aplikace  Pokud není zadaný, použije se výchozí nasazení v produkčním prostředí. |
| --No-Wait | Nečekejte na dokončení dlouho spuštěných operací. |

## <a name="az-spring-cloud-app-scale"></a>AZ jaře-Cloud App Scale

Ručně Škálujte aplikaci nebo její nasazení.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --CPU | Počet jader virtuálního procesoru na instanci aplikace |
| --Deployment-d | Název existujícího nasazení aplikace  Pokud není zadaný, použije se výchozí nasazení v produkčním prostředí. |
| --instance-počet | Počet instancí této aplikace |
| --paměť | Počet GB paměti na instanci aplikace |
| --No-Wait | Nečekejte na dokončení dlouhotrvajících operací. |

### <a name="examples"></a>Příklady

Nahorizontální navýšení kapacity aplikace na 4 PROCESORy a 8 GB paměti na jednu instanci.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Horizontální navýšení kapacity nasazení aplikace na 5 instancí.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>AZ jaře-Cloud App set-Deployment

Nastavte možnosti konfigurace pro produkční nasazení aplikace.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --Deployment-d | Název existujícího nasazení aplikace |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --No-Wait | Nečekejte na dokončení dlouhotrvajících operací. |

### <a name="examples"></a>Příklady

Proměňte pracovní nasazení aplikace do produkčního prostředí.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>AZ jaře-Cloud App show

Zobrazit podrobnosti o aplikaci v jarním cloudu Azure.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-show-deploy-log"></a>AZ jaře-Cloud App show-Deploy-log

Zobrazit protokol sestavení posledního nasazení ze zdrojového kódu.  Ve výchozím nastavení se jedná o produkční prostředí.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --Deployment-d | Název existujícího nasazení aplikace  Ve výchozím nastavení se jedná o produkční prostředí. |

## <a name="az-spring-cloud-app-start"></a>AZ jaře-Cloud App Start

Spustí instance aplikace.  Ve výchozím nastavení se jedná o produkční prostředí.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --Deployment-d | Název existujícího nasazení aplikace  Ve výchozím nastavení se jedná o produkční prostředí. |
| --No-Wait | Nečekejte na dokončení dlouhotrvajících operací. |

## <a name="az-spring-cloud-app-stop"></a>AZ jaře-Cloud App stop

Zastavte instance aplikace.  Ve výchozím nastavení se jedná o produkční prostředí.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --Deployment-d | Název existujícího nasazení aplikace  Ve výchozím nastavení se jedná o produkční prostředí. |
| --No-Wait | Nečekejte na dokončení dlouhotrvajících operací. |

## <a name="az-spring-cloud-app-update"></a>AZ jaře-Cloud App Update

Aktualizuje uloženou konfiguraci aplikace.

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Povinné parametry | |
| --- | ---: |
| --Name-n | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --Deployment-d | Název existujícího nasazení aplikace  Ve výchozím nastavení se jedná o produkční prostředí. |
| --Enable – trvalé úložiště | Datového.  Pokud má hodnotu true, připojte 50 GB disk s výchozí cestou. |
| --ENV | Proměnné prostředí oddělené mezerou ve formátu klíč [= hodnota]. |
| --je-veřejné | Datového.  Pokud má hodnotu true, přiřaďte aplikaci veřejnou doménu. |
| --JVM – možnosti | Řetězec obsahující možnosti JVM  Chcete-li zabránit chybám při analýze prostředí, použijte místo ' '. Například `--jvm-options='-Xms1024m -Xmx2048m` . |
| --No-Wait | Nečekejte na dokončení dlouhotrvajících operací. |
| --Běhová verze | Běhová verze jazyka používaného v aplikaci  Povolené hodnoty: `Java_11` , `Java_8` . |

### <a name="example"></a>Příklad

Přidejte proměnnou prostředí pro aplikaci.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>AZ pružiny Cloud App Binding list

Vypíše všechny vazby služby v aplikaci.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-binding-remove"></a>AZ jaře-Cloud App Binding Remove

Odebere vazbu služby z aplikace.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název vazby služby, která se má odebrat |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-binding-show"></a>AZ pružina Cloud App Binding show

Zobrazí podrobnosti vazby služby.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název vazby služby. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>AZ pružina Cloud App Binding Cosmos Add

Naváže Azure Cosmos DB k aplikaci.

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Povinné parametry | |
| --- | ---: |
| --API-Type | Zadejte typ rozhraní API pomocí jedné z následujících hodnot: Cassandra, Gremlin, Mongo, SQL, Table. |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název vazby služby. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

|Volitelné parametry | |
| --- | ---: |
| --Collection-Name | Název kolekce  Požadováno při použití Gremlin. |
| --Database-Name | Název databáze.  Požadováno při použití Mongo, SQL a Gremlin. |
| --klíčové místo | Cassandra klíčového místa.  Požadováno při použití Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>AZ pružiny Cloud App Binding Cosmos Update

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název vazby služby. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

|Volitelné parametry | |
| --- | ---: |
| --Collection-Name | Název kolekce  Požadováno při použití Gremlin. |
| --Database-Name | Název databáze.  Požadováno při použití Mongo, SQL a Gremlin. |
| --klíčové místo | Cassandra klíčového místa.  Požadováno při použití Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>AZ jaře-Cloud App Binding MySQL Add

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Database-Name | Název databáze. |
| --klíč | Klíč rozhraní API služby. |
| --Name | Název vazby služby. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Resource-ID | ID prostředku Azure služby, se kterou se má vytvořit vazba |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |
| --username | Uživatelské jméno pro přístup k databázi |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>AZ jarní-Cloud App Binding MySQL Update

Aktualizujte připojení vazby služby k aplikaci na Azure Database for MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název vazby služby. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --Database-Name | Název databáze. |
| --klíč | Klíč rozhraní API služby. |
| --username | Uživatelské jméno pro přístup k databázi |

## <a name="az-spring-cloud-app-binding-redis-add"></a>AZ pružina Cloud App Binding Redis Add

Vytvoření vazby mezipaměti Azure pro Redis s aplikací

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název vazby služby. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Resource-ID | ID prostředku Azure služby, se kterou chcete vytvořit propojení. |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --Disable-SSL | Zakáže protokol TLS. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>AZ pružiny Cloud App Binding Redis Update

Aktualizuje vazbu služby pro Azure cache pro Redis.

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název vazby služby. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --Disable-SSL | Zakáže protokol TLS. |

## <a name="az-spring-cloud-app-deployment-create"></a>AZ jaře-Cloud App Deployment Create

Vytvořte pracovní nasazení pro aplikaci.

Pokud chcete nasadit kód nebo aktualizovat nastavení na existující nasazení, použijte `az spring-cloud app deploy --deployment <staging-deployment>` nebo "AZ jaře-Cloud App Update--Deployment <staging deployment> .

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název vazby služby. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --CPU | Počet jader virtuálního procesoru na instanci.  Výchozí hodnota: 1 |
| --ENV | Proměnné prostředí oddělené mezerou ve formátu klíč [= hodnota]. |
| --instance-počet | Počet instancí. Výchozí hodnota: 1. |
| --JAR – cesta | Pokud je tato je k dispozici, nasaďte jar.  V opačném případě nasaďte aktuální složku jako tar. |
| --JVM – možnosti | Řetězec obsahující možnosti JVM  Chcete-li zabránit chybám při analýze prostředí, použijte místo ' '. Například `--jvm-options='-Xms1024m -Xmx2048m` . |
| --paměť | Počet GB paměti na instanci. |
| --No-Wait | Nečekejte na dokončení dlouho spuštěných operací. |
| --Běhová verze | Běhová verze jazyka používaného v aplikaci  Povolené hodnoty: `Java_11` , `Java_8` . |
| --Skip-Clone-Settings | Vytvořte pracovní nasazení klonováním aktuálního nastavení nasazení v produkčním prostředí. |
| --Target-Module | Podřízený modul, který má být nasazen.  Vyžaduje se, když ze zdrojového kódu sestavíte víc balíčků jar. |
| --verze | Verze nasazení  Nezměněno, pokud není nastaveno. |

### <a name="examples"></a>Příklady

Nasaďte zdrojový kód do nového nasazení aplikace.  Tím se zabalí aktuální adresář, sestaví se binární soubor pomocí Pivotového systému sestavení a pak se nasadí.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Nasaďte předem připravený jar do aplikace s možnostmi JVM a s proměnnými prostředí.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>AZ jaře-Cloud App Deployment DELETE

Odstraní nasazení aplikace.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název nasazení. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-deployment-list"></a>AZ jaře-Cloud App Deployment list

Vypíše všechna nasazení v aplikaci.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-deployment-show"></a>AZ jaře-Cloud App Deployment show

Zobrazit podrobnosti nasazení.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Name | Název nasazení. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --Service-s | Název služby jarního cloudu Azure.  Výchozí službu můžete nakonfigurovat pomocí nástroje `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-config-server-clear"></a>AZ jaře-Cloud config-server Clear

Vymaže všechna nastavení konfigurace na konfiguračním serveru.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-config-server-set"></a>AZ jaře-Cloud config-server set

Nastavte konfigurační nastavení na konfiguračním serveru pomocí souboru YAML.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --config-File | Cesta k souboru manifestu YAML pro konfiguraci konfiguračního serveru. |
| --Name | Název služby jarního cloudu Azure. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --No-Wait | Nespouštějte dlouho probíhající operace.

## <a name="az-spring-cloud-config-server-show"></a>AZ jaře-Cloud config-server show

Zobrazit nastavení konfiguračního serveru.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-config-server-git-set"></a>AZ jaře-Cloud config-server Git set

Nastavte vlastnosti Gitu pro konfigurační server.  Tato akce přepíše všechny existující vlastnosti Gitu.

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --URI | Identifikátor URI přidané konfigurace |

| Volitelné parametry | |
| --- | ---: |
| --odložit | Dočasně uloží objekt do místní mezipaměti místo odeslání do Azure.  Použijte `az cache` k zobrazení nebo vymazání. |
| --Host-Key | Klíč hostitele pro přidanou konfiguraci |
| --Host-klíč-– algoritmus | Algoritmus klíče hostitele pro přidanou konfiguraci. |
| --popisek | Popisek přidané konfigurace |
| --password | Heslo přidané konfigurace |
| --privátní klíč | Privátní klíč přidané konfigurace |
| --Search-Paths | Cesty hledání přidané konfigurace  Pro více cest použijte oddělovače čárky. |
| --Strict-Host-kontrola klíče | Umožňuje striktní kontrolu klíčů hostitele přidané konfigurace. |
| --username | Uživatelské jméno přidané konfigurace |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>AZ jaře-Cloud config-server Git úložiště přidat

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --úložiště – název | Identifikátor URI úložiště |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --URI | Identifikátor URI přidané konfigurace |

| Volitelné parametry | |
| --- | ---: |
| --odložit | Dočasně uloží objekt do místní mezipaměti místo odeslání do Azure.  Použijte `az cache` k zobrazení nebo vymazání. |
| --Host-Key | Klíč hostitele pro přidanou konfiguraci |
| --Host-klíč-– algoritmus | Algoritmus klíče hostitele pro přidanou konfiguraci. |
| --popisek | Popisek přidané konfigurace |
| --password | Heslo přidané konfigurace |
| --vzor | Vzor úložiště  Pro více cest použijte oddělovače čárky.|
| --privátní klíč | Privátní klíč přidané konfigurace |
| --Search-Paths | Cesty hledání přidané konfigurace  Pro více cest použijte oddělovače čárky. |
| --Strict-Host-kontrola klíče | Umožňuje striktní kontrolu klíčů hostitele přidané konfigurace. |
| --username | Uživatelské jméno přidané konfigurace |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>AZ jaře-Cloud config-server list úložiště Git

Zobrazit seznam všech úložišť Git definovaných v konfiguračním serveru

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --odložit | Dočasně uloží objekt do místní mezipaměti místo odeslání do Azure.  Použijte `az cache` k zobrazení nebo vymazání. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>AZ jaře-Cloud config-server Git Remove

Odebere existující konfiguraci úložiště Git z konfiguračního serveru.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --úložiště – název | Identifikátor URI úložiště |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --odložit | Dočasně uloží objekt do místní mezipaměti místo odeslání do Azure.  Použijte `az cache` k zobrazení nebo vymazání. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>AZ jaře-Cloud test-Endpoint Disable

Zakázání testovacího koncového bodu pro jarní cloud Azure

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-test-endpoint-enable"></a>AZ jaře-Cloud test-Endpoint Enable

Povolte testovací koncový bod pro jarní cloud Azure. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-test-endpoint-list"></a>AZ pružiny Cloud test-Endpoint list 

Vypíše dostupné klíče testovacího koncového bodu pro jarní cloud Azure.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |

| Volitelné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --Deployment-d | Název existujícího nasazení aplikace  Pokud není zadaný, použije se výchozí hodnota produkce. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>AZ jaře-Cloud test-Endpoint rerenew-Key

Znovu vygenerujte klíč testovacího koncového bodu pro jarní cloud Azure.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Povinné parametry | |
| --- | ---: |
| --Name | Název služby jarního cloudu Azure. |
| --Resource-Group-g | Název skupiny prostředků.  Výchozí skupinu můžete nakonfigurovat pomocí `az configure --defaults group=<name>` . |
| --typ | Typ klíče testovacího koncového bodu  Povolené hodnoty: primární, sekundární. |
