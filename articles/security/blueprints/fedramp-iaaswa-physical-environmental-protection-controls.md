---
title: "Zabezpečení Azure a dodržování předpisů plán, podle kterého ochrany - FedRAMP webové aplikace automatizace - fyzických a prostředí"
description: "FedRAMP webové aplikace automatizace - ochrany fyzických a prostředí"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 792b9da0f4e5ec73c39f56a6e4805cf3c37133c4
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2018
---
# <a name="physical-and-environmental-protection-pe"></a>Fyzických a prostředí ochrany (PE)

> [!NOTE]
> Tyto ovládací prvky jsou definovány NIST a USA Ministerstvo obchodu jako součást speciální publikace NIST 800-53 revize 4. Naleznete NIST 800-53 Rev. 4 informace o testování postupy a pokyny pro každý ovládací prvek.

## <a name="nist-800-53-control-pe-1"></a>NIST 800-53 Control PE-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Zásady ochrany fyzických a prostředí a postupy

**PE-1** organizace sama vyvinula, dokumenty a šíří do [přiřazení: organizace definované pracovníky nebo rolí] zásady ochrany fyzických a prostředí, která řeší účel, oboru, role, odpovědnosti, správu závazků, spolupráce mezi organizační entity a dodržování předpisů; a postupy pro usnadnění provádění zásady ochrany fyzických a prostředí a ovládací prvky přidružené ochrany fyzických a prostředí; kontroluje a aktualizuje aktuální zásady ochrany fyzických a prostředí [přiřazení: organizace definované frekvence]; a postupy ochrany fyzických a prostředí [přiřazení: organizace definované frekvence].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady ochrany fyzických a prostředí úrovni celého podniku a postupy zákazníka může být dostatečná k vyřešení tohoto ovládacího prvku. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-pe-2a"></a>NIST 800-53 řízení PE-2.a

#### <a name="physical-access-authorizations"></a>Povolení fyzický přístup

**PE 2.a** organizace sama vyvinula, schválí a udržuje seznam uživatelé, kteří mají autorizovaný přístup k zařízením, které se nachází informace systému.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Fyzický přístup k datacentru společnosti Microsoft, musí být schválen tým správy Datacenter (DCM) pomocí nástroje přístup datového centra. Přiřazení přístupu vyžadují koncové datum, po jejímž uplynutí přístup, je automaticky odstraněna a znovu musí schválit. Kromě toho při přístupu už nepotřebujete, osob zabezpečení datacenter nebo správu ručně požádat o ukončení přístupu. |


 ## <a name="nist-800-53-control-pe-2b"></a>NIST 800-53 řízení PE-2.b

#### <a name="physical-access-authorizations"></a>Povolení fyzický přístup

**PE 2.b** organizace problémy přihlašovací údaje pro autorizaci pro přístup k zařízení.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Tento nástroj přístup datacenter je autoritativní zdroj výpis všichni pracovníci s autorizovaný přístup k určité datového centra. Nástroj je spojen s datacentra fyzického zabezpečení přístupu k řízení zařízení a povolí přístup podle úrovně přístupu, které jsou schváleny tým DCM. Úrovně přístupu jsou přiřazeny v nástroji Microsoft buď uživatele vydané Odznáček nebo oznámení dočasný přístup, který je přiřazen v datacentru pomocí nadřízeného řízení místnosti (řádku). Úrovně přístupu jsou schváleny tým DCM. Navíc přiřazovány fyzické odznaky přihlašovací údaje vyžadují některé oblasti datacentra registrace uživatele biometrických dat (ruční geometrie nebo otisků prstů). |


 ## <a name="nist-800-53-control-pe-2c"></a>NIST 800-53 Control PE-2.c

#### <a name="physical-access-authorizations"></a>Povolení fyzický přístup

**PE 2.c** organizace zkontroluje přístup k seznamu s podrobnostmi o oprávněným zařízením přístup jednotlivci [přiřazení: organizace definované frekvence].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Kromě odvolání přístupu, které jsou popsané v části a Microsoft Azure recenze oprávnění seznamy přístupu pro datových centrech Azure každých 90 dní. Chcete-li odebrat nebo aktualizovat jednotlivý přístup podle potřeby. |


 ## <a name="nist-800-53-control-pe-2d"></a>NIST 800-53 řízení PE-2.d

#### <a name="physical-access-authorizations"></a>Povolení fyzický přístup

**PE 2.d** organizace odebere jednotlivce ze seznamu zařízení přístup, pokud už je vyžadován přístup.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure odebere přístup automaticky, když je dosaženo koncové datum přiřazení přístupu. Pokud už je vyžadován přístup, osob zabezpečení datacenter nebo správu bude ručně požadovat ukončení přístupu v nástroji přístup datového centra. Kromě toho Microsoft Azure odeberete všechny nepotřebné přístup autorizací zjištěna v důsledku Kontrola seznamu přístupu, který je popsaný v části c. |


 ## <a name="nist-800-53-control-pe-3a"></a>NIST 800-53 řízení PE-3.a

