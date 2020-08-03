---
title: Určení možnosti publikování – komerční tržiště Microsoftu
description: Tento článek popisuje kritéria způsobilosti a požadavky pro publikování nabídek Microsoft AppSource a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 37647a9591c0f686e4fc3f1fd858baa46e01f7ac
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498068"
---
# <a name="determine-your-publishing-option"></a>Určení možnosti publikování

Možnost publikování, kterou si zvolíte pro vaši nabídku, se vztahuje přímo na požadavky na způsobilost i na GTM výhody pro komerční tržiště. Důležitější je, že výběr možnosti publikování a typu nabídky definuje způsob, jakým uživatelé budou pracovat s vaší nabídkou komerčního tržiště.

Pokud chcete konfigurovat vaši nabídku, budete muset pochopit následující klíčové koncepty komerčního tržiště: možnosti publikování, typy nabídek a konfigurace a volání akce, která se řídí tím, jak a kde se vaše nabídka zobrazuje na komerčním webu Marketplace prodejní místa.

V tomto článku se dozvíte:

- Určení vhodného prezentace pro vaše řešení
- Které možnosti publikování a volání akce jsou k dispozici v každém prezentace
- Které typy nabídek jsou k dispozici pro jednotlivé možnosti publikování

## <a name="commercial-marketplace-publishing-options"></a>Možnosti publikování na komerční web Marketplace

V následující tabulce jsou uvedeny možnosti publikování pro typy nabídek v Microsoft AppSource a Azure Marketplace.

|   | **Seznam (kontakt)**  | **Seznam (zkušební verze)**  | **Free** | **BYOL** | **Transakce**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Virtuální počítač** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Aplikace Azure (pro více virtuálních počítačů)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Obrázek kontejneru** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Modul IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Spravované služby** |  |  |  | Azure Marketplace |   |
| **Konzultační služby** | Prodejní místa |  |  |  |   |
| **Aplikace SaaS** | Prodejní místa | Prodejní místa | Prodejní místa |  | Prodejní místa * |
| **Aplikace Microsoft 365** | AppSource | AppSource |  |  | AppSource * *  |
| **Doplněk pro Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; SaaS aplikace v režimu Transact v Microsoft AppSource jsou momentálně pouze kreditními kartami.

Nabídky &#42;&#42; Microsoft 365 jsou zdarma a je možné je můžou finančně zhodnocovat prostřednictvím SaaS nabídky jako licenční služba. Další informace najdete v tématu [monetizovat vašeho doplňku pro Office 365 prostřednictvím komerčního tržiště Microsoftu](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-call-to-action"></a>Zvolit volání akce

