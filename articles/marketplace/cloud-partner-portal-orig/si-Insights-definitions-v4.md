---
title: Definice Insights prodejce | Dokumentace Microsoftu
description: Obsahuje definice pro celou řadu termínů, které najdete v prodejce Insights...
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: e97f963c7ca2362130ede5fe44b637a9a7ef57e2
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809267"
---
<a name="seller-insights-definitions"></a>Definice Insights prodejce
=======================

Následující tabulka obsahuje definice pro celou řadu termíny používané ve službě Insights prodejce.

|  **Termín**                                        |  **Definice**                                                                                                                              |
|  --------------------------------------------    |  ---------------------------------------------------------------------------------------------------------------------------------           |
| Typ licence pro Azure                               | Typ licenční smlouvy k nákupu Azure použít zákazníky. Označované také jako kanál.                                                  |
| Typ licence Azure: Cloud Solution Provider      | Azure a nabídky Marketplace přes svého poskytovatele Cloud Solution Provider, který funguje jako svého prodejce opatřuje finanční koncového zákazníka.                 |
| Typ licence Azure: Enterprise                   | Azure a nabídky Marketplace prostřednictvím smlouvy Enterprise, podepsaný přímo s Microsoftem opatřuje finanční koncového zákazníka.                  |
| Typ licence Azure: Organizace prostřednictvím prodejce  | Azure a nabídky Marketplace prostřednictvím prodejce, který usnadňuje jejich smlouvy Enterprise s Microsoftem opatřuje finanční koncového zákazníka.     |
| Typ licence Azure: průběžné platby                | Azure a nabídky Marketplace prostřednictvím smlouvy s průběžnými platbami, podepsaný přímo s Microsoftem opatřuje finanční koncového zákazníka.                |
| GUID předplatného Azure                          | Globální jedinečný identifikátor (GUID) předplatné Azure, kdy zákazník využil k nákupu nabídky Marketplace.                     |
| Poplatek za dobu (kopie)                               | Určená pro zákazníky v fakturační měna zákazníka (kopie).                                                                 |
| Poplatek za dobu (PC)                               | Určená pro zákazníky, v *výběr měny (PC)*.                                                                      |
| Datum poplatku                                      | Datum, kdy se počítá poplatek zákazníka, obvykle hned za období využití.                                             |
| Název Instance cloudu                              | Cloud od Microsoftu v němž došlo k nasazení virtuálního počítače. (Azure Gov – instanci Microsoft cloudu pro státní správu USA; Azure Čína - instanci Microsoft cloudu v Číně; Služba Azure Germany – instanci Microsoft cloudu v Německu; Azure Global - instance cloudu společnosti Microsoft pro všechny globální umístění)                                                          |
| Vlastní měření využití                               | Měřené jednotky spotřebovávanou na vlastní měřič nabídky.                                                                                        |
| Zákazník                                         | Libovolný zákazník Azure nebo koncového uživatele, který získá, používá nebo jinak zobrazuje nabídku publikované prostřednictvím webu Marketplace. Zákazníci mohou identifikovat jedinečný *GUID předplatného Azure*.                                                                                                                                                                        |
| Město zákazníka                                    | Název města poskytované koncového zákazníka.                                                                                                  |
| Jazyk odběratele komunikace                  | Jazyk upřednostňuje zákazníka pro komunikaci.                                                                                    |
| Název společnosti zákazníka                            | Název společnosti poskytované koncového zákazníka.                                                                                               |
| Zemi zákazníka                                 | Název země poskytované koncového zákazníka.                                                                                               |
| Zákazník měny (kopie)                           | Měna preferované zákazníkem pro ceny a fakturace.                                                                              |
| Zákaznických e-mailů                                   | E-mailovou adresu poskytované koncového zákazníka.                                                                                              |
| Křestní jméno odběratele                              | Jméno zákazníka.                                                                                                              |
| Příjmení odběratele                               | Příjmení odběratele.                                                                                                               |
| Typ platby zákazníků                            | Typ platební nástroj využívaných zákazníkem.                                                                                         |
| Poštovní směrovací číslo zákazníka                             | PSČ poskytované koncového zákazníka.                                                                                                |
| Stav zákazníka                                   | Stav poskytované koncového zákazníka.                                                                                                      |
| Datum získání                                    | Datum první předplatné Azure, které všechny využili nabídku nákupu někým jiným než vámi.                                                                  |
| Datum ztráty                                        | Datum předplatné Azure bylo zrušeno všech nabídek někým jiným než vámi.                                                                     |
| Odhadovaný poplatek rozšířené (kopie)                   | Odhadované rozšířené poplatek za množství jednotek využití pro danou skladovou jednotku (v místní měně zákazníka). Tato hodnota nemusí být přesné z důvodu chyby zaokrouhlení nebo zkrácení.                                                                                                                                                                                           |
| Odhadovaný poplatek rozšířené (PC)                   | Odhadované rozšířené poplatek za množství jednotek využití pro daný skladová jednotka založená na směnných převod na datum využití se počítá (v místní měně vydavatele). Tato hodnota nemusí být přesné z důvodu chyby zaokrouhlení nebo zkrácení.                                                                                                                |
| Odhadované výběr (PC)                            | Odhadované poplatky za množství jednotek využití pro danou skladovou jednotku podle cizích měn převod na datum, kdy využití se počítá (v místní měně vydavatele). Tato hodnota nemusí být přesné z důvodu chyby zaokrouhlení nebo zkrácení.                                                                                                                |
| Odhadovaná cena (PC)                             | Odhadovaná cena za jednotku využití pro daný skladová jednotka založená na směnných převod na datum využití se počítá (v místní měně vydavatele). Tato hodnota nemusí být přesné z důvodu chyby zaokrouhlení nebo zkrácení.                                                                                                                                            |
| Stav poslední shromažďování                          | Pro konkrétní poplatek, nejnovější stav životní cyklus fakturace a kolekce. To může být: *Probíhá shromažďování*, *zjišťované*, *placené si*, *refundaci ve výši,* nebo *zápisu vypnout*.                                                                                                                                                                                      |
| Stav poslední shromažďování: shromážděných               | Byl shromážděn poplatek.                                                                                                                    |
| Stav poslední kolekce: Probíhá shromažďování  | Platí ještě nebyla shromážděna od zákazníka. Fakturace Microsoft Azure je stále fakturace nebo shromažďování od zákazníka.             |
| Stav poslední shromažďování: náhrady                  | Sazba byla vrácena (celý poplatek nebo částečné částka).                                                                      |
| Stavu poslední kolekce: Zápis vypnuto               | Jako nedobytné pohledávky odepsaných poplatek.                                                                                                      |
| IsNew zákazníka                                   | Zákazník nasazená s tímto *SKU* prvním v rámci kalendářního měsíce.                                                               |
| Trh                                           | Název země poskytované koncového zákazníka.                                                                                               |
| Typ licence Marketplace                         | Metodu fakturace nabídky Marketplace.                                                                                                 |
| Marketplace typ licence: Fakturuje v rámci Azure   | Společnost Microsoft je agenta pro tuto nabídku Marketplace a účtuje zákazníkům vaším jménem. (Průběžné platby platební karty nebo faktury Enterprise)       |
| Typ licence Marketplace: Používání vlastní licence | Virtuální počítač vyžaduje licenční klíč zákazníka k dispozici za účelem nasazení. Microsoft není fakturovat zákazníků pro jejich využití této nabídky na webu Marketplace. |
| Marketplace typ licence: zdarma                   | Nabídka Marketplace byl nakonfigurován jako zdarma pro všechny uživatele. Microsoft není fakturovat zákazníků pro jejich použití v rámci této nabídky Marketplace.    |
| Marketplace typ licence: Microsoft prodejce  | Společnost Microsoft se pro tuto nabídku Marketplace svého prodejce.                                                                                       |
| Pořadí Marketplace                                | Pro virtuální počítače, na webu Marketplace objednávku představuje nasazení jedné nebo mnoha virtuálních počítačů vázané na jeden *SKU* jedinečný předplatného Azure. Na jednu objednávku Marketplace mohou představovat větší proměnné jader v mnoha nasazeních. Pro spravované aplikace a služby vývojářů, pořadí Marketplace představuje jeden *SKU* zakoupit *GUID předplatného Azure*.           |
| Microsoft poplatek (kopie)                               | Microsoft poplatky za transakce v měně zákazníka.                                                                                 |
| Normalizovaná využití                                 | Hodin využití normalizovány na účet pro počet jader virtuálního počítače, které jsou zahrnuté ve využití: [počet jader virtuálního počítače] x [hodin nezpracovaná používání]. Virtuální počítače označený jako "SHAREDCORE" jako násobitel [počet jader virtuálního počítače] použít 1/6 (nebo 0.1666).                                                                                                                                       |
| Název nabídky                                       | Název nabídky Marketplace.                                                                                                           |
| Typ nabídky                                       | Typ řešení.                                                                                                                        |
| Typ nabídky: Vlastní měření řešení                | Zákazník nasadí vlastní Účtovaná podle objemu dat řešení z Marketplace.                                                                     |
| Typ nabídky: Spravované aplikace                  | Zákazník si koupili předplatné do aplikace spravované služby.                                                                   |
| Typ nabídky: Jeden virtuální počítač                            | Zákazník nasadí image tak, že vyberete jeden virtuální počítač.                                                                                     |
| Typ nabídky: Šablona řešení                    | Zákazník nasadit šablonu řešení.                                                                                                |
| Datum zrušení objednávky                                | Datum, kdy byla zrušena pořadí Marketplace.                                                                                                 |
| Počet objednávek                                      | Počet objednávek (aktivní a zrušené) této konkrétní *GUID předplatného Azure* nemá žádné nabídky.                               |
| Datum nákupní objednávky                              | Datum *Marketplace pořadí* byl vytvořen. Pro virtuální počítače je to první datum, bitové kopie byla nasazena s předplatným Azure zákazníka. Následné nasazení této stejnou bitovou kopii do stejného předplatného Azure jsou že všechny považovány za jednu objednávku.                                                                                                       |
| Stav objednávky                                     | Stav pořadí Marketplace v době, kdy data poslední aktualizaci.                                                                   |
| ORDER Status: Active                             | Pro spravované aplikace pořadí je aktivní. Pro virtuální počítače zákazník má minimálně jedno nasazení bitové kopie u svého předplatného Azure.   |
| ORDER Status: zrušeno                          | Pro spravované aplikace objednávka byla zrušená. Pro virtuální počítače, má zákazník odstraní všechna nasazení *SKU* ze svého předplatného Azure.        |
| ID objednávky                                          | Jedinečný identifikátor objednávky. Pro spravované aplikace zákazník koupil měsíční předplatné ke službě. Pro virtuální počítače má zákazník nasazené bitové kopie. Následné nasazení této stejnou bitovou kopii do stejného předplatného Azure jsou že všechny považovány za jednu objednávku.                                                                       |
| Typ platby: karta                               | Zákazník zaplatí jejich poplatků za Marketplace pomocí platební karty.                                                                          |
| Typ platby: faktury                            | Zákazník zaplatí jejich poplatků za Marketplace prostřednictvím faktury.                                                                                 |
| Výběr částka (PC)                               | Velikost placené, v upřednostňovanou *výběr měny (PC)*.                                                                            |
| Výběr měny (PC)                             | Měna použitá pro vaše výplaty.                                                                                                          |
| Výběr data                                      | Datum, kdy byla vyslána žádost platby od Microsoftu se svou bankou.                                                                           |
| Výběr stavu                                    | Označuje, kde je transakce v životního cyklu výběr: *placené si*, *nadcházející výběr*, nebo *není připraven pro výběr*.                        |
| Výběr stavu: Není připraven pro výběr              | Transakce není připraven pro výběr. (Viz *konečný stav kolekce* další podrobnosti)                                                    |
| Výběr stavu: Hotovostní platba                          | Transakce byla zahrnuta v posledních výpočtu výběr. Placené kladné hodnoty a Celková dlužná částka se odečíst od záporné hodnoty. |
| Výběr stav: Výběr nadcházející                   | Transakce je připravený pro výběr a zahrne do dalšího výpočtu dostupný výběr.                                           |
| Ve verzi Preview SKU                                      | Jste označili *SKU* jako "náhled", a můžete nasadit a použít tuto bitovou kopii pouze předplatná Azure na seznamu povolených vámi.                       |
| Cena (kopie)                                       | Cena za jednotku využití pro danou skladovou jednotku (v místní měně zákazníka).                                                                  |
| Vyjádřit výslovný souhlas propagační kontaktu                       | Určuje, zda zákazník proaktivně výslovného souhlasu pro propagační kontaktu vydavatele. V tuto chvíli jsme nejsou nabízí ten samý možnost pro zákazníky, aby nám sdělily "Ne" na panelu. Po nasazení této funkce začneme odpovídajícím způsobem aktualizuje.                                                                                          |
| Vydavatel měny (PC)                          | Měna upřednostňuje vydavatele pro výběr.                                                                                          |
| Nezpracovaná využití                                        | Hodin využití pro vaši nabídku Marketplace.                                                                                                      |
| Prodejce e-mailu                                   | E-mailová adresa prodejce zahrnutých v prodeji pro koncové zákazníky.                                                                  |
| Jméno prodejce                                    | Jméno prodejce Microsoft Správa koncového zákazníka.                                                                                |
| Identifikátor URI prostředku                                     | Jedinečný identifikátor pro jednotlivé virtuální počítače nebo pro vývojáře služby nasazení.                                                                    |
| Skladová jednotka (SKU)                                              | *Skladová položka* pojmenovat, jak je definováno během publikování. Nabídka může mít mnoho *SKU,* ale *SKU* může být přidružen pouze jediné nabídky.              |
| Typ SKU fakturace                                 | Metodu fakturace *SKU*.                                                                                                             |
| Typ SKU fakturace: BYOL                           | Virtuální počítač vyžaduje licenční klíč zákazníka k dispozici za účelem nasazení. Microsoft není fakturovat zákazníků pro jejich využití této nabídky na webu Marketplace.   |
| Typ SKU fakturace: zdarma                           | *SKU* je nakonfigurovaný, aby byl zdarma pro všechny uživatele. Microsoft není fakturovat zákazníků za využití tohoto *SKU.*                            |
| Typ SKU fakturace: Microsoft jako prodejce          | Microsoft je svého prodejce to *SKU.*                                                                                                   |
| Typ SKU fakturace: placené                           | Microsoft je agenta pro tuto *SKU* a účtuje zákazníkům vaším jménem. (Průběžné platby platební karty nebo faktury Enterprise)                   |
| Typ SKU fakturace: zkušební verze                          | Zákazník je v jejich zkušebního období a se převede na placené, je-li zrušit nebo odstranit.                                         |
| Částka daně (kopie)                                  | Částka daně, které jsou na faktuře zákazníka v *zákazníka měny (kopie)*.                                                               |
| Datum transakce                                 | Datum transakce se zaznamenávají do vytváření sestav výběr.                                                                               |
| Typ transakce                                 | Typ transakce, která hlásí.                                                                                              |
| Typ transakce: poplatek                         | Transakce je kladnou hodnotu představující dobu, účtuje zákazníkovi.                                                         |
| Typ transakce: Náhrada zákazníka                | Poplatek za zákazníka byla vrácena. Tato transakce je záporná hodnota se shoduje s množství kladné poplatek zákazníka. Můžete určit odpovídající kladnou hodnotu, která dříve byla zaplacena určením transakce se stejným *datum poplatku* a *typ transakce* = "Poplatek za" a *konečný Stav shromažďování* = "Náhrada."                   |
| Typ transakce: Výběr úpravy              | Transakce představuje kladné nebo záporné úprav u zůstatek na vašem microsoftem, vytvořit účet pro předchozí chybě fakturace nebo výběr.                                                                                                                                                                                            |
| Typ transakce: Odepsat                      | Poplatek za zákazníka odepsaných do špatného dluh. Tato transakce je záporná hodnota se shoduje s množství kladné poplatek zákazníka. Můžete určit odpovídající kladnou hodnotu, která byla dříve placené určením transakce se stejným *datum poplatku* a *typ transakce* = "Poplatek za" a *konečný Stav shromažďování* = "Odepsat." |
| Typ transakce: Odepsat Storno             | Transakce je kladnou hodnotu představující změnu doposud zapsán mimo transakci.                                         |
| Zkušební verze koncové datum                                   | Data zkušební období pro tuto objednávku se ukončí nebo skončila.                                                                              |
| Využití                                            | Použití ohlášených zákazníky *SKU.* Pro Image virtuálních počítačů představují záznamů využití ohlášené dobu pro velikost tohoto virtuálního počítače a *SKU*.   |
| Datum ukončení využití                                   | Koncové datum ohlašovaný období využití.                                                                                             |
| Datum využití                                       | Došlo k používání zákazníky datum.                                                                                                             |
| Referenční informace k využití                                  | Identifikátor pro jeden nebo více dnů využití ze strany zákazníků pro danou skladovou Položku přidruženou ke položka v sestavě výběr.                         |
| Datum zahájení využití                                 | Počáteční datum období využití hlásí.                                                                                           |
| Typ použití                                       | Popis měří využití. (*Normalized využití* nebo *nezpracovaná využití*)                                                               |
| Využití jednotek                                      | Jednotka měření pro uvedené využití. Virtuální počítače jsou vždy prováděno pomocí hodinové jednotky měření.                                      |
| Velikost virtuálního počítače                                          | Představuje velikost hardwaru virtuálního počítače, v souladu s nabídku Azure. Mezi příklady patří `Basic_A0`, `Standard_A11`, `Standard_D12`, a `Standard_G4`.   |
|  |  |


