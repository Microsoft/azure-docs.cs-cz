---
title: Co je nového v Power BI prostoru kolekce
description: Získat nejnovější informace na co je nového v Power BI prostoru kolekce
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 647b571fc05dca12ab9d4db45236962e4794ef4b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408247"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Co je nového v Power BI prostoru kolekce

Aktualizace **kolekce pracovních prostorů Power BI** vydávají v pravidelných intervalech. Však nemusí být vždy verze obsahuje nové funkce uživatelsky orientovaný; Některé verze se zaměřuje na funkce back endové službě. Zvýrazněte jsme nové funkce uživatelsky orientovaný sem.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>2017 března

**Schopnosti samoobslužné služby**

* [Vytvořit novou sestavu](create-report-from-dataset.md)
* [Sestavu uložit jako](save-reports.md)
* Vložení sestavy v režimu pro čtení, upravit nebo vytvořit nový 
* [Sestava přepínání mezi režimy úprav/čtení](toggle-mode.md)

**Datové připojení ke službě REST API**

* [Vytvoření datové sady](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Zápis dat 

**Rozhraní API pro správu**

* Klon sestavy a datové sady
* Vazbu sestavy na jinou sadu dat

**Ukázky**

* Aktualizovat [sestavy JavaScript pro vložení ukázka](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>Prosinec 2016

* [Ukázka vložit nový JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Října 2016

* [Pokročilou analýzu s Power BI prostoru kolekce a R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>31 srpna 2016
Součástí této verze:

* Všechny nové JavaScript SDK, která podporuje [rozšířené filtrování a stránky navigační](interact-with-reports.md).
* Kolekce pracovních prostorů Power BI se teď podporují v centrální Kanada datového centra. Zkontrolujte [stav datacenter](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11. července 2016
Součástí této verze:

* **Skvělé zprávy!** Služba Power BI prostoru kolekce je už ve verzi preview - jeho nyní GA (obecně k dispozici).  
* Všechna rozhraní API REST byl přesunut z **/beta** k **/v1.0**.
* Rozhraní .NET a JavaScript SDK se aktualizovala pro **verze 1.0**.
* Power BI API – volání lze ověřit nyní přímo pomocí klíče rozhraní API. Tokeny aplikací jsou potřeba jenom pro vložení. Jako součást tohoto zřizování a vývojářů tokeny jsou zastaralé v rozhraní API verze 1.0, ale budete pokračovat v betaverzi fungovat až do 30. prosinci 2016. Další informace najdete v tématu [ověřování a autorizaci s Power BI prostoru kolekce](app-token-flow.md).
* Podpora zabezpečení na úrovni (RLS) řádek pro tokeny aplikací a vložené sestavy. Další informace najdete v tématu [řádek zabezpečení na úrovni s Power BI prostoru kolekce](row-level-security.md).
* Aktualizovat ukázkovou aplikaci pro všechny **verze 1.0** volání rozhraní API.
* Kolekce pracovních prostorů Power BI podporu pro Azure SDK, prostředí PowerShell a rozhraní příkazového řádku.
* Uživatelé mohou exportovat vizualizace dat **.csv**.
* Kolekce pracovních prostorů Power BI jsou nyní podporovány ve všech stejné jazyky nebo národní prostředí jako Microsoft Azure. Další informace najdete v tématu [Azure - jazyky](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

