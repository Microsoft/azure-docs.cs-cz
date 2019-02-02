---
title: Nastavení pro aplikace Power BI nabízejí – Azure Marketplace nabízí | Dokumentace Microsoftu
description: Konfigurace nastavení nabídky pro nabídku aplikace Power BI webu Microsoft Marketplace AppSource.
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
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666783"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI aplikace nabízejí nastavení kartu

**Nová nabídka** otevře se stránka pro služby aplikací na první kartě s názvem **nabízejí nastavení**.  Bude poskytovat identifikátory primární a název vaší nabídky na této kartě.  Připojený hvězdička (*) na název pole označuje, že je povinný.

![Karta nastavení nabídky](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Nabídka nastavení pole 

V **nabízejí nastavení** kartu, je nutné zadat následující požadované pole.

|  Pole        |  Popis                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID nabídky**  | Jedinečný identifikátor (v rámci profilu vydavatele) pro tuto nabídku. Tento identifikátor se nebude zobrazovat v adresách URL produktu, šablon Resource Manageru, a oznámí fakturace. To má maximální délku než 50 znaků, lze pouze obsahovat malé alfanumerické znaky a pomlčky (-), ale nesmí končit spojovníkem. Toto pole nelze změnit po ukončení nabídky za provozu. Například pokud Contoso publikuje nabídka s ID nabídky `sample-SvcApp`, je přiřazena adresa URL AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publisher** | Jedinečný identifikátor vaší organizace v [AppSource](https://appsource.microsoft.com). Všechny nabídky by měly být přidruženy s vaším ID vydavatele. Tuto hodnotu nelze změnit po uložení nabídky.                         |
| **Název**      | Zobrazovaný název nabídky. Tento název se zobrazí v AppSource a portál partnerů cloudu. Může mít maximálně 50 znaků. Pokyny v tomto poli se zahrnou rozpoznat název značky pro tento produkt. Pokud je jak aplikace na trh, nezahrnujte název vaší organizace. Pokud jsou marketing této nabídky do jiných webových stránek a publikace, ujistěte se, že název jsou stejné všechny publikace.    <br/>Pokud vydáte nabídky během období preview aplikace Power BI, režimu náhledu, připojte řetězec `(Preview)` na název vaší aplikace, například `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Další postup

Na další kartě, kterou specifikujete [technické informace o](./cpp-technical-info-tab.md) pro vaši nabídku.
