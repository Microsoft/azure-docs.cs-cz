---
title: Migrace na Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Popisuje proces migrace z Azure Dev Spaces na přemostění do Kubernetes.
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, most na Kubernetes
ms.openlocfilehash: 8ffb7693ff223a9cb952964ded1e6967ceeb326e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499287"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrace na Bridge to Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces bude vyřazen 31. října 2023. Zákazníci by se měli přesunout na použití mostu na Kubernetes, nástroje pro vývojáře klienta.
>
> Účelem Azure Dev Spaces bylo převíjení uživatelů při vývoji na Kubernetes. Významnými kompromisy při přístupu k Azure Dev Spaces byla větší režie na uživatele, aby pochopili konfigurace Docker a Kubernetes a koncepty nasazení Kubernetes. V průběhu času se také stala jasné, že přístup Azure Dev Spaces neefektivně nesnížil rychlost vývoje vnitřních smyček na Kubernetes. Most na Kubernetes účinně snižuje rychlost vývoje vnitřních smyček a brání uživatelům v nepotřebných zátěžích.
>
> Základní mise zůstává beze změny: Sestavte si nejlepší prostředí pro vývoj, testování a ladění kódu mikroslužeb v kontextu větší aplikace.

Most na Kubernetes poskytuje světlejší důležitou alternativu k mnoha vývojářským scénářům, které pracují s Azure Dev Spaces. Most na Kubernetes je prostředí jenom na straně klienta využívající rozšíření v [aplikaci Visual Studio][vs]   a [Visual Studio Code][vsc].  

Most do Kubernetes pomáhá vývojovému prostředí tím, že umožňuje zavedené Kubernetes aplikace zahrnout službu spuštěnou na místní pracovní stanici pro vývoj. Na rozdíl od vývojových prostorů most mostu na Kubernetes snižuje složitost vnitřních smyček, protože je možné vytvářet konfigurace Docker a Kubernetes, což vývojářům umožňuje soustředit se čistě na obchodní logiku svého kódu mikroslužeb.

Most do Kubernetes vám pomůže iterovat na kódu běžícím na vašem vývojovém počítači a zároveň spotřebovávat závislosti a stávající konfiguraci z prostředí Kubernetes. Naproti tomu Azure Dev Spaces nasadí mikroslužbu do prostředí Kubernetes před tím, než budete moct vzdáleně ladit službu a iterovat na svém kódu.

Tento článek poskytuje srovnání mezi Azure Dev Spaces a přemostění Kubernetes a pokyny pro migraci z Azure Dev Spaces na přemostění na Kubernetes.

## <a name="development-approaches"></a>Způsoby vývoje

