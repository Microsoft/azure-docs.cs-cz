---
title: Nejčastější dotazy k komerčním obchodům na webu Marketplace & terminologie – partnerský partner
description: Získejte odpovědi na nejčastější dotazy týkající se komerčních analýz na webu Marketplace v partnerském centru. Tento článek obsahuje slovník dat pro terminologii analýzy.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
author: shganesh-dev
ms.author: shganesh
ms.openlocfilehash: ed836e3df28c5e39aa7f9d21f020c0d37292dcfc
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631796"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>Terminologie a běžné otázky ke komerčním analýzám na webu Marketplace

Tento článek se zabývá nejčastějšími dotazy týkajícími se analytických zpráv v partnerském centru a také poskytuje slovník analytických terminologie.

## <a name="common-questions"></a>Časté dotazy

**Nedaří se mi zobrazit data analýz v partnerském centru. Po přístupu na tyto stránky se zobrazí zpráva níže. Proč je to?**

![Zatím žádná data pro vaše nabídky](./media/analytics-faq-no-data.png)

Proč se vám může stát, že se vám tato zpráva zobrazuje:

- Pro vaše publikované nabídky na webu Marketplace zatím neexistují žádné akvizice. To může znamenat, že vaše nabídky jsou živé v Marketplace a získají zobrazení od zákazníků na stránkách zobrazení produktu, ale zákazníci zatím neudělali, že si je nakoupili a nasadili.
- Publikování vaší nabídky může být v procesu a ještě není živé. Zákazníci mohou získat pouze živé nabídky. Pokud chcete zjistit stav nabídek, přečtěte si téma Přehled na [řídicím panelu analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Další informace najdete v tématu [souhrnný řídicí panel v tématu Analýza komerčního tržiště](./summary-dashboard.md).
- Vaše nabídky můžou být uvedené jako **kontaktní mě**, což jsou jenom nabídky se seznamem a zákazníci na webu Marketplace je nemůžou koupit. I když tyto nabídky umožňují generovat zájemce a sdílí se s vámi, objednávky pro tyto nabídky se nevytvoří, protože je nejde koupit. Chcete-li kontrolovat typ seznamu nabídek, klikněte na stránku nastavení.

**Vím, že mám analytická data, ale zobrazuje se následující zpráva:**

![Pro daný rozsah dat nejsou k dispozici žádná data.](./media/analytics-faq-data-range.png)

Pokud obdržíte tuto zprávu, znamená to, že máte analytická data, ale pro vybraný rozsah dat nejsou k dispozici žádná data. Vyberte jiný rozsah kalendářních dat nebo vlastní rozsah kalendářních dat, abyste zobrazili data od 2010. Další informace najdete v části rozsah dat [v řídicím panelu souhrnu v tématu Přehled komerčních webů na webu Marketplace](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Slovník datových podmínek

| Název atributu | Sestavy | Definice|
|---|---|---|
| Typ licence Azure | Zákazník, objednávka | Typ licenční smlouvy používané zákazníky k nákupu Azure. Známý také jako kanál |
| Typ licence Azure: Cloud Solution Provider | Zákazník, objednávka | Koncový zákazník si nabídne Azure a vaši nabídku na webu Marketplace prostřednictvím poskytovatele Cloud Solution Provider, který funguje jako váš prodejce.|
| Typ licence Azure: Enterprise | Zákazník, objednávka | Koncový zákazník získává Azure a nabídku na webu Marketplace prostřednictvím smlouva Enterprise podepsaného přímo s Microsoftem.|
| Typ licence Azure: Enterprise přes prodejce  | Zákazník, objednávka | Koncový zákazník si nabídne Azure a vaši nabídku na webu Marketplace prostřednictvím prodejce, který usnadní jejich smlouva Enterprise s Microsoftem.|
| Typ licence Azure: průběžné platby| Zákazník, objednávka | Koncový zákazník získává Azure a nabídku na webu Marketplace prostřednictvím smlouvy s průběžnými platbami, která se podepisuje přímo s Microsoftem.|
| ID fakturačního účtu| Zákazník, objednávka, použití| Identifikátor účtu, ve kterém se vygenerovala faktura Tento atribut slouží k propojení analytických sestav se sestavou transakce výběr.|
| Název cloudové instance| Objednání| Microsoft Cloud, ve kterém došlo k nasazení virtuálního počítače.|
| Název instance cloudu: globální Azure| Objednání| Veřejný globální Cloud Microsoftu.|
| Název instance cloudu: Azure Government | Objednání| Cloudy Microsoftu pro konkrétní státní správu pro jednu z následujících vlád: Čína, Německo nebo USA v Americe.| |
| Město zákazníka| Zákazník| Název města, který zadal zákazník. Město může být jiné než město v předplatném Azure zákazníka.|
| Jazyk zákaznické komunikace  | Zákazník| Jazyk upřednostňovaný zákazníkem pro komunikaci.|
| Název společnosti zákazníka | Zákazník, objednávka | Název společnosti poskytnutý zákazníkem Název může být jiný než město v předplatném Azure zákazníka.|
| Země nebo oblast zákazníka | Zákazník, objednávka | Název země nebo oblasti, kterou zadal zákazník. Země nebo oblast se může lišit od země nebo oblasti v předplatném Azure zákazníka.|
| E-mail zákazníka| Zákazník| E-mailová adresa poskytnutá koncovým zákazníkem E-mail může být jiný než e-mailová adresa v předplatném Azure zákazníka.|
| Křestní jméno zákazníka| Zákazník| Název zadaný zákazníkem Název může být jiný než název uvedený v předplatném Azure zákazníka.|
| ID zákazníka | Zákazník, objednávka | Jedinečný identifikátor přiřazený k zákazníkovi. Zákazník může mít nula nebo více Azure Marketplace předplatných.|
| Poštovní směrovací číslo zákazníka  | Zákazník| Poštovní směrovací číslo poskytnuté zákazníkem Kód může být jiný než poštovní směrovací číslo poskytované v rámci předplatného Azure zákazníka.|
| Stav zákazníka| Zákazník| Stav (adresa) poskytovaný zákazníkem. Stav může být jiný než stav uvedený v předplatném Azure zákazníka.|
| Datum získání| Zákazník| První datum, kdy si zákazník koupil jakoukoli nabídku publikovanou vámi|
| Datum ztráty| Zákazník| Poslední datum, kdy zákazník zrušil poslední z předchozích nabídek, byl zakoupen.|
| Je nový zákazník  | Objednání| Tato hodnota identifikuje nového zákazníka, který získá jednu nebo více nabídek poprvé (nebo ne). Hodnota bude "Ano", pokud se nachází ve stejném kalendářním měsíci pro "datum získání". Pokud zákazník zakoupil nějaké nabídky před nahlášeným kalendářním měsícem, bude hodnota "ne". |
| Je verze Preview SKU| Objednání| Tato hodnota vám umožní zjistit, jestli jste SKU označili jako "Preview". Hodnota bude "Ano", pokud je odpovídajícím způsobem označena skladová jednotka a pouze předplatná Azure, která jsou autorizována, můžete nasadit a použít tuto bitovou kopii. Pokud nebyla položka SKU identifikována jako "Preview", bude hodnota "ne". |
| Je propagační kontaktní osoba v| Zákazník| Tato hodnota vám umožní zjistit, jestli se zákazník aktivně přihlásil k propagačnímu kontaktu od vydavatelů. V tuto chvíli Neprezentujeme možnost pro zákazníky, takže jsme na vývěsce uvedli "ne". Po nasazení této funkce se odpovídajícím způsobem spustí aktualizace.|
| Typ licence Marketplace| Objednání| Způsob fakturace nabídky Marketplace|
| Typ licence Marketplace: účtuje se přes Azure.| Objednání| Microsoft je vaším agentem pro tuto nabídku na webu Marketplace a účtuje zákazníky vaším jménem. (Buď platební karta PAYG, nebo faktura na podnikové straně)|
| Typ licence Marketplace: Přineste si vlastní licenci | Objednání| Virtuální počítač vyžaduje k nasazení licenční klíč poskytnutý zákazníkem. Společnost Microsoft si neúčtuje zákazníky, kteří si tyto nabídky na webu Marketplace dostanou.|
| Typ licence Marketplace: Free| Objednání| Tato nabídka je nakonfigurovaná tak, aby byla pro všechny uživatele volná. Společnost Microsoft neúčtuje zákazníkům za využití této nabídky.|
| Typ licence Marketplace: Microsoft jako prodejce  | Objednání| Microsoft je prodejcem této nabídky na webu Marketplace.|
| ID předplatného Marketplace | Zákazník, objednávka | Jedinečný identifikátor přidružený k předplatnému Azure, který zákazník použil k nákupu vaší nabídky na webu Marketplace. IDENTIFIKÁTORem byl dřív identifikátor GUID předplatného Azure.|
| Název nabídky  | Objednání| Název nabídky Marketplace.|
| Typ nabídky  | Objednání| Typ Microsoft Marketplace nabídky|
| Typ nabídky: spravovaná aplikace  | Objednání | Použití aplikace Azure: typ nabídky spravované aplikace, když jsou vyžadované následující podmínky: nasazení řešení založeného na předplatném pro zákazníka můžete nasadit buď pomocí virtuálního počítače, nebo celého řešení založeného na IaaS. Vy nebo váš zákazník požadujete, aby bylo řešení spravované partnerem. |
| Typ nabídky: aplikace Azure| Objednání | Pokud vaše řešení vyžaduje další automatizaci nasazení a konfigurace než jednoduchý virtuální počítač, použijte typ nabídky šablony řešení Azure Application.|
| Typ nabídky: konzultační služba| Objednání| Konzultační služby v Azure Marketplace pomůžou propojit zákazníky se službami pro podporu a rozšiřování jejich používání Azure.|
| Typ nabídky: kontejner | Objednání| Typ nabídky kontejneru použijte, pokud je vaše řešení image kontejneru Docker zřízená jako Kubernetes Azure Container Service.|
| Typ nabídky: Dynamics 365 Business Central| Objednání| Tento typ nabídky použijte, když se vaše řešení integruje s Dynamics 365 pro finance a operace.|
| Typ nabídky: Dynamics 365 pro zákaznickou zapojení | Objednání| Tento typ nabídky použijte, když se vaše řešení integruje s Dynamics 365 pro zapojení zákazníka.|
| Typ nabídky: modul IoT Edge | Objednání| Azure IoT Edge moduly jsou nejmenší výpočetní jednotky spravované IoT Edge a mohou obsahovat služby společnosti Microsoft (například Azure Stream Analytics), služby třetích stran nebo vlastní kód specifický pro řešení. |
| Typ nabídky: Power BI aplikace | Objednání| Pokud nasadíte aplikaci integrovanou s Power BI, použijte typ nabídky Power BI aplikace.|
| Typ nabídky: aplikace SaaS| Objednání| Pomocí typu nabídky aplikace SaaS můžete zákazníkům povolit, aby si koupili vaše technické řešení založené na SaaS jako předplatné.||
| Typ nabídky: virtuální počítač | Objednání | Typ nabídky virtuálního počítače použijte, když nasadíte virtuální zařízení do předplatného přidruženého k vašemu zákazníkovi.|
| Typ nabídky: rozšíření Visual Studio Marketplace  | Objednání| Typ nabídky, který jste dříve k dispozici vývojářům rozšíření Azure DevOps. Po přeposílání vývojářů rozšíření Azure DevOps můžou své rozšíření prodávat přímo zákazníkům. Nabídky rozšíření se dají nakonfigurovat jako placené nebo včetně zkušební verze. |
| Datum zrušení objednávky| Objednání| Datum, kdy se objednávka webu Marketplace zrušila|
| ID objednávky| Objednání| Jedinečný identifikátor objednávky zákazníka pro vaši službu Marketplace Nabídky založené na využití virtuálních počítačů nejsou přidruženy k objednávce.|
| Datum nákupu objednávky| Objednání| Datum vytvoření objednávky Marketplace.|
| Order Status| Objednání| Stav objednávky Marketplace v době poslední aktualizace dat.|
| Stav objednávky: aktivní  | Objednání| Zákazník zakoupil objednávku a nezrušil jejich pořadí.|
| Stav objednávky: zrušeno | Objednání| Zákazník si předtím koupil objednávku a následně zrušil jejich pořadí.|
| E-mail poskytovatele| Zákazník| E-mailová adresa poskytovatele zapojená v relaci mezi společností Microsoft a koncovým zákazníkem. Pokud zákazník je podnikem prostřednictvím prodejce, bude to prodejce. Pokud se jedná o poskytovatele Cloud Solution Provider (CSP), bude to CSP.|
| Název zprostředkovatele| Zákazník| Název zprostředkovatele zapojeného v relaci mezi společností Microsoft a koncovým zákazníkem. Pokud zákazník je podnikem prostřednictvím prodejce, bude to prodejce. Pokud se jedná o poskytovatele Cloud Solution Provider (CSP), bude to CSP.|
| SKU| Objednání| Název SKU (teď se označuje jako plán), jak je definovaný během publikování. Nabídka může mít mnoho SKU (plánů), ale každá může být přidružená jenom k jedné nabídce.|
| Datum ukončení zkušební verze| Objednání| Datum ukončení zkušebního období pro tuto objednávku skončí nebo skončí.|
|||

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na obchodním tržišti partnerského centra najdete v tématu [analýzy pro komerční tržiště v partnerském centru](./analytics.md).
- Grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaši nabídku, najdete v tématu [souhrnný řídicí panel v části komerční analýza na webu Marketplace](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu věnovaném [řídicímu panelu objednávky v rámci komerčních tržišť](./orders-dashboard.md).
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](./usage-dashboard.md).
- Podrobné informace o vašich zákaznících, včetně trendů růstu, najdete [v tématu řídicí panel zákazníka v analytickém obchodě na komerčním webu](./customer-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a AppSource, přečtěte si téma [hodnocení a recenze řídicího panelu na komerčních analýzách na webu Marketplace](./ratings-reviews.md).
