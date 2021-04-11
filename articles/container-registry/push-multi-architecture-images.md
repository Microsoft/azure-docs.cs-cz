---
title: Obrázky s více architekturami v registru
description: Použití služby Azure Container Registry k vytváření, importování, ukládání a nasazování imagí s více architekturami (multi-oblouky)
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: 520518a275e0a9d191ae770e560a0e6e8278eae2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802450"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Obrázky s více architekturami ve službě Azure Container Registry

V tomto článku se seznámíte s *více architekturami* a dozvíte se, jak můžete pomocí Azure Container Registrych funkcí vytvořit, Uložit a používat je. 

Vícestránkový obrázek je typ image kontejneru, který může kombinovat varianty pro různé architektury a někdy pro různé operační systémy. Při spuštění image s podporou více architektur budou klienti kontejnerů automaticky vybírat variantu image, která odpovídá vašemu operačnímu systému a architektuře.

## <a name="manifests-and-manifest-lists"></a>Manifesty a seznamy manifestů

Vícestránkové obrázky jsou založené na manifestech obrázků a seznamech manifestů.

### <a name="manifest"></a>Manifest

Jednotlivé image kontejneru jsou reprezentovány [manifestem](container-registry-concepts.md#manifest). Manifest je soubor JSON, který jedinečně identifikuje obrázek a odkazuje na jeho vrstvy a jejich odpovídající velikosti. 

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
    
Manifest v Azure Container Registry můžete zobrazit pomocí Azure Portal nebo nástrojů, jako je příkaz [AZ ACR úložištì show-Manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) in the Azure CLI.

### <a name="manifest-list"></a>Seznam manifestů

*Seznam manifestů* pro vícestránkové obrázky (obecně označované jako [index obrázku](https://github.com/opencontainers/image-spec/blob/master/image-index.md) pro Image OCI) je kolekce (index) imagí a Vy ji vytvoříte zadáním jednoho nebo více názvů obrázků. Obsahuje podrobnosti o jednotlivých obrázcích, jako je například podporovaný operační systém a architektura, velikost a výtah manifestu. Seznam manifest lze použít stejným způsobem jako název obrázku v `docker pull` `docker run` příkazech a. 

Rozhraní `docker` příkazového řádku spravuje manifesty a seznamy manifestu pomocí příkazu [Docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) .

> [!NOTE]
> V současné době je `docker manifest` příkaz a dílčí příkazy experimentální. Podrobnosti o použití experimentálních příkazů najdete v dokumentaci k Docker.

Seznam manifestu můžete zobrazit pomocí `docker manifest inspect` příkazu. Následuje výstup pro vícestránkový obraz `mcr.microsoft.com/mcr/hello-world:latest` , který má tři manifesty: dvě pro architektury operačního systému Linux a jeden pro architekturu Windows.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Pokud je v Azure Container Registry uložený seznam manifestů s více podmnožinami, můžete si také zobrazit seznam manifestů pomocí Azure Portal nebo pomocí nástrojů, jako je příkaz [AZ ACR úložištì show-Manifests](/cli/azure/acr/repository#az_acr_repository_how_manifests) .

## <a name="import-a-multi-arch-image"></a>Import vícestránkového obrázku 

Pomocí příkazu [AZ ACR import](/cli/azure/acr#az_acr_import) lze do služby Azure Container Registry importovat existující vícestránkový obrázek. Syntaxe importu obrázků je stejná jako u bitové kopie s jednou architekturou. Podobně jako při importu bitové kopie s jednou architekturou nepoužívá import více imagí příkazy Docker. 

Podrobnosti najdete v tématu [Import imagí kontejneru do registru kontejneru](container-registry-import-images.md).

## <a name="push-a-multi-arch-image"></a>Vložení vícestránkového obrázku

Když máte pracovní postupy sestavení pro vytváření imagí kontejnerů pro různé architektury, postupujte podle těchto kroků a nahrajte do služby Azure Container Registry více imagí.

1. Označte a nahrajte image každého konkrétní architektury do svého registru kontejneru. Následující příklad předpokládá dvě architektury Linux: arm64 a amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Spusťte `docker manifest create` , chcete-li vytvořit seznam manifestů pro kombinování předchozích imagí do obrázku s více podmnožinami.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Nahrajte manifest do svého registru kontejneru pomocí `docker manifest push` :

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Pomocí `docker manifest inspect` příkazu zobrazte seznam manifest. Příklad výstupu příkazu je zobrazen v předchozí části.

Po nahrání víceúrovňového manifestu do registru Pracujte s obrázkem s více podmnožinami stejným způsobem jako u bitové kopie s jednou architekturou. Můžete například načíst Image pomocí `docker pull` příkazu [AZ ACR úložiště](/cli/azure/acr/repository#az_acr_repository) a zobrazit značky, manifesty a další vlastnosti obrázku.

## <a name="build-and-push-a-multi-arch-image"></a>Sestavení a vložení obrázku s více podmnožinami

Pomocí funkcí [úloh ACR](container-registry-tasks-overview.md)můžete vytvořit a vložit více imagí do služby Azure Container Registry. Například definujte [úlohu s více kroky](container-registry-tasks-multi-step.md) v souboru YAML, která vytvoří obrázek pro více než Linux.

Následující příklad předpokládá, že máte samostatné fázemi pro dvě architektury, arm64 a amd64. Sestaví a vloží image pro konkrétní architekturu a potom vytvoří a vloží manifest s více podmnožinami, který má `latest` značku:

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Další kroky

* Použijte [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) k sestavení imagí kontejneru pro různé architektury.
* Seznamte se s vytvářením imagí pro víc platforem pomocí experimentálního modulu plug-in Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) .

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
