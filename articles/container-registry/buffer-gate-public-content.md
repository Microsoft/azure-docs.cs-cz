---
title: Správa veřejného obsahu v soukromém registru kontejnerů
description: Postupy a pracovní postupy v Azure Container Registry pro správu závislostí na veřejných imagí z Docker Hub a jiného veřejného obsahu
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 11/20/2020
ms.openlocfilehash: 0c92899528d417f9c91f8f8930ca4932dc74e850
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024734"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Správa veřejného obsahu pomocí Azure Container Registry

Tento článek představuje přehled postupů a pracovních postupů pro použití místního registru, jako je [Azure Container Registry](container-registry-intro.md) , k údržbě kopií veřejného obsahu, jako jsou image kontejnerů v Docker Hub. 


## <a name="risks-with-public-content"></a>Rizika s veřejným obsahem

Vaše prostředí může mít závislosti na veřejném obsahu, jako jsou obrázky veřejného kontejneru, [Helm grafy](https://helm.sh/), zásady neprů ( [Open Policy agent](https://www.openpolicyagent.org/) ) nebo jiné artefakty. Můžete například spustit [Nginx](https://hub.docker.com/_/nginx) pro směrování služeb nebo `docker build FROM alpine` nastavovat image přímo z Docker Hub nebo jiného veřejného registru. 

Bez správných ovládacích prvků, které mají závislosti na obsahu veřejného registru, mohou představovat rizika pro vaše pracovní postupy pro vývoj a nasazení imagí. Chcete-li zmírnit rizika, udržujte místní kopie veřejného obsahu, pokud je to možné. Podrobnosti najdete v [blogu otevřít iniciativu kontejneru](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Ověřování pomocí Docker Hub

V prvním kroku, pokud v rámci pracovního postupu sestavení nebo nasazení aktuálně vystavíte veřejné image z Docker Hub, doporučujeme místo vytváření anonymní žádosti o přijetí změn provést [ověření pomocí účtu Docker Hub](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) .

Při provádění častých anonymních žádostí o přijetí změn se může zobrazit chyba Docker, která je podobná `ERROR: toomanyrequests: Too Many Requests.` nebo `You have reached your pull rate limit.` ověřena z dokovacího centra, aby tyto chyby nedocházelo.

> [!NOTE]
> Od 2. listopadu 2020 se [limity četnosti stahování](https://docs.docker.com/docker-hub/download-rate-limit) vztahují na anonymní a ověřené požadavky na Docker Hub z účtů bezplatného plánu Docker a jsou vyhodnoceny podle IP adresy a ID Docker v uvedeném pořadí. 
>
> Při odhadování počtu žádostí o přijetí změn Vezměte v úvahu, že při použití služeb poskytovatele cloudu nebo při práci za podnikovým překladem adres (NAT) bude k dispozici více uživatelů, kteří mají v souhrnu k dispozici jako podmnožinu IP adres. Přidání ověřování placeného účtu Docker do požadavků odeslaných do služby Docker Hub se vyhnete potenciálním přerušením služeb z důvodu omezení četnosti.
>
> Podrobnosti najdete v tématu [ceny a předplatné Docker](https://www.docker.com/pricing) a [podmínek služby Docker](https://www.docker.com/legal/docker-terms-service).

### <a name="docker-hub-access-token"></a>Přístupový token Docker Hub

Docker Hub podporuje [tokeny osobního přístupu](https://docs.docker.com/docker-hub/access-tokens/) jako alternativy k heslu Docker při ověřování k dokovacímu centru. Pro automatizované služby, které přecházejí image z Docker Hub, se doporučuje používat tokeny. Můžete vygenerovat několik tokenů pro různé uživatele nebo služby a odvolat tokeny, pokud už nepotřebujete.

Pokud chcete provést ověření `docker login` pomocí tokenu, vynechejte na příkazovém řádku heslo. Po zobrazení výzvy k zadání hesla zadejte token místo toho. Pokud jste povolili dvojúrovňové ověřování pro účet Docker Hub, musíte při přihlašování z Docker CLI použít osobní přístupový token.

### <a name="authenticate-from-azure-services"></a>Ověřování ze služeb Azure

Několik služeb Azure, včetně App Service a Azure Container Instances podporuje přijímání imagí z veřejných registrů, jako je Docker Hub pro nasazení kontejnerů. Pokud potřebujete nasadit image z Docker Hub, doporučujeme nakonfigurovat nastavení pro ověřování pomocí účtu Docker Hub. Příklady:

**App Service**

* **Zdroj bitové kopie**: centrum Docker
* **Přístup k úložišti**: privátní
* **Přihlašovací jméno**: \<Docker Hub username>
* **Heslo**: \<Docker Hub token>

Podrobnosti najdete v tématu [ověřené přijetí změn Docker Hub na App Service](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html).

**Azure Container Instances**

* **Zdroj bitové kopie**: centrum Docker nebo jiný registr
* **Typ obrázku**: privátní
* **Přihlašovací server registru imagí**: Docker.IO
* **Uživatelské jméno v registru imagí**: \<Docker Hub username>
* **Heslo k registru bitové kopie**: \<Docker Hub token>
* **Obrázek**: Docker.IO/ \<repo name\> :\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Import imagí do služby Azure Container Registry
 
Pokud chcete začít spravovat kopie veřejných imagí, můžete vytvořit registr kontejnerů Azure, pokud ho ještě nemáte. Vytvořte registr pomocí rozhraní příkazového [řádku Azure](container-registry-get-started-azure-cli.md), [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md)nebo dalších nástrojů. 

Jako doporučený jednorázový krok [importujte](container-registry-import-images.md) základní image a další veřejný obsah do služby Azure Container Registry. Příkaz [AZ ACR import](/cli/azure/acr#az_acr_import) v Azure CLI podporuje import obrázků z veřejných registrů, jako je Docker Hub a Microsoft Container Registry, a z jiných privátních registrů kontejnerů. 

`az acr import` nevyžaduje místní instalaci Docker. Můžete ji spustit v místní instalaci rozhraní příkazového řádku Azure CLI nebo přímo v Azure Cloud Shell. Podporuje obrázky libovolného typu operačního systému, imagí s více architekturami nebo artefaktů OCI, jako jsou Helm grafy.

Příklad:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

V závislosti na potřebách vaší organizace můžete importovat do vyhrazeného registru nebo úložiště ve sdíleném registru.

## <a name="automate-application-image-updates"></a>Automatizace aktualizací imagí aplikace

Vývojáři imagí aplikace by měli zajistit, aby jejich kód odkazoval na místní obsah pod svým ovládacím prvkem. Například `Docker FROM` příkaz ve souboru Dockerfile by měl odkazovat na obrázek v registru privátních imagí, nikoli na veřejném registru. 

Při rozšiřování při importu obrázků nastavte [úlohu Azure Container Registry](container-registry-tasks-overview.md) pro automatizaci sestavení imagí aplikace, když se aktualizují základní image. Úkol automatizovaného sestavení může sledovat [aktualizace základní image](container-registry-tasks-base-images.md) i [aktualizace zdrojového kódu](container-registry-tasks-overview.md#trigger-task-on-source-code-update).

Podrobný příklad najdete v tématu [jak využívat a spravovat veřejný obsah s Azure Container Registry úkoly](tasks-consume-public-content.md). 

> [!NOTE]
> Jedna předkonfigurovaná úloha může automaticky znovu sestavit každou image aplikace, která odkazuje na závislý základní obrázek. 
 
## <a name="next-steps"></a>Další kroky
 
* Přečtěte si další informace o [ACR úlohách](container-registry-tasks-overview.md) pro vytváření, spouštění, vkládání a opravy imagí kontejnerů v Azure.
* Přečtěte si [, jak používat a spravovat veřejný obsah s Azure Container Registry úkoly](tasks-consume-public-content.md) pro pracovní postup automatizovaného uzavírání, který umožňuje aktualizovat základní image na vaše prostředí. 
* Další příklady pro automatizaci sestavení a aktualizací imagí najdete v [kurzech k ACR úlohám](container-registry-tutorial-quick-task.md) .
