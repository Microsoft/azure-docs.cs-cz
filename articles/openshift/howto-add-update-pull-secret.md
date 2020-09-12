---
title: Přidání nebo aktualizace tajného kódu pro vyžádání Red Hat v clusteru Azure Red Hat OpenShift 4
description: Přidání nebo aktualizace tajného kódu pro vyžádání Red Hat na stávajících 4 clusterech v ARO. x
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: tajný kód pro vyžádání obsahu, ARO, OpenShift, Red Hat
ms.openlocfilehash: 769b7589fb6496fc2f4123665ad1f6fe61d0cce2
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294743"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Přidání nebo aktualizace tajného kódu pro vyžádání Red Hat v clusteru Azure Red Hat OpenShift 4

Tento průvodce popisuje přidání nebo aktualizaci tajného kódu pro vyžádání Red Hat pro stávající cluster Azure Red Hat OpenShift (ARO) 4. x.

Pokud vytváříte cluster poprvé, můžete při vytváření clusteru přidat tajný klíč pro vyžádání obsahu. Další informace o tom, jak vytvořit cluster ARO s použitím tajného kódu pro vyžádání Red Hat, najdete v tématu [Vytvoření clusteru Azure Red Hat OpenShift 4](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## <a name="before-you-begin"></a>Než začnete

V tomto průvodci se předpokládá, že máte existující cluster Azure Red Hat OpenShift 4. Ujistěte se, že máte přístup správce ke clusteru.

## <a name="prepare-your-pull-secret"></a>Příprava tajného kódu pro vyžádání obsahu
Když vytvoříte cluster ARO bez přidání tajného klíče pro vyžádání obsahu Red Hat, na clusteru se automaticky vytvoří tajný kód pro vyžádání obsahu. Tento tajný kód pro vyžádání obsahu se ale plně neplní.

Tato část vás provede aktualizací tajného klíče pro vyžádání pomocí dalších hodnot z tajného kódu pro vyžádání obsahu Red Hat.

1. Načtěte tajný klíč s názvem `pull-secret` v `openshift-config` oboru názvů a uložte ho do samostatného souboru spuštěním následujícího příkazu: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    Výstup by měl vypadat přibližně takto: (Všimněte si, že se odebrala skutečná tajná hodnota.)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Navštivte [portál Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) a vyberte **Stáhnout tajný klíč pro vyžádání**. Váš tajný klíč pro stažení Red Hat bude vypadat následovně. (Všimněte si, že se odebraly skutečné tajné hodnoty.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Přihlaste se k souboru s tajným klíčem pro vyžádání obsahu, který jste získali z clusteru, přidáním do záznamů, které najdete v tajných klíčích pro vyžádání 

    > [!IMPORTANT]
    > Zahrnutím `cloud.openshift.com` údajů z libovolného tajného kódu Red Hat by váš cluster mohl začít odesílat data telemetrie do Red Hat. Tuto část zahrňte pouze v případě, že chcete odesílat data telemetrie. V opačném případě ponechte následující část.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Neodstraňujte ani neměňte `arosvc.azurecr.io` položku z vašeho tajného kódu pro vyžádání obsahu. Tato část je nutná pro správné fungování clusteru.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    Konečný soubor by měl vypadat nějak takto. (Všimněte si, že se odebraly skutečné tajné hodnoty.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Zajistěte, aby byl soubor platným kódem JSON. Existuje mnoho způsobů, jak ověřit JSON. Následující příklad používá JQ:

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Pokud se v souboru nachází chyba, zobrazí se jako `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>Přidání tajného kódu pro vyžádání do clusteru

Spusťte následující příkaz, který aktualizuje tajný klíč pro vyžádání obsahu.

> [!NOTE]
> Při spuštění tohoto příkazu dojde k tomu, že se uzly clusteru restartují o jednu po aktualizaci. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

Po nastavení tajného klíče budete připraveni povolit operátory s certifikací Red Hat.

### <a name="modify-the-configuration-files"></a>Úprava konfiguračních souborů

Pokud chcete povolit operátory Red Hat, upravte následující objekty.

Nejprve upravte konfigurační soubor operátor Samples. Pak můžete spuštěním následujícího příkazu upravit konfigurační soubor:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Změňte `spec.architectures.managementState` hodnoty a `status.architecture.managementState` z `Removed` na `Managed` . 

Následující fragment kódu YAML ukazuje pouze relevantní oddíly upravovaného souboru YAML:

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

Za druhé spusťte následující příkaz, který upraví konfigurační soubor centra operátora:  

```console
oc edit operatorhub cluster -o yaml
```

Změňte `Spec.Sources.Disabled` hodnoty a `Status.Sources.Disabled` z `true` na na `false` pro všechny zdroje, které chcete povolit.

Následující fragment kódu YAML ukazuje pouze relevantní oddíly upravovaného souboru YAML:

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Uložte soubor, aby se změny projevily.

## <a name="validate-that-your-secret-is-working"></a>Ověření funkčního tajného klíče

Po přidání tajného kódu pro vyžádání obsahu a úpravě správných konfiguračních souborů může trvat několik minut, než se cluster aktualizuje. Pokud chcete ověřit, jestli se váš cluster aktualizoval, spusťte následující příkaz, který zobrazí certifikované operátory a dostupné zdroje operátorů Red Hat:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Pokud nevidíte certifikované operátory a operátory Red Hat, počkejte pár minut a zkuste to znovu.

Aby se zajistilo, že se váš tajný kód pro vyžádání aktualizace aktualizoval a funguje správně, otevřete OperatorHub a vyhledejte všechny ověřené operátory Red Hat. Například zkontrolujte, zda je k dispozici operátor úložiště kontejneru OpenShift, a zkontrolujte, zda máte oprávnění k instalaci.

## <a name="next-steps"></a>Další kroky
Další informace o tajných klíčích pro vyžádání Red Hat najdete v tématu [použití tajných kódů pro vyžádání obrazu](https://docs.openshift.com/container-platform/4.5/openshift_images/managing_images/using-image-pull-secrets.html).

Další informace o Red Hat OpenShift 4 najdete v tématu [Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/welcome/index.html).
