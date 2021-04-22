---
title: Konfigurace informací o seznamu nabídky kontejnerů Azure na Microsoft AppSource
description: Nakonfigurujte podrobnosti seznamu nabídky kontejnerů Azure na Microsoft AppSource.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 0b9b96c4cbc2484597e51463a6b7a4ecfaf9e950
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892612"
---
# <a name="configure-azure-container-offer-listing-details"></a>Konfigurace podrobností seznamu nabídky kontejnerů Azure

Tato stránka umožňuje definovat podrobnosti nabídky, jako je název nabídky, popis, odkazy, kontakty, loga a snímky obrazovky.

> [!NOTE]
> Poskytují podrobnosti o seznamech nabídek jenom v jednom jazyku. Angličtina není nutná, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině]." Je také přijatelné poskytnout *užitečnou adresu URL pro odkaz* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

## <a name="marketplace-details"></a>Podrobnosti Marketplace

**Název** , který zde zadáte, se zákazníkům zobrazí jako název nabídky. Toto pole je předem vyplněno názvem, který jste zadali jako **alias nabídky** při vytváření nabídky, ale můžete ho změnit. Název:

- Může obsahovat symboly ochranných známek a autorských práv.
- Musí mít 50 znaků nebo méně.
- Nejde zahrnout emoji.

Zadejte krátký popis vaší nabídky pro **Shrnutí výsledků hledání** (až 100 znaků). Tento popis se dá použít ve výsledcích hledání na webu Marketplace.

Zadejte **krátký popis** vaší nabídky, maximálně 256 znaků. Tato akce se zobrazí ve výsledcích hledání a na stránce podrobností vaší nabídky.

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Použijte značky HTML k formátování popisu, aby bylo lépe zajímavější. Seznam povolených značek najdete v tématu [podporované značky HTML](supported-html-tags.md).

Zadejte webovou adresu (URL) zásad ochrany osobních údajů vaší organizace. Ujistěte se, že vaše nabídka vyhovuje předpisům a zákonům o ochraně osobních údajů. Na svém webu musíte taky publikovat platné zásady ochrany osobních údajů.

## <a name="useful-links"></a>Užitečné odkazy

Poskytněte dodatečné online dokumenty o vaší nabídce. Můžete přidat až 25 odkazů. Chcete-li přidat odkaz, vyberte **+ Přidat odkaz** a potom vyplňte následující pole:

- **Název** – zákazníci uvidí na stránce s podrobnostmi o vaší nabídce.
- **Odkaz** (URL) – zadejte odkaz pro zákazníky k zobrazení online dokumentu. Odkaz musí začínat na http://nebo https://.

### <a name="contact-information"></a>Kontaktní informace

Zadejte jméno, e-mail a telefonní číslo pro kontakt na **technickou podporu**, **technický kontakt** a **program poskytovatele Cloud Solution Provider** . Tyto informace se zákazníkům nezobrazují, ale budou k dispozici pro společnost Microsoft a mohou být poskytovány partnerům CSP.

V části **kontakt podpory** zadejte **web podpory** , kde se můžou zákazníci připojit k vašemu týmu podpory, kde jsou globální a Azure Government Azure (pokud jsou k dispozici).

## <a name="marketplace-media"></a>Média Marketplace

Poskytněte loga a obrázky, které se mají použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazaný obraz způsobí odmítnutí odeslání.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu, kterou používá Partnerské centrum.

### <a name="logos"></a>Značk

Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije k vytvoření dalších požadovaných velikostí. Volitelně můžete tuto položku nahradit jinou imagí později.

Tato loga se používají na různých místech v seznamu:

[!INCLUDE [logos-appsource-only](includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Screenshoty

Přidejte alespoň jeden (a až pět) snímků obrazovky, které ukazují, jak vaše nabídka funguje. Všechny snímky obrazovky musí mít 1280 x 720 pixelů a ve formátu PNG. Přidejte titulek pro každý snímek obrazovky.

### <a name="videos"></a>Videa

Přidejte si až pět volitelných videí, která předvádí vaši nabídku. Měly by se hostovat na externí službě video. Zadejte název, webovou adresu a miniaturu videa v obrazovém formátu PNG na 1280 x 720 pixelů.

Další materiály k seznamům Marketplace najdete v tématu [osvědčené postupy pro výpisy nabídky na webu Marketplace](gtm-offer-listing-best-practices.md).

Než budete pokračovat na další kartu v nabídce **vlevo, vyberte** možnost **Uložit koncept** .
<!-- #### Offer examples

The following examples show how the offer listing fields appear in different places of the offer.

This shows search results in Azure Marketplace:

[![Illustrates the search results in Azure Marketplace](media/azure-container/azure-create-7-search-results-mkt-plc-small.png)](media/azure-container/azure-create-7-search-results-mkt-plc.png#lightbox)

This shows the **Offer listing** page in Azure portal:

:::image type="content" source="media/azure-container/azure-create-8-offer-listing-portal.png" alt-text="Illustrates the Offer listing page in Azure portal.":::

This shows search results in Azure portal:

[![Illustrates the search results in Azure portal.](media/azure-container/azure-create-9-search-results-portal-small.png)](media/azure-container/azure-create-9-search-results-portal.png#lightbox) -->

## <a name="next-steps"></a>Další kroky

- [Nastavit nabízenou cílovou skupinu Preview](azure-container-preview-audience.md)
