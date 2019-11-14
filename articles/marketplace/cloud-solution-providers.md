---
title: Poskytovatelé cloudových řešení | Azure Marketplace
description: Vydavatelé teď mohou prodávat své nabídky prostřednictvím kanálu partnerů Microsoft Cloud poskytovatel řešení (CSP).
services: Azure, Marketplace, Compute, Storage, Networking, Partner Center
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/12/2019
ms.author: v-chjen
ms.openlocfilehash: 546702af671cfe9506a4fc0448f40b7b8353960c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038660"
---
# <a name="cloud-solution-providers"></a>Poskytovatelé cloudových řešení

Nabídky softwaru můžou kromě veřejné dostupnosti nabídek přes [Microsoft Web prodejní místa](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)kontaktovat miliony kvalifikovaných zákazníků Microsoftu, které obsluhují partneři v programu Cloud Solution Provider (CSP).

Vydavatelé konfigurují nabídky pro dostupnost v programu CSP na základě výslovného souhlasu, pro novou nabídku nebo pro stávající a umožňují partnerům prodávat vaše produkty a vytvářet řešení pro zákazníky.

Vydavatelé zodpovídají za poskytování podpory opravných řešení koncovým zákazníkům a poskytování mechanismů pro partnery v programu CSP a/nebo zákazníkům, aby vás kontaktovali o podporu. Osvědčeným postupem je poskytnout partnerům v programu CSP pomocí dokumentace uživatele, školení a oznámení o stavu/výpadku, aby partneři v programu CSP mohli zpracovávat žádosti o podporu vrstvy 1 od zákazníků.  

Následující nabídky mají nárok na to, aby je bylo možné prodávat partneři v programu CSP:

- Nabídky SaaS (software jako služba) Transact
- Virtual Machines (virtuální počítače)
- Šablony řešení
- Spravované aplikace

> [!NOTE]
> Ve výchozím nastavení se mohou v kontejnerech a prostředích SKU pro vlastní licence (BYOL) prodávat v programu CSP.

## <a name="how-to-configure-an-offering"></a>Postup konfigurace nabídky

