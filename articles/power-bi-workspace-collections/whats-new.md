---
title: Novinky v kolekcích pracovních prostorů Power BI
description: Získat nejnovější informace o tom, co je nového v kolekcích pracovních prostorů Power BI
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 0fe3116934ac4ab9b1e3fd3b5efcd6627df0ed3d
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046925"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Novinky v kolekcích pracovních prostorů Power BI

Aktualizace **kolekce pracovních prostorů Power BI** vydávají v pravidelných intervalech. Nicméně ne každá vydaná verze obsahuje nové funkce přístupných; Některé verze se zaměřuje na schopnosti služeb back-end. Zvýrazněte jsme nové možnosti přístupných tady.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>. Března 2017

**Samoobslužné funkce**

* [Vytvořit novou sestavu](create-report-from-dataset.md)
* [Uložit jako sestavu](save-reports.md)
* Vložení sestavy do nového režimu pro čtení nebo úpravy nebo vytvoření 
* [Přepnout mezi režimy úprav/čtení sestavy](toggle-mode.md)

**Připojení dat pomocí rozhraní REST API**

* [Vytvoření datové sady](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Zápis dat 

**Rozhraní API pro správu**

* Klonování sestavu a datovou sadu
* Svázat s jinou datovou sadu sestavy

**Ukázky**

* Aktualizovat [ukázku vložení sestavy jazyka JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>Prosinec 2016

* [Vložená ukázka JavaScriptu nové](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Říjen 2016

* [Pokročilé analýzy s Power BI pracovního prostoru kolekce a jazyka R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>31. srpna 2016
Součástí této verze:

* Všechny nové sady JavaScript SDK, který podporuje [rozšířené filtrování a navigace na stránce](interact-with-reports.md).
* Kolekce pracovních prostorů Power BI jsou nyní podporovány v datovém centru Kanada – střed. Zkontrolujte [datacenter stav](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11. července 2016
Součástí této verze:

* **Skvělé zprávy!** Služba kolekce pracovních prostorů Power BI už není ve verzi preview – jeho nyní GA (obecně dostupné).  
* Všechna rozhraní REST API přesunuty z **/beta** k **/v1.0**.
* .NET a sady SDK pro JavaScript byl aktualizován pro **v1.0**.
* Power BI API volání můžete nyní přímo ověřit pomocí klíče rozhraní API. Tokeny aplikací jsou zapotřebí pouze pro vkládání. Jako součást tohoto postupu zřizování a vývoj tokeny se již nepoužívají v verze 1.0 rozhraní API, ale budou dál fungovat ve verzi beta až do 30. prosince 2016. Další informace najdete v tématu [ověřování a autorizace pomocí kolekcí pracovních prostorů Power BI](app-token-flow.md).
* Řádek podpora zabezpečení na úrovni (RLS) tokenů aplikace a integrovaných sestav. Další informace najdete v tématu [s kolekcemi pracovních prostorů Power BI zabezpečení na úrovni řádků](row-level-security.md).
* Aktualizovat ukázkovou aplikaci pro všechny **v1.0** volání rozhraní API.
* Kolekce pracovních prostorů Power BI podporují pro sadu Azure SDK, PowerShell a rozhraní příkazového řádku.
* Uživatelé mohou exportovat data vizualizace **CSV**.
* Kolekce pracovních prostorů Power BI jsou nyní podporovány ve všech stejné jazyky nebo národní prostředí jako Microsoft Azure. Další informace najdete v tématu [Azure – jazyky](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

