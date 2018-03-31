---
title: Azure Mobile Engagement uživatelské rozhraní – kampaně Reach
description: Laern jak vytvářet a spravovat nabízených oznámení kampaně pomocí Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9d457a8d5f68f70f6bf7a5f086b5e1d65bd1ea95
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Jak vytvořit a spravovat kampaní nabízených oznámení
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

V části Reach UI slouží k vytvoření nové kampaně nabízených s komplexní vzorec tím, že poskytuje všechny informace, které potřebujete k odesílání nabízených oznámení. Možnosti nabízené kampaně mírně lišit v závislosti na typech čtyři kampaň: oznámení, hlasování, datová oznámení a dlaždice (pouze Windows Phone).

### <a name="option-applies-to"></a>Možnost se vztahuje na:
* Jazyky: Všechny (oznámení, hlasování, datová oznámení, dlaždice)
* Kampaň: Všechny (oznámení, hlasování, datová oznámení, dlaždice)
* Upozornění: Oznámení, hlasování
* Obsah: Jedinečný pro každý typ kampaně
* Cílové skupiny: Všechny (oznámení, hlasování, datová oznámení, dlaždice)
* Časový rámec: oznámení, hlasování, dlaždice
* Test: Všechny (oznámení, hlasování, datová oznámení, dlaždice)

![Reach-Campaign1][20]

## <a name="languages"></a>Jazyky
Rozevírací nabídky jazyků můžete použít k odeslání jinou verzi vaší nabízené do zařízení, které jsou nastavené na používají různé jazyky. Ve výchozím nastavení všechna zařízení se zobrazí stejné nabízeného oznámení bez ohledu na to, v jakém jazyce jsou nastaveny na používání. Uživatelé s jejich zařízení nastaven jiný jazyk, se zobrazí výchozí jazykovou verzi nabízeného oznámení. Mnohé z možností kampaň nabízených umožňují zadat alternativní obsah pro každý další jazyky, které vyberete. 

![Reach-Campaign2][21]

### <a name="language-differences-apply-to"></a>Jazyk rozdíly platí pro:
* Jazyky: Jedinečný jazyky může být vybraný kromě výchozí jazyk
* Kampaň: Stejný pro všechny jazyky
* Oznámení: Jedinečný pro každý jazyk kromě výchozí jazyk
* Obsah: Jedinečný pro každý jazyk kromě výchozí jazyk
* Cílová skupina: Lze filtrovat podle kritérium samostatné jazyk
* Časový rámec: stejný pro všechny jazyky
* Test: Mohou být odeslány na každý jazyk v čase

### <a name="supported-languages"></a>Podporované jazyky:
* Arabština (ar) 
* Bulharština (bg) 
* Katalánštinu (ca) 
* Čínština (zh) 
* Chorvatština (hr) 
* Czech (cs) 
* Dánština (da) 
* Holandština (nl) 
* Angličtina (en) 
* Finština (fi) 
* Francouzština (fr) 
* Němčina (de) 
* Řečtina (el) 
* Hebrejština (mu) 
* Hindská (dobrý den) 
* Maďarština (hu) 
* Indonéština (id) 
* Italština (it) 
* Japonština (ja) 
* Korejština (ko) 
* Lotyština (lv) 
* Litevština (lt) 
* Malajština (macrolanguage) (ms) 
* Norwegian Bokmål (nb) 
* Polština (pl) 
* Portugalština (pt) 
* Rumunština (ro) 
* Ruština (ru) 
* Srbština (sr) 
* Slovenština (sk) 
* Slovinština (sl) 
* Španělština (es) 
* Švédština (sv) 
* Tagalogština (tl) 
* Thajštině (tou) 
* Turečtina (tr) 
* Ukrajinská (Velká Británie) 
* Vietnamštině (vi) 

## <a name="campaign"></a>Kampaň
V části kampaň můžete použít k nastavení názvu a kategorie kampaně také jako, pokud budete chtít ignorovat cílovou skupinu části nabízené kampaně a místo toho odeslat tuto kampaň prostřednictvím rozhraní Reach API (a některé prvky s nízkou úrovní Push rozhraní API). Kategorie lze použít s vlastní oznámení šablonu pro oznámení ovládacího prvku v aplikaci na základě předdefinovaných nastavení. Můžete získat seznam existujících "kategorií" prostřednictvím rozhraní Reach API.

