---
title: Automatizace sestavování a opravování imagí kontejneru pomocí Azure Container Registrych úloh (úlohy ACR)
description: Úvod k ACR úlohám, sadě funkcí v Azure Container Registry, která poskytuje zabezpečené, automatizované vytváření imagí kontejnerů, správu a opravy v cloudu.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: bc32ce59a7ec99278fb193f375d4ca945c227d2f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172192"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizace sestavení a údržby imagí kontejneru pomocí úloh ACR

Kontejnery poskytují nové úrovně virtualizace a izolují závislosti aplikací a vývojářů od infrastruktury a provozních požadavků. Co zůstává, ale je potřeba řešit, jak se tato virtualizace aplikace spravuje, a jak se je opravovat v životním cyklu kontejneru.

## <a name="what-is-acr-tasks"></a>Co jsou ACR úkoly?

**ACR úkoly** jsou sadou funkcí v rámci Azure Container Registry. Poskytuje cloudové vytváření imagí kontejnerů pro Linux, Windows a ARM a umožňuje automatizovat [opravy operačních systémů a platforem](#automate-os-and-framework-patching) pro vaše kontejnery Docker. ACR úkoly nedosahují pouze vývoje vývojového cyklu vnitřních smyček do cloudu pomocí sestavení imagí kontejneru na vyžádání, ale také umožňuje automatizované sestavení při potvrzení zdrojového kódu nebo při aktualizaci základní image kontejneru. Pomocí aktivačních událostí základní aktualizace image můžete automatizovat pracovní postupy pro opravy operačního systému a aplikačního rozhraní, které udržují zabezpečená prostředí a přitom dodržují objekty zabezpečení neměnných kontejnerů.

Sestavování a testování imagí kontejneru s ACR úkoly čtyřmi způsoby:

* [Rychlý úkol](#quick-task): Vytváření a vkládání imagí kontejnerů na vyžádání v Azure, aniž by bylo nutné instalovat místní modul Docker. V cloudu si myslíte `docker build`. `docker push` Sestavte z místního zdrojového kódu nebo z úložiště Git.
* [Sestavit při potvrzení zdrojového kódu](#automatic-build-on-source-code-commit): Umožňuje automaticky spustit sestavení image kontejneru, když je kód uložen do úložiště Git.
* [Sestavit při aktualizaci základní image](#automate-os-and-framework-patching): Spustí sestavení image kontejneru, když se aktualizuje základní image obrázku.
* [Úlohy s více kroky](#multi-step-tasks): Definujte úlohy s více kroky, které sestavují image, spouštějí kontejnery jako příkazy a nahrajte image do registru. Tato funkce ACR úloh podporuje provádění úloh na vyžádání a paralelní sestavení imagí, testování a nabízené operace.

## <a name="quick-task"></a>Rychlý úkol

Cyklus vývoje vnitřních smyček, iterativní proces psaní kódu, sestavování a testování vaší aplikace před potvrzením řízení zdrojového kódu, je ve skutečnosti začátek správy životního cyklu kontejnerů.

Než začnete s prvním řádkem kódu, funkce [Rychlé úlohy](container-registry-tutorial-quick-task.md) ACR úkoly může poskytovat integrované vývojové prostředí tím, že převede přesměrování sestavení imagí kontejneru do Azure. Pomocí rychlých úloh můžete ověřit své automatizované definice sestavení a zachytit potenciální problémy před potvrzením kódu.

Pomocí známého `docker build` formátu převezme příkaz [AZ ACR Build][az-acr-build] v Azure CLI *kontext* (sadu souborů k sestavení), odešle IT úlohy ACR a ve výchozím nastavení po dokončení vloží do svého registru vytvořenou image.

Úvod najdete v tématu rychlý Start k [sestavení a spuštění image kontejneru](container-registry-quickstart-task-cli.md) v Azure Container Registry.  

Následující tabulka ukazuje několik příkladů podporovaných umístění kontextu pro úlohy ACR:

| Umístění kontextu | Popis | Příklad |
| ---------------- | ----------- | ------- |
| Místní systém souborů | Soubory v adresáři v místním systému souborů. | `/home/user/projects/myapp` |
| Větev hlavní větve GitHubu | Soubory v hlavní větvi úložiště GitHub (nebo jiné výchozí).  | `https://github.com/gituser/myapp-repo.git` |
| Větev GitHubu | Konkrétní větev úložiště GitHubu.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Podsložka GitHubu | Soubory v podsložce v úložišti GitHubu. Příklad zobrazuje kombinaci specifikace větve a podsložky. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Vzdálená tarballu | Soubory v komprimovaném archivu na vzdáleném serveru webserver. | `http://remoteserver/myapp.tar.gz` |

Ve výchozím nastavení ACR úlohy vytváří image pro Linux OS a amd64. `--platform` Zadejte značku pro vytváření imagí Windows nebo imagí Linux pro jiné architektury. Zadejte operační systém a volitelně podporovanou architekturu ve formátu operačního systému/architektury (například `--platform Linux/arm`). V případě architektur ARM můžete volitelně zadat variantu ve formátu OS/Architecture/variant (například `--platform Linux/arm64/v8`):

| OS | Architektura|
| --- | ------- | 
| Linux | AMD<br/>ARM<br/>arm64<br/>386 |
| Windows | AMD |

ACR úkoly jsou navržené jako primitivní životní cyklus kontejneru. Například Integrujte úlohy ACR do řešení CI/CD. Po provedení příkazu [AZ Login][az-login] s [instančním objektem][az-login-service-principal]by vaše řešení CI/CD mohl vydat příkazy [AZ ACR Build][az-acr-build] , aby se aktivovaly sestavení imagí.

Naučte se používat rychlé úlohy v prvním kurzu ACR Tasks, [sestavovat image kontejnerů v cloudu s Azure Container Registry úkoly](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatické sestavení při potvrzení zdrojového kódu

Použijte úlohy ACR k automatickému spuštění sestavení image kontejneru, když se kód potvrdí do úložiště Git v GitHubu nebo v Azure DevOps. Úlohy sestavení lze konfigurovat pomocí příkazu Azure CLI [AZ ACR Task][az-acr-task], umožňují zadat úložiště Git a volitelně také větev a souboru Dockerfile. Když váš tým potvrdí kód do úložiště, ACR úkoly – vytvořené webhookem spustí sestavení image kontejneru definované v úložišti.

> [!IMPORTANT]
> Pokud jste dříve vytvořili úkoly ve verzi Preview pomocí `az acr build-task` příkazu, je nutné tyto úlohy znovu vytvořit pomocí příkazu [AZ ACR Task][az-acr-task] .

Naučte se, jak aktivovat sestavení v potvrzení zdrojového kódu v druhém kurzu ACR úlohy, [Automatizujte sestavení imagí kontejneru pomocí úloh Azure Container Registry](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizace oprav operačního systému a rozhraní

Výkon úloh ACR, které mají skutečně vylepšit pracovní postup sestavení kontejneru, pochází z jeho schopnosti detekovat aktualizaci základní image. Po vložení aktualizované základní image do registru nebo obnovení základní image ve veřejném úložišti, jako je například Docker Hub, můžou úlohy ACR automaticky vytvářet na základě této aplikace bitové kopie aplikací.

Image kontejnerů se dají široce rozdělit do *základních* imagí a imagí *aplikací* . Základní image typicky obsahují operační systém a aplikační architektury, na kterých je vaše aplikace sestavená, spolu s dalšími úpravami. Tyto základní image jsou obvykle založeny na veřejných nadřazených obrázcích, například: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet]nebo [Node. js][base-node]. Několik imagí vaší aplikace může sdílet společný základní obrázek.

Pokud je v případě, že je nadřazeným nástrojem pro správu a údržbu image architektury operačního systému nebo aplikace, třeba s kritickou opravou zabezpečení operačního systému, je potřeba aktualizovat také základní image, aby zahrnovaly kritickou opravu. Každý obrázek aplikace musí být pak znovu sestaven, aby zahrnoval tyto opravy pro odesílání dat, které jsou nyní součástí základní image.

Vzhledem k tomu, že úlohy ACR dynamicky zjišťují základní závislosti obrázků při vytváření image kontejneru, může rozpoznat, kdy se aktualizuje základní obrázek image aplikace. S jedním předkonfigurovaným [úkolem sestavení](container-registry-tutorial-base-image-update.md#create-a-task)ACR úkoly **automaticky znovu sestaví každou image aplikace** . Díky této automatické detekci a novému sestavování vám ACR úlohy šetří čas a úsilí obvykle potřebné k ručnímu sledování a aktualizaci jednotlivých imagí a všech imagí aplikace, které odkazují na aktualizovanou základní image.

Úkol ACR sleduje základní aktualizaci obrázku, pokud je základní bitová kopie v jednom z následujících umístění:

* Stejný registr kontejneru Azure, ve kterém se úloha spouští
* Další Azure Container Registry ve stejné oblasti 
* Veřejné úložiště v Docker Hub
* Veřejné úložiště v Microsoft Container Registry

Další informace o opravách operačních systémů a rozhraní v rámci třetího kurzu ACR úlohy, [Automatizace sestavení imagí na základě aktualizace základního obrázku pomocí úloh Azure Container Registry](container-registry-tutorial-base-image-update.md).

## <a name="multi-step-tasks"></a>Úlohy s více kroky

Úlohy s více kroky poskytují definice úloh založené na kroku a provádění pro vytváření, testování a opravy imagí kontejnerů v cloudu. Kroky úlohy definují jednotlivá sestavení image kontejneru a operací nabízených oznámení. Mohou také definovat spuštění jednoho nebo více kontejnerů, u každého kroku pomocí kontejneru jako prostředí pro spuštění.

Můžete například vytvořit úlohu s více kroky, která automatizuje následující:

1. Sestavení image webové aplikace
1. Spuštění kontejneru webové aplikace
1. Sestavení image testu webové aplikace
1. Spusťte test kontejneru webové aplikace, který provádí testy proti běžícímu kontejneru aplikace.
1. Pokud testy projde, sestavte balíček pro archivaci grafu Helm.
1. `helm upgrade` Provedení použití nového balíčku pro archivaci grafu Helm

Úlohy s více kroky umožňují rozdělit sestavování, spouštění a testování obrázku do více sestavených kroků s podporou mezikrokových závislostí. Díky úlohám s více kroky v ACR úlohám máte podrobnější kontrolu nad vytvářením, testováním a pracovními postupy při opravách imagí.

Přečtěte si o úlohách s více kroky při [spouštění více kroků sestavení, testování a oprav úloh v ACR úlohách](container-registry-tasks-multi-step.md).

## <a name="view-task-logs"></a>Zobrazit protokoly úloh

Každý běh úlohy generuje výstup protokolu, který můžete zkontrolovat, abyste zjistili, jestli se kroky úlohy úspěšně spustily. Pokud k aktivaci úlohy použijete příkaz [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)nebo [AZ ACR Task Run](/cli/azure/acr/task#az-acr-task-run) , výstup protokolu pro spuštění úlohy se streamuje do konzoly a také se uloží pro pozdější načtení. Zobrazte protokoly spuštění úlohy ve Azure Portal nebo použijte příkaz [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

Od července 2019 budou data a protokoly pro úlohy spuštěné v registru standardně uchovány po dobu 30 dnů a pak automaticky vymazány. Pokud chcete archivovat data pro spuštění úlohy, povolte archivaci pomocí příkazu [AZ ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) . Následující příklad umožňuje archivaci úlohy spustit *CF11* v registru *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Další kroky

Až budete připraveni k automatizaci automatických oprav operačního systému a architektury sestavením imagí kontejneru v cloudu, podívejte se na [řadu kurzů ACR úloh](container-registry-tutorial-quick-task.md)tři části.

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
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