#### <a name="physical-access-control"></a>Fyzický přístup ovládacího prvku

**PE 3.a** organizace vynucuje autorizací fyzického přístupu na [přiřazení: organizace definované přechodu odkazuje na funkci níž se nachází informace systému] kontrolou jednotlivý přístup autorizací před udělením přístup k zařízení; a řízení vstup/výstup pro zařízení s využitím [výběr (jeden nebo více): [přiřazení: organizace definované fyzický přístup řízení systémů nebo zařízení]; chrání].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure vynucuje fyzický přístup povolení pro všechny body fyzický přístup k datových centrech Azure pomocí personální 24 x 7, výstrahy, video sledováním, vícefaktorového ověřování a man depeše portálu zařízení. |


 ## <a name="nist-800-53-control-pe-3b"></a>NIST 800-53 řízení PE-3.b

#### <a name="physical-access-control"></a>Fyzický přístup ovládacího prvku

**PE 3.b** organizace udržuje protokoly auditu fyzický přístup [přiřazení: organizace definované vstupní/výstupní body].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Všechny přístupy k zařízením datové centrum Azure jsou zaznamenána a auditovat. |


 ## <a name="nist-800-53-control-pe-3c"></a>NIST 800-53 Control PE-3.c

#### <a name="physical-access-control"></a>Fyzický přístup ovládacího prvku

**PE 3.c** poskytuje organizace [přiřazení: ochrana definované organizace zabezpečení] pro řízení přístupu k oblasti v rámci zařízení určená jako veřejně přístupná.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Datových centrech Azure neobsahují oblasti, které jsou určeny jako veřejně přístupná. |


 ## <a name="nist-800-53-control-pe-3d"></a>NIST 800-53 řízení PE-3.d

#### <a name="physical-access-control"></a>Fyzický přístup ovládacího prvku

**PE 3.d** doprovod návštěvníky organizace a monitoruje návštěvníka aktivity [přiřazení: organizace definované okolností, monitorování a nutnosti doprovod návštěvníka].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Všechny návštěvníky, které mají schvalovat přístupu do datového centra (viz PE-2), jsou určené jako doprovodu pouze na jejich odznaky nebo prostřednictvím jiné vizuální upozornění (např. barevnou odznaky) a nutných k zůstanou jejich doprovod za všech okolností. Doprovázené návštěvníci nemají žádné úrovně přístupu přidělených a mohou pouze na přístup k jejich doprovod projít. Doprovod monitorovat všechny aktivity jejich návštěvníka v datovém centru. |


 ## <a name="nist-800-53-control-pe-3e"></a>NIST 800-53 řízení PE-3.e

#### <a name="physical-access-control"></a>Fyzický přístup ovládacího prvku

**PE-3.e** Organizace zabezpečuje klíče, kombinace a dalších zařízení fyzický přístup.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. V rámci operace centra zabezpečení (SOC) jsou zabezpečené fyzické klíče a odznaky dočasný přístup. Zabezpečení osob jsou personál, 24 x 7. Klíče jsou rezervovány pro konkrétní pracovníky odpovídající oznámení osoby přístup ke klíči fyzické. Klíče inventáře prováděn každý shift a klíče nejsou povoleny mají být provedeny mimo lokalitu. |


 ## <a name="nist-800-53-control-pe-3f"></a>NIST 800-53 řízení PE-3.f

#### <a name="physical-access-control"></a>Fyzický přístup ovládacího prvku

**PE 3.f** inventáře organizace [přiřazení: organizace definované fyzický přístup zařízení] každých [přiřazení: organizace definované frekvence].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Fyzický přístup zařízení v rámci datových centrech Azure se inventarizují alespoň jednou za rok. Klíče a odznaky dočasný přístup se inventarizují několikrát za den během každé shift. Oznámení "BADGE" čtečky přístup a podobné zařízení přístup jsou propojeny s fyzického zabezpečení systému, kde je stav nepřetržitě zastoupení. |


 ## <a name="nist-800-53-control-pe-3g"></a>NIST 800-53 řízení PE-3.g

#### <a name="physical-access-control"></a>Fyzický přístup ovládacího prvku

**PE 3.g** organizace změní kombinace a klíče [přiřazení: organizace definované frekvence] nebo při klíče jsou ztraceny, dojde k ohrožení kombinace nebo jednotlivce jsou přenesených nebo byla ukončena.  

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Postupy pro implementaci v případech, kdy oznámení přístup mít datová centra služby Microsoft Azure nebo klíč dojde ke ztrátě nebo osoby je ukončeno, nebo přenos. V případě ukončení nebo přenos osoby přístup je okamžitě odebrána ze systému a jejich přístup oznámení odebrána. |


 ### <a name="nist-800-53-control-pe-3-1"></a>NIST 800-53 řízení PE-3 (1)

