---
title: Jak používat Azure Defender pro Registry kontejnerů
description: Přečtěte si, jak používat Azure Defender pro Registry kontejnerů ke skenování imagí v registrech.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 79a0db3b9f81368fbdaace5be3fd94ad29649291
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532597"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Použití Azure Defenderu pro Registry kontejnerů k prohledávání imagí pro ohrožení zabezpečení

Tato stránka vysvětluje použití integrovaného skeneru ohrožení zabezpečení ke skenování imagí kontejneru uložených v Azure Container Registry na základě Azure Resource Manager.

Pokud je povolený **Azure Defender pro Registry kontejnerů** , bude se okamžitě kontrolovat všechny image, které do registru zadáte. Kromě toho se kontroluje i všechny image vyhledané během posledních 30 dnů. 

Když skener ohlásí chyby zabezpečení pro Security Center, Security Center jako doporučení prezentuje závěry a související informace. Kromě toho jsou zde uvedené informace související s informacemi, jako jsou například nápravné kroky, relevantní CVEs, CVSS skóre a další. Zjištěná ohrožení zabezpečení můžete zobrazit pro jedno nebo více předplatných nebo pro konkrétní registr.

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|**Azure Defender pro Registry kontejnerů** se účtuje, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Podporované registry a Image:|![Ano ](./media/icons/yes-icon.png) . ACR Registry hostované pro Linux, které jsou přístupné z veřejného Internetu a poskytují přístup k prostředí.<br>![Žádné ](./media/icons/no-icon.png) Registry ACR hostované v systému Windows.<br>![Žádné ](./media/icons/no-icon.png) ' privátní ' Registry-Security Center vyžaduje, aby vaše registry byly přístupné z veřejného Internetu. Security Center se aktuálně nemůže připojit k registrům s přístupem, které jsou omezené pomocí brány firewall, koncového bodu služby nebo privátních koncových bodů, jako je například privátní odkaz Azure.<br>![Žádné ](./media/icons/no-icon.png) Image minimalist, jako jsou [pomocné image Docker](https://hub.docker.com/_/scratch/) , nebo Image "Distroless", které obsahují jenom aplikaci a její závislosti modulu runtime bez správce balíčků, prostředí nebo operačního systému.|
|Požadované role a oprávnění:|Role **Čtenář zabezpečení** a [Azure Container Registry Reader](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Identifikace ohrožení zabezpečení v obrázcích ve službě Azure Container Registry 

1. Postup povolení kontroly ohrožení zabezpečení imagí uložených v Azure Container Registry na základě Azure Resource Manager:

    1. Povolte **Azure Defender pro Registry kontejnerů** pro vaše předplatné.

        Security Center je teď připravený ke skenování imagí v registrech.

        >[!NOTE]
        > Tato funkce se účtuje na základě obrázku.

1. Kontroly imagí se spouštějí při každém vložení nebo importu a v případě, že se image obnovila během posledních 30 dnů. 

    Až se kontrola dokončí (obvykle po přibližně 2 minutách, ale může to být až 15 minut), výsledky jsou k dispozici jako Security Center doporučení.

1. [Zobrazte a opravte zjištění, jak je vysvětleno níže](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Identifikace ohrožení zabezpečení v obrázcích v jiných registrech kontejnerů 

1. Pomocí nástrojů ACR přeneste image do registru z Docker Hub nebo z Microsoft Container Registry.  Po dokončení importu budou importované image prohledávány pomocí Azure Defenderu. 

    Další informace najdete v [importu imagí kontejneru do registru kontejneru](../container-registry/container-registry-import-images.md) .

    Až se kontrola dokončí (obvykle po přibližně 2 minutách, ale může to být až 15 minut), výsledky jsou k dispozici jako Security Center doporučení.

1. [Zobrazte a opravte zjištění, jak je vysvětleno níže](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Zobrazit a opravit zjištění

1. Pokud si chcete prohlédnout nálezy, navštivte stránku **doporučení** . Pokud byly zjištěny problémy, je **třeba opravit slabá místa na doporučení v Azure Container Registry imagí** .

    ![Doporučení k nápravě problémů ](media/monitor-container-security/acr-finding.png)

1. Vyberte doporučení. 

    Otevře se stránka s podrobnostmi o doporučení s dalšími informacemi. Tyto informace obsahují seznam registrů s ohroženými bitovými kopiemi ("ovlivněné prostředky") a nápravné kroky. 

1. Výběrem konkrétního registru zobrazíte úložiště, která obsahují zranitelná úložiště.

    ![Výběr registru](media/monitor-container-security/acr-finding-select-registry.png)

    Otevře se stránka s podrobnostmi o registru se seznamem ovlivněných úložišť.

1. Vyberte konkrétní úložiště, ve kterém se zobrazí úložiště obsahující zranitelné obrázky.

    ![Výběr úložiště](media/monitor-container-security/acr-finding-select-repository.png)

    Otevře se stránka s podrobnostmi úložiště. Obsahuje seznam ohrožených imagí spolu s posouzením závažnosti zjištění.

1. Vyberte konkrétní obrázek pro zobrazení ohrožení zabezpečení.

    ![Vybrat obrázky](media/monitor-container-security/acr-finding-select-image.png)

    Otevře se seznam zjištění pro vybraný obrázek.

    ![Seznam zjištění](media/monitor-container-security/acr-findings.png)

1. Pokud se chcete dozvědět víc o hledání, vyberte hledání. 

    Otevře se podokno Podrobnosti o zjištěních.

    [![Podokno podrobností o zjištěních](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Toto podokno obsahuje podrobný popis problému a odkazy na externí prostředky, které pomáhají zmírnit hrozby.

1. Postupujte podle kroků v části náprava v tomto podokně.

1. Pokud jste provedli kroky potřebné k nápravě problému se zabezpečením, nahraďte image v registru:

    1. Odeslat aktualizovaný obrázek Tím se aktivuje kontrola. 
    
    1. Podívejte se na stránku doporučení pro doporučení ohrožení zabezpečení v Azure Container Registry imagí by mělo být opravené. 
    
        Pokud se doporučení stále zobrazuje a obrázek, který jste nastavili, se stále zobrazuje v seznamu ohrožených imagí, znovu ověřte postup nápravy.

    1. Když si jste jistí, že se aktualizovaná image nahrála, prohledala a už se nezobrazuje v doporučení, odstraňte z registru starou ohrožený image.


## <a name="disable-specific-findings-preview"></a>Zakázat konkrétní zjištění (Preview)

Pokud máte organizaci, kterou je třeba ignorovat, místo toho, aby ji bylo možné opravit, můžete ji případně zakázat. Zakázané závěry neovlivňují vaše zabezpečené skóre nebo generují nežádoucí hluk.

Pokud hledání odpovídá kritériím, která jste definovali v pravidlech vypnutí, nezobrazí se v seznamu zjištění. Mezi typické scénáře patří:

- Zakázat zjištění se závažností pod středníky
- Zakázat nálezy, které nejsou opravné
- Zakázat zjištění pomocí CVSS skóre pod 6,5
- Zakázat nálezy s určitým textem v rámci kontroly zabezpečení nebo kategorie (například "RedHat", "CentOS aktualizace zabezpečení pro sudo")

> [!IMPORTANT]
> Pokud chcete vytvořit pravidlo, potřebujete oprávnění k úpravám zásad v Azure Policy.
>
> Další informace najdete v [Azure Policy oprávnění služby Azure RBAC](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

Můžete použít kterékoli z následujících kritérií: 

- ID hledání 
- Kategorie
- Kontrolu zabezpečení 
- Skóre CVSS V3
- Závažnost 
- Stav oprav 

Vytvoření pravidla:

1. Na stránce s podrobnostmi o doporučeních **by se měla opravit ohrožení zabezpečení v Azure Container Registry imagí**, vyberte **zakázat pravidlo**.
1. Vyberte příslušný obor.
1. Definujte kritéria.
1. Vyberte **použít pravidlo**.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Vytvoření pravidla zákazu pro zjištění v registru pro VA":::

1. Postup zobrazení, přepsání nebo odstranění pravidla: 
    1. Vyberte **zakázat pravidlo**.
    1. V seznamu oborů se předplatná s aktivními pravidly zobrazí jako **použitá pravidla**.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Vytvoření pravidla zákazu pro zjištění v registru pro VA":::
    1. Chcete-li zobrazit nebo odstranit pravidlo, vyberte nabídku se třemi tečkami ("...").


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o Azure Defenderu](azure-defender.md)