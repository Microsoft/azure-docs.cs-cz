---
title: Principy zásad zabezpečení, iniciativ a doporučení v Azure Security Center
description: Přečtěte si o zásadách zabezpečení, iniciativách a doporučeních v Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176429"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Co jsou zásady zabezpečení, iniciativy a doporučení?

Security Center aplikuje na vaše předplatná bezpečnostní podněty. Tyto iniciativy obsahují jednu nebo více zásad zabezpečení. Každá z těchto zásad má za následek doporučení zabezpečení pro zlepšení stav zabezpečení. Tato stránka podrobně vysvětluje každou z těchto nápadů.


## <a name="what-is-a-security-policy"></a>Co je zásada zabezpečení?

Definice zásad Azure vytvořená v Azure Policy je pravidlo týkající se konkrétních podmínek zabezpečení, které chcete kontrolovat. Předdefinované definice zahrnují věci, jako je například řízení toho, jaký typ prostředků lze nasadit nebo vynucovat použití značek u všech prostředků. Můžete také vytvořit vlastní definice zásad.

Pokud chcete implementovat tyto definice zásad (ať už předdefinované, nebo vlastní), budete je muset přiřadit. Jakékoli z těchto zásad můžeme přiřadit prostřednictvím webu Azure Portal, PowerShellu nebo Azure CLI.

V Azure Policy existují různé typy zásad. Security Center hlavně používá zásady auditu, které kontrolují konkrétní podmínky a konfigurace a pak hlásí dodržování předpisů. K dispozici jsou také zásady "vynutilit", které lze použít k aplikování zabezpečených nastavení.

## <a name="what-is-a-security-initiative"></a>Co je iniciativa zabezpečení?

Iniciativa Azure je kolekce definic zásad Azure, neboli pravidel, která jsou seskupená do konkrétního cíle nebo účelu. Iniciativy Azure zjednodušují správu zásad tím, že seskupují sadu zásad dohromady a logicky jako jednu položku.

Bezpečnostní iniciativa definuje požadovanou konfiguraci vašich úloh a pomáhá zajistit dodržování požadavků na zabezpečení vaší společnosti nebo regulačních orgánů.

Podobně jako zásady zabezpečení se Security Center iniciativy vytvářejí také v Azure Policy. [Azure Policy](../governance/policy/overview.md) můžete použít ke správě zásad, vytváření iniciativ a přiřazování iniciativ k několika předplatným nebo pro celou skupinu pro správu.

Výchozí iniciativa automaticky přiřazená každému předplatnému v Azure Security Center je srovnávací test zabezpečení Azure. Tento srovnávací test je uživatelsky vytvořenou sadou základních pokynů pro zabezpečení a dodržování předpisů v závislosti na běžných architekturách dodržování předpisů, které jsou specifické pro Azure. Toto široce uznávané sestavování srovnávacích testů v ovládacích prvcích z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se soustředí na zabezpečení zaměřené na Cloud. Přečtěte si další informace o [srovnávacím testu zabezpečení Azure](../security/benchmarks/introduction.md).

Security Center nabízí následující možnosti pro práci s iniciativami a zásadami zabezpečení:

- **Zobrazit a upravit integrovanou výchozí iniciativu** – když povolíte Security Center, iniciativa s názvem "Azure Security test" se automaticky přiřadí všem Security Center registrovaným předplatným. Pokud chcete tuto iniciativu přizpůsobit, můžete v ní povolit nebo zakázat jednotlivé zásady úpravou parametrů zásad. V seznamu [integrovaných zásad zabezpečení](./policy-reference.md) se dozvíte, jaké možnosti jsou dostupné předem.

- **Přidejte vlastní iniciativy** – Pokud chcete přizpůsobit iniciativy zabezpečení použité pro vaše předplatné, můžete to udělat v rámci Security Center. Pak se dostanou doporučení, pokud vaše počítače nevyhovují vytvořeným zásadám. Pokyny k sestavování a přiřazování vlastních zásad najdete v tématu [použití vlastních iniciativ a zásad zabezpečení](custom-security-policies.md).

