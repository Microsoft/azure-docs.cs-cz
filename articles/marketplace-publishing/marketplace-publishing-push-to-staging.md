---
title: Připravit a otestovat vaši nabídku pro nasazení na webu Azure Marketplace | Dokumentace Microsoftu
description: Podrobné pokyny k poskytování marketingové obsahu, konfigurace věnovaného cenovým plánům a testování vaši nabídku před nasazením na Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714955"
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Dokončení vytvoření nabídky s marketingového obsahu
V tomto kroku se proces publikování je potřeba poskytnout určité marketingový obsah a podrobnosti o vaší nabídky a skladové položky na webu Azure Marketplace. Bude třeba, zadejte popis váš produkt, loga společnosti, cenové plány, podrobnosti o plány a další informace potřebné k převedení vaší nabídky a skladové položky do přípravného prostředí. Tyto informace slouží jako marketingový obsah na webu Azure Portal. Tento proces se začne [portálu pro publikování][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Krok 1: Zadejte Marketplace marketingové obsahu
**Angličtina je výchozí a jediný podporovaný jazyk.** Ujistěte se prosím, že všechny informace, které zadáte do polí musí být anglicky. Než přejdete k přípravám, dají se všechny informace kdykoli upravit.

1. Přejděte na portálu publikování [ https://publish.windowsazure.com ](https://publish.windowsazure.com).
2. V nabídce vlevo klikněte na tlačítko **marketingové** kartu.
3. Na hlavním panelu klikněte na tlačítko **angličtinu (US)** tlačítko.
   
   > [!IMPORTANT]
   > Všechna pole musí mít položky, včetně obrázků, pro, abyste mohli vložit do přípravného prostředí.
   > 
   > 

### <a name="details-and-plans"></a>Podrobnosti a plány
1. Zadejte název nabídky (maximálně 50 znaků), nabízejí souhrn (maximálně 100 znaků), nabízejí dlouhé shrnutí (maximálně 256 znaků), nabízejí popis (maximálně 1300 znaků), loga pod **podrobnosti** kartu
2. Zadejte název plánu (maximálně 50 znaků), plán souhrn (maximálně 100 znaků), plán popis (maximální 2000 znaků) v části **plány** kartu.
   
   > [!NOTE]
   > Můžete použít následující značky HTML pro formátování souhrnu, dlouhé shrnutí a popis nabídky a plány. Jsou povolené značky HTML h1, h2, h3, h4, h5, p, ol, ul, li, [target | href] silná, em, b, můžu.
   > 
   > 
3. Nezadávejte duplicitní text v části Popis nabídky a plán.
4. Nezadávejte duplicitní textu v názvu plánu a nabídky, dlouhé shrnutí.
5. Nezadávejte zadejte duplicitní text v části název plánu a nabízejí souhrn.
6. Nezadávejte plán identické názvy pro nabídky s více schématy.
7. Nahrajte obrázky požadovaných specifikací (jak je uvedeno na portálu publikování) ve formátu PNG, jeden pro každou velikost.
8. Ujistěte se, že loga postupujte podle pokynů logo na webu Azure Marketplace uvedené níže.
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Azure Marketplace loga**

Postupujte podle všech loga nahrát na portálu publikování níže uvedených pokynů:

* Design Azure má jednoduchou paletu barev. Zachovat počet primární a sekundární barvy na vaše logo nízké.
* Jsou bílé barvy motivu na webu Azure portal a černé. Vyhněte se proto používání tyto barvy jako barva pozadí vašeho loga. Použijte některé barvu, která by provedla vašeho loga viditelného na webu Azure Portal. Doporučujeme použít jednoduché primární barvy. **Pokud používáte průhledným pozadím, ujistěte se, že nejsou loga/text bílé nebo černé nebo modrou.**
* Nepoužívejte logo barevného přechodu pozadí.
* Předejde text, dokonce i vaše společnost nebo název značky, loga. Vzhled a chování vašeho loga musí být "ploché" a přechody se měli vyhnout.
* Logo by neměl být roztažená.
* Malé logo musí mít velikost 40 X 40 px
* Střední logo musí mít velikost 90 X 90 px
* Velké logo musí mít velikost 115 X 115 px
* Široké logo musí mít velikost 255 X 115 px
* Hero logo by měl mít velikost 815 X 290 px

> [!NOTE]
> Hero logo je volitelné. Vydavatele můžete nahrávat Hero logo. Ale jednou nahrané ikonu hero nelze odstranit z publikování portálu. V tu chvíli partner musí dodržovat pokyny pro Azure Marketplace pro Hero ikony.
> 
> 

  ![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Další pokyny pro ikonu Hero logo (volitelné)**

* Hero logo je volitelné. Vydavatele můžete nahrávat Hero logo. **Ale jednou nahrané ikonu hero nelze odstranit z publikování portálu. V tu chvíli partner musí dodržovat pokyny Azure Marketplace pro jiný Hero ikony neschválí nabídky do produkčního prostředí.**
* Zobrazovaný název vydavatele, název plánu a nabídky, dlouhé shrnutí se zobrazují v barva bílé písma. Proto byste se měli vyhnout zachovat všechny světlé barva pozadí ikony Hero. Černou, bílou a průhledné pozadí není povolena pro Hero ikony.
* Zobrazovaný název vydavatele, plán, title, nabídky, dlouhé shrnutí a tlačítka pro vytvoření jsou vložené prostřednictvím kódu programu uvnitř Hero logo Jakmile přejde nabídky uvedené. Při návrhu Hero logo, proto by neměl zadat libovolný text. Ponechte prázdné místo na pravé straně, protože text (například zobrazovaný název vydavatele, název plánu nabídky, dlouhé shrnutí) budou zahrnuty programově od nás tam. Prázdný text by měl být 415 x 100 na pravé straně (a je posunut 370px od levého okraje).
  
  ![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Odkazy
Na **odkazy** kartu na levém panelu, zadejte všechny odkazy s informacemi, které můžou zákazníkům pomoct. Zadejte název a URL pro každý odkaz.

![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Ukázkové obrázky (volitelné)
> [!NOTE]
> Včetně Ukázkový obrázek je volitelný krok.
> I když můžete nahrát několik imagí ukázka publikování portálu jenom jednu image (náhodně zadáno v systému) zobrazí na webu Azure Portal. Z tohoto důvodu doporučujeme nahrávání maximálně jednu image vzorku.
> 
> 

Na **ukázkové obrázky** kartu v nabídce vlevo, nahrajte nový obrázek kliknutím **nahrajte nový obrázek**. Pokud máte stávající bitovou kopii a chcete ho nahradit, klikněte na tlačítko **nahradit image**.

![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Právní informace
Na **právní** kartu, zadejte odkaz na svoje zásady/podmínky použití. Zadejte nebo vložte podmínky velkého **Terms of Use** pole. Omezení počtu znaků pro právní podmínky použití je 1 000 000 znaků.

![Kreslení](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Poznámka:** nabídky virtuálních počítačů po nabídka/SKU je připravené na webu Azure Portal, nebudete moct změnit pole uvedena níže:

* **Identifikátor nabídky:** [portál pro publikování -> Virtual Machines -> vaši nabídku Image virtuálního počítače -> kartu -> identifikátor nabídky]
* **Identifikátor SKU:** [portál pro publikování -> Virtual Machines -> -> nabídkou vyberte skladovou Položku Přidat -> karta skladové položky]
* **Vydavatel Namespace:** [portál pro publikování -> Virtual Machines -> karta -> Podrobnosti nám o vaší společnosti (najít v části "Krok 2 zaregistrujte vaši společnost.") -> návod Namespace-Namespace vydavatele >]

Nabídky virtuálních počítačů po nabídka/SKU je uveden v Tržišti Azure Marketplace, nelze změnit pole uvedena níže:

* **Identifikátor nabídky:** [portál pro publikování -> Virtual Machines -> vyberte -> vaší nabídky Image virtuálního počítače -> identifikátor nabídky]
* **Identifikátor SKU:** [portál pro publikování -> Virtual Machines -> -> nabídkou vyberte skladovou Položku Přidat -> karta skladové položky]
* **Vydavatel Namespace:** [portál pro publikování -> Virtual Machines -> návod -> karta Namespace-Namespace vydavatele Řekněte nám o vaše společnost (najít v části Krok 2 registrace) >]
* **Porty:** [portál pro publikování -> Virtual Machines -> vaši nabídku Image virtuálního počítače -> karta -> Otevřít porty]
* **Při změně cen uvedených skladové jednotky**
* **Fakturace Změna modelu uvedené skladové jednotky**
* **Odebrání fakturace oblastech uvedených skladové jednotky**
* **Změna počet datových disků uvedené skladové jednotky**

## <a name="step-2-set-your-prices"></a>Krok 2: Nastavení vašich cen
### <a name="pricing-models"></a>Cenové modely
| Cenový model | Popis |
| --- | --- |
| Základní |Paušální měsíční poplatek placené při nákupu; například 10 USD za měsíc. |
| Využití (označovaný také jako využití, měřidla) |Platba za použití, které jsou definovány pomocí vydavatel této nabídky. Nadlimitní využití není možné definovat za pracovní místo za uživatele, atd., protože neexistuje koncept zlomku uživatele nebo schopnost provádět poměrná částka. Využití je ohlášena partnera po hodinách. Zákazník zaplatí na měsíčního fakturačního cyklu, na rozdíl od začátku like měsíčních plánech. |
| Bezplatná zkušební verze |Zákazník může používat zdarma po omezenou dobu a pak po tomto datu platit běžné sazby. |
| Úroveň Free |Plán je vždy zdarma. |
| Migrace (označovaný také jako převod nebo upgrade a downgrade) plánu |Koncept uživatele přechod ze své stávající plán na jiný plán přijatelné. určené partnera. |

**Cenové modely dostupné typ nabídky**

> [!IMPORTANT]
> Dostupnost určitých cenové modely se liší podle typu nabídky. Najdete v následující tabulce.
> 
> 

|  | Pouze základní | Pouze spotřeba | Základní + využití |
| --- | --- | --- | --- |
| Image virtuálního počítače |Ne |Ano |Ne |
| Služba pro vývojáře |Ano |Ano |Ano |

### <a name="21-set-your-vm-prices"></a>2.1. Nastavení vašich cen virtuálních počítačů
V současné době pro virtuální počítače, máme následující **3 typy modely fakturace:**

* **Každou hodinu:** zákazníkům účtovat podle sazeb nastavit vydavatelé o velikostech virtuálních počítačů pro každý za každou hodinu. V případě klíčových **hodinové fakturace** modelu SKU, celková cena bude součtem nákladů na software účtuje vydavatelem a účtovat microsoftem náklady na infrastrukturu. Toto celkové náklady se zobrazí zákazníka jako po hodinách a měsíční cena při nákupu zvažují (viz následující snímek obrazovky). **Vydavatel obdrží 80 % nákladů softwaru, účtuje se podle nich.** Proto prosím zkontrolujte výpočtu odpovídajícím způsobem než nastavení cen pro skladové jednotky.
  
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Bezplatná zkušební verze:** Toto je jiný charakter hodinový model. Tady zákazník nebude účtovat, náklady na software pro prvních 30 days(Free) po nasazení virtuálního počítače. Po 30days se bude účtovat na základě HODINOVĚ podle sazby stanovené vydavatelů v hodinový model.
* **Přineste si – vlastní licence (BYOL):** vydavatele spravovat licencování softwaru běžícího na virtuálním počítači.

**Důležité:** po nabídka/SKU je uveden na webu Azure Marketplace, nebudete moct změnit pole uvedena níže.

* **Při změně cen uvedených skladové jednotky**
* **Fakturace Změna modelu uvedené skladové jednotky**
* **Odebrání fakturace oblastech uvedených skladové jednotky**
* **Změna počet datových disků uvedené skladové jednotky**
* **Identifikátor nabídky:** [portál pro publikování -> Virtual Machines -> vyberte -> vaší nabídky Image virtuálního počítače -> identifikátor nabídky]
* **Identifikátor SKU:** [portál pro publikování -> Virtual Machines -> -> nabídkou vyberte skladovou Položku Přidat -> karta skladové položky]
* **Vydavatel Namespace:** [portál pro publikování -> Virtual Machines -> návod -> karta Namespace-Namespace vydavatele Řekněte nám o vaše společnost (najít v části Krok 2 registrace) >]
* **Porty:** [portál pro publikování -> Virtual Machines -> vaši nabídku Image virtuálního počítače -> karta -> Otevřít porty]

### <a name="sell-to-countries-of-the-sku"></a>"Zákazník" země, do SKU
Je potřeba pečlivě zvážit, kde můžete zpřístupnit skladové jednotky. Některé země jsou klasifikovány jako "Microsoft úhrady" a další jsou klasifikovány jako "Úhrady nezávislým výrobcům softwaru."

* V zemích "Microsoft úhrady" společnost Microsoft shromažďuje daně od zákazníků a zaplatí (vykazovány) daň vládě.
* V zemích "ISV hradit" zodpovídají za shromažďuje daně, které zákazníkům a platit daň vládě partnery. Pokud chcete prodávat v zemích "ISV hradit", musí mít možnost Vypočítat a hradit daně v zemích, které vyberete.

> [!NOTE]
> Skladové jednotky nesmí být dostupný v zemích, pokud jste nastavili v cenách [portál pro publikování](https://publish.windowsazure.com). Pokyny k získání nastavení ceny za každou hodinu a BYOL skladové položky jsou uvedena níže.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 jak nastavit hodinové cenový model pro SKU
Postupujte podle kroků uvedených níže pro každou hodinu cenový model pro skladovou Položku instalační program:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **VIRTUÁLNÍCH počítačů** kartě a vyberte vaši nabídku.
3. V nabídce na levé straně straně, klikněte na tlačítko **SKU** kartu.
4. Ujistěte se, že SKU je označena jako "Hodinové fakturace Model". Pokud ne, pak klikněte na **upravit** tlačítko model fakturace vrátit zpět. Otevře se okno. Zrušte zaškrtnutí políčka "fakturaci a licencování provádí externě od Azure (označuje se také jako používání vlastní licence)" a uložte změny.
5. Pokud chcete povolit bezplatnou zkušební verzi pro první 30days skladovou Položku nasazení, vyberte možnost "Měsíc" pro otázku "Je bezplatná zkušební verze dostupná?" Jinak vyberte možnost "Ne zkušební verze". Nyní postupujte podle kroků uvedených níže.
6. V nabídce na levé straně straně, klikněte na tlačítko **ceny** kartu.
7. Vyberte základní oblast.
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Nastavení cen pro všechna jádra. **Cena je nutné zadat pro všechna jádra SKU i v případě skladové jednotky se nepodporuje.**
   
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Nastavení cen pro ostatní oblasti ručně nebo můžete použít Průvodce AUTOPRICE nastavení cen jiných oblastech založené na základní oblasti. Chcete-li použít AUTOPRICE průvodce kliknutím na tlačítko **AUTOPRICE ostatní trhy založené na ceny v (Spojené státy).** **Poznámka:** popisek tlačítka se může lišit v závislosti na oblasti, které jste vybrali. Protože při vytváření tohoto dokumentu jsme vybrali Spojených států, takže toto tlačítko má název jako "Automatické price ostatních trhů. na základě cen v USA" v následujícím snímku obrazovky.
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Otevře se průvodce automaticky cena. Na první stránce se zobrazí výběr pro základní trhu. Ujistěte se, oddíl a přejít na další stránku kliknutím na tlačítko "->".
    
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. Zobrazí se možnost pro výběr jader a plány na stránce 2. Vyberte požadované plány a klikněte na tlačítko "->" tlačítko. Klikněte na tlačítko **přepnout všechny** tlačítko Vybrat vše **služby plány** a **měřiče** nebo abyste mohli ručně zkontrolovat zaškrtávací políčka. **Cena je nutné zadat pro všechna jádra SKU i v případě skladové jednotky se nepodporuje.** Proto ověřte, jestli jsou vybrané všechny velikosti core.
    
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. Stránka 3 zobrazuje trzích nebo oblastech. Klikněte na tlačítko **přepnout všechny** tlačítko a vyberte všechny oblasti nebo ručně zaškrtněte políčka pro oblast. Klikněte na tlačítko "->" Přejít na další stránku. **Poznámka:** Microsoft daně uhrazené země jsou rozlišeny pomocí house symbolu. Další podrobnosti najdete v části "zákazník" země, do SKU na této stránce.
    
    ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. Stránka 4 zobrazuje kurzů. Klikněte na tlačítko Dokončit dokončete postup.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 jak nastavit BYOL cenový model pro SKU
Postupujte podle kroků níže uvedených nastavení BYOL cenový model pro SKU:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **VIRTUÁLNÍCH počítačů** kartě a vyberte vaši nabídku.
3. V nabídce na levé straně straně, klikněte na tlačítko **SKU** kartu.
4. Ujistěte se, že SKU je označen jako "Přineste vlastní licenci skladová položka". V opačném případě klikněte na tlačítko Upravit model fakturace vrátit zpět. Otevře se okno. Zaškrtněte políčko "fakturaci a licencování provádí externě od Azure (označuje se také jako používání vlastní licence)" a uložte změny.
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. V nabídce na levé straně straně, klikněte na tlačítko **ceny** kartu.
6. Vyberte základní oblast a zpřístupnit SKU v oblasti tak, že zaškrtnete políčko proti SKU části dostupnosti skladových položek EXTERNALLY-LICENSED (BYOL) (viz následující snímek obrazovky).
   
   ![Kreslení](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Zpřístupnit SKU v jiných oblastech ručně nebo můžete použít Průvodce AUTOPRICE pro tento účel. Odkazovat na body, #9 do #13 (které popisuje použití Průvodce AUTOPRICE) v části **"2.1.1 nastavení každou hodinu cenový model pro skladovou Položku"** této stránky.

### <a name="22-set-your-developer-service-prices"></a>2.2. Nastavení vašich cen služby pro vývojáře
Plány můžou být libovolnou kombinací base + využití, kde base je měsíční poplatek a při překročení limitu je cena, platba za použití. (Viz níže pro další podrobnosti.)

**Příklad:** nabídky služeb pro vývojáře společnosti Contoso

| Plánování | Cena | Zahrnuje | Následující způsob migrace |
| --- | --- | --- | --- |
| Free |hodnotou 0 USD/měsíc |Základní funkce. |Můžete migrovat do jiných plán |
| Bronzová |10 USD za měsíc |Základní funkce a kvóty 1 000 funkce X. |Můžete migrovat do bronzová Plus, stříbrné a zlaté plány |
| Bronzové Plus |Ukončení bezplatného zkušebního období: 0 USD za měsíc + 0 USD/meter01 |Základní funkce a kvóty funkce X 10 000.  Jakmile se používá funkce X kvóty, zákazníka můžete platit za použití prostřednictvím meter01. |Můžete migrovat do Gold a Silver a plány |
| Bronzové Plus |Placené interval (označovaný také jako platnost bezplatné zkušební verze): 10 USD za měsíc + 0,05 USD/meter01 |Základní funkce a kvóty funkce X 10 000.  Jakmile se používá funkce X kvóty, zákazníka můžete platit za použití prostřednictvím meter01. |Můžete migrovat do Gold a Silver a plány |
| Stříbrný |$ 0,15/meter01 |Zákazník může platit za použití prostřednictvím meter01, což je pro funkci X. |Můžete migrovat do bronzová a zlaté plány |
| Stříbrným Plus |20 USD za měsíc + $ 0,15/meter01 + 0.01 $/ meter02 |Základní funkce a kvóty 10 000 funkce X a Y funkce 100.  Jakmile se používá funkce X kvóty, zákazníka můžete platit za použití prostřednictvím meter01.  Jakmile se používá funkce Y kvóty, zákazníka můžete platit za použití prostřednictvím meter02. |Můžete migrovat do bronzová Plus a zlaté plány |
| Zlatá |1 000 USD/měsíc |Kvóta 10 000 X 1 000 funkce Y, funkce a funkce Z neomezený počet. |Můžete migrovat všechny plány kromě zdarma |

## <a name="step-3-provide-support-information"></a>Krok 3: Zadání podpory informace
Kontaktní údaje se používají pro interní komunikaci mezi Microsoft a partnerské pouze. Adresa URL podpory, bude k dispozici koncovým zákazníkům.

1. Přejděte **podporu** nadpis na levé straně portálu publikování.
2. Zadejte informace pod **technickým kontaktem**.
3. Zadejte informace pod **zákaznickou podporu**. Pokud poskytujete jenom e-mailovou podporu, zadejte fiktivní telefonní číslo a zadaný e-mail se použije místo toho.
4. Zadejte adresu URL podpory.

## <a name="step-4-choose-azure-marketplace-categories"></a>Krok 4: Výběr kategorií webu Azure Marketplace
**Kategorie** karta obsahuje celou řadu výběry. Vaše nabídka může spadat do těchto a můžete vybrat až o pěti kategorií.

## <a name="how-your-marketing-will-appear"></a>Jak se zobrazí vaše marketing
Tady je podrobné zobrazení využití nabízejí obchodní informace na [webu Azure Marketplace](https://azure.microsoft.com/marketplace/) a [webu Azure portal](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Web Azure Marketplace
![Kreslení](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![Kreslení](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Seznam nabídek na webu Azure Marketplace*

![Kreslení](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Podrobnosti o popis nabídky na webu Azure Marketplace*

![Kreslení](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Podrobnosti o cenách na webu Azure Marketplace popis nabídky*

### <a name="azure-portal"></a>Azure Portal
![Kreslení](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Seznam nabídek na webu Azure Portal*

![Kreslení](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Podrobnosti o popis nabídky na webu Azure Portal*

## <a name="next-steps"></a>Další postup
Teď, když dojde k načtení obsahu webu Marketplace, můžeme pokračovat v testování vaší nabídky v testovacím prostředí. Ale musíte vybrat typ příslušné nabídky ze seznamu níže, jako postup se liší podle typu nabídky.

* [Testování vaší nabídky virtuálních počítačů v testovacím prostředí](marketplace-publishing-vm-image-test-in-staging.md)
* [Test šablony nabídky řešení v testovacím prostředí](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
