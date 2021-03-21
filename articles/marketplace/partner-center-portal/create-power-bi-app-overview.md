---
title: Přehled vytváření aplikací Power BI – Microsoft AppSource
description: Tento článek popisuje kroky vysoké úrovně pro publikování Power BI aplikace do Microsoft AppSource. K dispozici jsou také technické a obchodní požadavky, které vaše aplikace Power BI musí splňovat, aby bylo možné je publikovat na komerčním webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 04/02/2020
ms.openlocfilehash: b02c5bbf2e6accbea9c7a33dc26c4e89e46bb697
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97679136"
---
# <a name="power-bi-app-creation-overview"></a>Přehled vytváření aplikací Power BI

Tento článek vysvětluje, jak publikovat aplikaci Power BI do Microsoft [AppSource](https://appsource.microsoft.com/). Balíček aplikace Power BI přizpůsobitelné obsahu, včetně datových sad, sestav a řídicích panelů. Tuto aplikaci pak můžete použít s jinými Power BI platformami pomocí AppSource, provádět úpravy a přizpůsobení, které vývojář povoluje, a propojit ho s vašimi vlastními daty.

## <a name="publishing-benefits"></a>Výhody publikování

Výhody publikování na komerčním webu Marketplace:

- Propagujte svoji společnost pomocí značky Microsoftu.
- Aplikace může dosáhnout více než 100 000 000 Microsoft 365 a uživatelů Dynamics 365 na AppSource a více než 200 000 organizací prostřednictvím Azure Marketplace.
- Dostanou vysoce kvalitní zájemce z těchto tržišť.
- Povýšit vaše služby na pole Microsoftu a prodejní týmy.

## <a name="overview"></a>Přehled

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Přehled kroků pro publikování Power BI aplikace" border="false":::

Toto jsou klíčové kroky pro publikování:

1. Vytvořte aplikaci v Power BI. Zobrazí se odkaz pro instalaci balíčku, který je hlavním technickým prostředkem pro tuto nabídku. Před vytvořením nabídky v partnerském centru odešlete testovací balíček do předprodukčního prostředí. Podrobnosti najdete v tématu [co jsou aplikace Power BI?](/power-bi/service-template-apps-overview)
2. Přidejte marketingové materiály, jako je například oficiální název, popis a loga.
3. Zahrňte právní a podpůrné dokumenty nabídky, jako jsou podmínek použití, zásady ochrany osobních údajů, zásady podpory a uživatelská pomoc.
4. Vytvoření nabídky – pomocí partnerského centra můžete upravit podrobnosti, včetně popisu nabídky, marketingových materiálů, právních informací, informací o podpoře a specifikace assetu.
5. Odešlete ji k publikování.
6. Monitorujte proces v partnerském centru, kde AppSource týmu testuje, ověřuje a osvědčuje vaši aplikaci.
7. Po certifikaci si aplikaci přečtěte v testovacím prostředí a uvolněte ji. Tato možnost zobrazí seznam na AppSource (bude to živě).
8. V Power BI odešlete balíček do produkčního prostředí. Podrobnosti najdete v tématu [Správa verze aplikace Power BI](/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Než začnete

Přečtěte si níže uvedené odkazy, které poskytují šablony, tipy a ukázky.

- [Vytvoření aplikace Power BI](/power-bi/service-template-apps-create)
- [Tipy pro vytváření Power BIch aplikací](/power-bi/service-template-apps-tips)
- [ukázky](/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Požadavky

Aby bylo možné publikovat na komerčním webu Marketplace, musí vaše nabídka aplikace Power BI splňovat následující technické a obchodní požadavky.

### <a name="technical-requirements"></a>Technické požadavky

Hlavním technickým prostředkem, který budete potřebovat, je [Power BI aplikace](/power-bi/connect-data/service-template-apps-overview). Jedná se o kolekci primárních datových sad, sestav nebo řídicích panelů. Zahrnuje také volitelné připojené služby a vložené datové sady, dříve označované jako [balíček obsahu](/power-bi/service-organizational-content-pack-introduction). Další informace o vývoji tohoto typu aplikace najdete v tématu [co jsou aplikace Power BI?](/power-bi/connect-data/service-template-apps-overview).

#### <a name="get-an-installation-web-address"></a>Získat instalační webovou adresu

Power BI aplikaci můžete vytvořit pouze v rámci [Power BI](https://powerbi.microsoft.com/) prostředí.

1. Přihlaste se pomocí [licence Power BI pro](/power-bi/service-admin-purchasing-power-bi-pro).
2. Vytvoření a otestování aplikace v Power BI.
3. Když obdržíte webovou adresu instalace aplikace, přidejte ji na stránku **technické konfigurace** v partnerském centru.

Po vytvoření a otestování aplikace v Power BI uložte webovou adresu instalace aplikace, protože ji budete potřebovat k [Vytvoření nabídky aplikace Power BI](create-power-bi-app-offer.md).

### <a name="business-requirements"></a>Obchodní požadavky

Mezi obchodní požadavky patří procedurální, smluvní a právní závazky. Musíte:

- Být registrovaným vydavatelem komerčního obchodu na webu Marketplace. Pokud nejste registrováni, postupujte podle kroků v části [Vytvoření účtu komerčního tržiště v partnerském centru](create-account.md).
- Poskytněte obsah, který splňuje kritéria pro uvedení vaší nabídky na AppSource. Další informace najdete v tématu [seznam aplikací pro AppSource? Tady je postup](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Odsouhlaste se a postupujte podle [prohlášení Microsoftu o zásadách ochrany osobních údajů](https://privacy.microsoft.com/privacystatement).

## <a name="next-step"></a>Další krok

- [Vytvoření nabídky aplikace Power BI v partnerském centru](create-power-bi-app-offer.md)