#### <a name="physical-access-control--information-system-access"></a>Fyzický přístup řízení | Přístup k informacím systému

**PE-3 (1)** organizace vynucuje autorizací fyzický přístup k systému informace kromě fyzické prvky řízení přístupu pro zařízení na [přiřazení: definované organizace obsahující jeden nebo více součástí fyzické prostory. informace o systému].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure další omezuje oblastí v datových centrech společnosti Microsoft, které obsahují důležité systémy (např. colocations, kritická prostředí, MDF místnostmi atd.) prostřednictvím různé mechanismy zabezpečení, jako je řízení přístupu elektronické, biometrických zařízení, a ovládací prvky Anti-passback. Přístup k Azure colocations jsou udělena jako samostatný, vyšší úroveň přístupu DCAT než jiné oblasti přístup datového centra. Kromě toho všechny Azure FTE a dodavatelů, kteří mají přístup k Azure Government colocations povinně oficiálně podstoupit blokování cloudu společnosti Microsoft a ověření přístupem USA před se autorizovaný přístup k prostředí. Další podrobnosti týkající se blokování cloud pro Azure Government colocations jsou uvedeny v části PS-03. |


 ## <a name="nist-800-53-control-pe-4"></a>NIST 800-53 Control PE-4

#### <a name="access-control-for-transmission-medium"></a>Řízení přístupu pro přenosového média

**PE 4** organizace řídí fyzický přístup k [přiřazení: informace definované organizace systému distribuce a přenosu řádky] v rámci organizace zařízení pomocí [přiřazení: ochrana definované organizace zabezpečení] .

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure implementovala řízení přístupu pro přenosového média prostřednictvím návrhu a vytvoření prostorách hlavní distribuční rámce (MDF) a colocations chránit informace systému distribuce a přenosu řádky z náhodných škod přerušení a fyzické manipulaci. Přístup k MDF místnostmi a colocations vyžadují dvoufaktorové ověřování (oznámení "BADGE" přístup a biometrických údajů). Tím se zajistí, že přístup je omezen na jen oprávněný personál (viz PE-2, PE 3). V rámci MDF jsou chráněny přenos a distribuci řádky z náhodných škod, přerušení a fyzické manipulaci prostřednictvím systému vedení, uzamčení stojany, klece nebo zásobník kabel. |


 ## <a name="nist-800-53-control-pe-5"></a>NIST 800-53 Control PE-5

#### <a name="access-control-for-output-devices"></a>Řízení přístupu pro výstupní zařízení

**PE 5** organizace řídí fyzický přístup k informace systémová výstupní zařízení a zabránit neoprávněným osobám v získání výstupu.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Datová centra služby Microsoft Azure, nemají výstupní zařízení (monitorování, tiskárny, zvuková zařízení atd.) trvale připojené k prostředkům Azure nebo Azure sdílené datové zdroje. Kromě nemá výstupní zařízení, osoby zabezpečení plní fyzické návody zařízení několikrát za shift kontrola pro položky, jako jsou dveře zamykají a stojany zabezpečený. Datacenter přístup je omezen na uživatele, kteří mají schvalovat povolení přístupu. Colocations vyžadují že dvoufaktorové ověřování (oznámení "BADGE" přístup a biometrika) získat přístup. |


 ## <a name="nist-800-53-control-pe-6a"></a>NIST 800-53 řízení PE-6.a

#### <a name="monitoring-physical-access"></a>Monitorování fyzického přístupu

**PE 6.a** organizace sleduje fyzický přístup k zařízením, které se nachází systému informace ke zjišťování a reakce na incidenty fyzického zabezpečení.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Fyzický přístup je monitorován pomocí implementace zabezpečení zařízení a procesů v datacentru. Příklady zahrnují elektronické monitorování 24 x 7 řízení přístupu, výstrahy a video systémy, jakož i 24 x 7 na lokality hlídky zabezpečení budovy a důvodů. Supervisor místnosti ovládací prvek nachází ve SOC za všech okolností zajistit monitorování fyzického přístupu v datovém centru. |


 ## <a name="nist-800-53-control-pe-6b"></a>NIST 800-53 řízení PE-6.b

#### <a name="monitoring-physical-access"></a>Monitorování fyzického přístupu

**PE 6.b** organizace zkontroluje fyzický přístup k protokolům [přiřazení: organizace definované frekvence] a po výskytu [přiřazení: událostí definovaných organizace nebo potenciální označení událostí].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Fyzický přístup k protokolům jsou zkontrolovány nepřetržitě a udržuje na následné investigativní revizi. |


 ## <a name="nist-800-53-control-pe-6c"></a>NIST 800-53 Control PE-6.c

