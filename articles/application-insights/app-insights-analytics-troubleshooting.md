---
title: Řešení potíží s Analytics ve službě Azure Application Insights | Microsoft Docs
description: 'Problémy s Application Insights analytics? Začněte tady. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2016
ms.author: mbullwin
ms.openlocfilehash: c479298187cfc4af6d9b33d9bd3839e8fa579566
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294155"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Řešení potíží s analýzami v nástroji Application Insights
Problémy s [Application Insights Analytics](app-insights-analytics.md)? Začněte tady. Analytics je nástroj výkonné vyhledávání systému Azure Application Insights.

## <a name="limits"></a>Omezení
* V současné době jsou omezená na právě přes týden posledních dat výsledky dotazu.
* Prohlížeče jsme test na: nejnovější vydání systému Chrome, okraj a prohlížeče Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Rozšíření známé kompatibilní prohlížeče
* Ghostery

Rozšíření zakázat, nebo použijte jiný prohlížeč.

## <a name="e-a"></a> "Neočekávané error.
![Došlo k neočekávané chybě obrazovky](./media/app-insights-analytics-troubleshooting/010.png)

Běhovém portálu – neošetřené výjimky došlo k vnitřní chybě.

* Vyčištění mezipaměti prohlížeče. 

## <a name="e-b"></a>403... Zkuste to prosím znovu načíst
![403... Zkuste to prosím znovu načíst](./media/app-insights-analytics-troubleshooting/020.png)

Došlo k chybě související s ověřováním (během ověřování nebo při generování přístupového tokenu). Na portálu může mít žádný způsob, jak obnovit beze změny nastavení prohlížeče.

* Ověřte [jsou povolené soubory cookie třetích stran](#cookies) v prohlížeči. 

## <a name="authentication"></a>403... Ověřte zóny zabezpečení
![403.. .verify zóny zabezpečení](./media/app-insights-analytics-troubleshooting/030.png)

Došlo k chybě související s ověřováním (během ověřování nebo při generování přístupového tokenu). Na portálu může mít žádný způsob, jak obnovit beze změny nastavení prohlížeče.

1. Ověřte [jsou povolené soubory cookie třetích stran](#cookies) v prohlížeči. 
2. Použili jste k otevření portálu Analytics oblíbenou položku, záložku nebo uložený odkaz? Jste přihlášeni s použitím jiných přihlašovacích údajů než těch, které jste použili při uložení odkazu?
3. Zkuste se přihlásit v soukromém nebo anonymním okně prohlížeče (před tím všechna taková okna zavřete). Budete muset zadat své přihlašovací údaje. 
4. Otevře další okno (obyčejnou) prohlížeče a přejděte na [Azure](https://portal.azure.com). Odhlaste se. Pak otevřete svůj odkaz a přihlaste se pomocí správných přihlašovacích údajů.
5. Uživatelům používajícím Microsoft Edge a Internet Explorer se tato chyba může zobrazit i v případě, že nejsou podporována nastavení důvěryhodné zóny.
   
    Ověřte obě [portálu analýza](https://analytics.applicationinsights.io) a [portál Azure Active Directory](https://portal.azure.com) jsou ve stejné zóny zabezpečení:
   
   * V Internet Exploreru otevřete **Možnosti Internetu**, **zabezpečení**, **Důvěryhodné servery**, **lokality**:
     
     ![Dialogové okno Možnosti Internetu, přidání webu do důvěryhodných serverů](./media/app-insights-analytics-troubleshooting/033.png)
     
     Pokud seznam Weby obsahuje některou z následujících adres URL, ujistěte se, že obsahuje i ty ostatní:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Prostředek se nenašel
![404... prostředek nebyl nalezen](./media/app-insights-analytics-troubleshooting/040.png)

Prostředek aplikace byla odstraněna z Application Insights a už není dostupný. To může dojít, pokud jste uložili adresu URL na stránku Analytics.

## <a name="e-e"></a>403 ... Žádné oprávnění
![403... neautorizovaných](./media/app-insights-analytics-troubleshooting/050.png)

Nemáte oprávnění k otevření této aplikace v Analytics.

* Obdrželi jste od někoho jiného odkaz? Požádejte je o zkontrolujte, zda jsou v [čtečky nebo přispěvatele pro tuto skupinu prostředků](app-insights-resources-roles-access-control.md).
* Uložíte odkaz použitím různých přihlašovacích údajů? Otevřete [portál Azure](https://portal.azure.com), odhlásit se a potom zkuste tohoto propojení znovu, zajištění správné přihlašovací údaje.

## <a name="html-storage"></a>403 ... Úložiště HTML5
Náš portál používá HTML5 localStorage a sessionStorage.

* Chrome: Nastavení ochrany osobních údajů, nastavení obsahu.
* Internet Explorer: Možnosti Internetu, karta Upřesnit, zabezpečení, povolte úložiště DOM

![403... Zkuste povolit úložiště HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Odběr nebyl nalezen
![404 ... Odběr nebyl nalezen](./media/app-insights-analytics-troubleshooting/070.png)

Adresa URL je neplatná. 

* Otevřete prostředek aplikace v [portál Application Insights](https://portal.azure.com). Potom pomocí tlačítka Analytics.

## <a name="e-h"></a>404... stránka neexistuje
![404 ... Stránka neexistuje.](./media/app-insights-analytics-troubleshooting/080.png)

Adresa URL je neplatná.

* Otevřete prostředek aplikace v [portál Application Insights](https://portal.azure.com). Potom pomocí tlačítka Analytics.

## <a name="cookies"></a>Povolit soubory cookie třetích stran
  V tématu [zakázání soubory cookie třetích stran](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), ale Všimněte si, musíme **povolit** je.


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

