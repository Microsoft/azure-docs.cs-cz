---
title: Příprava řešení Dynamics 365
description: Rozhraní pro balení, instalace a odinstalace součástí
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278582"
---
# <a name="dynamics-365-solution-preparation"></a>Příprava řešení Dynamics 365

Systém řešení Dynamics 365 je rozhraní pro balení, instalaci a odinstalaci součástí, které poskytují konkrétní obchodní funkce. Řešení používají nezávislé výrobce softwaru a další partnery Microsoft Dynamics 365 k distribuci rozšíření, která vytvoří.

Pokud jste existující ISV 365 (xRM), pravděpodobně už jste vytvořili spravované řešení a máte soubor řešení. zip. V řešení se ujistěte, že pole zobrazované jméno a popis obsahují informace o tom, co chcete, aby zákazníci viděli. Ty se zobrazí v centru pro správu CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Poznámka:** V následujícím příkladu balíčku budeme předpokládat, že název řešení je "SampleSolution. zip"._

Pokud jste novým nezávislým výrobcem softwaru, můžete získat další informace o vytváření řešení tady:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Pokud vaše řešení vyžaduje podpůrná data:

* Vytvoření ukázkových dat v testovacím prostředí
* Pomocí nástroje pro migraci konfigurace vytvořte schéma s pravidly porovnání pro vaše data.
* Uložte konfigurační schéma se soubory projektu. To budete potřebovat později, když aktualizujete konfigurační data.
* Exportujte konfigurační data. Nezapomeňte zadat název souboru pro export, který je smysluplný pro export.
