---
title: Oprávnění k úložištím v registru kontejnerů Azure
description: Vytvoření tokenu s oprávněními vymezenými pro konkrétní úložiště v registru pro vytahování nebo nabízení obrázků nebo provádění jiných akcí
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 9004c45401833d3070266055dd7eb99a2bb43bde
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618829"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Vytvoření tokenu s oprávněními s rozsahem úložiště

Tento článek popisuje, jak vytvořit tokeny a mapy oboru pro správu oprávnění s rozsahem úložiště v registru kontejneru. Vytvořením tokenů může vlastník registru poskytnout uživatelům nebo službám časově omezený přístup k úložištím, aby mohli vyžádat nebo vysunout obrázky nebo provést jiné akce. Token poskytuje podrobnější oprávnění než jiné [možnosti ověřování](container-registry-authentication.md)registru , která oprávnění oboru pro celý registr. 

Scénáře pro vytvoření tokenu zahrnují:

* Povolení, aby zařízení IoT s jednotlivými tokeny stáhla obrázek z úložiště
* Poskytnutí oprávnění externí organizaci k určitému úložišti 
* Omezte přístup k úložišti různým skupinám uživatelů ve vaší organizaci. Můžete například poskytnout přístup pro zápis a čtení vývojářům, kteří vytvářejí image, které cílí na konkrétní úložiště, a přístup pro čtení pro týmy, které se nasazují z těchto úložišť.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a platí určitá [omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení náhledu

* Tato funkce je k dispozici pouze v registru kontejneru **Premium.** Informace o vrstvách a limitech služby registru naleznete v tématu [Azure Container Registry SKU](container-registry-skus.md).
* V současné době nelze přiřadit oprávnění s rozsahem úložiště k identitě služby Azure Active Directory, jako je například instanční objekt nebo spravovaná identita.
* V registru povoleném pro anonymní přístup [k vyžádat nelze](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)vytvořit mapu oboru .

## <a name="concepts"></a>Koncepty

Chcete-li konfigurovat oprávnění s rozsahem úložiště, vytvořte *token* s přidruženou *mapou oboru*. 

* **Token** spolu s vygenerovaným heslem umožňuje uživateli ověřit pomocí registru. Můžete nastavit datum vypršení platnosti pro heslo tokenu nebo token kdykoli zakázat.  

  Po ověření pomocí tokenu může uživatel nebo služba provést jednu nebo více *akcí* s rozsahem jednoho nebo více úložišť.

  |Akce  |Popis  | Příklad |
  |---------|---------|--------|
  |`content/delete`    | Odebrání dat z úložiště  | Odstranění úložiště nebo manifestu |
  |`content/read`     |  Čtení dat z úložiště |  Vytáhnout artefakt |
  |`content/write`     |  Zápis dat do úložiště     | Použití `content/read` s push artefakt |
  |`metadata/read`    | Čtení metadat z úložiště   | Značky nebo manifesty seznamu |
  |`metadata/write`     |  Zápis metadat do úložiště  | Povolení nebo zakázání operací čtení, zápisu nebo odstranění |

* **Mapa oboru** seskupuje oprávnění úložiště, která použijete na token, a může se znovu vztahovat na jiné tokeny. Každý token je přidružen k mapě jednoho oboru. 

   S mapou oboru:

    * Konfigurace více tokenů se stejnými oprávněními pro sadu úložišť
    * Aktualizace oprávnění tokenu při přidání nebo odebrání akcí úložiště v mapě oboru nebo použití jiné mapy oboru 

  Azure Container Registry také poskytuje několik map oborů definovaných systémem, které můžete použít, s pevnými oprávněními ve všech úložištích.

Následující obrázek znázorňuje vztah mezi tokeny a mapami oboru. 

![Tokeny registru a mapy oboru](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Požadavky

* **Azure CLI** – příkazy rozhraní příkazového příkazu Azure k vytváření a správě tokenů jsou dostupné ve verzi Azure CLI 2.0.76 nebo novějším. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).
* **Docker** – Chcete-li se ověřit pomocí registru pro vytahovací nebo nabízené bitové kopie, potřebujete místní instalaci Dockeru. Docker poskytuje pokyny k instalaci pro systémy [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registr kontejnerů** – pokud ho nemáte, vytvořte v předplatném Azure registr kontejneru Premium nebo upgradujte existující registr. Například použijte [portál Azure](container-registry-get-started-portal.md) nebo [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Vytvořit token – cli

### <a name="create-token-and-specify-repositories"></a>Vytvoření tokenu a určení úložišť

Vytvořte token pomocí příkazu [az acr token create.][az-acr-token-create] Při vytváření tokenu můžete v každém úložišti zadat jedno nebo více úložišť a přidružené akce. Repozitáře ještě nemusí být v registru. Chcete-li vytvořit token zadáním existující mapy oboru, podívejte se na další část.

Následující příklad vytvoří token v registru *myregistry* s `samples/hello-world` následujícími `content/write` oprávněními na repo: a `content/read`. Ve výchozím nastavení příkaz nastaví `enabled`výchozí stav tokenu na `disabled` , ale můžete jej kdykoli aktualizovat.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

Výstup zobrazuje podrobnosti o tokenu, včetně dvou generovaných hesel. Doporučujeme uložit hesla na bezpečném místě, které chcete později použít k ověření. Hesla nelze znovu načíst, ale lze vygenerovat nová.

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

Výstup obsahuje podrobnosti o mapě oboru, kterou příkaz vytvořil. Mapu oboru s názvem `MyToken-scope-map`můžete použít k použití stejných akcí úložiště na jiné tokeny. Nebo aktualizujte mapu oboru později změnit oprávnění přidružené tokeny.

### <a name="create-token-and-specify-scope-map"></a>Vytvoření tokenu a určení mapy oboru

Alternativní způsob, jak vytvořit token je určit existující mapu oboru. Pokud ještě nemáte mapu oboru, nejprve ji vytvořte zadáním úložišť a přidružených akcí. Potom zadejte mapu oboru při vytváření tokenu. 

Chcete-li vytvořit mapu oboru, použijte příkaz [az acr scope-map create][az-acr-scope-map-create] . Následující příkaz vytvoří mapu oboru se stejnými oprávněními v úložišti, které `samples/hello-world` bylo použito dříve. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Spusťte [az acr token vytvořit][az-acr-token-create] token, určení mapy oboru *MyScopeMap.* Stejně jako v předchozím příkladu nastaví `enabled`příkaz výchozí stav tokenu na .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

Výstup zobrazuje podrobnosti o tokenu, včetně dvou generovaných hesel. Doporučujeme uložit hesla na bezpečném místě, které chcete později použít k ověření. Hesla nelze znovu načíst, ale lze vygenerovat nová.

## <a name="create-token---portal"></a>Vytvořit token - portál

Portál Azure můžete použít k vytvoření tokenů a map oboru. Stejně `az acr token create` jako u příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu, můžete použít existující mapu oboru nebo vytvořit mapu oboru při vytváření tokenu zadáním jednoho nebo více úložišť a přidružených akcí. Repozitáře ještě nemusí být v registru. 

Následující příklad vytvoří token a vytvoří mapu oboru s `samples/hello-world` následujícími `content/write` oprávněními v úložišti: a `content/read`.

1. Na portálu přejděte do registru kontejnerů.
1. V části **Služby**vyberte **Tokeny (náhled) > +Přidat**.
  ![Vytvořit token na portálu](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Zadejte název tokenu.
1. V části **Mapa oboru**vyberte **Vytvořit nový**.
1. Konfigurace mapy oboru:
    1. Zadejte název a popis mapy oboru. 
    1. V části **Repozitáře** `samples/hello-world`zadejte a **v části Oprávnění**vyberte `content/read` a `content/write`. Pak vyberte **+Přidat**.  
    ![Vytvoření mapy oboru na portálu](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Po přidání úložišť a oprávnění vyberte **Přidat** a přidejte mapu oboru.
1. Přijměte výchozí **stav** tokenu **Povoleno** a pak vyberte **Vytvořit**.

Po ověření a vytvoření tokenu se na obrazovce tokenů zobrazí podrobnosti **tokenů.**

### <a name="add-token-password"></a>Přidání hesla tokenu

Po vytvoření tokenu vygenerujte heslo. Chcete-li ověřit pomocí registru, musí být token povolen a mít platné heslo.

Můžete vygenerovat jedno nebo dvě hesla a nastavit datum vypršení platnosti pro každé z nich. 

1. Na portálu přejděte do registru kontejnerů.
1. V části **Služby**vyberte **Tokeny (náhled)** a vyberte token.
1. V podrobnostech tokenu vyberte **heslo1** nebo **heslo2**a vyberte ikonu Generovat.
1. Na obrazovce s heslem volitelně nastavte datum vypršení platnosti hesla a vyberte **generovat**.
1. Po vygenerování hesla jej zkopírujte a uložte na bezpečné místo. Po zavření obrazovky nelze načíst generované heslo, ale můžete vygenerovat nové.

    ![Vytvoření hesla tokenu na portálu](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Ověření pomocí tokenu

Pokud uživatel nebo služba používá token k ověření pomocí cílového registru, poskytuje název tokenu jako uživatelské jméno a jedno z jeho generovaných hesel. Metoda ověřování závisí na nakonfigurované akce nebo akce spojené s tokenem.

|Akce  |Jak ověřit  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`v azure cli |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`v azure cli  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`v azure cli     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`v azure cli   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`v azure cli |

## <a name="examples-use-token"></a>Příklady: Použití tokenu

Následující příklady používají token vytvořený dříve v tomto článku k provádění běžných operací v úložišti: nabízená a vytahovací obrázky, odstranění obrázků a značky úložiště seznamů. Token byl původně nastaven s`content/write` `content/read` oprávněními pro `samples/hello-world` nabízenou položky (a akcemi) v úložišti.

### <a name="pull-and-tag-test-images"></a>Vyžádat a označit testovací obrázky

Pro následující příklady nakreslete image `hello-world` a `alpine` z Docker Hubu a označte je pro váš registr a úložiště.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Ověření pomocí tokenu

Spusťte `docker login` ověření pomocí registru, Zadejte název tokenu jako uživatelské jméno a zadejte jedno z jeho hesel. Token musí mít `Enabled` stav.

Následující příklad je formátován pro prostředí bash a poskytuje hodnoty pomocí proměnných prostředí.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Výstup by měl zobrazovat úspěšné ověřování:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Nahrávání imagí do registru

Po úspěšném přihlášení se pokuste zasunutí označených obrázků do registru. Vzhledem k tomu, že `samples/hello-world` token má oprávnění k nabízení bitových kopií do úložiště, následující nabízení úspěšné:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Token nemá oprávnění k `samples/alpine` repo, takže následující pokus o nabízenou `requested access to the resource is denied`nabídku se nezdaří s chybou podobnou :

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Změna oprávnění pro nabízenou/vyžádat

Chcete-li aktualizovat oprávnění tokenu, aktualizujte oprávnění v přidružené mapě oboru. Aktualizované mapování oboru se použije okamžitě na všechny přidružené tokeny. 

Například aktualizovat `MyToken-scope-map` `content/write` s `content/read` a `samples/alpine` akce v úložišti `content/write` a `samples/hello-world` odebrat akci v úložišti.  

Chcete-li použít rozhraní příkazového příkazového příkazového příkazu k webu Azure, spusťte [aktualizaci mapy oboru AZ acr:][az-acr-scope-map-update]

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

Na webu Azure Portal:

1. Přejděte do registru kontejnerů.
1. V části **Služby**vyberte **Mapy oboru (Preview)** a vyberte mapu oboru, kterou chcete aktualizovat.
1. V části **Repozitáře** `samples/alpine`zadejte a **v části Oprávnění**vyberte `content/read` a `content/write`. Pak vyberte **+Přidat**.
1. V části **Repozitáře**vyberte `samples/hello-world` a **v části Oprávnění**odznačte `content/write`. Potom vyberte **Uložit**.

Po aktualizaci mapy oboru je následující nabízení úspěšné:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Vzhledem k tomu, `content/read` že `samples/hello-world` mapa oboru má pouze `samples/hello-world` oprávnění v úložišti, pokus o nabízení úložiště nyní selže:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Vytahování obrázků z obou úložišť proběhne úspěšně, protože mapa oboru poskytuje `content/read` oprávnění v obou úložištích:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Odstranění imagí

Aktualizujte mapu oboru `content/delete` přidáním `alpine` akce do úložiště. Tato akce umožňuje odstranění obrázků v úložišti nebo odstranění celého úložiště.

Pro stručnost zobrazíme pouze příkaz [az acr scope-map update][az-acr-scope-map-update] pro aktualizaci mapy oboru:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Chcete-li aktualizovat mapu oboru pomocí portálu, podívejte se na předchozí část.

K odstranění `samples/alpine` úložiště použijte následující příkaz odstranění úložiště [az acr.][az-acr-repository-delete] Chcete-li odstranit obrázky nebo úložiště, `docker login`token se neověřuje prostřednictvím . Místo toho předajte příkazu název tokenu a heslo. Následující příklad používá proměnné prostředí vytvořené dříve v článku:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Zobrazit značky repo 

Aktualizujte mapu oboru `metadata/read` přidáním `hello-world` akce do úložiště. Tato akce umožňuje čtení manifestu a tag dat v úložišti.

Pro stručnost zobrazíme pouze příkaz [az acr scope-map update][az-acr-scope-map-update] pro aktualizaci mapy oboru:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Chcete-li aktualizovat mapu oboru pomocí portálu, podívejte se na předchozí část.

Chcete-li číst `samples/hello-world` metadata v úložišti, spusťte příkaz [az acr repository show-manifests][az-acr-repository-show-manifests] nebo [az acr repository show-tags.][az-acr-repository-show-tags] 

Chcete-li číst metadata, token `docker login`není ověřit prostřednictvím . Místo toho předaj název tokenu a heslo do obou příkazů. Následující příklad používá proměnné prostředí vytvořené dříve v článku:

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
## <a name="manage-tokens-and-scope-maps"></a>Správa tokenů a map oborů

### <a name="list-scope-maps"></a>Mapy oboru seznamu

Pomocí příkazu [az acr scope-map list][az-acr-scope-map-list] nebo obrazovky **Mapy oboru (Náhled)** na portálu zobrazíte seznam všech map oboru nakonfigurovaných v registru. Například:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

Výstup zobrazuje mapy oboru, které jste definovali, a několik map oborů definovaných systémem, které můžete použít ke konfiguraci tokenů:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Zobrazit podrobnosti tokenu

Chcete-li zobrazit podrobnosti o tokenu, jako je například jeho stav a data vypršení platnosti hesla, spusťte příkaz [az acr token show][az-acr-token-show] nebo vyberte token na obrazovce **tokeny (náhled)** na portálu. Například:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Pomocí příkazu [az acr token y tokeny][az-acr-token-list] nebo obrazovky **Tokeny (Náhled)** na portálu uveďte všechny tokeny nakonfigurované v registru. Například:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Generovat hesla pro token

Pokud nemáte heslo tokenu nebo chcete generovat nová hesla, spusťte příkaz [pro generování tokenu az acr.][az-acr-token-credential-generate] 

Následující příklad generuje novou hodnotu pro password1 pro token *MyToken* s dobou vypršení platnosti 30 dnů. Ukládá heslo do proměnné `TOKEN_PWD`prostředí . Tento příklad je formátován pro bash shell.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Pokud chcete použít portál Azure ke generování hesla tokenu, přečtěte si postup v [tématu Vytvoření tokenu – portálu](#create-token---portal) dříve v tomto článku.

### <a name="update-token-with-new-scope-map"></a>Aktualizovat token pomocí nové mapy oboru

Pokud chcete aktualizovat token s jinou mapou oboru, spusťte [aktualizaci tokenu AZ ACR][az-acr-token-update] a zadejte novou mapu oboru. Například:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Na portálu na obrazovce **Tokeny (náhled)** vyberte token a v části **Mapa oboru**vyberte jinou mapu oboru.

> [!TIP]
> Po aktualizaci tokenu s novou mapou oboru můžete chtít vygenerovat nová hesla tokenů. Použijte příkaz [generovat token az acr token][az-acr-token-credential-generate] nebo znovu vygenerovat heslo tokenu na webu Azure Portal.

## <a name="disable-or-delete-token"></a>Zakázání nebo odstranění tokenu

Možná budete muset dočasně zakázat použití pověření tokenu pro uživatele nebo službu. 

Pomocí příkazového příkazu k onomu Azure spusťte `disabled`příkaz [az acr token update][az-acr-token-update] a nastavte `status` na :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Na portálu vyberte token na obrazovce **Tokeny (náhled)** a v části **Stav**vyberte **Zakázáno** .

Chcete-li odstranit token, který trvale zruší platnost přístupu všech uživatelům jeho pověření, spusťte příkaz [delete tokenu az acr.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Na portálu vyberte token na obrazovce **Tokeny (náhled)** a vyberte **Zahodit**.

## <a name="next-steps"></a>Další kroky

* Chcete-li spravovat mapy oboru a tokeny, použijte další příkazy ve skupinách příkazů [az acr scope-map][az-acr-scope-map] a [az az acr token.][az-acr-token]
* Podívejte se na [přehled ověřování](container-registry-authentication.md) pro další možnosti ověření pomocí registru kontejneru Azure, včetně použití identity Azure Active Directory, instančního objektu nebo účtu správce.


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
