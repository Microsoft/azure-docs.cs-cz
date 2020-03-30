---
title: Příprava řešení Dynamics 365
description: Rámec pro balení, instalaci a odinstalování komponent
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278582"
---
# <a name="dynamics-365-solution-preparation"></a>Příprava řešení Dynamics 365

Systém řešení Dynamics 365 je rámec pro balení, instalaci a odinstalaci součástí, které poskytují specifické obchodní funkce. Řešení používají nemožní výrobci a nechybí ani k tomu, aby poskytovatelé služeb Microsoft Dynamics 365 distribuovali rozšíření, která vytvářejí.

Pokud jste existujícím neřízeným diskem Dynamics 365 (xRM), pravděpodobně jste již vytvořili spravované řešení a máte soubor solution.zip. Ve svém řešení se ujistěte, že pole "Zobrazovaný název" a "Popis" odrážejí to, co mají zákazníci vidět. Ty jsou zobrazeny v Centru pro správu online aplikace CRM.

![CrmScreenShot1](media/CRMScreenShot1.png)

_**Poznámka:** V příkladu balíčku, který následuje budeme předpokládat, že název řešení je "SampleSolution.zip"_

Pokud jste nový isv, můžete získat další podrobnosti o vytvoření řešení zde:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Pokud vaše řešení vyžaduje podporu dat:

* Vytvoření ukázkových dat v testovacím prostředí
* Pomocí nástroje pro migraci konfigurace vytvořte schéma s pravidly porovnání dat.
* Uložte schéma konfigurace se soubory projektu. Budete to potřebovat později, pokud aktualizujete konfigurační data.
* Exportujte konfigurační data. Nezapomeňte dát souboru exportu název, který má smysl pro váš export.
