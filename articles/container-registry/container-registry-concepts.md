---
title: O úložišť a imagí ve službě Azure Container Registry
description: Úvod do klíčových konceptů Azure container registry, úložišť a imagí kontejneru.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: a14f0a2a86c5e4922fcddf3c92d48c6dfb1497a3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800325"
---
# <a name="about-registries-repositories-and-images"></a>O registrů, úložišť a imagí

Tento článek představuje klíčové koncepty registrů kontejnerů, úložiště a Image kontejnerů a související artefakty. 

## <a name="registry"></a>Registru

Kontejner *registru* je služba, která ukládá a distribuuje imagí kontejnerů. Docker Hub je registr veřejném kontejneru, který podporuje opensourcovou komunitu a slouží jako obecné katalog imagí. Služba Azure Container Registry poskytuje uživatelům přímou kontrolu nad jejich obrázků pomocí integrované ověřování [geografickou replikaci](container-registry-geo-replication.md) podporou globální distribuce a spolehlivost pro nasazení síťové blízkosti [ Konfigurace virtuální sítě a brány firewall](container-registry-vnet.md), [značky uzamčení](container-registry-image-lock.md), a mnoha dalším rozšířené funkce. 

Kromě imagí kontejnerů Dockeru, související s Azure Container Registry podporuje [obsahu artefakty](container-registry-image-formats.md) včetně formátů obrázku iniciativy otevřete kontejner (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Obsahu adresovatelný prvky artefakt

Adresa artefakt ve službě Azure container registry obsahuje následující prvky. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** – plně kvalifikovaný název registru hostitele. Hostitel registru ve službě Azure container registry je ve formátu *myregistry*. azurecr.io (malými písmeny). Při použití Dockeru nebo jiných nástrojů klienta s artefakty o přijetí změn nebo nabízených oznámení do služby Azure container registry, musíte zadat loginUrl. 
* **obor názvů** - oddělené lomítkem logické seskupení souvisejících imagí nebo artefakty – například pro pracovní skupinu nebo aplikaci
* **artefakt** – název úložiště pro konkrétní image nebo artefaktů
* **značka** -konkrétní verzi bitové kopie nebo uložen v úložišti artefaktů


Například může celý název image ve službě Azure container registry vypadat:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Naleznete v následujících částech Další informace o těchto prvcích.

## <a name="repository-name"></a>Název úložiště

Spravovat registry kontejnerů *úložišť*, kolekce imagí kontejneru nebo jiné artefakty se stejným názvem, ale jiné značky. Například následující tři Image jsou v úložišti "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Názvy úložišť může také obsahovat [obory názvů](container-registry-best-practices.md#repository-namespaces). Obory názvů umožňují skupiny obrázků s využitím dopředné lomítko oddělené úložiště názvy, například:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

Image kontejneru či jiný artefakt v registru je přidružen jeden nebo více značek, obsahuje jeden nebo více vrstev a je identifikován manifestu. Pochopení, jak tyto komponenty vzájemně souvisí může pomoci efektivně spravovat váš registr.

### <a name="tag"></a>Značka

*Značka* pro bitovou kopii či jiný artefakt určuje její verzi. Jeden artefakt v úložišti můžete přiřadit jednu nebo více značek a může být "neoznačený i." To znamená můžete odstranit všechny značky pomocí bitové kopie dat na obrázku (vrstvy) budou i nadále v registru.

Úložiště (nebo úložiště a obor názvů) plus definuje názvu obrázku značky. Vám může nabízená a vyžádaná instalace image tak, že zadáte jeho název v operaci push nebo pull.

Jak označit Image kontejneru se řídí vaše scénáře pro vývoj nebo nasazení. Například stabilní značky se doporučují pro základní Image a značky jedinečný pro nasazení bitových kopií. Další informace najdete v tématu [doporučení pro označování a správy verzí Image kontejneru](container-registry-image-tag-version.md).

### <a name="layer"></a>Vrstva

Image kontejnerů se skládají z nejméně jeden *vrstvy*, každý odpovídající řádek v souboru Dockerfile, který definuje image. Imagí v registru sdílejí společné vrstev zvyšuje efektivitu úložiště. Například více bitových kopií v různých úložištích může sdílet stejné základní vrstvě Alpine Linuxu, ale jenom jednu kopii této vrstvy je uložen v registru.

Sdílení vrstvy také optimalizuje distribuci vrstvy do uzlů pomocí více bitových kopií sdílení běžné vrstvy. Například pokud obrázku již v uzlu obsahuje vrstvy Alpine Linux jako její základ, nepřenese následné operace přijetí změn různých bitové kopie odkazující na stejnou úroveň vrstvy k uzlu. Místo toho odkazuje na vrstvě již existuje na uzlu.

Pro zajištění izolace zabezpečení a ochrana proti manipulaci s potenciální vrstvy, vrstvy nejsou sdíleny Registry.

### <a name="manifest"></a>Manifest

Každá image kontejneru nebo artefaktů nahrány do registru kontejneru je přidružena *manifest*. Manifest, vygeneruje registru při obrázku, převede, jednoznačně identifikuje image a určuje jeho vrstvy. Manifesty pro úložiště pomocí příkazu rozhraní příkazového řádku Azure můžete vytvořit seznam [az acr úložiště show manifesty][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Seznam manifest hodnoty Digest pro úložiště "acr-helloworld":

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

### <a name="manifest-digest"></a>Manifest ověřování algoritmem digest

Manifestů jsou označeny jedinečnou hodnotu hash SHA-256, nebo *manifestu digest*. Každý obrázek nebo artefaktů – jsou zda příznakem nebo ne--je identifikován jeho algoritmem digest. Hodnota ověřování algoritmem digest je jedinečný, i v případě, že data na úrovni image je stejné jako u jiného obrázku. Tento mechanismus je, co vám umožňuje opakovaně identicky tagged image nahrajete do registru. Například můžete opakovaně nabízet `myimage:latest` do registru bez chyb protože každé image je identifikován jeho jedinečné digest.

Image si můžete vyžádat z registru tak, že zadáte jeho přehled v operace přijetí změn. Některé systémy může být nakonfigurován, aby pomocí ověřování algoritmem digest zaručuje verze image právě načetli, i když stejně jako tagged image je následně do registru.

Třeba získat obrázek z úložiště "acr-helloworld" podle manifestu ověřování algoritmem digest:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Pokud opakovaně odeslat upravenou Image s shodné značky, můžete vytvořit osamocené imagí – bitové kopie, které neoznačených, ale i nadále spotřebovávají místo v registru. Neoznačených obrázků se nezobrazují v rozhraní příkazového řádku Azure nebo na webu Azure Portal při seznamu nebo zobrazení imagí podle klíčových slov. Jejich vrstvy však stále existovat a využívat místo v registru. Informace o uvolnění prostor využitý neoznačených obrázků najdete v tématu [odstranit Image kontejnerů ve službě Azure Container Registry](container-registry-delete.md).


## <a name="next-steps"></a>Další postup

Další informace o [úložiště obrázků](container-registry-storage.md) a [podporované formáty obsahu](container-registry-image-formats.md) ve službě Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


