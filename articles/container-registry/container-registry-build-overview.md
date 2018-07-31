---
title: Automatizace operačního systému a rozhraní framework opravy chyb s použitím služby Azure Container Registry Build (ACR buildu)
description: Úvod do ACR buildu, sadu funkcí ve službě Azure Container Registry, která poskytuje zabezpečené, automatizovat sestavení image kontejneru a používání dílčích oprav v cloudu.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 07/28/2018
ms.author: marsma
ms.openlocfilehash: 532817c6289c1718fd82a502e04dc10715ee7203
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343096"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatizace operačního systému a rozhraní framework opravy s využitím ACR buildu

Kontejnery poskytují nové úrovni, virtualizaci, izolaci závislostí aplikace a pro vývojáře z infrastruktury a provozních požadavků. Co je ještě, ale je potřeba vyřešit, jak je opravit této platformy application virtualization.

**ACR buildu** je sada funkcí v rámci Azure Container Registry. Poskytuje vytváření bitové kopie založené na cloudu kontejner pro Linux, Windows a ARM a můžete automatizovat [operačního systému a používání dílčích oprav framework](#automate-os-and-framework-patching) pro kontejnery Dockeru.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>Co je ACR buildu?

Azure Container Registry sestavení je služba sestavení image kontejneru nativní pro Azure. Sestaví ACR buildu vývoje vnitřní smyčky umožňuje v cloudu s použitím image kontejnerů na vyžádání a aktualizovat automatizované buildy na zdrojový kód potvrzení a základní image.

Image kontejneru aktivační události sestavení automaticky, když je kód potvrzen do úložiště Git, nebo když se aktualizuje základní image kontejneru. Pomocí aktivační události update základní image, můžete automatizovat vašeho operačního systému a architektura aplikace na opravy chyb pracovního postupu, zachování zabezpečení prostředí při týkajícími se objekty zabezpečení neměnné kontejnerů.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Rychlé sestavení: vnitřní smyčky rozšířit do cloudu

Začátek správy životního cyklu spuštěna před vývojáře potvrdit své první řádky kódu. ACR buildu [rychlé sestavení](container-registry-tutorial-quick-build.md) funkce umožňuje prostředí integrované místního vývoje vnitřní smyčky, snižování zátěže systému souborů sestavení do Azure. S rychlé sestavení můžete ověřit vaše definice sestavení automatizované před potvrzením kódu.

Pomocí známé `docker build` formátu, [az acr sestavení] [ az-acr-build] příkaz v rozhraní příkazového řádku Azure přebírá místní kontext, je odesílá do služby ACR buildu a ve výchozím nastavení, nahraje do jeho registru na sestavenou image dokončení. ACR buildu se řídí vaše geograficky replikované registry povolení rozptýleného vývojovým týmům využívat nejbližší replikovaného registru.

ACR buildu je navržena jako primitivní životní cyklus kontejneru. Například integrate ACR buildu do vašeho řešení CI/CD. Spuštěním [az login] [ az-login] s [instanční objekt služby][az-login-service-principal], pak vaše řešení CI/CD setkat [az acr buildu] [ az-acr-build] příkazy a podívejte se na obrázku sestavení.

Další informace o použití rychlé sestavení v prvním kurzu ACR buildu [sestavování imagí kontejneru v cloudu s Azure Container Registry Build](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatické sestavení na potvrzení zdrojového kódu

Použití ACR buildu pro automatickou aktivaci image kontejneru sestavení, když je kód potvrzen do úložiště Git. Vytvoření úlohy, konfigurovat pomocí příkazového řádku Azure [az acr-úloha sestavení][az-acr-build-task], umožňují zadat úložiště Git a volitelně větve a souboru Dockerfile. Pokud váš tým potvrzení kódu do úložiště, webhook služby ACR buildu vytvořené aktivuje sestavení Image kontejneru, který je definován v úložišti.

Zjistěte, jak aktivovat sestavení na potvrzení zdrojového kódu v kurzu druhý ACR buildu [image kontejneru automatizace sestavení s Azure Container Registry Build](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizace operačního systému a používání dílčích oprav framework

Výkon ACR buildu skutečně vylepšit svůj kanál sestavení kontejneru pochází z jeho schopnost zjišťovat aktualizace základní Image. Při aktualizace základní image je nahrány do vašeho registru, ACR buildu můžete automaticky sestavení jakékoli aplikace bitové kopie na jejím základě.

Image kontejnerů může být široce rozdělena do *základní* bitové kopie a *aplikace* bitové kopie. Základní Image obvykle zahrnují operačního systému a aplikačních architektur, podle kterých je aplikace sestavená, společně s další vlastní nastavení. Tyto základní Image představují samy o sobě obvykle založené na veřejné Image upstream, například: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], nebo [Node.js][base-node]. Několik imagí aplikace může sdílet společný základní image.

Když framework image operačního systému nebo aplikace se aktualizuje pomocí nadřazeného funkce maintainer, například s důležité opravy zabezpečení operačního systému, budete muset taky aktualizovat základní Image zahrnout důležité opravy. Každá image aplikace musíte pak také znovu sestavit, aby zahrnují tyto nadřazeného opravy nyní součástí základní image.

Protože ACR buildu dynamicky zjišťuje závislosti základní image, při sestavení image kontejneru, může zjistit, když dojde k aktualizaci image aplikace základní image. S jednou předem [úloha sestavení](container-registry-tutorial-base-image-update.md#create-build-task), ACR buildu pak **automaticky znovu sestaví všechny image aplikace** za vás. Toto automatické zjišťování a znovu sestavit, ACR buildu šetří čas a úsilí obvykle potřebnou k ruční sledování a aktualizaci každého aplikace bitové kopie odkazující na aktualizované základní image.

Další informace o operačním systému a rozhraní framework opravy chyb ve třetím kurzu ACR buildu [automatizace image založena na aktualizací základních imagí s Azure Container Registry Build](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Aktivační událost aktualizace základní image pro počáteční verzi preview, sestavení pouze v případě imagí základní a aplikace jsou umístěny ve stejné Azure container registry nebo veřejně přístupné úložišť v Docker Hubu.

## <a name="next-steps"></a>Další postup

Až budete připraveni k automatizaci operačního systému a rozhraní framework opravy chyb ve vytváření imagí kontejnerů v cloudu, přečtěte si část třídílné ACR buildu série kurzů.

> [!div class="nextstepaction"]
> [Sestavení imagí kontejnerů v cloudu se službou Azure Container Registry Build](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
