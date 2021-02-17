---
title: O registrech, úložištích, obrázcích a artefaktech
description: Seznámení se základními koncepty Azure Container Registry, úložišť, imagí kontejnerů a dalších artefaktů.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578116"
---
# <a name="about-registries-repositories-and-artifacts"></a>O registrech, úložištích a artefaktech

Tento článek představuje klíčové koncepty registrů kontejnerů, úložišť a imagí kontejnerů a souvisejících artefaktů. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Registr, úložiště a artefakty":::

## <a name="registry"></a>Registr

*Registr* kontejnerů je služba, která ukládá a distribuuje image kontejneru a související artefakty. Docker Hub je příkladem veřejného registru kontejneru, který slouží jako obecný katalog imagí kontejnerů Docker. Azure Container Registry poskytuje uživatelům přímou kontrolu nad svým obsahem kontejneru s integrovaným ověřováním, [geografickou replikací](container-registry-geo-replication.md) podporující globální distribuci a spolehlivost pro nasazení na konci sítě, [konfiguraci virtuálních sítí s privátním odkazem](container-registry-private-link.md), [uzamykání značek](container-registry-image-lock.md)a mnoho dalších vylepšených funkcí. 

Kromě imagí kontejneru kompatibilního s Docker, Azure Container Registry podporuje řadu [artefaktů obsahu](container-registry-image-formats.md) , včetně grafů Helm a formátů obrázků OCI (Open container Initiative).

## <a name="repository"></a>Repository

*Úložiště* je kolekce imagí kontejneru nebo jiných artefaktů v registru, které mají stejný název, ale různé značky. Například následující tři obrázky jsou v `acr-helloworld` úložišti:

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

## <a name="artifact"></a>Artefakt

Obrázek kontejneru nebo jiný artefakt v rámci registru je přidružen k jedné nebo více značkám, má jednu nebo více vrstev a je identifikován manifestem. Porozumět, jak tyto komponenty vzájemně souvisí, vám můžou efektivně spravovat registr.

### <a name="tag"></a>Značka

*Značka* pro obrázek nebo jiný artefakt určuje jeho verzi. Jednomu artefaktu v rámci úložiště je možné přiřadit jednu nebo více značek a může být také "bez příznaku". To znamená, že můžete odstranit všechny značky z obrázku, zatímco data obrázku (jeho vrstvy) zůstanou v registru.

Úložiště (nebo úložiště a obor názvů) plus značka definuje název obrázku. Můžete vložit a načíst image zadáním jejího názvu v operaci Push nebo Pull. Tato značka `latest` se používá ve výchozím nastavení, pokud ji nezadáte v příkazech Docker.

Způsob, jakým se ve vašich scénářích vytvářejí nebo nasazují image kontejnerů, najdete v příručce. Například stabilní značky jsou doporučeny pro zachování základních imagí a jedinečné značky pro nasazování imagí. Další informace najdete v tématu [doporučení pro označování a správu verzí imagí kontejneru](container-registry-image-tag-version.md).

