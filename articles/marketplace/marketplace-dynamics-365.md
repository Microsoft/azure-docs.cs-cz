---
title: Plánování nabídek Dynamics 365 pro Microsoft AppSource
description: Plánování nabídek Dynamics 365 pro Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/16/2021
ms.openlocfilehash: c5746b70f84ff68ea578ba141cc5251cc5ce07ea
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819353"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Plánování nabídky Microsoft Dynamics 365

Tento článek popisuje různé možnosti a funkce nabídky Dynamics 365 v Microsoft AppSource na komerčním webu Marketplace. AppSource zahrnuje nabídky, které sestavují nebo rozšířily Microsoft 365, Dynamics 365, PowerApps a Power BI. AppSource umožňuje vyplatit (*získat IT*), vypsat (*kontaktovat mi*) a vyzkoušení zkušební verze (*Vyzkoušejte si hned*) nabídky.

Než začnete, vytvořte si účet komerčního tržiště v [partnerském centru](./partner-center-portal/create-account.md) a ujistěte se, že je zaregistrovaný v programu komerčního tržiště. Projděte si také [Postup publikování a pokyny](/office/dev/store/submit-to-appsource-via-partner-center).

## <a name="licensing-options"></a>Možnosti licencování

Při přípravě na publikování nové nabídky se musíte rozhodnout, kterou možnost licencování zvolit. Tím určíte, které další informace budete později potřebovat k dispozici při vytváření nabídky v partnerském centru.

Toto jsou dostupné možnosti licencování pro Dynamics 365 nabízí:

| Možnost licence | Proces transakce |
| --- | --- |
| Získat hned (zdarma) | Seznamte se s vaší nabídkou pro zákazníky zdarma. |
| Bezplatná zkušební verze (výpis) | Poskytněte zákazníkům jednu, tři nebo šest měsíců bezplatné zkušební období. Nabídka pro výpis bezplatných zkušebních verzí se vytváří, spravuje a konfiguruje pomocí vaší služby a nemá předplatné spravované Microsoftem. |
| Kontaktujte mě | Shromažďovat kontaktní údaje zákazníka propojením systému pro správu vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a zdrojem Marketplace, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Další informace o konfiguraci CRM najdete v části **Customers Customer** na stránce pro **nastavení nabídek** vašeho typu nabídky. |
|

## <a name="test-drive"></a>Testovací jednotka

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Potenciální zákazníci

Když publikujete nabídku na komerční tržišti s partnerským centrem, budete ji chtít připojit k systému pro správu vztahů se zákazníky (CRM). To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt. Pokud chcete povolit testovací jednotku, je nutné připojení k CRM. v opačném případě je připojení k CRM volitelné. Partnerské centrum podporuje službu Azure Table, Dynamics 365 Customer Engagement, HTTPS Endpoint, Marketo a Salesforce.

## <a name="legal"></a>Právní informace

Vytvořte **podmínky a ujednání**. Zákazníci ji budou muset přijmout, aby mohli svoji nabídku vyzkoušet. Microsoft má standardní kontrakt, ale nevztahuje se na nabídky Dynamics 365.

## <a name="offer-listing-details"></a>Podrobnosti o seznamu nabídek

Tady je příklad toho, jak se zobrazují informace o nabídce v Microsoft AppSource (uvedené ceny jsou například jenom pro účely a neodrážejí skutečné náklady):

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Microsoft AppSource.":::

> [!NOTE]
> Pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině), není nutné, aby byla v anglickém obsahu.

Abyste mohli snadněji vytvořit nabídku, připravte tyto položky předem. Všechny jsou požadovány s výjimkou případů, kde je uvedeno.

