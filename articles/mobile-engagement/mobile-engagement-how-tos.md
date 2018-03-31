---
title: Azure Mobile Engagement uživatelské rozhraní - Reach postup
description: Přehled uživatelského rozhraní pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0c536d378023ee4a9675bb2d67262fb485c8052b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Jak začít pracovat, používáním a správou nabízených oznámení k oslovení koncových uživatelů
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Jakmile sada SDK je plně integrována do vaší aplikace, můžete začít používat pomocí části Reach uživatelského rozhraní pro nabízená oznámení uživatelům vaší aplikace.  

## <a name="do-your-first-push-notification-campaign"></a>Proveďte své první kampaně nabízených oznámení
* Potvrďte, že vaše kampaně Reach je integrována do vaší aplikace pomocí sady SDK. 
* Vyberte svou aplikaci

![First1][1]

* Přejděte do části "Reach" a klikněte na tlačítko "nové oznámení"

![First2][2]

* Vytvořte novou kampaň a pojmenujte ji
  
![First3][3]

* Vyberte, jak bude doručena oznámení, jako v aplikaci jenom

![First4][4]

* Vytvořte zprávu, kterou chcete push

![First5][5]

* Název může zapisovat na oznámení (volitelné).
* Zápis předávaný obsah zprávy.
* Můžete nahrát bitovou kopii. Upozorňujeme, že velikost souboru nesmí překročit 32 768 bajtů.
* Máte také možnost vybrat další možnosti, ale pro výběr v tomto kurzu, zjistíme, který později.
* Vyberte typ obsahu pouze jako oznámení

![First6][6]

* Vytvořte kampaň nabízených a se zobrazí v seznamu kampaně.

![First7][7]

## <a name="test-your-push-notification-campaign"></a>Testování kampaň nabízených oznámení
![Test1][8]

* Zaregistrujte zařízení.
* Klikněte na zaškrtávací políčko zařízení, které chcete push.
* Klikněte na tlačítko "Test" k odeslání nabízeného oznámení do zařízení.

![Test2][9]

* Aktivovat kampaň

![Test3][10]

* Teď, když jste vytvořili kampaň stačí pro oznámení poslat uživatelům aktivovat.

## <a name="send-personalized-pushes"></a>Odeslat přizpůsobené nabízených oznámení
* Tento příklad vytvoří push, kde je zadán kód vlastní slevu do nabízených oznámení.

![Personalize1][11]

Přizpůsobení funguje tak, že nahradíte značku podle z značku informace o aplikaci tak, budete muset zkontrolujte, zda že má uživatel správnou app-info definován jako první. V tomto příkladu bude mít cílových uživatelů značku informace o aplikaci s názvem rebate_code definované.
Jak je zobrazen výše nabízených oznámení obsah obsahuje značky ${rebate_code}, což bude znamenat, že ho nahradit za skutečný obsah značky informace o aplikaci.

> [!WARNING]
> Pokud informace o značce aplikace není definován pro uživatele, uživatel neobdrží nabízeného oznámení.

* Výsledek

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Můžete upravit text oznámení
![Personalize3][13]

* Název oznámení, včetně
* A obsah zprávy.
* Zvolte typ sdělení (textového zobrazení nebo zobrazení webové stránky)

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Text oznámení může také přizpůsobit, pokud:
* Adresa URL akce, budete chtít cílovou stránku můžete přizpůsobit
* Název,
* Tělo zprávy.

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Rozlišení vašeho Push Notification (uvnitř nebo mimo aplikaci)
* Vyberte typ oznámení push, vyberte aplikaci, přejděte k části "Připojit", vyberte nebo vytvoříte kampaně nabízených a přejděte k části "Oznámení".
* Klikněte na "doručení režim" Chcete.
* Klikněte na zaškrtávací políčko "Omezit aktivity", pokud chcete oznámení proběhne konkrétní aktivitami (obrazovkami).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>"Pouze mimo aplikaci" režimu doručení
![Differentiate2][16]

