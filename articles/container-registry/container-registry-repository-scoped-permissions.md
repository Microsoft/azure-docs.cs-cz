---
title: Oprávnění k úložištím v Azure Container Registry
description: Vytvoření tokenu s oprávněním vymezeným pro konkrétní úložiště v registru pro vyžádání nebo vložení imagí
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2019
ms.author: danlep
ms.openlocfilehash: 7b9d220ac7e507513458eab6b55276b3aa434739
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742746"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Oprávnění vymezená úložiště v Azure Container Registry 

Azure Container Registry podporuje několik [možností ověřování](container-registry-authentication.md) pomocí identit, které mají [přístup na základě rolí](container-registry-roles.md) k celému registru. V některých případech ale možná budete muset zadat přístup jenom ke konkrétním *úložištím* v registru. 

Tento článek ukazuje, jak vytvořit a použít přístupový token, který má oprávnění k provádění akcí pouze v konkrétních úložištích v registru. V případě přístupového tokenu můžete uživatelům nebo službám poskytnout vymezený, časově omezený přístup k úložištím a vyžádat si obrázky nebo provádět jiné akce. 

Další informace o konceptech a scénářích tokenů najdete v tématu [o oprávněních v oboru úložiště](#about-repository-scoped-permissions), dále v tomto článku.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a [platí některá omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení verze Preview

* Tato funkce je dostupná jenom v registru kontejnerů **Premium** . Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry SKU](container-registry-skus.md).
* V současné době nemůžete přiřadit oprávnění vymezená úložiště k objektu Azure Active Directory, jako je například instanční objekt nebo spravovaná identita.

## <a name="prerequisites"></a>Požadavky

