---
title: Monitorování zabezpečení kontejnerů v Azure Security Center
description: Naučte se kontrolovat stav zabezpečení vašich kontejnerů z Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 8ec06fc23b147eb3e4a5922242aa922063f4172c
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514148"
---
# <a name="monitor-the-security-of-your-containers"></a>Monitorování zabezpečení kontejnerů

Tato stránka vysvětluje použití funkcí zabezpečení kontejnerů popsaných v článku věnovaném [zabezpečení kontejnerů](container-security.md) v části koncepty.


## <a name="scan-your-arm-based-container-registries-for-vulnerabilities"></a>Prověřování registrů kontejnerů založených na ARM pro ohrožení zabezpečení 

1. Postup povolení kontroly ohrožení zabezpečení Azure Container Registry imagí:

    1. Ujistěte se, že jste v Azure Security Center cenové úrovně Standard.

    1. Na stránce **Nastavení cenové &** povolte pro vaše předplatné nepovinnou sadu kontejnerových registrací: ![ Povolení sady kontejnerů registrů kontejnerů.](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center je teď připravený ke skenování imagí, které se připravují do registru. 

      >[!NOTE]
      >Tato funkce se účtuje na základě obrázku.


1. Pokud chcete spustit kontrolu obrázku, nahrajte ho do svého registru. 

    Až se kontrola dokončí (obvykle po přibližně 2 minutách, ale může to být až 15 minut), výsledky jsou k dispozici jako Security Center doporučení.

1. Pokud si chcete prohlédnout nálezy, navštivte stránku **doporučení** . Pokud byly zjištěny problémy, zobrazí se následující doporučení:

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


## <a name="harden-your-containers-docker-hosts"></a>Posílit posílení hostitelů Docker kontejnerů

Security Center nepřetržitě monitoruje konfiguraci hostitelů Docker a vygeneruje doporučení zabezpečení, která odpovídají oborovým standardům.

Chcete-li zobrazit doporučení zabezpečení Azure Security Center pro hostitele Docker vašich kontejnerů:

1. Na Security Center navigačním panelu otevřete **compute & aplikace** a vyberte kartu **kontejnery** .

1. Volitelně můžete filtrovat seznam prostředků kontejneru na hostitele kontejnerů hostitelé.

    ![Filtr prostředků kontejneru](media/monitor-container-security/container-resources-filter.png)

1. V seznamu hostitelských počítačů kontejnerů vyberte jednu pro další prozkoumání.

    ![Doporučení hostitele kontejneru](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    Otevře se **Stránka informace o hostiteli kontejneru** s podrobnostmi o hostiteli a seznamem doporučení.

1. V seznamu doporučení vyberte doporučení, které chcete prozkoumat.

    ![Seznam doporučení hostitele kontejneru](media/monitor-container-security/container-host-rec.png)

1. Volitelně si přečtěte popis, informace, hrozby a kroky pro nápravu. 

1. V dolní části stránky vyberte **provést akci** .

    [![Tlačítko provést akci](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics se otevře s vlastní operací připravenou ke spuštění. Výchozí vlastní dotaz obsahuje seznam všech neúspěšných pravidel, která byla vyhodnocena, spolu s pokyny, které vám pomohou tyto problémy vyřešit.

    [![Akce Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. V případě potřeby selepšit parametry dotazu.

1. Pokud jste si jisti, že je příkaz vhodný a připravený pro hostitele, vyberte **Spustit**.



## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat funkce zabezpečení kontejneru Security Center. 

Další související materiály najdete na následujících stránkách: 

- [Security Center doporučení pro kontejnery](recommendations-reference.md#recs-containers)
- [Výstrahy na úrovni clusteru AKS](alerts-reference.md#alerts-akscluster)
- [Výstrahy na úrovni hostitele kontejneru](alerts-reference.md#alerts-containerhost)
