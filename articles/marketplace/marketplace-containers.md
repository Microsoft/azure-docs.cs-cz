---
title: Průvodce publikováním pro nabídky kontejnerů v Azure Marketplace
description: Tento článek popisuje požadavky na publikování nabídek kontejneru v Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 2dd44706e77252d3d00721c0e1cda22707eb28c3
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107887898"
---
# <a name="plan-an-azure-container-offer"></a>Plánování nabídky kontejneru Azure

Nabídky Azure Container vám pomůžou publikovat image kontejneru do Azure Marketplace. Tento průvodce použijte k pochopení požadavků pro tento typ nabídky.

Nabídky Azure Container jsou nabídky transakcí, které se nasazují a účtují prostřednictvím Azure Marketplace. Možnost výpisu, kterou uživatel vidí, **teď získá**.

Pokud je vaše řešení image kontejneru Docker, která je nastavená jako instance kontejneru Azure na základě Kubernetes, použijte typ nabídky kontejneru Azure.

> [!NOTE]
> Instance kontejneru Azure je instance Docker, která nabízí nejrychlejší a nejjednodušší způsob, jak spustit kontejner v Azure, aniž by bylo nutné spravovat žádné virtuální počítače nebo přijmout službu vyšší úrovně. Instance kontejnerů se dají nasadit přímo do Azure nebo orchestrovat služba Azure Kubernetes Services nebo Azure Kubernetes Service Engine.  

## <a name="licensing-options"></a>Možnosti licencování

Toto jsou dostupné možnosti licencování pro Azure Container:

| Možnost licence | Proces transakce |
| --- | --- |
| Free | Seznamte se s vaší nabídkou pro zákazníky zdarma. |
| BYOL | Možnost Přineste si vlastní licencování umožní vašim zákazníkům přenášet do Azure existující softwarové licence.\* |
|

\* Jako vydavatel podporujete všechny aspekty licenčních licencí k softwaru, včetně (ale ne omezeného) objednávky, plnění, měření, fakturace, fakturace, platby a shromažďování.

## <a name="customer-leads"></a>Potenciální zákazníci

Když publikujete nabídku na komerční tržišti s partnerským centrem, budete ji chtít připojit k systému pro správu vztahů se zákazníky (CRM). To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt. Pokud chcete povolit testovací jednotku, je nutné připojení k CRM. v opačném případě je připojení k CRM volitelné. Partnerské centrum podporuje službu Azure Table, Dynamics 365 Customer Engagement, HTTPS Endpoint, Marketo a Salesforce.

## <a name="legal-contracts"></a>Právní smlouvy

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní kontrakt, který můžete použít pro vaše nabídky na komerčním webu Marketplace. Když nabízený software obdržíte na základě standardního kontraktu, zákazníci ho musí přečíst a přijmout jenom jednou a vy nemusíte vytvářet vlastní podmínky.

Místo standardní smlouvy se můžete rozhodnout, že budete chtít zadat vlastní podmínky a ujednání. Zákazníci musí tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

## <a name="offer-listing-details"></a>Podrobnosti o seznamu nabídek

> [!NOTE]
> Pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině), není nutné, aby byla v anglickém obsahu.

Abyste mohli snadněji vytvořit nabídku, připravte tyto položky předem. Všechny jsou požadovány s výjimkou případů, kde je uvedeno.

- **Název** – název se zobrazí jako název vaší nabídky na komerčním webu Marketplace. Název může být ve vaší společnosti. Nemůže obsahovat Emoji (Pokud se nejedná o symboly ochranné známky a copyrightu) a je omezený na 50 znaků.
- **Shrnutí výsledků hledání** – účel nebo funkce vaší nabídky jako jediná věta bez konců řádků v 100 znacích nebo méně. Používá se ve výsledcích hledání na komerčních seznamech Marketplace.
- **Krátký popis** – podrobnosti účelu nebo funkce nabídky, zapsané v prostém textu bez konců řádků. Tato stránka se zobrazí na stránce s podrobnostmi o vaší nabídce.
- **Popis** – tento popis se zobrazí v seznamech komerčního tržiště. Vezměte v úvahu zahrnutí hodnot do kategorií, klíčových výhod, zamýšlené uživatelské základny, jakékoli kategorie nebo přidružení oboru, možností nákupu v aplikaci, všech potřebných zveřejnění a odkazu na Další informace. Toto textové pole obsahuje ovládací prvky formátovaného textu, které umožňují pružnější použití popisu. Volitelně můžete použít značky HTML pro formátování.
- **Odkaz zásady ochrany osobních údajů** – adresa URL zásad ochrany osobních údajů vaší společnosti. Zodpovídáte za to, že vaše aplikace vyhovuje předpisům a zákonům o ochraně osobních údajů.
- **Užitečné odkazy** (volitelné): odkazy na různé prostředky pro uživatele vaší nabídky. Například fóra, nejčastější dotazy a poznámky k verzi.
- **Kontaktní informace**
  - **Kontakt na podporu** – jméno, telefon a e-mail, které partneři Microsoftu použijí, když vaši zákazníci otevřou lístky. Zadejte adresu URL vašeho webu podpory.
  - **Technický kontakt** – jméno, telefon a e-mail Microsoftu pro použití přímo v případě problémů s vaší nabídkou. Tyto kontaktní údaje nejsou uvedené na komerčním webu Marketplace.
  - **Kontakt programu CSP** (volitelné): jméno, telefon a e-mailovou adresu, pokud se přihlásíte k programu CSP, aby vás mohli kontaktovat s případnými dotazy. Do svých marketingových materiálů můžete také přidat adresu URL.
