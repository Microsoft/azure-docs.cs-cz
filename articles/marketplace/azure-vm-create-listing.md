---
title: Konfigurace informací o seznamu nabídek virtuálních počítačů na Azure Marketplace
description: Přečtěte si, jak nakonfigurovat podrobnosti o seznamu nabídky virtuálních počítačů na Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 2af96c0c2e850d8e1be67c23e71aa677773c1e03
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283911"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>Postup konfigurace podrobností seznamu nabídky virtuálních počítačů

Na stránce **seznam nabídek** můžete definovat podrobnosti nabídky, jako je název nabídky, popis, odkazy a kontakty.

> [!NOTE]
> Vaše nabídka obsahu, jako je popis, dokumenty, snímky obrazovky a podmínky použití, nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v nástroji \<non-English language> ." Můžete také zadat adresu URL pro odkaz na web, který nabízí obsah v jiném jazyce, než který je použit v obsahu nabídky.

## <a name="marketplace-details"></a>Podrobnosti Marketplace

### <a name="name"></a>Název

Název, který zde zadáte, se zákazníkům zobrazí jako název seznamu nabídek. Toto pole se vyplní názvem, který jste zadali do pole **alias nabídky** při vytváření nabídky. Tento název můžete později změnit. Název:

- Může být ochranné známky. Můžete zahrnout symboly ochranné známky a autorských práv.
- Nemůže obsahovat více než 50 znaků.
- Nejde zahrnout emoji.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Zadejte krátký popis vaší nabídky, který se zobrazí ve výsledcích hledání Azure Marketplace. Může obsahovat až 100 znaků.

### <a name="long-summary"></a>Dlouhý souhrn

Zadejte delší popis nabídky, která se zobrazí ve výsledcích hledání Azure Marketplace. Může obsahovat až 256 znaků.

### <a name="description"></a>Popis

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Použijte značky HTML k formátování popisu, aby bylo lépe zajímavější. Seznam povolených značek najdete v tématu [podporované značky HTML](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Odkaz zásady ochrany osobních údajů

Zadejte webovou adresu (URL) zásad ochrany osobních údajů vaší organizace. Ujistěte se, že vaše nabídka vyhovuje předpisům a zákonům o ochraně osobních údajů. Na svém webu musíte taky publikovat platné zásady ochrany osobních údajů.

## <a name="useful-links"></a>Užitečné odkazy

Poskytněte dodatečné online dokumenty o vaší nabídce. Chcete-li přidat odkaz, vyberte možnost **Přidat odkaz**a poté vyplňte následující pole:

- **Název**: zákazníci uvidí jméno na stránce s podrobnostmi.
- **Odkaz (URL)**: zadejte odkaz, který zákazníkům umožní zobrazit online dokument.

## <a name="customer-support-links"></a>Odkazy na zákaznickou podporu

Poskytněte web podpory, kde se zákazníci můžou připojit k vašemu týmu podpory.

- Web Azure Global support
- Web podpory Azure Government

## <a name="partner-support-contact"></a>Kontakt na podporu partnerů

Poskytněte kontaktní informace partnerům Microsoftu, kteří se použijí, když vaši zákazníci otevřou lístek podpory. Tyto informace nejsou uvedené na Azure Marketplace.

- Název
- E-mail
- Rozložení

## <a name="engineering-contact"></a>Technický kontakt

Poskytněte kontaktní informace Microsoftu pro použití v případě problémů s vaší nabídkou, včetně problémů s certifikací. Tyto informace nejsou uvedené na Azure Marketplace.

- Název
- E-mail
- Rozložení

## <a name="azure-marketplace-media"></a>Azure Marketplace média

Poskytněte loga a obrázky, které se mají použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazaný obraz způsobí odmítnutí odeslání.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu, kterou používá Partnerské centrum.

### <a name="azure-marketplace-logos"></a>Loga Azure Marketplace

Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije toto k vytvoření **malého** a **středního** loga. Případně je můžete později nahradit různými obrázky.

- **Velký** (od 216 x 216 do 350 × 350 px, požadováno)
- **Střední** (90 x 90 px, volitelné)
- **Malý** (48 x 48 px, volitelné)

Tato loga se používají na různých místech v seznamu:

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Screenshoty

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Velikost každého snímku obrazovky musí být 1280 × 720 pixelů a ve formátu PNG. Každý snímek obrazovky musí obsahovat titulek.

### <a name="videos"></a>Videa

Přidejte až pět videí, která předvádí vaši nabídku. Videa by se měla hostovat na externí službě video. Zadejte název, webovou adresu a miniaturu videa v obrazovém formátu PNG na 1280 x 720 pixelů.

Další materiály k seznamům Marketplace najdete v tématu [osvědčené postupy pro výpisy nabídky na webu Marketplace](gtm-offer-listing-best-practices.md).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="next-steps"></a>Další kroky

- [Vytváření plánů](azure-vm-create-plans.md)
