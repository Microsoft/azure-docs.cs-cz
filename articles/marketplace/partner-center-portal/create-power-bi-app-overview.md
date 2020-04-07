---
title: Přehled vytváření aplikací Power BI – Azure Marketplace
description: Tento článek popisuje kroky vysoké úrovně pro publikování aplikace Power BI na Microsoft AppSource. K dispozici jsou také technické a obchodní požadavky, které musí vaše aplikace Power BI splňovat, aby se publikovala na komerčním trhu.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: c348a172b16e12334d33cf2718609694147fdce3
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674524"
---
# <a name="power-bi-app-creation-overview"></a>Přehled vytvoření aplikace Power BI

> [!IMPORTANT]
> Správu nabídek aplikací Power BI přesouváme z portálu partnerů cloudu do Centra partnerů. Dokud se vaše nabídky nemigrují, postupujte podle pokynů v [nabídce aplikace Power BI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) pro portál cloudových partnerů a spravujte své nabídky.

Tento článek vysvětluje, jak publikovat aplikaci Power BI na Microsoft [AppSource](https://appsource.microsoft.com/). Aplikace Power BI balí přizpůsobitelný obsah, včetně datových sad, sestav a řídicích panelů. Pak můžete aplikaci používat s jinými platformami Power BI pomocí AppSource, provádět úpravy a úpravy povolené vývojářem a připojit ji k vlastním datům.

## <a name="publishing-benefits"></a>Publikování výhod

Výhody publikování na komerčním trhu:

- Propagujte svou společnost pomocí značky Microsoft.
- Potenciálně oslovte více než 100 milionů uživatelů Office 365 a Dynamics 365 na AppSource a více než 200 000 organizací prostřednictvím Azure Marketplace.
- Získejte vysoce kvalitní potenciální zákazníky z těchto tržišť.
- Vaše služby budou propagované týmy microsoftu a telesales.

## <a name="overview"></a>Přehled

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="přehled kroků publikování aplikace Power BI" border="false":::

Toto jsou klíčové kroky publikování:

1. Vytvořte aplikaci v Power BI. Obdržíte odkaz na instalaci balíčku, který je hlavním technickým přínosem nabídky. Před vytvořením nabídky v Partnerském centru odešlete testovací balíček do předprodukčního programu. Podrobnosti najdete v tématu [Co jsou aplikace Power BI?](https://docs.microsoft.com/power-bi/service-template-apps-overview).
2. Přidejte marketingové materiály, například oficiální název, popis a loga.
3. Zahrňte právní a podpůrné dokumenty nabídky, jako jsou podmínky použití, zásady ochrany osobních údajů, zásady podpory a uživatelská nápověda.
4. Vytvoření nabídky: Pomocí Partnerského centra můžete upravit podrobnosti, včetně popisu nabídky, marketingových materiálů, právních informací, informací o podpoře a specifikací chabé služby.
5. Odešlete jej k publikování.
6. Sledujte proces v Partnerském centru, kde tým appsource onboarding testuje, ověřuje a certifikuje vaši aplikaci.
7. Až bude certifikována, projděte si aplikaci v testovacím prostředí a uvolněte ji. To bude seznam na AppSource (to "jde žít").
8. V Power BI odešlete balíček do produkčního prostředí. Podrobnosti najdete [v tématu Správa vydání aplikace Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Než začnete

Prohlédněte si níže uvedené odkazy, které poskytují šablony, tipy a ukázky.

- [Vytvoření aplikace Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Tipy pro vytváření aplikace Power BI](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [ukázky](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Požadavky

Aby byla nabídka aplikace Power BI zveřejněna na komerčním trhu, musí splňovat následující technické a obchodní požadavky.

### <a name="technical-requirements"></a>Technické požadavky

Hlavním technickým zdrojem, který budete potřebovat, je [aplikace Power BI](https://go.microsoft.com/fwlink/?linkid=2028636). Toto je kolekce primárních datových sad, sestav nebo řídicích panelů. Zahrnuje také volitelné připojené služby a vložené datové sady, dříve známé jako [balíček obsahu](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction). Další informace o vývoji tohoto typu aplikace najdete v tématu [Co jsou aplikace Power BI?](https://go.microsoft.com/fwlink/?linkid=2028636).

#### <a name="get-an-installation-web-address"></a>Získání webové adresy pro instalaci

Aplikaci Power BI můžete vytvářet jenom v prostředí [Power BI.](https://powerbi.microsoft.com/)

1. Přihlaste se pomocí [licence Power BI Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).
2. Vytvořte a otestujte aplikaci v Power BI.
3. Když obdržíte webovou adresu pro instalaci aplikace, přidejte ji na stránku **Technická konfigurace** v Centru partnerů.

Po vytvoření a testování aplikace v Power BI uložte webovou adresu pro instalaci aplikace, protože ji budete potřebovat k [vytvoření nabídky aplikace Pro Power BI](https://aka.ms/AzureCreatePBIServiceApp).

### <a name="business-requirements"></a>Obchodní požadavky

Obchodní požadavky zahrnují procesní, smluvní a právní povinnosti. Musíte:

- Být registrovaným vydavatelem komerčního tržiště. Pokud nejste zaregistrováni, postupujte podle pokynů v [části Staňte se vydavatelem komerčního tržiště](https://docs.microsoft.com/azure/marketplace/become-publisher).
- Zadejte obsah, který splňuje kritéria pro vaši nabídku, která mají být uvedena na AppSource. Další informace najdete [v tématu Mít aplikaci, která má být uvedena na AppSource? Zde je návod, jak](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Souhlasíte s tím, a postupujte [podle prohlášení společnosti Microsoft o zásadách ochrany osobních údajů](https://privacy.microsoft.com/privacystatement).

## <a name="next-steps"></a>Další kroky

- [Vytvoření nabídky aplikace Power BI v Partnerském centru](https://aka.ms/AzureCreatePBIServiceApp)