![Způsoby vývoje](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces pomohl vývojářům Kubernetes pracovat s kódem spuštěným přímo v jejich clusteru AKS a vyhnout se tak nutnosti místnímu prostředí, které se nepodobají nasazenému prostředí. Tento přístup zlepšil určité aspekty vývoje, ale také zavedl předpoklad učení a udržování dalších konceptů, jako jsou Docker, Kubernetes a Helm, a teprve potom můžete začít používat Azure Dev Spaces.

Most do Kubernetes umožňuje vývojářům pracovat přímo na svých vývojových počítačích při interakci se zbytkem jejich clusteru. Tento přístup využívá znalosti a rychlost spouštění kódu přímo na svých vývojových počítačích a sdílí závislosti a prostředí poskytované jejich clusterem. Tento přístup také využívá výhody a škálování, které pocházejí z provozu v Kubernetes.

## <a name="feature-comparison"></a>Porovnání funkcí

Azure Dev Spaces a most pro Kubernetes mají podobné funkce, liší se i v několika oblastech:

| Požadavek  | Azure Dev Spaces  | Přemostění na Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | V 15 oblastech Azure | Libovolný region služby AKS    |
| **Zabezpečení** |
| V clusteru je vyžadován přístup k zabezpečení.  | Přispěvatel clusteru AKS  | Kubernetes RBAC – aktualizace nasazení   |
| Na vašem vývojovém počítači je vyžadován přístup zabezpečení.  | –  | Místní správce/sudo   |
| **Použitelnost** |
| Nezávisle na artefaktech Kubernetes a Docker  | No  | Yes   |
| Automatické vrácení změn provedených po ladění  | No  | Yes   |
| **Podporované nástroje klienta** |
| Spolupracuje se sadou Visual Studio 2019  | Yes  | Yes   |
| Funguje s Visual Studio Code  | Yes  | Yes   |
| Funguje s rozhraním příkazového řádku  | Yes  | No   |
| **Kompatibilita s operačním systémem** |
| Funguje ve Windows 10  | Yes  | Yes  |
| Funguje na Linux  | Yes  | Yes  |
| Funguje na macOS  | Yes  | Yes  |
| **Možnosti** |
| Izolace vývojářů nebo vývoj pro týmovou spolupráci  | Yes  | Yes  |
| Selektivní přepsání proměnných prostředí  | No  | Yes  |
| Vytvoření grafu souboru Dockerfile a Helm  | Yes  | No  |
| Trvalé nasazení kódu do Kubernetes  | Yes  | No  |
| Vzdálené ladění v Kubernetes pod  | Yes  | No  |
| Místní ladění, připojené k Kubernetes  | No  | Yes  |
| Ladění více služeb současně na stejné pracovní stanici  | Yes  | Yes  |

## <a name="kubernetes-inner-loop-development"></a>Vývoj vnitřních smyček Kubernetes

Největší rozdíl mezi Azure Dev Spaces a mostem Kubernetes je místo, kde je spuštěn kód. Azure Dev Spaces pomáhá vyvíjet a ladit kód mikroslužeb, ale vyžaduje, abyste tento kód spustili v clusteru. Most na Kubernetes umožňuje vyvíjet a ladit kód mikroslužeb přímo na vývojovém počítači, a to i v kontextu větší aplikace běžící v Kubernetes. Most do Kubernetes rozšiřuje hranice clusteru Kubernetes a umožňuje místním procesům dědit konfiguraci z Kubernetes.

![Vývoj vnitřních smyček](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Při použití mostu na Kubernetes se naváže síťové připojení mezi vaším vývojovým počítačem a vaším clusterem.Po celou dobu životnosti tohoto připojení se do clusteru přidá proxy místo nasazení Kubernetes, které přesměrovává požadavky na službu do vašeho vývojového počítače. Při odpojení se nasazení aplikace vrátí k používání původní verze nasazení běžícího v clusteru. Tento přístup se liší od způsobu, jakým Azure Dev Spaces funguje, ve kterém je kód synchronizovaný do clusteru, sestavený a pak spuštěný. Azure Dev Spaces také nevrátí váš kód.

Most na Kubernetes má flexibilitu pro práci s aplikacemi spuštěnými v Kubernetes bez ohledu na jejich způsob nasazení. Pokud k sestavení a spuštění aplikace použijete CI/CD, a to bez ohledu na to, jestli používáte zavedené nástroje nebo vlastní skripty, můžete i nadále používat přemostění na Kubernetes k vývoji a ladění kódu.

> [!TIP]
>  [Rozšíření Microsoft Kubernetes][kubernetes-extension] vám umožňuje rychle vyvíjet Kubernetes manifesty pomocí technologie IntelliSense a pomáhá seznámení s Helm grafy.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Přechod na přemostění na Kubernetes z Azure Dev Spaces

1. Pokud používáte aplikaci Visual Studio, aktualizujte integrované vývojové prostředí (IDE) sady Visual Studio na verzi 16,7 nebo vyšší a nainstalujte most do rozšíření Kubernetes z [Visual Studio Marketplace][vs-marketplace]. Pokud používáte Visual Studio Code, nainstalujte [most do rozšíření Kubernetes][vsc-marketplace].
1. Zakažte kontroler Azure Dev Spaces pomocí Azure Portal nebo [Azure Dev Spaces CLI] [azds-Delete].
1. Použijte [Azure Cloud Shell](https://shell.azure.com). Nebo na Macu, Linux nebo Windows s nainstalovaným bash otevřete příkazový řádek prostředí bash. Ujistěte se, že v prostředí příkazového řádku jsou k dispozici následující nástroje: Azure CLI, Docker, kubectl, kudrlinkou, tar a gunzip.
1. Vytvořte registr kontejnerů nebo použijte existující. Registr kontejnerů můžete v Azure vytvořit pomocí [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) nebo pomocí [Docker Hub](https://hub.docker.com/). Při použití Azure Cloud Shell je k dispozici pouze Azure Container Registry pro hostování imagí Docker.
1. Spusťte skript migrace a převeďte Azure Dev Spaces assety na přemostění na prostředky Kubernetes. Skript vytvoří nový Image kompatibilní s mostem na Kubernetes, nahraje ho do určeného registru a pak pomocí [Helm](https://helm.sh) aktualizuje cluster s imagí. Musíte zadat skupinu prostředků, název clusteru AKS a registr kontejneru. Existují další možnosti příkazového řádku, jak je znázorněno zde:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   Skript podporuje následující příznaky:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. Ručně proveďte migraci všech úprav, jako je například nastavení proměnných prostředí, do *azds. yaml* do souboru *Values. yml* vašeho projektu.
1. volitelné Odeberte `azds.yaml` soubor z projektu.
1. Nakonfigurujte most na Kubernetes ve vaší nasazené aplikaci. Další informace o použití mostu na Kubernetes v aplikaci Visual Studio naleznete v tématu [použití mostu na Kubernetes v aplikaci Visual Studio][use-btk-vs]. VS Code najdete v tématu [použití přemostění na Kubernetes v vs Code][use-btk-vsc].
1. Spusťte ladění pomocí nově vytvořeného mostu pro Kubernetes profil ladění/spuštění.
1. Skript můžete spustit znovu podle potřeby pro opětovné nasazení do clusteru.

## <a name="team-development-in-a-shared-cluster"></a>Vývoj týmu ve sdíleném clusteru

Můžete také použít směrování specifické pro vývojáře s přemostěním na Kubernetes. Scénář vývoje Azure Dev Spaces týmu používá několik oborů názvů Kubernetes k izolaci služby od zbytku aplikace s využitím konceptu nadřazených a podřízených oborů názvů. Most na Kubernetes nabízí stejnou schopnost, ale s vylepšenými charakteristikami výkonu a v rámci stejného oboru názvů aplikace.

Oba mosty Kubernetes i Azure Dev Spaces vyžadují, aby byly v celé aplikaci přítomné a šířené hlavičky protokolu HTTP. Pokud jste již aplikaci nakonfigurovali pro zpracování šíření hlaviček pro Azure Dev Spaces, je nutné aktualizovat hlavičku. Pokud chcete přejít na most Kubernetes z Azure Dev Spaces, aktualizujte nakonfigurovanou hlavičku z *azds-Route-as* na *Kubernetes-Route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Vyhodnotit most na Kubernetes

Pokud chcete experimentovat s mostem na Kubernetes před tím, než zakážete Azure Dev Spaces v clusteru, nejjednodušší způsob je použít nový cluster. Pokud se pokusíte použít Azure Dev Spaces a Přemostit Kubernetes současně na stejném clusteru, budete při používání funkcí směrování na obou počítačích nabíhat v potížích.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Použití sady Visual Studio k vyhodnocení mostu na Kubernetes

1. Aktualizujte integrované vývojové prostředí (IDE) sady Visual Studio na verzi 16,7 nebo vyšší a nainstalujte most do rozšíření Kubernetes z [Visual Studio Marketplace][vs-marketplace].
1. Vytvořte nový cluster AKS a nasaďte svoji aplikaci. Můžete použít také [ukázkovou aplikaci][btk-sample-app].
1. Nakonfigurujte most na Kubernetes ve vaší nasazené aplikaci. Další informace o použití mostu na Kubernetes v aplikaci Visual Studio najdete v tématu [použití mostu na Kubernetes][use-btk-vs].
1. Spusťte ladění v aplikaci Visual Studio pomocí nově vytvořeného mostu pro ladicí profil Kubernetes.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Použití Visual Studio Code k vyhodnocení mostu na Kubernetes

1. Nainstalujte [most do rozšíření Kubernetes][vsc-marketplace].
1. Vytvořte nový cluster AKS a nasaďte svoji aplikaci. Můžete použít také [ukázkovou aplikaci][btk-sample-app].
1. Nakonfigurujte most na Kubernetes ve vaší nasazené aplikaci. Další informace o použití mostu na Kubernetes v Visual Studio Code najdete v tématu [použití mostu na Kubernetes][use-btk-vsc].
1. Spusťte ladění v aplikaci Visual Studio pomocí nově vytvořeného mostu pro Kubernetes spustit profil.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak most Kubernetes funguje.

> [!div class="nextstepaction"]
> [Jak funguje Přemostění na Kubernetes][how-it-works-bridge-to-kubernetes]


[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
