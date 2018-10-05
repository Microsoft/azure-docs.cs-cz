---
title: Zpráva k vydání verze prodejce Insights | Dokumentace Microsoftu
description: Poskytuje informace o změnách funkce prodejce přehledů.
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809192"
---
<a name="seller-insights-release-notes"></a>Zpráva k vydání verze Insights prodejce 
===============================
(Datum vydání: 28. července 2018)

Tento článek obsahuje informace o změnách na funkci na prodejce Insights [portál partnerů cloudu](https://cloudpartner.azure.com/#insights).


<a name="release-highlights"></a>Nejdůležitější Novinky ve verzi
------------------

-   *Odhad ceny* zviditelňují poplatky zákazníků s měny převod vliv.
-   *Naplánované výplaty* poskytují starší pohled potenciální výplaty.
-  *Použití odkazu identifikátory* poskytnout data věrnost mezi využití ze strany zákazníků a objednávek výplaty
-   *Použití s denním intervalem* poskytuje další členitosti a lepší přehled o využití ze strany zákazníků.


<a name="changes-to-data-structure-and-taxonomy"></a>Změny datové struktury a taxonomie
--------------------------------------

V následující tabulce jsou uvedeny metriky, které byly přidány nebo podstatně změna v této vydané verzi. 

| **Nový termín**                   |    **Definice**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Cena (kopie)                     | Cena za jednotku využití pro danou skladovou jednotku (v místní měně zákazníka).       |
| Odhadovaný poplatek rozšířené (kopie) | Odhadovaná rozšířené poplatek za množství jednotek využití pro danou skladovou jednotku (v místní měně zákazníka). Tato hodnota nemusí být přesné z důvodu chyby zaokrouhlení nebo zkrácení.   |
| Vydavatel měny (PC)        | Měna upřednostňuje vydavatele pro výběr.                               |
| Odhadovaná cena (PC)           | Odhadovaná cena za jednotku využití pro daný skladová jednotka založená na směnných převod na datum využití se počítá (v místní měně vydavatele). Tato hodnota nemusí být přesné z důvodu chyby zaokrouhlení nebo zkrácení.   |
| Odhadovaný poplatek rozšířené (PC) | Odhadované rozšířené poplatek za množství jednotek využití pro daný skladová jednotka založená na směnných převod na datum využití se počítá (v místní měně vydavatele). Tato hodnota nemusí být přesné z důvodu chyby zaokrouhlení nebo zkrácení. |
| Odhadované výběr (PC)          | Odhadované poplatky za množství jednotek využití pro danou skladovou jednotku podle cizích měn převod na datum, kdy využití se počítá (v místní měně vydavatele). Tato hodnota nemusí být přesné z důvodu chyby zaokrouhlení nebo zkrácení.   |
| Referenční informace k využití                | Identifikátor pro jeden nebo více dnů využití ze strany zákazníků pro danou skladovou Položku přidruženou ke položka v sestavě výběr. |
|  |  |
