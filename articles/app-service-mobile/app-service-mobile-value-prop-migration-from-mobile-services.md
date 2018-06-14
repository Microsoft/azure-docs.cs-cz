---
title: Používám Mobile Services, jak mi pomůže App Service?
description: Zjistěte, jaké výhody přináší služba App Service pro existující projekty Mobile Services.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 26b68a11-8352-4f78-acd2-e4e0ec177781
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 365f00ced38a1ddc20df211121fba43efff8ea87
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
ms.locfileid: "29460408"
---
# <a name="getting-started"></a>Používám Mobile Services, jak mi pomůže App Service?
## <a name="overview"></a>Přehled
Vaše stávající služba Mobile Service je v bezpečí a bude i nadále podporována. Existují však výhody, které vaší mobilní aplikaci poskytuje platforma *Azure App Service* a které dnes ve službě Mobile Services nejsou k dispozici:

* Jednodušší, snazší a nákladově efektivnější nabídka pro aplikace, které obsahují jak webové, tak mobilní klienty
* Nové hostitelské funkce včetně webových úloh, vlastní záznamy CNAME, lepší monitorování
* Integrace se službou Traffic Manager
* Možnosti připojení k místním prostředkům a sítím VPN pomocí virtuální sítě a Hybrid Connections
* Monitorování, výstrahy a řešení potíží pro aplikace pomocí Application Insights
* Širší spektrum využívaných výpočetních prostředků a cenových úrovní
* Integrované automatické škálování, vyrovnávání zatížení a monitorování výkonu
* Integrované funkce pro fázování, zálohování, vrácení zpět a testování za provozu

## <a name="new-hosting-features"></a>Nové hostitelské funkce
V *Azure App Service* běží back-end kód *mobilní aplikace* ve stejném kontejneru jako webová aplikace a aplikace API. Můžete využívat všechny funkce tohoto kontejneru, včetně některých funkcí, které nejsou v tuto chvíli dostupné ve službě Mobile Services:

* Přidání neustále běžící back-end logiky přes webové úlohy
* Zajištění, že back-end kód bude vždy spuštěn
* Použití vlastních záznamů CNAME, aby byly k dispozici popisné a stabilní názvy koncových bodů mobilního back-endu
* Geografické škálování aplikace pomocí nástroje Traffic Manager
* Zahrnutí jakýchkoli knihoven nebo balíčků, které chcete použít
* (Pro .NET) Využití jakékoli funkce technologie ASP.NET, včetně MVC
* (Pro Node.js) Využití jakékoli knihovny čistého JavaScriptu ekosystému Node, včetně běžných knihoven MVC

## <a name="access-on-premises-data-using-vnet"></a>Přístup k lokálním datům přes virtuální síť
Již dnes můžete pomocí služby Mobile Services používat Hybrid Connections pro přístup k místním prostředkům. Existují však situace, kdy je lepší použít řešení se sítí VPN. S *Azure App Service* můžete pro back-end kód mobilní aplikace použít virtuální síť Azure.

## <a name="use-your-favorite-backend-language"></a>Využití oblíbeného jazyka pro back-end
Služba *Azure App Service* nabízí širší a bohatší podporu platforem ASP.NET a Node.js, včetně přístupu k nejnovějším modulům runtime.

## <a name="set-up-automatic-scale"></a>Nastavení automatického škálování
S Mobile Services běžely všechny instance back-end kódu na malých virtuálních počítačích. Služba *Azure App Services* umožňuje vybrat velikost virtuálního počítače z mnohem širší nabídky. Kromě toho je možné podle různých metrik výkonu rychle změnit škálování (vertikálně nebo horizontálně navýšit kapacitu), abyste zvládali zátěž vyplývající z příchozích zákaznických požadavků.

## <a name="be-in-the-know"></a>Neustálý přehled
Na problémy dokážete reagovat v reálném čase díky monitorování a výstrahám, které vám a vašemu týmu automaticky odešlou oznámení. Integrací pokročilých funkcí analýzy a monitorování aplikací z Application Insights získáte přehled o výkonu vaší mobilní aplikace. S *Azure App Service* nyní můžete nastavit upozornění na základě různých metrik výkonu, a to buď prostřednictvím kódu programu, nebo přes web Azure Portal.

## <a name="keep-your-assets-safe"></a>Zabezpečení prostředků
Back-end a databáze je možné automaticky zálohovat. Kód a data jsou zabezpečeny proti havárii a lze je snadno obnovit. Díky tomu můžete řídit svou firmu s jistotou.

## <a name="ready-stage-go"></a>Připravit, naplánovat, start!
S *Azure App Service* nyní můžete pro své mobilní aplikace vytvořit několik privátních testovacích a přípravných prostředí. Můžete je využít k otestování aplikací předtím, než je nasadíte. Do ostrého provozu je možné přejít zcela bez výpadku. Webové aplikace se načítají předem – díky tomu mají zákazníci maximální pohodlí.

Výhody *App Service* pro existující službu Mobile Service můžete začít využívat tak, že si projdete tento [kurz](app-service-mobile-migrating-from-mobile-services.md).
