---
title: O repozitářích & obrázků
description: Úvod do klíčových konceptů registrů kontejnerů Azure, úložišť a ibi obrázků kontejnerů.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: ea6e2577d3eee91626dd613617a0b79e4ff3d6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247056"
---
# <a name="about-registries-repositories-and-images"></a>O registrech, repozitářích a obrázcích

Tento článek představuje klíčové koncepty registrů kontejnerů, úložišť a iimages kontejnerů a souvisejících artefaktů. 

## <a name="registry"></a>Registr

*Registr* kontejnerů je služba, která ukládá a distribuuje image kontejneru. Docker Hub je veřejný registr kontejnerů, který podporuje komunitu s otevřeným zdrojovým kódem a slouží jako obecný katalog ih. Azure Container Registry poskytuje uživatelům přímou kontrolu nad jejich image, s integrovaným ověřováním, [geografickou replikací](container-registry-geo-replication.md) podporující globální distribuci a spolehlivost pro nasazení v blízkosti sítě, [konfiguraci virtuální sítě a brány firewall](container-registry-vnet.md), [zamykání značek](container-registry-image-lock.md)a mnoho dalších rozšířených funkcí. 

Kromě ibi kontejnerů Dockeru podporuje Azure Container Registry související [artefakty obsahu,](container-registry-image-formats.md) včetně formátů bitových kopií Open Container Initiative (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Obsah adresovatelné prvky artefaktu

Adresa artefaktu v registru kontejneru Azure obsahuje následující prvky. 

`[loginUrl]/[namespace]/[artifact:][tag]`

* **loginUrl** - Plně kvalifikovaný název hostitele registru. Hostitel registru v registru kontejneru Azure je ve formátu *myregistry*.azurecr.io (všechna malá písmena). Musíte zadat loginUrl při použití Dockeru nebo jiných klientských nástrojů pro vytahovací nebo nabízený artefakty do registru kontejneru Azure. 
* **obor názvů** – logické seskupení souvisejících obrázků nebo artefaktů oddělené lomítkem – například pro pracovní skupinu nebo aplikaci
* **artefakt** - Název úložiště pro konkrétní obrázek nebo artefakt
* **tag** - Konkrétní verze obrázku nebo artefaktu uloženého v úložišti


Úplný název bitové kopie v registru kontejneru Azure může například vypadat takto:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Podrobnosti o těchto prvcích naleznete v následujících částech.

## <a name="repository-name"></a>Název úložiště

Registry kontejnerů spravují *úložiště*, kolekce inicicí kontejnerů nebo jiné artefakty se stejným názvem, ale s různými značkami. Například následující tři obrázky jsou v úložišti "acr-helloworld":


- *acr-helloworld:nejnovější*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Názvy úložišť mohou také obsahovat [obory názvů](container-registry-best-practices.md#repository-namespaces). Obory názvů umožňují seskupit obrázky pomocí názvů úložišť oddělených lomítkem, například:

- *marketing/kampaň10-18/web:v2*
- *marketing/kampaň10-18/api:v3*
- *marketing/campaign10-18/email-odesílatel:v2*
- *vrácení produktu/web-podání:20180604*
- *produkt-vrátí/starší-integrátor:20180715*

## <a name="image"></a>Image

Image kontejneru nebo jiný artefakt v registru je přidružen k jedné nebo více značek, má jednu nebo více vrstev a je identifikován manifestem. Pochopení vzájemného vztahu těchto součástí vám může pomoci efektivně spravovat registr.

### <a name="tag"></a>Značka

*Značka* pro bitovou kopii nebo jiný artefakt určuje jeho verzi. Jeden artefakt v úložišti může být přiřazen jeden nebo více značek a může být také "netagované." To znamená, že můžete odstranit všechny značky z bitové kopie, zatímco data obrazu (jeho vrstev) zůstanou v registru.

Úložiště (nebo úložiště a obor názvů) plus značka definují název obrázku. Obrázek můžete stisknout a vytáhnout zadáním jeho názvu v operaci push nebo pull.

Jak označit image kontejneru se řídí vaše scénáře k jejich vývoji nebo nasazení. Například stabilní značky se doporučují pro údržbu základních bitových kopií a jedinečné značky pro nasazení bitových kopií. Další informace naleznete [v tématu Doporučení pro označování a správu verzí imitek kontejneru](container-registry-image-tag-version.md).

### <a name="layer"></a>Vrstva

Image kontejneru se složí z jedné nebo více *vrstev*, z nichž každá odpovídá řádku v souboru Dockerfile, který definuje obraz. Obrázky v registru sdílejí společné vrstvy a zvyšují efektivitu úložiště. Například několik bitových kopií v různých repozitářích může sdílet stejnou základní vrstvu Alpine Linuxu, ale pouze jedna kopie této vrstvy je uložena v registru.

Sdílení vrstev také optimalizuje distribuci vrstev do uzlů s více obrazy, které sdílejí společné vrstvy. Například pokud obraz již na uzlu obsahuje vrstvu Alpine Linux jako jeho základ, následné vytažení jiného obrázku odkazující na stejnou vrstvu nepřenáší vrstvu do uzlu. Místo toho odkazuje na hladinu, která již v uzlu existuje.

Chcete-li zajistit zabezpečenou izolaci a ochranu před potenciální manipulací s vrstvami, vrstvy nejsou sdíleny mezi registry.

### <a name="manifest"></a>Manifest

Každá bitová kopie kontejneru nebo artefakt zasazený do registru kontejneru je přidružen k *manifestu*. Manifest generovaný registrem při nabízení bitové kopie jednoznačně identifikuje bitovou kopii a určuje její vrstvy. Manifesty úložiště můžete uvést pomocí příkazu Azure CLI [az acr repository manifestů][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Seznam manifestů pro úložiště "acr-helloworld" například uvádí:

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

### <a name="manifest-digest"></a>Manifest digest

Manifesty jsou identifikovány jedinečným hašišem SHA-256 nebo *zjevným výtahem*. Každý obrázek nebo artefakt – ať už označený nebo ne – je identifikován jeho digest. Hodnota digest je jedinečná i v případě, že data vrstvy obrazu jsou shodná s daty jiného obrazu. Tento mechanismus je to, co umožňuje opakovaně tlačit identicky označené obrázky do registru. Můžete například opakovaně nabízeny `myimage:latest` do registru bez chyby, protože každý obrázek je identifikován jeho jedinečný digest.

Bitovou kopii můžete z registru vytáhnout zadáním jeho digest v operaci vyžádat. Některé systémy mohou být nakonfigurovány tak, aby byly vyžádány pomocí výtahu, protože zaručují vytahování verze bitové kopie, i když je identicky označená bitová kopie následně zatlačena do registru.

Například vyžádat obrázek z úložiště "acr-helloworld" podle manifestdigest:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Pokud opakovaně push upravené obrázky s identické značky, můžete vytvořit osamocené obrazy -- obrazy, které jsou netagované, ale stále spotřebovávají místo v registru. Netagované bitové kopie se nezobrazují v azure cli nebo na webu Azure Portal, když zobrazíte nebo zobrazíte bitové kopie podle značky. Jejich vrstvy však stále existují a spotřebovávají místo v registru. Odstraněním netagovaného obrázku uvolníte místo v registru, když je manifest jediný nebo poslední, ukazující na určitou vrstvu. Informace o uvolnění místa používaného netagovanými bitovými kopiemi najdete [v tématu Odstranění ibi kontejnerů v registru kontejnerů Azure](container-registry-delete.md).

## <a name="next-steps"></a>Další kroky

Další informace o [ukládání bitových bitových žerek](container-registry-storage.md) a [podporovaných formátech obsahu](container-registry-image-formats.md) v registru kontejnerů Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