#### <a name="monitoring-physical-access"></a>Monitorování fyzického přístupu

**PE 6.c** organizace koordinuje výsledky recenze a vyšetřování pomocí funkce organizační reakcí na incidenty. 

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Tým zabezpečení jsou popsané události zabezpečení, které se vyskytují v datovém centru. Tým zabezpečení vytváří sestavy, které monitorují podrobnosti událostí zabezpečení, jakmile dojde k události. <br /> Pro incidenty nutnosti government oznámení bude koordinovat tým zabezpečení Microsoft Azure s poskytovatelem hlavní aplikace (například O365) oznámit government agentura zákazníka, nám certifikátu a FedRAMP v rámci pokyny CERT USA (viz IR-6). |


 ### <a name="nist-800-53-control-pe-6-1"></a>NIST 800-53 řízení PE-6 (1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Monitorování fyzického přístupu | Narušení výstrahy nebo vybavení sledováním.

**PE-6 (1)** organizace monitoruje fyzického narušení výstrahy a sledováním vybavení.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Kromě zabezpečení dohlížející na bezpečný 24 x 7 datová centra služby Microsoft Azure (pronajatý a plně spravovaná) také využívat výstrahy monitorování systémů a průmyslové televize. Výstrahy se monitorují a odpověděl nadřízeného místnosti řízení umístěným 24 x 7 v sociálního Během odpovědi situaci, nadřízeného místnosti ovládací prvek využívá kamery v oblasti incidentu se prozkoumat umožnit příjemci dat v reálném čase. |


 ### <a name="nist-800-53-control-pe-6-4"></a>NIST 800-53 řízení PE-6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Monitorování fyzického přístupu | Monitorování fyzického přístupu k systémům informace

**PE-6 (4)** organizace sleduje fyzický přístup k systému informace kromě monitorování fyzického přístupu zařízení jako [přiřazení: definované organizace obsahující jednu nebo více součástí informací o fyzické prostory. systém].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure sleduje fyzický přístup k zařízení a také informace o systémy v datacentru. Všechny Microsoft online services' zařízení je umístěn v umístění v rámci datových center, kde je monitorovat fyzický přístup. Všechny společné umístění a MDF místnostmi jsou chráněny řízení přístupu, výstrahy a video. |


 ## <a name="nist-800-53-control-pe-8a"></a>NIST 800-53 řízení PE-8.a

#### <a name="visitor-access-records"></a>Přístup k záznamům v návštěvníka

**PE 8.a** organizace udržuje návštěvníka přístup k záznamům v zařízení, kde se nachází systému informace pro [přiřazení: organizace definované časové období].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. V nástroji přístup datacenter ve formě schválené žádosti jsou zachovány přístup k záznamům v datovém centru. Jak je popsáno v PE 3, je potřeba mít vyvedena za všech okolností návštěvníky. Doprovodu zaznamená přístup v rámci datového centra a pokud potřeby můžete vztažen k návštěvníka pro budoucí revize. |


 ## <a name="nist-800-53-control-pe-8b"></a>NIST 800-53 řízení PE-8.b

#### <a name="visitor-access-records"></a>Přístup k záznamům v návštěvníka

**PE 8.b** organizace zkontroluje přístup k záznamům v návštěvníka [přiřazení: organizace definované frekvence].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Návštěvníky s požadavkem na schválené přístupu bude mít jejich přístup záznam zkontrolovat v době, kdy je jejich identifikace ověřit vůči formu government vydané ID nebo Microsoft vydávat oznámení "BADGE". Jak je popsáno v PE 3, jsou návštěvníky vyvedena za všech okolností v datovém centru. |


 ### <a name="nist-800-53-control-pe-8-1"></a>NIST 800-53 řízení PE-8 (1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>Přístup k záznamům v návštěvníka | Automatizované záznamy údržby / zkontrolujte

**PE-8 (1)** organizace využívá automatizované mechanismy pro usnadnění Údržba a kontrola záznamů přístup návštěvníka.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure udržuje datacenter přístup k záznamům v DCAT ve formě schválené DCAT požadavky. Požadavky DCAT můžete schválit pouze tým DCM. Úrovně přístupu v datovém centru nejsou přiřazeni a spravované v rámci DCAT. Přístup Datacenter je čtvrtletně zkontrolovat. Veškerý přístup k datových centrech Azure je zaznamenána DCAT a je k dispozici pro budoucí možné šetření. Návštěvníky je potřeba mít vyvedena za všech okolností. Přístup doprovodu v rámci datového centra je zaprotokolována v rámci alarmů monitorování systému a v případě potřeby můžete vztažen k návštěvníka pro budoucí revize. Návštěvník přístup kterou nepřetržitě kontrolují přiřazené doprovodu a supervisor místnosti řízení prostřednictvím průmyslové televize a alarmů systému pro monitorování. Návštěvníky nejsou k dispozici s přístupem a musí být doplněn jejich doprovod za všech okolností. |


 ## <a name="nist-800-53-control-pe-9"></a>NIST 800-53 Control PE-9

