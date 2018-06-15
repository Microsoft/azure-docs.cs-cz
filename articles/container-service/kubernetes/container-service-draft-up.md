---
title: Použít koncept s Azure Container Service a Azure registru kontejneru
description: Vytvořte cluster ACS Kubernetes a službu Azure Container Registry, abyste mohli vytvořit první aplikaci v Azure pomocí nástroje Draft.
services: container-service
author: squillace
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 68ad44bae0856ff000f2847049a15a946d83c0a3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32168533"
---
# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Použití nástroje Draft se službami Azure Container Service a Azure Container Registry k sestavení a nasazení aplikace do Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Draft](https://aka.ms/draft) je nový opensourcový nástroj usnadňující vývoj kontejnerových aplikací a jejich nasazení do clusterů Kubernetes, který nevyžaduje téměř žádné znalosti Dockeru ani Kubernetes – dokonce je nemusíte ani instalovat. Používání nástrojů, jako je Draft, umožňuje vám a vašim týmům zaměřit se na vytváření aplikací pomocí Kubernetes, aniž byste se museli tolik zabývat infrastrukturou.

Draft můžete používat s libovolným registrem imagí Dockeru a jakýmkoli clusterem Kubernetes, a to i místně. Tento kurz ukazuje, jak používat služby ACS s Kubernetes a ACR k vytvoření kanálu za provozu, ale zabezpečené vývojáře v Kubernetes pomocí koncept a jak používat Azure DNS vystavit tohoto kanálu vývojáře pro ostatní uživatele v doméně.


## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry
Můžete snadno [vytvořit novou službu Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md), ale postup je následující:

