---
title: Cloud Solution Provider – Microsoft Commercial Marketplace
description: Naučte se prodávat vaše nabídky prostřednictvím kanálu partnera programu Microsoft Cloud (CSP) na komerčním webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: bdd2dacbba4ef8b7c439a345ea70771354b7fb5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708590"
---
# <a name="cloud-solution-provider-program"></a>Program Cloud Solution Provider

Tento článek vysvětluje, jak nakonfigurovat nabídku, která bude k dispozici pro program poskytovatele Cloud Solution Provider (CSP). Kromě publikování vašich nabídek prostřednictvím služby [komerčního obchodu na webu Marketplace](overview.md#commercial-marketplace-online-stores)můžete také provést prodej prostřednictvím programu CSP, abyste mohli oslovit miliony kvalifikovaných zákazníků Microsoftu, které tento program obsluhuje.

Můžete nakonfigurovat nové nebo existující nabídky pro dostupnost v programu CSP na základě výslovného souhlasu, který partnerům CSP umožní prodávat vaše produkty a vytvářet řešení pro zákazníky.

Vydavatelé zodpovídají za poskytování podpory opravných řešení koncovým zákazníkům a poskytování mechanismů pro partnery v programu CSP a/nebo zákazníkům, aby vás kontaktovali o podporu. Osvědčeným postupem je poskytnout partnerům v programu CSP pomocí dokumentace uživatele, školení a oznámení o stavu/výpadku, aby partneři v programu CSP mohli zpracovávat žádosti o podporu vrstvy 1 od zákazníků.  

Následující nabídky mají nárok na to, aby je bylo možné prodávat partneři v programu CSP:

- Nabídky SaaS (software jako služba) Transact
- Virtual Machines (virtuální počítače)
- Šablony řešení
- Spravované aplikace

> [!NOTE]
> Do kontejnerů a přineste si vlastní licenci (BYOL), ve kterých se ve výchozím nastavení prodávají partneři v programu CSP.

## <a name="how-to-configure-an-offer"></a>Konfigurace nabídky

Nakonfigurujte nastavení výslovných přihlášení programu CSP při vytváření nabídky v partnerském centru. [Přečtěte si další informace o změně prostředí pro publikování](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Výslovný souhlas partnerského centra

Přihlašovací možnosti se nacházejí v modulu poskytovatelů CSP pro prodejce:

![Cílová skupina pro prodejce CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

Vyberte si ze tří možností:

1. Libovolný partner v programu CSP.
2. Konkrétní partneři v programu CSP, který je vybrán.
3. V programu CSP nejsou žádní partneři.

#### <a name="option-1-any-partner-in-the-csp-program"></a>Možnost 1: libovolný partner v programu CSP

![Libovolný partner v programu CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Když vyberete tuto možnost, všichni partneři v programu CSP budou mít nárok na prodej vaší nabídky svým zákazníkům.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>Možnost 2: konkrétní partneři v programu CSP, který vyberete

![Konkrétní partneři v programu CSP, který jsem vybral](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Výběrem této možnosti autorizujete, kteří partneři v programu CSP mají nárok na prodej vaší nabídky.

K autorizaci partnerů vyberte **Vybrat partneři CSP** a otevřenou nabídku, která vám umožní Hledat podle názvu partnera nebo CSP Azure Active Directory (Azure AD) ID tenanta.

![Výběr nabídky CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Můžete použít vyhledávací filtry, jako je například **země**, **kompetence**nebo **dovednost**.

![Země/oblast, kompetence a filtry dovedností pro hledání partnerů](media/marketplace-publishers-guide/csp-add-resellers.png)

Po výběru seznamu partnerů vyberte **Přidat**.

![Příklad seznamu autorizovaných partnerů v programu CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Tabulka zobrazující seznam partnerů, které jste vybrali, se zobrazí na stránce skupina prodejců CSP.

![Tabulka se seznamem partnerů na stránce skupiny prodejců na poskytovateli CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Pokud chcete zaregistrovat změny, vyberte **Uložit koncept** .

Pokud je tato nabídka Nepublikovaná, budete ji muset publikovat, abyste ji mohli zpřístupnit vybraným partnerům.

>[!NOTE]
>Pokud v dané oblasti autorizujete partnera v programu CSP, může prodávat nabídku zákazníkovi, který patří do příslušné oblasti. Další informace o tom, jak jsou v oblastech klasifikovány nabídky CSP, najdete v tématu věnovaném [cloudovým řešením pro regionální trhy a měnu](https://docs.microsoft.com/partner-center/regional-authorization-overview).

Pokud aktualizujete seznam CSP již publikované nabídky, přidejte další partnery a vyberte **synchronizovat cílovou skupinu CSP**.

Pokud máte nabídku, která už má seznam autorizovaných partnerů a chcete použít stejný seznam pro jinou nabídku, použijte **Import/export**. Přejděte na nabídku, která má seznam CSP, a vyberte **exportovat zprostředkovatele CSP**. Funkce vytvoří soubor. csv, který lze importovat do jiné nabídky.

#### <a name="option-3-no-partners-in-the-csp-program"></a>Možnost 3: žádní partneři v programu CSP

![Žádní partneři v programu CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Když vyberete tuto možnost, nebudete svoji nabídku mít v programu CSP. Tento výběr můžete kdykoli změnit.

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

 Pokud je vaše nabídka v současné době **možnost 2: konkrétní partneři v programu CSP** , který je vybrán a chcete přejít k **Možnosti 3: žádní partneři v programu CSP**, budete moci přejít na tuto možnost pouze v případě, že partneři v programu CSP, který jste předtím povolili, neprodali vaši nabídku koncovým zákazníkům. K vytvoření žádosti použijte následující pokyny:

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

Aby mohli partneři v programu Cloud Solution Provider efektivně představovat vaši nabídku a zapojit se do vaší organizace, musíte odeslat prodejní a podpůrné materiály, které budou k dispozici pro prodejce. Tyto prostředky nebudou zveřejněny zákazníkům v online obchodech.

### <a name="partner-center-csp-channel"></a>Kanál CSP pro partnerské Centrum

Pokud jste se přihlásili do kanálu CSP v partnerském centru, musí vydavatelé zadat adresu URL, která hostuje relevantní marketingové materiály a kontaktní údaje kanálu v modulu seznamu nabídek.

![Informace o prostředcích CSP partnerského centra](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [službách pro uvedení na trh](https://partner.microsoft.com/reach-customers/gtm).
- Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , abyste mohli vytvořit a nakonfigurovat vaši nabídku.
