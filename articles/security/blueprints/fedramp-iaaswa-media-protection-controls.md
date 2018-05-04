---
title: "Azure zabezpečení a ochrana média plán, podle kterého - FedRAMP webové aplikace automatizace – dodržování předpisů"
description: "FedRAMP webové aplikace automatizace - média ochrany"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 37812c2f7ee79685f9014a7999b4355e649ca6e1
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2018
---
# <a name="media-protection-mp"></a>Média ochrany (PP)

> [!NOTE]
> Tyto ovládací prvky jsou definovány NIST a USA Ministerstvo obchodu jako součást speciální publikace NIST 800-53 revize 4. Naleznete NIST 800-53 Rev. 4 informace o testování postupy a pokyny pro každý ovládací prvek.

## <a name="nist-800-53-control-mp-1"></a>Ovládací prvek NIST 800-53 MP-1

#### <a name="media-protection-policy-and-procedures"></a>Zásady ochrany média a postupy

**MP-1** organizace sama vyvinula, dokumenty a šíří do [přiřazení: organizace definované pracovníky nebo rolí] zásady ochrany média, které řeší účel, oboru, role, odpovědnosti, správu závazků, spolupráce mezi organizační entity a dodržování předpisů; a postupy pro usnadnění provádění zásady ochrany média a ovládací prvky ochrany přidružených médií; kontroluje a aktualizuje aktuální zásady ochrany média [přiřazení: organizace definované frekvence]; a postupy ochrany média [přiřazení: organizace definované frekvence].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může stačit média podnikové zásady ochrany a postupy zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-mp-2"></a>Ovládací prvek NIST 800-53 MP-2

#### <a name="media-access"></a>Média přístup

**MP-2** organizace omezuje přístup k [přiřazení: organizace definované typy médií digitální nebo jiných digitální] na [přiřazení: organizace definované pracovníky nebo rolí].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementovala média přístup prostřednictvím implementace zásady zabezpečení společnosti Microsoft. Prostřednictvím Active Directory objekty zásad skupiny (GPO v AD) a skupiny zabezpečení je řízena logického přístupu k digitálního média. Fyzický přístup k všechna média je omezený přístup procesem datového centra. Přístup je omezen na jednotlivce, kteří mají oprávněné obchodní účel přístupu k datům. Další informace o řízení přístupu datacenter zavedené naleznete PE-3, fyzické řízení přístupu. Standardní ochrany Asset definuje bezpečnostní opatření vyžaduje k ochraně utajení, integrita a dostupnost prostředků informace v rámci datová centra služby Microsoft Azure. |


 ## <a name="nist-800-53-control-mp-3a"></a>Ovládací prvek NIST 800-53 MP-3.a

#### <a name="media-marking"></a>Označení média

**MP 3.a** organizace označí média systému informace označující distribuční omezení, zpracování upozornění a informací o příslušné zabezpečení označení (pokud existuje).

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure označí prostředky v datových centrech společnosti Microsoft s HBI, MBI nebo LBI označení (vysoká, střední nebo Nízká obchodní dopad), který vyžaduje různé úrovně zabezpečení a zpracování opatření. Vlastníci Asset vyžadovaných ke klasifikaci jejich prostředky, které jsou uloženy v datacentru společnosti Microsoft. Další informace naleznete na Asset klasifikace Standard a Asset ochrany Standard. |


 ## <a name="nist-800-53-control-mp-3b"></a>Ovládací prvek NIST 800-53 MP-3.b

#### <a name="media-marking"></a>Označení média

