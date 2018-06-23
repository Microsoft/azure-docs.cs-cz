---
title: Nakonfigurujte nástroj obsahu moderátora zkontrolujte | Microsoft Docs
description: Konfigurace nebo získat týmu, značky, konektory, pracovní postupy a přihlašovací údaje.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: a3432a1d8f424fbe78570f47b774c6e7942e16b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342406"
---
# <a name="about-review-tool-settings"></a>O nastavení kontrolní nástroje #

Pomocí na řídicím panelu nástroje zkontrolujte na kartě nastavení, je snadné určit a změnit celou řadu součástí.

![Zkontrolujte nastavení moderátora obsahu](images/settings-1.png)

## <a name="team-and-subteams"></a>Tým a Subteams ## 

Spravovat svůj tým a subteams na této kartě. Může mít pouze jeden tým, ale můžete [vytvořit více subteams](subteams.md) a odeslání pozvánky pro budoucí členy. Po odeslání out pozve monitorovat je a změnit oprávnění pro členy týmu pozvat další uživatelé. Po členové týmu přijali vaše pozvání, můžete přiřadit různé subteams těmto členům. Můžete nastavit členové týmu role Správci, nebo recenzenti: Správci můžete pozvat jiných uživatelů, zatímco kontroloři nelze.

![Nastavení týmu moderátora obsahu](images/settings-2-team.png)

## <a name="tags"></a>Značky ##

Toto je, kde můžete provádět [definovat vlastní značky](tags.md) zadáním krátký kód, název a popis pro značek. Jakmile ho vytvoříte, je k dispozici během recenze. Můžete použít jiné značky pro různé recenze vypnutím viditelnost a zapněte.

![Obsahu moderátora značky nastavení](images/settings-3-tags.png)

## <a name="connectors"></a>Konektory ##

Pracovní postupy přidání funkcí pomocí konektorů pro komunikaci s nástrojem Kontrola. Zkontrolujte nástroj zavolá rozhraní API moderátora obsah s výchozím pracovním postupem pro moderování obsahu. Když registrujete nástroj zkontrolujte ji automaticky – zřizuje moderátora API přihlašovací údaje pro vás. Také podporuje integraci další konektor rozhraní API, tak dlouho, dokud konektor je k dispozici. Jsme provedli několik konektorů k dispozici ihned.

[Konektory karta](connectors.md) je, kde budete spravovat konektory. Můžete přidat nebo odstranit konektory a najít svůj klíč předplatného pro konkrétní konektor. Klikněte na tlačítko Připojit k přidat do vlastní pracovní postupy. 

![Nastavení konektory moderátora obsahu](images/settings-4-connectors.png)

## <a name="workflows"></a>Pracovní postupy ##

Správa pracovních postupů z karty pracovních postupů. Pracovní postupy můžete otestovat tím, že nahrajete ukázkový soubor. Můžete také [definovat vlastní pracovní postupy](workflows.md) pro bitové kopie a text s použitím dostupné konektory rozhraní API (najdete na kartě konektory). 

![Nastavení pracovního postupu moderátora obsahu](images/settings-5-workflows.png)

## <a name="credentials"></a>Přihlašovací údaje ##

Tato karta obsahuje rychlý přístup k vašemu obsahu moderátora předplatné klíči, které budete muset použít rozhraní API zahrnuty s obsahu moderátora (přerušování bitové kopie, Text přerušování, Správa seznamu, pracovního postupu a zkontrolujte rozhraní API).
 
![Přihlašovací údaje obsahu moderátora](images/settings-6-credentials.png)
