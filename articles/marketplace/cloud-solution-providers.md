---
title: Poskytovatelé cloudových řešení | Azure Marketplace
description: Vydavatelé nyní mohou prodávat své nabídky prostřednictvím partnerského kanálu Zprostředkovatele cloudových řešení (CSP) společnosti Microsoft.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: 9da8b6d8e5151bd6487d68c2796c261f19c11b55
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086551"
---
# <a name="cloud-solution-providers"></a>Poskytovatelé cloudových řešení

Softwarové nabídky mohou kromě veřejné dostupnosti nabídek prostřednictvím [webových prodejen společnosti Microsoft](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)oslovit miliony kvalifikovaných zákazníků společnosti Microsoft obsluhovaných partnery v programu Zprostředkovatel cloudových řešení (CSP).

Vydavatelé konfigurují nabídky dostupnosti v programu CSP na základě výslovného na novou nabídku nebo stávající nabídku, což partnerům umožňuje prodávat vaše produkty a vytvářet přibalená řešení pro zákazníky.

Vydavatelé jsou zodpovědní za poskytování podpory pro koncové zákazníky a za poskytování mechanismu pro partnery v programu CSP a/nebo zákazníky, kteří vás budou kontaktovat s žádostí o podporu. Je osvědčeným postupem poskytovat partnerům v programu CSP uživatelskou dokumentaci, školení a oznámení o stavu a výpadku služby (podle potřeby), aby partneři v programu CSP byli vybaveni pro zpracování požadavků podpory úrovně 1 od zákazníků.  

Následující nabídky jsou způsobilé k tomu, aby se přihlásily k prodeji partnery v programu CSP:

- Software jako služba (SaaS) transact nabízí
- Virtuální počítače (virtuální počítače)
- Šablony řešení
- Spravované aplikace

> [!NOTE]
> Kontejnery a přineste si vlastní licenci (BYOL) Virtuální počítače jsou přihlášeni k prodeji partnery v programu CSP ve výchozím nastavení.

## <a name="how-to-configure-an-offering"></a>Jak nakonfigurovat nabídku