#### <a name="power-equipment-and-cabling"></a>Napájení zařízení a kabeláže

**PE 9** organizace chrání power zařízení a power kabelů pro systém informace z poškození a odstranění.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure poskytuje ochranných prostory a příslušné označování pro kabely. V prostředí, které byly analyzovány pro ochranu před prostředí rizika, jako je krádež, ještě efektivněji, výbušnin, kouře, horních, prachu, musí být umístěny zařízení infrastruktury Microsoft Azure, například kabely, elektrický řádky a zálohování generátory vibrace, zemětřesení, škodlivé chemikálií, elektrické rušení, výpadky napájení, elektrický narušení (špičky). Všechny prostředky přenosné online službám (např. stojany, serverů, síťových zařízení) musí být uzamčena nebo zajištěny způsobem v místě, aby bylo možné zajistit ochranu proti odcizení nebo přesun škody. Napájení a informace o systému kabely v jakémkoli prostředí Microsoft Azure jsou správně označené a chráněná před zachycením nebo poškození. Napájení a informace o systému kabely jsou oddělené od sebe navzájem na všechny body v prostředí, aby se zabránilo narušení. |


 ## <a name="nist-800-53-control-pe-10a"></a>NIST 800-53 Control PE-10.a

#### <a name="emergency-shutoff"></a>Nouzový přístupnými

**PE 10.a** organizace poskytuje možnost vypnutím power informace systému nebo systémům, jednotlivé komponenty v případě nouze.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure nainstaloval tlačítka nouze napájení vypnuto (EPO) v umístění v rámci datového centra podle potřeby kódem místní ještě efektivněji. V některých datových centrech Microsoft Azure spravované návrhu datacenter již nevyžaduje EPO tlačítka. |


 ## <a name="nist-800-53-control-pe-10b"></a>NIST 800-53 řízení PE-10.b

#### <a name="emergency-shutoff"></a>Nouzový přístupnými

**PE 10.b** organizace umístí nouzový přístupnými přepínače nebo zařízení ve [přiřazení: organizace definované umístění informace o systému nebo součást systému] k usnadnění přístupu snadná a bezpečná pro pracovníky.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Tlačítka EPO jsou strategicky umístit umožňující aktivace v případě nouze. Tlačítka EPO mohou být umístěny v colocations, obsazená zařízení centra operace (FOCs), nebo podle potřeby kódem místní ještě efektivněji. |


 ## <a name="nist-800-53-control-pe-10c"></a>NIST 800-53 Control PE-10.c

#### <a name="emergency-shutoff"></a>Nouzový přístupnými

**PE 10.c** organizace chrání před neoprávněným aktivace možnost přístupnými nouzový napájení.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Pokud chcete zabránit náhodnému aktivace, EPO tlačítka pravděpodobně ochranných skříň, vyžadují dva aktivaci nebo využívat zvukové poplašné jako varování před aktivace. Kromě toho EPO tlačítka jsou pod dohledem videa. |


 ## <a name="nist-800-53-control-pe-11"></a>NIST 800-53 řízení PE-11

#### <a name="emergency-power"></a>Nouzový napájení

**PE 11** organizace poskytuje krátkodobé nepřerušitelný zdroj napájení pro usnadnění [výběr (jeden nebo více): řádné vypnutí systému informace; přechod systému informace na dlouhodobé alternativní power] v případě služby Zdroj výpadku primární napájení.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure implementovala nouzový power chrání datacenter zařízení a zadat okruhy s nepřerušitelný zdroj napájení (UPS) systému, které umožňuje krátkodobé napájení zajistit napájení, dokud generátory dokážou do režimu online. |


 ### <a name="nist-800-53-control-pe-11-1"></a>NIST 800-53 řízení PE-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Nouzový Power | Dlouhodobé alternativní zdroj napájení - minimální provozní schopností

