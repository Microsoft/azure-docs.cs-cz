---
title: Vytvoření nabídky konzultační služby – Microsoft Commercial Marketplace
description: Naučte se publikovat konzultační nabídku služby buď Microsoft AppSource nebo Azure Marketplace pomocí partnerského centra.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: 6ca03e56d49d26fcd34a25ce1e3734db65b19b65
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646840"
---
# <a name="create-a-consulting-service-offer"></a>Vytvoření nabídky konzultačních služeb

Tento článek popisuje, jak publikovat konzultační nabídku služby buď [Microsoft AppSource](https://appsource.microsoft.com/) , nebo [Azure Marketplace](https://azuremarketplace.microsoft.com/). Seznamte se s nabídkami konzultačních služeb založenými na Microsoft [Dynamics 365](https://dynamics.microsoft.com/) a Power platformou v AppSource. Seznamte se s nabídkami konzultačních služeb na základě Microsoft Azure Azure Marketplace. Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](create-account.md) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

Před vytvořením nabídky si přečtěte předpoklady v části [požadavky na konzultační služby](consulting-service-prerequisites.md).

## <a name="publishing-benefits"></a>Výhody publikování

Výhody publikování na komerčním webu Marketplace:

- Propagujte svoji společnost pomocí značky Microsoftu.
- Může mít více než 100 000 000 Office 365 a Dynamics 365 uživatelů na AppSource a více než 200 000 organizací prostřednictvím Azure Marketplace.
- Dostanou vysoce kvalitní zájemce z těchto tržišť.
- Povýšit vaše služby na pole Microsoftu a prodejní týmy

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **konzultační služba**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Po publikování nabídky se úpravy provedené v partnerském centru zobrazí pouze v online obchodech po opětovném publikování nabídky. Nezapomeňte po provedení změn vždy znovu publikovat.

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese nabídky Marketplace.
- Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1**, Webová adresa nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.
- Po zvolení možnost **vytvořit**se alias nabídky nedá změnit.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-setup"></a>Nastavení nabídky

### <a name="connect-lead-management"></a>Připojit řízení zájemců

Když publikujete nabídku na webu Marketplace s partnerským centrem, _musíte_ ji připojit k systému pro správu vztahů se zákazníky (CRM) nebo Marketing Automation. To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt.

1. Vyberte **připojit** a určete, kam chcete posílat potenciální zákazníky. Partnerské centrum podporuje následující systémy:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) pro zákaznickou zapojení
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Pokud váš systém CRM není uvedený výše, použijte [koncový bod](commercial-marketplace-lead-management-instructions-https.md) [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) nebo HTTPS k uložení zákaznických dat a pak EXPORTUJTE data do systému CRM.

2. Připojte svoji nabídku k cíli realizace při publikování v partnerském centru.
3. Potvrďte, že je správně nakonfigurované připojení k cíli realizace. Po publikování v partnerském centru ověříme připojení a pošleme vám testovacího vedoucího. I když náhled nabídky před tím, než začne fungovat, můžete také otestovat své zájemce tím, že se pokusíte nabídku koupit v prostředí verze Preview.
4. Ujistěte se, že připojení k cíli realizace zůstane aktualizované, takže nepřijdete o žádné zájemce.

Tady jsou některé další zdroje informací o vedoucím řízení:

