---
title: Jak vytvořit plány pro nabídku aplikací Azure
description: Naučte se vytvářet plány pro nabídku aplikací Azure v partnerském centru.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370089"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Jak vytvořit plány pro nabídku aplikací Azure

Nabídky prodávané prostřednictvím komerčního tržiště Microsoftu musí mít aspoň jeden plán, aby se vaše nabídka dala zobrazit na komerčním webu Marketplace. V rámci jedné nabídky můžete vytvořit nejrůznější plány s různými možnostmi. Tyto plány (někdy označované jako SKU) se mohou lišit v souvislosti s typem plánu ( _Šablona řešení_ nebo _spravovaná aplikace_ ), finanční zhodnocení nebo cílová skupina. Obecné pokyny k plánům najdete v tématu [plány a ceny pro komerční nabídky na webu Marketplace](plans-pricing.md).

## <a name="create-a-plan"></a>Vytvoření plánu

1. V horní části karty **Přehled plánu** vyberte **+ vytvořit nový plán**.
1. V dialogovém okně, které se zobrazí, zadejte do pole **ID plánu** jedinečný identifikátor ID plánu. Toto ID se bude zobrazovat zákazníkům v adrese URL produktu. Použijte až 50 malých alfanumerických znaků, pomlčky nebo podtržítka. Po zvolení **Create (vytvořit** ) nemůžete změnit ID plánu.
1. Do pole **název plánu** zadejte jedinečný název pro tento plán. Zákazníci uvidí tento název při rozhodování, který plán vybrat v rámci vaší nabídky. Použijte maximálně 50 znaků.
1. Vyberte **Vytvořit**.

## <a name="define-the-plan-setup"></a>Definování nastavení plánu

Karta **Nastavení plánu** vám umožní nastavit typ plánu, bez ohledu na to, jestli znovu používá technickou konfiguraci z jiného plánu, a jaké oblasti Azure by měl mít plán k dispozici v. Vaše odpovědi na této kartě budou mít vliv na to, která pole se zobrazí na jiných kartách tohoto plánu.

### <a name="select-the-plan-type"></a>Vyberte typ plánu.

- V seznamu **typ plánu** vyberte buď možnost **Šablona řešení** , nebo **spravovaná aplikace**.

Plán **šablony řešení** je spravován zcela zákazníkem. Plán **spravované aplikace** umožňuje vydavatelům spravovat aplikaci jménem zákazníka. Podrobnosti o těchto dvou plánech typů najdete v tématu [typy plánů](plan-azure-application-offer.md#types-of-plans).

### <a name="re-use-technical-configuration-optional"></a>Znovu použít technickou konfiguraci (volitelné)

Pokud jste v rámci této nabídky vytvořili více než jeden plán stejného typu a technická konfigurace je mezi nimi identická, můžete znovu použít technickou konfiguraci z jiného plánu. Toto nastavení nelze po publikování tohoto plánu změnit.

#### <a name="to-re-use-technical-configuration"></a>Opětovné použití technické konfigurace

1. Zaškrtněte políčko **Tento plán znovu používá technickou konfiguraci z jiného plánu stejného typu** .
1. V seznamu, který se zobrazí, vyberte základní plán, který chcete.

> [!NOTE]
> Když znovu použijete balíčky z jiného plánu, z tohoto plánu zmizí karta s celou **technickou konfigurací** . Pro tento plán se použijí i podrobnosti technické konfigurace z druhého plánu, včetně všech aktualizací, které v budoucnu provedete.

### <a name="select-azure-regions-clouds"></a>Výběr oblastí Azure (cloudy)

Váš plán se musí zpřístupnit aspoň v jedné oblasti Azure. Po publikování a dostupnosti plánu v konkrétní oblasti Azure nemůžete tuto oblast z vaší nabídky odebrat.

#### <a name="azure-global-region"></a>Globální oblast Azure

Globální zaškrtávací políčko **Azure** je ve výchozím nastavení zaškrtnuté. Díky tomu je váš plán dostupný pro zákazníky ve všech globálních oblastech Azure, které mají integraci komerčních webů na webu Marketplace. U plánů spravovaných aplikací můžete vybrat trhy, ve kterých chcete plán zpřístupnit.

Pokud chcete nabídku odebrat z této oblasti, zrušte zaškrtnutí políčka **Azure Global** .

#### <a name="azure-government-region"></a>Oblast Azure Government

Tato oblast nabízí řízený přístup pro zákazníky ze federální, státní, místní nebo samosprávnéové entity USA a také pro partnery, kteří jim mají nárok na jejich obsluhu. Jako vydavatel zodpovídáte za řízení dodržování předpisů, míry zabezpečení a osvědčené postupy. Azure Government používá fyzicky izolovaná datová centra a sítě (jenom v USA).

Služba Azure Government Services zpracovává data, která se vztahují na určité předpisy a požadavky státní správy. Příklad: FedRAMP, NIST 800,171 (DIB), ITAR, finanční úřad 1075, DoD – L4 a CJIS. Pro zajištění povědomí o certifikaci pro tyto programy můžete poskytnout až 100 odkazů, které je popisují. Můžou to být buď odkazy na váš výpis v programu, nebo odkazy na popisy dodržování předpisů s nimi na vašich vlastních webech. Tyto odkazy jsou viditelné pouze pro Azure Government zákazníky.

##### <a name="to-select-the-azure-government-region"></a>Výběr oblasti Azure Government

1. Zaškrtněte políčko **Azure Government** .
1. V části **Azure Government certifikace** vyberte **+ Přidat certifikaci (max 100)**.
1. Do zobrazených polí zadejte název a odkaz na certifikaci.
1. Chcete-li přidat další certifikaci, opakujte kroky 2 a 3.

Než budete pokračovat na další kartu, vyberte **Uložit koncept** .

## <a name="define-the-plan-listing"></a>Definice výpisu plánu

Na kartě **seznam plánů** můžete nakonfigurovat podrobnosti výpisu plánu. Tato karta zobrazuje konkrétní informace, které ukazují rozdíl mezi plány v rámci stejné nabídky. Můžete definovat název, souhrn a popis plánu, jak se mají zobrazit na komerčním webu Marketplace.

1. V poli **název plánu** se tady zobrazí název, který jste zadali dříve pro tento plán. Kdykoli ho můžete změnit. Tento název se zobrazí na komerčním webu Marketplace jako název softwarového plánu vaší nabídky a je omezený na 100 znaků.
1. V poli **Souhrn plánu** zadejte krátký souhrn plánu (ne nabídky). Tento souhrn je omezený na 100 znaků.
1. V poli **Popis plánu** vysvětlete, co dělá tento plán softwaru jedinečný a všechny rozdíly mezi ostatními plány v rámci vaší nabídky. Nepopisujte nabídku, jenom plán. Tento popis může obsahovat až 2 000 znaků.
1. Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="next-steps"></a>Další kroky

Proveďte některou z následujících akcí:

- Pokud konfigurujete plán šablony řešení, přejdete do [Konfigurace plánu šablony řešení](create-new-azure-apps-offer-solution.md).
- Pokud konfigurujete plán spravované aplikace, pokračujte tím, že [nakonfigurujete plán spravované aplikace](create-new-azure-apps-offer-managed.md).
