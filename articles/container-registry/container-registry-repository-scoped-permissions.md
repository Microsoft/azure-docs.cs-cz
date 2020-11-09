---
title: Oprávnění k úložištím v Azure Container Registry
description: Vytvoření tokenu s oprávněním vymezeným pro konkrétní úložiště v registru Premium pro vyžádání nebo vložení obrázků nebo provádění dalších akcí
ms.topic: article
ms.date: 05/27/2020
ms.openlocfilehash: b65b1bf69337cb172a17043490a5d13c7bd7afc2
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381231"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Vytvoření tokenu s oprávněními vymezenými úložištěm

Tento článek popisuje, jak vytvořit tokeny a mapy oboru pro správu oprávnění vymezeného úložiště v registru kontejnerů. Díky vytvoření tokenů může vlastník registru poskytnout uživatelům nebo službám s vymezeným časem omezený přístup k úložištím a vyžádat si obrázky nebo provádět jiné akce. Token poskytuje přesnější oprávnění než jiné [Možnosti ověřování](container-registry-authentication.md)registru, které mají rozsah oprávnění k celému registru. 

Mezi scénáře vytvoření tokenu patří:

* Umožňuje zařízením IoT s jednotlivými tokeny načíst image z úložiště.
* Poskytněte externí organizaci oprávnění ke konkrétnímu úložišti. 
* Omezte přístup k úložišti pro různé skupiny uživatelů ve vaší organizaci. Poskytněte třeba přístup pro zápis a čtení vývojářům, kteří vytvářejí image, které cílí na konkrétní úložiště, a přístup pro čtení týmů, které z těchto úložišť nasazují.