- **Název** – název se zobrazí jako název vaší nabídky na komerčním webu Marketplace. Název může být ve vaší společnosti. Nemůže obsahovat Emoji (Pokud se nejedná o symboly ochranné známky a copyrightu) a je omezený na 50 znaků.
- **Shrnutí výsledků hledání** – účel nebo funkce vaší nabídky jako jediná věta bez konců řádků v 100 znacích nebo méně. Používá se ve výsledcích hledání na komerčních seznamech Marketplace.
- **Popis** – tento popis se zobrazí v seznamech komerčního tržiště. Vezměte v úvahu zahrnutí hodnot do kategorií, klíčových výhod, zamýšlené uživatelské základny, jakékoli kategorie nebo přidružení oboru, možností nákupu v aplikaci, všech potřebných zveřejnění a odkazu na Další informace. Toto textové pole obsahuje ovládací prvky formátovaného textu, které umožňují pružnější použití popisu. Volitelně můžete použít značky HTML pro formátování.
- **Hledaná klíčová slova** (volitelné) – až tři klíčová slova pro hledání, která můžou zákazníci použít k vyhledání vaší nabídky. Nezahrnujte **název** a **Popis** nabídky. Tento text je automaticky zahrnutý do hledání.
- Produkt, ve kterém **vaše aplikace funguje** (volitelné) – názvy až tří produktů, se kterými vaše nabídka pracuje.
- **Odkazy na nápovědu a zásady ochrany osobních údajů** – adresy URL pro nápovědu a zásady ochrany osobních údajů společnosti vaší nabídky. Zodpovídáte za to, že vaše aplikace vyhovuje předpisům a zákonům o ochraně osobních údajů.
- **Kontaktní informace**
  - **Kontakt na podporu** – jméno, telefon a e-mail, které partneři Microsoftu použijí, když vaši zákazníci otevřou lístky. Zadejte adresu URL vašeho webu podpory.
  - **Technický kontakt** – jméno, telefon a e-mail Microsoftu pro použití přímo v případě problémů s vaší nabídkou. Tyto kontaktní údaje nejsou uvedené na komerčním webu Marketplace.
- **Podpůrné dokumenty** – až tři zákaznické dokumenty, jako jsou dokumenty White Paper, brožury, kontrolní seznamy nebo prezentace PowerPointu, ve formátu PDF.
- **Média**
    - **Log** – soubor PNG pro **velké** logo. Partnerské centrum použije k vytvoření dalších požadovaných velikostí loga. Případně je můžete později nahradit různými obrázky.
    - **Snímky obrazovky** – aspoň jeden až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Obrázky musí být 1280 × 720 pixelů, ve formátu PNG a obsahovat titulek.
    - **Videa** (volitelné) – až čtyři videa, která předvádí vaši nabídku. Zahrňte název, adresu URL pro YouTube nebo Vimeo a miniaturu PNG 1280 × 720 pixelů.

> [!Note]
> Vaše nabídka musí splňovat obecné [zásady certifikace komerčního tržiště](/legal/marketplace/certification-policies#100-general) , které se mají publikovat na komerčním webu Marketplace.

## <a name="additional-sales-opportunities"></a>Další prodejní příležitosti

Můžete si vybrat, jestli se chcete zúčastnit marketingových a prodejních kanálů podporovaných společností Microsoft. Při vytváření vaší nabídky v partnerském centru se zobrazí dvě karty na konci procesu pro **spolupráci s Microsoftem**. Tato možnost umožňuje prodejcům Microsoftu vzít v úvahu vhodné řešení IP při vyhodnocování potřeb zákazníků. Podrobné informace o tom, jak připravit vaši nabídku k vyhodnocení, najdete v tématu [možnost společného prodeje v partnerském centru](commercial-marketplace-co-sell.md) .

## <a name="next-steps"></a>Další kroky

Po zvážení výše popsaných položek plánování vyberte jednu z následujících možností (k dispozici také v obsahu na levé straně), abyste mohli začít vytvářet nabídku.

| Průvodce publikováním    | Poznámky  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) | Když vytváříte edici Enterprise, nejprve si prostudujte tyto další [postupy a pokyny pro publikování](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source). |
| [Dynamics 365 for Business Central](partner-center-portal/create-new-business-central-offer.md) |   |
| [Dynamics 365 pro služby Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) | Nejprve si přečtěte tyto další [postupy a pokyny pro publikování](/dynamics365/customer-engagement/developer/publish-app-appsource). |
| [Power BI](/partner-center-portal/create-power-bi-app-offer.md) | Nejprve si přečtěte tyto další [postupy a pokyny pro publikování](/power-bi/developer/office-store). |
|
