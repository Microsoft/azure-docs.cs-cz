---
title: Příprava řešení Dynamics 365 | Dokumentace Microsoftu
description: Architektura pro vytváření balíčků, instalace a odinstalace komponenty
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricarod.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ea62a826b9f99e264cb1b2242f8d8108453d8e7
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448764"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365 Solution Preparation

Systém Dynamics 365 solutioning je architektura určená k vytváření balíčků, instalace a odinstalace komponenty, které poskytují konkrétní obchodní funkce. Řešení používají nezávislé výrobce softwaru a dalšími partnery Microsoft Dynamics 365 pro distribuci rozšíření, který vytvoří.

Pokud jste stávající Dynamics 365 (xRM) nezávislých výrobců softwaru, pravděpodobně již jste vytvořili spravovaných řešení a solution.zip soubor. Ve vašem řešení Zkontrolujte prosím, že "Zobrazovaný název" a "Popis" pole odrážejí, co chcete zákazníkům naleznete v tématu. Se zobrazí v Centru pro správu CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Poznámka:** V následujícím příkladu balíček budeme předpokládat, že název řešení je "SampleSolution.zip"_

Pokud jste nový ISV, získáte další informace o vytvoření řešení tady: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Pokud vaše řešení, někdy potřebuje podpůrné dat:

* Vytvořit ukázková data ve vašem testovacím prostředí
* Nástroj pro migraci konfigurace použijte k vytvoření schématu pomocí pravidel pro porovnávání pro vaše data.
* Uložte konfigurační schéma se soubory projektu. Ho budete potřebovat později při aktualizaci konfigurační data.
* Exportujte konfigurační data. Je potřeba dát název, který je smysluplný pro export do souboru exportu.
