---
title: Řešení Azure VMware (AVS) – monitorování aktivity privátního cloudu služby AVS
description: V této části najdete popis dostupných informací o aktivitách v řešení Azure VMware pomocí služby AVS, včetně výstrah, událostí, úloh a auditu.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019668"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>Monitorování aktivity řešení VMware (AVS)

Protokoly aktivit služby AVS poskytují přehled o operacích provedených na portálu AVS. Seznam obsahuje výstrahy, události, úlohy a audit. Pomocí protokolů aktivit můžete určit, kdo a jaké operace byly provedeny. Protokoly aktivit nezahrnují žádné operace čtení provedené uživatelem.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Přístup k portálu pro funkci AVS

Přístup k [portálu AVS](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informace o aktivitě

Chcete-li získat přístup ke stránkám aktivity, vyberte v postranní nabídce položku **aktivita** .

![Přehled stránky aktivity](media/activity-page-overview.png)

Chcete-li zobrazit podrobnosti o jakékoli aktivitě na stránce aktivity, vyberte aktivitu. Otevře se panel podrobností na pravé straně. Akce na panelu závisí na typu aktivity. Kliknutím na **X** zavřete panel.

Kliknutím na záhlaví sloupce seřadíte zobrazení. Můžete filtrovat sloupce pro konkrétní hodnoty, které chcete zobrazit. Kliknutím na ikonu **Stáhnout jako sdílený svazek clusteru** si Stáhněte sestavu aktivity.

## <a name="alerts"></a>Výstrahy

Výstrahy jsou oznámením významné aktivity ve vašem prostředí služby AVS. Výstrahy obsahují události, které mají vliv na fakturaci nebo přístup uživatelů.

Pokud chcete výstrahy potvrdit a odebrat je ze seznamu, vyberte jednu nebo víc ze seznamu a klikněte na **Potvrdit**.

Pro výstrahy jsou k dispozici následující sloupce informací. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Column | Popis |
------------ | ------------- |
| Typ výstrahy | Kategorie výstrahy|
| Time | Čas, kdy došlo k upozornění. |
| Závažnost | Význam výstrahy.|
| Název prostředku | Název přiřazený k prostředku, například název privátního cloudu pro funkci AVS |
| Typ prostředku | Kategorie prostředku: privátní cloud AVS, cloudový stojan. |
| ID prostředku | Identifikátor prostředku |
| Popis | Popis, který výstrahu aktivoval. |
| Potvrzení | Označení, zda je výstraha potvrzena. |

## <a name="events"></a>Akce

Události zobrazují aktivitu uživatelů a systému na portálu pro funkci AVS. Na stránce události je uvedena aktivita přidružená ke konkrétnímu prostředku a závažnost dopadu.

Pro výstrahy jsou k dispozici následující sloupce informací. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Column | Popis |
------------ | ------------- |
| Time | Datum a čas, kdy došlo k události. |
| Typ události | Číselný kód identifikující událost. |
| Závažnost | Závažnost události.|
| Název prostředku | Název přiřazený k prostředku, například název privátního cloudu pro funkci AVS |
| Typ prostředku | Kategorie prostředku: privátní cloud AVS, cloudový stojan. |
| Popis | Popis, který výstrahu aktivoval. |

## <a name="tasks"></a>Úlohy

Úlohy jsou aktivity privátního cloudu pro funkci AVS, u kterých se očekává, že budou trvat 30 sekund nebo více. (Aktivity, které se očekávají za méně než 30 sekund, se oznamují jenom jako události.) Otevřete stránky úlohy a sledujte průběh úloh vašeho privátního cloudu služby AVS.

Pro výstrahy jsou k dispozici následující sloupce informací. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Column | Popis |
------------ | ------------- |
| ID úlohy | Jedinečný identifikátor úkolu |
| Operace | Akce, kterou úkol provede. |
| Uživatel | Uživatel přiřazený k dokončení úlohy. |
| Název prostředku | Název přiřazený k prostředku |
| Typ prostředku | Kategorie prostředku: privátní cloud AVS, cloudový stojan. |
| ID prostředku | Identifikátor prostředku |
| Začátek | Čas spuštění úlohy. |
| Konec | Čas ukončení úlohy |
| Stav | Aktuální stav úlohy. |
| Uplynulý čas | Čas potřebný k dokončení úkolu (Pokud je dokončen) nebo v současné době (pokud probíhá). |
| Popis | Popis úlohy |

## <a name="audit"></a>Auditování

Protokoly auditu udržují přehled o aktivitě uživatelů. Protokoly auditování můžete použít k monitorování aktivity uživatelů pro všechny uživatele.

Pro výstrahy jsou k dispozici následující sloupce informací. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Column | Popis |
------------ | ------------- |
| Time | Čas položky auditu |
| Operace | Akce, kterou úkol provede. |
| Uživatel | Uživatel přiřazený k úkolu |
| Název prostředku | Název přiřazený k prostředku |
| Typ prostředku | Kategorie prostředku: privátní cloud AVS, cloudový stojan. |
| ID prostředku | Identifikátor prostředku |
| Výsledek | Výsledek aktivity, například **úspěch**. |
| Doba trvání | Čas k dokončení úkolu. |
| Popis | Popis akce |

## <a name="next-steps"></a>Další kroky

* [Využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md) pro funkci AVS
