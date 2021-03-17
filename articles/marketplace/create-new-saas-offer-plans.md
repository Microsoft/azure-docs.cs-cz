---
title: Jak vytvořit plány pro nabídku SaaS v partnerském centru Microsoftu
description: Jak vytvořit plány pro novou nabídku softwaru jako služby (SaaS) pomocí portálu Microsoft Commercial Marketplace v partnerském centru.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 238ef9ec80b01470e28535d0eb42dbb3a377b005
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746433"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>Jak vytvořit plány pro nabídku SaaS

Nabídky prodávané prostřednictvím komerčního tržiště Microsoftu musí mít aspoň jeden plán. V rámci jedné nabídky můžete vytvořit nejrůznější plány s různými možnostmi. Tyto plány (někdy označované jako SKU) se mohou lišit podle verze, finanční zhodnocení nebo vrstev služby. Podrobné pokyny k plánům najdete v tématu [plány a ceny pro komerční nabídky na webu Marketplace](plans-pricing.md).

> [!NOTE]
> Pokud se rozhodnete zpracovávat transakce nezávisle, tato možnost se nezobrazí. Místo toho přejděte k tomu, [Jak prodávat nabídku SaaS](create-new-saas-offer-marketing.md).

## <a name="create-a-plan"></a>Vytvoření plánu

1. V horní části karty **Přehled plánu** vyberte **+ vytvořit nový plán**.

1. V dialogovém okně, které se zobrazí, zadejte do pole **ID plánu** jedinečný identifikátor ID plánu. Použijte až 50 malých alfanumerických znaků, pomlčky nebo podtržítka. Po zvolení **Create (vytvořit**) nemůžete změnit ID plánu.

1. Do pole **název plánu** zadejte jedinečný název pro tento plán. Použijte maximálně 50 znaků.

1. Vyberte **Vytvořit**.

## <a name="define-the-plan-listing"></a>Definice výpisu plánu

Na kartě **seznam plánů** můžete definovat název a popis plánu, jak se mají zobrazit na komerčním webu Marketplace.

1. V poli **název plánu** se tady zobrazí název, který jste zadali dříve pro tento plán. Kdykoli ho můžete změnit. Tento název se zobrazí na komerčním webu Marketplace jako název plánu softwaru vaší nabídky.

1. V poli **Popis plánu** vysvětlete, co dělá tento plán softwaru jedinečný a všechny rozdíly mezi ostatními plány v rámci vaší nabídky. Tento popis může obsahovat až 500 znaků.
1. Než budete pokračovat na další kartu, vyberte **Uložit koncept** . **ceny a dostupnost** pak můžete vybrat.

## <a name="define-markets-pricing-and-availability"></a>Definování trhů, cen a dostupnosti

Každý plán musí být k dispozici alespoň v jednom trhu. Na kartě **ceny a dostupnost** můžete nakonfigurovat trhy, které tento plán bude k dispozici v, požadovaný finanční zhodnocení model, cenu a fakturační období. Kromě toho můžete určit, jestli se má plán zviditelnit všem uživatelům, nebo jenom pro konkrétní zákazníky (označovaný také jako soukromý plán).

1. V části **trhy** vyberte odkaz **Upravit trhy** .
1. V dialogovém okně, které se zobrazí, vyberte umístění na trhu, kde má být plán dostupný. Musíte vybrat minimálně jeden z nich, maximálně 141 trhů.

   > [!NOTE]
   > Toto dialogové okno obsahuje vyhledávací pole a možnost filtrovat jenom země "daňové prominuté", ve kterých Microsoft za vás zavazuje prodeje a používání daně.

1. Vyberte **Uložit** a zavřete dialogové okno.

## <a name="define-a-pricing-model"></a>Definování cenového modelu

