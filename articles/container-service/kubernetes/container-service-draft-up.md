---
title: ZASTARALÉ Použití konceptu s Azure Container Service a Azure Container Registry
description: Vytvořte cluster ACS Kubernetes a službu Azure Container Registry, abyste mohli vytvořit první aplikaci v Azure pomocí nástroje Draft.
author: squillace
ms.service: container-service
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 039879cb725c20af3c40698947ea9abe189d08ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82731759"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>ZASTARALÉ Použití konceptu s Azure Container Service a Azure Container Registry k sestavení a nasazení aplikace do Kubernetes

> [!TIP]
> Aktualizovanou verzi tohoto článku, který používá službu Azure Kubernetes, najdete v tématu [použití konceptu ve službě Azure Kubernetes Service (AKS)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft) je nový opensourcový nástroj usnadňující vývoj kontejnerových aplikací a jejich nasazení do clusterů Kubernetes, který nevyžaduje téměř žádné znalosti Dockeru ani Kubernetes – dokonce je nemusíte ani instalovat. Používání nástrojů, jako je Draft, umožňuje vám a vašim týmům zaměřit se na vytváření aplikací pomocí Kubernetes, aniž byste se museli tolik zabývat infrastrukturou.

Draft můžete používat s libovolným registrem imagí Dockeru a jakýmkoli clusterem Kubernetes, a to i místně. V tomto kurzu se dozvíte, jak pomocí služby ACS s Kubernetes a ACR vytvořit živý, ale zabezpečený kanál pro vývojáře v Kubernetes s využitím konceptu, a jak používat Azure DNS k vystavení tohoto kanálu pro vývojáře, aby mohli ostatní zobrazit v doméně.


## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry
Můžete snadno [vytvořit novou službu Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md), ale postup je následující:

