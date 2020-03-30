---
title: az jarní mrak
description: Správa Azure Spring Cloud pomocí azure cli
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298784"
---
# <a name="az-spring-cloud"></a>az jarní mrak

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Správa Azure Spring Cloud pomocí azure cli

>[!Note]
> Azure Spring Cloud je momentálně ve verzi Preview.  Tyto příkazy mohou být změněny nebo odebrány v budoucí verzi.

| az jarní mrak |  |
|------|------:|
| [az jarní mrak vytvořit](#az-spring-cloud-create) | Vytvořte instanci Azure Spring Cloud. |
| [az jarní mrak odstranit](#az-spring-cloud-delete) | Odstraňte instanci Azure Spring Cloud. |
| [az jarní cloud seznam](#az-spring-cloud-list) | Seznam všech instancí Azure Spring Cloud v dané skupině prostředků, jinak seznam ID předplatného. |
| [az jaro-mrak show](#az-spring-cloud-show) | Zobrazte podrobnosti pro Azure Spring Cloud. |

| aplikace az spring-cloud | Příkazy pro správu aplikací v Azure Spring Cloudu.  |
| ---- | ----: |
| [az jarní cloud aplikace vytvořit](#az-spring-cloud-app-create) | Vytvořte novou aplikaci s výchozím nasazením ve službě Azure Spring Cloud. |
| [az jarní cloud app odstranit](#az-spring-cloud-app-delete) | Odstraňte aplikaci v Azure Spring Cloudu. |
| [nasazení aplikace az spring-cloud](#az-spring-cloud-app-deploy) | Nasaďte ze zdrojového kódu nebo předem sestaveného binárního souboru do aplikace a aktualizujte související konfigurace. |
| [az jarní-cloud seznam aplikací](#az-spring-cloud-app-list) | Seznam všech aplikací v Azure Spring Cloud. |
| [az jarní cloud app restart](#az-spring-cloud-app-restart) | Restartujte instance aplikace pomocí výchozích nastavení produkčního nasazení. |
| [az jarní cloudové měřítko aplikace](#az-spring-cloud-app-scale) | Ručně škálovat aplikaci nebo její nasazení. |
| [az jarní cloud set-nasazení aplikace](#az-spring-cloud-app-set-deployment) | Nastavte produkční nasazení aplikace. |
| [az jarní cloud app show](#az-spring-cloud-app-show) | Zobrazte podrobnosti o aplikaci v Azure Spring Cloud. |
| [az jarní-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Zobrazit protokoly sestavení pro nejnovější nasazení ze zdroje. Výchozí je produkční nasazení. |
| [az jarní cloud spuštění aplikace](#az-spring-cloud-app-start) | Spusťte instance aplikace pomocí výchozích nastavení produkčního nasazení. |
| [az jarní cloud app stop](#az-spring-cloud-app-stop) | Zastavte instance aplikace pomocí výchozích nastavení nasazení v produkčním prostředí. |
| [aktualizace aplikace az spring-cloud](#az-spring-cloud-app-update) | Aktualizujte konfiguraci zadané aplikace. |

| az jarní cloud oválná aplikace | Příkazy pro správu vazeb pomocí Azure Data Services.  Aplikace musí být restartována, než se tato nastavení projeví. |
| --- | ---: |
| [az jarní-cloud seznam vazeb aplikací](#az-spring-cloud-app-binding-list) | Seznam všech vazeb služby v aplikaci. |
| [az jarní cloud aplikace vazba odebrat](#az-spring-cloud-app-binding-remove) | Odeberte vazby služby z aplikace. |
| [az jarní cloud aplikace vazby show](#az-spring-cloud-app-binding-show) | Zobrazit podrobnosti o vazbě služby. |
| [az spring-cloud aplikace vazby cosmos přidat](#az-spring-cloud-app-binding-cosmos-add) | Svázat Azure CosmosDB s aplikací. |
| [aktualizace aplikace az spring-cloud cosmos](#az-spring-cloud-app-binding-cosmos-update) | Aktualizujte vazby služby Azure CosmosDB. |
| [az jaro-cloud aplikace vazba mysql přidat](#az-spring-cloud-app-binding-mysql-add) | Svázat databázi Azure pro MySQL s aplikací. |
| [az jarní cloud app vazba mysql update](#az-spring-cloud-app-binding-mysql-update) | Aktualizujte vazby služby Azure Database for MySQL. |
| [az spring-cloud app vazba redis přidat](#az-spring-cloud-app-binding-redis-add) | Spojte azure mezipaměť pro Redis s aplikací. |
| [aktualizace redis vazby aplikace az spring-cloud](#az-spring-cloud-app-binding-redis-update) | Aktualizujte vazby služby Azure Cache for Redis. |

| nasazení aplikace az spring-cloud | Příkazy pro správu životního cyklu nasazení aplikace v Azure Spring Cloud. |
| --- | ---: |
| [az jarní cloud ové nasazení aplikace vytvořit](#az-spring-cloud-app-deployment-create) | Vytvořte pracovní nasazení pro aplikaci. |
| [odstranění nasazení aplikace az spring-cloud](#az-spring-cloud-app-deployment-delete) | Odstraňte nasazení aplikace. |
| [seznam nasazení aplikací az spring-cloud](#az-spring-cloud-app-deployment-list) | Seznam všech nasazení v aplikaci. |
| [az jarní cloudové nasazení aplikace show](#az-spring-cloud-app-deployment-show) | Zobrazit podrobnosti o nasazení. |

| az jarní cloud config-server | Příkazy pro správu azure spring cloudový konfigurační server. |
| --- | ---: |
| [az jaro-cloud config-server jasné](#az-spring-cloud-config-server-clear) | Vymažte všechna nastavení na konfiguračním serveru. |
| [az jarní cloud config-server set](#az-spring-cloud-config-server-set) | Definujte konfigurační server ze souboru YAML. |
| [az jaro-cloud config-server show](#az-spring-cloud-config-server-show) | Zobrazit konfiguraci serveru Konfigurace. |
| [az spring-cloud config server git set](#az-spring-cloud-config-server-git-set) | Definujte vlastnosti git pro konfigurační server.  Předchozí hodnoty budou přepsány. |
| [az jaro-cloud config server git repo přidat](#az-spring-cloud-config-server-git-repo-add) | Přidejte novou konfiguraci úložiště git na konfigurační server. |
| [az spring-cloud config server git úložiště](#az-spring-cloud-config-server-git-repo-list) | Seznam všech konfigurací úložiště git pro konfigurační server. |
| [az jaro-cloud config server git repo odebrat](#az-spring-cloud-config-server-git-repo-remove) | Odeberte zadané úložiště git z konfiguračního serveru. |

| az pružina-cloud test-koncový bod | Příkazy pro správu testování koncových bodů v Azure Spring Cloud |
| --- | ---: |
| [az jaro-cloud test-koncový bod zakázat](#az-spring-cloud-test-endpoint-disable) | Zakažte koncový bod testu. |
| [az spring-cloud test-koncový bod povolit](#az-spring-cloud-test-endpoint-enable) | Povolte koncový bod testu. |
| [az jaro-cloud test-koncový bod seznamu](#az-spring-cloud-test-endpoint-list) | Seznam testovacích klíčů koncového bodu. |
| [az spring-cloud test-koncový bod obnovit klíč](#az-spring-cloud-test-endpoint-renew-key) | Znovu vygenerovat klíč koncového bodu testu. |

## <a name="az-spring-cloud-create"></a>az jarní mrak vytvořit

Vytvořte novou aplikaci s výchozím nasazením ve službě Azure Spring Cloud.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --název -n | Název této instance Azure Spring Cloud. |
| --resource-group -g | Určuje skupinu prostředků pro tuto aplikaci.  Konfigurace výchozí skupiny pomocí`az configure --defaults group=<name>` |

| Volitelné parametry | |
| --- | ---: |
| --umístění -l | Určuje umístění serveru pro tuto aplikaci.  Vyhledání platných míst pomocí`az account list-locations` |
| --ne-čekat | Nepoužívejte pro dlouhotrvající operace k dokončení.

### <a name="examples"></a>Příklady

Vytvoření nového Azure Spring Cloud ve WestUS

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az jarní mrak odstranit

Odstraňte instanci Azure Spring Cloud.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --název -n | Název instance Azure Spring Cloud, která má být odstraněna. |
| --resource-group -g | Název skupiny prostředků, do které patří Azure Spring Cloud. |

| Volitelné parametry | |
| --- | ---: |
| -ne-čekat | Nečekejte na dokončení dlouhotrvajících operací. |

### <a name="example"></a>Příklad

Odstraňte instanci Azure Spring Cloud s názvem MyService z myresourcegroup.

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az jarní cloud seznam

Seznam všech instancí Azure Spring Cloud přidružených ke skupině prostředků. Pokud není zadána žádná skupina prostředků, uveďte ID předplatného.

```azurecli
az spring-cloud list --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --resource-group -g | Název skupiny prostředků. |

## <a name="az-spring-cloud-show"></a>az jaro-mrak show

Zobrazte podrobnosti pro zadanou instanci Azure Spring Cloud.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --název -n | Název instance Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků, do které patří instance Azure Spring Cloud.

## <a name="az-spring-cloud-app-create"></a>az jarní cloud aplikace vytvořit

Vytvořte novou aplikaci v Azure Spring Cloudu.

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
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --cpu | Počet virtuálních jader na instanci.  Výchozí nastavení: 1. |
| --enable-persistent-storage | Logická hodnota.  Pokud true, připojí 50GB disk s výchozí cestou. |
| --počet instancí | Počet instancí.  Výchozí nastavení: 1. |
| --je-veřejná | Logická hodnota.  Pokud true, přiřadí public domain. |
| --paměť | Počet GB paměti na instanci.  Výchozí nastavení: 1. |

### <a name="examples"></a>Příklady

Vytvořte aplikaci s výchozí konfigurací.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Vytvořte veřejně přístupnou aplikaci se 3 instancemi.  Každá instance má 3 GB paměti a 2 jádra procesoru.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az jarní cloud app odstranit

Odstraní aplikaci v Azure Spring Cloud.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Povinné parametry | |
| --- | ---: |
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-app-deploy"></a>nasazení aplikace az spring-cloud

Nasaďte aplikaci do Azure Spring Cloud ze zdrojového kódu nebo předem sestaveného binárního souboru a aktualizujte související konfigurace.

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
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --cpu | Počet virtuálních jader CPI na instanci. |
| --nasazení -d | Název existujícího nasazení aplikace.  Pokud není zadáno, výchozí hodnota pro produkční nasazení. |
| --env | Proměnné prostředí oddělené mezerami ve formátu 'key[=value]'. |
| --počet instancí | Počet instancí. |
| --jar-cesta | Pokud je k dispozici, nasadit jar z dané cesty. V opačném případě nasaďte aktuální složku jako dehet. |
| --jvm-možnosti | Řetězec obsahující možnosti JVM.  Místo chyb při analýzách prostředí použijte '=' místo ' '. `--jvm-options='-Xms1024m -Xmx2048m`Např. |
| --paměť | Počet GB paměti na instanci. |
| --ne-čekat | Nečekejte na dokončení dlouhotrvajících operací. |
| --runtime-version --runtime-version --runtime-version --run | Runtime verze jazyka používaného v aplikaci.  Povolené `Java_11`hodnoty: `Java_8`, . |
| --cílový modul | Podřízený modul, který má být nasazen.  Povinné při více jar balíčky jsou vytvořeny ze zdrojového kódu. |
| --verze | Verze nasazení.  Beze změny, pokud není nastavena. |

### <a name="examples"></a>Příklady

Nasaďte zdrojový kód do aplikace. Tím se zabalí aktuální adresář, vytvoří binární soubor pomocí služby pivotal build service a pak se nasadí do aplikace.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Nasaďte předem sestavenou nádobu do aplikace pomocí možností JVM a proměnných prostředí.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Nasaďte zdrojový kód na konkrétní nasazení aplikace.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az jarní-cloud seznam aplikací

Seznam všech aplikací v instanci Azure Spring Cloud.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Povinné parametry | |
| --- | ---: |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-app-restart"></a>az jarní cloud app restart

Restartujte instance aplikace.  Výchozí nastavení pro produkční nasazení.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --nasazení -d | Název existujícího nasazení aplikace.  Pokud není zadáno, výchozí hodnota pro produkční nasazení. |
| --ne-čekat | Nečekejte na dokončení dlouhotrvajících operací. |

## <a name="az-spring-cloud-app-scale"></a>az jarní cloudové měřítko aplikace

Ručně škálovat aplikaci nebo její nasazení.

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
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --cpu | Počet virtuálních jader procesoru na instanci aplikace. |
| --nasazení -d | Název existujícího nasazení aplikace.  Pokud není zadáno, výchozí hodnota pro produkční nasazení. |
| --počet instancí | Počet instancí této aplikace. |
| --paměť | Počet GB paměti na instanci aplikace. |
| --ne-čekat | Nečekejte na dokončení dlouhotrvajících operací. |

### <a name="examples"></a>Příklady

Vertikálně navýšit kapacitu aplikace na 4 jádra procesoru a 8 GB paměti na instanci.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Horizontální navýšení kapacity nasazení aplikace na 5 instancí.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az jarní cloud set-nasazení aplikace

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
| --nasazení -d | Název existujícího nasazení aplikace. |
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --ne-čekat | Nečekejte na dokončení dlouhotrvajících operací. |

### <a name="examples"></a>Příklady

Swap pracovní nasazení aplikace do produkčního prostředí.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az jarní cloud app show

Zobrazte podrobnosti o aplikaci v Azure Spring Cloud.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Povinné parametry | |
| --- | ---: |
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az jarní-cloud app show-deploy-log

Zobrazit protokol sestavení posledního nasazení ze zdrojového kódu.  Výchozí je produkční prostředí.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Povinné parametry | |
| --- | ---: |
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --nasazení -d | Název existujícího nasazení aplikace.  Výchozí je produkční prostředí. |

## <a name="az-spring-cloud-app-start"></a>az jarní cloud spuštění aplikace

Spustí instance aplikace.  Výchozí je produkční prostředí.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --nasazení -d | Název existujícího nasazení aplikace.  Výchozí je produkční prostředí. |
| --ne-čekat | Nečekejte na dokončení dlouhotrvajících operací. |

## <a name="az-spring-cloud-app-stop"></a>az jarní cloud app stop

Zastavit instance aplikace.  Výchozí je produkční prostředí.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --nasazení -d | Název existujícího nasazení aplikace.  Výchozí je produkční prostředí. |
| --ne-čekat | Nečekejte na dokončení dlouhotrvajících operací. |

## <a name="az-spring-cloud-app-update"></a>aktualizace aplikace az spring-cloud

Aktualizujte uloženou konfiguraci aplikace.

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
| --název -n | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --nasazení -d | Název existujícího nasazení aplikace.  Výchozí je produkční prostředí. |
| --enable-persistent-storage | Boolean.  Pokud true, připojte 50GB disk s výchozí cestou. |
| --env | Proměnné prostředí oddělené mezerami ve formátu 'key[=value]'. |
| --je-veřejná | Boolean.  Pokud je true, přiřaďte aplikaci veřejnou doménu. |
| --jvm-možnosti | Řetězec obsahující možnosti JVM.  Místo chyb při analýzách prostředí použijte '=' místo ' '. `--jvm-options='-Xms1024m -Xmx2048m`Např. |
| --ne-čekat | Nečekejte na dokončení dlouhotrvajících operací. |
| --runtime-version --runtime-version --runtime-version --run | Runtime verze jazyka používaného v aplikaci.  Povolené `Java_11`hodnoty: `Java_8`, . |

### <a name="example"></a>Příklad

Přidejte proměnnou prostředí pro aplikaci.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az jarní-cloud seznam vazeb aplikací

Seznam všech vazeb služby v aplikaci.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-app-binding-remove"></a>az jarní cloud aplikace vazba odebrat

Odeberte vazby služby z aplikace.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --jméno | Název vazby služby, která má být odebrána. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-app-binding-show"></a>az jarní cloud aplikace vazby show

Zobrazit podrobnosti o vazbě služby.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --jméno | Název vazby služby. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az spring-cloud aplikace vazby cosmos přidat

Spojte databázi Azure Cosmos s aplikací.

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
| --api-typ | Zadejte typ rozhraní API pomocí jedné z následujících hodnot: cassandra, gremlin, mongo, sql, tabulka. |
| --aplikace | Obsahuje název aplikace. |
| --jméno | Název vazby služby. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

|Volitelné parametry | |
| --- | ---: |
| --název kolekce | Název kolekce.  Povinné při použití Gremlin. |
| --název databáze | Název databáze.  Povinné při použití Mongo, SQL a Gremlin. |
| --klíč-prostor | Cassandra klíčové místo.  Povinné při použití Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>aktualizace aplikace az spring-cloud cosmos

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
| --jméno | Název vazby služby. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

|Volitelné parametry | |
| --- | ---: |
| --název kolekce | Název kolekce.  Povinné při použití Gremlin. |
| --název databáze | Název databáze.  Povinné při použití Mongo, SQL a Gremlin. |
| --klíč-prostor | Cassandra klíčové místo.  Povinné při použití Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az jaro-cloud aplikace vazba mysql přidat

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
| --název databáze | Název databáze. |
| --klíč | Klíč rozhraní API služby. |
| --jméno | Název vazby služby. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --id zdroje | ID prostředku Azure služby svázat. |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |
| --username | Uživatelské jméno pro přístup k databázi. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az jarní cloud app vazba mysql update

Aktualizujte připojení vazby služby aplikace do databáze Azure pro MySQL.

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
| --jméno | Název vazby služby. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --název databáze | Název databáze. |
| --klíč | Klíč rozhraní API služby. |
| --username | Uživatelské jméno pro přístup k databázi. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az spring-cloud app vazba redis přidat

Spojte azure mezipaměť pro Redis s aplikací.

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
| --jméno | Název vazby služby. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --id zdroje | ID prostředku Azure služby, se kterou chcete vytvořit vazbu. |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --disable-ssl | Zakažte TLS. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>aktualizace redis vazby aplikace az spring-cloud

Aktualizujte vazby služby pro Azure Cache pro Redis.

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --jméno | Název vazby služby. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --disable-ssl | Zakažte TLS. |

## <a name="az-spring-cloud-app-deployment-create"></a>az jarní cloud ové nasazení aplikace vytvořit

Vytvořte pracovní nasazení pro aplikaci.

Chcete-li nasadit kód nebo aktualizovat `az spring-cloud app deploy --deployment <staging-deployment>` nastavení na existující nasazení, použijte <staging deployment>nebo 'az jarní a cloudapp update --deployment .

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --jméno | Název vazby služby. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --cpu | Počet virtuálních procesorových jader na instanci.  Výchozí: 1 |
| --env | Proměnné prostředí oddělené mezerami ve formátu 'key[=value]'. |
| --počet instancí | Počet instancí. Výchozí nastavení: 1. |
| --jar-cesta | Pokud je k dispozici, nasaďte jar.  V opačném případě nasaďte aktuální složku jako dehet. |
| --jvm-možnosti | Řetězec obsahující možnosti JVM.  Místo chyb při analýzách prostředí použijte '=' místo ' '. `--jvm-options='-Xms1024m -Xmx2048m`Např. |
| --paměť | Počet GB paměti na instanci. |
| --ne-čekat | Nečekejte na dokončení dlouhotrvajících operací. |
| --runtime-version --runtime-version --runtime-version --run | Runtime verze jazyka používaného v aplikaci.  Povolené `Java_11`hodnoty: `Java_8`, . |
| --skip-clone-settings --skip-clone-settings --skip-clone-settings --skip | Vytvořte testovací nasazení klonováním aktuálního nastavení produkčního nasazení. |
| --cílový modul | Podřízený modul, který má být nasazen.  Povinné při více jar balíčky jsou vytvořeny ze zdrojového kódu. |
| --verze | Verze nasazení.  Beze změny, pokud není nastavena. |

### <a name="examples"></a>Příklady

Nasaďte zdrojový kód do nového nasazení aplikace.  To bude zabalit aktuální adresář, sestavení binární pomocí pivotal build system a potom nasadit.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Nasaďte předem sestavenou nádobu do aplikace s možnostmi JVM a proměnnými prostředí.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>odstranění nasazení aplikace az spring-cloud

Odstraňte nasazení aplikace.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --jméno | Název nasazení. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-app-deployment-list"></a>seznam nasazení aplikací az spring-cloud

Seznam všech nasazení v aplikaci.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-app-deployment-show"></a>az jarní cloudové nasazení aplikace show

Zobrazit podrobnosti o nasazení.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Povinné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --jméno | Název nasazení. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --služba -s | Název Azure Spring Cloud.  Výchozí službu můžete `az configure --defaults spring-cloud=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-config-server-clear"></a>az jaro-cloud config-server jasné

Vymažte všechna nastavení konfigurace na konfiguračním serveru.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --jméno | Název Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-config-server-set"></a>az jarní cloud config-server set

Nastavte nastavení konfigurace na konfiguračním serveru pomocí souboru YAML.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Povinné parametry | |
| --- | ---: |
| --config-file | Cesta k cestě k manifestu YAML pro konfiguraci konfiguračního serveru. |
| --jméno | Název Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --ne-čekat | Nepoužívejte pro dlouhotrvající operace k dokončení.

## <a name="az-spring-cloud-config-server-show"></a>az jaro-cloud config-server show

Zobrazí nastavení serveru Config.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --jméno | Název Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-config-server-git-set"></a>az spring-cloud config-server git set

Nastavte vlastnosti git pro konfigurační server.  Tím se přepíší všechny existující vlastnosti git.

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
| --jméno | Název Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --uri | Identifikátor URI přidané konfigurace. |

| Volitelné parametry | |
| --- | ---: |
| --odložit | Dočasně uložte objekt v místní mezipaměti namísto odesílání do Azure.  Slouží `az cache` k zobrazení / vymazání. |
| --hostitelský klíč | Klíč hostitele pro přidanou konfiguraci. |
| --algoritmus hostitelského klíče | Algoritmus klíče hostitele pro přidanou konfiguraci. |
| --popisek | Popisek přidané konfigurace. |
| --password | Heslo přidané konfigurace. |
| --soukromý klíč | Soukromý klíč přidané konfigurace. |
| --vyhledávací cesty | Hledání cest přidané konfigurace.  Pro více cest použijte oddělovače čárek. |
| --strict-host-key-checking --strict-host-key-checking --strict-host-key-checking --strict | Umožňuje přísnou kontrolu klíče hostitele přidané konfigurace. |
| --username | Uživatelské jméno přidané konfigurace. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az jaro-cloud config-server git repo přidat

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
| --jméno | Název Azure Spring Cloud. |
| --repo-jméno | Identifikátor URI repo. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --uri | Identifikátor URI přidané konfigurace. |

| Volitelné parametry | |
| --- | ---: |
| --odložit | Dočasně uložte objekt v místní mezipaměti namísto odesílání do Azure.  Slouží `az cache` k zobrazení / vymazání. |
| --hostitelský klíč | Klíč hostitele pro přidanou konfiguraci. |
| --algoritmus hostitelského klíče | Algoritmus klíče hostitele pro přidanou konfiguraci. |
| --popisek | Popisek přidané konfigurace. |
| --password | Heslo přidané konfigurace. |
| --vzor | Vzor pro repo.  Pro více cest použijte oddělovače čárek.|
| --soukromý klíč | Soukromý klíč přidané konfigurace. |
| --vyhledávací cesty | Hledání cest přidané konfigurace.  Pro více cest použijte oddělovače čárek. |
| --strict-host-key-checking --strict-host-key-checking --strict-host-key-checking --strict | Umožňuje přísnou kontrolu klíče hostitele přidané konfigurace. |
| --username | Uživatelské jméno přidané konfigurace. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az spring-cloud config-server git úložiště

Vypsat všechna úložiště git definovaná na konfiguračním serveru

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Povinné parametry | |
| --- | ---: |
| --jméno | Název Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --odložit | Dočasně uložte objekt v místní mezipaměti namísto odesílání do Azure.  Slouží `az cache` k zobrazení / vymazání. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az jaro-cloud config-server git repo odebrat

Odeberte existující konfiguraci úložiště git z konfiguračního serveru.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Povinné parametry | |
| --- | ---: |
| --jméno | Název Azure Spring Cloud. |
| --repo-jméno | Identifikátor URI repo. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --odložit | Dočasně uložte objekt v místní mezipaměti namísto odesílání do Azure.  Slouží `az cache` k zobrazení / vymazání. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az jaro-cloud test-koncový bod zakázat

Zakázání koncového bodu testu azure jarního cloudu

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --jméno | Název Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az spring-cloud test-koncový bod povolit

Povolte koncový bod testu pro Azure Spring Cloud. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Povinné parametry | |
| --- | ---: |
| --jméno | Název Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |

## <a name="az-spring-cloud-test-endpoint-list"></a>az jaro-cloud test-koncový bod seznamu 

Seznam dostupných klíčů koncového bodu testu pro Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Povinné parametry | |
| --- | ---: |
| --jméno | Název Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |

| Volitelné parametry | |
| --- | ---: |
| --aplikace | Obsahuje název aplikace. |
| --nasazení -d | Název existujícího nasazení aplikace.  Výchozí je výroba, pokud není zadána. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az spring-cloud test-koncový bod obnovit klíč

Znovu vygenerujte klíč koncového bodu testu pro Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Povinné parametry | |
| --- | ---: |
| --jméno | Název Azure Spring Cloud. |
| --resource-group -g | Název skupiny prostředků.  Výchozí skupinu můžete `az configure --defaults group=<name>`nakonfigurovat pomocí aplikace . |
| --typ | Typ klíče koncového bodu testu.  Povolené hodnoty: Primární, Sekundární. |
