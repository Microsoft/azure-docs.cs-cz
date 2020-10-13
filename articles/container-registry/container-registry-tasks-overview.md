---
title: Přehled úloh ACR
description: Úvod k ACR úlohám, sadě funkcí v Azure Container Registry, která poskytuje zabezpečené, automatizované vytváření imagí kontejnerů, správu a opravy v cloudu.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 24cc0415fe8756e900a8ea0ce7039f6b4710cf6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488639"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizace sestavení a údržby imagí kontejneru pomocí úloh ACR

Kontejnery poskytují nové úrovně virtualizace a izolují závislosti aplikací a vývojářů od infrastruktury a provozních požadavků. Co zůstává, ale je potřeba řešit, jak se tato virtualizace aplikace spravuje, a jak se je opravovat v životním cyklu kontejneru.

## <a name="what-is-acr-tasks"></a>Co jsou ACR úkoly?

**ACR úkoly** jsou sadou funkcí v rámci Azure Container Registry. Poskytuje cloudové vytváření imagí kontejnerů pro [platformy](#image-platforms) , včetně systémů Linux, Windows a ARM, a umožňuje automatizovat [opravy operačních systémů a platforem](#automate-os-and-framework-patching) pro kontejnery Docker. ACR úkoly nerozšiřují do cloudu pouze vývoj "vnitřních smyček" cyklus vývoje na vyžádání pomocí sestavení imagí kontejneru na vyžádání, ale také umožňuje automatizované sestavení aktivované aktualizacemi zdrojového kódu, aktualizace základní image kontejneru nebo časovače. Například s triggery aktualizace základního obrázku můžete automatizovat pracovní postup opravy operačního systému a rozhraní Application Framework, zachovat zabezpečená prostředí a přitom dodržovat zásady neměnných kontejnerů.

## <a name="task-scenarios"></a>Scénáře úloh

Úlohy ACR podporují několik scénářů pro sestavování a správu imagí kontejneru a další artefakty. Podrobnosti najdete v následujících částech tohoto článku.

* **[Rychlé úlohy](#quick-task)** – Sestavte a nahrajte jednu Image kontejneru do registru kontejneru na vyžádání v Azure, aniž byste museli instalovat místní modul Docker. `docker build` `docker push` V cloudu si myslíte.
* **Automaticky aktivované úlohy** – povolením jedné nebo více *triggerů* sestavíte Image:
  * **[Aktivovat při aktualizaci zdrojového kódu](#trigger-task-on-source-code-update)** 
  * **[Aktivovat aktualizaci základní image](#automate-os-and-framework-patching)** 
  * **[Aktivovat podle plánu](#schedule-a-task)** 
* **[Úloha s více kroky](#multi-step-tasks)** – umožňuje rozšířit možnosti vytváření a nabízených imagí pro úlohy ACR s využitím více kroků a pracovních postupů využívajících více kontejnerů. 

Každý úkol ACR má přidružený [kontext zdrojového kódu](#context-locations) – umístění sady zdrojových souborů používané k sestavení image kontejneru nebo jiného artefaktu. Příklady kontextů zahrnují úložiště Git nebo místní systém souborů.

Úlohy mohou také využít výhod [proměnných spuštění](container-registry-tasks-reference-yaml.md#run-variables), takže můžete znovu použít definice úloh a standardizovat značky pro image a artefakty.

## <a name="quick-task"></a>Rychlý úkol

Cyklus vývoje vnitřních smyček, iterativní proces psaní kódu, sestavování a testování vaší aplikace před potvrzením řízení zdrojového kódu, je ve skutečnosti začátek správy životního cyklu kontejnerů.

Než začnete s prvním řádkem kódu, funkce [Rychlé úlohy](container-registry-tutorial-quick-task.md) ACR úkoly může poskytovat integrované vývojové prostředí tím, že převede přesměrování sestavení imagí kontejneru do Azure. Pomocí rychlých úloh můžete ověřit své automatizované definice sestavení a zachytit potenciální problémy před potvrzením kódu.

Pomocí známého `docker build` formátu převezme příkaz [AZ ACR Build][az-acr-build] v Azure CLI [kontext](#context-locations) (sadu souborů k sestavení), odešle IT úlohy ACR a ve výchozím nastavení po dokončení vloží do svého registru vytvořenou image.

Úvod najdete v tématu rychlý Start k [sestavení a spuštění image kontejneru](container-registry-quickstart-task-cli.md) v Azure Container Registry.  

ACR úkoly jsou navržené jako primitivní životní cyklus kontejneru. Například Integrujte úlohy ACR do řešení CI/CD. Po provedení příkazu [AZ Login][az-login] s [instančním objektem][az-login-service-principal]by vaše řešení CI/CD mohl vydat příkazy [AZ ACR Build][az-acr-build] , aby se aktivovaly sestavení imagí.

Naučte se používat rychlé úlohy v prvním kurzu ACR Tasks, [sestavovat image kontejnerů v cloudu s Azure Container Registry úkoly](container-registry-tutorial-quick-task.md).

> [!TIP]
> Pokud chcete vytvořit a vložit image přímo ze zdrojového kódu bez souboru Dockerfile, Azure Container Registry poskytuje příkaz [AZ ACR Pack Build][az-acr-pack-build] (Preview). Tento nástroj sestaví a nahraje image ze zdrojového kódu aplikace pomocí [cloudového nativního Buildpacksu](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Spustit úlohu při aktualizaci zdrojového kódu

Aktivovat sestavení image kontejneru nebo úlohu s více kroky, když je kód potvrzený, nebo se žádost o přijetí změn provedla nebo aktualizuje na veřejné nebo soukromé úložiště Git v GitHubu nebo v Azure DevOps. Například nakonfigurujete úlohu sestavení pomocí příkazu Azure CLI [AZ ACR Task Create][az-acr-task-create] zadáním úložiště Git a volitelně větví a souboru Dockerfile. Když tým aktualizuje kód v úložišti, ACR úkoly – vytvořené webhookem spustí sestavení image kontejneru definované v úložišti. 

Úlohy ACR podporují následující triggery při nastavení úložiště Git jako kontextu úkolu:

| Trigger | Ve výchozím nastavení povolená |
| ------- | ------------------ |
| Potvrzení | Yes |
| Žádost o přijetí změn | No |

Pokud chcete nakonfigurovat aktivační proceduru aktualizace zdrojového kódu, je nutné zadat úlohu pomocí tokenu PAT (Personal Access token) pro nastavení Webhooku ve veřejném nebo privátním úložišti GitHubu nebo Azure DevOps.

> [!NOTE]
> V současné době ACR úkoly nepodporují potvrzení změn nebo triggery žádostí o přijetí změn v úložištích GitHub Enterprise.

Naučte se, jak aktivovat sestavení v potvrzení zdrojového kódu v druhém kurzu ACR úlohy, [Automatizujte sestavení imagí kontejneru pomocí úloh Azure Container Registry](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizace oprav operačního systému a rozhraní

Výkon úloh ACR, které mají skutečně vylepšit pracovní postup sestavení kontejneru, pochází z jeho schopnosti detekovat aktualizaci *základní image*. Pro většinu imagí kontejnerů je základní image nadřazená image, na které je založena jedna nebo více imagí aplikace. Základní image obvykle obsahují operační systém a někdy aplikační architektury. 

Můžete nastavit úlohu ACR pro sledování závislosti na základní imagi při sestavení image aplikace. Po vložení aktualizované základní image do registru nebo obnovení základní image ve veřejném úložišti, jako je například Docker Hub, můžou úlohy ACR automaticky vytvářet na základě této aplikace bitové kopie aplikací.
Díky této automatické detekci a novému sestavování vám ACR úlohy šetří čas a úsilí obvykle potřebné k ručnímu sledování a aktualizaci jednotlivých imagí a všech imagí aplikace, které odkazují na aktualizovanou základní image.

Další informace o [aktivačních událostech základní aktualizace imagí](container-registry-tasks-base-images.md) pro úlohy ACR A naučíte se, jak aktivovat sestavení image při vložení základní image do registru kontejneru v kurzu [Automatizace sestavení imagí kontejneru, když se v registru kontejnerů Azure aktualizuje základní image](container-registry-tutorial-base-image-update.md) .

## <a name="schedule-a-task"></a>Naplánování úlohy

Volitelně můžete naplánovat úlohu nastavením jedné nebo více *triggerů časovače* při vytváření nebo aktualizaci úlohy. Plánování úlohy je užitečné pro spouštění úloh kontejnerů podle definovaného plánu nebo spuštění operací údržby nebo testů na obrázcích, které se pravidelně automaticky zadávají do registru. Podrobnosti najdete v tématu [spuštění úlohy ACR podle definovaného plánu](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Úlohy s více kroky

Úlohy s více kroky poskytují definice úloh založené na kroku a provádění pro vytváření, testování a opravy imagí kontejnerů v cloudu. Kroky úlohy definované v [souboru YAML](container-registry-tasks-reference-yaml.md) určují jednotlivé operace sestavení a nabízených oznámení pro Image kontejneru nebo jiné artefakty. Mohou také definovat spuštění jednoho nebo více kontejnerů, u každého kroku pomocí kontejneru jako prostředí pro spuštění.

Můžete například vytvořit úlohu s více kroky, která automatizuje následující:

1. Sestavení image webové aplikace
1. Spuštění kontejneru webové aplikace
1. Sestavení image testu webové aplikace
1. Spusťte testovací kontejner webové aplikace, který provádí testy proti běžícímu kontejneru aplikace.
1. Pokud testy projde, sestavte balíček pro archivaci grafu Helm.
1. Provedení `helm upgrade` použití nového balíčku pro archivaci grafu Helm

Úlohy s více kroky umožňují rozdělit sestavování, spouštění a testování obrázku do více sestavených kroků s podporou mezikrokových závislostí. Díky úlohám s více kroky v ACR úlohám máte podrobnější kontrolu nad vytvářením, testováním a pracovními postupy při opravách imagí.

Přečtěte si o úlohách s více kroky při [spouštění více kroků sestavení, testování a oprav úloh v ACR úlohách](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Umístění kontextu

V následující tabulce jsou uvedeny příklady podporovaných umístění kontextu pro úlohy ACR:

| Umístění kontextu | Popis | Příklad |
| ---------------- | ----------- | ------- |
| Místní systém souborů | Soubory v adresáři v místním systému souborů. | `/home/user/projects/myapp` |
| Větev hlavní větve GitHubu | Soubory v rámci hlavní větve (nebo jiné výchozí) ve veřejném nebo privátním úložišti GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Větev GitHubu | Konkrétní větev veřejného nebo privátního úložiště GitHubu.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Podsložka GitHubu | Soubory v podsložce ve veřejném nebo privátním úložišti GitHub. Příklad zobrazuje kombinaci specifikace větve a podsložky. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Potvrzení GitHubu | Konkrétní potvrzení ve veřejném nebo privátním úložišti GitHub. Příklad zobrazuje kombinaci specifikace SHA (commit hash) a podsložky. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Podsložka Azure DevOps | Soubory v podsložce ve veřejném nebo privátním úložišti Azure. Příklad ukazuje kombinaci specifikace větve a podsložek. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Vzdálená tarballu | Soubory v komprimovaném archivu na vzdáleném serveru webserver. | `http://remoteserver/myapp.tar.gz` |
| Artefakt v registru kontejneru | Soubory [artefaktů OCI](container-registry-oci-artifacts.md) v úložišti registru kontejnerů. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> Když jako kontext pro úlohu použijete privátní úložiště Git, musíte zadat token pro osobní přístup (PAT).

## <a name="image-platforms"></a>Image – platformy

Ve výchozím nastavení ACR úlohy vytváří image pro Linux OS a amd64. Zadejte `--platform` značku pro vytváření imagí Windows nebo imagí Linux pro jiné architektury. Zadejte operační systém a volitelně podporovanou architekturu ve formátu operačního systému/architektury (například `--platform Linux/arm` ). V případě architektur ARM můžete volitelně zadat variantu ve formátu OS/Architecture/variant (například `--platform Linux/arm64/v8` ):

| Operační systém | Architektura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Zobrazení výstupu úkolu

Každý běh úlohy generuje výstup protokolu, který můžete zkontrolovat, abyste zjistili, jestli se kroky úlohy úspěšně spustily. Když úlohu spouštíte ručně, výstup protokolu pro spuštění úlohy je streamování do konzoly a také uložený pro pozdější načtení. Když se úkol automaticky aktivuje, například potvrzením zdrojového kódu nebo obnovením základní image, ukládají se jenom protokoly úloh. Zobrazte protokoly spuštění v Azure Portal nebo použijte příkaz [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

Přečtěte si další informace o [zobrazení a správě protokolů úloh](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Další kroky

Až budete připraveni automatizovat sestavení a údržbu imagí kontejneru v cloudu, podívejte se na [řadu kurzů ACR Tasks](container-registry-tutorial-quick-task.md).

Volitelně můžete nainstalovat [rozšíření Docker pro Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) a rozšíření [účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pro práci se službou Azure Container Registry. Vyhrajte a nahrajte image do služby Azure Container registry nebo spouštějte ACR úlohy, a to všechno v rámci Visual Studio Code.

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
