---
title: Automatizace operačního systému a rozhraní framework opravy chyb s Azure Container Registry (ACR úloh)
description: Úvod do úlohy služby ACR, sadu funkcí ve službě Azure Container Registry, která poskytuje zabezpečené, automatizovat sestavení image kontejneru a používání dílčích oprav v cloudu.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: cd2b14dc29f865a162cb1ced605e740a96f7a46a
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329969"
---
# <a name="automate-os-and-framework-patching-with-acr-tasks"></a>Automatizace operačního systému a rozhraní framework opravy chyb s úlohami služby ACR

Kontejnery poskytují nové úrovni, virtualizaci, izolaci závislostí aplikace a pro vývojáře z infrastruktury a provozních požadavků. Co je ještě, ale je potřeba vyřešit, jak je opravit této platformy application virtualization.

## <a name="what-is-acr-tasks"></a>Co je ACR úlohy?

**Úlohy služby ACR** je sada funkcí v rámci Azure Container Registry. Poskytuje vytváření bitové kopie založené na cloudu kontejner pro Linux, Windows a ARM a můžete automatizovat [operačního systému a používání dílčích oprav framework](#automate-os-and-framework-patching) pro kontejnery Dockeru. Úlohy služby ACR nejen rozšiřuje cyklu vývoje "vnitřní smyčky" do cloudu s využitím sestavování imagí kontejneru na vyžádání, ale taky umožňuje automatizované buildy na potvrzení zdrojového kódu nebo při aktualizaci základní image kontejneru. Pomocí aktivační události update základní image, můžete automatizovat vašeho operačního systému a architektura aplikace na opravy chyb pracovního postupu, zachování zabezpečení prostředí při týkajícími se objekty zabezpečení neměnné kontejnerů.

Vytváření a testování imagí kontejnerů s ACR úkoly čtyři způsoby:

* [Rychlých úloh](#quick-task): Vytváření a nasdílení změn kontejneru obrázků na vyžádání, v Azure, bez nutnosti místní instalace modul Docker. Myslíte `docker build`, `docker push` v cloudu. Sestavení z místního zdrojového kódu nebo úložiště Git.
* [Sestavení na zdrojový kód potvrzení](#automatic-build-on-source-code-commit): Automaticky aktivujte sestavení image kontejneru, když je kód potvrzen do úložiště Git.
* [Sestavení na aktualizací základních imagí](#automate-os-and-framework-patching): Aktivujte sestavení image kontejneru, když byl aktualizován této bitové kopie základní image.
* [Vícekrokové úlohy](#multi-step-tasks-preview) (preview): Definujte vícekrokových úkolů, které sestavování imagí, spouštění kontejnerů jako příkazy a nahrávání imagí do registru. Tato funkce ve verzi preview služby ACR úloh podporuje spouštění úlohy na vyžádání a paralelní bitové kopie sestavení, testování a operací push.

## <a name="quick-task"></a>Rychlých úloh

Začátek správy životního cyklu kontejneru je ve skutečnosti cyklu vývoje vnitřní smyčky, iterativní proces psaní kódu, vytváření a testování vaší aplikace před zakoupením do správy zdrojového kódu.

Předtím, než potvrdíte svůj první řádek kódu, ACR úlohy [rychlých úloh](container-registry-tutorial-quick-task.md) funkce může poskytnout sestavení integrované vývojové prostředí přesměrováním svou image kontejneru do Azure. S rychlé úkoly můžete ověřit vaše automatizované definice sestavení a zachycení potenciálních problémů před potvrzením kódu.

Pomocí známé `docker build` formátu, [az acr sestavení] [ az-acr-build] příkaz v rozhraní příkazového řádku Azure má *kontextu* (sadu souborů pro sestavení), odešle ACR úlohy a ve výchozím nastavení, Posune sestavenou image po dokončení jeho registru.

Následující tabulka ukazuje několik příkladů kontextu podporovaná umístění pro úlohy ACR:

| Místní umístění | Popis | Příklad: |
| ---------------- | ----------- | ------- |
| Místní systém souborů | Soubory v adresáři v místním systému souborů. | `/home/user/projects/myapp` |
| Hlavní větve na Githubu | Soubory v předloze (nebo jiné výchozí) větev úložiště GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Větve na Githubu | Konkrétní větvi z úložiště GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub PR | Žádost o přijetí změn v úložišti na Githubu. | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| Podsložky Githubu | Soubory podsložky v úložišti na Githubu. Příklad ukazuje kombinací specifikace žádosti o přijetí změn a podsložky. | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| Vzdálené tarballu | Soubory v komprimovaný archiv na vzdáleném webovém serveru. | `http://remoteserver/myapp.tar.gz` |

Úlohy služby ACR je navržena jako primitivní životní cyklus kontejneru. Například integrate úlohy služby ACR do vašeho řešení CI/CD. Spuštěním [az login] [ az-login] s [instanční objekt služby][az-login-service-principal], pak vaše řešení CI/CD setkat [az acr buildu] [ az-acr-build] příkazy a podívejte se na obrázku sestavení.

Další informace o použití rychlé úkoly v prvním kurzu ACR úlohy [sestavování imagí kontejneru v cloudu pomocí služby Azure Container Registry úlohy](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatické sestavení na potvrzení zdrojového kódu

Použití služby ACR úkoly pro automatickou aktivaci image kontejneru sestavení, když je kód potvrzen do úložiště Git. Vytvoření úlohy, konfigurovat pomocí příkazového řádku Azure [az acr úloh][az-acr-task], umožňují zadat úložiště Git a volitelně větve a souboru Dockerfile. Pokud váš tým potvrzení kódu do úložiště, webhook služby ACR úkoly vytvořeny aktivuje sestavení Image kontejneru, který je definován v úložišti.

> [!IMPORTANT]
> Pokud jste už dříve vytvořili úlohy ve verzi Preview pomocí příkazu `az acr build-task`, tyto úlohy bude potřeba vytvořit znovu pomocí příkazu [az acr task][az-acr-task].

Zjistěte, jak aktivovat sestavení na potvrzení kódu zdroje v druhé kurzu ACR úlohy [image kontejneru automatizace sestavení s Azure Container Registry úkoly](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizace operačního systému a používání dílčích oprav framework

Výkon úloh ACR skutečně vylepšit vaše pracovní postup sestavení kontejneru pochází z jeho schopnost zjišťovat aktualizace základní Image. Při aktualizace základní image je nahrány do vašeho registru, můžete úlohy ACR automaticky sestavení žádné Image aplikací na jejím základě.

Image kontejnerů může být široce rozdělena do *základní* bitové kopie a *aplikace* bitové kopie. Základní Image obvykle zahrnují operačního systému a aplikačních architektur, podle kterých je aplikace sestavená, společně s další vlastní nastavení. Tyto základní Image představují samy o sobě obvykle založené na veřejné Image upstream, například: [Nástroj Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], nebo [Node.js ][base-node]. Několik imagí aplikace může sdílet společný základní image.

Když framework image operačního systému nebo aplikace se aktualizuje pomocí nadřazeného funkce maintainer, například s důležité opravy zabezpečení operačního systému, budete muset taky aktualizovat základní Image zahrnout důležité opravy. Každá image aplikace musíte pak také znovu sestavit, aby zahrnují tyto nadřazeného opravy nyní součástí základní image.

Protože ACR úlohy dynamicky zjišťuje základní image závislostí při sestavení image kontejneru, může zjistit, když dojde k aktualizaci image aplikace základní image. S jednou předem [úloha sestavení](container-registry-tutorial-base-image-update.md#create-a-task), pak úlohy ACR **automaticky znovu sestaví všechny image aplikace** za vás. S tuto automatické zjišťování a znovu sestavit, úlohy ACR šetří čas a úsilí obvykle potřebnou k ruční sledování a aktualizaci každého aplikace bitové kopie odkazující na aktualizované základní image.

Další informace o operačním systému a rozhraní framework opravy chyb ve třetím kurzu ACR úlohy [automatizace image založena na aktualizací základních imagí s Azure Container Registry úkoly](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Základní image aktualizuje aktivační události sestavení pouze v případě imagí základní a aplikace jsou umístěny ve stejné Azure container registry i základní třídy se nachází ve veřejném úložišti Docker Hubu.

## <a name="multi-step-tasks-preview"></a>Úlohy s více kroky (Preview)

Vícekrokových úkolů, funkce ve verzi preview služby ACR úloh obsahuje definice založené na krok úlohy a provádění pro sestavování, testování a opravy chyb imagí kontejnerů v cloudu. Kroky úlohy definují jednotlivá sestavení image kontejneru a operací nabízených oznámení. Mohou také definovat spuštění jednoho nebo více kontejnerů, u každého kroku pomocí kontejneru jako prostředí pro spuštění.

Můžete například vytvořit vícekrokové úlohy, která automatizuje následující:

1. Sestavíte image webové aplikace
1. Spuštění kontejneru webové aplikace
1. Sestavíte image test webové aplikace
1. Spuštění kontejneru test webové aplikace, který provádí testy pro spuštěné aplikace kontejneru
1. Pokud jsou testy úspěšné, vytvořit balíček archivu grafu helmu
1. Provést `helm upgrade` pomocí nového balíčku archivu grafu helmu

Vícekrokové úlohy umožňují rozdělit sestavování, spouštění a testování bitovou kopii do více možností složení kroků s podporou závislost mezi kroku. Pomocí vícekrokových úkolů do úlohy služby ACR budete mít podrobnější kontrolu nad bitové kopie sestavení, testování a operační systém a framework opravy chyb pracovních postupů.

Další informace o vícekrokových úkolů v [spuštění několika kroky sestavení, testování a opravu úkoly v úlohách ACR](container-registry-tasks-multi-step.md).

> [!IMPORTANT]
> Možnost vícekrokových úkolů ACR úloh je v současnosti ve verzi preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce můžou změnit před obecné dostupnosti (GA)

## <a name="next-steps"></a>Další postup

Až budete připraveni k automatizaci operačního systému a rozhraní framework opravy chyb ve vytváření imagí kontejnerů v cloudu, přečtěte si část třídílné série kurzů ACR úlohy.

> [!div class="nextstepaction"]
> [Sestavení imagí kontejnerů v cloudu pomocí Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

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
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png