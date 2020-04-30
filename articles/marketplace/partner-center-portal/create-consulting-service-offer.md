---
title: Vytvoření nabídky konzultační služby v partnerském centru – Azure Marketplace
description: Naučte se publikovat konzultační nabídku služby buď Azure Marketplace nebo AppSource pomocí partnerského centra.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869796"
---
# <a name="create-a-consulting-service-offer"></a>Vytvoření nabídky konzultační služby

> [!IMPORTANT]
> Přesouváme správu nabídek konzultačních služeb od portál partnerů cloudu do partnerského centra. Dokud se vaše nabídky nemigrují, postupujte podle pokynů v [nabídce konzultační služby Azure a Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) , kde najdete portál partnerů cloudu ke správě nabídek.

Tento článek popisuje, jak publikovat konzultační nabídku služby na [Azure Marketplace](https://azuremarketplace.microsoft.com/) nebo [AppSource](https://appsource.microsoft.com/). Seznamte se s nabídkami konzultačních služeb založenými na Microsoft [Dynamics 365](https://dynamics.microsoft.com/) a Power platformou v AppSource. Seznamte se s nabídkami konzultačních služeb na základě Microsoft Azure Azure Marketplace.

Pokud chcete vytvořit nabídku konzultační služby v rámci Azure Marketplace nebo AppSource konzultačních služeb, musíte nejdřív [mít v partnerském centru účet vydavatele](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)a váš účet musí být zaregistrovaný v programu komerčního tržiště. Před vytvořením nabídky si přečtěte předpoklady v části [požadavky na konzultační služby](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Výhody publikování

Výhody publikování na komerčním webu Marketplace:

- Propagujte svoji společnost pomocí značky Microsoftu.
- Může mít více než 100 000 000 Office 365 a Dynamics 365 uživatelů na AppSource a více než 200 000 organizací prostřednictvím Azure Marketplace.
- Dostanou vysoce kvalitní zájemce z těchto tržišť.
- Povýšit vaše služby na pole Microsoftu a prodejní týmy

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

Po splnění požadavků popsaných výše postupujte podle těchto kroků a vytvořte nabídku konzultační služby.

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com)a v horní nabídce vyberte **řídicí panel** .
2. V levém panelu navigace vyberte **komerční Marketplace**a pak vyberte **Přehled**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Znázorňuje nabídku pro komerční tržiště.":::

3. Vyberte **+ Nová nabídka**a pak vyberte **konzultační službu**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Znázorňuje tlačítko pro vytvoření nové nabídky.":::

4. Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

    - Toto ID je viditelné pro zákazníky na webové adrese nabídky Marketplace.
    - Používejte jenom malá písmena, číslice, pomlčky a podtržítka, ale ne mezery. Délka je omezena na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1**, adresa URL nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
    - ID nabídky může&#39;změnit po výběru **vytvořit**.

5. Zadejte **alias nabídky**. Toto je název, který se používá k odkazování na nabídku v partnerském centru.

    - Tento název není&#39;v Marketplace použit. &#39;se liší od názvu nabídky a dalších hodnot, které se zobrazují zákazníkům. Toto pole můžete použít k přiřazení názvu nabídky, která je užitečnější pro interní identifikaci této nabídky. nezobrazuje se zákazníkům.
    - Alias nabídky může&#39;změnit po výběru **vytvořit**.

Po zadání těchto dvou hodnot vyberte **vytvořit** a pokračujte na stránku **nastavení nabídky** .

## <a name="offer-setup"></a>Nastavení nabídky

Po zadání ID nabídky a aliasu nabídky vytvoří partnerský centrum nabídku konceptu a zobrazí stránku **nastavení nabídky** . Pomocí těchto kroků můžete nastavit nabídku.

### <a name="connect-lead-management"></a>Připojit řízení zájemců

Když publikujete nabídku na webu Marketplace s partnerským centrem, _musíte_ ji připojit k systému pro správu vztahů se zákazníky (CRM) nebo Marketing Automation. To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt.

1. Vyberte **připojit** a určete, kam chcete posílat potenciální zákazníky. Partnerské centrum podporuje následující systémy:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) pro zákaznickou zapojení
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Pokud váš systém CRM není&#39;výše uveden, použijte [koncový bod](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) nebo HTTPS k uložení zákaznických dat a pak EXPORTUJTE data do systému CRM.

2. Připojte svoji nabídku k cíli realizace při publikování v partnerském centru.
3. Potvrďte, že je správně nakonfigurované připojení k cíli realizace. Po publikování v partnerském centru&#39;me ověřit připojení a pošleme vám testovacího vedoucího. I když náhled nabídky před tím, než začne fungovat, můžete také otestovat své zájemce tím, že se pokusíte nabídku koupit v prostředí verze Preview.
4. Ujistěte se, že připojení k cíli realizace zůstane aktualizované, takže&#39;nepřijdete o žádné zájemce.

