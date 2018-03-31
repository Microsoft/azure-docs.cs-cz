---
title: Azure Mobile Engagement uživatelské rozhraní – segmenty
description: Postup vytvoření a správa segmentů uživatelů pro identifikaci vzorů využití pomocí Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 53c5b1b672a0d7212a3d298c3e411a9cc6ac0807
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Postup vytvoření a správa segmentů uživatelů pro identifikaci vzorů využití
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento článek popisuje **SEGMENTY** kartě **Mobile Engagement** portálu. Můžete použít **Mobile Engagement** portálu ke sledování a správě mobilních aplikací.

Části segmenty rozhraní umožňuje pracovat na segmentace uživatelů na základě různých chování a analýz, které můžete získat z aplikace a můžete také přistupovat prostřednictvím rozhraní API segmenty. Segmenty se vypočítávají nejprve 24 hodin po jejich vytvoření a jejich jsou přepočítávány každých 24 hodin na základě nejnovější analytics informací. Jakmile se počítá segment, zobrazuje graf "Dne do historie den" každý den.

> [!NOTE]
> Mnoho části **Mobile Engagement** portál obsahovat uživatelského rozhraní **zobrazit NÁPOVĚDU k** tlačítko. Chcete-li získat další kontextové informace o oddílu na toto tlačítko.
> 
> 

## <a name="create-segments"></a>Vytvoření segmentů
Můžete vytvořit na základě až 10 kritérií na určitou dobu až na 60 dnů v minulosti z části analytics segment. Můžete například vytvořit segment podle osob, které se mají zobrazit určité stránky nebo vyhledávat konkrétní obsahu v aplikaci během posledních 10 dnů. Tyto informace jsou k dispozici v části analytics. Ano můžete ho vytvořit segment, a pak nastavte nabízených oznámení pro tuto podmnožinu uživatelů potřebujete, aby se vraťte k aplikaci. 

> [!NOTE]
> Jakmile je vypočítána segment, nemůže být upraven; může být pouze klonovat (zkopírovaný) nebo zničení (odstraněno). Segment můžete klonovat stejné aplikace (se stejným ID aplikace), a můžete ho také klonovat do jiných aplikací (s jinou AppID). 

 ![segments1][35] 

## <a name="examples-segments"></a>Příklady segmenty
 ![segments2][36]

Segmenty umožní segmentovat koncovým uživatelům vaší aplikace.
Segmentace vaši uživatelé je důležité marketingové strategie. Azure Mobile Engagement vám umožňuje získat historických dat a vytvořte vlastní segmenty. Tento výkonný nástroj umožňuje další informace o zákazníkům ve vaší aplikaci. Můžete snadno analyzovat segmenty a použít jako cíle nabízené segmenty.
Běžné případ použití je chcete odesílat oznámení oznámení na podporu koncovým uživatelům, aby hodnocení aplikace v úložišti. Místo odesílání oznámení do všichni vaši koncoví uživatelé, můžete vytvořit segment, který byste zadat jenom uživatelé, kteří použili aplikaci každý den pro poslední měsíc a neměla skvělé uživatelské prostředí. Jakmile odešlete méně, vysoce cílové nabízená oznámení, získáte lepší návratnost investic.

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Segmenty, které můžete vytvořit na základě hlavní Azure Mobile Engagement prvků:
* Události: vytvoření segment tohoto cíle jeden konkrétní události aplikace, která došlo k více než dvakrát týdně. 
* Relace: vytvoření segment uživatelů, kteří použili aplikaci více než 5 výskyty minulého týdne.
* Aktivita: vytvořte segment uživatelů, kteří použili jednu stránku nebo obsah vyšší nebo nižší než 10 čas poslední měsíc.
* Úlohu: vytvořte segment uživatelů, kteří dokončili úlohy maximálně dvakrát denně.
* Havárií: vytvořte segment všechny uživatele, kteří předtím havárie více než 10 výskyty minulého týdne. (Tento segment omluva nebo i kupónů může push!)
* Chyba: vytvořte segment všechny uživatele, kteří mají došlo k chybě víc než 100krát poslední 3 dny.
* Informace o aplikaci: vytvořte segment, který cílí vlastní informace o aplikaci, kterým došlo během posledních 25 dnů.
  
  ![segments4][38]

Pokud chcete použít Segment s optimálně, musíte provést vlastní integrace sady SDK v aplikaci s označování plánu značek "Informace o aplikaci".
Potom přejděte na domovskou stránku rozhraní, vyberte aplikaci a klikněte na v části "Segmenty".

1. Vyberte v části "Segmenty".
2. Klikněte na "nový segment" tlačítko vytvořte nový segment.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Skutečné životnosti příklad: Vytvoření jednoduché segmentu na základě informací "Relace"
Vytvořte segment všichni koncoví uživatelé, kteří použili aplikaci alespoň 50 časy během posledního týdne. Z tohoto místa najít pouze koncovým uživatelům, kteří mají stráví nejméně 30 sekund ve vaší aplikaci na relaci. Zobrazí všichni koncoví uživatelé, kteří mají pozitivní zkušenost ve vaší aplikaci. Potom segment vytvořili může nabízená oznámení pro tyto koncové uživatele a požádat ho o hodnocení aplikace v úložišti.

 ![segments5][39]

1. Chcete-li rychle najít v seznamu "Segment" Zadejte název segment.
2. Klikněte na tlačítko "Vytvořit".
   
   ![segments6][40]

Vyberte relaci.

 ![segments7][41]

1. Vyberte dobu "Poslední týden".
2. Klikněte na Další.
   
   ![segments8][42]
3. Vyberte operátor, který je v seznamu relevantní: =; ≥, ≤.
4. Zadejte počet chcete.
5. Vyberte, chcete, aby výskyt. 
6. Klikněte na Další.
   V tomto příkladu je nastaven tak přes poslední týden, odpovídající uživatelé, kteří provedli alespoň 50 relací.
   
   ![segments9][43]

V případě segmentace relace můžete délka relace jako kritérium.

1. Vyberte operátor, ze seznamu.
2. Zadejte délku na relaci.
3. Klikněte na Další.
   V tomto příkladu se říká, že přes všechny relace, byla segmentované v oddílu výskyt, vyberte pouze uživatelé, kteří mají stráví déle než 30 sekund na relaci.
   
   ![segments10][44]

Chcete-li načíst trychtýře. úplný název kritériem a klikněte na tlačítko Dokončit.

 ![segments11][45]

Po dokončení nastavení kritériem se zobrazí v segmentu trychtýřového grafu.
Protože segment je na základě analýzy dat, se vypočítávají segmenty jednou za den.
V tomto příkladu 47,7 % celkový koncoví uživatelé shodná s kritériem. Měly by být uživatele, kteří mají dobrou prostředí a bude může zajistit vyšší hodnocení, pokud je nabízená oznámení, které je vyzývají k hodnocení aplikace v úložišti.

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