- **Přidání standardů dodržování předpisů regulativního předpisu jako iniciativ** – řídicí panel dodržování předpisů Security Centere zobrazuje stav všech posouzení v rámci vašeho prostředí v kontextu určitého standardu nebo nařízení (například Azure SNS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Další informace najdete v tématu [zlepšení dodržování legislativních předpisů](security-center-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Co je to doporučení zabezpečení?

Pomocí zásad Security Center pravidelně analyzují stav dodržování předpisů vašich prostředků, aby se identifikovaly potenciální slabá místa zabezpečení a slabé stránky. Pak vám poskytne doporučení k nápravě těchto problémů. Doporučení jsou výsledkem vyhodnocení prostředků proti relevantním zásadám a určení prostředků, které nesplňují vaše definované požadavky.

Security Center provádí doporučení zabezpečení na základě zvolených iniciativ. Když se zásady z vaší iniciativy porovnají s vašimi prostředky a zjistíte, že jeden nebo více nedodržuje předpisy, zobrazí se jako doporučení v Security Center.

Doporučení jsou akce, které můžete provést při zabezpečení a posílení zdrojů. Každé doporučení poskytuje následující informace:

- Stručný popis problému
- Kroky k nápravě, které je potřeba provést, aby bylo možné implementovat doporučení.
- Ovlivněné prostředky

V praxi funguje takto:

1. Srovnávací test zabezpečení Azure je ***iniciativa***
1. Zahrnuje ***zásadu*** , která vyžaduje, aby všechny účty Azure Storage omezily přístup k síti, takže omezí jejich útok. Tato zásada se nazývá "účty úložiště by měla omezit přístup k síti pomocí pravidel virtuální sítě" a je možné ji zakázat nebo povolit z Azure Policy
1. Pokud Azure Security Center najde účet Azure Storage na některém z chráněných předplatných, vyhodnotí tyto účty a zjistí, jestli jsou chráněné pomocí pravidel virtuální sítě. Pokud nejsou, zobrazí se ***doporučení*** k opravě této situace a posílení zabezpečení těchto prostředků. 

Iniciativa (1) tedy zahrnuje zásady (2), které vygenerují doporučení v případě potřeby (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Zobrazení vztahu mezi doporučením a zásadou

Jak je uvedeno výše, Security Center integrovaná doporučení jsou založená na testu zabezpečení Azure. Téměř každé doporučení má základní zásadu, která je odvozena z požadavku ve srovnávacím testu.

Při prohlížení podrobností doporučení je často užitečné, abyste mohli zobrazit základní zásady. Pro každé doporučení podporované zásadou použijte odkaz **Zobrazit definici zásad** ze stránky s podrobnostmi doporučení a přejít přímo na položku Azure Policy pro příslušné zásady:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Odkaz na stránku Azure Policy pro konkrétní zásadu podporující doporučení":::

Pomocí tohoto odkazu můžete zobrazit definici zásady a zkontrolovat logiku hodnocení. 

Pokud si prohlédnete seznam doporučení v našich [doporučeních o zabezpečení](recommendations-reference.md), přečtěte si také odkazy na stránky definice zásad:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Přístup na stránku Azure Policy pro konkrétní zásadu přímo z referenční stránky Azure Security Center doporučení":::


## <a name="next-steps"></a>Další kroky

Tato stránka je vysvětlena na vysoké úrovni, základní koncepty a vztahy mezi zásadami, iniciativami a doporučeními. Související informace najdete v těchto tématech:

- [Vytváření vlastních iniciativ](custom-security-policies.md)
- [Vypnutí zásad zabezpečení pro zákaz doporučení](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Naučte se, jak upravit zásady zabezpečení v Azure Policy](../governance/policy/tutorials/create-and-manage.md)