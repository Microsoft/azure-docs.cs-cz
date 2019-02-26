---
title: Nastavení pro aplikace Power BI nabízejí – Azure Marketplace nabízí | Dokumentace Microsoftu
description: Konfigurace nastavení nabídky pro aplikaci Power BI nabídku marketplace Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: 49bbe5dcf17a9aa20cb47f477c59e7115d29dacc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819595"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI aplikace nabízejí nastavení kartu

Když otevřete **nová nabídka** stránce aplikace služby, se nejprve zobrazí **nabízejí nastavení** kartu. Zadejte primární identifikátory a název vaší nabídky na této kartě. Hvězdička (*) označuje povinné pole.

![Karta nastavení nabídky](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Nabídka nastavení pole 

Na **nabízejí nastavení** kartě, budete muset zadat informace v následující požadované pole:

|  Pole        |  Popis                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID nabídky**  | Jedinečný identifikátor (v rámci profilu vydavatele) pro tuto nabídku. Tento identifikátor se nebude zobrazovat v adresách URL produktu, šablon Azure Resource Manageru, a oznámí fakturace. Maximální délka je 50 znaků. Může obsahovat jenom malé alfanumerické znaky a pomlčky (-). Nemůže končit spojovníkem. Tento identifikátor nelze změnit po ukončení nabídky za provozu. Pokud Contoso publikuje nabídka s ID nabídky `sample-SvcApp`, tuto nabídku je přiřazena adresa URL AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publisher** | Jedinečný identifikátor vaší organizace v [AppSource](https://appsource.microsoft.com). Všechny nabídky by měly být přidruženy s vaším ID vydavatele. Tuto hodnotu nelze změnit, až se uloží nabídky.                         |
| **Název**      | Zobrazovaný název pro vaši nabídku. Tento název se zobrazí na AppSource a na portál partnerů cloudu. Maximální délka je 50 znaků. Použijte název značky, který lze rozpoznat pro tento produkt. Pokud aplikace na trh s tímto názvem, nezahrnujte název vaší organizace. Pokud zadáváte v rámci této nabídky na jiné weby a publikací, použijte stejný název ve všech publikacích.    <br/>Pokud uvolníte nabídky během období preview pro aplikace Power BI, přidejte řetězec `(Preview)` na konec názvu vaší aplikace, například takto: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Další postup

Na další kartě určete [technické informace o](./cpp-technical-info-tab.md) pro vaši nabídku.
