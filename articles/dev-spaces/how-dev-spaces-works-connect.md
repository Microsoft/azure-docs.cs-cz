---
title: Jak připojit vývojový počítač ke clusteru AKS funguje
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje procesy, které používají Azure Dev Spaces k připojení vašeho vývojového počítače ke clusteru služby Azure Kubernetes.
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241709"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Jak připojit vývojový počítač ke clusteru AKS funguje

Pomocí Azure Dev Spaces můžete připojit svůj vývojový počítač ke clusteru AKS, který vám umožní spouštět a ladit kód ve vývojovém počítači, jako kdyby byl spuštěný v clusteru. Azure Dev Spaces přesměruje provoz mezi připojeným clusterem AKS spuštěním pod sebou v clusteru, který funguje jako vzdálený Agent pro přesměrování provozu mezi vývojovým počítačem a clusterem. Toto přesměrování provozu umožňuje kódu na vašem vývojovém počítači a službách spuštěných ve vašem clusteru AKS komunikovat, jako kdyby byly ve stejném clusteru AKS. Toto připojení také umožňuje spustit a ladit kód s kontejnerem ve vývojovém počítači nebo bez něj. Připojení vývojového počítače k vašemu clusteru vám pomůže rychle vyvíjet aplikace a provádět komplexní testování.

## <a name="connecting-to-your-cluster"></a>Připojování ke clusteru

Připojíte se ke stávajícímu clusteru AKS pomocí [Visual Studio Code][vs-code] s nainstalovaným rozšířením [Azure dev Spaces][azds-vs-code] spuštěným na MacOS nebo Windows 10. Po navázání připojení máte možnost přesměrovat veškerý provoz do nebo z nového nebo existujícího v clusteru do vašeho vývojového počítače.

> [!NOTE]
> Při použití Visual Studio Code pro připojení ke clusteru vám rozšíření Azure Dev Spaces nabízí možnost přesměrovat službu na vývojový počítač. Tato možnost je pohodlný způsob, jak identifikovat pod pro přesměrování. Všechna přesměrování mezi clusterem AKS a vaším vývojovým počítačem jsou pro objekt pod.

Připojení ke clusteru nevyžaduje, abyste ve svém clusteru povolili Azure Dev Spaces. Místo toho, když rozšíření Azure Dev Spaces naváže připojení ke clusteru,:

* Nahradí kontejner v pod clusterem AKS pomocí vzdáleného kontejneru agenta, který přesměruje provoz do vývojového počítače. Při přesměrování nového pod Azure Dev Spaces v clusteru AKS vytvoří nový pod vzdáleným agentem.
* Spustí [kubectl][kubectl-port-forward] na svém vývojovém počítači a přesměruje provoz z vývojového počítače do vzdáleného agenta spuštěného v clusteru.
* Shromažďuje informace o prostředí z vašeho clusteru pomocí vzdáleného agenta. Toto jsou informace o prostředí, včetně proměnných prostředí, viditelných služeb, připojení svazků a tajných připojení.
* Nastaví prostředí v Visual Studio Code terminálu, aby služba ve vývojovém počítači mohla přistupovat ke stejným proměnným, jako kdyby byla spuštěna v clusteru.  
* Aktualizuje soubor hostitelů pro mapování služeb v clusteru AKS na místní IP adresy ve vývojovém počítači. Tyto položky souborů hostitelů umožňují, aby kód spuštěný ve vývojovém počítači vytvářely požadavky na jiné služby spuštěné v clusteru. Pokud chcete aktualizovat soubor hostitelů, Azure Dev Spaces při připojování ke clusteru požádat o přístup správce na vašem vývojovém počítači.

Pokud ve vašem clusteru máte povolené Azure Dev Spaces, máte také možnost použít [přesměrování provozu, které nabízí Azure dev Spaces][how-it-works-routing]. Přesměrování provozu nabízené nástrojem Azure Dev Spaces umožňuje připojit se k kopii vaší služby spuštěné v podřízeném vývojovém prostoru. Použití podřízeného prostoru pro vývoj pomáhá vyhnout se přerušení dalších práce v nadřazeném vývojovém prostoru, protože přesměrujete přenosy, které cílí na instanci vaší služby podřízeného prostoru, a ponechání instance nadřazeného prostoru této služby beze změny.

Jakmile se připojíte ke clusteru, provoz se směruje na váš vývojový počítač bez ohledu na to, jestli vaše služba běží na vašem vývojovém počítači.

## <a name="running-code-on-your-development-computer"></a>Spuštění kódu na vašem vývojovém počítači

Po navázání připojení ke clusteru AKS můžete v počítači nativně spustit libovolný kód bez kontejneru. Veškerý síťový provoz, který vzdálený agent obdrží, se přesměruje na místní port zadaný během připojení, takže váš nativní běžící kód může přijmout a zpracovat tento provoz. Proměnné prostředí, svazky a tajné klíče z vašeho clusteru jsou zpřístupněny kódu běžícímu na vašem vývojovém počítači. Kromě toho, že se položky souborů hostitelů a přesměrování portů přidávají do vašeho vývojářského počítače Azure Dev Spaces, může váš kód odesílat síťový provoz do služeb spuštěných v clusteru pomocí názvů služeb z vašeho clusteru a tento provoz se předává do služeb spuštěných ve vašem clusteru.

Vzhledem k tomu, že váš kód běží na vašem vývojovém počítači, máte flexibilitu při používání libovolného nástroje, který obvykle používáte pro vývoj, ke spuštění kódu a jeho ladění. Provoz se směruje mezi vývojovým počítačem a vaším clusterem a celou dobu, po kterou jste se připojili. Toto trvalé připojení umožňuje spustit, zastavit a restartovat kód tak, jak potřebujete, aniž byste museli znovu navázat připojení.

Kromě toho Azure Dev Spaces poskytuje způsob, jak replikovat proměnné prostředí a připojené soubory, které jsou k dispozici ve vašem clusteru AKS ve vývojovém počítači prostřednictvím souboru *azds-Local. env* . Tento soubor můžete také použít k vytvoření nových proměnných prostředí a připojení svazků.

## <a name="additional-configuration-with-azds-localenv"></a>Další konfigurace pomocí azds-Local. env

Soubor *azds-Local. env* umožňuje replikovat proměnné prostředí a připojené soubory, které jsou k dispozici pro vaše lusky v clusteru AKS. V souboru *azds-Local. env* můžete zadat následující akce:

* Stáhněte svazek a nastavte cestu k tomuto svazku jako proměnnou prostředí.
* Stáhněte si jednotlivý soubor nebo sadu souborů ze svazku a připojte ho na svém vývojovém počítači.
* Zpřístupněte službu bez ohledu na to, ke kterému clusteru jste připojení.

Tady je příklad souboru *azds-Local. env* :

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

Výchozí soubor *azds-Local. env* se nevytvoří automaticky, takže musíte ručně vytvořit soubor v kořenu projektu.

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

Když se připojíte ke clusteru AKS, diagnostické protokoly z vašeho clusteru se zaprotokolují do [dočasného adresáře][azds-tmp-dir]vašeho vývojového počítače. Pomocí Visual Studio Code můžete k tisku aktuálních proměnných prostředí a záznamů DNS z clusteru AKS použít taky příkaz *Zobrazit diagnostické informace* .

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s připojením místního vývojového počítače ke clusteru AKS, přečtěte si téma [připojení vývojového počítače ke clusteru AKS][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
