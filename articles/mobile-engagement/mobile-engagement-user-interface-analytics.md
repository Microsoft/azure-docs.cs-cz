---
title: Azure Mobile Engagement uživatelské rozhraní - Analytics
description: Zjistěte, jak analyzovat historická data o vaší aplikaci pomocí Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6b2533ac-b8ec-4e35-872c-d563895bdc0c
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fceae1ffff40fc525170121181e21726fe2bd3f7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-analyze-historical-data-about-your-application"></a>Jak analyzovat historická data o vaší aplikace
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento článek popisuje **ANALYTICS** kartě **Mobile Engagement** portálu. Můžete použít **Mobile Engagement** portálu ke sledování a správě mobilních aplikací. Všimněte si, že pokud chcete začít používat portál musíte nejprve vytvořit **Azure Mobile Engagement** účtu.

Analýza část uživatelského rozhraní obsahuje souhrnné informace o aplikaci na základě historických dat, který se aktualizuje každých 24 hodin. Informace se zobrazí na jiné řídicí panely skládá z řádku, řádku/výsečové grafy, mřížky a mapy. Data můžete také stáhnout jako soubory .csv. Většina stejné informace je k dispozici v reálném čase v části monitorování uživatelského rozhraní a je také přístupný z rozhraní API Analytics.

> [!NOTE]
> Mnoho části **Mobile Engagement** portál obsahovat uživatelského rozhraní **zobrazit NÁPOVĚDU k** tlačítko. Chcete-li získat další kontextové informace o oddílu na toto tlačítko.

## <a name="standard-and-custom-analytics"></a>Standardní a vlastní Analytics
Azure Mobile Engagement poskytuje sadu basic, standard analytické informace o aplikacích, které může být proto vytvořen při integraci aplikace s SDK. Azure Mobile Engagement také poskytuje schopnost shromažďovat informace o další vlastní analýzy, které chcete o chování vlastní koncoví uživatelé. To provedete tak, že vytvoříte plán značek vlastní "značky (app-info)", vytvořené z **nastavení** tak, aby Azure Mobile Engagement může shromažďovat tato další data za vás.

## <a name="analytics"></a>Analýzy
* Řídicí panel: Zobrazuje obecné informace o nových a aktivující uživatelům a jejich vývoj.
* Uživatelé: Uživatelé se identifikují podle identifikátoru zařízení: Tento identifikátor je jedinečný pro každé zařízení (jeden nový uživatel je ve skutečnosti jedno nové zařízení). Uživatel se považuje za nové v daném časovém intervalu Pokud uskutečnil během tohoto intervalu uskutečnil první relaci. Uživatel se považuje za udrženého, pokud během posledních 7 dnů uskutečnil alespoň jednu relaci. Aktivní uživatelé jsou uživatelé, kteří provedené alespoň jednu relaci během daného období. Můžete řadit, měsíčně, týdně, denní nebo každou hodinu časových období. Všechny grafy vypadat podobně jako ale umožňují filtrovat podle různých funkcí, jako je například verzi vaší aplikace a potom seřadit podle v časovém intervalu. Standardní informace o shromažďovaných funkcí integraci sady SDK zahrnuje následující: aktivních uživatelů, nového uživatele, počet relací, délka každé relaci, technické informace o zemi, místní hodnoty, umístění, poskytovatel jazyk, zařízení, firmwaru, sítě (Wi-Fi) , verze aplikace a sady SDK, používaných zákazníky. Tuto informaci lze zobrazit v reálném čase z části monitorování.

> [!NOTE]
> Časové období je založena na datum z nastavení zařízení uživatelů, takže může uživatele, jehož telefon má datum nesprávné sady zobrazí v nesprávný časové období.