"Pouze mimo aplikaci" způsob dodání poskytuje nabízené oznámení při ukončení aplikace. Toto je standardní nabízených oznámení.
Když vyberete "pouze mimo aplikaci", musí již zadanými certifikáty z platformy, na které aplikace je vychází (APNS nebo GCM).

### <a name="see-also"></a>Další informace najdete v tématech
* [Certifikáty Apple Push Notification Service –](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging – certifikát](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"v aplikaci pouze" způsob dodání.
![Differentiate3][17]

"V aplikaci jenom" doručení režim poskytuje nabízených oznámení, když aplikace běží.
Pro toto oznámení není potřeba projít APNS a GCM systému.
V aplikaci systému doručení může použít k dosažení koncovým uživatelům.
Můžete plně přizpůsobit oznámení a rozhodnout, ve kterém se zobrazí aktivity (obrazovky) oznámení.

### <a name="anytime-delivery-mode"></a>"Kdykoliv" doručení režimu
Můžete zvolit způsob dodání "Kdykoliv", zajišťuje vám k dosažení koncovému uživateli, jestli aplikace běží, nebo ne.
Když vyberete "Kdykoliv", musí již zadanými certifikáty z platformy, na které vytváří vaše aplikace (APNS nebo GCM). 

## <a name="schedule-a-push-campaign"></a>Plán kampaně nabízených
### <a name="plan-to-start-a-campaign"></a>Chcete spustit na kampaň
![Shedule1][18]

Je 21 z března a máte hlášení aby a plánované pro 22 z března půlnoci. Nemusíte zůstat před rozhraní udělat push! Můžete naplánovat předem přesný minutu, které se budou odesílat oznámení.

* Zrušte zaškrtnutí "Žádný" zaškrtávací políčko a vyberte čas spuštění 
* Vyberte datum a čas, který chcete spustit kampaně nabízených.

### <a name="plan-to-end-a-campaign"></a>Plán pro ukončení kampaně
![Shedule2][19]

Chcete kampaň zastavit na 25 z března ve 3. hodině ale víte, že nebudete existuje provést.
Nemusíte zůstat před rozhraní tak, aby nabízel! Můžete naplánovat předem přesný minutu, kterou vaše kampaň se zastaví.

* Klikněte na "Žádný" zaškrtávací políčko nebo vyberte čas ukončení
* Vyberte datum a čas chcete kampaň nabízených dokončit.

### <a name="end-a-campaign-manually"></a>Ručně ukončete kampaň
![Shedule3][20]

Ve výchozím nastavení, "Žádný" jsou vybrané zaškrtávací políčka.
To znamená, že tato kampaň spustí, jakmile ji aktivovat v části reach a skončí, když se zastaví v oddílu reach.

> [!NOTE]
> Kampaně vytvořen bez koncové datum místně uložených nabízeného oznámení na zařízení a zobrazit ji při příštím otevření aplikace i v případě, že se tato kampaň ručně ukončí.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Vylepšení nabízená oznámení pomocí textového zobrazení
### <a name="what-is-a-text-view"></a>Co je textového zobrazení?
![TextView1][21]

Zobrazení textu je automaticky otevírané okno s textového obsahu. Toto automaticky otevírané okno se zobrazí, jakmile uživatel klikne na nabízené oznámení.
Zobrazení textu umožňuje prezentovat další obsah koncovému uživateli. Toto je také možnost předložit volání akce, jako přechod na stránku aplikace, přesměrování k úložišti, otevření webové stránky, odesílání e-mailu, spouštění hledání zjištěnou atd...

### <a name="example-text-view"></a>Příklad: Zobrazení textu
* Vytvořte kampaň nabízených oznámení v části "Reach" a zadejte název kampaně

![TextView2][22]

* Zapište zprávu, která se zobrazí na oznámení.
* Vyberte typ obsahu oznámení "text"

![TextView3][23]

> [!NOTE]
> Když stisknete textového zobrazení, je vždy se dodává s oznámení nejdřív. 

* Zadejte text (po výběru textového obsahu oznámení, dílčí části se zobrazí, umožní vám zadat text, který chcete zobrazit.)

![TextView4][24]

* Napište název, který se zobrazí v horní části zprávy.
* Zadejte hlavní obsah textu zobrazení.
* Zapište obsah, který se zobrazí na tlačítko akce (tlačítko akce umožňuje aplikaci, aby se určité akci například otevřete stránku aplikace, přesměrovat na obchod s aplikacemi nebo jakýkoli jiný typu zdroje, které můžete zadat).
* Zápis obsahu, který se zobrazí na tlačítko Konec (kliknutím na tlačítko konec textového zobrazení zmizí.)
* Vytvořte kampaň nabízených oznámení a zobrazí se v seznamu kampaně.

![TextView5][25]

* Aktivujte kampaň nabízených oznámení k odesílání textového zobrazení pro vaše uživatele.

![TextView6][26]

* Výsledek

![TextView7][27]

* Uživatel obdrží oznámení a klepněte na něj.
* Textového zobrazení se zobrazí automaticky otevírané okno, které uživateli umožňují pracovat s ním.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Vylepšení nabízených oznámení s webové zobrazení
### <a name="what-is-a-web-view"></a>Co je webové zobrazení?
![WebView1][28]

Webové zobrazení je automaticky otevírané okno s obsahem serveru. Toto automaticky otevírané okno se zobrazí, když uživatel klikne na nabízené oznámení.
Webové zobrazení umožňuje dosáhnout větší interakce s koncovým uživatelem.
Toto je také možnost k dispozici volání akce, jako je přesměrování na App Store, otevření webové stránky, odesílání e-mailu, spuštění vyhledávání zjištěnou, atd...

### <a name="example-web-view"></a>Příklad: Webové zobrazení
* Vytvořte kampaň nabízených v části "Reach" a zadejte název kampaně.

![WebView2][29]

* Zapište zprávu, která se zobrazí na oznámení.
* Vyberte typ obsahu, oznámení jako "web"

![WebView3][30]

### <a name="about-announcement-types"></a>O typech oznámení:
* Pouze oznámení: je jednoduchý standardní oznámení. Znamená to, že pokud uživatel klikne na něm, bez dalšího zobrazení se zobrazí, ale jenom akce, které jsou přidružené k ní dojde.
* Text oznámení: je oznámení, že zapojí uživateli Podíváme se na zobrazení textu.
* Sdělení webovém: je oznámení, že zapojí uživateli Podíváme se na webové zobrazení.
  Vyberte obsah "Web oznámení".

> [!NOTE]
> Po stisknutí webové zobrazení, je vždy se dodává s oznámení nejdřív.

* Definovat webového obsahu (po výběru webový obsah oznámení, zobrazí se část, umožňuje definovat webový obsah zobrazení, které chcete zobrazit.)

![WebView4][31]

* Zapsat název, který se zobrazí v horní části (volitelné) zprávy.
* Zápis váš HTML kód.
* Klikněte na zdroj úpravy tlačítka režimu Přepnout edition a v tématu jak to vypadá.
* Zapište obsah, který se zobrazí na tlačítko akce (tlačítko akce umožňuje aplikaci, aby se určité akci například otevřete stránku aplikace, přesměrování Store nebo jakýkoli druh zdroje, které můžete zadat).
* Zápis obsahu, který se zobrazí na tlačítko Konec (kliknutím na tlačítko Konec webového zobrazení zmizí).
* Výsledek

![WebView5][32]

* Příjem oznámení a klepněte na ni uživatele.
* Textového zobrazení se zobrazí automaticky otevírané okno, které uživateli umožňují pracovat s ním.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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

