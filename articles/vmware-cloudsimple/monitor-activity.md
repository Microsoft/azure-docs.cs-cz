---
title: Sledování aktivity privátního cloudu
titleSuffix: Azure VMware Solution by CloudSimple
description: Popisuje informace dostupné o aktivitách v řešení Azure VMware podle cloudového jednoduchého prostředí, včetně výstrah, událostí, úkolů a auditu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019668"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitorování řešení VMware podle aktivity CloudSimple

CloudSimple protokoly aktivit poskytují přehled o operacích provedených na portálu CloudSimple.  Seznam obsahuje výstrahy, události, úkoly a audit.  Protokoly aktivit slouží k určení, kdo, kdy a jaké operace byly provedeny.  Protokoly aktivit neobsahují žádné operace čtení provedené uživatelem.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informace o aktivitě

Chcete-li získat přístup ke stránkám Aktivita, vyberte v boční nabídce položku **Aktivita.**

![Přehled stránky aktivity](media/activity-page-overview.png)

Chcete-li zobrazit podrobnosti o některé z aktivit na stránce aktivity, vyberte aktivitu. Vpravo se otevře panel podrobností. Akce v panelu závisí na typu aktivity. Klepnutím na **x** panel zavřete.

Klikněte na záhlaví sloupce pro seřazení displeje.  Sloupce můžete filtrovat pro konkrétní hodnoty, které chcete zobrazit.  Stáhněte si zprávu o aktivitách kliknutím na **ikonu Stáhnout jako CSV.**

## <a name="alerts"></a>Výstrahy

Výstrahy jsou oznámení o jakékoli významné aktivity ve vašem clouduSimple prostředí.  Výstrahy zahrnují události, které ovlivňují fakturaci nebo přístup uživatelů.

Chcete-li potvrdit výstrahy a odebrat je ze seznamu, vyberte jednu nebo více ze seznamu a klepněte na tlačítko **Potvrdit**.

Následující sloupce informací jsou k dispozici pro výstrahy. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Sloupec | Popis |
------------ | ------------- |
| Typ výstrahy | Kategorie výstrahy.|
| Time | Čas, kdy došlo k upozornění. |
| Severity | Význam výstrahy.|
| Název prostředku | Název přiřazený prostředku, například název privátního cloudu. |
| Typ prostředku | Kategorie zdrojů: Privátní cloud, Cloud Rack. |
| ID prostředku | Identifikátor prostředku. |
| Popis | Popis toho, co spustilo výstrahu. |
| Potvrzeno | Údaj o tom, zda je záznam potvrzen. |

## <a name="events"></a>Události

Události ukazují aktivitu uživatelů a systému na portálu CloudSimple. Na stránce Události je uvedena aktivita přidružená k určitému prostředku a závažnost dopadu.

Následující sloupce informací jsou k dispozici pro výstrahy. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Sloupec | Popis |
------------ | ------------- |
| Time | Datum a čas došlo k události. |
| Event Type | Číselný kód, který identifikuje událost. |
| Severity | Závažnost události.|
| Název prostředku | Název přiřazený prostředku, například název privátního cloudu. |
| Typ prostředku | Kategorie zdrojů: Privátní cloud, Cloud Rack. |
| Popis | Popis toho, co spustilo výstrahu. |

## <a name="tasks"></a>Úlohy

Úkoly jsou aktivity privátního cloudu, u kterých se očekává dokončení 30 sekund nebo déle. (Aktivity, u kterých se očekává, že budou trvat méně než 30 sekund, jsou hlášeny pouze jako události.) Otevřete stránky Úkoly a sledujte průběh úkolů privátního cloudu.

Následující sloupce informací jsou k dispozici pro výstrahy. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Sloupec | Popis |
------------ | ------------- |
| ID úlohy | Jedinečný identifikátor úkolu |
| Operace | Akce, kterou úloha provede. |
| Uživatel | Uživatel přiřazený k dokončení úkolu. |
| Název prostředku | Název přiřazený zdroji. |
| Typ prostředku | Kategorie zdrojů: Privátní cloud, Cloud Rack. |
| ID prostředku | Identifikátor prostředku. |
| Start | Čas zahájení úkolu |
| End | Čas ukončení úkolu. |
| Status | Aktuální stav úkolu. |
| Uplynulý čas | Čas, který úkol trvalo k dokončení (pokud je dokončen) nebo je právě přijímá (pokud probíhá). |
| Popis | Popis úkolu. |

## <a name="audit"></a>Auditování

Protokoly auditu sledují aktivitu uživatele. Protokoly auditu můžete použít ke sledování aktivity uživatelů pro všechny uživatele.

Následující sloupce informací jsou k dispozici pro výstrahy. Klikněte na **Upravit sloupce** a vyberte sloupce, které chcete zobrazit.

| Sloupec | Popis |
------------ | ------------- |
| Time | Čas zadání auditu. |
| Operace | Akce, kterou úloha provede. |
| Uživatel | Uživatel přiřazený k úkolu. |
| Název prostředku | Název přiřazený zdroji. |
| Typ prostředku | Kategorie zdrojů: Privátní cloud, Cloud Rack. |
| ID prostředku | Identifikátor prostředku. |
| Výsledek | Výsledek aktivity, například **Úspěch**. |
| Doba trvání | Čas na dokončení úkolu. |
| Popis | Popis akce. |

## <a name="next-steps"></a>Další kroky

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md)
