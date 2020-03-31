---
title: Přehled úloh ACR
description: Úvod k úlohám ACR, sada funkcí v registru kontejnerů Azure, která poskytuje zabezpečené a automatizované vytváření image kontejnerů, správu a opravy v cloudu.
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: 4fda57c1d7c866f2e6f72b04d75e53f91e995baf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087281"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizace sestavení a údržby image kontejneru pomocí úloh ACR

Kontejnery poskytují nové úrovně virtualizace, izolace závislostí aplikací a vývojářů od infrastruktury a provozních požadavků. Co však zůstává, je potřeba řešit, jak je virtualizace této aplikace spravována a opravena v průběhu životního cyklu kontejneru.

## <a name="what-is-acr-tasks"></a>Co jsou úkoly ACR?

**Úlohy ACR** je sada funkcí v rámci registru kontejnerů Azure. Poskytuje cloudové vytváření bitových bitových obrázků kontejnerů pro [platformy](#image-platforms) včetně Linuxu, Windows a ARM a může automatizovat [opravy operačního systému a architektury](#automate-os-and-framework-patching) pro vaše kontejnery Dockeru. Úlohy ACR nejen rozšiřuje váš vývojový cyklus "vnitřní smyčky" do cloudu pomocí sestavení image kontejneru na vyžádání, ale také umožňují automatické sestavení spouštěné aktualizacemi zdrojového kódu, aktualizacemi základní bitové kopie kontejneru nebo časovači. Například s aktivační události aktualizace základní image, můžete automatizovat operační systém a aplikační framework opravy pracovního postupu, udržování zabezpečených prostředí při dodržování zásad neměnné kontejnery.

## <a name="task-scenarios"></a>Scénáře úkolů

ACR Úlohy podporuje několik scénářů pro vytváření a údržbu iimages kontejneru a další artefakty. Podrobnosti naleznete v následujících částech tohoto článku.

* **[Rychlá úloha](#quick-task)** – sestavení a nabízení image jednoho kontejneru do registru kontejnerů na vyžádání v Azure, bez nutnosti místní instalace Docker Engine. Přemýšlejte, `docker build` `docker push` v oblaku.
* **Automaticky spuštěné úlohy** – Povolte jednu nebo více *aktivačních událostí* k vytvoření bitové kopie:
  * **[Aktivační událost při aktualizaci zdrojového kódu](#trigger-task-on-source-code-update)** 
  * **[Aktivační událost na aktualizaci základního obrázku](#automate-os-and-framework-patching)** 
  * **[Aktivační událost podle plánu](#schedule-a-task)** 
* **[Vícekrokový úkol](#multi-step-tasks)** – Rozšiřte možnosti sestavení a nabízení jednoho obrázku úloh ACR pomocí vícekrokových pracovních postupů založených na více kontejnerech. 

Každá úloha ACR má přidružený [kontext zdrojového kódu](#context-locations) - umístění sady zdrojových souborů použitých k vytvoření image kontejneru nebo jiného artefaktu. Příklady kontextů zahrnují úložiště Git nebo místní souborový systém.

Úlohy mohou také využívat [možnosti spouštění proměnných](container-registry-tasks-reference-yaml.md#run-variables), takže můžete znovu použít definice úloh a standardizovat značky pro obrazy a artefakty.

## <a name="quick-task"></a>Rychlý úkol

Vývojový cyklus vnitřní smyčky, iterativní proces psaní kódu, vytváření a testování aplikace před potvrzením správy zdrojového kódu, je ve skutečnosti začátkem správy životního cyklu kontejneru.

Před potvrzením prvního řádku kódu může funkce [rychlých úloh](container-registry-tutorial-quick-task.md) ACR Tasks poskytovat integrované vývojové prostředí převedením sestavení image kontejneru do Azure. Pomocí rychlých úkolů můžete ověřit automatické definice sestavení a zachytit potenciální problémy před potvrzením kódu.

Pomocí známého `docker build` formátu příkaz [az acr build][az-acr-build] v azure cli přebírá [kontext](#context-locations) (sadu souborů k sestavení), odešle úkoly ACR a ve výchozím nastavení odešle vytvořenou bitovou kopii do svého registru po dokončení.

Úvod najdete v tématu rychlý start [k sestavení a spuštění image kontejneru](container-registry-quickstart-task-cli.md) v registru kontejnerů Azure.  

ACR Úkoly je navržen jako primitivní životního cyklu kontejneru. Můžete například integrovat úlohy ACR do řešení CI/CD. Spuštěním [az přihlášení][az-login] s [instančním objektem][az-login-service-principal], vaše CI / CD řešení by pak vydat [az acr sestavení][az-acr-build] příkazy k zahájení image staví.

Zjistěte, jak používat rychlé úkoly v prvním kurzu Úkoly ACR, [Vytvářet ibi kontejnerů v cloudu pomocí úloh registru kontejnerů Azure](container-registry-tutorial-quick-task.md).

> [!TIP]
> Pokud chcete vytvořit a push image přímo ze zdrojového kódu, bez Dockerfile, Azure Container Registry poskytuje [az acr pack sestavení][az-acr-pack-build] příkaz (náhled). Tento nástroj vytvoří a odešle bitovou kopii ze zdrojového kódu aplikace pomocí [cloudových nativních buildpacků](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Úloha spuštění při aktualizaci zdrojového kódu

Aktivujte sestavení image kontejneru nebo úlohu s více kroky, když je potvrzený kód nebo je žádost o přijetí dál platnosti nebo aktualizace do veřejného nebo soukromého úložiště Git v GitHubu nebo Azure DevOps. Například nakonfigurujte úlohu sestavení pomocí příkazu Azure CLI [az az úlohy vytvoření][az-acr-task-create] zadáním úložiště Git a volitelně větev a Dockerfile. Když váš tým aktualizuje kód v úložišti, webhook vytvořený úkoly ACR aktivuje sestavení image kontejneru definované v úložišti. 

Úkoly ACR podporují následující aktivační události, když jako kontext úlohy nastavíte úložiště Git:

| Trigger | Ve výchozím nastavení povolená |
| ------- | ------------------ |
| Potvrzení | Ano |
| Žádost o přijetí změn | Ne |

Chcete-li nakonfigurovat aktivační událost aktualizace zdrojového kódu, musíte úkolu poskytnout osobní přístupový token (PAT) pro nastavení webového háku ve veřejném nebo soukromém úložišti GitHub nebo Azure DevOps.

> [!NOTE]
> V současné době acr úkoly nepodporuje potvrzení nebo žádosti o vyžádat aktivační události v úložiště GitHub Enterprise.

Zjistěte, jak aktivovat sestavení na potvrzení zdrojového kódu v druhém kurzu Úkoly [ACR, Automatizovat sestavení image kontejneru pomocí úloh registru kontejnerů Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizace os a framework oprav

Výkon Úlohy ACR skutečně zlepšit váš pracovní postup sestavení kontejneru pochází z jeho schopnost detekovat aktualizaci *základní image*. Funkce většiny iobrazů kontejneru, základní bitová kopie je nadřazená bitová kopie, na které je založen jeden nebo více iobrazů aplikace. Základní bitové kopie obvykle obsahují operační systém a někdy i aplikační architektury. 

Úlohu ACR můžete nastavit ke sledování závislosti na základní bitové kopii při vytváření bitové kopie aplikace. Když je aktualizovaná základní bitová kopie odesílána do registru nebo je základní bitová kopie aktualizována ve veřejném repo, například v Docker Hubu, úlohy ACR mohou automaticky vytvářet libovolné image aplikace založené na něm.
Díky této automatické detekci a opětovnému sestavení vám úlohy ACR ušetří čas a úsilí, které jsou obvykle nutné k ručnímu sledování a aktualizaci každé bitové kopie aplikace odkazující na aktualizovanou základní bitovou kopii.

Přečtěte si další informace o [aktivačních událostech aktualizace základní bitové kopie](container-registry-tasks-base-images.md) pro úlohy ACR. A zjistěte, jak spustit sestavení bitové kopie při zasunutí základní bitové kopie do registru kontejnerů v kurzu [Automatizovat image kontejneru při aktualizaci základní bitové kopie v registru kontejnerů Azure](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Naplánování úlohy

Volitelně naplánujte úkol nastavením jedné nebo více *aktivačních událostí časovače* při vytváření nebo aktualizaci úkolu. Plánování úlohy je užitečné pro spouštění úloh kontejnerů podle definovaného plánu nebo pro spuštění operací údržby nebo testů na irekách, které jsou pravidelně nabízeny do registru. Podrobnosti naleznete [v tématu Spuštění úlohy ACR podle definovaného plánu](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Úlohy s více kroky

Vícekrokové úlohy poskytují definici a provádění úloh na základě kroků pro vytváření, testování a opravy iobrazek kontejnerů v cloudu. Kroky úlohdefinované v [souboru YAML](container-registry-tasks-reference-yaml.md) určují jednotlivé operace sestavení a nabízených operací pro ifotky kontejnerů nebo jiné artefakty. Mohou také definovat spuštění jednoho nebo více kontejnerů, u každého kroku pomocí kontejneru jako prostředí pro spuštění.

Můžete například vytvořit úlohu ve více krocích, která automatizuje následující:

1. Vytvoření bitové kopie webové aplikace
1. Spuštění kontejneru webové aplikace
1. Vytvoření testovací bitové kopie webové aplikace
1. Spuštění testovacího kontejneru webové aplikace, který provádí testy proti spuštěnému kontejneru aplikace
1. Pokud testy projdou, vytvořte archivní balíček grafu Helm
1. Provedení `helm upgrade` nového balíčku archivu grafu Helm

Vícekrokové úlohy umožňují rozdělit budovu, spuštění a testování bitové kopie na kompozitnější kroky s podporou závislostí mezi kroky. S vícekrokovými úkoly v úlohách ACR máte podrobnější kontrolu nad vytvářením obrázků, testováním a os a pracovními postupy oprav architektury.

Informace o vícekrokových úlohách v [běhu úloh vícekrokového sestavení, testování a oprav v úlohách ACR](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Kontextová umístění

V následující tabulce je uvedeno několik příkladů podporovaných umístění kontextu pro úkoly ACR:

| Umístění kontextu | Popis | Příklad |
| ---------------- | ----------- | ------- |
| Místní souborový systém | Soubory v adresáři v místním souborovém systému. | `/home/user/projects/myapp` |
| Hlavní větev GitHubu | Soubory v rámci hlavní (nebo jiné výchozí) větve veřejného nebo soukromého úložiště GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Větev GitHubu | Konkrétní větev veřejného nebo soukromého úložiště GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Podsložka GitHub | Soubory v rámci podsložky ve veřejném nebo soukromém úložišti GitHub. Příklad ukazuje kombinaci specifikace větve a podsložky. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Potvrzení GitHubu | Konkrétní potvrzení ve veřejném nebo soukromém úložišti GitHub. Příklad ukazuje kombinaci hash potvrzení (SHA) a specifikace podsložky. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Podsložka Azure DevOps | Soubory v rámci podsložky ve veřejném nebo soukromém azure repo. Příklad ukazuje kombinaci specifikace větve a podsložky. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Vzdálený tarball | Soubory v komprimovaném archivu na vzdáleném webovém serveru. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Při použití privátního úložiště Git jako kontextu pro úlohu je třeba zadat osobní přístupový token (PAT).

## <a name="image-platforms"></a>Platformy obrázků

Ve výchozím nastavení acr úkoly vytváří image pro operační systém Linux a architekturu amd64. Zadejte `--platform` značku pro vytváření bitových kopií systému Windows nebo linuxových obrazů pro jiné architektury. Zadejte operační systém a volitelně podporovanou architekturu ve `--platform Linux/arm`formátu OS/architecture (například). Pro architektury ARM volitelně určete variantu ve formátu OS/architecture/variant (například `--platform Linux/arm64/v8`):

| Operační systém | Architektura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>rameno64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Zobrazení výstupu úkolu

Každé spuštění úlohy generuje výstup protokolu, který můžete zkontrolovat a zjistit, zda byly kroky úlohy úspěšně spuštěny. Když spustíte úlohu ručně, výstup protokolu pro spuštění úlohy je streamován do konzoly a také uložen pro pozdější načítání. Když se úloha automaticky spustí, například potvrzením zdrojového kódu nebo aktualizací základní bitové kopie, protokoly úloh se uloží pouze. Zobrazení protokolů spuštění na webu Azure Portal nebo použijte příkaz [az acr protokoly úloh.](/cli/azure/acr/task#az-acr-task-logs)

Další informace o [zobrazení a správě protokolů úkolů](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Další kroky

Až budete připraveni automatizovat sestavení image kontejneru a údržbu v cloudu, podívejte se na [výukové řady Úlohy ACR](container-registry-tutorial-quick-task.md).

Volitelně nainstalujte [rozšíření Docker u Visual Studia code](https://code.visualstudio.com/docs/azure/docker) a rozšíření účtu [Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pro práci s registry kontejnerů Azure. Vyžádat a push image do registru kontejneru Azure nebo spustit úlohy ACR, to vše v rámci kódu Visual Studio.

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
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