Ke každému plánu musíte přidružit cenový model: buď _paušální_ , nebo _na uživatele_. Všechny plány ve stejné nabídce musí používat stejný cenový model. Nabídka například nemůže mít jeden plán, který je paušální sazbou a druhý plán na uživatele. Další informace najdete v tématu [cenové modely SaaS](plan-saas-offer.md#saas-pricing-models).

> [!IMPORTANT]
> Po publikování vaší nabídky nemůžete změnit cenový model. Kromě toho musí všechny plány pro stejnou nabídku sdílet stejný cenový model.

### <a name="configure-flat-rate-pricing"></a>Konfigurace cen paušálních sazeb

1. Na kartě **ceny a dostupnost** v části **ceny** vyberte **paušální sazba**.
1. Zaškrtněte buď **měsíční** , nebo **roční** zaškrtávací políčko, případně zadejte cenu.

### <a name="add-a-custom-meter-dimension"></a>Přidat vlastní dimenzi měřiče

Tato možnost je dostupná jenom v případě, že jste vybrali ceny paušálních sazeb. Další informace najdete v tématu [účtované vyúčtování pro SaaS pomocí komerčního měření služby Marketplace](./partner-center-portal/saas-metered-billing.md).

1. V části **dimenze služby pro měření na Marketplace** vyberte odkaz **Přidat vlastní dimenzi měření (max. 30)** .
1. Do pole **ID** zadejte neproměnlivý odkaz na identifikátor při generování událostí využití.
1. Do pole **Zobrazovaný název** zadejte zobrazovaný název přidružený k dimenzi. Například "textové zprávy jsou odesílány".
1. V poli **Měrná jednotka** zadejte popis fakturační jednotky. Například "na textovou zprávu" nebo "za 100 e-mailů".
1. Do pole **cena za jednotku v USD** zadejte cenu za jednu jednotku dimenze.
1. V poli **měsíční množství zahrnuté do základního** pole zadejte množství (jako celé číslo) dimenze, která je zahrnutá v každém měsíci, pro zákazníky, kteří platíte za opakovaný měsíční poplatek. Chcete-li nastavit neomezené množství, zaškrtněte místo toho zaškrtávací políčko.
1. V poli **roční množství zahrnuté do základního** pole zadejte množství dimenze (jako celé číslo), které je každý měsíc zahrnuto pro zákazníky, kteří platí pro periodický roční poplatek. Chcete-li nastavit neomezené množství, zaškrtněte místo toho zaškrtávací políčko.
1. Chcete-li přidat další vlastní dimenzi měřiče, vyberte odkaz **Přidat další dimenzi** a opakujte kroky 1 až 7.

### <a name="configure-per-user-pricing"></a>Konfigurace cen za uživatele

1. Na kartě **ceny a dostupnost** v části **ceny** vyberte **jednotlivé uživatele**.
2. V části **omezení uživatelů** zadejte minimální a maximální počet uživatelů pro tento plán.
3. V části **fakturační období** zadejte měsíční cenu, roční cenu nebo obojí.

### <a name="validate-custom-prices"></a>Ověřit vlastní ceny

Chcete-li nastavit vlastní ceny v individuálním trhu, exportujte, upravte a pak Importujte tabulku s cenami. Zodpovídáte za ověřování těchto cen a vlastnictví těchto nastavení. Podrobné informace najdete v tématu [vlastní ceny](plans-pricing.md#custom-prices).

1. Aby bylo možné exportovat data o cenách, musíte nejdřív Uložit změny cen. U dolního okraje karty **ceny a dostupnost** vyberte **Uložit koncept**.
1. V části **ceny** vyberte odkaz **exportovat data cen** .
1. Otevřete soubor exportedPrice.xlsx v aplikaci Microsoft Excel.
1. V tabulce proveďte požadované aktualizace informací o cenách a pak uložte. Soubor CSV.<br> Než budete moct soubor aktualizovat, možná budete muset povolit úpravy v Excelu.
2. Na kartě **ceny a dostupnost** v části **ceny** vyberte odkaz **importovat data ceny** .
3. V dialogovém okně, které se zobrazí, klikněte na tlačítko **Ano**.
4. Vyberte soubor exportedPrice.xlsx, který jste aktualizovali, a pak klikněte na **otevřít**.

### <a name="enable-a-free-trial"></a>Povolit bezplatnou zkušební verzi

V rámci vaší nabídky můžete nakonfigurovat bezplatnou zkušební verzi pro každý plán. Zaškrtnutím políčka povolíte měsíční bezplatnou zkušební verzi. Toto zaškrtávací políčko není k dispozici pro plány, které používají službu měření na webu Marketplace. Další informace najdete v tématu [bezplatné zkušební](plans-pricing.md#free-trials)verze.

> [!IMPORTANT]
> Po publikování nabídky s podporou transakcí bez bezplatné zkušební verze ji nelze pro tento plán zakázat. Před publikováním nabídky se ujistěte, že je toto nastavení správné, abyste se vyhnuli nutnosti opětovného vytvoření plánu.

- V části **bezplatná zkušební verze** zaškrtněte políčko **Povolení bezplatné zkušební verze na jeden měsíc** .

## <a name="choose-who-can-see-your-plan"></a>Zvolit, kdo může zobrazit váš plán

Každý plán můžete nakonfigurovat tak, aby byl viditelný pro všechny uživatele nebo jenom pro konkrétní cílovou skupinu. Přístup k privátnímu plánu udělíte pomocí ID klientů s možností zahrnutí popisu každého ID tenanta, které přiřadíte. Můžete přidat maximálně 10 ID klientů ručně nebo až 20 000 ID klientů pomocí. Soubor CSV.

> [!NOTE]
> Pokud publikujete soukromý plán, můžete později změnit jeho viditelnost na veřejné. Po publikování veřejného plánu ale nemůžete změnit jeho viditelnost na Private.

### <a name="make-your-plan-public"></a>Nastavte svůj plán jako veřejný

1. V části **plán viditelnost** vyberte **veřejné** pole.
1. Vyberte **Uložit koncept** a pak v levém horním rohu karty vyberte **plán – přehled** , který se vrátí na kartu **Přehled plánu** .
1. Pokud chcete pro tuto nabídku vytvořit další plán, vyberte v horní části karty **Přehled plánu** možnost **+ vytvořit nový plán**. Pak opakujte postup v části [Vytvoření plánu](#create-a-plan) . V opačném případě přejdete na [Zobrazit vaše plány](#view-your-plans).

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Ruční přidání ID tenanta pro soukromý plán 

1. V části **plán viditelnost** zaškrtněte políčko **Toto je soukromý plán** .
1. Do pole **ID tenanta** , které se zobrazí, zadejte ID TENANTA Azure AD cílové skupiny, které chcete udělit přístup k tomuto privátnímu plánu. Vyžaduje se minimálně jedno ID tenanta.
1. Volitelné Do pole **Popis** zadejte popis této cílové skupiny.
1. Chcete-li přidat jiné ID tenanta, opakujte kroky 2 a 3.
1. Až skončíte s přidáváním ID klientů, vyberte **Uložit koncept** a pak v levém horním rohu karty vyberte **plán – přehled** se vraťte na kartu **Přehled plánu** .
1. Pokud chcete pro tuto nabídku vytvořit další plán, vyberte v horní části karty **Přehled plánu** možnost **+ vytvořit nový plán**. Pak opakujte postup v části [Vytvoření plánu](#create-a-plan) . V opačném případě přejdete na [Zobrazit vaše plány](#view-your-plans).

### <a name="use-a-csv-file-for-a-private-plan"></a>Použijte. Soubor CSV pro soukromý plán

1. V části **plán viditelnost** zaškrtněte políčko **Toto je soukromý plán** .
2. Vyberte odkaz **exportovat cílovou skupinu (CSV)** .
3. Otevřete. Soubor CSV a přidejte identifikátory Azure, kterým chcete udělit přístup k soukromé nabídce, do sloupce **ID** .
4. V případě potřeby zadejte popis pro každou cílovou skupinu ve sloupci **Popis** .
5. Přidejte do sloupce **Type** "TenantID" pro každý řádek s ID Azure.
6. Uložte. Soubor CSV.
7. Na kartě **ceny a dostupnost** v části **plán viditelnost** vyberte odkaz **importovat cílovou skupinu (CSV)** .
8. V dialogovém okně, které se zobrazí, vyberte **Ano**.
9. Vyberte. Soubor CSV a pak vyberte **otevřít**.
10. Vyberte **Uložit koncept** a pak v levém horním rohu karty vyberte **plán – přehled** , který se vrátí na kartu **Přehled plánu** .
11. Pokud chcete pro tuto nabídku vytvořit další plán, vyberte v horní části karty **Přehled plánu** možnost **+ vytvořit nový plán**. Pak opakujte postup v části [Vytvoření plánu](#create-a-plan) . V opačném případě, pokud jste dokončili vytváření plánů, jděte do další části: **zobrazení vašich plánů**.

## <a name="view-your-plans"></a>Zobrazit vaše plány

Po vytvoření jednoho nebo více plánů uvidíte název svého plánu, plán ID, cenový model, dostupnost (veřejné nebo soukromé), aktuální stav publikování a všechny dostupné akce na kartě **Přehled plánu** .

Akce, které jsou k dispozici ve sloupci **Akce** na kartě **Přehled plánu** , se liší v závislosti na stavu plánu a mohou zahrnovat následující:

- Pokud je stav plánu **koncept**, odkaz ve sloupci **Akce** bude vyslovit **Odstranit koncept**.
- Pokud je stav plánu **Live**, odkaz ve sloupci **Akce** buď **zastaví prodej plánu** , nebo **synchronizuje soukromou cílovou skupinu**. Odkaz **synchronizovat soukromou cílovou skupinu** bude publikovat jenom změny vašich privátních cílových skupin, aniž byste museli publikovat další aktualizace, které jste mohli v této nabídce udělat.

## <a name="next-steps"></a>Další kroky

- Naučte se [prodávat nabídku SaaS](create-new-saas-offer-marketing.md) prostřednictvím spolupracujícího **s Microsoftem** a **prodávat prostřednictvím programů CSP** .
- [Postup testování a publikování nabídky SaaS na komerčním webu Marketplace](test-publish-saas-offer.md).