- **Média**
    - **Log** – soubor PNG pro **velké** logo. Partnerské centrum použije k vytvoření dalších požadovaných velikostí loga. Případně je můžete později nahradit různými obrázky.
    - **Snímky obrazovky** – aspoň jeden až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Obrázky musí být 1280 × 720 pixelů, ve formátu PNG a obsahovat titulek.
    - **Videa** (volitelné) – až čtyři videa, která předvádí vaši nabídku. Zahrňte název, adresu URL pro YouTube nebo Vimeo a miniaturu PNG 1280 × 720 pixelů.

> [!Note]
> Vaše nabídka musí splňovat obecné [zásady certifikace komerčního tržiště](/legal/marketplace/certification-policies#100-general) , které se mají publikovat na komerčním webu Marketplace.

## <a name="preview-audience"></a>Cílová skupina Preview

Cílová skupina Preview má přístup k vaší nabídce před publikováním živě v online obchodech, aby bylo možné otestovat kompletní funkce před publikováním v reálném čase. Na stránce **Preview cílovou skupinu** můžete definovat skupinu omezené verze Preview.

Můžete odesílat pozvánky do ID předplatných Azure. Přidejte až 10 ID ručně nebo importujte až 100 se souborem. csv. Pokud je vaše nabídka už živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování změn nebo aktualizací vaší nabídky.

## <a name="plans-and-pricing"></a>Plány a ceny

Nabídky kontejnerů vyžadují aspoň jeden plán. Plán definuje rozsah a omezení řešení. Můžete vytvořit několik plánů pro vaši nabídku, abyste svým zákazníkům poskytli různé možnosti technického a licencování. 

Kontejnery podporují dva modely licencování: bezplatný nebo Přineste si vlastní licenci (BYOL). BYOL znamená, že budete zákazníky účtovat přímo a společnost Microsoft vám nebude účtovat žádné poplatky. Microsoft předává jenom poplatky za využití infrastruktury Azure. Další informace najdete v tématu [Možnosti Transact](marketplace-commercial-transaction-capabilities-and-considerations.md)-The pro prodej na webu Marketplace.

## <a name="additional-sales-opportunities"></a>Další prodejní příležitosti

Můžete si vybrat, jestli se chcete zúčastnit marketingových a prodejních kanálů podporovaných společností Microsoft. Při vytváření vaší nabídky v partnerském centru se zobrazí dvě karty na konec procesu:

- **Prodávejte dál prostřednictvím CSP** – umožníte partnerům Microsoft Cloud poskytovatelům řešení (CSP), aby vaše řešení mohli dál prodávat jako součást sady. Další informace o tomto programu najdete v tématu [program Cloud Solution Provider](cloud-solution-providers.md).
- **Spoluprodejní pomocí Microsoftu** – Umožněte prodejcům Microsoftu, aby při vyhodnocování potřeb svých zákazníků brali v úvahu vhodné řešení IP pro vlastní prodej. Podrobnosti o způsobilosti společného prodeje najdete v tématu [požadavky na stav společného prodeje](/legal/marketplace/certification-policies). Podrobnosti o přípravě vaší nabídky k vyhodnocení najdete v tématu [možnost společného prodeje v partnerském centru](commercial-marketplace-co-sell.md).

## <a name="container-offer-requirements"></a>Požadavky na nabídky kontejneru

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Podpora modelu fakturace Free nebo BYOL. |
| Obrázek sestavený z souboru Dockerfile | Image kontejneru musí být založené na specifikaci image Docker a sestavené z souboru Dockerfile. Další informace o vytváření imagí Docker najdete v části "použití" tématu [souboru Dockerfile reference](https://docs.docker.com/engine/reference/builder/#usage). |
| Hostování v úložišti Azure Container Registry | Image kontejneru musí být hostované v úložišti Azure Container Registry. Další informace o práci s Azure Container Registry najdete v tématu [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |
| Označování obrázků | Image kontejneru musí obsahovat aspoň jednu značku (maximální počet značek: 16). Další informace o označování obrázku najdete na `docker tag` stránce s [dokumentací k dokovacímu](https://docs.docker.com/engine/reference/commandline/tag) serveru. |

## <a name="next-steps"></a>Další kroky

- [Příprava technických prostředků](azure-container-technical-assets.md)
