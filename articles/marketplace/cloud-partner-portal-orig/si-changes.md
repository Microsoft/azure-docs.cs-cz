---
title: Poznámky k verzi prodejce Insights
description: Poskytuje informace o změnách funkce prodejce Insights.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: 4f7e461241793b27d838c04311509f43932df291
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808963"
---
# <a name="seller-insights-release-notes"></a>Poznámky k verzi prodejce Insights 

(Datum vydání: 1. března 2019)

Tento článek poskytuje informace o změnách funkce prodejce Insights v [portál partnerů cloudu](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Zvýraznění vydaných verzí 1. března 2019

* *Trend zákazníka* se přidal do souhrnu.
* *Pět nejoblíbenějších zákazníků* na Shrnutí odráží všechna předplatná Azure, která má zákazník
* Trend *normalizovaného využití & trendu aktivních objednávek* na souhrnu přesunutý pod *měsíčními objednávkami na první pohled*
* *Sestava odsouhlasení výběrů* se aktualizovala.
* *Pět nejoblíbenějších zákazníků* na výběr odráží všechna předplatná Azure, která má zákazník
* *Sestava využití* aktualizovaná s ID zákazníka
* *Zákaznická Tenurea* na objednávkách & využití odráží všechna předplatná Azure, která má zákazník


(Datum vydání: 28. července 2018)

## <a name="release-highlights-for-july-28-2018"></a>Zvýraznění verze na 28. července 2018


-   *Odhadované ceny* poskytují pohled na poplatky za zákazníky s důsledky převodu měny.
-   *Prognózy* vydaných výsledků poskytují předchozí pohled na potenciální výběry.
-  *Referenční identifikátory použití* poskytují věrnost dat mezi zákaznickým využitím a objednávkami s výběry.
-   *Využití v denním zrnitě* poskytuje větší členitost a lepší přehled o používání zákazníků.


### <a name="changes-to-data-structure-and-taxonomy"></a>Změny struktury dat a taxonomie

V následující tabulce jsou uvedeny metriky, které byly v této verzi přidány nebo podstatně změněny. 

| **Nový termín**                   |    **Definice**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Cena (CC)                     | Cena za jednotku využití pro danou SKLADOVOU položku (v měně zákazníka).       |
| Odhadované rozšířené poplatky (CC) | Odhadované rozšířené náklady na množství jednotek využití pro danou skladovou jednotku (v měně zákazníka). Tato hodnota může být nepřesná, protože došlo k chybám zaokrouhlení nebo zkrácení.   |
| Měna vydavatele (PC)        | Měna, kterou Vydavatel upřednostňuje pro výběr.                               |
| Odhadovaná cena (PC)           | Odhadovaná cena jednotky využití pro danou SKLADOVOU jednotku na základě konverze cizí směny při využití data se počítá (v měně vydavatele). Tato hodnota může být nepřesná, protože došlo k chybám zaokrouhlení nebo zkrácení.   |
| Odhadované rozšířené poplatky (PC) | Odhadované rozšířené náklady na množství jednotek využití pro danou skladovou jednotku na základě konverze cizí směny při využití data se počítá (v měně vydavatele). Tato hodnota může být nepřesná, protože došlo k chybám zaokrouhlení nebo zkrácení. |
| Odhadovaný výběr (PC)          | Odhadovaná platba za množství jednotek využití pro danou skladovou jednotku na základě konverze cizí směny na datum, kdy se využití počítá (v měně vydavatele). Tato hodnota může být nepřesná, protože došlo k chybám zaokrouhlení nebo zkrácení.   |
| Odkaz na použití                | Identifikátor jednoho nebo více dnů využití zákazníka pro danou skladovou jednotku přidruženou k položce v sestavě výběr. |
|  |  |
