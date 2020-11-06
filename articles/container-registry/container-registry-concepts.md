---
title: O úložištích & imagí
description: Seznámení se základními koncepty Azure Container Registry, úložišť a imagí kontejnerů.
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: cd2f93c119817c722401f7290064894f3d39dac9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335890"
---
# <a name="about-registries-repositories-and-images"></a>O registrech, úložištích a obrázcích

Tento článek představuje klíčové koncepty registrů kontejnerů, úložišť a imagí kontejnerů a souvisejících artefaktů. 

## <a name="registry"></a>Registr

*Registr* kontejnerů je služba, která ukládá a distribuuje image kontejneru. Docker Hub je veřejný registr kontejnerů, který podporuje open source komunitu a slouží jako obecný katalog imagí. Azure Container Registry poskytuje uživatelům přímou kontrolu nad svými obrázky s integrovaným ověřováním, [geografickou replikací](container-registry-geo-replication.md) podporující globální distribuci a spolehlivost pro nasazení v rámci sítě, [konfiguraci virtuálních sítí a brány firewall](container-registry-vnet.md), [uzamykání značek](container-registry-image-lock.md)a mnoho dalších vylepšených funkcí. 

Kromě imagí kontejneru Docker Azure Container Registry podporuje související [artefakty obsahu](container-registry-image-formats.md) včetně formátů obrázků OCI (Open container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Obsah s adresovatelnými prvky artefaktu

Adresa artefaktu ve službě Azure Container Registry obsahuje následující prvky. 

`[loginUrl]/[repository:][tag]`

* **loginUrl** – plně kvalifikovaný název hostitele registru. Hostitel registru ve službě Azure Container Registry má formát *myregistry*. azurecr.IO (všechna malá písmena). Je nutné zadat loginUrl při použití Docker nebo jiných klientských nástrojů pro vyžádání nebo nabízení artefaktů do služby Azure Container Registry. 
* **úložiště** – název logického seskupení jednoho nebo více souvisejících imagí nebo artefaktů, například obrázky pro aplikaci nebo základní operační systém. Může zahrnovat cestu k *oboru názvů* . 
* identifikátor **označení** konkrétní verze obrázku nebo artefaktu uloženého v úložišti.

Například úplný název obrázku ve službě Azure Container Registry může vypadat takto:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Podrobnosti o těchto prvcích najdete v následujících oddílech.

## <a name="repository-name"></a>Název úložiště

*Úložiště* je kolekce imagí kontejneru nebo jiných artefaktů se stejným názvem, ale s různými značkami. Například následující tři obrázky jsou v úložišti "ACR-HelloWorld":


- *ACR-HelloWorld: nejnovější*
- *ACR-HelloWorld: V1*
- *ACR-HelloWorld: v2*

Názvy úložišť můžou zahrnovat taky [obory názvů](container-registry-best-practices.md#repository-namespaces). Obory názvů umožňují identifikovat související úložiště a vlastnictví artefaktů ve vaší organizaci pomocí názvů s oddělenými lomítky. Registr ale spravuje všechna úložiště nezávisle, nikoli jako hierarchii. Příklad:

- *Marketing/campaign10-18/web: v2*
- *Marketing/campaign10 – 18/API: V3*
- *Marketing/campaign10-18/e-mail – odesilatel: v2*
- *produkt – vrácení a odeslání na webu: 20180604*
- *produkt – vrátí nebo starší integrátor: 20180715*

Názvy úložišť můžou obsahovat jenom malé alfanumerické znaky, tečky, pomlčky, podtržítka a lomítka. 

Úplná pravidla pojmenování úložiště najdete v tématu věnovaném [specifikaci distribuce pro iniciativu](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)v rámci služby Open container.

## <a name="image"></a>Image

Obrázek kontejneru nebo jiný artefakt v rámci registru je přidružen k jedné nebo více značkám, má jednu nebo více vrstev a je identifikován manifestem. Porozumět, jak tyto komponenty vzájemně souvisí, vám můžou efektivně spravovat registr.

### <a name="tag"></a>Značka

*Značka* pro obrázek nebo jiný artefakt určuje jeho verzi. Jednomu artefaktu v rámci úložiště je možné přiřadit jednu nebo více značek a může být také "bez příznaku". To znamená, že můžete odstranit všechny značky z obrázku, zatímco data obrázku (jeho vrstvy) zůstanou v registru.

Úložiště (nebo úložiště a obor názvů) plus značka definuje název obrázku. Můžete vložit a načíst image zadáním jejího názvu v operaci Push nebo Pull. Tato značka `latest` se používá ve výchozím nastavení, pokud ji nezadáte v příkazech Docker.

Způsob, jakým se ve vašich scénářích vytvářejí nebo nasazují image kontejnerů, najdete v příručce. Například stabilní značky jsou doporučeny pro zachování základních imagí a jedinečné značky pro nasazování imagí. Další informace najdete v tématu [doporučení pro označování a správu verzí imagí kontejneru](container-registry-image-tag-version.md).

Pravidla pro pojmenovávání značek najdete v [dokumentaci k Docker](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Vrstva

Image kontejneru se skládají z jedné nebo více *vrstev* , z nichž každá odpovídá čáře v souboru Dockerfile, která definuje obrázek. Image v registru sdílejí společné vrstvy a zvyšují efektivitu úložiště. Například několik imagí v různých úložištích může sdílet stejnou základní vrstvu systému Alpine Linux, ale v registru je uložena pouze jedna kopie této vrstvy.

Sdílení vrstev také optimalizuje distribuci vrstev na uzly s více obrázky, které sdílejí společné vrstvy. Například pokud obrázek již na uzlu obsahuje jako základ vrstvu Alpine Linux, následné načtení jiného obrázku odkazujícího na stejnou vrstvu nepřenáší vrstvu do uzlu. Místo toho odkazuje na již existující vrstvu na uzlu.

Pro zajištění bezpečné izolace a ochrany před potenciálním manipulací vrstev nejsou vrstvy sdíleny napříč Registry.

### <a name="manifest"></a>Manifest

Každá image kontejneru nebo artefakt nabízený do registru kontejneru je přidružena k *manifestu*. Manifest generovaný registrem při posunutí obrázku, jedinečně identifikuje obrázek a určuje jeho vrstvy. Můžete vytvořit seznam manifestů pro úložiště pomocí příkazu Azure CLI [AZ ACR úložiště show-Manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Seznamte se například s manifesty pro úložiště "ACR-HelloWorld":

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

### <a name="manifest-digest"></a>Výtah manifestu

Manifesty jsou označeny jedinečnou hodnotou hash SHA-256 nebo *algoritmem Digest manifestu*. Každý obrázek nebo artefakt – bez příznaku nebo not--je identifikován jeho hodnotou Digest. Hodnota Digest je jedinečná i v případě, že jsou data vrstev obrázku shodná s jinou imagí. Tento mechanismus umožňuje opakovaně nabízet identicky označené obrázky do registru. Můžete například opakovaně nabízet `myimage:latest` do registru bez chyby, protože každý obrázek je identifikován jedinečným algoritmem Digest.

Můžete načíst obrázek z registru zadáním jeho výtahu do operace Pull. Některé systémy můžou být nakonfigurované tak, aby vyčetly výtah, protože zaručují, že se verze image načítá, a to i v případě, že se následně do registru vloží identický označený obrázek.

Můžete například načíst obrázek z úložiště ACR-HelloWorld pomocí výtahu manifestu:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Pokud opakovaně nahrajete upravené image s identickými značkami, můžete vytvářet osamocené bitové kopie – obrázky, které jsou netagované, ale stále využívají místo v registru. Netagované obrázky se v rozhraní příkazového řádku Azure nebo v Azure Portal nezobrazí, když jsou obrázky podle značky. Nicméně jejich vrstvy stále existují a využívají místo v registru. Odstranění obrázku bez příznaku uvolní místo v registru, když je manifest pouze jeden, nebo poslední z nich odkazuje na konkrétní vrstvu. Informace o uvolnění místa využívaného netagovanými obrázky najdete v tématu [odstranění imagí kontejneru v Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Další kroky

Další informace o [úložišti imagí](container-registry-storage.md) a [podporovaných formátech obsahu](container-registry-image-formats.md) v Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