**PE-11 (1)** organizace poskytuje dlouhodobé alternativní napájení pro informace o systému, který je schopné udržovat minimálně požadované provozní funkce v případě ztrátou rozšířené primární power zdroje.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementovala dlouhodobé alternativní zdroj napájení informace systému, který je schopné udržovat minimálně vyžaduje, že probíhá provozní schopností při ztrátou rozšířené primární power zdroje. Při selhání napájení, nebo hodnota klesne na úroveň nepřijatelné napětí, nepřerušitelný zdroj napájení (UPS) systémy okamžitě nové a převzít zatížení napájení. To poskytuje dostatek napájení pro spuštění servery, dokud generátory může trvat přes. Nouzové generátory získali výkon zálohování pro delším výpadkům a plánované údržby a může pracovat datového centra s rezervy na místě paliva v případě přírodní havárie. Azure udržuje vznětových generátor na řadu našich datacentrech. Zálohování generátory se používají, když je potřeba pomáhají udržovat stabilitu mřížky nebo v mimořádná opravy a údržby situace, které vyžadují nám trvat našich datacentrech vypnutí napájení mřížky. |


 ## <a name="nist-800-53-control-pe-12"></a>NIST 800-53 řízení PE-12

#### <a name="emergency-lighting"></a>Nouzové osvětlení

**PE-12** organizace používá a udržuje automatické nouzové osvětlení pro informace o systému, aktivuje se v případě výpadku napájení nebo přerušení a který pokrývá nouzový ukončí a trasy k vyprázdnění v zařízení.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Datová centra služby Microsoft Azure (pronajatý a plně spravovaná) implementovat nouzové osvětlení ve formě režijní náklady na nouzové osvětlení na vyhrazené okruhy zálohována UPS a generátor systémy (viz PE-11). Automatické nouzové osvětlení je implementováno podél všechny trasy k vyprázdnění, nouzový ukončí a uvnitř colocations v souladu se National ještě efektivněji a přidružení ochrany (NFPA) životnosti bezpečnostní kód. V případě, že dojde ke ztrátě napájení, se automaticky změní nouzového osvětlení power poskytované systémy UPS a generátor. Nouzové osvětlení systémy v rámci datová centra služby Microsoft Azure podstoupit pravidelná údržba zajistit, aby zůstaly ve správné funkčním stavu. |


 ## <a name="nist-800-53-control-pe-13"></a>NIST 800-53 řízení PE-13

#### <a name="fire-protection"></a>Protipožární ochrana

**PE 13** organizace používá a udržuje ještě efektivněji potlačení detekce zařízení nebo systémy a pro systém informace o podporovaných zdroj nezávislé energie.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure implementovala protipožární ochrana nainstalováním ještě efektivněji zjišťování a ještě efektivněji systémů potlačení v datových centrech Microsoft Azure. <br /> Datová centra služby Microsoft Azure implementovat mechanismy detekce robustní ještě efektivněji. Přístup Microsoft Azure ještě efektivněji ochrany zahrnuje použití photoelectric detektory kouře nainstalovat pod podlaží a na horní meze, které jsou integrované s postřikovačů ještě efektivněji ochrany. Kromě toho jsou systémy Xtralis VESDA (velmi časná orientační detekce přístroje) v každé společné umístění, které monitorování vzduchem. VESDA jednotky jsou systémy vzorkování vysoce citlivé letecké nainstalované v rámci více prostory vysoké hodnoty. Jednotky VESDA umožňují odpověď investigativní před alarm detekce skutečné ještě efektivněji. <br /> 'Stanice pro vyžádání obsahu, ještě efektivněji alarmů polí jsou nainstalovány v rámci datových center pro ruční ještě efektivněji upozornění. Ještě efektivněji přístroje jsou umístěné v rámci v datacentrech a jsou správně prověřovány, servis a označí každý rok. Zabezpečení pracovníků hlídek všechny oblasti vytváření více než jednou. každou shift. Datacenter pracovníky provádět každodenní návodu lokality zajištění všech ještě efektivněji sledování, které byly splněny požadavky. <br /> Oblasti obsahující citlivé elektrické zařízení (colocations, MDFs, atd.) jsou chráněny dvojité vzájemné spojení předběžné akce (suchých kanálu) postřikovače. Postřikovačů suchých kanálu se systém dvoufázová předběžné akce, který vyžaduje připojení postřikovacím head aktivace (z důvodu heat) jak kouře detekce k uvolnění horních. Aktivace head postřikovacím uvolní letecké tlak v kanály, což umožňuje kanály vyplníte horních. Horních vydání kouře nebo heat detektor také při aktivaci. <br /> Ještě efektivněji detekce/potlačení a nouzové osvětlení systémy jsou zapojeny do datového centra UPS a generátor systémů poskytujících zdroje redundantní napájení. |


 ### <a name="nist-800-53-control-pe-13-1"></a>Ovládací prvek PE-13 NIST 800-53 (1)

#### <a name="fire-protection--detection-devices--systems"></a>Protipožární ochrana | Detekce zařízení nebo systémy

