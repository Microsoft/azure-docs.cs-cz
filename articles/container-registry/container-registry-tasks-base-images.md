---
title: Aktualizace základních bitových obrázků – úkoly
description: Další informace o základních ibi pro image kontejneru aplikace a o tom, jak základní aktualizace bitové kopie můžete aktivovat úlohu registru kontejnerů Azure.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617928"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Aktualizace základních bitových obrázků pro úkoly ACR

Tento článek obsahuje základní informace o aktualizacích základní bitové kopie aplikace a o tom, jak tyto aktualizace můžou aktivovat úlohu registru kontejnerů Azure.

## <a name="what-are-base-images"></a>Co jsou základní obrázky?

Dockerfiles definující většinu imitek kontejneru určují nadřazenou bitovou kopii, ze které je obraz založen, často označovaný jako jeho *základní bitová kopie*. Základní image obvykle obsahují operační systém, například [Alpine Linux][base-alpine] nebo [Windows Nano Server][base-windows], na kterém jsou použity zbývající vrstvy kontejneru. Můžou také obsahovat architektury aplikace, jako je třeba [Node.js][base-node] nebo [.NET Core][base-dotnet]. Tyto základní obrázky jsou samy o sobě obvykle založeny na veřejných upstream image. Některé obrázky aplikace mohou sdílet společnou základní bitovou kopii.

Základní image často aktualizuje maintainer image, aby zahrnovala nové funkce nebo vylepšení operačního systému nebo architektury v imagi. Dalším běžným důvodem pro aktualizaci základní image jsou opravy zabezpečení. Když dojde k těmto aktualizacím upstream, musíte také aktualizovat základní image tak, aby zahrnovala kritickou opravu. Každá bitová kopie aplikace pak musí být také znovu sestavena tak, aby zahrnovala tyto upstream opravy, které jsou nyní součástí základní bitové kopie.

V některých případech, například soukromý vývojový tým, může základní bitová kopie zadat více než operační systém nebo rámec. Základní bitová kopie může být například obrázek sdílené součásti služby, který je třeba sledovat. Členové týmu může být nutné sledovat tuto základní bitovou kopii pro testování nebo je třeba pravidelně aktualizovat image při vývoji bitových kopií aplikace.

## <a name="track-base-image-updates"></a>Sledování aktualizací základního obrazu

ACR Tasks zahrnuje možnost, která za vás při aktualizaci základní image kontejnerů automaticky tyto image sestaví.

ACR Úlohy dynamicky zjišťuje závislosti základní image při vytváření image kontejneru. V důsledku toho může zjistit, kdy je aktualizována základní bitová kopie aplikace. S jednou předkonfigurovanou úlohou sestavení mohou úlohy ACR automaticky znovu sestavit každou bitovou kopii aplikace, která odkazuje na základní bitovou kopii. Díky této automatické detekci a opětovnému sestavení vám úlohy ACR ušetří čas a úsilí, které jsou obvykle nutné k ručnímu sledování a aktualizaci každé bitové kopie aplikace odkazující na aktualizovanou základní bitovou kopii.

## <a name="base-image-locations"></a>Umístění základního obrázku

Pro sestavení image z Dockerfile, úloha ACR detekuje závislosti na základních irekcích v následujících umístěních:

* Stejný registr kontejnerů Azure, ve kterém je úloha spuštěna
* Jiný soukromý registr kontejnerů Azure ve stejné nebo jiné oblasti 
* Veřejné úložiště v Docker Hubu 
* Veřejné ukládání do pou v registru kontejnerů Microsoft

Pokud základní bitová `FROM` kopie zadaná v příkazu je umístěna v jednom z těchto umístění, úloha ACR přidá zavěšení, aby bylo zajištěno, že obraz bude znovu sestaven při každé aktualizaci základny.

## <a name="additional-considerations"></a>Další aspekty

* **Základní obrazy pro obrazy aplikace** - V současné době úloha ACR sleduje pouze aktualizace základních bitových kopií pro obrazy aplikace *(runtime).* Nesleduje aktualizace základní bitové kopie pro mezilehlé *(buildtime)* bitové kopie používané ve vícestupňových souborech Dockerfiles.  

* **Ve výchozím nastavení povoleno** – Při vytváření úlohy ACR pomocí příkazu [az acr task create][az-acr-task-create] je ve výchozím nastavení *úloha povolena* pro aktivaci aktualizací základní bitové kopie. To znamená, `base-image-trigger-enabled` že vlastnost je nastavena na True. Pokud chcete zakázat toto chování v úloze, aktualizujte vlastnost false. Spusťte například následující příkaz [az acr task update:][az-acr-task-update]

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Aktivační událost ke sledování závislostí** – Chcete-li povolit úlohu ACR k určení a sledování závislostí image kontejneru , které obsahují jeho základní bitovou kopii, musíte nejprve spustit úlohu k sestavení bitové kopie **alespoň jednou**. Můžete například spustit úlohu ručně pomocí příkazu [az acr task run.][az-acr-task-run]

* **Stabilní značka pro základní obraz** – Chcete-li spustit úlohu při aktualizaci základního obrazu, musí mít základní bitová kopie *stabilní* značku, například `node:9-alpine`. Toto značkování je typické pro základní bitovou kopii, která je aktualizována os a framework opravy na nejnovější stabilní verzi. Pokud je základní bitová kopie aktualizována novou značkou verze, nespustí úlohu. Další informace o označování obrázků naleznete v [doporučených postupech](container-registry-image-tag-version.md). 

* **Další aktivační události úlohy** – V úloze spouštěné aktualizacemi základní bitové kopie můžete také povolit aktivační události na základě [potvrzení zdrojového kódu](container-registry-tutorial-build-task.md) nebo [plánu](container-registry-tasks-scheduled.md). Aktualizace základníbitové kopie může také spustit [úlohu ve více krocích](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Další kroky

Podívejte se na následující kurzy pro scénáře pro automatizaci sestavení bitové kopie aplikace po aktualizaci základní bitové kopie:

* [Automatizace sestavení image kontejneru při aktualizaci základní bitové kopie ve stejném registru](container-registry-tutorial-base-image-update.md)

* [Automatizace sestavení image kontejneru při aktualizaci základní bitové kopie v jiném registru](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
