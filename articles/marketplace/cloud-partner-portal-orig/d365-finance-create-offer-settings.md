---
title: Jak vyplnit formulář pro nastavení nabídky | Dokumentace Microsoftu
description: Vysvětluje různá pole, které vyžadují hodnoty ve formuláři nabízejí nastavení pro novou aplikaci Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: a050a8cfdd67f662786fc0b9b7ed5451c88dccb1
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809532"
---
<a name="how-to-fill-out-the-offer-settings-form"></a>Jak vyplnit formulář pro nastavení nabídky
=======================================

Formulář pro nastavení nabídky je základní formulář a zadejte nastavení nabídky.
Povinná pole jsou popsané níže.

### <a name="offer-id"></a>ID nabídky

`OfferId` je jedinečný identifikátor nabídky v rámci profilu vydavatele.
Toto ID se nebude zobrazovat v adresách URL produktu. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-). ID nesmí končit spojovníkem a nesmí být delší než 50 znaků. Toto pole je uzamčen, jakmile uvedete nabídky.

Pokud například partner "Contoso" vytvoří nazývá "ukázkové webové aplikaci" ID nabídky, se zobrazí v AppSource jako:

&emsp; `https://appsource.microsoft.com/marketplace/apps/contoso.sample-Web App?tab=Overview`


### <a name="publisher-id"></a>ID vydavatele

Tento rozevírací seznam umožňuje zvolit profil vydavatele, který chcete publikovat v rámci této nabídky. Toto pole je uzamčen, jakmile uvedete nabídky.


### <a name="name"></a>Název

Toto je zobrazovaný název aplikace/nabídky, které se zobrazí v aplikaci Microsoft [AppSource](https://appsource.microsoft.com/). Může mít maximálně 50 znaků.

> [!NOTE]
> Krátký název musí být stejný jako název vydavatele zadaný v manifestu aplikace.

Klikněte na **Uložit** uložte svůj postup. Dalším krokem je přidání technické informace pro vaši nabídku.
