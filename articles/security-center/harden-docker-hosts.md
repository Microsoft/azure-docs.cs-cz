---
title: Použití Azure Security Center k posílení zabezpečení hostitelů Docker a ochraně kontejnerů
description: Jak chránit hostitele Docker a ověřit, jestli jsou kompatibilní s testem služby CIS Docker
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 500fa45db7e0e6bffb587d9d352ee1ab49f14703
ms.sourcegitcommit: 5b69ba21787c07547edfbfd5254eaf34315cfadd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712268"
---
# <a name="harden-your-docker-hosts"></a>Posílení zabezpečení hostitelů Dockeru

Azure Security Center identifikuje nespravované kontejnery hostované na virtuálních počítačích s IaaS Linux nebo jiné počítače se systémem Linux s kontejnery Docker. Security Center nepřetržitě vyhodnocuje konfiguraci těchto kontejnerů. Pak je porovná s [centrem testování v Docker pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Security Center zahrnuje celou RuleSet srovnávacího testu služby CI Docker a upozorní vás, pokud vaše kontejnery nevyhovují žádnému z ovládacích prvků. Když nalezne chybnou konfiguraci, Security Center vygeneruje doporučení zabezpečení. Pomocí **stránky doporučení** Security Center můžete zobrazit doporučení a opravit problémy.

V případě, že jsou chyby zabezpečení zjištěny, jsou seskupeny do jednoho doporučení.

>[!NOTE]
> Tyto kontroly srovnávacích testů služby ci se nespouštějí na AKS instancích nebo virtuálních počítačích spravovaných datacihly.

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|Vyžaduje [Azure Defender pro servery](defender-for-servers-introduction.md) .|
|Požadované role a oprávnění:|**Čtenář** v pracovním prostoru, ke kterému se hostitel připojuje|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Identifikace a náprava ohrožení zabezpečení v konfiguraci Docker

1. V nabídce Security Center otevřete stránku **doporučení** .

1. Filtr na **ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by měl být opraven** a vybrat doporučení.

    Stránka s doporučením zobrazuje ovlivněné prostředky (hostitelé Docker). 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Doporučení k nápravě ohrožení zabezpečení v konfiguraci zabezpečení kontejnerů ":::

1. Chcete-li zobrazit a opravit ovládací prvky CIS, které konkrétní hostitel nezdařil, vyberte hostitele, kterého chcete prozkoumat. 

    > [!TIP]
    > Pokud jste spustili na stránce inventarizace assetů a dosáhli jsme tohoto doporučení, ybrat na stránce doporučení tlačítko **provést akci** .
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Doporučení k nápravě ohrožení zabezpečení v konfiguraci zabezpečení kontejnerů ":::

    Log Analytics se otevře s vlastní operací připravenou ke spuštění. Výchozí vlastní dotaz obsahuje seznam všech neúspěšných pravidel, která byla vyhodnocena, spolu s pokyny, které vám pomohou tyto problémy vyřešit.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Doporučení k nápravě ohrožení zabezpečení v konfiguraci zabezpečení kontejnerů ":::

1. V případě potřeby selepšit parametry dotazu.

1. Pokud jste si jisti, že je příkaz vhodný a připravený pro hostitele, vyberte **Spustit**.


## <a name="next-steps"></a>Další kroky

Posílení zabezpečení Docker je pouze jedním aspektem funkcí zabezpečení kontejneru Security Center. 

Další informace o [zabezpečení kontejnerů najdete v Security Center](container-security.md).