---
title: Konfigurace nastavení nástroj pro kontrolu Content Moderator | Dokumentace Microsoftu
description: Konfiguraci nebo požádejte o svůj tým, značky, konektory, pracovní postupy a přihlašovací údaje.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 2ffed5e561cf9988ec10ecb7e10318d148281057
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51851533"
---
# <a name="review-tool-settings"></a>Zkontrolujte nastavení nástroje #

Na kartě nastavení na řídicím panelu nástrojů kontroly, je snadné k definování a změnit mnoho komponent.

![Content Moderator zkontrolovat nastavení](images/settings-1.png)

## <a name="team-and-subteams"></a>Tým a Podtýmy ## 

Spravujte svůj tým a podtýmy na této kartě. Můžete mít jenom jeden tým, ale můžete [vytvořit více podtýmy](subteams.md) a odeslání pozvánky pro budoucí členy. Poté, co se poslala pozvánky, monitorovat je a změnit oprávnění pro členy týmu pozvat další uživatele. Po členové týmu přijali vaši pozvánku, můžete přiřadit jiný podtýmy členy. Můžete nastavit členové týmu role administrators nebo kontrolorů: Správci může pozvat ostatní uživatele, zatímco nelze revidující.

![Moderátor nastavení týmu obsahu](images/settings-2-team.png)

## <a name="tags"></a>Značky ##

To je, kde můžete [definovat vlastní značky](tags.md) tak, že zadáte krátký kód, název a popis pro svá klíčová slova. Po vytvoření ho je k dispozici během kontroly. Různých klíčových slov můžete použít pro jiné kontroly to zapnutím viditelnost odhlásit a znovu přihlásit.

![Content Moderator značky nastavení](images/settings-3-tags.png)

## <a name="connectors"></a>Konektory ##

Pracovní postupy přidat funkce pomocí konektorů ke komunikaci se nástroj pro recenze. Nástroj pro recenze zavolá rozhraní API moderátor obsah s výchozí pracovní postup pro moderování obsahu. Při registraci pro nástroj pro recenze, se automaticky – zřizuje Moderator API přihlašovací údaje za vás. Také podporuje integraci další konektor API, za předpokladu, konektor je k dispozici. Provedli jsme několik konektorů k dispozici ihned.

[Kartou konektorů](connectors.md) je, kde budete spravovat konektory. Můžete přidat nebo odstranit konektory a vyhledat konkrétní konektor váš klíč předplatného. Klikněte na připojit, přidejte do své vlastní pracovní postupy. 

![Content Moderator konektory nastavení](images/settings-4-connectors.png)

## <a name="workflows"></a>Pracovní postupy ##

Správa pracovních postupů z karty pracovních postupů. Pracovní postupy můžete otestovat tím, že nahrajete do ní ukázkový soubor. Můžete také [definovat vlastní pracovní postupy](workflows.md) pro obrázků a textu s použitím dostupných konektorů API (tuto možnost najdete na kartě konektory). 

![Content Moderator nastavení pracovního postupu](images/settings-5-workflows.png)

## <a name="credentials"></a>Přihlašovací údaje ##

Tato karta poskytuje rychlý přístup k vašemu klíči předplatného Content Moderator, který budete muset použít zahrnutá rozhraní API s Content Moderatorem (moderování obrázků, moderování textu, Správa seznamu, pracovních postupů a revizi rozhraní API).
 
![Content Moderator pověření](images/settings-6-credentials.png)
