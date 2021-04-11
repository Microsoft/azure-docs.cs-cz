---
title: Rozšíření clusteru – Kubernetes s povoleným Azure ARC
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek obsahuje koncepční přehled funkce rozšíření clusteru s povoleným Kubernetesem Azure ARC.
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451049"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Rozšíření clusteru v Kubernetes s povoleným ARC Azure

[Grafy Helm](https://helm.sh/) vám pomůžou spravovat aplikace Kubernetes poskytováním stavebních bloků potřebných k definování, instalaci a upgradu i těch nejsložitějších aplikací Kubernetes. Funkce rozšíření clusteru se snaží sestavovat nad komponentami balení Helm. Tím zajistíte Azure Resource Manager řízená prostředí pro správu a životní cyklus rozšíření clusteru, jako je například Azure Monitor a Azure Defender pro Kubernetes. Funkce rozšíření clusteru poskytuje oproti a výše uvedeným výhodám další výhody, které jsou již k dispozici nativně pomocí Helm grafů:

- Získejte inventář všech clusterů a rozšíření, která jsou na těchto clusterech nainstalovaná.
- K automatizaci nasazení rozšíření clusteru použijte Azure Policy.
- Přihlaste se k odběru vlaků každé rozšíření.
- Nastavte automatický upgrade pro rozšíření.
- Podpora pro události vytváření instancí rozšíření a správy životního cyklu, které se aktualizují a odstraňují.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architektura

[![Architektura ](./media/conceptual-extensions.png) rozšíření clusteru](./media/conceptual-extensions.png#lightbox)

Instance rozšíření clusteru je vytvořená jako přípona Azure Resource Manager prostředku ( `Microsoft.KubernetesConfiguration/extensions` ) na Kubernetes prostředku s povoleným ARC Azure (reprezentovaným `Microsoft.Kubernetes/connectedClusters` ) v Azure Resource Manager. Reprezentace v Azure Resource Manager umožňuje vytvořit zásadu, která kontroluje všechny prostředky Kubernetes s povoleným rozšířením Azure ARC s konkrétním rozšířením clusteru nebo bez něj. Jakmile určíte, které clustery neobsahují rozšíření clusteru s požadovanými hodnotami vlastností, můžete tyto nekompatibilní prostředky napravit pomocí Azure Policy.

`config-agent`Běžící v clusteru sleduje nové nebo aktualizované prostředky rozšíření u prostředku Kubernetes s povoleným ARC Azure. `extensions-manager`Spuštění ve vašem clusteru načte graf Helm z Azure Container registry nebo Microsoft Container registry a nainstaluje ho do clusteru. 

`config-agent` `extensions-manager` Součásti a spuštěné v clusteru zpracovávají aktualizace verze a odstraňování instancí rozšíření.

> [!NOTE]
> * `config-agent` Monitoruje dostupnost nových nebo aktualizovaných prostředků rozšíření v prostředku Kubernetes s povoleným ARC. Proto agenti vyžadují připojení k požadovanému stavu, který se má do clusteru načíst. Pokud se agenti nepodaří připojit k Azure, šíření požadovaného stavu do clusteru se zpozdí.
> * Nastavení chráněné konfigurace pro rozšíření se ukládají po dobu až 48 hodin v Kubernetes službách s podporou ARC Azure. Výsledkem je, že Pokud cluster zůstane během 48 hodin po vytvoření prostředku rozšíření v Azure odpojený, rozšíření přejde ze `Pending` stavu do `Failed` stavu. Doporučujeme, aby se clustery převedly do online režimu, jak je to možné.

## <a name="next-steps"></a>Další kroky

* Pomocí našeho rychlého startu můžete [připojit cluster Kubernetes ke službě Azure ARC](./quickstart-connect-cluster.md).
* [Nasaďte rozšíření clusteru](./extensions.md) v clusteru Kubernetes s povoleným ARC Azure.