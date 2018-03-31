---
title: Azure Mobile Engagement uživatelské rozhraní - Reach
description: Zjistěte, jak k oslovení uživatelů vaší aplikace pomocí nabízených oznámení pomocí Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d999b83df7d9d467f08ce8ec72468c738e8acfa5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Postup oslovení uživatelů vaší aplikace pomocí nabízených oznámení
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento článek popisuje **dosáhnout** kartě **Mobile Engagement** portálu. Můžete použít **Mobile Engagement** portálu ke sledování a správě mobilních aplikací. Všimněte si, že pokud chcete začít používat portál musíte nejprve vytvořit **Azure Mobile Engagement** účtu. Další informace najdete v tématu [vytvoření účtu Azure Mobile Engagement](mobile-engagement-create.md).

Části Reach uživatelské rozhraní je nástroj správy kampaň nabízených kde můžete vytvořit nebo upravit nebo aktivovat nebo dokončit nebo monitorování a získat statistiky kampaní nabízených oznámení a funkce, které lze také přistupovat přes rozhraní Reach API (a některé prvky nízkou úroveň Push rozhraní API) . Mějte na paměti, jestli používáte rozhraní API nebo uživatelského rozhraní, bude potřeba integrovat Azure Mobile Engagement a Reach do své aplikace pro každou platformu sadou SDK, abyste mohli používat kampaně Reach.

> [!NOTE]
> Mnoho části **Mobile Engagement** portál obsahovat uživatelského rozhraní **zobrazit NÁPOVĚDU k** tlačítko. Chcete-li získat další kontextové informace o oddílu na toto tlačítko.
> 
> 

## <a name="four-types-of-push-notifications"></a>Čtyři typy nabízená oznámení
1. Oznámení – umožňují odesílat reklamní zprávy pro uživatele, kteří přesměruje je to jiné umístění uvnitř vaší aplikace a odešlete je webová stránka nebo úložiště mimo vaší aplikace. 
2. Hlasování – umožňují od koncových uživatelů sbírat informace ve formě odpovědí na otázky.
3. Datová oznámení - umožňují odesílat binární nebo base64 datový soubor. Informace obsažené v datovém oznámení se odešlou do vaší aplikace změnit aktuální zkušeností uživatelů ve vaší aplikaci. Aplikace musí umět zpracovat data v datovém oznámení.

## <a name="campaign-details"></a>Podrobnosti kampaně
Můžete upravit, klonování, odstranit nebo aktivovat kampaní, které nebyly ještě aktivována podržením ukazatele nad jejich názvy nebo můžete kliknout na k jejich otevření. Může klonovat kampaní, které již byla aktivována podržením ukazatele nad jejich názvy nebo můžete kliknout na k jejich otevření. Jakmile byl aktivován však nelze změnit na kampaň.

![Reach1][18]

## <a name="reach-feedback"></a>Dosažení zpětné vazby
Klikněte na **statistiky** a zobrazit podrobnosti kampaně Reach. **Jednoduché** zobrazení nabízí vizuální znázornění ve formě pruhový graf sloupec o co se stalo po kampaň aktivovala. **Upřesnit** zobrazení obsahuje podrobnější informace o nabízené kampaně. Tyto údaje nebudou dostupné, pokud odesíláte zkušební kampaně tj oznámení odeslat testovací zařízení. Zde je, jak by měl interpretovat tyto podrobnosti:

1. **Poslat** -určuje počet zpráv nabídnutých do zařízení. Tento počet bude záviset na cílovou skupinu, kterou jste zadali při vytváření nabízené kampaně. Pokud nezadáte žádné cílové skupiny, pak tato nabízená bude odesílat pro všechna registrovaná zařízení. Podobně jako všechny ostatní nabízených služeb, jsme není nabízených oznámení přímo do zařízení, ale místo toho je odešlete do příslušné platformy konkrétní služeb nabízených oznámení (PNS - APNS nebo GCM/WNS) tak, aby jejich doručování oznámení do zařízení. 
2. **Doručit** -určuje počet zpráv, které jsou úspěšně doručil systém oznámení platformy a zařízení a potvrzené jako přijaté Mobile Engagement SDK. 
   
   *Důvody pro doručené počet je menší než počet stisknutí:*
   
   1. Pokud má uživatel ze zařízení odinstaluje aplikaci, ale systém PNS neví o tom, v době, kdy chcete systém PNS odeslat nabízeného oznámení potom zpráva se zahodí.
   2. Pokud zařízení je aplikace, ale jsou v režimu offline se zařízeními pro dlouhou dobu, systém PNS se nepodaří doručit zprávu do zařízení. 
   3. Pokud zpráva získat doručit do zařízení, ale v aplikaci Mobile Engagement SDK obsah zprávy nerozpozná, pak zahodí zprávy. Toto může nastat, pokud přizpůsobení oznámení v aplikaci vygeneruje výjimku, která jsme catch SDK a drop zprávy. To může také nastat, pokud aplikace na zařízení používá verzi sady Mobile Engagement SDK, která není možné zjistit, novější verze nabízené zprávy odeslané ze platformy, ale toto je pouze v případě, že aplikace byl upgradován po oznámení byl odeslán z t Použí služby platformy. **Upřesnit** kartě zjistit, kolik zpráv byly vyřazeny. 
   4. Na zařízeních s iOS někdy není získat doručeny zprávy je buď zařízení na nízký stav baterie. nebo pokud aplikace využívá významné množství power při zpracování vzdáleného oznámení. Jedná se o omezení zařízení iOS.   
