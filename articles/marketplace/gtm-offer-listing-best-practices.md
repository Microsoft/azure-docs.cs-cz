---
title: Nabídka s doporučenými postupy – komerční tržiště Microsoftu
description: Přečtěte si o osvědčených postupech pro uvedení na trh pro váš Microsoft AppSource a Azure Marketplace nabídek.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/06/2020
ms.openlocfilehash: efe60079814958116d8042991e1cfd6e2f6f71f5
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213887"
---
# <a name="offer-listing-best-practices"></a>Osvědčené postupy nabídky

Tento článek obsahuje návrhy na vytváření a poutavé nabídky Microsoftu pro komerční tržišti. Následující tabulky popisují osvědčené postupy pro dokončení informací nabídky v partnerském centru. Analýzu toho, jak vaše nabídky provádí, najdete na [řídicím panelu přehledy Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights) v partnerském centru. 

## <a name="storefront-offer-details"></a>Podrobnosti nabídky prezentace

| Nastavení prezentace | Osvědčený postup |
|:--- |:--- |  
| Název nabídky | V případě aplikací zadejte jasný název, který obsahuje klíčová slova pro hledání, která zákazníkům pomůžou vyhledat vaši nabídku. <br> <br> V případě konzultačních služeb použijte tento formát: [název nabídky: [doba trvání] [typ nabídky] (například contoso: implementace 2-Week). |
| Popis nabídky | Zadejte jasný popis, který popisuje jeho hodnotu v prvních několika větách na pozici vaší nabídky.  Pamatujte, že tyto věty mohou být použity ve výsledcích vyhledávacího stroje. Základní komponenty pro vaši polohu hodnoty by měly zahrnovat: <ul> <li>Popis produktu nebo řešení. </li> <li> Uživatel, který je výhodou z produktu nebo řešení. </li> <li> Potřebu zákazníka nebo bolesti adres produktů nebo řešení. </li> </ul> <br> Pokud je to možné, využijte standardní oborové slovníace nebo používání na základě výhod.  Nespoléhá se na funkce a funkce k prodeji vašeho produktu.  Místo toho se zaměřte na hodnotu, kterou doručujete. <br> <br> V případě servisních konzultačních služeb jasně uveďte profesionální službu, kterou poskytnete. |

> [!IMPORTANT]
> Ujistěte se, že název vaší nabídky a popis nabídky se řídí **[zásadami Microsoftu a pokyny pro značky](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** a dalšími relevantními pokyny pro konkrétní produkt, které se vztahují na ochranné známky společnosti Microsoft a na názvy softwaru, produktů a služeb společnosti Microsoft.

## <a name="storefront-listing-details"></a>Podrobnosti o prezentace seznamu

Kategorie a odvětví pro různé prezentace budou platit pro různé typy nabídek.

| Prezentace | Kategorie <br>od prezentace | Kategorie <br>od prezentace | Obory <br> pro AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| **Typ nabídky**   |  **Azure Marketplace**  | **AppSource**  |
| Aplikace Azure | X | |
| Kontejner | X | |
| Konzultační služby | | | X |
| Dynamics 365 Customer Engagement & Power Platform | | X | X |
| Správa dodavatelských řetězců & finance Dynamics 365 | | X | X | 
| Dynamics 365 Business Central | | X | X |
| Moduly IoT Edge | X | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Virtuální počítač Azure |  X |    |

### <a name="categories"></a>Kategorie

Microsoft AppSource a Azure Marketplace jsou dvě různé prodejní místa, které slouží jako jedinečné požadavky zákazníků. Azure Marketplace se zaměřuje na odborníky na IT a vývojáře, kteří vytvářejí řešení v Azure.  Microsoft AppSource cílení firemních uživatelů, kteří hledají obchodní a/nebo SaaS aplikace, doplňky pro Dynamics 365, Microsoft 365 doplňky a aplikace Power Platform.

Kategorie a podkategorie jsou namapovány na jednotlivé prezentacey založené na cílové cílové skupině. Vaše nabídka bude publikována do Microsoft AppSource nebo Azure Marketplace v závislosti na typu nabídky, možnosti transakce nabídky a výběru kategorie/podkategorie. 

Vyberte kategorie a podkategorie, které nejlépe zarovnejte s vaší nabídkou a zamýšlenou cílovou skupinou. Můžete vybrat:

* Až dvě (2) kategorie, včetně primární a sekundární kategorie (volitelné).
* Až dvě (2) podkategorie pro každou primární nebo sekundární kategorii. Pokud není vybraná žádná podkategorie, nabídne se vám nabídka jenom pro vybranou kategorii.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

**Důležité: SaaS nabídky a doplňky pro Microsoft 365**

Konkrétní podrobnosti o tom, jak mohou možnosti jazyka Transact ovlivnit zobrazení a nákup zákazníků z webu Marketplace, najdete v [příručce podle typu nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-overview) . V případě nabídek SaaS, funkce transakce nabídky a výběr kategorie určí prezentace, kde se vaše nabídka publikuje:

| Nabídka SaaS    | Nabídka SaaS   | Nabídka SaaS  | Nabídka SaaS   | Nabídka SaaS   | Nabídka SaaS   | Nabídka SaaS    | Příslušné prezentace | Příslušné prezentace |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| Měřené fakturace | Microsoft 365 Doplňky | Kontaktujte mě | Transact (minimálně 1 plán) | Pouze soukromý plán | Pouze veřejný plán | Veřejné & soukromé plány | AppSource | Azure Marketplace |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | ×<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | ×<sup>1</sup> | ×<sup>1</sup> |
|  |  |  | X |  |  | X | ×<sup>1</sup> | ×<sup>1, 2</sup> |
|  |  | X |  |  |  |  | ×<sup>1</sup> | ×<sup>1</sup> | 

1. V závislosti na kategoriích/podkategoriích a průmyslových výběrech
2. Nabídky s privátními plány budou publikovány na Azure Portal


### <a name="industries"></a>Obory

Výběr v odvětví se vztahuje jenom na nabídky publikované v AppSource a konzultačních službách publikovaných v Azure Marketplace.  Vyberte možnost odvětví a/nebo vertikálně, pokud vaše nabídka adresuje specifické potřeby a v popisu nabídky zavolá funkce specifické pro konkrétní obor. Můžete vybrat až dva (2) obory a dvě (2) svislého počtu vybraných pro jednotlivé obory.

>[!Note]
>Pro konzultační nabídky služby v Azure Marketplace neexistují žádné obory svislé.

| **Obory** |  **Svislé čáry** |
| :------------------- | :----------------|
| **Zemědělství** | |
| **Architektura & konstrukce** | |
| **Automobilový průmysl** | |
| **Distribuce** | Hromadné <br> Odeslání balíčku & balíčků |  
| **Vzdělávání** | Vyšší vzdělávání <br> Primární & sekundární edu/K-12 <br> Knihovny & muzeí |
| **Finanční služby** | Bankovní & kapitálové trhy <br> Pojišťovací | 
| **Státní správa** |  Obrana & Intelligence <br> Civilní samospráva <br> Veřejné zabezpečení & spravedlnosti |
| **Zdravotnictví** | Plátce stavu <br> Poskytovatel stavu <br> Pharmaceuticals | 
| **Pohostinství & cestování** | Cestovní & Transport <br> Hotely & volný čas <br> Restaurace & potravinové služby | 
| **Prostředky výrobního &** | Chemický & Agrochemical <br> Diskrétní výroba <br> Energetický sektor | 
| **Komunikace s médii &** | Media & Entertainment <br> Telekomunikace | 
| **Ostatní odvětví veřejného sektoru** | Lesní & rybolov <br> Pro neziskový sektor | 
| **Profesionální služby** | Profesionální služby partnerů <br> Právní informace | 
| **Real Estate** | |

Obor jenom pro Microsoft AppSource:

| **Obor** |  **Svislé čáry** |
| :------------------- | :----------------|
| **Maloobchodní & spotřební zboží** | Maloobchodní <br> Spotřební zboží |

### <a name="applicable-products"></a>Příslušné produkty

Vyberte příslušné produkty, ve kterých aplikace pracuje, aby se nabídka zobrazovala v části vybrané produkty v AppSource.

### <a name="search-keywords"></a>Hledaná klíčová slova

Klíčová slova můžou zákazníkům pomáhat při hledání vaší nabídky. Identifikujte nejdůležitější klíčová slova pro vyhledávání pro vaši nabídku, zahrňte je do souhrnu a popisu nabídky a také do části klíčové slovo v části Podrobnosti seznamu nabídky.

## <a name="storefront-marketing-details"></a>Podrobnosti o marketingu prezentace
| Nastavení prezentace | Osvědčený postup |
|:--- |:--- |  
| Logo nabídky (formát PNG, 48 &nbsp; &times; &nbsp; 48): stránka hledání | Návrh a optimalizace loga pro digitální média:<br>Nahrajte logo ve formátu PNG na stránku vyhledávání vaší nabídky. |
| Logo nabídky (formát PNG, 216 &nbsp; &times; &nbsp; 216): Stránka s podrobnostmi aplikace | Návrh a optimalizace loga pro digitální média:<br>Nahrajte logo ve formátu PNG na stránku se seznamem podrobností aplikace vaší nabídky. |
| Dokumenty "Další informace" | Do části "Další informace" zahrňte podporu prodejních a marketingových prostředků: <ul> <li> dokumenty White Paper, </li> <li> brožury, </li> <li> kontrolní seznamy nebo </li> <li> Prezentace aplikace PowerPoint.</li> </ul>Uloží všechny soubory ve formátu PDF. Tady by měl být váš cíl informovat zákazníky a neprodávat jim. <br><br>Přidejte odkaz na cílovou stránku aplikace do všech dokumentů a přidejte parametry adresy URL, které vám pomohou sledovat návštěvníky a zkušební verze. |
| Videa: AppSource, konzultační služby a SaaS nabídky | Nejsilnější videa sdělují hodnotu vaší nabídky ve formě mluveného komentáře:<ul> <li> Udělejte zákazníka, ne firmu, Hero příběh. </li> <li> Vaše video by mělo řešit hlavní výzvy a cíle vašeho cílového zákazníka. </li> <li> Doporučená délka: 60-90 sekund.</li> <li> Zahrňte klíčová slova pro hledání, která používají název videí. </li> <li> Zvažte přidání dalších videí, jako jsou postupy, začátky nebo zákaznické reference. </li> </ul> |
| Snímky obrazovky (1280 &nbsp; &times; &nbsp; 720) | Přidat až pět snímků obrazovky:<br>Do názvů souborů zahrňte klíčová slova pro hledání klíčů. |

## <a name="link-to-your-offer-page-from-your-website"></a>Odkaz na stránku nabídky z webu

Když propojíte AppSource nebo Azure Marketplace BADGE na vašem webu s vaším výpisem na komerčním webu Marketplace, můžete podporovat silné analýzy a vytváření sestav, a to tak, že na konci adresy URL zadáte následující parametry dotazu:
* **Src**: zahrnuje zdroj, ze kterého je směrován provoz do AppSource (například web, LinkedIn nebo Facebook).
* **mktcmpid**: vaše ID marketingové kampaně, které může obsahovat až 16 znaků v libovolné kombinaci písmen, číslic, podtržítka a spojovníků (například *blogpost_12*).

Následující příklad adresy URL obsahuje oba předchozí parametry dotazu: `https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

Přidáním parametrů k AppSource adrese URL si můžete prohlédnout efektivitu kampaně na řídicím panelu Analytics v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace/).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [výhodách komerčního tržiště](./gtm-your-marketplace-benefits.md).

Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , abyste mohli vytvořit a nakonfigurovat vaši nabídku.

