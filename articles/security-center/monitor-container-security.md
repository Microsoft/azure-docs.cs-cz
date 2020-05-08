---
title: Monitorování zabezpečení kontejnerů v Azure Security Center
description: Naučte se kontrolovat stav zabezpečení vašich kontejnerů z Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 900398a701659bff593df042db16890792e5cffd
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744732"
---
# <a name="monitoring-the-security-of-your-containers"></a>Monitorování zabezpečení kontejnerů

Tato stránka vysvětluje použití funkcí zabezpečení kontejnerů popsaných v článku věnovaném [zabezpečení kontejnerů](container-security.md) v části koncepty.

Azure Security Center pokrývá následující tři aspekty zabezpečení kontejneru:

- **Správa ohrožení zabezpečení** – Pokud používáte cenovou úroveň Standard Security Center (viz [ceny](/azure/security-center/security-center-pricing)), můžete Azure Container Registry na bázi ARM kontrolovat při každém vložení nového obrázku. Skener (s technologií Qualys) prezentuje závěry jako doporučení Security Center.
    Podrobné pokyny najdete v části [Kontrola registrů kontejnerů](#scanning-your-arm-based-container-registries-for-vulnerabilities) v následujících chybách.

- **Posílení zabezpečení kontejnerů Docker hostitelé** – Security Center najde nespravované kontejnery hostované na virtuálních počítačích s IaaS Linux nebo v jiných počítačích se systémem Linux, na kterých běží Docker, a průběžně porovnává konfigurace kontejnerů s centrem pro testování přes Internet Security (SNS) Docker. Security Center vás upozorní na to, že kontejnery nevyhovují žádnému z ovládacích prvků. Nepřetržité monitorování rizik zabezpečení z důvodu neplatných konfigurací je zásadní součástí jakéhokoli programu zabezpečení. 
    Podrobné pokyny najdete v článku [posílení zabezpečení kontejnerů Docker pro kontejnery](#hardening-your-containers-docker-hosts) .

- **Posílení zabezpečení clusterů služby Azure Kubernetes** – Security Center poskytuje doporučení při hledání ohrožení zabezpečení v konfiguraci clusterů služby Azure Kubernetes. Podrobnosti o specifických doporučeních, která se mohou zobrazit, najdete v tématu [věnovaném doporučením služby Kubernetes](recommendations-reference.md#recs-containers).

- **Ochrana modulem runtime** – Pokud používáte cenovou úroveň Standard Security Center, získáte ochranu před hrozbami v reálném čase pro vaše kontejnerová prostředí. Security Center generuje výstrahy pro podezřelé aktivity na úrovni hostitele a clusteru AKS. Podrobnosti o relevantních výstrahách zabezpečení, které se mohou zobrazit, najdete v částech [výstrahy pro clustery služby Azure Kubernetes](alerts-reference.md#alerts-akscluster) a [výstrahy pro kontejnery – úroveň hostitele](alerts-reference.md#alerts-containerhost) v referenční tabulce výstrahy.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Kontrola registrů kontejnerů založených na ARM pro ohrožení zabezpečení 

1. Postup povolení kontroly ohrožení zabezpečení Azure Container Registry imagí:

    1. Ujistěte se, že jste v Azure Security Center cenové úrovně Standard.

    1. Na stránce **Nastavení cenové &** povolte pro vaše předplatné nepovinnou sadu kontejnerových registrací ![: povolení sady kontejnerů registrů kontejnerů.](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center je teď připravený ke skenování imagí, které se připravují do registru. 

        >[!NOTE]
        >Tato funkce se účtuje na základě obrázku.


1. Pokud chcete spustit kontrolu obrázku, nahrajte ho do svého registru. 

    Až se kontrola dokončí (obvykle po přibližně 10 minutách), výsledky jsou k dispozici v Security Center doporučení.
    

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


## <a name="hardening-your-containers-docker-hosts"></a>Posílení zabezpečení kontejnerů Docker

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
