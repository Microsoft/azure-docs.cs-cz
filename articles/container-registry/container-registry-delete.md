---
title: Odstranit prostředky obrázku
description: Podrobné informace o tom, jak efektivně spravovat velikost registru pomocí příkazů rozhraní příkazového řádku Azure
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "78403336"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Odstranění imagí kontejneru v Azure Container Registry pomocí rozhraní příkazového řádku Azure

Pokud chcete zachovat velikost služby Azure Container Registry, měli byste pravidelně odstraňovat zastaralá data imagí. I když některé image kontejnerů nasazené do produkčního prostředí můžou vyžadovat dlouhodobé ukládání, můžou se obvykle odstranit i další. Například ve scénáři automatizovaného sestavení a testování může registr rychle vyplnit image, které nikdy nebudou nasazeny, a lze je vymazat krátce po dokončení sestavení a průchodu testu.

Vzhledem k tomu, že je možné odstranit obrazová data několika různými způsoby, je důležité pochopit, jak jednotlivé operace odstranění ovlivňují využití úložiště. Tento článek se věnuje několika metodám odstraňování dat imagí:

* Odstranění [úložiště](#delete-repository): odstraní všechny image a všechny jedinečné vrstvy v rámci úložiště.
* Odstranit podle [značky](#delete-by-tag): odstraní obrázek, značku, všechny jedinečné vrstvy, na které obrázek odkazuje, a všechny ostatní značky přidružené k imagi.
* Odstranit [manifest Digest](#delete-by-manifest-digest): odstraní obrázek, všechny jedinečné vrstvy, na které odkazuje obrázek, a všechny značky přidružené k imagi.

K dispozici jsou ukázkové skripty, které usnadňují automatizaci operací odstranění.

Úvod do těchto konceptů najdete v tématu [o registrech, úložištích a obrázcích](container-registry-concepts.md).

## <a name="delete-repository"></a>Odstranit úložiště

Odstraněním úložiště dojde k odstranění všech imagí v úložišti, včetně všech značek, jedinečných vrstev a manifestů. Při odstraňování úložiště obnovíte prostor úložiště používaný obrázky, které odkazují na jedinečné vrstvy v daném úložišti.

Následující příkaz rozhraní příkazového řádku Azure odstraní úložiště "ACR-HelloWorld" a všechny značky a manifesty v rámci úložiště. Pokud na vrstvy, na které odkazují odstraněné manifesty, neodkazuje žádný jiný obrázek v registru, jejich data vrstvy jsou také odstraněna a obnovují se prostor úložiště.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Odstranit podle značky

Jednotlivé image můžete z úložiště odstranit zadáním názvu a značky úložiště v operaci odstranění. Při odstranění pomocí značky obnovíte prostor úložiště využívaný všemi jedinečnými vrstvami v imagi (vrstvy, které nejsou sdíleny žádnými ostatními imagemi v registru).

Pokud chcete odstranit podle značky, použijte příkaz [AZ ACR úložiště Delete][az-acr-repository-delete] a zadejte název Image v `--image` parametru. Všechny vrstvy, které jsou pro obrázek jedinečné, a všechny další značky přidružené k imagi se odstraní.

Například odstraněním image "ACR-HelloWorld: nejnovější" z registru "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Odstranění *pomocí značky* by nemělo být zaměněno s odstraněním značky (odznačení). Značku můžete odstranit pomocí příkazu Azure CLI [AZ ACR zrušit označení úložiště][az-acr-repository-untag]. Při zrušit označeníí obrázku se neuvolní žádné místo, protože jeho [manifest](container-registry-concepts.md#manifest) a data vrstvy zůstávají v registru. Odstraní se jenom samotný odkaz na značku.

## <a name="delete-by-manifest-digest"></a>Odstranit podle výtahu manifestu

[Výtah manifestu](container-registry-concepts.md#manifest-digest) může být přidružen k jednomu, žádnému nebo více značkám. Při odstranění algoritmem Digest jsou všechny značky, na které se odkazuje manifest, odstraněny, stejně jako data vrstev pro všechny vrstvy, které jsou pro Image jedinečné. Data sdílené vrstvy se neodstraňují.

Chcete-li odstranit seznam Digest, nejprve vypište výtahy manifestu pro úložiště obsahující obrázky, které chcete odstranit. Například:

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

V dalším kroku zadejte hodnotu Digest, kterou chcete odstranit, z příkazu [AZ ACR úložiště Delete][az-acr-repository-delete] . Příkaz má tento formát:

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

`acr-helloworld:v2`Obrázek je odstraněn z registru, stejně jako jakákoli data vrstvy, která jsou pro tento obrázek jedinečná. Pokud je manifest spojen s více značkami, jsou odstraněny také všechny přidružené značky.

## <a name="delete-digests-by-timestamp"></a>Odstranit výtahy podle časového razítka

Aby se zachovala velikost úložiště nebo registru, možná budete muset pravidelně odstranit výtahy manifestů starší než určité datum.

Následující příkaz Azure CLI vypíše všechny hodnoty Digest manifestu v úložišti starším než zadané časové razítko ve vzestupném pořadí. `<acrName>`Hodnoty a nahraďte hodnotami, které `<repositoryName>` jsou vhodné pro vaše prostředí. Časové razítko může být úplný výraz data a času nebo datum, jako v tomto příkladu.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Po identifikaci zastaralých výtahů manifestů můžete spuštěním následujícího skriptu bash odstranit výtahy manifestu starší než zadané časové razítko. Vyžaduje rozhraní příkazového řádku Azure a **xargs**. Ve výchozím nastavení skript neprovede žádné odstranění. Pokud chcete `ENABLE_DELETE` `true` Povolit odstranění obrázku, změňte hodnotu na.

> [!WARNING]
> Použijte následující vzorový skript s varováním – data odstraněná v obrazech je možné obnovit. Pokud máte systémy, které vyžádají image podle výtahu manifestu (na rozdíl od názvu bitové kopie), neměli byste tyto skripty spouštět. Odstraněním výtahů manifestu zabráníte těmto systémům v navrácení imagí z registru. Místo toho, aby se vybral manifest, zvažte přijetí *jedinečného schématu označování* , což je [doporučený osvědčený postup](container-registry-image-tag-version.md). 

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

## <a name="delete-untagged-images"></a>Odstranit neoznačené obrázky

Jak je uvedeno v oddílu [výtahu manifestu](container-registry-concepts.md#manifest-digest) , vložení upravené Image pomocí existující značky **odznačí** dříve nabízený obrázek, výsledkem je osamocený obrázek (neboli "dangling"). Manifest dříve vloženého obrázku – a jeho data vrstev – zůstanou v registru. Vezměte v úvahu následující posloupnost událostí:

1. Push image *ACR-HelloWorld* s označením **nejnovější**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Podívejte se na manifesty pro úložiště *ACR-HelloWorld*:

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

1. Upravit *ACR-HelloWorld* souboru Dockerfile
1. Push image *ACR-HelloWorld* s označením **nejnovější**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Podívejte se na manifesty pro úložiště *ACR-HelloWorld*:

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

Jak vidíte ve výstupu posledního kroku v sekvenci, je teď osamocený manifest, jehož `"tags"` vlastnost je prázdný seznam. Tento manifest stále existuje v registru společně s libovolnými jedinečnými daty vrstev, na které odkazuje. **Chcete-li odstranit takové osamocené bitové kopie a jejich data vrstvy, je nutné odstranit výtah manifestu**.

## <a name="delete-all-untagged-images"></a>Odstranit všechny neoznačené obrázky

Pomocí následujícího příkazu rozhraní příkazového řádku Azure můžete zobrazit seznam všech netagovaných imagí v úložišti. `<acrName>`Hodnoty a nahraďte hodnotami, které `<repositoryName>` jsou vhodné pro vaše prostředí.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Pomocí tohoto příkazu ve skriptu můžete odstranit všechny neoznačené obrázky v úložišti.

> [!WARNING]
> Použití následujících ukázkových skriptů s opatrností – data odstraněných obrázků je možné obnovit. Pokud máte systémy, které vyžádají image podle výtahu manifestu (na rozdíl od názvu bitové kopie), neměli byste tyto skripty spouštět. Odstraněním netagovaných imagí znemožníte těmto systémům navrácení imagí z registru. Místo toho, aby se vybral manifest, zvažte přijetí *jedinečného schématu označování* , což je [doporučený osvědčený postup](container-registry-image-tag-version.md).

**Rozhraní příkazového řádku Azure v bash**

Následující skript bash odstraní všechny neoznačené image z úložiště. Vyžaduje rozhraní příkazového řádku Azure a **xargs**. Ve výchozím nastavení skript neprovede žádné odstranění. Pokud chcete `ENABLE_DELETE` `true` Povolit odstranění obrázku, změňte hodnotu na.

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

**Rozhraní příkazového řádku Azure v PowerShellu**

Následující skript PowerShellu odstraní všechny neoznačené image z úložiště. Vyžaduje prostředí PowerShell a rozhraní příkazového řádku Azure CLI. Ve výchozím nastavení skript neprovede žádné odstranění. Pokud chcete `$enableDelete` `$TRUE` Povolit odstranění obrázku, změňte hodnotu na.

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

Jako alternativu ke skriptování příkazů rozhraní příkazového řádku Azure můžete spuštěním úlohy na vyžádání nebo naplánované ACR odstranit všechny značky, které jsou starší než určitá doba trvání nebo odpovídají zadanému filtru názvů. Další informace najdete v tématu [Automatické mazání imagí z Azure Container Registry](container-registry-auto-purge.md).

Volitelně můžete pro každý registr nastavit [zásady uchovávání informací](container-registry-retention-policy.md) pro správu netagovaných manifestů. Když zapnete zásadu uchovávání informací, manifesty obrázků v registru, které nemají žádné přidružené značky, a podkladová data vrstvy se po uplynutí nastaveného období odstraní automaticky.

## <a name="next-steps"></a>Další kroky

Další informace o úložišti imagí v Azure Container Registry najdete [v části úložiště imagí kontejneru v Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