Dostupné možnosti publikování nabízejí odlišnou zákaznickou zapojení a zároveň vám dávají přístup k výhodám sdílení potenciálních zákazníků a [komerčních webů na webu Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Všimněte si volání akce, které odpovídá možnosti publikování:

| **Možnost publikování**    | **Popis**  |
| :------------------- | :-------------------|
| **Seznam** | Jednoduchý seznam vaší aplikace nebo služby, který umožňuje uživateli na webu Marketplace požádat o připojení k zákazníkovi prostřednictvím **kontaktních** hovorů. |
| **Zkušební verze** | Pomocí komerčního tržiště Vylepšete možnosti zjistitelnosti a automatizujte zřizování zkušebního prostředí vašeho řešení. umožníte tak potenciálním uživatelům používat vaše SaaS, IaaS nebo prostředí v aplikaci od Microsoftu zdarma po určitou dobu, než se nakupují. Možnost volání na akci, která se používá pro možnost publikování zkušební verze, je buď **bezplatná zkušební verze** , nebo **testovací jednotka**. |
| **BYOL** | Pomocí komerčního tržiště Vylepšete zjistitelnost a automatizujte zřizování vašich řešení a dokončete finanční transakci samostatně. Typy nabídek BYOL jsou ideální pro migrace z místního prostředí do cloudu. Volání akce je **teď Get**.
| **Transakce** | Nabídky Transact jsou prodávány prostřednictvím komerčního tržiště. Společnost Microsoft zodpovídá za fakturaci a kolekce. Volání akce je **teď Get**.|

> [!Note]
> Při použití možnosti publikování v transakčním systému je důležité pochopit důležité informace o cenách, fakturaci, fakturaci a výběru před výběrem typu nabídky a vytvořením nabídky. Pokud se chcete dozvědět víc, přečtěte si článek [Možnosti Transact](./marketplace-commercial-transaction-capabilities-and-considerations.md)-The pro obchod na webu.

## <a name="selecting-a-storefront"></a>Výběr prezentace

Každý prezentace slouží jako jedinečné požadavky zákazníků a cílí na konkrétní cílové skupiny. Typ vaší nabídky, možnosti jazyka Transact a kategorie určí, kde bude vaše nabídka publikována. Kategorie a podkategorie jsou namapovány na jednotlivé prezentacey na základě cílové cílové skupiny:

**Microsoft AppSource** cílí obchodním uživatelům, kteří hledají obchodní nebo Oborová řešení a konzultační služby pro Dynamics 365, Microsoft 365 a Power Platform.

**Azure Marketplace** cílí na odborníky na IT a vývojáře, kteří hledají řešení vytvořená pro nebo v Azure, a také konzultační služby, které urychlují používání Azure.

Vyberte kategorii a podkategorii, které nejlépe zarovnejte s cílovou cílovou skupinou. Například Brána Firewall webových aplikací by měla být publikována do Azure Marketplace v kategorii zabezpečení jako zamýšlená cílová skupina odborníky na IT. Aplikace pro správu smluv by se měla místo toho publikovat do AppSource v kategorii prodej, protože zamýšlená cílová skupina je firemním uživatelům. Výběr nesprávné kategorie nebo podkategorie může mít za následek publikování vaší nabídky na nesprávné prezentace.

### <a name="publishing-to-both-storefronts-saas-offers-only"></a>Publikování do obou prodejní místa (jenom nabídky SaaS)

Nabídky SaaS mohou být publikovány do Azure Marketplace nebo AppSource. Pokud je vaše nabídka SaaS určená pro technickou cílovou skupinu (Azure Marketplace *) i pro* obchodní cílovou skupinu (AppSource), vyberte kategorii nebo podkategorii, které platí pro jednotlivé prezentace. Nabídky publikované do obou prodejní místa by měly mít popozici hodnoty, která se rozšiřuje na odborníky v oblasti IT *a* obchodní uživatele.

> [!IMPORTANT]
> Nabídky SaaS s měřenou fakturací jsou k dispozici prostřednictvím Azure Marketplace a Azure Portal. SaaS nabízí pouze soukromé plány, které jsou k dispozici prostřednictvím Azure Portal.

| Měřené fakturace | Veřejný plán | Soukromý plán | K dispozici v: |
|---|---|---|---|
| Ano             | Ano         | No           | Azure Marketplace a Azure Portal |
| Ano             | Ano         | Ano          | Azure Marketplace a Azure Portal * |
| Ano             | No          | Ano          | Pouze Azure Portal |
| No              | No          | Ano          | Pouze Azure Portal |

&#42; soukromý plán této nabídky bude k dispozici pouze prostřednictvím Azure Portal

Zákazníci v Azure Portal zakoupí například nabídku s měřením fakturace a soukromým plánem (žádný veřejný plán). Přečtěte si další informace o [privátních nabídkách na komerčním webu Microsoft Marketplace](private-offers.md).

### <a name="categories"></a>Kategorie

Kategorie a podkategorie jsou namapovány na jednotlivé prezentacey založené na cílové cílové skupině. Vyberte kategorie a podkategorie, které nejlépe odpovídají vaší nabídce a zamýšlené cílové skupině. Můžete vybrat:

- Aspoň jedna a až dvě kategorie. Máte možnost zvolit primární a sekundární kategorii.
- Až dvě podkategorie pro každou primární nebo sekundární kategorii. Pokud nevyberete žádnou podkategorii, vaše nabídka bude i nadále zjistitelná v rámci vybrané kategorie.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Další kroky

- Jakmile se rozhodnete pro možnost publikování, budete připraveni [Vybrat typ nabídky](./publisher-guide-by-offer-type.md) , který se bude používat k prezentaci vaší nabídky.
- Pokud chcete dokončit výběr a konfiguraci vaší nabídky, přečtěte si požadavky na způsobilost v části Možnosti publikování podle typu nabídky.
- Prohlédněte si vzory publikování podle prezentace, kde najdete příklady, jak vaše řešení mapuje typ a konfiguraci nabídky.
