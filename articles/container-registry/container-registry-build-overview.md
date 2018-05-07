---
title: Automatizovat operačního systému a framework opravy s Azure Container registru sestavení (ACR sestavení)
description: Úvod do ACR sestavení, sada funkcí v registru kontejner Azure, která poskytuje zabezpečené, automatizované sestavení image kontejneru a opravy chyb v cloudu.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 05/01/2018
ms.author: marsma
ms.openlocfilehash: 7506351c0d65fb167136478d2dc383cdabd81835
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatizovat operačního systému a framework opravy s ACR sestavení

Kontejnery zadejte nové úrovně virtualizace, izolování aplikací a vývojáře závislostí z infrastruktury a provozních požadavků. Co je ještě, ale je potřeba vyřešit, jak opravit této aplikace application virtualization.

**Sestavení ACR**, sada funkcí v rámci Azure registru kontejner, poskytuje nejen kontejneru nativní bitové kopie sestavení možnost, ale také automatizuje [operačního systému a opravy chyb framework](#automate-os-and-framework-patching) pro Docker kontejnerů.

> [!IMPORTANT]
> ACR sestavení je v současné době ve verzi preview a je podporován pouze registrech kontejner Azure v **východní USA** a **západní Evropa** oblasti. Verze Preview jsou k dispozici pro vás, za předpokladu, že souhlasíte se [dodatečné podmínky použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="what-is-acr-build"></a>Co je ACR sestavení?

Sestavení Azure kontejneru registru je služba Azure nativní kontejneru image sestavení. Sestaví sestavení ACR umožňuje vnitřní smyčky vývoj v cloudu pomocí bitové kopie na vyžádání kontejneru a aktualizovat automatizovaných sestaveních na zdrojový kód potvrzení a základní bitovou kopii.

Aktivační události kontejneru image vytvoří automaticky když kódu se zaměřuje na úložiště Git, nebo když se aktualizuje základní bitovou kopii kontejneru. S aktivační události update základní bitovou kopii, je možné automatizovat operačním systému a architektura aplikace na opravy pracovního postupu, zachování zabezpečení prostředí při se objekty neměnné kontejnerů.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Rychlé sestavení: vnitřní smyčky rozšířit do cloudu

Začátek – Správa životního cyklu spuštěna před vývojáři potvrdit jejich prvních řádků kódu. ACR sestavení [rychlé vytvoření](container-registry-tutorial-quick-build.md) funkce umožňuje získat integrované místní smyčky vnitřní vývojové prostředí, snižování zátěže sestavení do Azure. S rychlé sestavení můžete ověřit automatizované sestavení definic před potvrzením kódu.

Pomocí známé `docker build` formát, [az acr sestavení] [ az-acr-build] příkaz v rozhraní příkazového řádku Azure přebírá kontext místní, odešle do služby ACR sestavení a ve výchozím nastavení, nabízených oznámení bitovou kopii integrovaný do jeho registru na dokončení. Sestavení ACR odpovídá vaší registrech geograficky replikované povolení rozmístěné vývojové týmy využít nejbližší replikované registru. Verzi Preview sestavení ACR je k dispozici v oblasti Východ USA a západní Evropa.

Sestavení ACR slouží jako kontejner životní primitivní. Například integrované ACR sestavení do řešení CI/CD. Spuštěním [az přihlášení] [ az-login] s [instanční objekt][az-login-service-principal], pak může vydávat řešení CI/CD [az acr sestavení] [ az-acr-build] příkazy, které ji bitové kopie sestavení.

Naučte se používat rychlé sestavení z prvního kurzu ACR sestavení, [vytvářet bitové kopie kontejneru v cloudu s Azure Container registru sestavení](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatické sestavení na potvrzení zdrojového kódu

Použijte ACR sestavení automaticky spouštět bitovou kopii kontejneru sestavení, když kód se zaměřuje na úložiště Git. Úlohy, konfigurovat pomocí příkazu příkazového řádku Azure CLI sestavení [az acr-úlohu sestavení][az-acr-build-task], umožňují určit úložiště Git a volitelně větve a soubor Docker. Pokud váš tým potvrdí kód do úložiště, webhook ACR sestavení vytvořené aktivuje build bitové kopie kontejneru definované v úložišti.

Zjistěte, jak aktivovat sestavení na potvrzení zdrojového kódu v kurzu druhý ACR sestavení [automatické kontejneru image sestavení s Azure Container registru sestavení](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizovat operačního systému a opravy chyb framework

Výkon sestavení ACR skutečně vylepšit svůj kanál sestavení kontejneru pochází z jeho schopnost rozpoznat aktualizace základní bitovou kopii. Když aktualizovanou bitovou kopii pro základní vložena do registru, ACR sestavení můžete automaticky vytvořit žádné Image aplikací založených na.

Kontejner Image může být široce rozdělena do *základní* bitové kopie a *aplikace* bitové kopie. Vaše základní Image obvykle zahrnují operační systém a architektur aplikací, na kterých je postavena aplikace, spolu s jinými. Tyto základní Image jsou sami obvykle založené na veřejné nadřazeného imagích, například [Alpine Linux] [ base-alpine] nebo [Node.js][base-node]. Některé aplikace obrázků by mohly sdílet běžné základní bitovou kopii.

Když nadřazený funkce maintainer je aktualizováno framework image operačního systému nebo aplikace, například s důležité opravy zabezpečení operačního systému, je nutné také aktualizovat vaše základní Image zahrnout důležité opravy. Každé bitové kopie aplikace musí pak také znovu sestavit zahrnout tyto nadřazeného opravy teď součástí základní bitové kopie.

Protože ACR sestavení dynamicky zjišťuje základní image závislosti, při vytváření obrazem kontejneru, může zjistit při aktualizaci základní bitovou kopii image aplikace. S jedním předkonfigurované [úloze sestavení](container-registry-tutorial-base-image-update.md#create-build-task), ACR sestavení potom **automaticky znovu sestaví všechny bitové kopie aplikace** za vás. Toto automatické zjišťování a znovu sestavit, sestavení ACR uloží vytvořením čas a úsilí obvykle vyžaduje, aby ručně sledovat a aktualizovat každé aplikaci bitové kopie odkazující na aktualizovanou základní bitovou kopii.

Další informace o operačního systému a framework opravy v kurzu třetí sestavení ACR [automatické image je založený na aktualizace základní bitovou kopii s Azure Container registru sestavení](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Počáteční verzi Preview sestavení aktivační událost základní image aktualizace pouze v případě, že Image základní a aplikace jsou umístěny ve stejné registru kontejner Azure.

## <a name="next-steps"></a>Další postup

Až budete připraveni k automatizaci operačního systému a framework opravy ve vytváření bitových kopií kontejneru v cloudu, podívejte se na kurz řady ACR sestavení třemi částmi.

> [!div class="nextstepaction"]
> [Vytvářet kontejner bitové kopie v cloudu s Azure Container registru sestavení](container-registry-tutorial-quick-build.md)

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
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