- [Přehled řízení vedoucích](commercial-marketplace-get-customer-leads.md)
- [Nejčastější dotazy ke správě potenciálních zákazníků](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [Běžné chyby konfigurace zájemce](../lead-management-for-cloud-marketplace.md#publishing-config-errors)
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

3. Pokud jste jako primární produkt vybrali **Azure** , vyberte až tři **oblasti řešení**. To usnadňuje zákazníkům v Azure Marketplace najít vaši nabídku. Pokud jste nezvolili Azure, přeskočte tento krok.

    - Analýzy
    - Modernizace aplikací
    - Archiv
    - AI a strojové učení
    - Backup
    - Velký objem dat
    - Datová platforma
    - Správa Datacenter
    - DevOps
    - Zotavení po havárii
    - Identita
    - Internet věcí
    - Migrace
    - Sítě
    - Zabezpečení
    - Storage

1. Pokud jste jako primární produkt vybrali **Azure** , máte možnost vybrat až šest **oborů**. To usnadňuje zákazníkům v Azure Marketplace najít vaši nabídku. Seznamte se s [osvědčenými postupy v nabídce](../gtm-offer-listing-best-practices.md), kde najdete úplný seznam oborů. Pokud jste nezvolili Azure, přeskočte tento krok.
1. Pokud jste vybrali *jiný* produkt než Azure, vyberte až tři produkty, které jsou k **dispozici**. To usnadňuje zákazníkům v AppSource najít vaši nabídku. Podrobnosti najdete v tématu [Microsoft AppSource konzultačních pokynů pro výpisy služby](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
1. Pokud jste vybrali *jiný* produkt než Azure, máte možnost vybrat si až dva **obory** a dvě **svislé** oblasti pro každý obor. To usnadňuje zákazníkům v AppSource najít vaši nabídku. Seznamte se s [osvědčenými postupy v nabídce](../gtm-offer-listing-best-practices.md)s úplným seznamem odvětví a svislých.
1. Přidejte až tři **kompetence** , které vaše společnost získala, aby se zobrazila v seznamu nabídek konzultačních služeb. Vyžaduje se aspoň jedna kompetence s výjimkou služeb Azure expert účastníci programu MSP a Azure Networking účastníci programu MSP.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

Tady definujete podrobnosti nabídky, které se zobrazí na webu Marketplace. To zahrnuje název nabídky, popis, obrázky a tak dále. Při konfiguraci této nabídky nezapomeňte postupovat podle zásad, které jsou podrobné na [stránce Zásady certifikace komerčního tržiště](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) .

> [!NOTE]
> Podrobnosti nabídky nejsou v angličtině, pokud popis nabídky začíná frází, &quot; Tato aplikace je k dispozici pouze v [jazyk bez angličtiny]. &quot; Také je v pořádku poskytnutí užitečného odkazu na nabídku obsahu v jazyce, který se liší od toho, který se používá v podrobnostech seznamu nabídky.

Tady je příklad toho, jak se zobrazují informace o nabídce v Azure Marketplace (uvedené ceny jsou například jenom pro účely a nejsou určené k tomu, aby odrážely skutečné náklady):

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Logo
2. Cena
3. Oblasti řešení
4. Obory
5. Název nabídky
6. Shrnutí
7. Popis
8. Snímky obrazovky a videa

<br>Tady je příklad toho, jak se zobrazují informace o nabídce v Microsoft AppSource (uvedené ceny jsou například jenom pro účely a nejsou určené k tomu, aby odrážely skutečné náklady):

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Logo
2. Cena
3. Produkty
4. Obory
5. Název nabídky
6. Shrnutí
7. Popis
8. Snímky obrazovky a videa
9. Dokumenty

### <a name="name"></a>Name

Název, který zde zadáte, se zobrazí jako název vaší nabídky. Toto pole je předem vyplněno textem, který jste zadali do pole **alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Dá se použít jako ochranná známka (a může obsahovat symboly ochranné známky nebo autorských práv).
- Nemůže být delší než 50 znaků.
- Nejde zahrnout emoji.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Zadejte krátký popis vaší nabídky. Může to být až 100 znaků dlouhé a používá se ve výsledcích hledání na webu Marketplace.

### <a name="description"></a>Popis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

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

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

#### <a name="store-logos"></a>Ukládat loga

Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije toto, aby se vytvořilo **malé** logo. Volitelně můžete tuto položku nahradit jinou imagí později.

- **Velký** (od 216 x 216 do 350 × 350 px, požadováno)
- **Malý** (48 x 48 px, volitelné)

Tato loga se používají na různých místech v seznamu.

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

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

Další informace o tom, jak dlouho můžete očekávat, že vaše nabídka je v každé fázi publikování, najdete v tématu o tom, jak [zjistit stav publikování nabídky na komerčním webu Marketplace](publishing-status.md).

## <a name="update-your-existing-consulting-service-offers"></a>Aktualizace stávajících nabídek konzultačních služeb

- [Aktualizace stávající nabídky na komerčním webu Marketplace](update-existing-offer.md)