* **Azure CLI** – Tento článek vyžaduje místní instalaci rozhraní příkazového řádku Azure CLI (verze 2.0.76 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).
* **Docker** – k ověření pomocí registru budete potřebovat taky místní instalaci Docker. Docker poskytuje pokyny k instalaci pro systémy [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registr kontejneru s úložištěmi** – Pokud ho nemáte, vytvořte v předplatném Azure registr kontejnerů. Použijte například [Azure Portal](container-registry-get-started-portal.md) nebo rozhraní příkazového [řádku Azure CLI](container-registry-get-started-azure-cli.md). 

  Pro účely testování [dosaďte](container-registry-get-started-docker-cli.md) nebo [importujte](container-registry-import-images.md) jednu nebo více ukázkových imagí do registru. Příklady v tomto článku se týkají následujících imagí ve dvou úložištích: `samples/hello-world:v1` a `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Vytvoření přístupového tokenu

Pomocí příkazu [AZ ACR token Create][az-acr-token-create] vytvořte token. Při vytváření tokenu zadejte jedno nebo více úložišť a přidružených akcí pro každé úložiště, nebo zadejte existující mapu oboru s těmito nastaveními.

### <a name="create-access-token-and-specify-repositories"></a>Vytvoření přístupového tokenu a určení úložišť

Následující příklad vytvoří přístupový token s oprávněními k provádění akcí `content/write` a `content/read` v úložišti `samples/hello-world` a `content/read` akci v úložišti `samples/nginx`. Ve výchozím nastavení příkaz vygeneruje dvě hesla. 

V tomto příkladu se nastaví stav tokenu na `enabled` (výchozí nastavení), ale můžete token kdykoli aktualizovat a nastavit stav na `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

Výstup zobrazuje podrobnosti o tokenu, včetně generovaných hesel a mapování oboru. Doporučuje se ukládat hesla na bezpečném místě pro pozdější použití s `docker login`. Hesla se znovu nedají načíst, ale můžou se vygenerovat nové.

Výstup také ukazuje, že se automaticky vytvoří mapa oboru s názvem `MyToken-scope-map`. Můžete použít mapu oboru a použít stejné akce úložiště pro jiné tokeny. Nebo aktualizujte mapu oboru později, abyste změnili oprávnění tokenu.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

### <a name="create-a-scope-map-and-associated-token"></a>Vytvoření mapy oboru a přidruženého tokenu

Případně můžete při vytváření tokenu zadat mapu oboru s úložištěmi a přidruženými akcemi. Chcete-li vytvořit mapu oboru, použijte příkaz [AZ ACR Scope-map Create][az-acr-scope-map-create] .

Následující příklad příkazu Vytvoří mapu oboru se stejnými oprávněními použitými v předchozím příkladu. Povoluje `content/write` a `content/read` akce v úložišti `samples/hello-world` a `content/read` akci v úložišti `samples/nginx`:

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

Výstup je podobný tomuto:

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

Spuštěním [AZ ACR token Create][az-acr-token-create] vytvořte token přidružený k mapě oboru *MyScopeMap* . Ve výchozím nastavení příkaz vygeneruje dvě hesla. V tomto příkladu se nastaví stav tokenu na `enabled` (výchozí nastavení), ale můžete token kdykoli aktualizovat a nastavit stav na `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

Výstup zobrazuje podrobnosti o tokenu, včetně generovaných hesel a mapování oboru, které jste použili. Doporučuje se ukládat hesla na bezpečném místě pro pozdější použití s `docker login`. Hesla se znovu nedají načíst, ale můžou se vygenerovat nové.

## <a name="generate-passwords-for-token"></a>Generovat hesla pro token

Pokud byla při vytváření tokenu vytvořena hesla, pokračujte [v ověřování pomocí registru](#authenticate-using-token).

Pokud nemáte heslo tokenu nebo chcete generovat nová hesla, spusťte příkaz [AZ ACR token Credential Generate][az-acr-token-credential-generate] .

Následující příklad vygeneruje nové heslo pro vytvořený token, jehož doba platnosti je 30 dní. Heslo ukládá do proměnné prostředí TOKEN_PWD. Tento příklad je naformátován pro prostředí bash shell.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Ověřování pomocí tokenu

Spusťte `docker login` k ověření pomocí registru pomocí přihlašovacích údajů tokenu. Jako uživatelské jméno zadejte název tokenu a zadejte jedno z jeho hesel. Následující příklad je naformátován pro prostředí bash a poskytuje hodnoty pomocí proměnných prostředí.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Výstup by měl zobrazit úspěšné ověření:

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Ověření oboru přístupu

Můžete ověřit, že token poskytuje vymezená oprávnění pro úložiště v registru. V tomto příkladu se úspěšně dokončí následující příkazy `docker pull`, aby vyčetly image dostupné v úložištích `samples/hello-world` a `samples/nginx`:

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Vzhledem k tomu, že vzorový token umožňuje `content/write` akci pouze v úložišti `samples/hello-world`, `docker push` k tomuto úložišti, ale pro `samples/nginx`selže:

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Aktualizovat mapu a token oboru

Pokud chcete aktualizovat oprávnění tokenu, aktualizujte oprávnění v přidružené mapě oboru pomocí [AZ ACR Scope-map Update][az-acr-scope-map-update]. Pokud například chcete aktualizovat *MyScopeMap* a odebrat akci `content/write` v úložišti `samples/hello-world`:

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Pokud je mapa oboru přidružena k více než jednomu tokenu, příkaz aktualizuje oprávnění všech přidružených tokenů.

Pokud chcete aktualizovat token s jinou mapou oboru, spusťte příkaz [AZ ACR token Update][az-acr-token-update]. Příklad:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Po aktualizaci tokenu nebo mapování oboru přidruženého k tokenu se změny oprávnění projeví při příštím `docker login` nebo jiném ověřování pomocí tokenu.

Po aktualizaci tokenu můžete vytvořit nová hesla pro přístup k registru. Spusťte příkaz [AZ ACR token Credential Generate][az-acr-token-credential-generate]. Příklad:

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>O oprávněních s rozsahem úložiště

### <a name="concepts"></a>Koncepty

Pokud chcete nakonfigurovat oprávnění s rozsahem úložiště, vytvořte *přístupový token* a přidruženou *mapu oboru* pomocí příkazů v Azure CLI.

* **Přístupový token** je přihlašovací údaje, které se používají s heslem k ověřování pomocí registru. Přidružená ke každému tokenu jsou povolené *Akce* oboru pro jedno nebo více úložišť. Pro každý token můžete nastavit čas vypršení platnosti. 

* **Akce** u každého zadaného úložiště zahrnuje jednu nebo více z následujících možností.

  |Akce  |Popis  |
  |---------|---------|
  |`content/read`     |  Načte data z úložiště. Můžete například načíst artefakt.  |
  |`metadata/read`    | Načte metadata z úložiště. Například seznam značek nebo zobrazit metadata manifestu.   |
  |`content/write`     |  Zapište data do úložiště. Pomocí `content/read` můžete odeslat artefakt.    |
  |`metadata/write`     |  Zapsat metadata do úložiště. Například aktualizujte atributy manifestu.  |
  |`content/delete`    | Odeberte data z úložiště. Například odstraňte úložiště nebo manifest. |

* **Mapa oboru** je objekt registru, který seskupuje oprávnění úložiště, která použijete u tokenu, nebo se může znovu vztahovat na jiné tokeny. Pokud při vytváření tokenu nepoužijete mapu oboru, vytvoří se pro vás automaticky mapa oboru, abyste mohli uložit nastavení oprávnění. 

  Mapa oboru vám pomůže nakonfigurovat více uživatelů se shodným přístupem k sadě úložišť. Azure Container Registry taky poskytuje systémem definované mapy oborů, které můžete použít při vytváření přístupových tokenů.

Následující obrázek shrnuje vztah mezi tokeny a mapami rozsahu. 

![Mapy a tokeny rozsahu registru](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Scénáře

Mezi scénáře použití přístupového tokenu patří:

* Poskytněte zařízení IoT s jednotlivými tokeny k získání image z úložiště.
* Poskytněte externí organizaci oprávnění ke konkrétnímu úložišti. 
* Omezte přístup k úložišti pro konkrétní skupiny uživatelů ve vaší organizaci. Poskytněte třeba přístup pro zápis a čtení vývojářům, kteří vytvářejí image, které cílí na konkrétní úložiště, a přístup pro čtení týmů, které z těchto úložišť nasazují.

### <a name="authentication-using-token"></a>Ověřování pomocí tokenu

Pro ověření pomocí cílového registru použijte název tokenu jako uživatelské jméno a jedno z jeho přidružených hesel. Metoda ověřování závisí na nakonfigurovaných akcích.

### <a name="contentread-or-contentwrite"></a>obsah/čtení nebo obsah/zápis

Pokud token povoluje pouze `content/read` nebo `content/write` akce, poskytněte přihlašovací údaje tokenu v jednom z následujících toků ověřování:

* Ověřování pomocí Docker pomocí `docker login`
* Ověřování pomocí registru pomocí příkazu [AZ ACR Login][az-acr-login] v rozhraní příkazového řádku Azure

Po ověření token povoluje nakonfigurované akce v oboru úložiště nebo úložišť s vymezeným oborem. Pokud například token povolí akci `content/read` v úložišti, jsou na obrázcích v tomto úložišti povoleny `docker pull` operace.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>metadata/čtení, metadata/zápis, nebo obsah/odstranění

Pokud token povoluje `metadata/read`, `metadata/write`nebo `content/delete` akcí v úložišti, musí se v rámci souvisejících příkazů [AZ ACR úložištì][az-acr-repository] v Azure CLI zadat přihlašovací údaje tokenu.

Pokud je například `metadata/read` akcí povoleno v úložišti, předejte přihlašovací údaje tokenu při spuštění příkazu [AZ ACR úložiště show-Tags][az-acr-repository-show-tags] k vypsání značek.

## <a name="next-steps"></a>Další kroky

* Pokud chcete spravovat mapy oboru a přístupové tokeny, použijte další příkazy v příkazu [AZ ACR Scope-map][az-acr-scope-map] a [AZ ACR token][az-acr-token] groups.
* V tématu [Přehled ověřování](container-registry-authentication.md) pro scénáře ověřování pomocí služby Azure Container Registry použijte účet správce nebo Azure Active Directory identitu.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