Nastavení výslovných přihlášení k programu CSP se konfiguruje v partnerském centru nebo v prostředí pro vytváření nabídek portál partnerů cloudu. [Přečtěte si další informace o změně prostředí pro publikování](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Výslovný souhlas partnerského centra

V partnerském centru najdete přihlašovací informace v modulu cloudu pro prodejce CSP.

![Cílová skupina pro prodejce CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

V modulu pro prodej prodejců CSP máte tři možnosti, jak vybírat z těchto možností:

- Možnost One: libovolný partner v programu CSP
- Možnost 2: konkrétní partneři v programu CSP, který vyberete
- Možnost tři: žádní partneři v programu CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Možnost One: libovolný partner v programu CSP

![Libovolný partner v programu CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Když vyberete tuto možnost, všichni partneři v programu CSP budou mít nárok na prodej vaší nabídky svým zákazníkům.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Možnost 2: konkrétní partneři v programu CSP, který vyberete

![Konkrétní partneři v programu CSP, který jsem vybral](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Výběrem této možnosti autorizujete, kteří partneři v programu CSP mají nárok na prodej vaší nabídky.

Pokud chcete partnerům autorizovat, klikněte na **Vybrat partneři CSP** a zobrazí se nabídka, která vám umožní Hledat podle názvu partnera nebo ID tenanta Azure Active Directory (CSP).

![Výběr nabídky CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Můžete použít vyhledávací filtry, jako je například **země**, **kompetence**nebo **dovednost**.

![Země, kompetence a filtry dovedností pro hledání partnerů](media/marketplace-publishers-guide/csp-add-resellers.png)

Po výběru seznamu partnerů vyberte **Přidat**.

![Příklad seznamu autorizovaných partnerů v programu CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Tabulka zobrazující seznam partnerů, které jste vybrali, se zobrazí na stránce skupina prodejců CSP.

![Tabulka se seznamem partnerů na stránce skupiny prodejců na poskytovateli CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Pokud chcete zaregistrovat změny, vyberte **Uložit koncept** .

Pokud je tato nabídka Nepublikovaná, budete ji muset publikovat, abyste ji mohli zpřístupnit vybraným partnerům.

>[!NOTE]
>Pokud v dané oblasti autorizujete partnera v programu CSP, může prodávat nabídku zákazníkovi, který patří do příslušné oblasti. Další informace o tom, jak jsou v oblastech klasifikovány nabídky CSP, najdete v tématu [Cloud Solution Provider program – místní trhy a měnu](https://docs.microsoft.com/partner-center/regional-authorization-overview) .

Pokud aktualizujete seznam CSP již publikované nabídky, přidejte další partnery a vyberte **synchronizovat cílovou skupinu CSP**.

Pokud máte nabídku, která už má seznam autorizovaných partnerů a chcete použít stejný seznam pro jinou nabídku, použijte **Import/export**. Přejděte na nabídku, která má seznam CSP, a vyberte **exportovat zprostředkovatele CSP**. Funkce vytvoří soubor. csv, který lze importovat do jiné nabídky.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Možnost tři: žádní partneři v programu CSP

![Žádní partneři v programu CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Když vyberete tuto možnost, nebudete svoji nabídku mít v programu CSP. Tento výběr můžete kdykoli změnit.

### <a name="cloud-partner-portal-opt-in"></a>portál partnerů cloudu výslovný souhlas

V portál partnerů cloudu se výslovný souhlas nastavuje na kartě Marketplace nebo prezentace. Možnost zvolit konkrétní partnery v programu CSP je k dispozici pouze v partnerském centru.

![Prostředí pro výslovný souhlas zprostředkovatele CSP v CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Zrušení autorizace partnerů v programu CSP

Pokud jste partner v programu CSP povolili a Tento partner už svůj produkt znovu prodal svým zákazníkům, nebudete moct tohoto partnera zrušit.

Pokud partner v programu CSP neprodal váš produkt svým zákazníkům a chcete poskytovatele CSP odebrat po publikování vaší nabídky, postupujte podle následujících pokynů:

1. Přejít na [stránku support Request](https://aka.ms/marketplacepublishersupport). Pro vás se automaticky vyplní prvních několik rozevíracích nabídek.

   > [!NOTE]
   > Neměňte výběry předem vyplněných rozevíracích nabídek.

2. V **části vybrat verzi produktu**vyberte **živá nabídka Správa**.
3. **Vyberte kategorii, která nejlépe popisuje problém**, a zvolte kategorii, která se vztahuje na vaši nabídku.
4. Pro **vyberte problém, který nejlépe popisuje problém**, vyberte **aktualizovat existující nabídku**.
5. Vyberte možnost **Další** , aby se přesměrovala na **stránku podrobnosti o problému** , kde můžete zadat další podrobnosti o vašem problému.
6. Jako název problému použijte **deautorizovat CSP** a vyplňte zbývající části požadovaných oddílů.




## <a name="navigate-between-options"></a>Navigace mezi možnostmi

V této části můžete přecházet mezi třemi možnostmi prodejců CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navigace z možnosti One: všichni partneři v programu CSP

Pokud je vaše nabídka momentálně **možnost 1: všichni partneři v programu CSP** a chcete přejít na kteroukoli z dalších dvou možností, vytvořte žádost pomocí následujících pokynů:

1. Přejít na [stránku support Request](https://aka.ms/marketplacepublishersupport). Pro vás se automaticky vyplní prvních několik rozevíracích nabídek.

   > [!NOTE]
   > Neměňte výběry předem vyplněných rozevíracích nabídek.

2. V **části vybrat verzi produktu**vyberte **živá nabídka Správa**.
3. **Vyberte kategorii, která nejlépe popisuje problém**, a zvolte kategorii, která se vztahuje na vaši nabídku.
4. Pro **vyberte problém, který nejlépe popisuje problém**, vyberte **aktualizovat existující nabídku**.
5. Vyberte možnost **Další** , aby se přesměrovala na **stránku podrobnosti o problému** , kde můžete zadat další podrobnosti o vašem problému.
6. Jako název problému použijte **deautorizovat CSP** a vyplňte zbývající části požadovaných oddílů.

> [!NOTE]
> Pokud se snažíte přejít na možnost 2 a partner v programu CSP již nabídku svým zákazníkům znovu prodal, je tento partner ve výchozím nastavení již v seznamu autorizovaných partnerů.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navigace z možnosti 2: konkrétní partneři v programu CSP vybraný program

Pokud je vaše nabídka v současné době **možnost 2: konkrétní partneři v programu CSP, který jsem vybral** a chcete přejít k **Možnosti jedna: všichni partneři v programu CSP**, vytvořte žádost pomocí následujících pokynů:

1. Přejít na [stránku support Request](https://aka.ms/marketplacepublishersupport). Pro vás se automaticky vyplní prvních několik rozevíracích nabídek.

   > [!NOTE]
   > Neměňte výběry předem vyplněných rozevíracích nabídek.

2. V **části vybrat verzi produktu**vyberte **živá nabídka Správa**.
3. **Vyberte kategorii, která nejlépe popisuje problém**, a zvolte kategorii, která se vztahuje na vaši nabídku.
4. Pro **vyberte problém, který nejlépe popisuje problém**, vyberte **aktualizovat existující nabídku**.
5. Vyberte možnost **Další** , aby se přesměrovala na **stránku podrobnosti o problému** , kde můžete zadat další podrobnosti o vašem problému.
6. Jako název problému použijte **deautorizovat CSP** a vyplňte zbývající části požadovaných oddílů.

 Pokud je vaše nabídka v současné době **možnost 2: konkrétní partneři v programu CSP** , který je vybrán a chcete přejít k **Možnosti 3: žádní partneři v programu CSP**, budete moci přejít na tuto možnost pouze v případě, že partneři v programu CSP, který jste předtím povolili, neprodali vaši nabídku koncovým zákazníkům. K vytvoření žádosti prosím použijte následující pokyny:

1. Přejít na [stránku support Request](https://aka.ms/marketplacepublishersupport). Pro vás se automaticky vyplní prvních několik rozevíracích nabídek.

   > [!NOTE]
   > Neměňte výběry předem vyplněných rozevíracích nabídek.

2. V **části vybrat verzi produktu**vyberte **živá nabídka Správa**.
3. **Vyberte kategorii, která nejlépe popisuje problém**, a zvolte kategorii, která se vztahuje na vaši nabídku.
4. Pro **vyberte problém, který nejlépe popisuje problém**, vyberte **aktualizovat existující nabídku**.
5. Vyberte možnost **Další** , aby se přesměrovala na **stránku podrobnosti o problému** , kde můžete zadat další podrobnosti o vašem problému.
6. Jako název problému použijte **deautorizovat CSP** a vyplňte zbývající části požadovaných oddílů.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navigace z možnosti 3: žádní partneři v programu CSP

Pokud je vaše nabídka v současné době **možnost 3: žádní partneři v programu CSP**, můžete kdykoli přejít na kteroukoli z dalších dvou možností.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Sdílení materiálů pro prodej a podporu s partnery v programu CSP

Aby mohli partneři v programu Cloud Solution Provider efektivně představovat vaši nabídku a zapojit se do vaší organizace, musíte odeslat prodejní a podpůrné materiály, které budou k dispozici pro prodejce. Tyto prostředky nebudou zveřejněny zákazníkům v prodejní místa na webu Marketplace.

### <a name="partner-center-csp-channel"></a>Kanál CSP pro partnerské Centrum

Pokud jste se přihlásili do kanálu CSP v partnerském centru, musí vydavatelé zadat adresu URL, která hostuje relevantní marketingové materiály a kontaktní informace kanálu, do kanálu CSP v modulu seznam nabídek:

![Informace o prostředcích CSP partnerského centra](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>portál partnerů cloudu kanál CSP

Pokud jste se rozhodli do kanálu CSP v portál partnerů cloudu, vydavatelé musí zadat adresu URL, která hostuje relevantní marketingové materiály a kontaktní informace kanálu k kanálu CSP:

![portál partnerů cloudu informace o promateriálcích CSP](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Další kroky

Navštivte [příručku pro vydavatele Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Další informace o službách GTM na webu Marketplace najdete v tématu [služby pro uvedení na trh](https://partner.microsoft.com/reach-customers/gtm).

Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) nebo [portál partnerů cloudu](https://cloudpartner.azure.com/) a vytvořte a nakonfigurujte vaši nabídku.