**PE-13 (1)** zahrnuje organizace aktivovat zjišťování zařízení nebo systémů informace systému které aktivovat automaticky a upozorňovat [přiřazení: organizace definované pracovníky nebo rolí] a [přiřazení: nouzové definované organizace respondéry] v případě ještě efektivněji.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure zahrnuje fire detekce zařízení nebo systémů informace systému které aktivovat automaticky a upozornit pracovníky datacenter společně s nouzový respondéry v případě ještě efektivněji. V případě, že jeden z mechanismů zjišťování ještě efektivněji aktivována v žádné společné umístění, oddělení místní ještě efektivněji automaticky informuje prostřednictvím ještě efektivněji poplašný systém. Kromě toho systémech protipožární ochrany a ještě efektivněji detekce, jsou svázané do systému zabezpečení oznamování místní zaměstnancům zařízením a zabezpečení. |


 ### <a name="nist-800-53-control-pe-13-2"></a>Ovládací prvek PE-13 NIST 800-53 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>Protipožární ochrana | Potlačení zařízení nebo systémy

**PE-13 (2)** organizace zahrnuje fire potlačení zařízení nebo systémů informace systému, které poskytují automatické oznámení žádné aktivace [přiřazení: organizace definované pracovníky nebo rolí] a [přiřazení: organizace definované nouzový respondéry].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. V případě, že jeden z potlačení systémů ještě efektivněji se aktivuje v datacentru, je prostřednictvím ještě efektivněji poplašný systém automaticky upozornění místní oddělení ještě efektivněji. Kromě toho systémech protipožární ochrany a ještě efektivněji detekce, jsou svázané do systému zabezpečení oznamování místní zaměstnancům zařízením a zabezpečení. |


 ### <a name="nist-800-53-control-pe-13-3"></a>Ovládací prvek PE-13 NIST 800-53 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>Protipožární ochrana | Automatická ještě efektivněji potlačení

**PE-13 (3)** organizace využívá potlačení funkci Automatická ještě efektivněji pro informace o systému, pokud zařízení není přiřazeny zdroje trvale.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Datová centra služby Microsoft Azure jsou personál, 24 x 7. Potlačení systémech protipožární zaujmout automaticky bez ručních zásahů, při zjištění situaci alarmů ještě efektivněji. |


 ## <a name="nist-800-53-control-pe-14a"></a>NIST 800-53 řízení PE-14.a

#### <a name="temperature-and-humidity-controls"></a>Teploty a vlhkosti ovládací prvky

**PE 14.a** organizace udržuje úrovně teploty a vlhkosti v zařízení, kde je umístěn v systému informace [přiřazení: definované organizaci přijatelné úrovni].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure udržuje úrovně teploty a vlhkosti v souladu s pokyny American společnosti z vytápění, Refrigerating a klimatizace technici (ASHRAE). Úrovně teploty a vlhkosti jsou monitorovány nepřetržitě podle datacentra vytváření správu systému (BMS). |


 ## <a name="nist-800-53-control-pe-14b"></a>NIST 800-53 řízení PE-14.b

#### <a name="temperature-and-humidity-controls"></a>Teploty a vlhkosti ovládací prvky

**PE 14.b** organizace monitoruje teploty a vlhkosti úrovně [přiřazení: organizace definované frekvence].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. V datová centra služby Microsoft Azure jsou teploty a vlhkosti úrovně nepřetržitě monitorován pomocí správy systému sestavování (BMS). Datacenter pracovníky monitoru BMS z pracoviště Operations Center (FOC), tak, aby předtím, než se překročí všechny body alarmů můžou spravovat teploty a vlhkosti v rámci datového centra. |


 ### <a name="nist-800-53-control-pe-14-2"></a>Ovládací prvek PE-14 NIST 800-53 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Teploty a vlhkosti ovládací prvky | Monitorování pomocí nastavení upozornění nebo oznámení

**PE-14 (2)** organizace používá teploty a vlhkosti, monitorování, které poskytuje upozornění na změny potenciálně škodlivého nebo upozornění pro pracovníky a vybavení.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. V datová centra služby Microsoft Azure jsou teploty a vlhkosti úrovně nepřetržitě monitorován pomocí správy systému sestavování (BMS). Datacenter pracovníky monitoru BMS z pracoviště Operations Center (FOC), tak, aby předtím, než se překročí všechny body alarmů můžou spravovat teploty a vlhkosti v rámci datového centra. |


 ## <a name="nist-800-53-control-pe-15"></a>NIST 800-53 řízení PE-15

#### <a name="water-damage-protection"></a>Horních poškození ochrany

