---
title: Plánování kapacity pro role serveru služby Azure App Service v zásobníku Azure | Microsoft Docs
description: Plánování kapacity pro role serveru služby Azure App Service v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: f54481fe59df21b500ee860d1e9a202ed32bdd87
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097144"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Plánování kapacity pro role serveru služby Azure App Service v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Pokud chcete nastavit připravené produkční nasazení služby Azure App Service v zásobníku Azure, je nutné naplánovat kapacitu očekáváte, že systému pro podporu.  

Tento článek obsahuje pokyny pro minimální počet instancí výpočetní a výpočetní jednotky SKU byste měli použít pro všechna produkční nasazení.

Můžete naplánovat strategie kapacity služby App Service pomocí těchto pokynů. Budoucích verzích zásobník Azure poskytuje možnosti vysoké dostupnosti pro službu App Service.

| Role serveru služby App Service | Minimální doporučená počet instancí | Doporučené výpočetní SKU|
| --- | --- | --- |
| Kontroler | 2 | A1 |
| Front-end | 2 | A1 |
| Správa | 2 | A3 |
| Vydavatel | 2 | A1 |
| Pracovníci web - sdílené | 2 | A1 |
| Pracovníci web - dedicated | 2 na vrstvě | A1 |

## <a name="controller-role"></a>Role řadiče

**Doporučená minimální**: dvě instance A1 Standard

Azure App Service Controller obvykle dojde nízkou spotřeby procesoru, paměti a síťovým prostředkům. Pro zajištění vysoké dostupnosti, ale musí mít dva řadiče. Dva řadiče jsou také maximální počet řadičů povolené. Můžete vytvořit druhý řadič weby přímé z instalačního programu během nasazení.

## <a name="front-end-role"></a>Front End role

**Doporučená minimální**: dvě instance A1 Standard

Front-endu směruje požadavky webové pracovníkům v závislosti na dostupnosti webového pracovní. Pro zajištění vysoké dostupnosti měli byste mít více než jeden Front-endu a může mít více než dvě. Pro účely plánování kapacity zvažte, že každý základní může zpracovat přibližně 100 požadavků za sekundu.

## <a name="management-role"></a>Role správy

**Doporučená minimální**: dvě instance standardní A3

Role služby správy aplikací Azure zodpovídá za aplikace služby Azure Resource Manager a koncové body rozhraní API, portálu rozšíření (správce, klienta funkce portálu.) a službu data. Role serveru pro správu jsou obvykle vyžaduje pouze o 4 GB paměti RAM v produkčním prostředí. Ale dojít vysokou úroveň využití procesoru, pokud se provádí řadu úloh správy (například vytvoření webu). Pro zajištění vysoké dostupnosti měli byste mít více než jeden server přiřazený k této roli, a alespoň dvě jádra na serveru.

## <a name="publisher-role"></a>Role vydavatele

**Doporučená minimální**: dvě instance A1 Standard

Pokud mnoho uživatelů současně publikují, roli vydavatele setkat s velkou využití procesoru. Pro zajištění vysoké dostupnosti zpřístupníte víc než jednu roli vydavatele.  Vydavatel zpracovává jenom provoz FTP/FTPS.

## <a name="web-worker-role"></a>Webové role pracovního procesu

**Doporučená minimální**: dvě instance A1 Standard

Pro zajištění vysoké dostupnosti měli byste mít alespoň čtyři webové role pracovního procesu, dvě pro sdílené webové stránky režim a dvě pro každou vrstvu vyhrazených pracovních chcete nabízet. Sdílené a režimy vyhrazeným výpočetním poskytují různé úrovně služby klientům. Můžete potřebovat další webové pracovníci Pokud řadu vašim zákazníkům:

- Používání vyhrazeným výpočetním režimu pracovníka vrstev (které jsou prostředky).
- Spuštěna v režimu sdílené výpočetní.

Jakmile uživatel vytvoří plán služby App Service pro režim vyhrazeným výpočetním SKU, počet webové pracovníky zadaný v, že plán služby App Service bude nadále již nebudou dostupné pro uživatele.

Zajistit Azure Functions na uživatele ve model plánu spotřeby musíte nasadit sdílené pracovníci Web.

Při rozhodování o počtu sdílených rolí pracovního procesu Web pro použití, přečtěte si tyto aspekty:

- **Paměť**: paměti je nejdůležitější prostředku pro roli pracovního procesu Web. Nedostatek paměti ovlivňuje výkonu webu, když je virtuální paměti prohodily z disku. Každý server vyžaduje asi 1,2 GB paměti RAM pro operační systém. Paměť RAM nad touto prahovou hodnotou slouží ke spuštění webové stránky.
- **Procento webů active**: obvykle o 5 procent aplikace v Azure App Service na nasazení zásobník Azure aktivní. Však procento žádostí, které jsou aktivní v každém okamžiku může být vyšší nebo nižší. Se aktivní aplikační kurzem 5 procent, maximální počet aplikací k umístění v Azure App Service na Azure zásobníku nasazení by měl být menší než:
  - 20krát počet active webů (5 × 20 = 100).
- **Průměrná obsazeného**: nároky na paměť průměrná pro aplikace dodržen v produkčním prostředí je přibližně 70 MB. Pomocí této nároky, je paměť přidělená mezi všechny pracovní webové role počítače nebo virtuální počítače můžete vypočítat následujícím způsobem:

    *Počet aplikací, zajištěno * 70 MB * 5 % - (počet rolí pracovního procesu webové * 1044 MB)*

   Například pokud je 5 000 aplikací v prostředí, které běží 10 rolí pracovního procesu Web, musí mít každý pracovní webové role virtuálních počítačů 7060 MB paměti RAM:

   5 000 * 70 * 0,05 – (10 * 1044) = 7060 (= 7 GB)

   Informace o přidání více instancí pracovního procesu naleznete v tématu [přidání více rolí pracovního procesu](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Role souborového serveru

Pro roli souborového serveru, můžete použít samostatný souborový server pro vývoj a testování, například při nasazení služby Azure App Service na Development Kit zásobník Azure můžete použít tuto šablonu - <https://aka.ms/appsvconmasdkfstemplate>. Pro produkční účely měli byste použít předem nakonfigurovaná souborovém serveru Windows nebo předem nakonfigurovaný soubor jiný systém než Windows server.

V produkčním prostředí role souborového serveru narazí náročné diskové vstupně-výstupní operace. Vzhledem k tomu, že ho ve uložený všechny soubory obsahu a aplikace pro uživatele webové servery, musíte předem nakonfigurovat jednu z následujících akcí pro tuto roli:

- Souborový Server Windows
- Cluster souborových serverů s Windows
- soubor jiný systém než Windows server
- cluster souborových jiný systém než Windows server
- zařízení NAS (Network Attached Storage)

Další informace najdete v tématu [zřídit souborový server](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Další postup

[Před zahájením práce s App Service v Azure zásobníku](azure-stack-app-service-before-you-get-started.md)