> [!WARNING]
> Pokud použijete možnost "Ignorovat cílovou skupinu, nabízení se se uživatelům odešle přes rozhraní API" v části "Kampaň" kampaně Reach, kampaň se neodesílal automaticky, musíte ručně odesílání prostřednictvím rozhraní Reach API.

![Reach-Campaign3][22]

### <a name="option-applies-to"></a>Možnost se vztahuje na:
* Název: všechny
* Kategorie: Oznámení, hlasování
* Ignorovat cílovou skupinu, nabízení se odešle uživatelům prostřednictvím rozhraní API: všechny

## <a name="notification"></a>Oznámení
Část oznámení můžete nastavit základní nastavení pro vaše včetně nabízené: název nabízeného oznámení, zprávu, bitovou kopii v aplikaci, nebo pokud je zprávy. Mnoho nastavení oznámení jsou specifické pro platformu zařízení. Můžete určit, zda vaše nabízení se odešle "aplikace" nebo "mimo aplikaci" nebo obojí. (Nezapomeňte, že uživatelé můžou "výslovný souhlas" nebo "výslovný nesouhlas s" aplikace"z" nabízených oznámení na operační systém úrovni na svých zařízeních a Azure Mobile Engagement nebudete moci toto nastavení přepsat. Nezapomeňte taky, že rozhraní Reach API zpracovává "v aplikaci" a "mimo aplikaci" nabízených oznámení. Rozhraní API nabízené slouží ke zpracování nabízených oznámení "mimo aplikaci" příliš.) Oznámení lze přizpůsobit s obrázky a obsah HTML, včetně přímých odkazů pro propojování mimo aplikaci nebo na jiné místo v aplikaci (Android SDK 2.1.0 nebo novější záměrné kategorií vyžaduje). Můžete změnit oznámení "BADGE" ikony nebo iOS a odeslání text nebo webového obsahu (místní okno s html, adresa URL odkaz na obsah do jiného umístění uvnitř nebo vně aplikace). Můžete také prozvonit zařízení se systémem Android nebo zavibrovat s nabízeného oznámení. (Nezapomeňte, že budete potřebovat správné oprávnění sady SDK v systémem Android manifest souboru prstence nebo zavibrovat zařízením.) Není aktuálně žádné oborový standard pro Android "velký obrázek" velikostí, protože velikost obrazovky se liší na každé zařízení, ale 400 × 100 obrázky fungovat na téměř jakoukoli velikosti obrazovky.

### <a name="delivery-types"></a>Typy doručení:
* Jen mimo aplikaci: oznámení budou doručeny, pokud uživatel nepoužívá aplikaci.
* Mimo pouze oznámení aplikace vyžaduje certifikát od společnosti Apple nebo Google (certifikát služby APN nebo GCM).
* V aplikaci jenom: oznámení se zobrazí, jenom když je aplikace spuštěna.
* Oznámení používá systém doručení Capptain spojit uživatele. Visual rozložení nebo zobrazení vaší nabízené můžete plně přizpůsobit.
* Kdykoli: Tuto možnost zajistí, že odesílat oznámení, která buď aplikace běží, nebo ne.

![Reach Campaign4][23]

### <a name="option-applies-to"></a>Možnost se vztahuje na:
* Upozornění: Oznámení, hlasování

## <a name="content"></a>Obsah
Části obsahu můžete upravovat obsah oznámení, hlasování, datová oznámení a dlaždice (pouze Windows Phone). Nastavení obsahu kampaní nabízených je specifické pro daný typ kampaně. 

### <a name="see-also"></a>Další informace najdete v tématech
* [Dokumentace k uživatelského rozhraní – dosáhnout – Push obsahu][Link 29]

![Reach-Campaign5][24]

## <a name="audience"></a>Cílová skupina
V části cílové skupiny můžete definovat standardní seznam položek, které mají omezit kampaně nebo omezení kampaň na základě přizpůsobené kritérií. Standardní sada možností pro omezení cílovou skupinu umožňuje odešlete do nové nebo staré uživatele nebo pouze uživatele nativního nabízení. Můžete také nastavit kvótu omezit počet uživatelů, kteří obdrží nabízeného oznámení. Výraz pro filtrování kampaň zahrnout jeden nebo více kritéria pro cílové uživatele můžete ručně upravit. Výraz cílové skupiny, můžete zadat ručně. Takové výraz musí explicitně definovat vztah mezi kritérii. Kritérium se popisuje identifikátor, který musí začínat velkým písmenem a nesmí obsahovat mezery. Vztah mezi kritéria lze popsat pomocí 'a', 'nebo', 'není operátory a také '(',')'. Příklad: "Criterion1 nebo (Criterion1 a není Criterion2)".