Tady jsou některé další zdroje informací o vedoucím řízení:

- [Přehled řízení vedoucích](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Nejčastější dotazy ke správě potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Běžné chyby konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Přehled řízení vedoucích](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Ujistěte se, že je vypnutý blokování automaticky otevíraných oken)

Než budete pokračovat k další části, můžete vybrat možnost **Uložit koncept** .

### <a name="properties"></a>Vlastnosti

Tato stránka umožňuje nastavit primární produkt, který vaše konzultační služba nabízí nejlépe, nastavit typ konzultační služby a vybrat příslušné produkty.

1. Z rozevíracího seznamu vyberte **primární produkt** .
2. Z rozevíracího seznamu vyberte **typ konzultační služby** :

    - **Posouzení** : hodnocení prostředí&#39;s zákazníky k určení použitelnosti řešení a zajištění odhadu nákladů a časování.
    - **Přehled** : Úvod do řešení nebo poradenské služby za účelem vystavení zájmu zákazníků pomocí platforem, ukázek a příkladů zákazníků.
    - **Implementace** : kompletní instalace, která má za následek plně funkční řešení. Omezení na řešení, která je možné implementovat ve dvou nebo méně týdnech.
    - **Ověření koncepce** : implementace omezeného rozsahu, která určuje, jestli řešení splňuje požadavky zákazníků.
    - **Workshop** : interaktivní zapojení na místní&#39;zákazníka. Může zahrnovat školení, stručné hodnocení, posouzení nebo ukázky založené na datech a prostředí zákazníka&#39;s.

