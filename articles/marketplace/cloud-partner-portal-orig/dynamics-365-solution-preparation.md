---
title: Příprava řešení Dynamics 365
description: Rozhraní pro balení, instalace a odinstalace součástí
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: bcb3bb63f305aeb98efda3baf0f6661bd7f67a7c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824230"
---
# <a name="dynamics-365-solution-preparation"></a>Příprava řešení Dynamics 365

Systém řešení Dynamics 365 je rozhraní pro balení, instalaci a odinstalaci součástí, které poskytují konkrétní obchodní funkce. Řešení používají nezávislé výrobce softwaru a další partnery Microsoft Dynamics 365 k distribuci rozšíření, která vytvoří.

Pokud jste existující ISV 365 (xRM), pravděpodobně už jste vytvořili spravované řešení a máte soubor řešení. zip. V řešení se ujistěte, že pole zobrazované jméno a popis obsahují informace o tom, co chcete, aby zákazníci viděli. Ty se zobrazí v centru pro správu CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Poznámka:** V následujícím příkladu balíčku budeme předpokládat, že název řešení je "SampleSolution. zip"._

Pokud jste novým nezávislým výrobcem softwaru, můžete získat další informace o vytváření řešení tady: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Pokud vaše řešení vyžaduje podpůrná data:

* Vytvoření ukázkových dat v testovacím prostředí
* Pomocí nástroje pro migraci konfigurace vytvořte schéma s pravidly porovnání pro vaše data.
* Uložte konfigurační schéma se soubory projektu. To budete potřebovat později, když aktualizujete konfigurační data.
* Exportujte konfigurační data. Nezapomeňte zadat název souboru pro export, který je smysluplný pro export.