> [!NOTE]
> S velké cílovou skupinu součástí kampaně může být na straně serveru cílení na kontrolu pomalé, obzvláště pokud se pokusíte spustit několik kampaní ve stejnou dobu.

* Pokud je to možné spusťte pouze jeden kampaň najednou.
* Nejvýše spusťte pouze čtyři kampaně v čase.
* Push jenom na aktivní uživatele (zaškrtávací políčko "zaujmout jen uživatele, kteří se dají oslovit pomocí nativního nabízení" a "Zaujmout jen aktivní uživatele"), aby pouze vaši uživatelé, kteří stále mít nainstalovanou aplikaci a použít ho bude muset být kontrolována.
  Jakmile je definovány cílovou skupinu, můžete zjistit počet uživatelů, kteří obdrží tato nabízená tlačítko Simulovat. To bude vypočte se počet známých uživatelů potenciálně cílem touto cílovou skupinou (jde o odhad vycházející z náhodného vzorku uživatelů). Uvědomte si, že součástí této cílové skupiny jsou i uživatelé, kteří aplikaci odinstalovali, ale není dostupný.

### <a name="see-also"></a>Další informace najdete v tématech
* [Nové nabízené kritérium dokumentace - Reach - uživatelského rozhraní][Link 28]

![Reach-Campaign6][25]

### <a name="edit-expression"></a>Upravit výraz
![Reach Campaign7][26]

### <a name="limit-your-audience-option-applies-to"></a>Omezení, které vaše cílové skupiny možnost se vztahuje na:
* Zaujmout jen podmnožinu uživatelů: všechny (oznámení, hlasování, datová oznámení, dlaždice)
* Zaujmout jen staré uživatele: všechny (oznámení, hlasování, datová oznámení, dlaždice)
* Zaujmout jen nové uživatele: všechny (oznámení, hlasování, datová oznámení, dlaždice)
* Zaujmout jen neaktivní uživatele: oznámení, hlasování, dlaždice
* Zaujmout jen aktivní uživatele: všechny (oznámení, hlasování, datová oznámení, dlaždice)
* Zaujmout jen uživatele, kteří se dají oslovit pomocí nativního nabízení: oznámení, hlasování

## <a name="time-frame"></a>Časového rámce
Nastavení, když bude zasláno nabízeného oznámení nebo časový rámec můžete nechat prázdné pro okamžité spuštění kampaň můžete části časového rámce. Mějte na paměti, že pomocí koncovým uživatelům se časové pásmo může spusťte kampaň dříve, než očekávat pro koncové uživatele v Asii a odesílat malé dávky oznámení najednou, dokud všechny časových pásem na světě odpovídat časového rámce, nastavte pro kampaň za den. Pomocí koncoví uživatelé časové pásmo může také způsobit zpoždění v kampaně vzhledem k tomu, že má k vyžádání čas z telefonu před zahájením nabízeného oznámení.

> [!NOTE]
> Kampaně bez koncové datum může ukládat do mezipaměti nabízených oznámení místně a stále je zobrazit po můžete ručně dokončení kampaně. Aby se zabránilo toto chování konkrétní koncový čas pro kampaně.

### <a name="see-also"></a>Další informace najdete v tématech
* [Dosažení – jak Tos – plánování][Link 3] 

![Reach-Campaign8][27]

### <a name="settings-apply-to"></a>Nastavení se vztahují na:
* Časový rámec: oznámení, hlasování, dlaždice

## <a name="test"></a>Test
Testovací část vám pomůže tento nabízené poslat testovací zařízení před uložením kampaně. Pokud jste nakonfigurovali všechny vlastní jazyků pro tuto kampaň, můžete otestovat nabízeného oznámení v jednotlivé jazyky. Můžete nastavit testovací zařízení z "Můj účet".

> [!NOTE]
> Žádné na straně serveru, který data se protokolují, když použijete tlačítko pro "test" nabízených oznámení, data se protokolují pouze pro skutečné nabízené kampaně.

### <a name="see-also"></a>Další informace najdete v tématech
* [Dokumentace k uživatelského rozhraní – Můj účet][Link 14]

![Reach Campaign9][28]

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

