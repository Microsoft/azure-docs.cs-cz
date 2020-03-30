---
title: Push and pull OCI artefakt
description: Nabízený a vyprovokující artefakty iniciativy Open Container Initiative (OCI) pomocí registru privátního kontejneru v Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371048"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Nabízení a vytahování artefaktu OCI pomocí registru kontejnerů Azure

Pomocí registru kontejnerů Azure můžete ukládat a spravovat [artefakty OCI (Open Container Initiative)](container-registry-image-formats.md#oci-artifacts) a také i image kontejnerů kompatibilních s Dockerem.

Chcete-li demonstrovat tuto možnost, tento článek ukazuje, jak použít [nástroj Registru OCI jako úložiště (ORAS)](https://github.com/deislabs/oras) k nabízení ukázkového artefaktu – textového souboru – do registru kontejnerů Azure. Potom naváděte artefakt z registru. Můžete spravovat různé artefakty OCI v registru kontejneru Azure pomocí různých nástrojů příkazového řádku vhodné pro každý artefakt.

## <a name="prerequisites"></a>Požadavky

* **Registr kontejnerů Azure** – Vytvořte registr kontejnerů ve svém předplatném Azure. Například použijte [portál Azure](container-registry-get-started-portal.md) nebo [Azure CLI](container-registry-get-started-azure-cli.md).
* **Nástroj ORAS** - Stáhněte a nainstalujte aktuální verzi ORAS pro váš operační systém z [úložiště GitHub](https://github.com/deislabs/oras/releases). Nástroj se uvolní jako komprimovaný`.tar.gz` tarball ( soubor). Extrahujte a nainstalujte soubor pomocí standardních postupů pro operační systém.
* **Instanční objekt služby Azure Active Directory (volitelné)** – Chcete-li ověřit přímo s ORAS, vytvořte [instanční objekt](container-registry-auth-service-principal.md) pro přístup k registru. Ujistěte se, že instanční objekt je přiřazen a role, jako je Například AcrPush tak, aby má oprávnění k nabízení a vyžádat artefakty.
* **Azure CLI (volitelné)** – chcete-li použít individuální identitu, potřebujete místní instalaci příkazového příkazu k onomu Azure. Doporučuje se verze 2.0.71 nebo novější. Spuštěním `az --version `najděte verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).
* **Docker (volitelné)** - Chcete-li použít individuální identitu, musíte mít také Docker nainstalován místně, k ověření s registrem. Docker nabízí balíčky pro snadnou konfiguraci Dockeru v libovolném systému [macOS][docker-mac], [Windows][docker-windows] nebo [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Přihlášení k registru

Tato část zobrazuje dva navrhované pracovní postupy pro přihlášení do registru v závislosti na použité identitě. Zvolte metodu vhodnou pro vaše prostředí.

### <a name="sign-in-with-oras"></a>Přihlášení pomocí orasu

Pomocí [instančního objektu](container-registry-auth-service-principal.md) `oras login` s nabízenými právy spusťte příkaz pro přihlášení k registru pomocí ID a hesla instančního objektu služby. Zadejte úplný název registru (všechna malá písmena), v tomto případě *myregistry.azurecr.io*. ID aplikace instančního objektu `$SP_APP_ID`je předáno v `$SP_PASSWD`proměnné prostředí a heslo v proměnné .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Chcete-li si přečíst heslo `--password-stdin`od společnosti Stdin, použijte .

### <a name="sign-in-with-azure-cli"></a>Přihlášení pomocí Azure CLI

[Přihlaste](/cli/azure/authenticate-azure-cli) se k Azure CLI s vaší identitou pro nabízení a vyžádat artefakty z registru kontejneru.

Potom použijte příkaz Azure CLI [az az acr přihlášení](/cli/azure/acr?view=azure-cli-latest#az-acr-login) pro přístup k registru. Například k ověření do registru s názvem *myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`pomocí klienta Dockeru nastaví token `docker.config` Azure Active Directory v souboru. Klient Dockeru musí být nainstalován a spuštěn, aby dokončil tok individuálního ověřování.

## <a name="push-an-artifact"></a>Push artefakt

Vytvořte textový soubor v místním pracovním adresáři s ukázkovým textem. Například v bash shell:

```bash
echo "Here is an artifact!" > artifact.txt
```

Pomocí `oras push` příkazu můžete tento textový soubor převést do registru. Následující příklad odešle ukázkový `samples/artifact` textový soubor do repo. Registr je identifikován plně kvalifikovanýnázev registru *myregistry.azurecr.io* (všechna malá písmena). Artefakt je označen `1.0`. Artefakt má ve výchozím nastavení nedefinovaný typ identifikovaný řetězcem typu `artifact.txt` *média* za názvem souboru . Další typy najdete v [tématu Artefakty OCI.](https://github.com/opencontainers/artifacts) 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Výstup pro úspěšné nabízení je podobný následujícímu:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Chcete-li spravovat artefakty v registru, pokud používáte `az acr` Azure CLI, spusťte standardní příkazy pro správu ibi. Například získat atributy artefaktu pomocí příkazu [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

Výstup je podobný tomuto:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Vytáhnout artefakt

Spusťte `oras pull` příkaz k vytažení artefaktu z registru.

Nejprve odeberte textový soubor z místního pracovního adresáře:

```bash
rm artifact.txt
```

Spuštěním `oras pull` vytáhnete artefakt a zadejte typ média použitý k nabízení artefaktu:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Ověřte, zda byla vyžádat úspěšnou:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Odebrání artefaktu (volitelné)

Chcete-li artefakt odebrat z registru kontejnerů Azure, použijte příkaz [delete úložiště az acr.][az-acr-repository-delete] Následující příklad odebere artefakt, který jste tam uložili:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Další kroky

* Další informace [o knihovně ORAS](https://github.com/deislabs/oras/tree/master/docs), včetně konfigurace manifestu pro artefakt
* Navštivte úložiště [artefaktů OCI,](https://github.com/opencontainers/artifacts) kde najdete referenční informace o nových typech artefaktů.



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
