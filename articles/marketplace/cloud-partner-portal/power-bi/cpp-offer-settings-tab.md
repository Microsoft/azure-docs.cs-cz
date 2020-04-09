---
title: Nastavení nabídky nabídky aplikace Power BI | Azure Marketplace
description: Konfigurace nastavení nabídky pro nabídku aplikace Power BI pro tržiště Microsoft AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 1bf8abb2d8ac6c1c9d2e03ef5e4f1edce530cbdb
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985758"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Karta Nastavení nabídky aplikací Power BI

>[!Important]
>dubna 2020 začneme přesouvat správu nabídek aplikací Power BI do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Podle pokynů v [přehledu vytváření aplikací Power BI](https://aka.ms/AzureCreatePBIServiceApp) spravujte migrované nabídky.

Když otevřete stránku **Nová nabídka** pro servisní aplikace, zobrazí se nejprve karta **Nastavení nabídky.** Na této kartě zadáte primární identifikátory a název nabídky. Hvězdička (*) označuje povinné pole.

![Karta nastavení nabídky](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Pole Nastavení nabídky 

Na kartě **Nastavení nabídky** je třeba zadat informace do následujících povinných polí. Povinná pole jsou obžalována hvězdičkou (*).

|  Pole        |  Popis                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID nabídky\***  | Jedinečný identifikátor (v rámci profilu vydavatele) pro nabídku. Tento identifikátor se zobrazí v adresách URL produktů, šablonách Azure Resource Manageru a v sestavách fakturace. Maximální délka je 50 znaků. Může obsahovat pouze malé alfanumerické znaky a pomlčky (-). Nemůže to skončit pomlčkou. Tento identifikátor nelze změnit po přepychu nabídky do provozu. Pokud Contoso publikuje nabídku `sample-SvcApp`s ID nabídky , `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`je jí přiřazena adresa URL AppSource .      |
| **Vydavatel\*** | Jedinečný identifikátor vaší organizace v [AppSource](https://appsource.microsoft.com). Všechny nabídky by měly být spojeny s vaším ID majitele stránek. Tuto hodnotu nelze po uložení nabídky změnit.                         |
| **Jméno\***      | Zobrazovaný název vaší nabídky. Tento název se zobrazí na AppSource a na portálu cloudových partnerů. Maximální délka je 50 znaků. Použijte značku, která je pro váš produkt rozpoznatelná. Neuvádějte zde název vaší organizace, pokud není aplikace uváděna na trh s tímto názvem. Pokud tuto nabídku poskytujete na jiných webech a v publikacích, použijte stejný název ve všech publikacích.    <br/>Pokud během období náhledu pro Aplikace Power BI `(Preview)` vydáte nabídku, přidejte řetězec na `Sample Scv App (Preview)`konec názvu aplikace, například takto: . |
|     |     |


## <a name="next-steps"></a>Další kroky

Na další kartě zadáte [technické informace](./cpp-technical-info-tab.md) pro vaši nabídku.