1. Vytvořte skupinu prostředků Azure pro správu registru ACR a clusteru Kubernetes ve službě ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Pomocí příkazu [AZ ACR Create](/cli/azure/acr#az-acr-create) vytvořte registr image ACR a ujistěte se, že `--admin-enabled` je tato možnost nastavená na `true` .
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Vytvoření služby Azure Container Service pomocí Kubernetes

Nyní jste připraveni použít příkaz [az acs create](/cli/azure/acs#az-acs-create) k vytvoření clusteru ACS s použitím Kubernetes jako hodnoty `--orchestrator-type`.
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

Když teď máte cluster, můžete importovat přihlašovací údaje pomocí příkazu [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). Nyní máte místní konfigurační soubor pro váš cluster, který pro svou práci potřebuje Helm a Draft.

## <a name="install-and-configure-draft"></a>Instalace a konfigurace nástroje Draft


1. Stáhněte si koncept pro vaše prostředí https://github.com/Azure/draft/releases a nainstalujte ho do své cesty, aby se příkaz mohl použít.
2. Stáhněte si Helm pro vaše prostředí https://github.com/kubernetes/helm/releases a [nainstalujte ho do vaší cesty, aby se příkaz mohl použít](https://helm.sh/docs/intro/quickstart#install-helm).
3. Nakonfigurujte Draft pro použití vašeho registru a vytvoření subdomény pro každý diagram Helmu, který vytvoří. Ke konfiguraci nástroje Draft potřebujete:
   - Název služby Azure Container Registry (v tomto příkladu `draftacsdemo`).
   - Klíč registru nebo heslo získané příkazem `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

   Volání `draft init` a proces konfigurace vás vyzve k zadání výše uvedených hodnot. Všimněte si, že formát adresy URL registru je název registru (v tomto příkladu `draftacsdemo` ) plus `.azurecr.io` . Vaše uživatelské jméno má vlastní název registru. Proces při prvním spuštění vypadá asi takto:
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

V úložišti Draft najdete [šest jednoduchých ukázkových aplikací](https://github.com/Azure/draft/tree/master/examples). Naklonujte úložiště a pojďme použít [příklad Java](https://github.com/Azure/draft/tree/master/examples/example-java). Přejděte do části Příklady/Java a zadejte a `draft create` sestavte aplikaci. Mělo by to vypadat asi jako v následujícím příkladu.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

Výstup zahrnujte soubor Dockerfile a digram Helmu. K sestavení a nasazení stačí zadat `draft up`. Výstup je rozsáhlý, ale měl by být podobný jako v následujícím příkladu.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Bezpečné zobrazení vaší aplikace

Váš kontejner je teď spuštěný ve službě ACS. Pokud ho chcete zobrazit, použijte `draft connect` příkaz, který vytvoří zabezpečené připojení k IP adrese clusteru s konkrétním portem pro vaši aplikaci, abyste ho mohli zobrazit lokálně. V případě úspěchu vyhledejte adresu URL pro připojení k vaší aplikaci na prvním řádku po indikátoru **úspěšnosti** .

> [!NOTE]
> Pokud se zobrazí zpráva oznamující, že nebyly připraveny žádné lusky, chvíli počkejte a pak to zkuste znovu `kubectl get pods -w` .

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

V předchozím příkladu můžete zadat `curl -s http://localhost:46143` příjem odpovědi, `Hello World, I'm Java!` . Po stisknutí kombinace kláves CTRL + nebo CMD + C (v závislosti na prostředí operačního systému) se zabezpečené tunelové připojení odpojí a můžete pokračovat v iteraci.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Sdílení aplikace konfigurací domény nasazení pomocí Azure DNS

V předchozích krocích jste už provedli smyčku pro vývojáře, kterou koncept vytvoří. Aplikaci však můžete sdílet přes Internet pomocí:
1. Instalace příchozího přenosu do clusteru ACS (za účelem poskytnutí veřejné IP adresy, na které se má aplikace zobrazit)
2. Delegování vlastní domény pro Azure DNS a mapování domény na IP adresu služba ACS přiřadí vašemu kontroleru příchozího přenosu dat.

### <a name="use-helm-to-install-the-ingress-controller"></a>Pomocí Helm nainstalujte kontroler příchozího přenosu dat.
Pomocí **Helm** můžete vyhledat a nainstalovat `stable/traefik` , adaptér příchozího přenosu dat, a povolit tak příchozí požadavky na vaše sestavení.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Nyní nastavte na kontroleru `ingress` sledování, aby při nasazení zachytil hodnotu externí IP adresy. Tato IP adresa bude v další části namapovaná na vaši doménu nasazení.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

V tomto případě je externí IP adresa pro doménu nasazení `13.64.108.240`. Nyní můžete namapovat doménu na tuto IP adresu.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mapování příchozí IP adresy na vlastní subdoménu

Draft vytvoří vydanou verzi pro každý diagram Helmu, který vytvoří – pro každou aplikaci, na které pracujete. Každé z nich získá vygenerovaný název, který se používá v **konceptu** jako _subdoména_ nad _doménou_ kořenového nasazení, kterou ovládáte. (V tomto příkladu používáme `squillace.io` jako doménu nasazení.) Pokud chcete povolit toto chování subdomény, musíte `'*.draft'` v položkách DNS pro vaši doménu nasazení vytvořit záznam A, aby se jednotlivé generované subdomény směrovaly do kontroleru příchozího přenosu clusteru Kubernetes. 

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
   Pomocí příkazu [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) získejte názvové servery pro delegování řízení DNS do Azure DNS pro vaši doménu.
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
3. Přidejte získané servery DNS k poskytovateli vaší domény nasazení – to vám umožní použít Azure DNS ke směrování vaší domény, jak budete chtít. Postup se liší podle toho, co doména nabízí; [delegování názvové servery domény na Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) obsahuje některé z podrobných údajů, které byste měli znát. 
4. Po delegování vaší domény na Azure DNS vytvořte položku sady záznamů pro mapování domény nasazení na `ingress` IP adresu z kroku 2 předchozí části.
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
5. Přeinstalovat **koncept**

   1. Odstraňte **koncept** z clusteru zadáním příkazu `helm delete --purge draft` . 
   2. Přeinstalujte **koncept** pomocí stejného `draft-init` příkazu, ale s `--ingress-enabled` možností:
      ```bash
      draft init --ingress-enabled
      ```
      Odpovězte na výzvy, které jste provedli poprvé, výše. Existuje však ještě jedna otázka, na kterou můžete reagovat, pomocí úplné cesty k doméně, kterou jste nakonfigurovali s Azure DNS.

6. Zadejte doménu nejvyšší úrovně pro příchozí přenosy (např. draft.example.com): draft.squillace.io
7. Když zavoláte `draft up` tuto dobu, budete moci zobrazit vaši aplikaci (nebo `curl` ji) na adrese URL formuláře `<appname>.draft.<domain>.<top-level-domain>` . V případě tohoto příkladu, `http://handy-labradoodle.draft.squillace.io` . 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Další kroky

Když teď máte cluster ACS Kubernetes, můžete prozkoumat používání služby [Azure Container Registry](../../container-registry/container-registry-intro.md), abyste mohli vytvářet další a jiná nasazení tohoto scénáře. Můžete například vytvořit sadu záznamů DNS domény draft._základní_doména.doména_nejvyšší_úrovně_, která bude pro specifická nasazení ACS řídit vše pro subdoménu na nižší úrovni.






