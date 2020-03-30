---
title: Odstranění obrazových prostředků
description: Podrobnosti o tom, jak efektivně spravovat velikost registru odstraněním dat image kontejneru pomocí příkazů Azure CLI.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403336"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Odstranění ibi kontejnerů v registru kontejnerů Azure pomocí azure cli

Chcete-li zachovat velikost registru kontejnerů Azure, měli byste pravidelně odstraňovat zastaralá obrazová data. Zatímco některé image kontejnerů nasazené do produkčního prostředí mohou vyžadovat dlouhodobější úložiště, jiné lze obvykle odstranit rychleji. Například v automatizovaném scénáři sestavení a testování může váš registr rychle vyplnit bitové kopie, které nemusí být nikdy nasazeny a mohou být vymazány krátce po dokončení průchodu sestavení a testu.

Vzhledem k tomu, že obrazová data můžete odstranit několika různými způsoby, je důležité pochopit, jak každá operace odstranění ovlivňuje využití úložiště. Tento článek popisuje několik metod pro odstranění obrazových dat:

* Odstranit [úložiště](#delete-repository): Odstraní všechny obrázky a všechny jedinečné vrstvy v úložišti.
* Odstranit [podle značky](#delete-by-tag): Odstraní obraz, značku, všechny jedinečné vrstvy, na které obrázek odkazuje, a všechny ostatní tagy spojené s obrazem.
* Odstranit [podle přehledu manifestu](#delete-by-manifest-digest): Odstraní obraz, všechny jedinečné vrstvy, na které obrázek odkazuje, a všechny tagy spojené s obrazem.

Ukázkové skripty jsou k dispozici pro automatizaci operací odstranění.

Úvod k těmto konceptům najdete [v tématu O registrech, úložištích a obrázcích](container-registry-concepts.md).

## <a name="delete-repository"></a>Odstranit úložiště

Odstraněním úložiště odstraníte všechny obrazy v úložišti, včetně všech tagů, jedinečných vrstev a manifestů. Když odstraníte úložiště, obnovíte úložný prostor používaný obrazy, které odkazují na jedinečné vrstvy v tomto úložišti.

Následující příkaz Azure CLI odstraní úložiště "acr-helloworld" a všechny značky a manifesty v rámci úložiště. Pokud vrstvy odkazované odstraněnými manifesty nejsou odkazovány žádnými jinými bitovými kopiemi v registru, budou také odstraněna jejich data vrstev a obnoví se úložný prostor.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Odstranit podle značky

Jednotlivé obrázky můžete odstranit z úložiště zadáním názvu úložiště a značky v operaci odstranění. Když odstraníte značkou, obnovíte úložný prostor využívaný všemi jedinečnými vrstvami v obraze (vrstvy, které nejsou sdíleny žádnými jinými obrazy v registru).

Chcete-li odstranit značkou, použijte [az acr repozitář delete][az-acr-repository-delete] a zadejte název obrázku v parametru. `--image` Odstraní se všechny vrstvy jedinečné pro obraz a všechny ostatní tagy spojené s obrazem.

Například odstranění bitové kopie "acr-helloworld:latest" z registru "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Odstranění *podle značky* by nemělo být zaměňováno s odstraněním značky (zrušení označení). Značku můžete odstranit pomocí zrušení značky Azure CLI příkaz [az acr repository][az-acr-repository-untag]. Při zrušení označení bitové kopie není uvolněno žádné místo, protože jeho [data manifestu](container-registry-concepts.md#manifest) a vrstvy zůstávají v registru. Odstraní se pouze samotný odkaz na značku.

## <a name="delete-by-manifest-digest"></a>Odstranit podle přehledu manifestu

Manifest [digest](container-registry-concepts.md#manifest-digest) může být přidružen k jedné, žádné nebo více značek. Když odstraníte pomocí digest, odstraní se všechny tagy odkazované manifestem, stejně jako data vrstev pro všechny vrstvy jedinečné pro obraz. Data sdílené vrstvy se neodstraní.

Chcete-li odstranit pomocí digest, nejprve seznam manifestdigests pro úložiště obsahující obrázky, které chcete odstranit. Například:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
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

Dále zadejte digest, který chcete odstranit v příkazu [delete úložiště az acr.][az-acr-repository-delete] Příkaz má tento formát:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Chcete-li například odstranit poslední manifest uvedený v předchozím výstupu (se značkou "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

Bitová `acr-helloworld:v2` kopie je odstraněna z registru, stejně jako všechna data vrstvy jedinečná pro tento obrázek. Pokud je manifest přidružen k více značkám, odstraní se také všechny přidružené značky.

## <a name="delete-digests-by-timestamp"></a>Odstranit výtahy podle časového razítka

Chcete-li zachovat velikost úložiště nebo registru, může být nutné pravidelně odstraňovat manifestdigesty starší než určité datum.

Následující příkaz Azure CLI uvádí všechny manifest digest v úložišti starší než zadané časové razítko, ve vzestupném pořadí. Nahraďte `<acrName>` a `<repositoryName>` s hodnotami vhodnými pro vaše prostředí. Časové razítko může být výraz úplné datum-čas nebo datum, jako v tomto příkladu.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Po identifikaci zastaralé manifest digests, můžete spustit následující Bash skript odstranit manifest digests starší než zadané časové razítko. Vyžaduje azure CLI a **xargs**. Ve výchozím nastavení skript neprovádí žádné odstranění. Změňte `ENABLE_DELETE` hodnotu tak, `true` aby byla povolena odstranění obrazu.

> [!WARNING]
> Použijte následující ukázkový skript opatrně – odstraněná obrazová data jsou nezdolná. Pokud máte systémy, které vytahují obrazy podle manifestdigest (na rozdíl od názvu obrázku), neměli byste tyto skripty spouštět. Odstranění matné listy manifestu zabrání těmto systémům v vytažení bitových kopií z registru. Místo toho, aby tahání podle manifestu, zvažte přijetí jedinečné schéma *označování,* [doporučený osvědčený postup](container-registry-image-tag-version.md). 

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

## <a name="delete-untagged-images"></a>Odstranění netagovaných obrázků

Jak je uvedeno v části [Manifest digest,](container-registry-concepts.md#manifest-digest) stisknutí upraveného obrazu pomocí existující značky **odznačí** dříve posunutou bitovou kopii, což vede k osamocenému (nebo "visícímu") obrázku. Dříve posunutý manifest bitové kopie a data jeho vrstev zůstane v registru. Zvažte následující posloupnost událostí:

1. Push image *acr-helloworld* s tag **nejnovější:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Zkontrolujte manifesty pro repozitář *acr-helloworld*:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
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

1. Upravit *acr-helloworld* Dockerfile
1. Push image *acr-helloworld* s tag **nejnovější:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Zkontrolujte manifesty pro repozitář *acr-helloworld*:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
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

Jak můžete vidět ve výstupu poslední krok v sekvenci, je `"tags"` nyní osamocený manifest, jehož vlastnost je prázdný seznam. Tento manifest stále existuje v registru, spolu s všechna data jedinečné vrstvy, na které odkazuje. **Chcete-li odstranit tyto osamocené obrazy a jejich data vrstev, je nutné odstranit podle manifestdigest**.

## <a name="delete-all-untagged-images"></a>Odstranění všech neoznačených obrázků

Všechny neoznačené obrázky můžete v úložišti uvést pomocí následujícího příkazu Azure CLI. Nahraďte `<acrName>` a `<repositoryName>` s hodnotami vhodnými pro vaše prostředí.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Pomocí tohoto příkazu ve skriptu můžete odstranit všechny netagované obrázky v úložišti.

> [!WARNING]
> Následující ukázkové skripty používejte opatrně – odstraněná obrazová data jsou nezdolná. Pokud máte systémy, které vytahují obrazy podle manifestdigest (na rozdíl od názvu obrázku), neměli byste tyto skripty spouštět. Odstranění neoznačených obrázků zabrání těmto systémům v vytažení bitových kopií z registru. Místo toho, aby tahání podle manifestu, zvažte přijetí jedinečné schéma *označování,* [doporučený osvědčený postup](container-registry-image-tag-version.md).

**Azure CLI v Bash**

Následující skript Bash odstraní všechny neoznačené obrázky z úložiště. Vyžaduje azure CLI a **xargs**. Ve výchozím nastavení skript neprovádí žádné odstranění. Změňte `ENABLE_DELETE` hodnotu tak, `true` aby byla povolena odstranění obrazu.

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
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI v PowerShellu**

Následující skript prostředí PowerShell odstraní všechny neoznačené obrázky z úložiště. Vyžaduje Prostředí PowerShell a azure CLI. Ve výchozím nastavení skript neprovádí žádné odstranění. Změňte `$enableDelete` hodnotu tak, `$TRUE` aby byla povolena odstranění obrazu.

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


## <a name="automatically-purge-tags-and-manifests-preview"></a>Automatické vymazání značek a manifestů (Preview)

Jako alternativu k skriptování příkazů Azure CLI spusťte úlohu ACR na vyžádání nebo naplánovanou acr, abyste odstranili všechny značky, které jsou starší než určitá doba trvání nebo odpovídají zadanému filtru názvů. Další informace najdete v tématu [Automatické vymazání ibi z registru kontejnerů Azure](container-registry-auto-purge.md).

Volitelně můžete nastavit [zásady uchovávání informací](container-registry-retention-policy.md) pro každý registr pro správu netagovaných manifestů. Pokud povolíte zásady uchovávání informací, manifesty bitových obrázků v registru, které nemají žádné přidružené značky, a podkladová data vrstvy se po nastavené mši automaticky odstraní.

## <a name="next-steps"></a>Další kroky

Další informace o úložišti bitových bitových údajů v registru kontejnerů Azure najdete [v tématu Úložiště bitových obrázků kontejneru v registru kontejnerů Azure](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
