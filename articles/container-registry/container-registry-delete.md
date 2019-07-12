---
title: Odstranit image prostředky ve službě Azure Container Registry
description: Podrobnosti o tom, jak efektivní správa velikosti registru tak, že odstraníte data image kontejneru.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/17/2019
ms.author: danlep
ms.openlocfilehash: c603afa61499a615a0882cef06f14fd3d080a9ef
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797765"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Odstranit Image kontejnerů ve službě Azure Container Registry

Pokud chcete zachovat velikost svého registru kontejneru Azure, odstraníte pravidelně zastaralé image data. Zatímco některé kontejnerové Image nasadili do produkčního prostředí můžou vyžadovat dlouhodobější úložiště, jiné obvykle se dá odstranit rychleji. Například automatické sestavení a testovací scénáře, svého registru můžete vyplnit s imagí, které nikdy je možno nasadit a může být odstraněna krátce po dokončení sestavení a testování průchodu.

Budete moct odstranit bitové kopie dat v několika různými způsoby, a proto je důležité pochopit, jak každá operace odstranění ovlivňuje využití úložiště. Tento článek se týká několik metod pro odstranění dat bitové kopie:

* Odstranit [úložiště](#delete-repository): Odstraní všechny Image a všechny jedinečné vrstvy v rámci tohoto úložiště.
* Tím odstraníte [značka](#delete-by-tag): Odstraní image, značky, všechny jedinečné vrstvy odkazuje image a všechny ostatní značky, které jsou spojené s imagí.
* Tím odstraníte [manifestu digest](#delete-by-manifest-digest): Odstraní image, všechny jedinečné vrstvy odkazuje image a všechny značky, které jsou spojené s imagí.

Ukázkové skripty pro automatizaci operace odstranění.

Úvodní informace o těchto konceptech najdete v tématu [o registrů, úložišť a imagí](container-registry-concepts.md).

## <a name="delete-repository"></a>Odstranit úložiště

Odstranit úložiště odstraní všechny Image v úložišti, včetně všech značek, jedinečné vrstvami a manifestů. Při odstranění úložiště, obnovování prostor úložiště využitý pomocí bitové kopie, které odkazují na jedinečné vrstvy v tomto úložišti.

Následující rozhraní příkazového řádku Azure odstraní "acr-helloworld" úložiště a všechny značky a manifesty v rámci tohoto úložiště. Pokud vrstvy odkazuje odstraněné manifesty nejsou odkazovány ostatních imagí v registru, svá data na úrovni je také odstranit, obnovování prostor úložiště.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Odstranit podle značky

Můžete odstranit jednotlivé Image z úložiště zadáním názvu úložiště a značky v operaci odstranění zopakovat. Když odstraníte podle značky, obnovíte prostor úložiště využitý tak všechny jedinečné vrstvy na obrázku (ne sdílí ostatních imagí v registru vrstvy).

Chcete-li odstranit podle klíčových slov, použijte [az acr úložiště odstranit][az-acr-repository-delete] a zadejte název bitové kopie `--image` parametr. Odstraní se všechny vrstvy, které jsou jedinečné pro bitovou kopii a všechny ostatní značky, které jsou spojené s imagí.

Odstraňuje se například "acr-helloworld:latest" image z registru "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Odstraňuje se *podle klíčových slov* neměly by být zaměňovány odstranění označení (Změna tagu). Značka s pomocí příkazu Azure CLI můžete odstranit [az acr úložiště Odtagujte][az-acr-repository-untag]. Když Odtagujte bitovou kopii, protože není uvolněno místo v žádné její [manifest](container-registry-concepts.md#manifest) a vrstva dat zůstanou v registru. Pouze značky referenční samotné se odstraní.

## <a name="delete-by-manifest-digest"></a>Odstranit manifestu ověřování algoritmem digest

A [manifestu digest](container-registry-concepts.md#manifest-digest) lze přidružit jeden, jeden nebo více značek. Když odstraníte pomocí ověřování algoritmem digest, jsou všechny značky, které odkazuje manifest odstraněn, protože se data na úrovni pro všechny vrstvy, které jsou jedinečné pro bitovou kopii. Sdílené vrstvu, kterou se neodstraní data.

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

Dále určete digest, kterou chcete odstranit v [az acr úložiště odstranit][az-acr-repository-delete] příkazu. Příkaz má tento formát:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Například chcete-li odstranit poslední manifestu uvedené v předchozím výstupu (se značkou "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

`acr-helloworld:v2` Bitová kopie je odstraněna z registru, jako jsou všechna data vrstvy jedinečný této bitové kopie. Pokud manifest je přidruženo více značek, odstraní se také všechny přidružené značky.

## <a name="delete-digests-by-timestamp"></a>Odstranit přehledu pomocí časového razítka

K udržování velikosti úložiště nebo registru, můžete potřebovat odstranit pravidelně manifestu přehledu starší než ke konkrétnímu datu.

Následující příkaz rozhraní příkazového řádku Azure zobrazí seznam všech manifestu digest v úložišti starší než zadané časové razítko ve vzestupném pořadí. Nahraďte `<acrName>` a `<repositoryName>` s hodnotami, které jsou vhodné pro vaše prostředí. Časové razítko může být výraz úplné datum a čas nebo datum, jako v následujícím příkladu.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Po identifikaci zastaralé manifestu přehledu, můžete spustit následující skript Bash manifestu přehledu starší než zadané časové razítko odstranění. Vyžaduje Azure CLI a **xargs**. Ve výchozím nastavení že skript provádí žádná odstranění. Změnit `ENABLE_DELETE` hodnota, která se `true` Povolit odstranění image.

> [!WARNING]
> Použijte následující ukázkový skript s rozmyslem – data odstraněná image je NEOPRAVITELNÁ. Pokud máte systémy, které o přijetí změn imagí v manifestu digest (na rozdíl od název image), byste neměli spouštět tyto skripty. Odstranění manifestu přehledu zabrání těchto systémech stahování imagí z registru. Místo potažením manifestu, zvažte využití *jedinečné tagování* schéma, [osvědčený postup doporučuje][tagging-best-practices]. 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Odstranit neoznačených obrázků

Jak je uvedeno v [manifestu digest](container-registry-concepts.md#manifest-digest) části doručením (push) změny image pomocí existující značky **untags** dřív nabízený bitové kopie, výsledkem je obrázek osamocené (nebo "nepropojená"). Obraz dřív nabízený manifest – a jeho data na úrovni – zůstane v registru. Vezměte v úvahu následující posloupnost událostí:

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

Jak je vidět ve výstupu příkazu poslední krok v pořadí, je teď osamocený manifestu, jehož `"tags"` vlastnosti je prázdný seznam. Tento manifest stále existuje v registru, spolu s daty všechny jedinečné vrstvy, na kterou odkazuje. **Chcete-li například Odstranit osamocené obrázků a jejich data vrstev, je nutné odstranit podle manifestu digest**.

## <a name="delete-all-untagged-images"></a>Odstranit všechny neoznačených obrázků

V úložišti pomocí následujícího příkazu rozhraní příkazového řádku Azure můžete vytvořit seznam všech neoznačených obrázků. Nahraďte `<acrName>` a `<repositoryName>` s hodnotami, které jsou vhodné pro vaše prostředí.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Pomocí tohoto příkazu ve skriptu, můžete odstranit všechny neoznačených obrázků v úložišti.

> [!WARNING]
> Pomocí následujících ukázkových skriptech s rozmyslem – odstranit data obrázku je NEOPRAVITELNÁ. Pokud máte systémy, které o přijetí změn imagí v manifestu digest (na rozdíl od název image), byste neměli spouštět tyto skripty. Odstranění neoznačených obrázků zabrání těchto systémech stahování imagí z registru. Místo potažením manifestu, zvažte využití *jedinečné tagování* schéma, [osvědčený postup doporučuje][tagging-best-practices].

**Azure CLI v prostředí Bash**

Následující skript Bash odstraní všechny neoznačených obrázků z úložiště. Vyžaduje Azure CLI a **xargs**. Ve výchozím nastavení že skript provádí žádná odstranění. Změnit `ENABLE_DELETE` hodnota, která se `true` Povolit odstranění image.

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
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI v prostředí PowerShell**

Následující skript prostředí PowerShell odstraní všechny neoznačených obrázků z úložiště. Vyžaduje prostředí PowerShell a rozhraní příkazového řádku Azure. Ve výchozím nastavení že skript provádí žádná odstranění. Změnit `$enableDelete` hodnota, která se `$TRUE` Povolit odstranění image.

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
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```

## <a name="next-steps"></a>Další postup

Další informace o službě storage image ve službě Azure Container Registry najdete v části [úložiště image kontejneru ve službě Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
