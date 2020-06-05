---
title: Jak provést samočinnou diagnostiku a řešení problémů v Azure jaře cloudu
description: Naučte se, jak sami diagnostikovat a řešit problémy v Azure jarním cloudu.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.openlocfilehash: f1d5161b0efe0b8d9b9c0b84ac149050c3823ab6
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84429473"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Jak provést samočinnou diagnostiku a řešení problémů v Azure jaře cloudu
Diagnostika App Service je interaktivní prostředí, které vám může pomoct při odstraňování potíží s aplikací. Není nutná žádná konfigurace. Když vyhledáte nějaké problémy, App Service Diagnostika ukazuje, co je špatné, a provede vás pokyny k řešení potíží a vyřešení problému.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.
* Nasazená instance služby jarní cloudová služba Azure Pokud chcete začít, postupujte podle našeho [rychlého startu na webu nasazení aplikace prostřednictvím rozhraní příkazového řádku Azure CLI](spring-cloud-quickstart-launch-app-cli.md) .
* V instanci služby už je vytvořená aspoň jedna aplikace.

## <a name="navigate-to-the-diagnostics-page"></a>Přejít na stránku Diagnostika
1. Přihlaste se k portálu Azure.
2. Přejít na stránku s **přehledem** jarního cloudu Azure.
3. Otevřete **diagnostiku a řešení problémů** v nabídce na levé straně stránky.

 ![Diagnostika, dialogové okno řešení](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Hledat zaznamenané problémy
Chcete-li zjistit problém, můžete hledat zadáním klíčového slova nebo kliknutím na možnost Skupina řešení a prozkoumat vše v této kategorii.

 ![Problémy vyhledávání](media/spring-cloud-diagnose/search-detectors.png)

Po výběru **kontroly stavu konfiguračního serveru**, **stavu konfiguračního serveru**nebo **Historie aktualizací serveru** se zobrazí různé výsledky.

![Možnosti problémů](media/spring-cloud-diagnose/detectors-options.png)

Najděte svůj cílový detektor a kliknutím ho spusťte. Po spuštění detektoru se zobrazí souhrn diagnostiky. Můžete vybrat **Zobrazit úplnou sestavu** pro kontrolu diagnostických podrobností nebo kliknutím na tlačítko **Zobrazit dlaždici** přejít zpátky na seznam detektorů.

 ![Souhrnná Diagnostika](media/spring-cloud-diagnose/summary-diagnostics.png)

Na stránce diagnostické podrobnosti můžete změnit časový rozsah diagnostiky pomocí kontroleru v pravém horním rohu. Chcete-li zobrazit další metriky nebo protokoly, přepněte jednotlivé diagnostiky. Pro metriky a protokoly může trvat 15 minut.

 ![Podrobnosti diagnostiky](media/spring-cloud-diagnose/diagnostics-details.png)

Některé výsledky obsahují související dokumentaci.

 ![Související podrobnosti](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Další kroky
* [Monitorování prostředků Spring Cloudu s využitím výstrah a skupin akcí](spring-cloud-tutorial-alerts-action-groups.md)
* [Ovládací prvky zabezpečení pro službu Azure Spring Cloud](spring-cloud-concept-security-controls.md)