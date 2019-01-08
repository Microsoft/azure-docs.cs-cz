---
title: Odstranit image prostředky ve službě Azure Container Registry
description: Podrobnosti o tom, jak efektivní správa velikosti registru tak, že odstraníte data image kontejneru.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.openlocfilehash: b18638057def03a02024200edb157e5caf08a669
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065167"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Odstranit Image kontejnerů ve službě Azure Container Registry

Pokud chcete zachovat velikost svého registru kontejneru Azure, odstraníte pravidelně zastaralé image data. Zatímco některé kontejnerové Image nasadili do produkčního prostředí můžou vyžadovat dlouhodobější úložiště, jiné obvykle se dá odstranit rychleji. Například automatické sestavení a testovací scénáře, svého registru můžete vyplnit s imagí, které nikdy je možno nasadit a může být odstraněna krátce po dokončení sestavení a testování průchodu.

Budete moct odstranit bitové kopie dat v několika různými způsoby, a proto je důležité pochopit, jak každá operace odstranění ovlivňuje využití úložiště. V tomto článku se nejdřív představuje komponenty imagí Dockeru registru a kontejner a potom zahrnuje několik metod pro odstranění dat obrázků.

## <a name="registry"></a>Registr

Kontejner *registru* je služba, která ukládá a distribuuje imagí kontejnerů. Docker Hub je veřejného registru kontejnerů Dockeru, zatímco služba Azure Container Registry poskytuje privátní Docker registry kontejnerů v Azure.

## <a name="repository"></a>Úložiště

Spravovat registry kontejnerů *úložišť*, kolekce imagí kontejnerů se stejným názvem, ale jiné značky. Například následující tři Image jsou v úložišti "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Názvy úložišť může také obsahovat [obory názvů](container-registry-best-practices.md#repository-namespaces). Obory názvů umožňují seskupit obrázků s využitím dopředné lomítko oddělené úložiště názvy, například:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>Součásti image

Image kontejneru do registru je přidružen jeden nebo více značek, obsahuje jeden nebo více vrstev a je identifikován manifestu. Pochopení, jak tyto komponenty vzájemně souvisí může pomoci určit nejlepší metody pro uvolnění místa v registru.

### <a name="tag"></a>Značka

Obrázku *značka* určuje jeho verzi. Jedinou bitovou kopii v úložišti můžete přiřadit jednu nebo více značek a může být "neoznačený i." To znamená můžete odstranit všechny značky pomocí bitové kopie dat na obrázku (vrstvy) i nadále registru.

Úložiště (nebo úložiště a obor názvů) plus definuje názvu obrázku značky. Vám může nabízená a vyžádaná instalace image tak, že zadáte jeho název v operaci push nebo pull.

V privátním registru, jako je Azure Container Registry názvu image také zahrnuje plně kvalifikovaný název registru hostitele. Hostitel registr pro Image do služby ACR je ve formátu *acrname.azurecr.io*. Například by být úplný název první obrázek v oboru názvů 'uvádění na trh' v předchozí části:

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

Informace o obrázku označování osvědčené postupy, najdete v článku [označování Dockeru: Osvědčené postupy pro označování a správy verzí Image dockeru] [ tagging-best-practices] blogový příspěvek na webu MSDN.

### <a name="layer"></a>Vrstva

Bitové kopie jsou tvořeny jednoho nebo víc *vrstvy*, každý odpovídající řádek v souboru Dockerfile, který definuje image. Imagí v registru sdílejí společné vrstev zvyšuje efektivitu úložiště. Například více bitových kopií v různých úložištích může sdílet stejné základní vrstvě Alpine Linuxu, ale jenom jednu kopii této vrstvy je uložen v registru.

Sdílení vrstvy také optimalizuje distribuci vrstvy do uzlů pomocí více bitových kopií sdílení běžné vrstvy. Například pokud obrázku již v uzlu obsahuje vrstvy Alpine Linux jako její základ, nepřenese následné operace přijetí změn různých bitové kopie odkazující na stejnou úroveň vrstvy k uzlu. Místo toho odkazuje na vrstvě již existuje na uzlu.

### <a name="manifest"></a>Manifest