Tato funkce je k dispozici na úrovni služby Registry kontejneru **Premium** . Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně služeb](container-registry-skus.md).

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a [platí některá omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení verze Preview

* V současné době nemůžete přiřazovat oprávnění rozsahu úložiště k identitě Azure Active Directory, jako je instanční objekt nebo spravovaná identita.
* Nemůžete vytvořit mapování oboru v registru s povoleným [anonymním přístupem pro vyžádání](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)obsahu.

## <a name="concepts"></a>Koncepty

Chcete-li nakonfigurovat oprávnění s rozsahem úložiště, vytvořte *token* s přidruženou *mapou oboru*. 

* **Token** společně s generovaným heslem umožňuje uživateli ověření pomocí registru. Můžete nastavit datum vypršení platnosti pro heslo tokenu nebo kdykoli zakázat token.  

  Po ověření pomocí tokenu může uživatel nebo služba provádět jednu nebo více *akcí* v oboru jednoho nebo více úložišť.

  |Akce  |Popis  | Příklad |
  |---------|---------|--------|
  |`content/delete`    | Odebrání dat z úložiště  | Odstranění úložiště nebo manifestu |
  |`content/read`     |  Čtení dat z úložiště |  Načíst artefakt |
  |`content/write`     |  Zápis dat do úložiště     | Použití s `content/read` pro k nahrání artefaktu |
  |`metadata/read`    | Čtení metadat z úložiště   | Vypsat značky nebo manifesty |
  |`metadata/write`     |  Zápis metadat do úložiště  | Povolení nebo zakázání operací čtení, zápisu nebo odstranění |

* **Mapování oboru** seskupí oprávnění úložiště, která použijete pro token, a můžete je znovu použít na jiné tokeny. Každý token je přidružen k jedné mapě oboru. 

   S mapou oboru:

    * Konfigurace více tokenů se stejnými oprávněními pro sadu úložišť
    * Aktualizovat oprávnění tokenu při přidávání nebo odebírání akcí úložiště v mapě oboru nebo použití jiné mapy oboru 

  Azure Container Registry taky poskytuje několik systémových mapování oborů, která můžete použít při vytváření tokenů. Oprávnění mapování oborů definovaných systémem se vztahují na všechna úložiště v registru.

Následující obrázek znázorňuje vztah mezi tokeny a mapami rozsahu. 

![Tokeny registru a mapy oborů](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Požadavky

* **Azure CLI** – příkazy rozhraní příkazového řádku Azure pro vytváření a správu tokenů jsou k dispozici v Azure CLI verze 2.0.76 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).
* **Docker** – k ověření pomocí registru pro vyžádání nebo vložení imagí potřebujete místní instalaci Docker. Docker poskytuje pokyny k instalaci pro systémy [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registr kontejneru** – Pokud ho nemáte, vytvořte v předplatném Azure službu Premium Container registry nebo upgradujte existující registr. Použijte například [Azure Portal](container-registry-get-started-portal.md) nebo rozhraní příkazového [řádku Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Vytvořit token – rozhraní příkazového řádku

### <a name="create-token-and-specify-repositories"></a>Vytvoření tokenu a určení úložišť

Pomocí příkazu [AZ ACR token Create][az-acr-token-create] vytvořte token. Při vytváření tokenu můžete zadat jedno nebo více úložišť a přidružených akcí pro každé úložiště. Úložiště ještě nemusí být v registru. Pokud chcete vytvořit token zadáním existující mapy oboru, přečtěte si [Další část](#create-token-and-specify-scope-map).

Následující příklad vytvoří v registru token *myregistry* s následujícími oprávněními pro `samples/hello-world` úložiště: `content/write` a `content/read` . Ve výchozím nastavení příkaz nastaví výchozí stav tokenu na `enabled` , ale stav můžete kdykoli aktualizovat `disabled` .

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

Výstup zobrazuje podrobnosti o tokenu. Ve výchozím nastavení se generují dvě hesla. Doporučuje se ukládat hesla na bezpečném místě, aby je bylo možné později použít k ověřování. Hesla se znovu nedají načíst, ale můžou se vygenerovat nové.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
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

> [!NOTE]
> Pokud chcete znovu vygenerovat hesla tokenu a nastavit dobu vypršení platnosti hesla, přečtěte si téma [obnovení hesla k tokenům](#regenerate-token-passwords) dále v tomto článku.

Výstup obsahuje podrobnosti o mapování oboru, který příkaz vytvořil. `MyToken-scope-map`K použití stejných akcí v úložišti pro jiné tokeny můžete použít mapu rozsahu, který je zde pojmenován. Nebo aktualizujte mapování oboru později a změňte oprávnění přidružených tokenů.

### <a name="create-token-and-specify-scope-map"></a>Vytvořit token a zadat mapu oboru

Alternativním způsobem, jak vytvořit token, je zadat existující mapu oboru. Pokud ještě nemáte mapu oboru, nejprve ji vytvořte zadáním úložišť a přidružených akcí. Pak při vytváření tokenu zadejte mapu oboru. 

Chcete-li vytvořit mapu oboru, použijte příkaz [AZ ACR Scope-map Create][az-acr-scope-map-create] . Následující příkaz vytvoří mapu oboru se stejnými oprávněními k `samples/hello-world` dříve použitému úložišti. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Spuštěním [AZ ACR token Create][az-acr-token-create] vytvořte token a určete mapu oboru *MyScopeMap* . Jak je uvedeno v předchozím příkladu, příkaz nastaví výchozí stav tokenu na `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

Výstup zobrazuje podrobnosti o tokenu. Ve výchozím nastavení se generují dvě hesla. Doporučuje se ukládat hesla na bezpečném místě, aby je bylo možné později použít k ověřování. Hesla se znovu nedají načíst, ale můžou se vygenerovat nové.

> [!NOTE]
> Pokud chcete znovu vygenerovat hesla tokenu a nastavit dobu vypršení platnosti hesla, přečtěte si téma [obnovení hesla k tokenům](#regenerate-token-passwords) dále v tomto článku.

## <a name="create-token---portal"></a>Vytvoření tokenu – portál

Pomocí Azure Portal můžete vytvořit tokeny a mapy oborů. Stejně jako u `az acr token create` příkazu rozhraní příkazového řádku můžete použít existující mapu oboru nebo vytvořit mapu oboru při vytváření tokenu zadáním jednoho nebo více úložišť a přidružených akcí. Úložiště ještě nemusí být v registru. 

Následující příklad vytvoří token a vytvoří mapu oboru s následujícími oprávněními pro `samples/hello-world` úložiště: `content/write` a `content/read` .

1. Na portálu přejděte do registru kontejneru.
1. V části **oprávnění úložiště** vyberte **tokeny (Preview) > + přidat**.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Vytvoření tokenu na portálu":::
1. Zadejte název tokenu.
1. V části **Mapa oboru** vyberte **vytvořit novou**.
1. Nakonfigurujte mapu oboru:
    1. Zadejte název a popis mapy oboru. 
    1. V části **úložiště** zadejte a `samples/hello-world` v části **oprávnění** vyberte  `content/read` a `content/write` . Pak vyberte **+ Přidat**.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Vytvořit mapu oboru na portálu":::

    1. Po přidání úložišť a oprávnění vyberte **Přidat** a přidejte mapu oboru.
1. Přijměte výchozí **stav** tokenu **Enabled** a pak vyberte **vytvořit**.

Po ověření a vytvoření tokenu se na obrazovce **tokeny** zobrazí podrobnosti o tokenu.

### <a name="add-token-password"></a>Přidat heslo tokenu

Pokud chcete použít token vytvořený na portálu, musíte vygenerovat heslo. Můžete vygenerovat jedno nebo dvě hesla a nastavit datum vypršení platnosti pro každé z nich. 

1. Na portálu přejděte do registru kontejneru.
1. V části **oprávnění úložiště** vyberte **tokeny (Preview)** a vyberte token.
1. V podrobnostech tokenu vyberte **Heslo1** nebo **password2** a vyberte ikonu generovat.
1. Na obrazovce heslo volitelně nastavte datum vypršení platnosti hesla a vyberte **Generovat**. Doporučuje se nastavit datum vypršení platnosti.
1. Po vygenerování hesla ho zkopírujte a uložte na bezpečné místo. Po zavření obrazovky nelze načíst vygenerované heslo, ale můžete vygenerovat nové.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Vytvořit heslo tokenu na portálu":::

## <a name="authenticate-with-token"></a>Ověřování pomocí tokenu

Pokud uživatel nebo služba používá token k ověření pomocí cílového registru, poskytne název tokenu jako uživatelské jméno a jedno z vygenerovaných hesel. 

Metoda ověřování závisí na konfigurované akci nebo akcích přidružených k tomuto tokenu.

|Akce  |Ověřování  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` v Azure CLI<br/><br/>Příklad: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` v Azure CLI<br/><br/>Příklad: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` v Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` v Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` v Azure CLI |

## <a name="examples-use-token"></a>Příklady: použití tokenu

V následujících příkladech se používá token vytvořený dříve v tomto článku k provádění běžných operací s úložištěm: nabízené a vyžádané obrázky, odstraňování imagí a seznam značek úložiště. V úložišti se nejdřív nastavila oprávnění push ( `content/write` a `content/read` Akce) `samples/hello-world` .

### <a name="pull-and-tag-test-images"></a>Načíst a označit image testu

V následujících příkladech si načetli `hello-world` `alpine` image a z dokovacího centra a označíte je jako registr a úložiště.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag alpine myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Ověřování pomocí tokenu

Spusťte `docker login` nebo `az acr login` k ověření pomocí registru pro vložení nebo vyžádání imagí. Jako uživatelské jméno zadejte název tokenu a zadejte jedno z jeho hesel. Token musí mít `Enabled` stav.

Následující příklad je naformátován pro prostředí bash a poskytuje hodnoty pomocí proměnných prostředí.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Výstup by měl zobrazit úspěšné ověření:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Nahrávání imagí do registru

Po úspěšném přihlášení se pokusí do registru odeslat označené obrázky. Vzhledem k tomu, že token má oprávnění k odesílání imagí do `samples/hello-world` úložiště, následující nabízené oznámení je úspěšné:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Token nemá oprávnění k `samples/alpine` úložišti, takže následující pokus o vložení se nezdaří s chybou, která je podobná této `requested access to the resource is denied` :

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="update-token-permissions"></a>Aktualizovat oprávnění tokenu

Pokud chcete aktualizovat oprávnění tokenu, aktualizujte oprávnění v přidružené mapě oboru. Aktualizovaná mapa oboru se okamžitě použije na všechny přidružené tokeny. 

Například proveďte aktualizaci `MyToken-scope-map` pomocí `content/write` akcí a `content/read` v `samples/alpine` úložišti a odeberte `content/write` akci v `samples/hello-world` úložišti.  

Pokud chcete použít rozhraní příkazového řádku Azure, spusťte příkaz [AZ ACR Scope-map Update][az-acr-scope-map-update] , aby se aktualizovala mapa oboru:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

Na webu Azure Portal:

1. Přejděte do registru kontejneru.
1. V části **oprávnění úložiště** vyberte **mapy oboru (Preview)** a vyberte mapu oboru, která se má aktualizovat.
1. V části **úložiště** zadejte a `samples/alpine` v části **oprávnění** vyberte `content/read` a `content/write` . Pak vyberte **+ Přidat**.
1. V části **úložiště** vyberte `samples/hello-world` a v části **oprávnění** zrušte výběr `content/write` . Pak vyberte **Uložit**.

Po aktualizaci mapy oboru proběhne následující nabízení oznámení:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Vzhledem k tomu, že mapa oboru má pouze `content/read` oprávnění k `samples/hello-world` úložišti, pokus o nahrání tohoto `samples/hello-world` úložiště nyní selhává:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Navýšení imagí z obou úložišť je úspěšné, protože mapa oboru poskytuje `content/read` oprávnění pro obě úložiště:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Odstranění imagí

Aktualizujte mapu oboru přidáním `content/delete` akce do `alpine` úložiště. Tato akce povolí odstranění imagí v úložišti nebo odstranění celého úložiště.

V případě zkrácení se pro aktualizaci mapy oboru zobrazuje jenom příkaz [AZ ACR Scope-map Update][az-acr-scope-map-update] :

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Postup aktualizace mapy oboru pomocí portálu najdete v [předchozí části](#update-token-permissions).

K odstranění úložiště použijte následující příkaz [AZ ACR úložiště Delete][az-acr-repository-delete] `samples/alpine` . Pokud chcete odstranit Image nebo úložiště, předejte do příkazu název a heslo tokenu. Následující příklad používá proměnné prostředí vytvořené dříve v článku:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Zobrazit značky úložiště 

Aktualizujte mapu oboru přidáním `metadata/read` akce do `hello-world` úložiště. Tato akce umožňuje číst manifest a data značek v úložišti.

V případě zkrácení se pro aktualizaci mapy oboru zobrazuje jenom příkaz [AZ ACR Scope-map Update][az-acr-scope-map-update] :

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Postup aktualizace mapy oboru pomocí portálu najdete v [předchozí části](#update-token-permissions).

Pokud chcete číst metadata v `samples/hello-world` úložišti, spusťte příkaz [AZ ACR úložiště show-Manifests][az-acr-repository-show-manifests] nebo [AZ ACR úložiště show-Tags][az-acr-repository-show-tags] . 

Chcete-li číst metadata, předejte jméno a heslo tokenu do příkazu. Následující příklad používá proměnné prostředí vytvořené dříve v článku:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Ukázkový výstup:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>Správa tokenů a map oboru

### <a name="list-scope-maps"></a>Seznam map oboru

K vypsání všech map oboru nakonfigurovaných v registru použijte příkaz [AZ ACR Scope-map list][az-acr-scope-map-list] nebo obrazovku **Scope Maps (Preview)** na portálu. Zde je příklad:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

Výstup se skládá ze tří mapovaných rozsahů definovaných systémem a dalších map oboru generovaných vámi. Tokeny lze konfigurovat pomocí kterékoli z těchto map oboru.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Zobrazit podrobnosti tokenu

Pokud chcete zobrazit podrobnosti o tokenu, jako je jeho stav a datum vypršení platnosti hesla, spusťte příkaz [AZ ACR token show][az-acr-token-show] nebo vyberte token na obrazovce **tokeny (Preview)** na portálu. Zde je příklad:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

K vypsání všech tokenů nakonfigurovaných v registru použijte příkaz [AZ ACR token list][az-acr-token-list] nebo obrazovku **tokens (Preview)** na portálu. Zde je příklad:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Znovu vygenerovat hesla tokenů

Pokud jste nevygenerovali heslo tokenu nebo chcete generovat nová hesla, spusťte příkaz [AZ ACR token Credential Generate][az-acr-token-credential-generate] . 

V následujícím příkladu je vygenerována nová hodnota pro Heslo1 pro token *MyToken* , přičemž doba platnosti je 30 dní. Heslo ukládá do proměnné prostředí `TOKEN_PWD` . Tento příklad je naformátován pro prostředí bash shell.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Pokud chcete použít Azure Portal k vygenerování hesla tokenu, přečtěte si postup v části [vytvoření tokenu – portál](#create-token---portal) výše v tomto článku.

### <a name="update-token-with-new-scope-map"></a>Aktualizovat token s mapou nového oboru

Pokud chcete aktualizovat token s jinou mapou oboru, spusťte příkaz [AZ ACR token Update][az-acr-token-update] a zadejte novou mapu oboru. Zde je příklad:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Na portálu na obrazovce **tokeny (Preview)** vyberte token a v části **Mapa oboru** vyberte jinou mapu oboru.

> [!TIP]
> Po aktualizaci tokenu pomocí nové mapy oboru můžete vygenerovat nová hesla tokenů. Pomocí příkazu [AZ ACR token Credential Generate][az-acr-token-credential-generate] nebo znovu vygenerujte heslo tokenu v Azure Portal.

## <a name="disable-or-delete-token"></a>Zakázat nebo odstranit token

Možná budete muset dočasně zakázat použití přihlašovacích údajů tokenu pro uživatele nebo službu. 

Pomocí Azure CLI spuštěním příkazu [AZ ACR token Update][az-acr-token-update] nastavte na `status` `disabled` :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Na portálu vyberte na obrazovce **tokeny (Preview)** token a v části **stav** vyberte **zakázáno** .

Pokud chcete odstranit token pro trvalé zrušení platnosti přístupu kýmkoli, kdo používá jeho přihlašovací údaje, spusťte příkaz [AZ ACR token Delete][az-acr-token-delete] . 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Na portálu vyberte token na obrazovce **tokeny (Preview)** a vyberte **Zahodit**.

## <a name="next-steps"></a>Další kroky

* Pokud chcete spravovat mapy oboru a tokeny, použijte další příkazy v příkazu [AZ ACR Scope-map][az-acr-scope-map] a [AZ ACR token][az-acr-token] groups.
* V tématu [Přehled ověřování](container-registry-authentication.md) najdete další možnosti ověřování pomocí služby Azure Container Registry, včetně použití Azure Active Directory identity, instančního objektu nebo účtu správce.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
