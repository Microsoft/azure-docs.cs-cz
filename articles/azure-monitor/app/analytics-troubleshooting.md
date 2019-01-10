---
title: Řešení potíží s analýzami ve službě Azure Application Insights | Dokumentace Microsoftu
description: 'Problémy s Application Insights analytics? Začněte tady. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2016
ms.author: mbullwin
ms.openlocfilehash: bf999e15c04a64f717bcd7bac87e357d5ccc92e6
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189928"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Řešení potíží s analýzami v nástroji Application Insights
Problémy s [Application Insights Analytics](analytics.md)? Začněte tady. Analytics je nástroj výkonné hledání služby Azure Application Insights.

## <a name="limits"></a>Omezení
* V současné době jsou omezené jenom za týden posledních dat výsledků dotazu.
* Testujeme v prohlížečích: nejnovější verze prohlížečů Chrome, Microsoft Edge a Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Rozšíření známé nekompatibilní prohlížeč
* Ghostery

Zakázat rozšíření nebo použijte jiný prohlížeč.

## <a name="e-a"></a> "Neočekávaná chyba"
![Došlo k neočekávané chybě obrazovky](media/analytics-troubleshooting/010.png)

Během portálu runtime neošetřené výjimky došlo k vnitřní chybě.

* Vymazat mezipaměť prohlížeče. 

## <a name="e-b"></a>403... Zkuste to prosím znovu načíst
![403... Zkuste to prosím znovu načíst](media/analytics-troubleshooting/020.png)

Došlo k chybě související s ověřováním (během ověřování nebo při generování přístupového tokenu). Portál možná nebude možné obnovit bez změny nastavení prohlížeče.

* Ověřte [jsou povolené soubory cookie třetích stran](#cookies) v prohlížeči. 

## <a name="authentication"></a>403... ověření zóny zabezpečení
![403.. .verify zóny zabezpečení](media/analytics-troubleshooting/030.png)

Došlo k chybě související s ověřováním (během ověřování nebo při generování přístupového tokenu). Portál možná nebude možné obnovit bez změny nastavení prohlížeče.

1. Ověřte [jsou povolené soubory cookie třetích stran](#cookies) v prohlížeči. 
2. Použili jste k otevření portálu Analytics oblíbenou položku, záložku nebo uložený odkaz? Jste přihlášeni s použitím jiných přihlašovacích údajů než těch, které jste použili při uložení odkazu?
3. Zkuste se přihlásit v soukromém nebo anonymním okně prohlížeče (před tím všechna taková okna zavřete). Budete muset zadat své přihlašovací údaje. 
4. Otevřete (další obyčejné) okno prohlížeče a přejděte na [Azure](https://portal.azure.com). Odhlaste se. Pak otevřete svůj odkaz a přihlaste se pomocí správných přihlašovacích údajů.
5. Uživatelé Microsoft Edge a Internet Explorer můžete také získat tuto chybu, když nejsou podporována nastavení důvěryhodné zóny.
   
    Jak ověřit [portál Analytics](https://portal.azure.com) a [portálu Azure Active Directory](https://portal.azure.com) jsou ve stejné zóně zabezpečení:
   
   * V Internet Exploreru otevřete **Možnosti Internetu**, **zabezpečení**, **Důvěryhodné servery**, **lokality**:
     
     ![Dialogové okno Možnosti Internetu, přidání serveru do důvěryhodných lokalit](media/analytics-troubleshooting/033.png)
     
     Pokud seznam Weby obsahuje některou z následujících adres URL, ujistěte se, že obsahuje i ty ostatní:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Prostředek se nenašel
![404... prostředek se nenašel](media/analytics-troubleshooting/040.png)

Prostředek aplikace už odstranil z Application Insights a t není k dispozici. To může nastat, pokud jste uložili adresu URL na stránce analýzy.

## <a name="e-e"></a>403 ... Žádné autorizace
![403... Neautorizováno](media/analytics-troubleshooting/050.png)

Nemáte oprávnění otevřít tuto aplikaci v Analytics.

* Obdrželi jste od někoho jiného odkaz? Požádejte ho, abyste měli jistotu, které jsou v [čtenáři a přispěvatelé této skupiny prostředků](../../azure-monitor/app/resources-roles-access-control.md).
* Jste uložili odkaz použitím různých přihlašovacích údajů? Otevřít [webu Azure portal](https://portal.azure.com), odhlaste se a pak zkuste tento odkaz znovu, poskytování správných přihlašovacích údajů.

## <a name="html-storage"></a>403 ... Úložiště HTML5
Náš portál používá HTML5 localStorage a sessionStorage.

* Chrome: Nastavení ochrany osobních údajů, nastavení obsahu.
* Aplikace Internet Explorer: Možnosti Internetu, Upřesnit, zabezpečení, povolte úložiště modelu DOM

![403... Zkuste povolit úložiště HTML5](media/analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Předplatné nebylo nalezeno.
![404 ... Předplatné nebylo nalezeno.](media/analytics-troubleshooting/070.png)

Adresa URL je neplatná. 

* Otevřete prostředek aplikace [portál Application Insights](https://portal.azure.com). Potom použijte tlačítko Analytics.

## <a name="e-h"></a>404... stránka neexistuje.
![404 ... Stránka neexistuje.](media/analytics-troubleshooting/080.png)

Adresa URL je neplatná.

* Otevřete prostředek aplikace [portál Application Insights](https://portal.azure.com). Potom použijte tlačítko Analytics.

## <a name="cookies"></a>Povolit soubory cookie třetích stran
  V tématu [zakázání soubory cookie třetích stran](https://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), ale Všimněte si, že musíme **povolit** je.


[!INCLUDE [app-insights-analytics-footer](../../../includes/app-insights-analytics-footer.md)]