Každá image kontejneru do registru kontejneru je přidružena *manifest*. Manifest, vygeneruje registru při obrázku, převede, jednoznačně identifikuje image a určuje jeho vrstvy. Manifesty pro úložiště pomocí příkazu rozhraní příkazového řádku Azure můžete vytvořit seznam [az acr úložiště show manifesty][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Například výpis manifest hodnoty Digest, úložiště, které "acr-helloworld":

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

Manifest popsaném se liší od manifest image můžete zobrazit na webu Azure Portal nebo pomocí [kontrolovat docker manifestu][docker-manifest-inspect]. V následující části "manifest digest" odkazuje na digest vygenerované pomocí operace push, ne *config.digest* v manifestu obrázků. Můžete o přijetí změn a odstranění imagí od **manifestu digest**, ne config.digest. Následující obrázek ukazuje dva druhy přehledu.

![Ověřování algoritmem digest manifestu a config.digest na webu Azure Portal][manifest-digest]

### <a name="manifest-digest"></a>Manifest ověřování algoritmem digest

Manifestů jsou označeny jedinečnou hodnotu hash SHA-256, nebo *manifestu digest*. Každá image – zda příznakem nebo ne--je identifikován jeho algoritmem digest. Hodnota ověřování algoritmem digest je jedinečný, i v případě, že data na úrovni image je stejné jako u jiného obrázku. Tento mechanismus je, co vám umožňuje opakovaně identicky tagged image nahrajete do registru. Například můžete opakovaně nabízet `myimage:latest` do registru bez chyb protože každé image je identifikován jeho jedinečné digest.

Image si můžete vyžádat z registru tak, že zadáte jeho přehled v operace přijetí změn. Některé systémy může být nakonfigurován, aby pomocí ověřování algoritmem digest zaručuje verze image právě načetli, i když stejně jako tagged image je následně do registru.

Například přebírání bitovou kopii z úložiště "acr-helloworld" podle manifestu ověřování algoritmem digest:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Pokud opakovaně odeslat upravenou Image s shodné značky, můžete vytvořit osamocené imagí – bitové kopie, které neoznačených, ale i nadále spotřebovávají místo v registru. Neoznačených obrázků se nezobrazují v rozhraní příkazového řádku Azure nebo na webu Azure Portal při seznamu nebo zobrazení imagí podle klíčových slov. Jejich vrstvy však stále existovat a využívat místo v registru. [Odstranit neoznačených obrázků](#delete-untagged-images) část tohoto článku popisuje uvolnění využité neoznačených obrázků.

## <a name="delete-image-data"></a>Odstranit data bitové kopie

Obrazová data můžete odstranit z vašeho registru kontejneru několika způsoby:

* Odstranit [úložiště](#delete-repository): Odstraní všechny Image a všechny jedinečné vrstvy v rámci tohoto úložiště.
* Tím odstraníte [značka](#delete-by-tag): Odstraní image, značky, všechny jedinečné vrstvy odkazuje image a všechny ostatní značky, které jsou spojené s imagí.
* Tím odstraníte [manifestu digest](#delete-by-manifest-digest): Odstraní image, všechny jedinečné vrstvy odkazuje image a všechny značky, které jsou spojené s imagí.

## <a name="delete-repository"></a>Odstranit úložiště

Odstranit úložiště odstraní všechny Image v úložišti, včetně všech značek, jedinečné vrstvami a manifestů. Při odstranění úložiště, obnovování prostor úložiště využitý pomocí bitové kopie, které byly v tomto úložišti.

Následující rozhraní příkazového řádku Azure odstraní "acr-helloworld" úložiště a všechny značky a manifesty v rámci tohoto úložiště. Pokud vrstvy odkazuje odstraněné manifesty nejsou odkazovány ostatních imagí v registru, odstraní se také svá data na úrovni.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Odstranit podle značky

Můžete odstranit jednotlivé Image z úložiště zadáním názvu úložiště a značky v operaci odstranění zopakovat. Když odstraníte podle značky, obnovíte prostor úložiště využitý tak všechny jedinečné vrstvy na obrázku (ne sdílí ostatních imagí v registru vrstvy).

Chcete-li odstranit podle klíčových slov, použijte [az acr úložiště odstranit] [ az-acr-repository-delete] a zadejte název bitové kopie `--image` parametr. Odstraní se všechny vrstvy, které jsou jedinečné pro bitovou kopii a všechny ostatní značky, které jsou spojené s imagí.

Odstraňuje se například "acr-helloworld:latest" image z registru "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Odstraňuje se *podle klíčových slov* neměly by být zaměňovány odstranění označení (Změna tagu). Značka s pomocí příkazu Azure CLI můžete odstranit [az acr úložiště Odtagujte][az-acr-repository-untag]. Když Odtagujte bitovou kopii, protože není uvolněno místo v žádné její [manifest](#manifest) a vrstvy data zůstanou uložena v registru. Pouze značky referenční samotné se odstraní.

## <a name="delete-by-manifest-digest"></a>Odstranit manifestu ověřování algoritmem digest

A [manifestu digest](#manifest-digest) lze přidružit jeden, jeden nebo více značek. Když odstraníte pomocí ověřování algoritmem digest, jsou všechny značky, které odkazuje manifest odstraněn, protože se data na úrovni pro všechny vrstvy, které jsou jedinečné pro bitovou kopii. Sdílené vrstvu, kterou se neodstraní data.

První seznam manifest hodnoty odstranit tak, že ověřování algoritmem digest, Digest pro úložiště, který obsahuje Image, kterou chcete odstranit. Příklad:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Dále určete digest, kterou chcete odstranit v [az acr úložiště odstranit] [ az-acr-repository-delete] příkazu. Příkaz má tento formát:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Například chcete-li odstranit poslední manifestu uvedené v předchozím výstupu (se značkou "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

"Acr-helloworld:v2" bitová kopie je odstraněna z registru, jako jsou všechna data vrstvy jedinečný této bitové kopie. Pokud manifest je přidruženo více značek, odstraní se také všechny přidružené značky.

## <a name="delete-untagged-images"></a>Odstranit neoznačených obrázků

Jak je uvedeno v [manifestu digest](#manifest-digest) části doručením (push) změny image pomocí existující značky **untags** dřív nabízený bitové kopie, výsledkem je obrázek osamocené (nebo "nepropojená"). Obraz dřív nabízený manifest – a jeho data na úrovni – zůstane v registru. Vezměte v úvahu následující posloupnost událostí:

1. Nahrání image *acr-helloworld* značkou **nejnovější**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Zkontrolujte manifesty pro úložiště *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Upravit *acr-helloworld* souboru Dockerfile
1. Nahrání image *acr-helloworld* značkou **nejnovější**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Zkontrolujte manifesty pro úložiště *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Jak je vidět ve výstupu příkazu poslední krok v pořadí, je teď osamocený manifestu, jehož `"tags"` vlastnost je prázdné pole. Tento manifest stále existuje v registru, spolu s daty všechny jedinečné vrstvy, na kterou odkazuje. **Chcete-li například Odstranit osamocené obrázků a jejich data vrstev, je nutné odstranit podle manifestu digest**.

### <a name="list-untagged-images"></a>Seznam neoznačených obrázků

V úložišti pomocí následujícího příkazu rozhraní příkazového řádku Azure můžete vytvořit seznam všech neoznačených obrázků. Nahraďte `<acrName>` a `<repositoryName>` s hodnotami, které jsou vhodné pro vaše prostředí.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?!(tags[?'*'])].digest"
```

### <a name="delete-all-untagged-images"></a>Odstranit všechny neoznačených obrázků

Pomocí následujících ukázkových skriptech s rozmyslem – odstranit data obrázku je NEOPRAVITELNÁ.

**Azure CLI v prostředí Bash**

Následující skript Bash odstraní všechny neoznačených obrázků z úložiště. Vyžaduje Azure CLI a **xargs**. Ve výchozím nastavení že skript provádí žádná odstranění. Změnit `ENABLE_DELETE` hodnota, která se `true` Povolit odstranění image.

> [!WARNING]
> Pokud máte systémy, které o přijetí změn imagí v manifestu digest (na rozdíl od název image), byste neměli spouštět tento skript. Odstranění neoznačených obrázků zabrání těchto systémech stahování imagí z registru. Místo potažením manifestu, zvažte využití *jedinečné tagování* schéma, [osvědčený postup doporučuje][tagging-best-practices].

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?!(tags[?'*'])].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
fi
```

**Azure CLI v prostředí PowerShell**

Následující skript prostředí PowerShell odstraní všechny neoznačených obrázků z úložiště. Vyžaduje prostředí PowerShell a rozhraní příkazového řádku Azure. Ve výchozím nastavení že skript provádí žádná odstranění. Změnit `$enableDelete` hodnota, která se `$TRUE` Povolit odstranění image.

> [!WARNING]
> Pokud máte systémy, které o přijetí změn imagí v manifestu digest (na rozdíl od název image), byste neměli spouštět tento skript. Odstranění neoznačených obrázků zabrání těchto systémech stahování imagí z registru. Místo potažením manifestu, zvažte využití *jedinečné tagování* schéma, [osvědčený postup doporučuje][tagging-best-practices].

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?!(tags[?'*'])].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>Další postup

Další informace o službě storage image ve službě Azure Container Registry najdete v části [úložiště image kontejneru ve službě Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