* Uchování: Uživatel se považuje za udrženého, pokud během tohoto intervalu uskutečnil první relaci uskutečnil v daném časovém intervalu. Časové intervaly, za které udržených uživatelů (a noví) uživatelé počítají můžete změnit na hodiny, dny, týdny nebo měsíce. Analýza uchování uživatele je vytvořená na základě kohorty. Kohorty je sada všichni noví uživatelé zjistil za dané období (tj, sadu uživatelů provedením jejich první relaci během této doby). Používáme kohorty 1 den, den 2, 4 dní, 7 dní nebo 1 měsíc. Vzhledem kohorty, každý 1 den, 2 dní, 4 dní, 7 dní, nebo 1 měsíc, Azure Mobile Engagement vypočítá sadu všichni uživatelé, kteří patří do kohorty a jsou stále aktivní (tedy sadu uživatelů, kteří během období alespoň jednu relaci). Tuto sadu uživatelů, se nazývá kohorty verze. (Azure Mobile Engagement můžete zjistit, kolik uživatelů jsou stále používají vaši aplikaci, ale pouze konkrétní úložiště platformy poznáte, kolik uživatelů odinstalovat aplikace – například GooglePlay iTunes, Windows Store, atd.).
* Relace: Jedno použití aplikace uživatelem. Relace se generují z pořadí aktivity prováděné uživateli (aktivita je obvykle přidružena k použití jednu obrazovku aplikace, ale to se může lišit v závislosti na způsobu, jakým sady SDK integrovaná do aplikace). Uživatel může provádět pouze jednu aktivitu najednou: relace začíná, jakmile uživatel zahájí první aktivitu a zastaví, jakmile skončí poslední aktivita. Je-li uživatel více než několik sekund bez provádění všech činností, jeho pořadí aktivit rozdělit na dvě odlišné relace.
* Aktivity: Názvy jednotlivých obrazovek v aplikaci a délka uživatelé vynaložit na každý obrazovky. Aktivity jsou vlastní analytické možnost, která odpovídá "informace o aplikaci" značek, které jste nastavili pro vlastní aplikace:
* Cesta k uživatele: Ukazuje, jak uživatelé přecházejí mezi aktivitami (obrazovkami) aplikace. Přesunutím posuvníku upravíte úroveň podrobností. Modré uzly představují aktivity aplikace. Jejich velikost je úměrná době uživatelé stráví ve frontě. Bílé uzly představují zahájení a ukončení relace. Červené uzly představují havárie. Spojnice představují přechody mezi aktivitami aplikace (nebo mezi aktivitami a haváriemi). Klikněte na uzel nebo spojnici zobrazíte popisek s dalšími informacemi o vašich dat: časem stráveným na konkrétní obrazovce, počtem přechodů a procentem přechodů ze zdrojové aktivity k cílové aktivitě. (---60 %---> B znamená, že uživatelé z aktivity A přejde k aktivitě B 60 % času.) Graf můžete přeuspořádat tak, jak chcete upřesnit. jeho pozice se uloží pokaždé, když provedete změny. Zobrazením nebo skrytím havárií můžete graf zjednodušit.
* Události: Konkrétní akce provedené uživatelem v aplikaci. Rozdělení událostí, které se zobrazí jako počet událostí na uživatele na relaci. Událost představuje okamžitou akci, například kliknutí na tlačítko nebo příjem oznámení. (Význam událostí závisí na tom, jak má sady SDK integrovaná do aplikace.) Událost může dojít během relace nebo úlohy, případně může být samostatná.
* Úlohy: Podobné události, s výjimkou se zaměřuje na délce akce. Například úlohy může zjistit technické informace o tom, jak dlouho bude trvat obsahu zatížení nebo volání webové služby. Může také ukazují, jak dlouho trvalo uživatele k vyplnění formuláře, vytvoření účtu nebo nákupu. Úlohu představuje dobu trvání nějakého úkolu, například dobu stahování nebo dobu banner se zobrazuje na obrazovce. (Význam úloh závisí na tom, jak má sady SDK integrovaná do aplikace.) Úlohy jsou obvykle spojovány s úkoly na pozadí, které se provádějí mimo rozsah relace (tedy bez zásahu uživatele).
* Technicals: Technické informace o zařízení uživatelů vaší aplikace, můžete sledovat, například národního prostředí, operátora, sítě, zařízení, firmwaru a obrazovky velikost zařízení uživatelů a verzi aplikace a verze sady SDK používaná ve vaší aplikaci.
* Chyby: Informace o technických chyby uvnitř aplikace, které nezpůsobí aplikace došlo k chybě. Chybu představuje náhlý problém, například chybu sítě nebo chybnou manipulaci. (Význam událostí závisí na tom, jak má sady SDK integrovaná do aplikace.) K chybě může dojít během relace nebo úlohy, případně může být samostatná.
* Dojde k chybě: Informace o chybách, ke kterým způsobit, že aplikace došlo k chybě. Havárie je vznikl nečekaně stav, kdy aplikace přestane vykonávat své předpokládané funkce a musí být zastavena. Havárie je většinou způsobena chybou v aplikaci.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Přístup k uchování přehled
![Analytics3][12]

Přehled uchování je rozdělena uprostřed do několik karet, každý zobrazující přehledu po určitou dobu uchování. Doba uchování 2 dny je vidět v příkladu. Ostatní karty zobrazit období uchovávání 4 dny a 7 dní.

## <a name="understanding-the-retention-overview-cards"></a>Principy kartách uchování – přehled
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Každou kartu se skládá ze 3 hlavní části:
1. 1: kohorty a období považovány za
2. 2 – 4: uchování pro aktuální období
3. 5: minigraf historie

### <a name="here-is-detailed-information-about-each-element"></a>Tady je podrobné informace o jednotlivých prvků:
1. Kohorty a období: typ kohorty poskytuje toto titulku. Zde "2 dny období" znamená, že se podíváme na chování uživatelů více než 2 dny, uživatelé, které byly přijaty během období 2 dny a jestli se připojení v následující bloky dvou dnů. V předchozím příkladu zvažuje aktivity uživatelů mezi 21 a 22nd listopadu.
2. To poskytuje míru uchování nad 21 a 22 listopadu pro uživatele, které dorazily 19 a 20. listopadu. Zde jsme měli 1 aktivního uživatele mezi 21 a 22nd, přes 3, které byly nové uživatele mezi 19th a 20.
3. Visual ukazatel poskytuje stejné informace, jak je uvedeno výše graficky znázornit. (Třetí kruhu je z číslo 33 %). Barva jsou uvedeny další informace: zelená značí toto číslo se ročně zvýší z předchozí výpočtu. Žlutý znamená stabilní a red znamená snížení.
4. Označuje hodnoty používané pro výpočet.
5. Toto je minigraf historii hodnot uchovávání informací. Umožňuje zobrazit hodnoty v minulosti tak, aby měl jak vyvinuly široký zobrazení.

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