3. **Zobrazí** -určuje počet zpráv, které jsou úspěšně zobrazí uživateli aplikace na zařízení ve formě systémové oznámení nabízených nebo out z – aplikaci v centru oznámení, nebo oznámení v aplikaci v mobilní aplikaci.  **Upřesnit** na kartě se zjistit, kolik byly systémová oznámení a kolik byly oznámení v aplikaci. 
   
   *Důvody pro zobrazené počet je menší než počet doručené (čekání na který se má zobrazit.)*
   
   1. Pokud tato kampaň oznámení na něm měli koncové datum je možné, že byla doručena oznámení, ale při čas dodán otevřít a zobrazit ji pro uživatele aplikace, je již platnost vypršela, se nikdy zobrazovat.   
   2. Pokud oznámení je oznámení v aplikaci, pak oznámení se zobrazí, jenom když aplikace uživatel otevře aplikaci. V případech, kdy uživatel aplikace neotevřel aplikace sady SDK oznámí, že byla oznámení doručit ale ještě nebyla nezobrazí, dokud otevření aplikace. 
   3. Pokud oznámení je oznámení v aplikaci a nakonfigurovat zobrazený na konkrétní aktivity nebo obrazovce pak také oznámení se ohlásí jako doručen, ale ještě není doručit neprojeví, dokud uživatel otevře aplikaci na konkrétní obrazovce. 
4. **Interakce uživatele** -určuje počet zpráv, které má uživatel aplikace zpracoval s a bude obsahovat zprávy, které jsou zpracované nebo ukončené. 
   
   * *Uživatel aplikace může oznámení v některém z následujících způsobů:*
     
     1. Pokud se oznámení o oznámení systému nebo na více systémů z – aplikaci nebo oznámení v aplikaci, pak aplikace uživatel klikne na oznámení odeslaných jen oznámení.
     2. Pokud oznámení je oznámení v aplikaci s text nebo webového zobrazení nebo hlasování aplikace uživatel klikne na tlačítko akce v oznámení.
     3. Pokud oznámení je oznámení v aplikaci s webové zobrazení pak aplikace uživatel klikne na adresu URL webové pouze v zobrazení [Android]
   * *Uživatel aplikace můžete ukončit oznámení v některém z následujících způsobů:*
     
     1. Klepnutím na tlačítko Zavřít na oznámení přímo. 
     2. K načtení rychle nebo odstraněním oznámení. 
     3. Oznámení v aplikaci s text nebo webového obsahu a dotazuje se obvykle zobrazují uživateli aplikace ve dvou krocích. Nejprve zobrazí oznámení a po kliknutí na něm, zobrazí se jim následné obsahu text/web/dotazování. Uživatel aplikace můžete ukončit oznámení v některém z těchto kroků a podrobností v rozšířeném zobrazení zachytí to. 
5. **Reakcemi** -určuje počet zpráv, které byly explicitně reakcemi uživatelem aplikace. Toto je počet nejvíce zajímavé, jak to ukazuje, kolik uživatelů aplikace byly zajímají o zprávu, kterou instalováni v oznámení. 

> [!NOTE]
> Na iOS a Windows otevřete platformy, pokud má uživatel aplikace a tato kampaň byla kampaň "Kdykoliv" potom je možné, že oba mimo aplikaci a oznámení v aplikaci se zobrazují ve stejnou dobu. To může způsobit vyšší, než doručené zobrazené počtu. Pokud uživatel pracuje nebo akce oznámení a pak i počet uživatelské interakce/Actioned může být větší než doručené. 
> 
> 

![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

