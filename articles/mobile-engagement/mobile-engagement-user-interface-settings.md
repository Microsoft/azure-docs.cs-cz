---
title: Azure Mobile Engagement uživatelské rozhraní – nastavení
description: Zjistěte, jak spravovat globální nastavení aplikace pomocí Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 31df9f151febcb7e5f0f2cd4a83053b3aac416a3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>Jak spravovat globální nastavení aplikace
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

**Nastavení** možnosti nabídky, které jsou k dispozici pro aplikací měnit, v závislosti na platformě aplikace a oprávnění, budete mít udělená pro aplikaci. Nastavení zahrnuje následující: Podrobnosti, projekty, nativní oznámení, rychlosti Push, značkou Tag (app-info) a komerční naléhavost. Možnost nabídky značky (informace o aplikaci) v části nastavení lze spravovat vaše aplikace (pomocí SDK) nebo váš back-end (pomocí rozhraní API pro zařízení). 

> [!NOTE]
> Mnoho části **Mobile Engagement** portál obsahovat uživatelského rozhraní **zobrazit NÁPOVĚDU k** tlačítko. Chcete-li získat další kontextové informace o oddílu na toto tlačítko.
> 
> 

## <a name="details"></a>Podrobnosti
Umožňuje změnit název a popis aplikace, zobrazte vlastník aplikace, a oprávnění vaší role. 

Konfigurace analýzy umožňuje zobrazit nebo změnit týdny spustit na den a čas uchování ve dnech po.

  ![settings1][46]

## <a name="projects"></a>Projekty
Umožňuje vybrat všechny projekty, které má vaše aplikace se zobrazí v. 

Můžete taky vyhledat na projekt a zobrazit název, popis, vlastníka a oprávnění vaší role žádné projektu, který je součástí vaší aplikace.

Další informace najdete v tématu: [dokumentace uživatelského rozhraní – Domů][Link 13]

  ![settings3][48]

## <a name="native-push"></a>Nativní oznámení
Můžete zaregistrovat nový certifikát nebo odstraňte a existujícího certifikátu pro použití nativního nabízení. Nativního nabízení umožňuje Azure Mobile Engagement k aplikaci kdykoli, i když není spuštěna. 

Po zadání přihlašovacích údajů nebo certifikáty pro aspoň jednu službu s nativním nabízením, můžete vybrat "Kdykoliv" při vytváření kampaně Reach a použijte parametr "oznamovatelem" v rozhraní API PUSH.

### <a name="apple-push-notification-service-apns"></a>Služby nabízených oznámení Apple (APNS)
Pokud chcete umožnit nativní oznámení pomocí služby Apple Push Notification Service musíte zaregistrovat svůj certifikát. Musíte se k určení typu certifikátu jako vývoj (vývoj) nebo produkční (PRODUKČNÍMU). Pak bude nutné nahrajete certifikát a heslo.

Další informace najdete v tématu: [dokumentaci k sadě SDK - iOS – postup přípravy aplikace pro Apple nabízená oznámení][Link 5]

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>Služba nabízených oznámení Windows (WPNS)
Pokud chcete umožnit nativní oznámení pomocí Služby nabízených oznámení Windows, musíte zadat přihlašovací údaje pro vaši aplikaci. Budete potřebovat vaše identifikátor zabezpečení (SID) balíčku a tajný klíč.

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud Messaging pro Android (GCM)
Pokud chcete umožnit nativní oznámení pomocí služby GCM, postupujte podle pokynů z Google. Pak musíte vložit jednoduchý klíče rozhraní API serveru nakonfigurovaný bez omezení IP adres. Vyžaduje integraci se sadou SDK pro Android v1.12.0 +.

Další informace naleznete v tématu: 

* [Android SDK dokumentaci k integraci GCM][Link 5]
* [Příručka vývojáře Google GCM](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>Zasílání zpráv pro Android (ADM) zařízení Amazon
Chcete-li umožnit nativní oznámení pomocí ADM, je nutné zadat Amazon <OAuth credentials> skládající se z ID klienta a tajný klíč klienta (vyžaduje integraci se sadou SDK pro Android v2.1.0 +).

Další informace naleznete v tématu: 

* [Android SDK dokumentaci k integraci ADM][Link 5]
* [ADM dokumentaci pro vývojáře Amazon](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>Rychlost nabízení
Zobrazí aktuální rychlost nabízení svojí aplikace a umožňuje definovat rychlost nabízení svojí aplikace.

  ![settings7][52]

## <a name="tag-app-info"></a>Na tag (app-info)
![settings11][56]

## <a name="commercial-pressure"></a>Komerční naléhavost
![settings12][57]

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