Nastavení přihlášení k programu CSP je nakonfigurováno v partnerském centru nebo na portálu cloudových partnerů, které nabízí prostředí pro vytváření nabídek. [Přečtěte si další informace o měnícím se prostředí vydavatele](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Přihlášení do partnerského centra

V Partnerském centru najdete možnost i v ysty přihlášení v modulu publika prodejce CSP.

![Publikum prodejce CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

V modulu publika prodejce CSP máte tři možnosti, ze kterých si můžete vybrat:

- Možnost první: Každý partner v programu CSP
- Druhá možnost: Konkrétní partneři v programu CSP, který vyberu
- Třetí možnost: Žádní partneři v programu CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Možnost první: Každý partner v programu CSP

![Každý partner v programu CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Výběrem této možnosti jsou všichni partneři v programu CSP způsobilí vaši nabídku dále prodávat svým zákazníkům.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Druhá možnost: Konkrétní partneři v programu CSP, který vyberu

![Konkrétní partneři v programu CSP, který vyberu](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Výběrem této možnosti autorizujete, kteří partneři v programu CSP jsou způsobilí k prodeji vaší nabídky.

Pokud chcete autorizovat partnery, klikněte na **Vybrat partnery CSP** a zobrazí se nabídka, která vám umožní vyhledávat podle názvu partnera nebo podle ID klienta CSP Azure Active Directory (AAD).

![Vybrat nabídku CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Můžete použít vyhledávací filtry, například **Země**, **Kompetence**nebo **Dovednost**.

![Země, kompetence a filtry dovedností pro vyhledávání partnerů](media/marketplace-publishers-guide/csp-add-resellers.png)

Po výběru seznamu partnerů vyberte **Přidat**.

![Příklad seznamu autorizovaných partnerů v programu CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Na stránce publika prodejce CSP se zobrazí tabulka se seznamem partnerů, které jste vybrali.

![Tabulka se seznamem partnerů na stránce publika prodejce CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Chcete-li změny zaregistrovat, vyberte **Uložit pracovní verzi.**

Pokud tato nabídka není publikovaná, budete muset nabídku publikovat, aby byla dostupná vybraným partnerům.

>[!NOTE]
>Pokud autorizujete partnera v programu CSP v dané oblasti, může nabídku prodat libovolnému zákazníkovi, který patří do této konkrétní oblasti. Další informace o tom, jak jsou nabídky CSP klasifikovány v regionech, najdete v tématu [Regionální trhy a měna programu Zprostředkovatel cloudových řešení.](https://docs.microsoft.com/partner-center/regional-authorization-overview)

Pokud aktualizujete seznam CSP již publikované nabídky, přidejte další partnery a vyberte **možnost Synchronizovat cílovou skupinu CSP**.

Pokud máte nabídku, která již obsahuje seznam autorizovaných partnerů a chcete použít stejný seznam pro jinou nabídku, použijte **možnost Import/Export**. Přejděte na nabídku, která obsahuje seznam CSP, a vyberte **možnost Exportovat csp**. Funkce vyvíjí soubor .csv, který lze importovat do jiné nabídky.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Třetí možnost: Žádní partneři v programu CSP

![V programu CSP nejsou žádní partneři](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Výběrem této možnosti se odhlásíte z programu CSP. Tento výběr můžete kdykoli změnit.

### <a name="cloud-partner-portal-opt-in"></a>Přihlášení k portálu cloudových partnerů

Na portálu partnerů cloudu je přihlášení nastaveno na kartě Marketplace nebo Storefront. Možnost vybrat si konkrétní partnery v programu CSP je k dispozici pouze v Partnerském centru.

![Zkušenosti s opt-inem CSP v CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Deautorizovat partnery v programu CSP

Pokud jste autorizovali partnera v programu CSP a tento partner již produkt znovu prodal svým zákazníkům, nebudete moci tohoto partnera deautorizovat.

Pokud partner v programu CSP neprodal váš produkt svým zákazníkům a chcete odstranit csp po publikování nabídky, postupujte podle následujících pokynů:

1. Přejděte na [stránku žádosti o podporu](https://aka.ms/marketplacepublishersupport). Prvních několik rozbalovacích nabídek se automaticky vyplní za vás.

   > [!NOTE]
   > Neměňte předem vyplněný výběr rozbalovací nabídky.

2. V **části Vyberte verzi produktu**vyberte možnost Správa **živých nabídek**.
3. V **části Vyberte kategorii, která tento problém nejlépe popíše**, zvolte kategorii, která odkazuje na vaši nabídku.
4. V **části Vyberte problém, který nejlépe popisuje problém**, vyberte možnost Aktualizovat existující **nabídku**.
5. Chcete-li zadat další podrobnosti o **problému,** vyberte **možnost Další,** chcete-li zadat další podrobnosti o problému.
6. Jako název problému použijte **deautorizovatautorac csp** a vyplňte zbývající části požadované.




## <a name="navigate-between-options"></a>Přechod mezi možnostmi

V této části můžete procházet mezi třemi možnostmi prodejce CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Přechod z možnosti jedna: Libovolný partner v programu CSP

Pokud je vaše nabídka aktuálně **možnost 1: Libovolný partner v programu CSP** a chcete přejít na některou z dalších dvou možností, vytvořte požadavek podle následujících pokynů:

1. Přejděte na [stránku žádosti o podporu](https://aka.ms/marketplacepublishersupport). Prvních několik rozbalovacích nabídek se automaticky vyplní za vás.

   > [!NOTE]
   > Neměňte předem vyplněný výběr rozbalovací nabídky.

2. V **části Vyberte verzi produktu**vyberte možnost Správa **živých nabídek**.
3. V **části Vyberte kategorii, která tento problém nejlépe popíše**, zvolte kategorii, která odkazuje na vaši nabídku.
4. V **části Vyberte problém, který nejlépe popisuje problém**, vyberte možnost Aktualizovat existující **nabídku**.
5. Chcete-li zadat další podrobnosti o **problému,** vyberte **možnost Další,** chcete-li zadat další podrobnosti o problému.
6. Jako název problému použijte **deautorizovatautorac csp** a vyplňte zbývající části požadované.

> [!NOTE]
> Pokud se pokoušíte přejít na možnost dvě a partner v programu CSP již nabídku svým zákazníkům znovu prodal, je tento partner ve výchozím nastavení již ve vašem seznamu autorizovaných partnerů.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Přechod z možnosti dvě: Konkrétní partneři v programu CSP, který jsem vybral

Pokud je vaše nabídka aktuálně **možnost 2: Konkrétní partneři v programu CSP,** který jsem vybral, a chcete přejít na **možnost jedna: Libovolný partner v programu CSP**, vytvořte požadavek podle následujících pokynů:

1. Přejděte na [stránku žádosti o podporu](https://aka.ms/marketplacepublishersupport). Prvních několik rozbalovacích nabídek se automaticky vyplní za vás.

   > [!NOTE]
   > Neměňte předem vyplněný výběr rozbalovací nabídky.

2. V **části Vyberte verzi produktu**vyberte možnost Správa **živých nabídek**.
3. V **části Vyberte kategorii, která tento problém nejlépe popíše**, zvolte kategorii, která odkazuje na vaši nabídku.
4. V **části Vyberte problém, který nejlépe popisuje problém**, vyberte možnost Aktualizovat existující **nabídku**.
5. Chcete-li zadat další podrobnosti o **problému,** vyberte **možnost Další,** chcete-li zadat další podrobnosti o problému.
6. Jako název problému použijte **deautorizovatautorac csp** a vyplňte zbývající části požadované.

 Pokud je vaše nabídka v současné době **možností 2: Konkrétní partneři v programu CSP,** který jsem vybral, a chcete přejít na **možnost 3: Žádní partneři v programu CSP**, budete moci přejít na tuto možnost pouze v případě, že partneři v programu CSP, který jste dříve autorizovali, vaši nabídku neprodají koncovým zákazníkům. K vytvoření požadavku použijte následující pokyny:

1. Přejděte na [stránku žádosti o podporu](https://aka.ms/marketplacepublishersupport). Prvních několik rozbalovacích nabídek se automaticky vyplní za vás.

   > [!NOTE]
   > Neměňte předem vyplněný výběr rozbalovací nabídky.

2. V **části Vyberte verzi produktu**vyberte možnost Správa **živých nabídek**.
3. V **části Vyberte kategorii, která tento problém nejlépe popíše**, zvolte kategorii, která odkazuje na vaši nabídku.
4. V **části Vyberte problém, který nejlépe popisuje problém**, vyberte možnost Aktualizovat existující **nabídku**.
5. Chcete-li zadat další podrobnosti o **problému,** vyberte **možnost Další,** chcete-li zadat další podrobnosti o problému.
6. Jako název problému použijte **deautorizovatautorac csp** a vyplňte zbývající části požadované.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Přechod z možnosti 3: V programu CSP nejsou žádní partneři

Pokud je vaše nabídka aktuálně **možnost 3: Žádní partneři v programu CSP**, můžete kdykoli přejít na některou z dalších dvou možností.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Sdílení prodejních a podpůrných materiálů s partnery v programu CSP

Chcete-li partnerům v programu Poskytovatele cloudových řešení nejefektivněji reprezentovat vaši nabídku a spolupracovat s vaší organizací, musíte odeslat prodejní a podpůrné materiály, které budou k dispozici prodejcům. Tyto prostředky nebudou vystaveny zákazníkům v obchodech tržiště.

### <a name="partner-center-csp-channel"></a>Partnerský centr csp kanál

Pokud jste se přihlásili do kanálu CSP v Partnerském centru, musí majitelé stránek zadat adresu URL, která hostuje relevantní marketingové materiály a kontaktní informace kanálu kanálu CSP v modulu nabídky:

![Informace o zajištění csp partnerského centra](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Kanál CSP portálu cloudových partnerů

Pokud jste se přihlásili do kanálu CSP na portálu partnerů cloudu, vydavatelé musí zadat adresu URL, která hostuje relevantní marketingové materiály a kontaktní informace kanálu CSP:

![Informace o zajištění k zajištění portálu cloudových partnerů](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Další kroky

Navštivte [Azure Marketplace a Průvodce vydavatelem AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Další informace o službách MARKETPLACE GTM najdete v [tématu Služby pro uvedení na trh](https://partner.microsoft.com/reach-customers/gtm).

Chcete-li vytvořit a nakonfigurovat nabídku, přihlaste se do [Centra partnerů.](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
