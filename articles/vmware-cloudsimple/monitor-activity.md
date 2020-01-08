---
title: Aktivita monitorování privátního cloudu
titleSuffix: Azure VMware Solution by CloudSimple
description: Popisuje informace, které jsou k dispozici v aktivitě v řešení Azure VMware podle CloudSimple prostředí, včetně výstrah, událostí, úloh a auditu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459202"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitorování aktivity řešení VMware podle CloudSimple

Protokoly aktivit CloudSimple poskytují přehled o operacích provedených na portálu CloudSimple.  Seznam obsahuje výstrahy, události, úlohy a audit.  Pomocí protokolů aktivit můžete určit, kdo a jaké operace byly provedeny.  Protokoly aktivit nezahrnují žádné operace čtení provedené uživatelem.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informace o aktivitě

Chcete-li získat přístup ke stránkám aktivity, vyberte v postranní nabídce položku **aktivita** .

![Přehled stránky aktivity](media/activity-page-overview.png)

Chcete-li zobrazit podrobnosti o jakékoli aktivitě na stránce aktivity, vyberte aktivitu. Otevře se panel podrobností na pravé straně. Akce na panelu závisí na typu aktivity. Kliknutím na **X** zavřete panel.

Kliknutím na záhlaví sloupce seřadíte zobrazení.  Můžete filtrovat sloupce pro konkrétní hodnoty, které chcete zobrazit.  Kliknutím na ikonu **Stáhnout jako sdílený svazek clusteru** si Stáhněte sestavu aktivity.

## <a name="alerts"></a>Výstrahy

Výstrahy jsou oznámením významné aktivity v prostředí CloudSimple.  Výstrahy obsahují události, které mají vliv na fakturaci nebo přístup uživatelů.

Pokud chcete výstrahy potvrdit a odebrat je ze seznamu, vyberte jednu nebo víc ze seznamu a klikněte na **Potvrdit**.

Pro výstrahy jsou k dispozici následující sloupce informací. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Column | Popis |
------------ | ------------- |
| Typ výstrahy | Kategorie výstrahy|
| Time | Čas, kdy došlo k upozornění. |
| Závažnost | Význam výstrahy.|
| Název prostředku | Název přiřazený k prostředku, jako je například název privátního cloudu. |
| Typ prostředku | Kategorie prostředku: privátní cloud, cloudový stojan. |
| ID prostředku | Identifikátor prostředku |
| Popis | Popis, který výstrahu aktivoval. |
| Potvrzení | Označení, zda je výstraha potvrzena. |

## <a name="events"></a>Akce

Události zobrazují aktivitu uživatelů a systémů na portálu CloudSimple. Na stránce události je uvedena aktivita přidružená ke konkrétnímu prostředku a závažnost dopadu.

Pro výstrahy jsou k dispozici následující sloupce informací. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Column | Popis |
------------ | ------------- |
| Time | Datum a čas, kdy došlo k události. |
| Typ události | Číselný kód identifikující událost. |
| Závažnost | Závažnost události.|
| Název prostředku | Název přiřazený k prostředku, jako je například název privátního cloudu. |
| Typ prostředku | Kategorie prostředku: privátní cloud, cloudový stojan. |
| Popis | Popis, který výstrahu aktivoval. |

## <a name="tasks"></a>Úlohy

Úlohy jsou aktivity privátního cloudu, u kterých se očekává, že budou trvat 30 sekund nebo déle. (Aktivity, které se očekávají za méně než 30 sekund, se oznamují jenom jako události.) Otevřete stránky úlohy a sledujte průběh úloh pro váš privátní cloud.

Pro výstrahy jsou k dispozici následující sloupce informací. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Column | Popis |
------------ | ------------- |
| ID úkolu | Jedinečný identifikátor úkolu |
| Operace | Akce, kterou úkol provede. |
| Uživatel | Uživatel přiřazený k dokončení úlohy. |
| Název prostředku | Název přiřazený k prostředku |
| Typ prostředku | Kategorie prostředku: privátní cloud, cloudový stojan. |
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
| Typ prostředku | Kategorie prostředku: privátní cloud, cloudový stojan. |
| ID prostředku | Identifikátor prostředku |
| Výsledek | Výsledek aktivity, například **úspěch**. |
| Doba trvání | Čas k dokončení úkolu. |
| Popis | Popis akce |

## <a name="next-steps"></a>Další kroky

* [Využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md)
