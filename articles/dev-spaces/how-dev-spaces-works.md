---
title: Jak Azure Dev Spaces funguje
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Popisuje procesy, které Azure Dev Spaces napájení
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 40125b07029c78b4e8858d54058aabe038109057
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837410"
---
# <a name="how-azure-dev-spaces-works"></a>Jak Azure Dev Spaces funguje

Vývoj aplikace Kubernetes může být náročný. Potřebujete konfigurační soubory Docker a Kubernetes. Musíte zjistit, jak aplikaci testovat v místním prostředí a pracovat s dalšími závislými službami. Možná budete muset zvládnout vývoj a testování více služeb najednou a s týmem vývojářů.

Azure Dev Spaces poskytuje více způsobů, jak rychle iterovat a ladit aplikace Kubernetes a spolupracovat s týmem. Tento článek popisuje, co Azure Dev Spaces může dělat a jak funguje.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Rychlé iterace a ladění aplikace Kubernetes

Azure Dev Spaces snižuje úsilí při vývoji, testování a iteraci aplikace Kubernetes v kontextu clusteru AKS. Toto snížení úsilí umožňuje vývojářům soustředit se na obchodní logiku svých aplikací a nekonfigurují své služby tak, aby běžely v Kubernetes.

### <a name="local-process-with-kubernetes"></a>Místní proces s Kubernetes

Pomocí místního procesu s Kubernetes můžete připojit svůj vývojový počítač k vašemu clusteru Kubernetes, což vám umožní spouštět a ladit kód ve vývojovém počítači, jako kdyby byl spuštěný v clusteru. Azure Dev Spaces přesměruje provoz mezi připojeným clusterem tak, že v clusteru spustíte pod, který funguje jako vzdálený Agent pro přesměrování provozu mezi vývojovým počítačem a clusterem. Toto přesměrování provozu umožňuje kódu na vašem vývojovém počítači a službách spuštěných ve vašem clusteru komunikovat, jako kdyby byly ve stejném clusteru. Další informace o připojení vývojového počítače ke clusteru Kubernetes najdete v tématu [Jak funguje místní proces s Kubernetes][how-it-works-local-process-kubernetes].

### <a name="run-your-code-in-aks"></a>Spuštění kódu v AKS

Kromě přesměrování provozu mezi vývojovým počítačem a vaším AKS clusterem můžete pomocí Azure Dev Spaces nakonfigurovat a rychle spustit kód přímo v AKS. Pomocí sady Visual Studio, Visual Studio Code nebo Azure Dev Spaces CLI, Azure dev Spaces nahraje váš kód do clusteru a pak ho sestaví a spustí. Azure dev Spaces může také inteligentně synchronizovat změny kódu a restartovat službu, aby se změny projevily podle potřeby. Při spuštění kódu se protokoly sestavení a trasování HTTP streamují zpátky do vašeho klienta, abyste mohli sledovat průběh a diagnostikovat případné problémy. Můžete také použít Azure Dev Spaces, pro připojení ladicího programu v aplikaci Visual Studio a Visual Studio Code na služby Java, Node.js a .NET Core. Další informace najdete v tématu [jak připravovat projekt pro Azure dev Spaces funguje][how-it-works-prep], [Jak spustit kód s Azure dev Spaces funguje][how-it-works-up]a [jak vzdálené ladění kódu pomocí Azure dev Spaces funguje][how-it-works-remote-debugging].

## <a name="team-development"></a>Týmový vývoj

Azure Dev Spaces pomáhá týmům pracovat na jejich aplikaci na stejném clusteru AKS bez rušivého výpadku.

### <a name="intelligent-routing-between-dev-spaces"></a>Inteligentní směrování mezi vývojové prostory

Pomocí Azure Dev Spaces může tým sdílet jeden cluster AKS s aplikací nativní pro Cloud a vytvářet izolované vývojové prostory, kde tým může vyvíjet, testovat a ladit bez rušivého vývoje. Základní verze aplikace se spouští v kořenovém vývojovém prostoru. Členové týmu pak vytvoří nezávislé podřízené vývojové prostory na základě kořenového místa pro vývoj, testování a ladění změn aplikace. Díky schopnostem směrování ve vývojových prostorech můžou podřízené vývojové prostory směrovat požadavky mezi službami běžícími v podřízeném vývojovém prostoru a nadřazeného vývojového prostoru. Toto směrování umožňuje vývojářům spustit svou vlastní verzi služby a znovu použít závislé služby z nadřazeného prostoru. Každý podřízený prostor má svou vlastní jedinečnou adresu URL, kterou můžou sdílet a přistupovat jiné pro spolupráci. Další informace o tom, jak funguje směrování v Azure Dev Spaces, najdete v článku [jak směrování pracuje s Azure dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Živé testování otevřené žádosti o získání dat

Můžete také použít akce GitHubu s Azure Dev Spaces k otestování změn aplikace v žádosti o přijetí změn přímo v clusteru před sloučením. Azure Dev Spaces může automaticky nasadit recenzi aplikace do vašeho clusteru, což umožňuje autorovi i ostatním členům týmu zkontrolovat změny v kontextu celé aplikace. Pomocí možností směrování Azure Dev Spaces se tato verze revize aplikace taky nasadí do vašeho clusteru, aniž by to mělo vliv na jiné vývojové prostory. Všechny tyto funkce umožňují spolehlivě schvalovat a slučovat žádosti o přijetí změn. Příklad akcí a Azure Dev Spaces GitHubu najdete v tématu [Akce githubu & službě Azure Kubernetes][pr-flow].

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s připojením místního vývojového počítače ke clusteru AKS, přečtěte si téma [připojení vývojového počítače ke clusteru AKS][connect].

Chcete-li začít používat Azure Dev Spaces pro týmový vývoj, přečtěte si téma [vývoj týmu v Azure dev Spaces][quickstart-team] rychlý Start.

[connect]: https://code.visualstudio.com/docs/containers/local-process-kubernetes
[how-it-works-local-process-kubernetes]: /visualstudio/containers/overview-local-process-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development