1. Vytvořte skupinu prostředků Azure pro správu registru Azure Container Registry a clusteru Kubernetes ve službě ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Vytvoření pomocí registru ACR bitové kopie [vytvořit az acr](/cli/azure/acr#az_acr_create) a ujistěte se, že `--admin-enabled` je možnost nastavena na `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Vytvoření služby Azure Container Service pomocí Kubernetes

Nyní jste připraveni použít příkaz [az acs create](/cli/azure/acs#az_acs_create) k vytvoření clusteru ACS s použitím Kubernetes jako hodnoty `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Vzhledem k tomu, že Kubernetes není výchozím typem orchestrátoru, nezapomeňte použít přepínač `--orchestrator-type kubernetes`.

Výstup po úspěšném provedení je podobný následujícímu.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Když teď máte cluster, můžete importovat přihlašovací údaje pomocí příkazu [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Nyní máte místní konfigurační soubor pro váš cluster, který pro svou práci potřebuje Helm a Draft.

## <a name="install-and-configure-draft"></a>Instalace a konfigurace nástroje Draft


1. Stáhnout koncept pro vaše prostředí na https://github.com/Azure/draft/releases a instalaci do své CESTĚ, můžete použít příkaz.
2. Stáhněte si helm pro vaše prostředí na https://github.com/kubernetes/helm/releases a [nainstalovat do své CESTĚ, aby bylo možné použít příkaz](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Nakonfigurujte Draft pro použití vašeho registru a vytvoření subdomény pro každý diagram Helmu, který vytvoří. Ke konfiguraci nástroje Draft potřebujete:
  - Název služby Azure Container Registry (v tomto příkladu `draftacsdemo`).
  - Klíč registru nebo heslo získané příkazem `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

  Volání `draft init` a proces konfigurace zobrazí výzvu pro výše uvedené hodnoty, Všimněte si, že adresa URL formátu adresy URL registru v registru (v tomto příkladu `draftacsdemo`) plus `.azurecr.io`. Vaše uživatelské jméno je název registru svoje vlastní. Proces při prvním spuštění vypadá asi takto:
 ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
```

Nyní jste připraveni nasadit aplikaci.


## <a name="build-and-deploy-an-application"></a>Sestavení a nasazení aplikace

V úložišti Draft najdete [šest jednoduchých ukázkových aplikací](https://github.com/Azure/draft/tree/master/examples). Naklonujte úložiště a umožňuje použít [příkladu Java](https://github.com/Azure/draft/tree/master/examples/java). Přejděte do adresáře příklady nebo java a typ `draft create` pro sestavení aplikace. Mělo by to vypadat asi jako v následujícím příkladu.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

Výstup zahrnujte soubor Dockerfile a digram Helmu. K sestavení a nasazení stačí zadat `draft up`. Výstup je rozsáhlé, ale musí být jako v následujícím příkladu.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Bezpečné prohlížení vaší aplikace

Vaše kontejneru je nyní spuštěna v rámci služby ACS. Chcete-li ji zobrazit, použijte `draft connect` příkaz, který vytvoří zabezpečené připojení k IP clusteru s konkrétní port pro vaši aplikaci, aby si můžete zobrazit místně. Pokud bylo úspěšné, vyhledejte adresu URL pro připojení k vaší aplikace na první řádek po **úspěch** indikátoru.

> [!NOTE]
> Pokud se zobrazí zpráva, že žádné pracovními stanicemi soustředěnými kolem byla připravena, počkejte chvíli a zkuste to znovu, nebo můžete sledovat pracovními stanicemi soustředěnými kolem bude připravena s `kubectl get pods -w` a poté opakujte když ho podporují.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

V předchozím příkladu, můžete zadat `curl -s http://localhost:46143` přijímat odpovědi, `Hello World, I'm Java!`. Když vás CTRL + nebo CMD + C (v závislosti na prostředí operačního systému), zabezpečené tunelové propojení je odpojí a můžete pokračovat v iterace.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Sdílení aplikace podle konfigurace domény nasazení s Azure DNS

Již jste provedli iteraci smyčky vývojáře, která koncept vytvoří v předchozích krocích. Však můžete sdílet aplikaci přes internet pomocí:
1. Instalace příjem příchozích dat v clusteru služby ACS (k poskytnutí veřejnou IP adresu, na které se mají zobrazit aplikace)
2. Delegování vlastní domény do Azure DNS a mapování vaší domény na IP adresu služby ACS přiřadí k řadiči příjem příchozích dat

### <a name="use-helm-to-install-the-ingress-controller"></a>K instalaci řadiče příjem příchozích dat použijte helm.
Použití **helm** k vyhledání a instalace `stable/traefik`, řadič příjem příchozích dat můžete povolit příchozí žádosti pro vaše buildy.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Nyní nastavte na kontroleru `ingress` sledování, aby při nasazení zachytil hodnotu externí IP adresy. Tato IP adresa bude v další části [namapovaná na vaši doménu nasazení](#wire-up-deployment-domain).

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

V tomto případě je externí IP adresa pro doménu nasazení `13.64.108.240`. Nyní můžete namapovat doménu na tuto IP adresu.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mapování IP příjem příchozích dat na vlastní subdoména

Draft vytvoří vydanou verzi pro každý diagram Helmu, který vytvoří – pro každou aplikaci, na které pracujete. Každé z nich získá vygenerovaný název, který je používán **koncept** jako _subdomény_ nad kořenu _nasazení domény_ , kterou řídíte. (V tomto příkladu používáme jako doménu nasazení `squillace.io`.) K povolení tohoto chování subdomén musíte v záznamech DNS pro vaši doménu nasazení vytvořit záznam A pro `'*.draft'`, aby se každá vygenerovaná subdoména přesměrovala do kontroleru příchozího přenosu clusteru Kubernetes. 

Váš poskytovatel domény má vlastní způsob přiřazování serverů DNS. Pokud chcete [delegovat názvové servery vaší domény do Azure DNS](../../dns/dns-delegate-domain-azure-dns.md), postupujte následovně:

1. Vytvořte skupinu prostředků pro vaši zónu DNS.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Vytvořte zónu DNS pro vaši doménu.
Pomocí příkazu [az network dns zone create](/cli/azure/network/dns/zone#az_network_dns_zone_create) získejte názvové servery pro delegování řízení DNS do Azure DNS pro vaši doménu.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Přidejte získané servery DNS k poskytovateli vaší domény nasazení – to vám umožní použít Azure DNS ke směrování vaší domény, jak budete chtít. Způsob, jak to uděláte, se liší podle domény zadat; [delegovat nameservers vaší domény do Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) obsahuje některé podrobnosti, které byste měli vědět. 
4. Jakmile vaše doména byla přenesena do Azure DNS, vytvořit položku A sadu záznamů pro vaše nasazení domény mapování na `ingress` IP z kroku 2 v předchozí části.
  ```azurecli
  az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
  ```
Výstup by měl vypadat asi takto:
  ```json
  {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
  }
  ```
5. Přeinstalujte **konceptu**

   1. Odebrat **draftd** z clusteru zadáním `helm delete --purge draft`. 
   2. Přeinstalujte **koncept** pomocí stejné `draft-init` příkazu, ale `--ingress-enabled` možnost:
    ```bash
    draft init --ingress-enabled
    ```
   Reagujete na výzvy, jako jste to udělali při prvním výše. Máte ale jedna další otázka reagovat, pomocí cesty dokončení domény, který jste nakonfigurovali s Azure DNS.

6. Zadejte doménu nejvyšší úrovně pro příjem příchozích dat (např. draft.example.com): draft.squillace.io
7. Při volání `draft up` této doby bude moci zobrazit vaše aplikace (nebo `curl` ji) na adrese URL ve formátu `<appname>.draft.<domain>.<top-level-domain>`. V případě tohoto příkladu `http://handy-labradoodle.draft.squillace.io`. 
```bash
curl -s http://handy-labradoodle.draft.squillace.io
Hello World, I'm Java!
```


## <a name="next-steps"></a>Další postup

Když teď máte cluster ACS Kubernetes, můžete prozkoumat používání služby [Azure Container Registry](../../container-registry/container-registry-intro.md), abyste mohli vytvářet další a jiná nasazení tohoto scénáře. Můžete například vytvořit sadu záznamů DNS domény draft._základní_doména.doména_nejvyšší_úrovně_, která bude pro specifická nasazení ACS řídit vše pro subdoménu na nižší úrovni.






