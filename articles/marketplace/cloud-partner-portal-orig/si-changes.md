---
title: Poznámky k verzi přehledů prodejců
description: Obsahuje informace o změnách funkce Přehledy prodejců.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: dsindona
ms.openlocfilehash: 1b88a8353c1cb9f97f18d720171f0827d7d85254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285397"
---
# <a name="seller-insights-release-notes"></a>Poznámky k verzi Služby Seller Insights 

(Datum vydání: 1. března 2019)

Tento článek obsahuje informace o změnách funkce Přehledy prodejců na [portálu partnerů cloudu](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Vydání zdůrazňuje pro březen 1, 2019

* *Odběratel trend* přidán do souhrnu
* *Pět nejlepších zákazníků v* souhrnu odráží všechna předplatná Azure, která má zákazník
* *Trend normalizovaného využití & trend aktivních objednávek* v souhrnu přesunutý v rámci *měsíčních objednávek na první pohled*
* *Zpráva o odsouhlasení výplaty* byla aktualizována.
* *Pět nejlepších zákazníků* v oblasti výplaty odráží všechna předplatná Azure, která zákazník má
* *Sestava využití* byla aktualizována pomocí ID zákazníka
* *Trvání zákazníka* na objednávkách & využití odráží všechna předplatná Azure, která má zákazník


(Datum vydání: Červenec 28, 2018)

## <a name="release-highlights-for-july-28-2018"></a>Vydání zdůrazňuje pro červenec 28, 2018


-   *Odhadované ceny* poskytují zobrazení zákaznických poplatků s důsledky převodu měny.
-   *Předpokládané výplaty* poskytují dřívější pohled na potenciální výplaty.
-  *Identifikátory referencí použití* poskytují věrnost dat mezi používáním zákazníků a objednávkami s výplatami
-   *Použití na denní zrno* poskytuje více rozlišovací schopnost a lepší přehled o využití zákazníka.


### <a name="changes-to-data-structure-and-taxonomy"></a>Změny ve struktuře dat a taxonomii

V následující tabulce jsou uvedeny metriky, které byly přidány nebo podstatně změněny v této verzi. 

| **Nový termín**                   |    **Definice**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Cena (CC)                     | Cena za jednotku použití pro danou skladovou položku (v měně zákazníka).       |
| Odhadované rozšířené nabití (CC) | Odhadovaný rozšířený poplatek za množství jednotek využití pro danou skladovou položku (v měně zákazníka). Tato hodnota nemusí být přesná z důvodu chyb zaokrouhlení nebo zkrácení.   |
| Měna vydavatele (PC)        | Měna preferovaná vydavatelem pro výplatu.                               |
| Odhadovaná cena (PC)           | Vypočítá se odhadovaná cena za jednotku využití pro danou skladovou položku na základě převodu cizí měny k datu využití (v měně vydavatele). Tato hodnota nemusí být přesná z důvodu chyb zaokrouhlení nebo zkrácení.   |
| Odhadované rozšířené nabití (PC) | Vypočítá se odhadovaný rozšířený poplatek za množství jednotek využití pro danou skladovou položku na základě převodu cizí měny k datu použití (v měně vydavatele). Tato hodnota nemusí být přesná z důvodu chyb zaokrouhlení nebo zkrácení. |
| Odhadovaná výplata (PC)          | Odhadovaná platba za množství jednotek využití pro danou skladovou položku na základě převodu cizí měny k datu výpočtu využití (v měně vydavatele). Tato hodnota nemusí být přesná z důvodu chyb zaokrouhlení nebo zkrácení.   |
| Odkaz na použití                | Identifikátor pro jeden nebo více dní využití zákazníka pro danou skladovou položku přidruženou k položce v sestavě výplaty. |
|  |  |