**MP 3.b** stanoví organizace [přiřazení: organizace definované typy médií informace systému] z označení tak dlouho, dokud média zůstat v rámci [přiřazení: definované organizace řídí oblasti].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure vyžaduje asset vlastníky přiřadit jejich prostředky pomocí klasifikace asset a žádné prostředky se nevztahují tento požadavek. V prostředí datového centra společnosti Microsoft označují prostředky serverů, síťových zařízení a magnetické pásky. Jiného digitálního média, jako jsou USB flash nebo Flash disky, externí nebo vyměnitelné pevné disky, nebo se nepoužívají, disky CD/DVD. Bez digitální média se nepoužívá v datovém centru. |


 ## <a name="nist-800-53-control-mp-4a"></a>Ovládací prvek NIST 800-53 MP-4.a

#### <a name="media-storage"></a>Média úložiště

**MP 4.a** organizace fyzicky ovládací prvky a bezpečně uloží [přiřazení: organizace definované typy médií digitální nebo jiných digitální] v [přiřazení: definované organizace řídí oblasti].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure digitálního média jsou fyzicky a bezpečně uložené prostředky v rámci společného umístění místnosti datového centra. Datových centrech společnosti Microsoft mít několik vrstev fyzické prvky řízení přístupu (oznámení "BADGE" přístup, biometrika; PE 3 najdete další podrobnosti o fyzické prvky řízení přístupu) a sledování videa k poskytování zabezpečeného úložiště. Digitálního média pro zahrnuje serverů, síťových zařízení a magnetické pásky, které používají pro zálohování. Bez digitální média nepoužije v prostředí datového centra. |


 ## <a name="nist-800-53-control-mp-4b"></a>Ovládací prvek NIST 800-53 MP-4.b

#### <a name="media-storage"></a>Média úložiště

**MP 4.b** organizace chrání média systému informace, dokud jsou zničen média nebo upravený pomocí schválených zařízení technik a postupů.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure digitálního média prostředky jsou chráněné v datovém centru colocations Microsoft prostřednictvím fyzické prvky řízení přístupu (PE-3) a řízení přístupu logické (IA-2) po dobu jeho existence prostředku. Prostředky Microsoft Azure jsou vymazána, vyprázdní nebo zničení pomocí metod, které jsou konzistentní s SP NIST 800-88 před uvolnění prostředků. Microsoft Azure pro odstraňování asset využívá dohlížející na bezpečný asset odstraňování služby. |


 ## <a name="nist-800-53-control-mp-5a"></a>Ovládací prvek NIST 800-53 MP-5.a

#### <a name="media-transport"></a>Přenos médií

**MP 5.a** organizace chrání a ovládací prvky [přiřazení: organizace definované typy médií informace systému] během přenosu mimo řízené oblastí pomocí [přiřazení: ochrana definované organizace zabezpečení].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure digitálního média v datových centrech společnosti Microsoft obsahovat servery, síťová zařízení a magnetické záložní pásky a disky, kde je to vhodné. Datových centrech společnosti Microsoft se nedoporučuje používat jiný digitální média. Microsoft využívá tři metody k ochraně média, které při přepravě mimo datové centrum: 1) zabezpečený přenos, 2) šifrování 3) čistit, mazání nebo zrušení. |


 ## <a name="nist-800-53-control-mp-5b"></a>Ovládací prvek NIST 800-53 MP-5.b

#### <a name="media-transport"></a>Přenos médií

**MP 5.b** organizace udržuje odpovědnosti za pro média informace systému během přenosu mimo řízené oblasti.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure udržuje odpovědnosti za pro prostředky opuštění datového centra prostřednictvím pokyny z SP NIST 800-88: konzistentní čištění nebo vyprazdňování, odstraňování asset, šifrování, přesný inventarizaci, sledování a ochranu předávací protokol během přenosu. |


 ## <a name="nist-800-53-control-mp-5c"></a>Ovládací prvek NIST 800-53 MP-5.c

#### <a name="media-transport"></a>Přenos médií

**MP 5.c** organizace dokumenty činnosti spojené s přenos médií informace o systému.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure udržuje záznamy inventáře před přenosu, sledování a ochranu o jeho postupném předávání během přenosu, asset čištění nebo vyprazdňování, odstraňování asset, obdržení prostředků a inventáře ověření po přenosu. |


 ## <a name="nist-800-53-control-mp-5d"></a>Ovládací prvek NIST 800-53 MP-5

