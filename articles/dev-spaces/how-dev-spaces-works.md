---
title: Jak Azure Dev Spaces funguje
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje procesy, které pohánějí Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234966"
---
# <a name="how-azure-dev-spaces-works"></a>Jak Azure Dev Spaces funguje

Vývoj aplikace Kubernetes může být náročný. Potřebujete konfigurační soubory Dockera a Kubernetese. Musíte zjistit, jak otestovat aplikaci místně a komunikovat s jinými závislými službami. Možná budete muset zpracovat vývoj a testování více služeb najednou a s týmem vývojářů.

Azure Dev Spaces vám nabízí několik způsobů, jak rychle itetovat a ladit aplikace Kubernetes a spolupracovat s týmem. Tento článek popisuje, co azure dev spaces můžete dělat a jak to funguje.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Rychle iterate a ladění aplikace Kubernetes

Azure Dev Spaces snižuje úsilí o vývoj, testování a iteraci aplikace Kubernetes v kontextu clusteru AKS. Toto snížení úsilí umožňuje vývojářům zaměřit se na obchodní logiku svých aplikací a nekonfigurovat jejich služby tak, aby běžely v Kubernetes.

### <a name="connect-your-development-machine-to-aks"></a>Připojte svůj vývojový stroj k AKS

Pomocí Azure Dev Spaces můžete připojit vývojový počítač k clusteru AKS, což vám umožní spouštět a ladit kód ve vývojovém počítači, jako by byl spuštěn v clusteru. Azure Dev Spaces přesměruje provoz mezi připojeným clusterem AKS spuštěním podu v clusteru, který funguje jako vzdálený agent pro přesměrování provozu mezi vývojovým počítačem a clusterem. Toto přesměrování provozu umožňuje kódu vývojového počítače a služeb spuštěných v clusteru AKS komunikovat, jako by byly ve stejném clusteru AKS. Další informace o připojení vývojového počítače k systému AKS naleznete v [tématu Jak funguje připojení vývojového počítače k clusteru AKS][how-it-works-connect].

### <a name="run-your-code-in-aks"></a>Spuštění kódu v AKS

Kromě přesměrování provozu mezi vývojovým počítačem a clusterem AKS můžete pomocí Azure Dev Spaces nakonfigurovat a rychle spustit kód přímo v AKS. Pomocí Visual Studia, visual studio code nebo Azure Dev Spaces CLI, Azure Dev prostory nahraje váš kód do clusteru, pak sestavení a spuštění. Azure Dev prostory můžete také inteligentně synchronizovat změny kódu a restartovat službu tak, aby odrážely změny podle potřeby. Při spuštění kódu, protokoly sestavení a trasování HTTP jsou přenášeny zpět do klienta, takže můžete sledovat průběh a diagnostikovat všechny problémy. Můžete také použít Azure Dev Spaces, připojit ladicí program v Sadě Visual Studio a Visual Studio Kód java, Node.js a .NET Core služby. Další informace najdete [v tématu Jak funguje příprava projektu pro Azure Dev Spaces][how-it-works-prep], [Jak funguje spuštění kódu s Azure Dev Spaces][how-it-works-up]a Jak vzdálené ladění kódu pomocí Azure [Dev Spaces funguje][how-it-works-remote-debugging].

## <a name="team-development"></a>Týmový vývoj

Azure Dev Spaces pomáhá týmům produktivně pracovat na jejich aplikaci ve stejném clusteru AKS, aniž by byly rušivé.

### <a name="intelligent-routing-between-dev-spaces"></a>Inteligentní směrování mezi dev prostory

S Azure Dev Spaces může tým sdílet jeden cluster AKS, který se spouštějí s aplikací nativní pro cloud, a vytvářet izolované dev prostory, kde může tým vyvíjet, testovat a ladit bez narušení ostatních vývojových prostorů. Směrná verze aplikace je spuštěna v kořenovém prostoru pro vývoj. Členové týmu pak vytvořit nezávislé podřízené dev prostory na základě kořenového prostoru pro vývoj, testování a ladění změn v aplikaci. Prostřednictvím možností směrování v oblasti dev spaces mohou podřízené dev prostory směrovat požadavky mezi službami spuštěných v podřízeném dev prostoru a nadřazeným dev prostorem. Toto směrování umožňuje vývojářům spouštět vlastní verzi služby při opakovaném použití závislých služeb z nadřazeného prostoru. Každý podřízený prostor má svou vlastní jedinečnou adresu URL, kterou mohou sdílet a přistupovat k ní ostatní uživatelé pro spolupráci. Další informace o tom, jak směrování funguje v Azure Dev Spaces, najdete v tématu [Jak funguje směrování s Azure Dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Živé testování otevřené žádosti o přijetí k přijetí ven

Akce GitHubu s Azure Dev Spaces můžete také použít k testování změn vaší aplikace v žádosti o přijetí změn přímo v clusteru před sloučením. Azure Dev Spaces můžete automaticky nasadit verzi revizní aplikace do clusteru, což umožňuje autorovi i ostatním členům týmu zkontrolovat změny v kontextu celé aplikace. Pomocí možností směrování Azure Dev Spaces se tato verze revizní verze aplikace také nasadí do clusteru, aniž by to mělo vliv na další vývojářské prostory. Všechny tyto funkce umožňují s jistotou schvalovat a slučovat žádosti o přijetí vyžádat. Příklad akcí GitHubu a Azure Dev Spaces najdete v tématu [Akce GitHubu & azure kubernetes service][pr-flow].

## <a name="next-steps"></a>Další kroky

Informace o zahájení připojení místního vývojového počítače ke clusteru AKS naleznete v [tématu Připojení vývojového počítače ke clusteru AKS][connect].

Pokud chcete začít používat Azure Dev Spaces pro vývoj týmu, podívejte se na vývoj týmu v azure [dev spaces][quickstart-team] quickstart.

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development