---
title: Příprava a otestovat vaši nabídku pro nasazení do Azure Marketplace | Microsoft Docs
description: Podrobné pokyny k poskytnete marketingové obsah, konfigurace cenovou plány a testování vaši nabídku před nasazením do Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: mbaldwin
ms.openlocfilehash: 5bec9ac89487a824b9de9209547f0b96fa5fe678
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29947266"
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Vytvoření nabídky s obsahem, marketing
V tomto kroku procesu publikování musíte poskytnout určité marketing obsah a podrobnosti o nabídka nebo jednotky SKU v Azure Marketplace. Bude například zadat popis produkt společnosti, loga společnosti, cena plány, podrobnosti o plány a další informace, které jsou potřeba nabízená nabídka nebo SKU pracovní. Tyto informace slouží jako marketing obsah na webu Azure portal. Začnete tohoto procesu [publikování portál][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Krok 1: Zadejte Marketplace marketingové obsahu
**Angličtina je výchozí a pouze podporovaný jazyk.** Ujistěte se, že všechny informace uvedené v pole jsou v angličtině. Než přejdete k přípravám, dají se všechny informace kdykoli upravit.

1. Přejděte na portálu publikování [ https://publish.windowsazure.com ](https://publish.windowsazure.com).
2. V nabídce vlevo klikněte **Marketing** kartě.
3. Na hlavním panelu klikněte **angličtinu (US)** tlačítko.
   
   > [!IMPORTANT]
   > Všechna pole musí mít položky, včetně pro obrázky, abyste mohli k pracovní.
   > 
   > 

### <a name="details-and-plans"></a>Podrobnosti a plány
1. Zadejte název nabídky (maximálně 50 znaků), nabízejí souhrn (maximálně 100 znaků), nabízejí dlouhý souhrn (maximálně 256 znaků), nabízejí popis (maximální počet znaků 1300), loga pod **podrobnosti** karta
2. Zadejte název plánu (maximálně 50 znaků), plán souhrn (maximálně 100 znaků), naplánujte popis (maximální počet znaků 2000) v části **plány** kartě.
   
   > [!NOTE]
   > Můžete použít následující značky HTML k formátování souhrnu dlouhé shrnutí a popis nabídky a plány. Povolené značky HTML jsou h1, h2, h3, h4, h5, p, ol, ul, li, [cíl | href] silná, em, b, i.
   > 
   > 
3. Nezadávejte duplicitní text v části Popis nabídky a plánování.
4. Nezadávejte duplicitní textu v nadpisu a nabídka dlouhé shrnutí plánu.
5. Nezadávejte zadejte duplicitní text v části název plánu a nabízejí souhrn.
6. Nezadávejte názvy identické plán pro nabídku s více schématy.
7. Nahrajte obrázky požadovaným specifikacím (jak je uvedeno na portálu publikování) ve formátu PNG, jeden pro každou velikost.
8. Ujistěte se, že loga, postupujte podle pokynů logo Azure Marketplace uvedených níže.
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Logu Azure Marketplace**

Postupujte podle všech loga publikování portál nahrát následující pokyny:

* Azure návrh obsahuje paletu barev jednoduché. Zachovat počet primární a sekundární barvy na vaše logo nízkou.
* Barev motivů na portálu Azure jsou bílé a černé. Proto Vyhněte se používání těchto barvy jako barvu pozadí vašeho loga. Pomocí některé barvu, která by zkontrolujte, že vaše loga viditelného na portálu Azure. Doporučujeme, abyste jednoduché primární barvy. **Pokud používáte průhledné pozadí, ujistěte se, že nejsou loga nebo textu bílé nebo černé nebo modré.**
* Nepoužívejte logo barevného přechodu pozadí.
* Vyhněte se umístění na logo text, i vaše společnost nebo název značky. Vzhled a chování vaše logo musí být 'ploché' a byste neměli přechody.
* Logo nesmí dojít k roztažení.
* Malé logo musí mít velikost 40 X 40 px
* Střední logo musí mít velikost 90 X 90 px
* Velké logo musí mít velikost 115 X 115 px
* Široké logo musí mít velikost 255 X 115 px
* Nejdůležitější logo musí mít velikost 815 X 290 px

> [!NOTE]
> Logo nejdůležitější je volitelný. Vydavatele můžete vypnout nahrávat logo nejdůležitější. Ale po nahrané ikonu hrdina nelze odstranit z publikování portálu. V ten moment se musí partnerovi postupujte podle pokynů Azure Marketplace pro nejdůležitější ikony.
> 
> 

  ![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Další pokyny pro ikonu hrdina logo (volitelné)**

* Logo nejdůležitější je volitelný. Vydavatele můžete vypnout nahrávat logo nejdůležitější. **Ale po nahrané ikonu hrdina nelze odstranit z publikování portálu. V ten moment se musí partnerovi splňovat pokyny Azure Marketplace pro nejdůležitější ikony else nebude možné ji schválit nabídku do produkčního prostředí.**
* Zobrazovaný název vydavatele, název plánu a nabídky dlouhé shrnutí se zobrazují v barva bílé písma. Proto byste neměli udržuje všechny světla barva na pozadí na ikonu nejdůležitější. Černé, bílé a průhledné pozadí není povolena pro nejdůležitější ikony.
* Zobrazovaný název vydavatele, plán název, dlouhé shrnutí nabídku a tlačítko pro vytvoření jsou vloženému prostřednictvím kódu programu uvnitř logo hrdina po nabídku přejde uvedené. Při návrhu logo nejdůležitější, proto by neměl zadat libovolný text. Nechte prázdné místo na pravé straně, protože textu (tj. zobrazovaný název vydavatele, název plánu dlouhé shrnutí nabídka) budou zahrnuty prostřednictvím kódu programu tím, abychom přes zde. Prázdné místo pro text by mělo být 415 × 100 na pravé straně (a posunut 370px od levého okraje).
  
  ![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Odkazy
Na **odkazy** na levém panelu, zadejte jakékoli odkazy s informacemi, které pomáhají zákazníkům. Zadejte název a URL pro každý odkaz.

![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Ukázkové obrázky (volitelné)
> [!NOTE]
> Včetně ukázkového obrázku je volitelný krok.
> I když můžete nahrát více bitových kopií ukázka v publikační portálu, získá zobrazí pouze jeden obrázek (náhodně vybrány v systému) na portálu Azure. Z tohoto důvodu doporučujeme odesílání maximálně jeden Ukázkový obrázek.
> 
> 

Na **ukázkové obrázky** klikněte v nabídce vlevo, kliknutím na nahrát novou bitovou kopii **nahrát novou bitovou kopii**. Pokud máte stávající image a chcete ho nahradit, klikněte na tlačítko **nahraďte image**.

![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Právní informace
Na **právní** kartě, zadejte odkaz na vaše zásady nebo podmínky použití. Zadejte nebo vložte podmínky v velkých **podmínky použití** pole. Omezení počtu znaků pro právní podmínky použití je 1 000 000 znaků.

![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Poznámka:** pro virtuální počítač nabízí po nabídka nebo SKU je připravené na portálu Azure, nelze změnit daných polí níže:

* **Nabídka identifikátor:** [publikování portál -> virtuální počítače -> vaši nabídku bitové kopie virtuálního počítače -> karta -> nabízejí identifikátor]
* **Identifikátor SKU:** [publikování portál -> virtuální počítače -> vyberte vaši nabídku -> Přidat -> karta SKU SKU]
* **Vydavatel Namespace:** [publikování portál -> virtuální počítače -> karta -> sdělte nám o vaší společnosti (najít v části "Krok 2 zaregistrovat vaše společnost") -> návod Namespace-Namespace vydavatele >]

Pro virtuální počítač nabízí po nabídka skladová položka je uvedena v Azure Marketplace, nelze změnit daných polí níže:

* **Nabídka identifikátor:** [publikování portál -> virtuální počítače -> vyberte vaši nabídku -> bitové kopie virtuálního počítače -> nabízejí identifikátor]
* **Identifikátor SKU:** [publikování portál -> virtuální počítače -> vyberte vaši nabídku -> Přidat -> karta SKU SKU]
* **Vydavatel Namespace:** [publikování portál -> virtuální počítače -> návod -> karta Namespace vydavatele Řekněte nám o vaše společnost (najít v části Krok 2 zaregistrovat) -> Namespace]
* **Porty:** [publikování portál -> virtuální počítače -> vaši nabídku bitové kopie virtuálního počítače -> karta -> otevřené porty]
* **Změna seznamu SKU(s) – ceny**
* **Změna modelu uvedené SKU(s) fakturace**
* **Odebrání fakturace oblasti uvedené SKU(s)**
* **Změna počet datových disků z uvedených SKU(s)**

## <a name="step-2-set-your-prices"></a>Krok 2: Nastavení vaší ceny
### <a name="pricing-models"></a>Cenových úrovních
| Cenový model | Popis |
| --- | --- |
| Základní |Ploché měsíční míra placené při nákupu; například 10 za měsíc. |
| Spotřeba (také známa jako využití, měřidla) |Platba za použití, která je definována vydavatelem nabídku. Nadlimitní události úrovně nemůže být definovaný na pracovní stanici, každý uživatel, atd., protože neexistuje žádná koncepce zlomek uživatele nebo schopnost provést proration. Využití je hodinu hlášených partnera. Zákazník platí v systému měsíční fakturačního cyklu, a budete například měsíční plány. |
| Bezplatná zkušební verze |Zákazník může používat bezplatné po omezenou dobu a pak po tomto datu platit normální sazby. |
| Úroveň Free |Plán je vždy zdarma. |
| Migrace (také známa jako převod nebo upgradu nebo přechod na starší verzi) plánu |Koncept uživatele přesunutí ze své stávající plán jiného přijatelné plánu; definované partnera. |

**Cenových úrovních podle typu nabídky**

> [!IMPORTANT]
> Dostupnost určité cenovou modely se liší podle typu nabídky. Viz následující tabulka.
> 
> 

|  | Základní pouze | Pouze spotřeba | Základní + spotřeba |
| --- | --- | --- | --- |
| Image virtuálního počítače |Ne |Ano |Ne |
| Služba pro vývojáře |Ano |Ano |Ano |

### <a name="21-set-your-vm-prices"></a>2.1. Nastavit vaše ceny virtuálních počítačů
V současné době pro virtuální počítače, máme následující **3 typy modely fakturace:**

* **HODINOVĚ:** zákazníkům získat účtovat na základě za hodinu podle sazby nastavit vydavatelé na velikosti virtuálních počítačů. Pro **každou hodinu fakturace** modelu o SKU, bude celková cena souhrn účtovat vydavatelem softwaru náklady a náklady na infrastrukturu účtovat společností Microsoft. Celkové náklady na tento zobrazí se zákazník jako hodinové a měsíc cena při nákupu zvažují (viz obrázek). **Vydavatel obdrží 80 % náklady na software poplatky z nich.** Proto prosím zkontrolujte odpovídajícím způsobem před nastavení výpočet ceny pro vaše SKU.
  
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Bezplatná zkušební verze:** Toto je jiný příchuť hodinové modelu. Zde není získat zákazník účtovat náklady na software pro prvních 30 days(Free) po nasazení virtuálního počítače. Po 30days získat účtovat na základě za hodinu podle sazby nastavit vydavatelé v hodinové modelu.
* **Převést – vaše – vlastní – licence (BYOL):** daných vydavatelů spravovat licencování softwaru spuštěné ve virtuálním počítači.

**Důležité:** po nabídka skladová položka je uvedena v Azure Marketplace, nelze změnit daných polí níže.

* **Změna seznamu SKU(s) – ceny**
* **Změna modelu uvedené SKU(s) fakturace**
* **Odebrání fakturace oblasti uvedené SKU(s)**
* **Změna počet datových disků z uvedených SKU(s)**
* **Nabídka identifikátor:** [publikování portál -> virtuální počítače -> vyberte vaši nabídku -> bitové kopie virtuálního počítače -> nabízejí identifikátor]
* **Identifikátor SKU:** [publikování portál -> virtuální počítače -> vyberte vaši nabídku -> Přidat -> karta SKU SKU]
* **Vydavatel Namespace:** [publikování portál -> virtuální počítače -> návod -> karta Namespace vydavatele Řekněte nám o vaše společnost (najít v části Krok 2 zaregistrovat) -> Namespace]
* **Porty:** [publikování portál -> virtuální počítače -> vaši nabídku bitové kopie virtuálního počítače -> karta -> otevřené porty]

### <a name="sell-to-countries-of-the-sku"></a>Zemích "Zákazníkovi" verze SKU
Je třeba pečlivě zvážit, kde můžete zpřístupnit vaše SKU. V některých zemích jsou klasifikovány jako "Poslání Microsoft" a jiné jsou klasifikovány jako "ISV poslání."

* V zemích "Poslání Microsoft" společnost Microsoft shromažďuje daně od zákazníků a platí daně (vykazovány) k vláda.
* V "ISV uhradit" zemích jsou zodpovědní za shromažďování zákazníkům daně a platícího daně pro vláda partnery. Pokud si zvolíte, aby je prodal v "ISV uhradit" zemích, musí mít možnost výpočtu a platit daně v zemích, kterou vyberete.

> [!NOTE]
> Vaše SKU nebudete mít k dispozici v zemích, pokud nastavíte v jejich cenovou [publikování portál](https://publish.windowsazure.com). Pokyny k získání nastavení ceny každou hodinu a BYOL SKU je uveden níže.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 postup nastavení hodinové cenový model pro SKU
Postupujte podle kroků uvedených níže na každou hodinu cenový model pro SKU instalaci:

1. Přihlášení k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce vlevo straně, klikněte na tlačítko **SKU** kartě.
4. Ujistěte se, že verze SKU je označena jako "Hodinové Model fakturace". Pokud ne, potom klikněte na **upravit** tlačítko vrátit fakturační model. Otevře se okno. Zrušte zaškrtnutí políčka 'fakturace a licencování se provádí externě z Azure (neboli model použití vlastní licence) a uložte změny.
5. Pokud chcete povolit bezplatné zkušební verze pro první 30days SKU nasazení, pak vyberte možnost "Jeden měsíc" pro otázku "Je bezplatnou zkušební verzi k dispozici?" Jinak vyberte možnost "Ne zkušební verze". Nyní postupujte podle kroků uvedených níže.
6. Na straně nabídce vlevo klikněte na **cenová** kartě.
7. Vyberte základní oblast.
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Nastavte ceny pro všechny jader. **Je nutné zadat ceny pro všechna jádra SKU, i v případě, že vaše SKU je nepodporuje.**
   
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Ceny pro jiné oblasti nastavit ručně nebo můžete použít Průvodce AUTOPRICE nastavit tyto ceny jiných oblastí, na základě základní oblasti. Chcete-li použít AUTOPRICE průvodce kliknutím na tlačítko **AUTOPRICE ostatní trhů na základě ON ceny IN Spojené státy.** **Poznámka:** popisek tlačítka se můžou lišit v závislosti na oblasti, které jste vybrali. Vzhledem k tomu, že při vytváření tohoto dokumentu jsme vybrali Spojených státech amerických, proto tlačítko označené jako "Automaticky ceny jiných trzích podle ceny ve Spojených státech amerických" na tomto snímku obrazovky.
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Otevře se průvodce automaticky ceny. Na první stránce zobrazuje výběr základní trhu. Zkontrolujte vaše části a přesun na další stránku kliknutím na tlačítko "->".
    
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. Možnost pro výběr jader a plány se zobrazí na stránce 2. Vyberte požadované plány a klikněte na tlačítko "->" tlačítko. Klikněte na tlačítko **přepnutí všechny** tlačítko Vybrat vše **služby plány** a **měřidla** nebo abyste mohli ručně zkontrolovat políček. **Je nutné zadat ceny pro všechna jádra SKU, i v případě, že vaše SKU je nepodporuje.** Proto zkontrolujte, jestli jsou vybrané všechny základní velikosti.
    
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. Stránka 3 zobrazí trhů nebo oblasti. Klikněte **přepnutí všechny** tlačítko a vyberte všechny oblasti nebo ručně zaškrtněte políčka pro oblast. Klikněte na tlačítko "->" pro přesun na další stránku. **Poznámka:** Microsoft daň poskytována zemích jsou rozlišeny pomocí úklidové symbolu. Další podrobnosti naleznete v části "Zákazníkovi" zemí verze SKU části této stránky.
    
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. Stránka 4 zobrazí kurzů. Klikněte na tlačítko Dokončit proveďte kroky.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 postup nastavení BYOL cenový model pro SKU
Postupujte podle kroků uvedených níže BYOL cenový model pro SKU nastavení:

1. Přihlášení k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce vlevo straně, klikněte na tlačítko **SKU** kartě.
4. Ujistěte se, že verze SKU je označena jako "Přineste si vlastní licenci SKU". V opačném případě klikněte na tlačítko Upravit vrátit fakturační model. Otevře se okno. Zaškrtněte políčko, fakturace a licencování se provádí externě z Azure (neboli model použití vlastní licence) a uložte změny.
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. Na straně nabídce vlevo klikněte na **cenová** kartě.
6. Vyberte základní oblast a zpřístupnit verze SKU v oblasti zaškrtnutím políčka proti SKU části dostupnosti SKU EXTERNALLY-LICENSED (BYOL) (viz obrázek).
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Zpřístupnit verze SKU v jiných oblastech ručně nebo můžete použít Průvodce AUTOPRICE pro tento účel. Odkazovat bodů #9 až &#13; (které popisuje použití Průvodce AUTOPRICE) v části **"2.1.1 postup nastavení každou hodinu cenový model pro SKU"** části této stránky.

### <a name="22-set-your-developer-service-prices"></a>2.2. Nastavit vaše ceny služby vývojáře
Plány může být libovolnou kombinací základní + využívání, kde základní je měsíční poplatek a Nadlimitní je cena platím za použití. (Viz níže podrobnosti.)

**Příklad:** nabídky služeb pro vývojáře společnosti Contoso

| Plánování | Cena | Zahrnuje | Cesty migrace |
| --- | --- | --- | --- |
| Free |$0 za měsíc |Základní funkce. |Můžete migrovat do jiných plán |
| Bronzová |10 za měsíc |Základní funkce a kvótu 1000 funkce X. |Můžete migrovat do bronzová Plus, stříbrný a zlatý plánů |
| Bronzovým Plus |Bezplatné zkušební období: $0/ měsíc + $0/meter01 |Základní funkce a kvótu 10 000 funkce X.  Jakmile funkci X kvóta se použije, může zákazník platba za použití prostřednictvím meter01. |Můžete migrovat do Silver Plus a zlatý plánů |
| Bronzovým Plus |Placené období (také známa jako platnost bezplatné zkušební verze): $10/měsíc + $ hodnotu 0,05/meter01 |Základní funkce a kvótu 10 000 funkce X.  Jakmile funkci X kvóta se použije, může zákazník platba za použití prostřednictvím meter01. |Můžete migrovat do Silver Plus a zlatý plánů |
| Stříbrný |$ 0,15/meter01 |Zákazník může platit za použití prostřednictvím meter01, což je pro funkci X. |Můžete migrovat do bronzová a zlatý plánů |
| Stříbrná Plus |měsíc 20 + $ 0,15/meter01 + $ 0,01/meter02 |Základní funkce a kvótu 10 000 funkce X a 100 funkce Y.  Jakmile funkci X kvóta se použije, může zákazník platba za použití prostřednictvím meter01.  Jakmile funkci Y kvóta se použije, může zákazník platba za použití prostřednictvím meter02. |Můžete migrovat do bronzová Plus a zlatý plánů |
| Zlatá |1 000 USD za měsíc |Kvóta 10 000 X 1 000 funkce Y, funkce a funkce Z neomezená. |Můžete migrovat všechny plány, s výjimkou volné |

## <a name="step-3-provide-support-information"></a>Krok 3: Zadání podporu informace
Kontaktní údaje se používají pro interní komunikaci mezi partnery a Microsoft jenom. Adresa URL podpory bude k dispozici koncovým zákazníkům.

1. Přejděte na **podporu** záhlaví na levé straně publikování portálu.
2. Zadejte informace v části **technický kontakt**.
3. Zadejte informace v části **zákaznickou podporu**. Pokud zadáte pouze e-mailová podpora, zadejte fiktivní telefonní číslo a zadané e-mailu se použije místo.
4. Zadejte adresu URL podpory.

## <a name="step-4-choose-azure-marketplace-categories"></a>Krok 4: Vyberte kategorie Azure Marketplace
**Kategorie** karta poskytuje řadu výběry. Vaši nabídku může spadají pod tyto a můžete si vybrat až pěti kategorií.

## <a name="how-your-marketing-will-appear"></a>Jak se zobrazí vaše marketing
Zde je podrobný přehled o způsobu použití nabídku marketingové informace na [webu Azure Marketplace](https://azure.microsoft.com/marketplace/) a v [portál Azure](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Webu Azure Marketplace.
![Kreslení](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![Kreslení](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Seznam všech nabídek na webu Azure Marketplace*

![Kreslení](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Popis informace na webu Azure Marketplace*

![Kreslení](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Nabízejí popis podrobnosti o cenách na webu Azure Marketplace*

### <a name="azure-portal"></a>Azure Portal
![Kreslení](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Seznam všech nabídek na portálu Azure*

![Kreslení](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Podrobnosti popis nabídky na portálu Azure*

## <a name="next-steps"></a>Další kroky
Teď, když obsah Marketplace je načtena, umožňuje přesun při testování vaši nabídku při přípravě. Ale je nutné vybrat typ odpovídající nabídka ze seznamu níže, jako kroky se liší podle typu nabídky.

* [Otestovat vaši nabídku virtuálních počítačů v pracovní](marketplace-publishing-vm-image-test-in-staging.md)
* [Otestovat vaši nabídku šablony řešení v pracovní](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: postup publikování nabídky pro Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