#### <a name="media-transport"></a>Přenos médií

**MP 5** organizace omezuje aktivity přidružené k přenosu informací média systému oprávněný personál.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure omezuje aktivity přenosu asset oprávněný personál prostřednictvím ochranu předávací protokol. Použití zámků, manipulovat doklad závěr a vyžadování ověření inventáře asset zajišťuje, že jen oprávněný personál se účastní přenosu asset. |


 ### <a name="nist-800-53-control-mp-5-4"></a>NIST 800-53 řízení MP-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>Přenos médií | Kryptografické ochrany

**MP-5 (4)** systém informace implementuje kryptografických mechanismů chránit důvěrnost a integritu informace uložené v digitálního média během přenosu mimo řízené oblasti.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure zahrnuje distribučních služby Data Protection (bodů) ke správě kryptografických klíčů pomocí FIPS 140-2 šifrování na úrovni ověřit 3 modulu (cert #1694) a modulu hardwarového zabezpečení (cert #1178) zabezpečit AES 256 bitů zašifrovaná data na páskách magnetická. |


 ## <a name="nist-800-53-control-mp-6a"></a>Ovládací prvek NIST 800-53 MP-6.a

#### <a name="media-sanitization"></a>Čištění média

**MP 6.a** organizace upraví [přiřazení: média systému informace definované organizace] před uvolnění, vydání mimo organizační ovládací prvek, nebo verze pro opakované použití pomocí [přiřazení: techniky definované organizace čištění a postupy] v souladu s příslušné standardy federal a organizace a zásady.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure vyžaduje digitálního média v prostředí datového centra Microsoft Azure tak, aby vyčištěny/vyprázdní pomocí nástroje Microsoft Azure schválení a v souladu s aktualizací SP NIST 800-88, pokyny pro čištění média, před se znovu nebo odstraněny . Média bez digitální nepoužívá Microsoft Azure v prostředí datového centra. |


 ## <a name="nist-800-53-control-mp-6b"></a>Ovládací prvek NIST 800-53 MP-6.b

#### <a name="media-sanitization"></a>Čištění média

**MP 6.b** organizace využívá čištění mechanismy s sílu a integrity úměrná kategorie zabezpečení nebo klasifikaci informací.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure pomocí jednotky výmaz dat a procesy čistí nebo vymazání dat v souladu s SP NIST 800-88 a které jsou úměrná klasifikace asset Microsoft Azure prostředku. Microsoft Azure pro prostředky, které vyžadují odstranění, využívá dohlížející na bezpečný asset odstraňování služby. |


 ### <a name="nist-800-53-control-mp-6-1"></a>NIST 800-53 řízení MP-6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>Čištění média | Zkontrolujte / schválit / sledovat / dokumentů / ověření

**MP – 6 (1)** organizace zkontroluje, schválí, sleduje, dokumenty a ověřuje akcí čištění a uvolnění média.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementovala média čištění postupy v souladu s pokyny v SP NIST 800-88 pro Asset klasifikace Standard a Asset ochrany Standard. Všechna média magnetické nebo elektronické je odstraněna podle následujících specifikací NIST SP 800-88 v souladu s klasifikaci prostředku Azure jejich vyčištěny. Azure využívá jednotky výmaz dat z extrémně protokol řešení env. EPS softwaru podporuje NIST SP 800-88 požadavky pro čištění a mazání zabezpečení vymazání. |


 ### <a name="nist-800-53-control-mp-6-2"></a>NIST 800-53 řízení MP-6 (2)

#### <a name="media-sanitization--equipment-testing"></a>Čištění média | Testování zařízení

**MP – 6 (2)** organizace testy čištění zařízení a postupy [přiřazení: organizace definované frekvence] ověřit, že se se dosáhne určený čištění.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure používá k čistí nebo vymazání dat v souladu s SP NIST 800-88 jednotky výmaz dat a procesy. Za 180 dní, operace řadiče domény testy jednotek výmaz dat Microsoft Azure a proces pro vymazání. Operace řadiče domény v testu, ověřuje, zda se zamýšlené čištění dosahují prostřednictvím forenzní analýzu otestované pevných disků, potvrďte, že dat má byl upravený jednotkami výmaz dat |


 ### <a name="nist-800-53-control-mp-6-3"></a>NIST 800-53 řízení MP-6 (3)

#### <a name="media-sanitization--nondestructive-techniques"></a>Čištění média | Nedestruktivní techniky

**MP – 6 (3)** nedestruktivní čištění techniky organizace se vztahuje na přenosném úložném zařízení ještě před takovým zařízením připojení k systému informace v následujících případech: [přiřazení: definované organizace okolnosti, které vyžadují čištění přenosná úložná zařízení].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure zajistí, že datových centrech Azure postupujte podle nástroje a vyměnitelná média zabezpečení postup v datového centra služby spustit adresáře prevence nákazy prostředí Government malwarem na přenosné úložné zařízení. Postup určuje, s jednotky USB před použitím v prostředí Government provedeny následující akce: <br /> (1) Pokud nejprve zakoupené jednotky od výrobce nebo dodavatele, než počáteční použití nebo opakovaně používáno pro jiný nástroj formátu jednotky USB. <br /> (2) kontrola žádné jednotky USB, který se má použít v oblasti určené Government malwaru, před provedením jednotku do oblasti. <br /> (3) po použití formátu disku v oblasti určené Government, jednotku před odchodem oblasti. <br /> Nástroje a postup zabezpečení vyměnitelného média také vyžaduje, aby všechny jezdce, zrušených, odcizené byly ztraceny nebo jednotky nikdy nebudou znovu nezavedou do datových centrech Azure, ale budou se místo toho zařazena do katalogu a zničen. |


 ## <a name="nist-800-53-control-mp-7"></a>Ovládací prvek NIST 800-53 MP-7

#### <a name="media-use"></a>Použití média

**MP-7** organizace [výběr: omezuje; znemožňuje] použití [přiřazení: organizace definované typy médií informace systému] na [přiřazení: systémy informace definované organizace nebo součástí systému] pomocí [přiřazení: Ochrana definovaná organizace zabezpečení].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure vyžaduje asset vlastníky přiřadit jejich prostředky pomocí klasifikace asset a žádné prostředky se nevztahují tento požadavek. V prostředí datového centra Microsoft Azure označují prostředky serverů a síťových zařízení. Jiného digitálního média jako jednotky USB flash nebo Flash spravuje specifické zásady a postupy, kterými se řídí způsob správy těchto zařízení. Nepoužívají se disky CD/DVD. Bez digitální média se nepoužívá v datovém centru. Použití digitálního média v prostředí datového centra Microsoft Azure je monitorovaných 24 x 7 prostřednictvím pokrytí průmyslové televize. Další podrobnosti najdete v části PE-06. |


 ### <a name="nist-800-53-control-mp-7-1"></a>NIST 800-53 řízení MP-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>Použití média | Zakázat používání bez vlastníka

**MP-7 (1)** organizace zakáže použití přenosné úložné zařízení v systémech organizace, pokud taková zařízení osobní vlastník.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici žádné médium řízenou zákazníka v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft omezuje použití s možností zápisu, vyměnitelného média na médium, které byly explicitně schváleny Datacentra správu prostřednictvím nástroje řadiče domény a vyměnitelná média postupu. Médium, které vlastní osobní nebo má žádné osobní vlastníka je zakázáno v libovolné oblasti produkční uvedených v dokumentu Microsoft Datacenter pracovní pravidla a nařízení. |