1. Pokud jste vybrali primární produkt **Azure**, vyberte až tři **oblasti řešení**. To usnadňuje zákazníkům v Azure Marketplace najít vaši nabídku. Pokud nefungovala&#39;vyberte Azure, tento krok přeskočte.
2. Pokud jste vybrali _jiný_ produkt než Azure, vyberte až tři produkty, které jsou k **dispozici**. To usnadňuje zákazníkům v AppSource najít vaši nabídku. Podrobnosti najdete v tématu [Microsoft AppSource konzultačních pokynů pro výpisy služby](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Vyberte až šest **odvětví** , na které se vaše nabídka vztahuje. To zákazníkům usnadní nalezení vaší nabídky.
4. Přidejte až tři **kompetence** , které vaše společnost získala, aby se zobrazila v seznamu nabídek konzultačních služeb. Vyžaduje se aspoň jedna kompetence, s výjimkou služeb Azure expert MSP&#39;s a Azure Networking MSP&#39;s.

Než budete pokračovat k další části seznamu nabídek, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

Zde&#39;vše definovat nabídky, které se zobrazují na webu Marketplace. To zahrnuje název nabídky, popis, obrázky a tak dále. Při konfiguraci této nabídky postupujte podle pokynů na [stránce Zásady podrobné zásady pro Microsoft&#39;s](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) .

> [!NOTE]
> Podrobnosti o nabídce&#39;t musí být v angličtině, pokud popis nabídky začíná frází, &quot;Tato aplikace je k dispozici pouze v [jazyk bez angličtiny]. &quot;&#39;taky také v pořádku, aby poskytovala užitečný odkaz na obsah nabídky v jazyce, který se&#39;liší od toho, který se používá v podrobnostech o seznamu nabídek.

### <a name="name"></a>Název

Název, který zde zadáte, se zobrazí jako název vaší nabídky. Toto pole je předem vyplněno textem, který jste zadali do pole **alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Dá se použít jako ochranná známka (a může obsahovat symboly ochranné známky nebo autorských práv).
- Může&#39;být delší než 50 znaků.
- Může&#39;t zahrnovat emoji.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Zadejte krátký popis vaší nabídky. Může to být až 100 znaků dlouhé a používá se ve výsledcích hledání na webu Marketplace.

### <a name="description"></a>Popis

Zadejte delší popis nabídky, maximálně 3 000 znaků. Zobrazí se zákazníkům v přehledu výpisu na webu Marketplace.

Do popisu zahrňte jednu nebo více z následujících možností:

- Hodnota a klíčové výhody, které vaše nabídka nabízí
- Přidružení kategorie nebo oboru nebo obojí
- Možnosti nákupu v aplikaci
- Jakékoli požadované zpřístupnění

Zde jsou některé tipy pro psaní popisu:

- Jasně popište hodnotu vaší nabídky v prvních několika větách popisu. Zadejte následující položky:
  - Popis nabídky
  - Typ uživatele, který je z nabídky výhodou
  - Zákazník potřebuje nebo vydá adresy nabídky.
- Pamatujte, že ve výsledcích hledání se může zobrazit několik prvních vět.
- &#39;se nespoléhá na funkce a funkce pro prodej vašeho produktu. Místo toho se zaměřte na hodnotu, kterou nabízí vaše nabídka.
- Zkuste použít slovníky specifické pro konkrétní obor nebo na základě výhod.

Abychom mohli podrobnější popis, použijte Editor formátovaného textu k formátování popisu. Editor formátovaného textu umožňuje přidat čísla, odrážky, tučné písmo, kurzívu a odsazení, aby bylo možné tento popis čitelnější.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Ukazuje Editor formátovaného textu pro zápis popisu nabídky." border="false":::

### <a name="keywords"></a>Klíčová slova

Zadejte až tři hledaná klíčová slova, která jsou relevantní pro vaši primární produkt a konzultační službu. Díky tomu budou vaše nabídky snazší najít.

### <a name="duration"></a>Doba trvání

Nastavte očekávanou dobu trvání této zapojení se zákazníkem.

### <a name="contact-information"></a>Kontaktní informace

Musíte zadat jméno, e-mail a telefonní číslo pro **primární** a **sekundární kontakt**. Tyto informace&#39;zákazníkům nevidí. Je k dispozici pro společnost Microsoft a může být poskytnuta partnerům Cloud Solution Provider (CSP).

### <a name="supporting-documents"></a>Podpůrné dokumenty

Přidejte až tři (ale alespoň jednu) podporu dokumentů PDF pro vaši nabídku.

### <a name="marketplace-images"></a>Image Marketplace

Poskytněte loga a obrázky, které se mají použít s vaší nabídkou. Všechny obrázky musí být ve formátu. png. Rozmazaný obraz se odmítne.

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

#### <a name="store-logos"></a>Ukládat loga

Poskytněte v každé z následujících velikostí pixelů soubory. png vaší nabídky&#39;s:

- **Malé (48 x 48)**
- **Velký (216 x 216)**

Všechna loga jsou povinná a používají se na různých místech v seznamu Marketplace.

#### <a name="screenshots-optional"></a>Snímky obrazovky (volitelné)

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Každá velikost musí být 1280 × 720 pixelů a ve formátu. png.

#### <a name="videos-optional"></a>Videa (volitelné)

Přidejte až čtyři videa, která předvádí vaši nabídku. Zadejte video&#39;název, jeho webová adresa (URL) a miniaturu. png obrázku na 1280 × 720 pixelů velikosti.

Než budete pokračovat k další části, ceníku a dostupnosti, vyberte **Uložit koncept** .

## <a name="pricing-and-availability"></a>Ceny a dostupnost

Tady můžete definovat prvky, jako jsou ceny, trhy a privátní klíč.

1. **Trh**: nastavte trh, na který bude nabídka dostupná. Na jednu nabídku můžete vybrat jenom jeden trh.
    1. U USA nebo Kanady vyberte možnost **Upravit stavy** (nebo **provincie**) a určete, kde bude vaše nabídka k dispozici.
2. **Cílová skupina verze Preview**: Nakonfigurujte **Skrýt klíč** , který se používá k nastavení soukromé cílové skupiny pro vaši nabídku.
3. **Ceny**: Určete, jestli je vaše nabídka **volná** nebo **placená** nabídka.

    > [!NOTE]
    > Konzultační nabídky služby jsou jenom pro výpis. K jakékoli transakci dojde přímo mimo komerční web Marketplace.

4. V případě placené nabídky zadejte **cenu a měnu** a zda je cena **pevná** nebo **Odhadovaná**. Je-li odhadované, je nutné zadat v popisu, jaké faktory budou mít vliv na cenu.
5. Vyberte **Uložit koncept**.

## <a name="review-and-publish"></a>Kontrola a publikování

Po dokončení všech požadovaných částí nabídky můžete odeslat nabídku pro kontrolu a publikování.

1. Až budete připraveni publikovat nabídku konzultačních služeb, klikněte na tlačítko **zkontrolovat a publikovat**.
2. Přečtěte si podrobnosti o finální stránce pro odeslání.
3. V případě potřeby zapište si do certifikačního týmu poznámku, pokud se domníváte, že některé z podrobností vaší nabídky vyžadují vysvětlení.
4. Až budete připraveni, vyberte **Odeslat**.
5. Stránka s **přehledem nabídky** zobrazuje, ve které fázi publikování vaše nabídka probíhá.

Další informace o tom, jak dlouho můžete očekávat, že vaše nabídka je v každé fázi publikování, najdete v tématu o tom, jak [zjistit stav publikování nabídky na komerčním webu Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Aktualizace stávajících nabídek konzultačních služeb

- [Aktualizace stávající nabídky na komerčním webu Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
