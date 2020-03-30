---
title: Jak funguje připojení vývojového počítače ke clusteru AKS
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje procesy pomocí Azure Dev Spaces k připojení vývojového počítače k clusteru služby Azure Kubernetes.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241709"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Jak funguje připojení vývojového počítače ke clusteru AKS

Pomocí Azure Dev Spaces můžete připojit vývojový počítač k clusteru AKS, což vám umožní spouštět a ladit kód ve vývojovém počítači, jako by byl spuštěn v clusteru. Azure Dev Spaces přesměruje provoz mezi připojeným clusterem AKS spuštěním podu v clusteru, který funguje jako vzdálený agent pro přesměrování provozu mezi vývojovým počítačem a clusterem. Toto přesměrování provozu umožňuje kódu vývojového počítače a služeb spuštěných v clusteru AKS komunikovat, jako by byly ve stejném clusteru AKS. Toto připojení také umožňuje spouštět a ladit kód s kontejnerem nebo bez kontejneru ve vývojovém počítači. Připojení vývojového počítače ke clusteru vám pomůže rychle vyvinout aplikaci a provést komplexní testování.

## <a name="connecting-to-your-cluster"></a>Připojení ke clusteru

Ke stávajícímu clusteru AKS se připojíte pomocí [kódu Visual Studia][vs-code] s nainstalovaným rozšířením Azure [Dev Spaces][azds-vs-code] spuštěným v MacOS nebo Windows 10. Při navázání připojení máte možnost přesměrovat veškerý provoz do a z nového nebo existujícího podu v clusteru do vývojového počítače.

> [!NOTE]
> Při použití Visual Studio Code pro připojení ke clusteru, rozšíření Azure Dev Spaces vám dává možnost přesměrování služby do vývojového počítače. Tato možnost je pohodlný způsob, jak identifikovat pod pro přesměrování. Veškeré přesměrování mezi clusterem AKS a vývojovým počítačem je pro pod.

Připojení ke clusteru nevyžaduje, abyste měli v clusteru povoleno azure dev spaces. Místo toho, když rozšíření Azure Dev Spaces naváže připojení k vašemu clusteru, bude:

* Nahradí kontejner v podu v clusteru AKS kontejnerem vzdáleného agenta, který přesměruje provoz do vývojového počítače. Při přesměrování nového podu azure dev spaces vytvoří nový pod ve vašem clusteru AKS se vzdáleným agentem.
* Spustí [port kubectl vpřed][kubectl-port-forward] ve vývojovém počítači a přesměruje přenos y z vývojového počítače vzdálenému agentovi spuštěnému v clusteru.
* Shromažďuje informace o prostředí z clusteru pomocí vzdáleného agenta. Tyto informace o prostředí zahrnují proměnné prostředí, viditelné služby, připojení svazků a tajné připojení.
* Nastaví prostředí v terminálu kódu sady Visual Studio, aby služba ve vývojovém počítači přistupovala ke stejným proměnným, jako kdyby byla spuštěna v clusteru.  
* Aktualizuje soubor hosts na mapové služby v clusteru AKS na místní IP adresy ve vývojovém počítači. Tyto položky souborů hosts umožňují kódu spuštěnému ve vývojovém počítači podávat požadavky na jiné služby spuštěné v clusteru. Chcete-li aktualizovat soubor hosts, Azure Dev Spaces požádá o přístup správce ve vývojovém počítači při připojování k clusteru.

Pokud máte v clusteru povoleno azure dev spaces, máte také možnost použít [přesměrování provozu nabízené Azure Dev Spaces][how-it-works-routing]. Přesměrování provozu nabízené Azure Dev Spaces umožňuje připojení ke kopii vaší služby spuštěné v podřízeném dev prostoru. Použití podřízeného dev prostoru pomáhá zabránit narušení ostatních pracujících v nadřazeném prostoru pro vývoj, protože pouze přesměrováváte provoz zaměřený na instanci podřízeného prostoru služby a ponechá vající nadřazenou vesmírnou instanci služby beze změny.

Po připojení ke clusteru je provoz směrován do vývojového počítače bez ohledu na to, zda je služba spuštěna ve vývojovém počítači.

## <a name="running-code-on-your-development-computer"></a>Spuštění kódu ve vývojovém počítači

Po navázání připojení ke clusteru AKS můžete spustit libovolný kód nativně v počítači bez kontejnerizace. Jakýkoli síťový provoz, který vzdálený agent obdrží, je přesměrován na místní port zadaný během připojení, aby nativně spuštěný kód mohl tento provoz přijmout a zpracovat. Proměnné prostředí, svazky a tajné klíče z clusteru jsou k dispozici kódu spuštěnému ve vývojovém počítači. Vzhledem k položkám souborů hostitelů a předávání portů přidanému do vývojářského počítače pomocí azure dev spaces může váš kód odesílat síťový provoz službám spuštěným v clusteru pomocí názvů služeb z clusteru a tento provoz se přesměruje do služby, které jsou spuštěny v clusteru.

Vzhledem k tomu, že váš kód je spuštěn ve vývojovém počítači, máte flexibilitu použít libovolný nástroj, který běžně používáte pro vývoj ke spuštění kódu a ladění. Provoz je směrován mezi vývojovým počítačem a clusterem po celou dobu připojení. Toto trvalé připojení umožňuje spustit, zastavit a restartovat kód tolik, kolik potřebujete, aniž byste museli znovu navázat připojení.

Azure Dev Spaces navíc poskytuje způsob replikace proměnných prostředí a připojených souborů dostupných pro pody v clusteru AKS ve vývojovém počítači prostřednictvím souboru *azds-local.env.* Tento soubor můžete také použít k vytvoření nových proměnných prostředí a připojení svazků.

## <a name="additional-configuration-with-azds-localenv"></a>Další konfigurace s azds-local.env

Soubor *azds-local.env* umožňuje replikovat proměnné prostředí a připojené soubory, které jsou k dispozici vašim podům v clusteru AKS. V souboru *azds-local.env* můžete určit následující akce:

* Stáhněte svazek a nastavte cestu k tomuto svazku jako proměnnou prostředí.
* Stáhněte si jednotlivé soubory nebo sadu souborů ze svazku a připojte jej do vývojového počítače.
* Zpřístupní te službu bez ohledu na cluster, ke které jste připojeni.

Zde je příklad *souboru azds-local.env:*

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

Výchozí soubor *azds-local.env* není vytvořen automaticky, takže je nutné ručně vytvořit soubor v kořenovém adresáři projektu.

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

Při připojení ke clusteru AKS jsou diagnostické protokoly z clusteru zaznamenány do [dočasného adresáře][azds-tmp-dir]vývojového počítače . Pomocí kódu sady Visual Studio můžete také použít příkaz *Zobrazit diagnostické informace* k tisku aktuálních proměnných prostředí a položek DNS z clusteru AKS.

## <a name="next-steps"></a>Další kroky

Informace o zahájení připojování místního vývojového počítače ke clusteru AKS naleznete v [tématu Připojení vývojového počítače ke clusteru AKS][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
