---
title: Vlastnosti marketingu
description: Popis různých marketingových polí shromážděných na portálu a jak se budou zobrazovat v katalogu zařízení s certifikací Azure
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: template-concept
ms.openlocfilehash: 45ecab18451ca0915ee3891278db47feb7469915
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969235"
---
# <a name="marketing-properties"></a>Vlastnosti marketingu

V procesu [přidávání podrobností o zařízení](tutorial-02-adding-device-details.md)budete muset zadat marketingové informace, které se zobrazí v [katalogu zařízení s certifikací Azure](https://devicecatalog.azure.com). Tyto informace se shromažďují v rámci procesu odeslání certifikace na portálu zařízení s certifikací Azure a budou se používat jako parametry filtru v katalogu. Tento článek poskytuje mapování mezi poli shromážděnými na portálu k tomu, jak se zobrazují v katalogu. Po přečtení tohoto článku by partneři měli lépe pochopit, jaké informace je třeba poskytnout během procesu certifikace, aby co nejlépe představovaly jejich produkt v katalogu.

![PDP – přehled](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Dlaždice produktu katalog zařízení s certifikací Azure

Návštěvníci tohoto katalogu budou nejdřív pracovat s vaším zařízením jako dlaždice produktového katalogu na stránce hledání. Tato akce poskytne základní přehled o zařízení a certifikacech, které byly uděleny.

![Šablona dlaždice produktu](./media/concepts-marketing/product-tile.png)

| Pole | Description                  | Kam přidat na portálu                |
|---------------|-------------------------|----------------------------------|
| Název zařízení | Veřejný název certifikovaného zařízení         | Karta základy podrobností zařízení|
| Název firmy| Veřejný název vaší společnosti  | Nelze upravovat na portálu. Extrahování z názvu účtu MPN |
| Fotka produktu  | Obrázek zařízení s minimálním rozlišením 200p × 200p  | Podrobnosti o marketingu |
| Klasifikace certifikace  | Povinná jmenovka certifikace zařízení s certifikací Azure a volitelné certifikační štítky  | Karta základy podrobností o zařízení Musí projít odpovídajícím testováním v části Connect & test. |

## <a name="product-description-page-information"></a>Informace o stránce s popisem produktu

Jakmile zákazník klikne na dlaždici zařízení na stránce hledání v katalogu, přejdou na stránku s popisem produktu vašeho zařízení. V takovém případě budou nalezeny hromadné informace poskytované během procesu certifikace.

Horní část stránky s popisem produktu zvýrazňuje klíčové charakteristiky, některé z nich již byly použity pro dlaždici produktu.

![PDP horní pruhový](./media/concepts-marketing/pdp-top.png)

| Pole | Description                  | Kam přidat na portálu                |
|---------------|-------------------------|----------------------------------|
| Třída zařízení | Klasifikace typu formuláře a primárního účelu vašeho zařízení ([Další informace](./resources-glossary.md))       | Karta základy podrobností zařízení|
| Typ zařízení | Klasifikace zařízení na základě připravenosti implementace ([Další informace](./resources-glossary.md)) | Karta základy podrobností zařízení |
| Geografická dostupnost | Oblasti, ve kterých je vaše zařízení k dispozici k nákupu  | Podrobnosti o marketingu |
| Operační systémy  | Operační systémy, které vaše zařízení podporuje  | Karta Podrobnosti o produktu v podrobnostech o zařízení |
| Cílové odvětví  | Přední 3 odvětví, pro které je vaše zařízení optimalizované  | Podrobnosti o marketingu |
| Popis produktu  | Textové pole, ve kterém můžete napsat svůj popis vašeho produktu. To může zachytit podrobnosti, které nejsou uvedené na portálu, nebo přidat další kontext pro výhody používání vašeho zařízení. | Podrobnosti o marketingu|

Zbývající část stránky se zaměřuje na zobrazení technických specifikací vašeho zařízení ve formátu tabulky, který vašemu zákazníkovi pomůže lépe porozumět vašemu produktu. Pro usnadnění práce jsou zde uvedeny také informace zobrazené v horní části stránky. Zbytek tabulky je doplněn součástmi určenými na portálu.

![PDP – dolní stránka](./media/concepts-marketing/pdp-bottom.png)

| Pole | Description                  | Kam přidat na portálu                |
|---------------|-------------------------|----------------------------------|
| Typ součásti | Klasifikace typu formuláře a primárního účelu vašeho zařízení ([Další informace](./resources-glossary.md))       | Podrobnosti o produktu podrobnosti o zařízení|
| Název součásti| Název součásti, kterou popisujete | Podrobnosti o produktu podrobnosti o zařízení |
| Další informace o komponentách | Další specifikace hardwaru, jako jsou například zahrnuté senzory, konektivita, akcelerátory atd.  | Další informace o komponentách v podrobnostech o zařízení ([Další](./how-to-using-the-components-feature.md)informace)  |
| Text závislosti zařízení | Text poskytovaný partnerem, který popisuje různé závislosti, které produkt vyžaduje pro připojení k Azure ([Další informace](./how-to-indirectly-connected-devices.md))   | Oddíl zákaznických komentářů na kartě závislosti v podrobnostech o zařízení |
| Odkaz na závislosti zařízení  | Odkaz na certifikované zařízení, které váš aktuální produkt vyžaduje  | Karta závislosti v podrobnostech o zařízení |

## <a name="shop-links"></a>Propojení dílny
K dispozici na dlaždici produkt a na stránce s popisem produktu je tlačítko koupit. Když na něj klikne zákazník, otevře se okno, které jim umožní vybrat distributora (můžete vytvořit seznam až 5 distributorů). Po výběru se zákazník přesměruje na adresu URL poskytnutou partnerem.

![Obrázek překryvných oken dílenského prostředí](./media/concepts-marketing/shop.png)

| Pole | Description                  | Kam přidat na portálu                |
|---------------|-------------------------|----------------------------------|
| Název distributora | Jméno distributora, který prodává váš produkt | Podrobnosti o marketingu|
| Získat zařízení| Odkaz na externí web pro zákazníka k nákupu zařízení (nebo vyžádání nabídky od distributora) To může být stejné jako stránka výrobce, pokud je distributor stejný jako výrobce zařízení. Pokud není k dispozici stránka nákupu, přesměruje se na stránku distributora, aby ji zákazník kontaktoval přímo.  | Adresa URL stránky distributora v podrobnostech o marketingu Pokud není k dispozici žádná nákupní stránka, odkaz bude ve výchozím nastavení nastaven na adresu URL distributora v podrobnostech marketingu. |

## <a name="external-links"></a>Externí odkazy
Také je součástí stránky popis produktu odkazy, které odkazují na weby nebo soubory poskytované partnerskými servery, které zákazníkům pomohou lépe pochopit produkt. Zobrazují se v horní části stránky pod textem popisu produktu. Zobrazené odkazy se budou lišit pro různé typy zařízení a certifikační programy.

| Odkaz | Description                  | Kam přidat na portálu                |
|---------------|-------------------------|----------------------------------|
| Příručka Začínáme * | Soubor PDF s pokyny pro uživatele k připojení a používání zařízení se službami Azure | Přidání příručky Začínáme na portálu|
| Stránka výrobce *|Odkaz na stránku výrobce Tato stránka může být konkrétní produktovou stránkou pro vaše zařízení nebo na domovskou stránku společnosti, pokud není k dispozici marketingová stránka. | Marketingová stránka výrobce v podrobnostech o marketingu |
| Model zařízení | Veřejné modely DTDL pro řešení IoT technologie Plug and Play  | Nelze upravovat na portálu. Model zařízení musí být nahrán do ([úložiště veřejného modelu](https://aka.ms/modelrepo) ).  |
| Zdrojový kód zařízení | Adresa URL zdrojového kódu zařízení pro typy zařízení sady dev Kit| Karta základy podrobností zařízení  |

 **Požadováno pro všechna publikovaná zařízení*

## <a name="next-steps"></a>Další kroky
Teď, když máte přehled o tom, jak používáme informace, které zadáte během certifikace, teď můžete svoje zařízení certifikovat. Zahajte svůj certifikační projekt nebo přejděte zpět do části Podrobnosti o zařízení a přidejte své informace o svém vlastním marketingovém oddělení.

- [Zahájit svou cestu k certifikaci](./tutorial-00-selecting-your-certification.md)
- [Přidání podrobností o zařízení](./tutorial-02-adding-device-details.md)
