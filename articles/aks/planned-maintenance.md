---
title: Použití plánované údržby pro cluster Azure Kubernetes Service (AKS) (Preview)
titleSuffix: Azure Kubernetes Service
description: Naučte se používat plánovanou údržbu ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: f1e0822e77d8466b1b9796041fbdba53c3f9c91f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782904"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Pomocí plánované údržby můžete naplánovat časová období údržby pro cluster Azure Kubernetes Service (AKS) (Preview).

V clusteru AKS se automaticky provedla pravidelná údržba. Ve výchozím nastavení tato práce může nastat kdykoli. Plánovaná údržba umožňuje naplánovat týdenní časová období údržby, která aktualizují vaši řídicí plochu, a také kubeá prostředí v instanci VMSS a minimalizuje dopad úloh. Po naplánování proběhne veškerá údržba během vybraného okna. V clusteru můžete naplánovat jedno nebo více týdenních oken tím, že v určitém dni zadáte den nebo časový rozsah. Okna údržby se konfigurují pomocí Azure CLI.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Omezení

Při použití plánované údržby platí následující omezení:

- AKS si vyhrazuje právo Tato okna přerušit pro neplánované/reaktivní operace údržby, které jsou naléhavé nebo kritické.
- V současné době se operace údržby považují za *nejlepší* a v rámci zadaného okna se nezaručují.
- Aktualizace nelze blokovat po dobu delší než 7 dní.

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření rozhraní příkazového řádku aks-preview

Budete také potřebovat rozšíření Azure CLI AKS ve verzi *Preview* 0.5.4 nebo novější. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] . Nebo nainstalujte jakékoli dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Povolení údržby každé pondělí v 1:10:00 až 2:10:00

Chcete-li přidat časový interval pro správu a údržbu, můžete použít `az aks maintenanceconfiguration add` příkaz.

> [!IMPORTANT]
> Plánovaná časová období údržby se zadává v koordinovaném univerzálním čase (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

Následující příklad výstupu ukazuje časový interval pro správu a údržbu od 1:10:00 do 2:10:00 každé pondělí.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Pokud chcete v průběhu dne kdykoli začít s údržbou, vynechejte parametr *počáteční-hodina* . Například následující příkaz nastaví časové období údržby pro celý den každé pondělí:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Přidat konfiguraci údržby se souborem JSON

Místo použití parametrů můžete použít také soubor JSON pro vytvoření okna údržby. Vytvořte `test.json` soubor s následujícím obsahem:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

Výše uvedený soubor JSON určuje časové intervaly pro správu a údržbu každé úterý v intervalu 1:10:00-3:10:00 a v každé středu v intervalu 1:10:00-2:10:00 a v 6:10:00-7:10:00. Existuje také výjimka z *2021-05-26T03:00:00Z* do *2021-05-30T12:00:00Z* , kde údržba není povolena, i když se překrývá s časovým obdobím údržby. Následující příkaz přidá okna údržby z `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Aktualizuje existující časové období údržby.

Chcete-li aktualizovat existující konfiguraci údržby, použijte `az aks maintenanceconfiguration update` příkaz.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Zobrazit všechna okna údržby v existujícím clusteru

Pokud chcete zobrazit všechna aktuální okna konfigurace údržby ve vašem clusteru AKS, použijte `az aks maintenanceconfiguration list` příkaz.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

Ve výstupu níže vidíte, že existují dvě časová období údržby konfigurovaná pro myAKSCluster. Jedno okno je v pondělí v rozmezí 1:10:00 a jiné okno je v pátek v intervalu 4:10:00.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Zobrazení konkrétního okna konfigurace údržby v clusteru AKS

Pokud chcete zobrazit konkrétní okno Konfigurace údržby v clusteru AKS, použijte `az aks maintenanceconfiguration show` příkaz.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

Následující příklad výstupu ukazuje okno údržby pro *výchozí nastavení*:

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Odstraní určité okno Konfigurace údržby v existujícím clusteru AKS.

K odstranění určitého okna konfigurace údržby v clusteru AKS použijte `az aks maintenanceconfiguration delete` příkaz.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít s upgradem clusteru AKS, přečtěte si téma [upgrade clusteru AKS][aks-upgrade] .


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