Pravidla pro pojmenovávání značek najdete v [dokumentaci k Docker](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Vrstva

Image kontejneru a artefakty se skládají z jedné nebo více *vrstev*. Různé typy artefaktů definují různé vrstvy odlišně. Například v imagi kontejneru Docker každá vrstva odpovídá čáře v souboru Dockerfile, která definuje obrázek:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Vrstvy image kontejneru":::

Artefakty v registru sdílejí společné vrstvy a zvyšují efektivitu úložiště. Například několik imagí v různých úložištích může mít společnou ASP.NET Core základní vrstvu, ale v registru je uložena pouze jedna kopie této vrstvy. Sdílení vrstev také optimalizuje distribuci vrstev na uzly s několika artefakty, které sdílejí společné vrstvy. Pokud již obrázek na uzlu obsahuje ASP.NET Core vrstvu jako základní, následné načtení jiného obrázku odkazujícího na stejnou vrstvu nepřenáší vrstvu do uzlu. Místo toho odkazuje na již existující vrstvu na uzlu.

Pro zajištění bezpečné izolace a ochrany před potenciálním manipulací vrstev nejsou vrstvy sdíleny napříč Registry.

### <a name="manifest"></a>Manifest

Každá image kontejneru nebo artefakt nabízený do registru kontejneru je přidružena k *manifestu*. Manifest generovaný registrem při posunutí obsahu, jedinečně identifikuje artefakty a určuje vrstvy. Můžete vytvořit seznam manifestů pro úložiště pomocí příkazu Azure CLI [AZ ACR úložiště show-Manifests][az-acr-repository-show-manifests]. 

Základní manifest pro `hello-world` bitovou kopii systému Linux vypadá podobně jako v následujícím příkladu:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```

Můžete vytvořit seznam manifestů pro úložiště pomocí příkazu Azure CLI [AZ ACR úložiště show-Manifests][az-acr-repository-show-manifests]:

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

Manifesty jsou označeny jedinečnou hodnotou hash SHA-256 nebo *algoritmem Digest manifestu*. Každý obrázek nebo artefakt – bez příznaku nebo not--je identifikován jeho hodnotou Digest. Hodnota algoritmu Digest je jedinečná i v případě, že jsou data vrstvy artefaktů shodná s jiným artefaktem. Tento mechanismus umožňuje opakovaně nabízet identicky označené obrázky do registru. Můžete například opakovaně nabízet `myimage:latest` do registru bez chyby, protože každý obrázek je identifikován jedinečným algoritmem Digest.

Artefakt můžete načíst z registru zadáním jeho výtahu do operace Pull. Některé systémy můžou být nakonfigurované tak, aby vyčetly výtah, protože zaručují, že se verze image načítá, a to i v případě, že se do registru vloží identický označený obrázek.

> [!IMPORTANT]
> Pokud opakovaně přidáváte upravené artefakty s identickými značkami, můžete vytvořit "osamocené" artefakty, které jsou netagované, ale stále spotřebovávat místo v registru. Netagované obrázky se v rozhraní příkazového řádku Azure nebo v Azure Portal nezobrazí, když jsou obrázky podle značky. Nicméně jejich vrstvy stále existují a využívají místo v registru. Odstranění obrázku bez příznaku uvolní místo v registru, když je manifest pouze jeden, nebo poslední z nich odkazuje na konkrétní vrstvu. Informace o uvolnění místa využívaného netagovanými obrázky najdete v tématu [odstranění imagí kontejneru v Azure Container Registry](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Adresování artefaktu

Chcete-li vyřešit artefakt registru pro operace push a pull pomocí Docker nebo jiných klientských nástrojů, zkombinujte plně kvalifikovaný název registru, název úložiště (včetně cesty k oboru názvů, pokud je k dispozici), a značku artefaktu nebo výtah manifestu. Vysvětlení těchto podmínek najdete v předchozích částech.

  **Adresa podle značky**: `[loginServerUrl]/[repository][:tag]`
    
  **Adresa podle Digest**: `[loginServerUrl]/[repository@sha256][:digest]`  

Při použití Docker nebo jiných klientských nástrojů k vyžádání nebo nabízení artefaktů do služby Azure Container Registry použijte plně kvalifikovanou adresu URL registru, která se označuje také jako název *přihlašovacího serveru* . V cloudu Azure má plně kvalifikovanou adresu URL služby Azure Container Registry formát `myregistry.azurecr.io` (malými písmeny).

> [!NOTE]
> * V adrese URL přihlašovacího serveru registru nemůžete zadat číslo portu, jako je například `myregistry.azurecr.io:443` . 
> * Tato značka `latest` se používá ve výchozím nastavení, pokud nezadáte značku do příkazu.  

   
### <a name="push-by-tag"></a>Push podle značky

Příklady: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Vyžádat podle značky

Příklad: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Vyžádat výtah z manifestu


Příklad:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [úložišti registru](container-registry-storage.md) a [podporovaných formátech obsahu](container-registry-image-formats.md) v Azure Container Registry.

Naučte se [načítat a vyžádat obrázky](container-registry-get-started-docker-cli.md) z Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


