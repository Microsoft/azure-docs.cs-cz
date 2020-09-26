---
title: Správa podepsaných imagí
description: Naučte se, jak povolit důvěryhodnost obsahu pro službu Azure Container Registry, a nahrajte a vyžádat si podepsané image. Vztah důvěryhodnosti obsahu je funkcí úrovně Premium Service.
ms.topic: article
ms.date: 09/06/2019
ms.openlocfilehash: 36d2a8ddef184804facdace2d517d7e2fdf1b24c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253475"
---
# <a name="content-trust-in-azure-container-registry"></a>Důvěryhodnost obsahu ve službě Azure Container Registry

Azure Container Registry implementuje model [vztahu důvěryhodnosti obsahu][docker-content-trust] Docker, který umožňuje vkládání a přijímání podepsaných imagí. Tento článek vám pomůže začít s povolením vztahu důvěryhodnosti obsahu v registrech kontejnerů.

> [!NOTE]
> Vztah důvěryhodnosti obsahu je funkce [úrovně Premium služby](container-registry-skus.md) Azure Container Registry.

## <a name="how-content-trust-works"></a>Princip fungování důvěryhodnosti obsahu

V každém distribuovaném systému navrženém s ohledem na zabezpečení je důležité ověřovat *zdroj* i *integritu* dat, která do systému přicházejí. Příjemci dat potřebují mít možnost ověřit vydavatele (zdroj) dat i to, že po publikování nedošlo ke změně dat (integrita). 

Jako vydavateli imagí vám důvěryhodnost obsahu umožňuje **podepisovat** image, které odesíláte do svého registru. Příjemci vašich imagí (lidé nebo systémy stahující image z vašeho registru) můžou nakonfigurovat své klienty tak, aby stahovali *pouze* podepsané image. Když příjemci imagí stáhnou podepsanou image, jejich klient Dockeru ověří integritu image. V tomto modelu mají příjemci jistotu, že jste podepsané image ve vašem registru skutečně publikovali vy a že po publikování nedošlo k jejich změně.

### <a name="trusted-images"></a>Důvěryhodné image

Důvěryhodnost obsahu pracuje se **značkami** v úložišti. Úložiště imagí můžou obsahovat image s podepsanými i nepodepsanými značkami. Například můžete podepsat pouze image `myimage:stable` a `myimage:latest`, ale ne image `myimage:dev`.

### <a name="signing-keys"></a>Podpisové klíče

