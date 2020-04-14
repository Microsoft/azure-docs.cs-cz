---
title: Nejčastější dotazy a terminologie pro analýzu komerčního trhu v Partnerském centru
description: Přečtěte si, jak řešit nejčastější dotazy týkající se analýzy komerčního tržiště. Obsahuje datový slovník pro terminologii analýzy.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: b7367e58de818c20723c02a6763b1bf1e3b18f24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251822"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>Nejčastější dotazy a terminologie pro analýzu komerčního trhu

Tento článek se zabývá běžně kladenými dotazy týkajícími se analytických zpráv v Partnerském centru a také obsahuje slovník terminologie analýzy.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tato část obsahuje odpovědi na nejčastější dotazy týkající se **zpráv No Analytics Available Yet** v Centru partnerů.

**V Partnerském centru nelze zobrazit analytická data. Když přistupuji k těmto stránkám, zobrazí se níže uvedená zpráva. Proč?**

![Zatím žádná data pro vaše nabídky](./media/analytics-faq-no-data.png)

Proč se vám může dostat tuto zprávu:

- Pro publikované nabídky na tržišti v současné době neexistují žádné akvizice. To může znamenat, že vaše nabídky jsou aktivní na marketplace a získávají zobrazení od zákazníků na stránkách zobrazení produktu, ale zákazníci ještě nepodnikli kroky k jejich nákupu a nasazení.
- Publikování vaší nabídky může být v procesu a ještě není živé. Zákazníci mohou získat pouze živé nabídky. Chcete-li zkontrolovat stav nabídek, přečtěte si část Přehled na [řídicím panelu Analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Další informace najdete [v tématu Summary Dashboard v analýzách komerčního tržiště](./summary-dashboard.md).
- Vaše nabídky mohou být uvedeny jako **Služby Kontaktujte mě**, což jsou nabídky pouze pro seznam a zákazníci na trhu je nemohou zakoupit. I když tyto nabídky generují zájemce a jsou s vámi sdíleny, objednávky se pro tyto nabídky nevytvářejí, protože je nelze zakoupit. Chcete-li zkontrolovat typ nabídky, přejděte na stránku nastavení.

**Vím, že mám analytická data, ale zobrazuje se níže uvedená zpráva:**

![Pro dané časové období nejsou k dispozici žádná data.](./media/analytics-faq-data-range.png)

Pokud se zobrazí tato zpráva, znamená to, že máte analytická data, ale pro vybrané období nejsou k dispozici žádná data. Vyberte jiné časové období nebo vlastní rozsah dat, chcete-li zobrazit všechna data od roku 2010. Další informace naleznete v části Rozsah dat [v souhrnném řídicím panelu v analýze komerčního tržiště](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Slovník datových termínů

| Název atributu | Sestavy | Definice|
|---|---|---|
| Typ licence Azure | Zákazník, Objednávka | Typ licenční smlouvy, kterou zákazníci používají k nákupu Azure. Také známý jako kanál |
| Typ licence Azure: Poskytovatel cloudových řešení | Zákazník, Objednávka | Koncový zákazník pořizuje Azure a vaši nabídku marketplace prostřednictvím svého poskytovatele cloudových řešení, který jedná jako váš prodejce.|
| Typ licence Azure: Enterprise | Zákazník, Objednávka | Koncový zákazník pořizuje Azure a vaši nabídku marketplace prostřednictvím smlouvy Enterprise, podepsané přímo se společností Microsoft.|
| Typ licence Azure: Enterprise prostřednictvím prodejce  | Zákazník, Objednávka | Koncový zákazník pořizuje Azure a vaši nabídku marketplace prostřednictvím prodejce, který usnadňuje jejich smlouvu Enterprise s Microsoftem.|  |
| Typ licence Azure: Plaťte průběžně| Zákazník, Objednávka | Koncový zákazník pořizuje Azure a vaši nabídku marketplace prostřednictvím smlouvy "Pay as You Go", podepsané přímo se společností Microsoft.||
| Název instance cloudu| Objednání| Microsoft Cloud, ve kterém došlo k nasazení virtuálního počítače.||
| Název instance cloudu: Azure Global| Objednání| Veřejný globální cloud Microsoftu.|| |
| Název instance cloudu: Azure Government | Objednání| Microsoft pro jednu z následujících vlád: Čína, Německo nebo Spojené státy americké.| |
| Město zákazníka| Zákazník| Název města poskytnutý zákazníkem. Město se může lišit od města v předplatném Azure zákazníka.||
| Jazyk komunikace se zákazníky  | Zákazník| Jazyk preferovaný zákazníkem pro komunikaci.||
| Název společnosti odběratele | Zákazník, Objednávka | Název společnosti poskytnutý zákazníkem. Název se může lišit od města v předplatném Azure zákazníka.|  |
| Země zákazníka | Zákazník, Objednávka | Název země poskytnutý zákazníkem. Země se může lišit od země v předplatném Azure zákazníka.|  |
| E-mail pro zákazníky| Zákazník| E-mailová adresa poskytnutá koncovým zákazníkem. E-mail se může lišit od e-mailové adresy v předplatném Azure zákazníka.||
| Jméno zákazníka| Zákazník| Jméno poskytnuté zákazníkem. Název se může lišit od názvu uvedeného v předplatném Azure zákazníka.| |
| ID zákazníka | Zákazník, Objednávka | Jedinečný identifikátor přiřazený zákazníkovi. Zákazník může mít nula nebo více předplatných Azure Marketplace.|  |
| PSČ zákazníka  | Zákazník| PSČ poskytnuté zákazníkem. Kód se může lišit od PSČ uvedeného v předplatném Azure zákazníka.| |
| Stát zákazníka| Zákazník| Stát (adresa) poskytnutý zákazníkem. Stát může být jiný než stav poskytovaný v předplatném Azure zákazníka.| |
| Datum nabytí| Zákazník| První datum, kdy zákazník zakoupil jakoukoli vyznavatelku.| |
| Datum ztráty| Zákazník| Poslední datum, kdy zákazník zrušil poslední ze všech dříve zakoupených nabídek.||
| Je nový zákazník  | Objednání| Hodnota identifikuje nového zákazníka, který poprvé získá jednu nebo více vašich nabídek (nebo ne). Hodnota bude "Ano", pokud ve stejném kalendářním měsíci pro "Datum získání". Hodnota bude "Ne", pokud zákazník zakoupil některou z vašich nabídek před nahlášeným kalendářním měsícem. |
| Je náhled SKU| Objednání| Hodnota vás upozorní, pokud jste označili skladovou položku jako "náhled". Hodnota bude "Ano", pokud skladová položka byla označena odpovídajícím způsobem a pouze předplatná Azure autorizovaná vámi můžete nasadit a použít tuto bitovou kopii. Hodnota bude "Ne", pokud skladová položka nebyla identifikována jako "náhled".  |
| Je propagační kontakt Opt In| Zákazník| Hodnota vás upozorní, pokud se zákazník aktivně přihlásil k propagačnímu kontaktu od vydavatelů. V tuto chvíli nepředstavujeme možnost zákazníkům, takže jsme uvedli "ne" plošně. Po nasazení této funkce začneme odpovídajícím způsobem aktualizovat.|
| Typ licence Marketplace| Objednání| Způsob fakturace nabídky Marketplace.||
| Typ licence Marketplace: Fakturovaný prostřednictvím Azure| Objednání| Společnost Microsoft je vaším zástupcem pro tuto nabídku Marketplace a účtuje zákazníkům vaším jménem. (Platební karta PAYG nebo podniková faktura)||
| Typ licence Marketplace: Přineste si vlastní licenci | Objednání| Virtuální ho virtuálního uživatele vyžaduje licenční klíč poskytnutý zákazníkem k nasazení. Společnost Microsoft neúčtuje zákazníkům, že tímto způsobem uvádějí své nabídky na trhu.||
| Typ licence Marketplace: Zdarma| Objednání| Nabídka je konfigurována tak, aby byla zdarma pro všechny uživatele. Společnost Microsoft neúčtuje zákazníkům za jejich použití této nabídky.||
| Typ licence Marketplace: Microsoft jako prodejce  | Objednání| Společnost Microsoft je vaším prodejcem pro tuto nabídku Marketplace.|  |
| ID předplatného Marketplace | Zákazník, Objednávka | Jedinečný identifikátor přidružený k předplatnému Azure, které zákazník použil k nákupu vaší nabídky marketplace. ID bylo dříve identifikátor GUID předplatného Azure.||
| Název nabídky  | Objednání| Název nabídky Marketplace.|| |
| Typ nabídky  | Objednání| Typ nabídky Microsoft Marketplace.|||
| Typ nabídky: Spravovaná aplikace  | Objednávky | Použití aplikace Azure: typ nabídky spravované aplikace, když jsou vyžadovány následující podmínky: Nasadíte buď řešení založené na předplatném pro zákazníka pomocí virtuálního počítače nebo celého řešení založeného na IaaS. Vy nebo váš zákazník požadujete, aby řešení bylo řízeno partnerem. |
| Typ nabídky: Aplikace Azure| Objednávky | Typ nabídky šablony řešení Azure, když vaše řešení vyžaduje další nasazení a automatizaci konfigurace nad rámec jednoduchého virtuálního počítače.||
| Typ nabídky: Konzultační servis| Objednání| Poradenské služby na Azure Marketplace pomáhají propojit zákazníky se službami a podporovat a rozšiřovat jejich používání Azure.| |
| Typ nabídky: Kontejner | Objednání| Typ nabídky Container použijte, když vaše řešení je image kontejneru Dockeru zřízená jako služba kontejneru Azure založená na Kubernetes.||
| Typ nabídky: Dynamics 365 Business Central| Objednání| Tento typ nabídky použijte, pokud je vaše řešení integrováno s dynamics 365 pro finance a provoz| |
| Typ nabídky: Dynamics 365 pro zapojení zákazníků | Objednání| Tento typ nabídky použijte, pokud je vaše řešení integrováno s dynamics 365 pro customer engagement.||
| Typ nabídky: Modul IoT Edge | Objednání| Moduly Azure IoT Edge jsou nejmenší výpočetní jednotky spravované IoT Edge a mohou obsahovat služby Microsoftu (například Azure Stream Analytics), služby třetích stran nebo vlastní kód specifický pro řešení. |
| Typ nabídky: Aplikace Power BI | Objednání| Typ nabídky aplikace Power BI použijte při nasazení aplikace integrované s Power BI.|  |
| Typ nabídky: Aplikace SaaS| Objednání| Pomocí typu nabídky aplikace SaaS umožníte zákazníkovi zakoupit si technické řešení založené na SaaS jako předplatné.||
| Typ nabídky: Virtuální počítač | Objednání| Typ nabídky virtuálního počítače použijte při nasazení virtuálního zařízení do předplatného přidruženého k zákazníkovi.||
| Typ nabídky: Rozšíření Visual Studio Marketplace  | Objednání| Typ nabídky dříve k dispozici vývojářům rozšíření Azure DevOps. Do budoucna azure devops rozšíření vývojáři můžete prodávat své rozšíření přímo zákazníkům. Nabídky rozšíření lze nakonfigurovat jako placené nebo včetně zkušební verze. |
| Datum zrušení objednávky| Objednání| Datum zrušení objednávky marketplace.||
| ID objednávky| Objednání| Jedinečný identifikátor objednávky zákazníka pro vaši službu Marketplace. Nabídky založené na využití virtuálního počítače nejsou přidruženy k objednávce.| |
| Datum nákupu objednávky| Objednání| Datum vytvoření marketplace objednávky.|||
| Order Status| Objednání| Stav objednávky Marketplace v době poslední aktualizace dat.|     |
| Stav objednávky: Aktivní  | Objednání| Zákazník si objednávku zakoupil a nezrušil její objednávku.|         |
| Stav objednávky: zrušeno | Objednání| Zákazník již dříve zakoupil objednávku a následně její objednávku zrušil.||
| E-mail poskytovatele| Zákazník| E-mailová adresa poskytovatele zapojeného do vztahu mezi společností Microsoft a koncovým zákazníkem. Pokud je zákazníkem podnik prostřednictvím prodejce, bude to prodejce. Pokud se jedná o poskytovatele cloudových řešení (CSP), bude to zprostředkovatel csp.|
| Název zprostředkovatele| Zákazník| Název zprostředkovatele zapojeného do vztahu mezi společností Microsoft a koncovým zákazníkem. Pokud je zákazníkem podnik prostřednictvím prodejce, bude to prodejce. Pokud se jedná o poskytovatele cloudových řešení (CSP), bude to zprostředkovatel csp.|
| Skladová jednotka (SKU)| Objednání| Název skladové položky definovaný během publikování. Nabídka může mít mnoho skladových položk, ale skladová položka může být přidružena pouze k jedné nabídce.||
| Datum ukončení zkušební verze| Objednání| Datum ukončení nebo ukončení zkušebního období pro tuto objednávku.||

## <a name="next-steps"></a>Další kroky

- Přehled analytických přehledů dostupných na komerčním trhu Partnerského centra najdete v [článku Analýza pro komerční tržiště v Centru partnerů](./analytics.md).
- Grafy, trendy a hodnoty souhrnných dat, které shrnují aktivitu na marketplace pro vaši nabídku, najdete [v tématu Souhrnný řídicí panel v analýze komerčního tržiště](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a formátu ke stažení najdete [v tématu Řídicí panel objednávek v analýze komerčního tržiště](./orders-dashboard.md).
- Pro virtuální počítač (VM) nabízí využití a účtované metriky účtované podle objemu dat, najdete [v tématu Využití Řídicí panel v komerčním tržišti analýzy](./usage-dashboard.md).
- Podrobné informace o zákaznících, včetně trendů růstu, najdete [v tématu Řídicí panel zákazníků v analýzách komerčního tržiště](./customer-dashboard.md).
- Seznam žádostí o stažení za posledních 30 dní najdete [v tématu Řídicí panel ke stažení v analýze komerčního tržiště](./downloads-dashboard.md).
- Konsolidované zobrazení zpětné vazby od zákazníků u nabídek na Azure Marketplace a AppSource najdete [v tématu Hodnocení a recenze v analýze komerčního webu](./ratings-reviews.md).
