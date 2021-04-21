---
title: Spustit inicializační kontejnery
description: Pomocí spouštěcích kontejnerů v Azure Container Instances provádět úlohy instalace ve skupině kontejnerů před spuštěním kontejnerů aplikací.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 9ccaf1a67d6ca3bcff422acb591b528cc72a9608
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763932"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Spuštění kontejneru init pro úlohy nastavení ve skupině kontejnerů

Azure Container Instances podporuje *inicializační kontejnery* ve skupině kontejnerů. Spuštění kontejnerů inicializace před zahájením kontejneru nebo kontejnerů aplikace se dokončilo. Podobně jako [kontejnery Kubernetes init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)použijte jeden nebo více kontejnerů init k provedení logiky inicializace pro kontejnery aplikace, jako je například nastavení účtů, spuštění instalačních skriptů nebo konfigurace databází.

Tento článek ukazuje, jak použít šablonu Azure Resource Manager ke konfiguraci skupiny kontejnerů pomocí inicializačního kontejneru.

## <a name="things-to-know"></a>Co je potřeba vědět

* **Verze rozhraní API** – k nasazení kontejnerů inicializace potřebujete aspoň Azure Container Instances rozhraní api verze 2019-12-01. Nasaďte pomocí `initContainers` vlastnosti v [souboru YAML](container-instances-multi-container-yaml.md) nebo [šabloně správce prostředků](container-instances-multi-container-group.md).
* **Pořadí spuštění** – kontejnery inicializace jsou spouštěny v pořadí zadaném v šabloně a před jinými kontejnery. Ve výchozím nastavení můžete zadat maximálně 59 kontejnerů init na skupinu kontejnerů. Nejméně jeden kontejner, který není inicializací, musí být ve skupině.
* **Prostředí hostitele** – kontejnery inicializace se spouštějí na stejném hardwaru jako zbytek kontejnerů ve skupině.
* **Prostředky** – neurčíte prostředky pro inicializační kontejnery. Jsou jim přidělené celkové prostředky, jako jsou CPU a paměť, které jsou k dispozici pro skupinu kontejnerů. Při spuštění kontejneru init se ve skupině nespouštějí žádné další kontejnery.
* **Podporované vlastnosti** – kontejnery init můžou používat vlastnosti skupiny, jako jsou svazky, tajné klíče a spravované identity. Nemůžou ale používat porty nebo IP adresu, pokud je nakonfigurovaná pro skupinu kontejnerů. 
* **Zásady restartování** – každý kontejner init musí být úspěšně ukončen před spuštěním dalšího kontejneru ve skupině. Pokud se kontejner init neukončí úspěšně, akce restartování závisí na [zásadách restartování](container-instances-restart-policy.md) nakonfigurovaných pro tuto skupinu:

    |Zásada ve skupině  |Zásada v inicializaci  |
    |---------|---------|
    |Vždy     |OnFailure (Při selhání)         |
    |OnFailure (Při selhání)     |OnFailure (Při selhání)         |
    |Nikdy     |Nikdy         |
* **Poplatky** – skupina kontejnerů se účtuje za první nasazení kontejneru init.

## <a name="resource-manager-template-example"></a>Příklad šablony Správce prostředků

Začněte tím, že zkopírujete následující JSON do nového souboru s názvem `azuredeploy.json` . Šablona nastaví skupinu kontejnerů pomocí jednoho inicializačního kontejneru a dvou kontejnerů aplikace:

* Kontejner *init1* spouští image [BUSYBOX](https://hub.docker.com/_/busybox) z Docker Hub. V režimu spánku po dobu 60 sekund a pak zapíše řetězec příkazového řádku do souboru ve [svazku emptyDir](container-instances-volume-emptydir.md).
* V kontejnerech aplikací je spuštěná `aci-wordcount` Image kontejneru Microsoftu:
    * Kontejner *Hamlet* spustí aplikaci WORDCOUNT ve své výchozí konfiguraci a spočítá wordové kmitočty v Shakespeare *Hamlet*.
    * Kontejner aplikace *Juliet* čte řetězec příkazového řádku ze svazku emptDir, aby se spouštěla aplikace WORDCOUNT místo na *Romeo a julietu* Shakespeare.

Další informace a příklady použití `aci-wordcount` obrázku najdete v tématu [nastavení proměnných prostředí v instancích kontejnerů](container-instances-environment-variables.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>Nasazení šablony

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Nasaďte šablonu pomocí příkazu [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

Ve skupině s kontejnerem init se čas nasazení zvyšuje, protože doba potřebná k dokončení kontejneru inicializace nebo kontejnerů.


## <a name="view-container-logs"></a>Zobrazení protokolů kontejneru

Chcete-li ověřit úspěšné spuštění kontejneru init, zobrazte výstup protokolu kontejnerů aplikací pomocí příkazu [AZ Container logs][az-container-logs] . `--container-name`Argument určuje kontejner, ze kterého mají být vyžádané protokoly. V tomto příkladu si načetli protokoly pro kontejnery *Hamlet* a *Juliet* , které zobrazují jiný výstup příkazu:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Výstup:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Výstup:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Další kroky

Kontejnery init vám pomůžou provádět úlohy nastavení a inicializace pro kontejnery aplikací. Další informace o spuštění kontejnerů založených na úlohách najdete v tématu [spuštění kontejnerových úloh pomocí zásad restartování](container-instances-restart-policy.md).

Azure Container Instances poskytuje další možnosti pro úpravu chování kontejnerů aplikací. Mezi příklady patří:

* [Nastavení proměnných prostředí v instancích kontejnerů](container-instances-environment-variables.md)
* [Nastavení příkazového řádku v instanci kontejneru pro přepsání výchozí operace příkazového řádku](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-container-logs]: /cli/azure/container#az_container_logs
