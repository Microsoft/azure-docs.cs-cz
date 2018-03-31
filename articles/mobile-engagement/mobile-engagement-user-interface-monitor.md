---
title: Azure Mobile Engagement uživatelské rozhraní – monitorování
description: Naučte se monitorovat data v reálném čase o vaší aplikaci pomocí Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b91ad89a-b89d-4377-abb0-cc2d16a2836d
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b370a5db1acee37ce234f5ec3f004be20719cfea
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-monitor-real-time-data-about-your-application"></a>Postup sledování v reálném čase dat o aplikaci
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento článek popisuje **monitorování** kartě **Mobile Engagement** portálu. Můžete použít **Mobile Engagement** portálu ke sledování a správě mobilních aplikací. Všimněte si, že pokud chcete začít používat portál musíte nejprve vytvořit **Azure Mobile Engagement** účtu. 

V části monitorování uživatelského rozhraní obsahuje informace analýzu v reálném čase a umožňuje nastavit výstrahy při dosažení prahové hodnoty pro většinu stejné informace, které je k dispozici v minulosti v [ANALYTICS](mobile-engagement-user-interface-analytics.md) část uživatelského rozhraní. Najdete v článku **Glosář** tématu [koncepty](http://go.microsoft.com/fwlink/?LinkId=525555) tématu definice termínů a zkratek v analýzy a monitorování (například následující: aktivního uživatele, nový uživatel uchovávají uživatele, relace, cesta uživatele Graf, uživatelé mapy, sledování adres URL, trendy, aktivity, události, úlohy, chyba, doplňující informace, havárií a App-info).

> [!NOTE]
> Mnoho části **Mobile Engagement** portál obsahovat uživatelského rozhraní **zobrazit NÁPOVĚDU k** tlačítko. Chcete-li získat další kontextové informace o oddílu na toto tlačítko.
> 
> 

## <a name="monitor---sessions-jobs-events-errors-and-crashes"></a>Monitorování – relací, úlohy, události, chyb a dojde k chybě
Zobrazí počet uživatelů, kteří jsou aktuálně v relaci a na konkrétní obrazovky nebo provádění určitých akcí. Můžete zobrazit aktivitu uživatelů rozdělené podle relací, úlohy, události, chyb a dojde k chybě. Můžete zobrazit informace o aktuálním a zobrazit informace z poslední hodinu, den nebo týden. Zobrazí se všechny informace v každé kategorii nebo řazení podle konkrétní relace, úlohy, události, chyb a havárií.  Monitorování za provozu je vhodné použít během události, jako je například kampaně nabízených, pokud je uptick v akci správné po odeslání nabízených oznámení.

![Monitor1][14]  

## <a name="troubleshooting-with-monitor---events---details"></a>Řešení potíží s monitorování - události – podrobnosti
Generování událost v aplikaci z testovací zařízení a hledání v monitorování - události – podrobnosti je jedním z nejjednodušších způsobů chcete zjistit ID zařízení pro testovací zařízení a potvrďte, že integrace Azure Mobile Engagement Analytics, monitorování, a Segmenty pracuje z vaší aplikace. Až budete mít ID zařízení v testovací zařízení, můžete ho přidat do zařízení pomocí testu "Moje zařízení – účet". Pokud nelze generovat událost, ujistěte se, že Azure Mobile Engagement je správně integrovaný do vaší aplikace Android nebo iOS/Web/Windows/Windows Phone pomocí sady SDK.

Další informace najdete v tématu: [dokumentaci k sadě SDK][Link 5]

![Monitor2][15]  

## <a name="troubleshooting-with-monitor---crashes---details"></a>Řešení potíží s monitorováním - spadne – podrobnosti
O aplikaci z monitorování - havárií - podrobnosti sloužící k určení příčiny chyb aplikace můžete zkontrolovat informace o havárii. Vyhledání by také známé problémy s každou verzi sady SDK v poznámkách k verzi pro každou verzi sady SDK pro Android nebo iOS/Web/Windows/Windows Phone.

Další informace najdete v tématu: [dokumentaci k sadě SDK – poznámky k verzi][Link 5]

![Monitor3][16]

## <a name="monitor---alerts"></a>Monitorování – výstrahy
Můžete také zadat podmínek pro výstrahy, které se budou automaticky posílat vám prostřednictvím e-mailu nebo pomocí rychlé zprávy. (Všechny služby vyhovující protokolu XMPP, například Google Talk nebo Apple iChat nejsou podporovány.) Výstrahy jsou založené na předem definované detekce prahová hodnota větší než (>) nebo menší než (<) určitý počet relací, úlohy, události, chyby nebo dojde k chybě za sekundu, minutu nebo hodinu. Výstrahy můžete sledovat všechny aktivity daného typu nebo právě sledovat konkrétní úlohy, události nebo chyba činnost. 

Můžete také zadat minimální rychlost detekce, což je minimální množství minut, po které dojde k oddělení dvou oznámení stejného upozornění a ujistěte se, že při aktivaci upozornění nikdy nedostanete více než 1 oznámení na zadaný interval.

![Monitor4][17]

## <a name="see-also"></a>Další informace najdete v tématech
* [Koncepty][Link 6]
* [Řešení potíží s příručce služby][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
