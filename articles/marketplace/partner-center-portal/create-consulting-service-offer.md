---
title: Vytvoření nabídky konzultační služby – Microsoft Commercial Marketplace
description: Naučte se publikovat konzultační nabídku služby buď Microsoft AppSource nebo Azure Marketplace pomocí partnerského centra.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 626dc5a7d1cd52182e68eb0d217b2ac4c653330e
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994350"
---
# <a name="create-a-consulting-service-offer"></a>Vytvoření nabídky konzultačních služeb

Tento článek popisuje, jak publikovat konzultační nabídku služby buď [Microsoft AppSource](https://appsource.microsoft.com/) , nebo [Azure Marketplace](https://azuremarketplace.microsoft.com/). Seznamte se s nabídkami konzultačních služeb založenými na Microsoft [Dynamics 365](https://dynamics.microsoft.com/) a Power platformou v AppSource. Seznamte se s nabídkami konzultačních služeb na základě Microsoft Azure Azure Marketplace. Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

Před vytvořením nabídky si přečtěte předpoklady v části [požadavky na konzultační služby](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Výhody publikování

Výhody publikování na komerčním webu Marketplace:

- Propagujte svoji společnost pomocí značky Microsoftu.
- Může mít více než 100 000 000 Office 365 a Dynamics 365 uživatelů na AppSource a více než 200 000 organizací prostřednictvím Azure Marketplace.
- Dostanou vysoce kvalitní zájemce z těchto tržišť.
- Povýšit vaše služby na pole Microsoftu a prodejní týmy

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace** > **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka** > **konzultační služba**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Po publikování nabídky se úpravy provedené v partnerském centru zobrazí pouze v prodejní místa po opětovném publikování nabídky. Nezapomeňte po provedení změn vždy znovu publikovat.

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese nabídky Marketplace.
- Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1**, Webová adresa nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.
- Po zvolení možnost **vytvořit**se alias nabídky nedá změnit.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-setup"></a>Nastavení nabídky

### <a name="connect-lead-management"></a>Připojit řízení zájemců

Když publikujete nabídku na webu Marketplace s partnerským centrem, _musíte_ ji připojit k systému pro správu vztahů se zákazníky (CRM) nebo Marketing Automation. To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt.

1. Vyberte **připojit** a určete, kam chcete posílat potenciální zákazníky. Partnerské centrum podporuje následující systémy:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) pro zákaznickou zapojení
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Pokud váš systém CRM není uvedený výše, použijte [koncový bod](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) nebo HTTPS k uložení zákaznických dat a pak EXPORTUJTE data do systému CRM.

2. Připojte svoji nabídku k cíli realizace při publikování v partnerském centru.
3. Potvrďte, že je správně nakonfigurované připojení k cíli realizace. Po publikování v partnerském centru ověříme připojení a pošleme vám testovacího vedoucího. I když náhled nabídky před tím, než začne fungovat, můžete také otestovat své zájemce tím, že se pokusíte nabídku koupit v prostředí verze Preview.
4. Ujistěte se, že připojení k cíli realizace zůstane aktualizované, takže nepřijdete o žádné zájemce.

Tady jsou některé další zdroje informací o vedoucím řízení:

- [Přehled řízení vedoucích](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Nejčastější dotazy ke správě potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Běžné chyby konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [Přehled řízení vedoucích](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Ujistěte se, že je vypnutý blokování automaticky otevíraných oken)

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="properties"></a>Vlastnosti

Tato stránka umožňuje nastavit primární produkt, který vaše konzultační služba nabízí nejlépe, nastavit typ konzultační služby a vybrat příslušné produkty.

1. Z rozevíracího seznamu vyberte **primární produkt** .
2. Z rozevíracího seznamu vyberte **typ konzultační služby** :

    - **Posouzení**: vyhodnocení prostředí zákazníka za účelem určení použitelnosti řešení a zajištění odhadu nákladů a časování.
    - **Přehled**: Úvod do řešení nebo poradenské služby za účelem vystavení zájmu zákazníků pomocí platforem, ukázek a příkladů zákazníků.
    - **Implementace**: kompletní instalace, která má za následek plně funkční řešení. Omezení na řešení, která je možné implementovat ve dvou nebo méně týdnech.
    - **Ověření koncepce**: implementace omezeného rozsahu, která určuje, jestli řešení splňuje požadavky zákazníků.
    - **Workshop**: interaktivní zapojení v místním prostředí zákazníka. Může zahrnovat školení, stručné hodnocení, posouzení nebo ukázky založené na datech a prostředí zákazníka.

3. Pokud jste vybrali primární produkt **Azure**, vyberte až tři **oblasti řešení**. To usnadňuje zákazníkům v Azure Marketplace najít vaši nabídku. Pokud jste nezvolili Azure, přeskočte tento krok.
4. Pokud jste vybrali _jiný_ produkt než Azure, vyberte až tři produkty, které jsou k **dispozici**. To usnadňuje zákazníkům v AppSource najít vaši nabídku. Podrobnosti najdete v tématu [Microsoft AppSource konzultačních pokynů pro výpisy služby](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
5. Vyberte až šest **odvětví** , na které se vaše nabídka vztahuje. To zákazníkům usnadní nalezení vaší nabídky.
6. Přidejte až tři **kompetence** , které vaše společnost získala, aby se zobrazila v seznamu nabídek konzultačních služeb. Vyžaduje se aspoň jedna kompetence s výjimkou služeb Azure expert účastníci programu MSP a Azure Networking účastníci programu MSP.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

Tady definujete podrobnosti nabídky, které se zobrazí na webu Marketplace. To zahrnuje název nabídky, popis, obrázky a tak dále. Při konfiguraci této nabídky nezapomeňte postupovat podle zásad, které jsou podrobné na [stránce Zásady certifikace komerčního tržiště](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) .

> [!NOTE]
> Podrobnosti nabídky nejsou v angličtině, pokud popis nabídky začíná frází, &quot;Tato aplikace je k dispozici pouze v [jazyk bez angličtiny]. &quot; Také je v pořádku poskytnutí užitečného odkazu na nabídku obsahu v jazyce, který se liší od toho, který se používá v podrobnostech seznamu nabídky.

### <a name="name"></a>Name

Název, který zde zadáte, se zobrazí jako název vaší nabídky. Toto pole je předem vyplněno textem, který jste zadali do pole **alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Dá se použít jako ochranná známka (a může obsahovat symboly ochranné známky nebo autorských práv).
- Nemůže být delší než 50 znaků.
- Nejde zahrnout emoji.

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
- Nespoléhá se na funkce a funkce k prodeji vašeho produktu. Místo toho se zaměřte na hodnotu, kterou nabízí vaše nabídka.
- Zkuste použít slovníky specifické pro konkrétní obor nebo na základě výhod.

Chcete-li podrobnější popis nabídky, použijte Editor formátovaného textu k použití formátování.

![Použití editoru textu ve formátu RTF](./media/rich-text-editor.png)

| <center>Změnit formát textu | <center>Přidat odrážky nebo číslování | <center>Přidat nebo odebrat odsazení textu |
| --- | --- | --- |
| <center>![Změna formátu textu pomocí editoru formátovaného textu](./media/text-editor3.png) |  <center>![Přidávání seznamů pomocí editoru formátovaného textu](./media/text-editor4.png) |  <center>![Použití editoru formátovaného textu k odsazení](./media/text-editor5.png) |

### <a name="keywords"></a>Klíčová slova

Zadejte až tři hledaná klíčová slova, která jsou relevantní pro vaši primární produkt a konzultační službu. Díky tomu budou vaše nabídky snazší najít.

### <a name="duration"></a>Doba trvání

Nastavte očekávanou dobu trvání této zapojení se zákazníkem.

### <a name="contact-information"></a>Kontaktní informace

Musíte zadat jméno, e-mail a telefonní číslo pro **primární** a **sekundární kontakt**. Tyto informace se zákazníkům nezobrazují. Je k dispozici pro společnost Microsoft a může být poskytnuta partnerům Cloud Solution Provider (CSP).

### <a name="supporting-documents"></a>Podpůrné dokumenty

Přidejte až tři (ale alespoň jednu) podporu dokumentů PDF pro vaši nabídku.

### <a name="marketplace-images"></a>Image Marketplace

Poskytněte loga a obrázky, které se mají použít s vaší nabídkou. Všechny obrázky musí být ve formátu. png. Rozmazaný obraz se odmítne.

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

#### <a name="store-logos"></a>Ukládat loga

Zadejte soubory PNG pro logo vaší nabídky v každé z následujících velikostí pixelů:

- **Malé (48 x 48)**
- **Velký (216 x 216)**

Všechna loga jsou povinná a používají se na různých místech v seznamu Marketplace.

#### <a name="screenshots-optional"></a>Snímky obrazovky (volitelné)

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Každá velikost musí být 1280 × 720 pixelů a ve formátu PNG.

#### <a name="videos-optional"></a>Videa (volitelné)

Přidejte až čtyři videa, která předvádí vaši nabídku. Zadejte název videa, jeho webovou adresu (URL) a miniaturu obrázku PNG o velikosti videa na 1280 × 720 pixelů.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="pricing-and-availability"></a>Ceny a dostupnost

Tady můžete definovat prvky, jako jsou ceny, trhy a privátní klíč.

1. **Trh**: nastavte trh, na který bude nabídka dostupná. Na jednu nabídku můžete vybrat jenom jeden trh.
    1. U USA nebo Kanady vyberte možnost **Upravit stavy** (nebo **provincie**) a určete, kde bude vaše nabídka k dispozici.
2. **Cílová skupina verze Preview**: Nakonfigurujte **Skrýt klíč** , který se používá k nastavení soukromé cílové skupiny pro vaši nabídku.
3. **Ceny**: Určete, jestli je vaše nabídka **volná** nebo **placená** nabídka.

    > [!NOTE]
    > Konzultační nabídky služby jsou jenom pro výpis. K jakékoli transakci dojde přímo mimo komerční web Marketplace.

4. V případě placené nabídky zadejte **cenu a měnu** a zda je cena **pevná** nebo **Odhadovaná**. Je-li odhadované, je nutné zadat v popisu, jaké faktory budou mít vliv na cenu.
5. Než budete pokračovat, vyberte **Uložit koncept** .

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