**PE 15** organizace chrání systémové informace z škod vzniklých v důsledku úniku horních tím, že poskytuje hlavní přístupnými nebo izolace ventily, které jsou přístupné, funguje správně a známé klíče osobám.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure poskytuje horních nebo úniku detekce v oblastech s představuje riziko úniku horních (například letecké obslužné rutiny jednotky). Potlačení systémech protipožární mít také k úniku detekce výstrahy, které jsou monitorovány. Zjišťování systému horních nebo úniku je integrovaná do systému kontrolují výstrahy a oznámení. Postřikovače v datacentru rozděleny na zóny. Personál datového centra se seznámíte s postupy v naléhavých vyžadující použití ventily přístupnými horních a jejich umístění. Riserů postřikovacím mít možnost vypnuté, samostatně nebo jako skupinu prostřednictvím ventily brány. Všechny postřikovačů v kritická místa jsou postřikovačů dvojité vzájemné spojení typ předběžné akce, které vyžadují dvě formy aktivace předtím, než je zahájeno toku. Přetížení postřikovacím systému se monitoruje a sleduje výstražné zařízení před úniky horních. |


 ### <a name="nist-800-53-control-pe-15-1"></a>Ovládací prvek PE-15 NIST 800-53 (1)

#### <a name="water-damage-protection--automation-support"></a>Vodní poškození ochrany | Podpora automatizace

**PE-15 (1)** organizace využívá automatizované mechanismy pro zjištění přítomnosti horních v blízkosti výstrahy a informace o systému [přiřazení: organizace definované pracovníky nebo rolí].

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure používá automatizované mechanismy k detekci přítomnosti horních v datacentru a výstrahy pracovníky datového centra. Azure poskytuje horních nebo úniku detekce v oblastech s představuje riziko úniku horních (například letecké obslužné rutiny jednotky). Potlačení systémech protipožární mít také k úniku detekce výstrahy, které jsou monitorovány. Zjišťování systému horních nebo úniku je integrovaná do systému kontrolují výstrahy a oznámení. Přetížení postřikovacím systému se monitoruje a sleduje výstražné zařízení před úniky horních. |


 ## <a name="nist-800-53-control-pe-16"></a>NIST 800-53 řízení PE-16

#### <a name="delivery-and-removal"></a>Doručení a odebrání

**PE-16** organizace povolí, sleduje a ovládací prvky [přiřazení: organizace definované typy informace o systémových součástí] zadávání a ukončení zařízení a udržuje záznamy těchto položek.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure implementuje tento požadavek potřeby jejich zákazníků. Microsoft Azure implementuje striktní vynucení je povolené, zadejte a zavřete datového centra. Všechny systémové součásti na prostředky jsou sledovány v databázi nástroje pro správu asset. |


 ## <a name="nist-800-53-control-pe-17a"></a>NIST 800-53 řízení PE-17.a

#### <a name="alternate-work-site"></a>Alternativní pracovní lokality

**PE 17.a** organizace využívá [přiřazení: kontrolních mechanismů pro zabezpečení organizace definované] v lokalitách alternativní práci.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může být zákazníka podnikové úrovni alternativní pracovní lokality zřizuje. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-pe-17b"></a>NIST 800-53 řízení PE-17.b

#### <a name="alternate-work-site"></a>Alternativní pracovní lokality

**PE 17.b** organizace vyhodnocuje jako vhodná, na účinnosti zabezpečení ovládací prvky v lokalitách alternativní pracovní.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může být zákazníka podnikové úrovni alternativní pracovní lokality zřizuje. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-pe-17c"></a>NIST 800-53 řízení PE-17

#### <a name="alternate-work-site"></a>Alternativní pracovní lokality

**PE 17** organizace poskytuje prostředky pro zaměstnance ke komunikaci s pracovníky informace o zabezpečení v případě incidenty zabezpečení nebo problémy.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může být zákazníka podnikové úrovni alternativní pracovní lokality zřizuje. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-pe-18"></a>Ovládací prvek PE 18 NIST 800-53

#### <a name="location-of-information-system-components"></a>Umístění součástí systému informace

**PE 18** organizace umisťuje informace komponent systému v zařízení, chcete-li minimalizovat potenciální poškození [přiřazení: definované organizace fyzických a prostředí ohrožení] a minimalizovat možnost pro neoprávněný přístup přístup.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Azure implementuje přístup v rámci návrhu strategické datacenter vyhovět umístění ovládací prvek komponenty informace o systému. Všechny Microsoft online services' zařízení je umístěn v umístění, které byly analyzovány pro ochranu před prostředí rizika například odcizení, ještě efektivněji, výbušnin, kouře, horních, prachu, vibrace, zemětřesení, škodlivé chemikálií, elektrické rušení, výpadky napájení, elektrický narušení (špičky) a záření. Zařízení a infrastruktury byla implementována seismická výztuhy pro ochranu před riziky prostředí. Všechny společné umístění a MDF místnostmi jsou chráněny řízení přístupu, výstrahy a video. Zařízení je také hlídány zabezpečení osobami 24 x 7. Všechny přenosné Azure prostředky jsou zamčené nebo zajištěny způsobem v místě, aby bylo možné zajistit ochranu proti odcizení nebo přesun škody. |