Důvěryhodnost obsahu se spravuje prostřednictvím používání sady kryptografických podpisových klíčů. Tyto klíče jsou přidružené ke konkrétnímu úložišti v registru. Klienti Dockeru a váš registr při správě důvěryhodnosti značek v úložišti používají několik typů podpisových klíčů. Pokud povolíte důvěryhodnost obsahu a integrujete ji do svého kanálu publikování a využívání kontejnerů, musíte tyto klíče pečlivě spravovat. Další informace najdete v pozdější části [Správa klíčů](#key-management) v tomto článku a v tématu [Správa klíčů pro důvěryhodnost obsahu][docker-manage-keys] v dokumentaci k Dockeru.

> [!TIP]
> Toto byl velmi obecný přehled modelu důvěryhodnosti obsahu Dockeru. Podrobný popis důvěryhodnosti obsahu najdete v tématu [Důvěryhodnost obsahu v Dockeru][docker-content-trust].

## <a name="enable-registry-content-trust"></a>Povolení důvěryhodnosti obsahu u registru

Prvním krokem je povolení důvěryhodnosti obsahu na úrovni registru. Jakmile povolíte důvěryhodnost obsahu, klienti (uživatelé nebo služby) budou do vašeho registru moct odesílat podepsané image. Po povolení důvěryhodnosti obsahu v registru nedojde k omezení možnosti využívat registr pouze na zákazníky s povolenou důvěryhodností obsahu. Příjemci bez povolené důvěryhodnosti obsahu můžou váš registr normálně využívat i nadále. Příjemcům, kteří ve svých klientech povolili důvěryhodnost obsahu, se však zobrazí *pouze* podepsané image ve vašem registru.

Pokud chcete pro svůj registr povolit důvěryhodnost obsahu, nejprve přejděte do tohoto registru na webu Azure Portal. V části **zásady**vyberte možnost Uložit povolený **vztah důvěryhodnosti obsahu**  >  **Enabled**  >  **Save**. V Azure CLI taky můžete použít příkaz [AZ ACR config Content-Trust Update][az-acr-config-content-trust-update] .

![Snímek obrazovky ukazuje povolení vztahu důvěryhodnosti obsahu pro registr v Azure Portal.][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>Povolení důvěryhodnosti obsahu u klientů

Aby bylo možné pracovat s důvěryhodnými imagemi, musí vydavatelé i příjemci imagí povolit důvěryhodnost obsahu pro své klienty Dockeru. Jako vydavatel můžete podepisovat image, které odešlete do registru s povolenou důvěryhodností obsahu. Jako příjemci se vám po povolení důvěryhodnosti obsahu budou v registru zobrazovat pouze podepsané image. V klientech Dockeru je důvěryhodnost obsahu ve výchozím nastavení zakázaná, ale můžete ji povolit pro jednotlivé relace prostředí nebo příkazy.

Pokud chcete povolit důvěryhodnost obsahu v relaci prostředí, nastavte proměnnou prostředí `DOCKER_CONTENT_TRUST` na hodnotu **1**. Příklad v prostředí Bash:

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

Pokud místo toho chcete povolit nebo zakázat důvěryhodnost obsahu pro jeden příkaz, řada příkazů Dockeru podporuje argument `--disable-content-trust`. Povolení důvěryhodnosti obsahu pro jeden příkaz:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

Pokud jste povolili důvěryhodnost obsahu v relaci prostředí a chcete ji zakázat pro jeden příkaz:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>Udělení oprávnění k podepisování imagí

Do vašeho registru můžou odesílat důvěryhodné image pouze uživatelé nebo systémy, kterým k tomu udělíte oprávnění. Pokud chcete uživateli (nebo systému prostřednictvím instančního objektu) udělit oprávnění k odesílání důvěryhodných imagí, udělte příslušné identitě Azure Active Directory roli `AcrImageSigner`. To je kromě `AcrPush` role (nebo ekvivalentní), která je nutná pro vkládání imagí do registru. Podrobnosti najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).

> [!NOTE]
> [Účtu správce](container-registry-authentication.md#admin-account) služby Azure Container Registry nemůžete udělit oprávnění k odesílání důvěryhodných imagí.

Následují podrobnosti o udělení role `AcrImageSigner` na webu Azure Portal a v Azure CLI.

### <a name="azure-portal"></a>portál Azure

Přejděte do registru v Azure Portal a pak vyberte **řízení přístupu (IAM)**  >  **Přidat přiřazení role**. V části **Přidat přiřazení role**vyberte `AcrImageSigner` v části **role**, **Vyberte** jednoho nebo více uživatelů nebo instančních objektů a pak klikněte na **Uložit**.

V tomto příkladu se role `AcrImageSigner` přiřadila dvěma entitám: instančnímu objektu service-principal a uživateli Azure User.

![Povolení důvěryhodnosti obsahu pro registr na webu Azure Portal][content-trust-02-portal]

### <a name="azure-cli"></a>Azure CLI

Pokud chcete uživateli udělit oprávnění k podpisování pomocí Azure CLI, přiřaďte uživateli roli `AcrImageSigner` s oborem vymezeným vaším registrem. Příkaz má tento formát:

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

Pokud například chcete tuto roli udělit sami sobě, můžete v ověřené relaci Azure CLI spustit následující příkazy. Hodnotu `REGISTRY` upravte tak, aby odpovídala názvu vašeho registru kontejneru Azure.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
USER=$(az account show --query user.name --output tsv)
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)
```

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee $USER
```

Oprávnění k odesílání důvěryhodných imagí do vašeho registru můžete udělit také [instančnímu objektu](container-registry-auth-service-principal.md). Použití instančního objektu je užitečné u sestavovacích a dalších bezobslužných systémů, které potřebují odesílat důvěryhodné image do vašeho registru. Formát je podobný jako při udělování oprávnění uživateli, ale jako hodnotu `--assignee` použijte ID instančního objektu.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

Hodnota `<service principal ID>` může být **ID aplikace**, **ID objektu** nebo jeden z **hlavních názvů služby** instančního objektu. Další informace o práci s instančními objekty a službou Azure Container Registry najdete v tématu [Ověřování ve službě Azure Container Registry pomocí instančních objektů](container-registry-auth-service-principal.md).

> [!IMPORTANT]
> Po změně jakékoli role spusťte příkaz `az acr login` a aktualizujte místní token identity pro rozhraní příkazového řádku Azure CLI tak, aby se nové role mohly projevit. Informace o ověřování rolí pro identitu najdete v tématu [Správa přístupu k prostředkům Azure pomocí RBAC a Azure CLI](../role-based-access-control/role-assignments-cli.md) a [řešení potíží s Azure RBAC](../role-based-access-control/troubleshooting.md).

## <a name="push-a-trusted-image"></a>Odeslání důvěryhodné image

Pokud chcete do registru kontejneru odeslat značku důvěryhodné image, povolte důvěryhodnost obsahu a odešlete image pomocí příkazu `docker push`. Při prvním odeslání podepsané značky se zobrazí výzva k vytvoření hesla pro kořenový podpisový klíč i podpisový klíč úložiště. Kořenový podpisový klíč i podpisový klíč úložiště se vygenerují a uloží místně na vašem počítači.

```console
$ docker push myregistry.azurecr.io/myimage:v1
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

Po prvním spuštění příkazu `docker push` s povolenou důvěryhodností obsahu bude klient Dockeru používat stejný kořenový klíč i pro další odeslání. Při každém dalším odeslání do stejného úložiště se zobrazí pouze výzva k zadání klíče úložiště. Při každém odeslání důvěryhodné image do nového úložiště se zobrazí výzva k zadání hesla pro nový klíč úložiště.

## <a name="pull-a-trusted-image"></a>Stažení důvěryhodné image

Pokud chcete stáhnout důvěryhodnou image, povolte důvěryhodnost obsahu a jako obvykle spusťte příkaz `docker pull`. Pro získání důvěryhodných imagí `AcrPull` je role pro normální uživatele dostatečná. Nejsou vyžadovány žádné další role jako `AcrImageSigner` role. Příjemci s povolenou důvěryhodností obsahu můžou stahovat pouze image s podepsanou značkou. Tady je příklad stažení podepsané značky:

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

Pokud se klient s povolenou důvěryhodností obsahu pokusí stáhnout nepodepsanou značku, operace selže:

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
No valid trust data for unsigned
```

### <a name="behind-the-scenes"></a>Informace pro pokročilé uživatele

Po spuštění příkazu `docker pull` si klient Dockeru pomocí stejné knihovny jako v [rozhraní příkazového řádku Notary][docker-notary-cli] vyžádá mapování stahované značky na hodnotu hash SHA-256. Po ověření podpisů u dat důvěryhodnosti klient předá modulu Docker pokyn ke „stažení podle hodnoty hash“. Během stahování modul použije adresu obsahu v podobě kontrolního součtu SHA-256 k vyžádání a ověření manifestu image z registru kontejneru Azure.

## <a name="key-management"></a>Správa klíčů

Jak je uvedeno ve výstupu příkazu `docker push` při odeslání první důvěryhodné image, nejcitlivější je kořenový klíč. Nezapomeňte si kořenový klíč zálohovat a uložte ho na bezpečné místo. Klient Dockeru ve výchozím nastavení ukládá podpisové klíče do následujícího adresáře:

```sh
~/.docker/trust/private
```

Svůj kořenový klíč a klíče úložiště zálohujte tak, že je zkomprimujete v archivu a uložíte je na bezpečném místě. Příklad v prostředí Bash:

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

Kromě místně vygenerovaného kořenového klíče a klíče úložiště služba Azure Container Registry při odeslání důvěryhodné image vygeneruje a uloží několik dalších klíčů. Podrobný popis různých klíčů v implementaci důvěryhodnosti obsahu Dockeru, včetně dalších pokynů pro správu, najdete v tématu [Správa klíčů pro důvěryhodnost obsahu][docker-manage-keys] v dokumentaci k Dockeru.

### <a name="lost-root-key"></a>Ztracený kořenový klíč

Pokud ztratíte přístup ke svému kořenovému klíči, ztratíte tím přístup ke značkám podepsaným tímto klíčem ve všech úložištích. Azure Container Registry nemůže obnovit přístup ke značkám imagí podepsaným ztraceným kořenovým klíčem. Pokud chcete ze svého registru odebrat veškerá data důvěryhodnosti (podpisy), nejprve pro registr zakažte a pak znovu povolte důvěryhodnost obsahu.

> [!WARNING]
> Zakázáním a opětovným povolením důvěryhodnosti obsahu v registru se **odstraní veškerá data důvěryhodnosti pro všechny podepsané značky ve všech úložištích ve vašem registru**. Tato akce je nevratná – Azure Container Registry nemůže odstraněná data důvěryhodnosti obnovit. Samotné image se zákazem důvěryhodnosti obsahu neodstraní.

Pokud chcete pro svůj registr zakázat důvěryhodnost obsahu, přejděte do tohoto registru na webu Azure Portal. V části **zásady**vyberte možnost **důvěryhodnost obsahu**  >  **zakázáno**  >  **Uložit**. Zobrazí se upozornění na ztrátu všech podpisů v registru. Výběrem **OK** trvale odstraníte všechny podpisy ve vašem registru.

![Zakázání důvěryhodnosti obsahu pro registr na webu Azure Portal][content-trust-03-portal]

## <a name="next-steps"></a>Další kroky

* Další informace o vztahu důvěryhodnosti obsahu najdete [v části důvěryhodnost obsahu v Docker][docker-content-trust] . Přestože se tento článek zabýval několika klíčovými body, důvěryhodnost obsahu je rozsáhlé téma, kterému se podrobněji věnuje dokumentace k Dockeru.

* Příklad použití vztahu důvěryhodnosti obsahu při sestavování a vložení image Docker najdete v dokumentaci k [Azure Pipelines](/azure/devops/pipelines/build/content-trust) .

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-content-trust-update]: /cli/azure/acr/config/content-trust#az-acr-config-content-trust-update
