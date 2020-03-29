---
title: Sledování zabezpečení kontejnerů v Azure Security Center
description: Zjistěte, jak zkontrolovat stav zabezpečení kontejnerů z Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919528"
---
# <a name="monitoring-the-security-of-your-containers"></a>Sledování zabezpečení vašich kontejnerů

Tato stránka vysvětluje, jak používat funkce zabezpečení kontejneru popsané v [článku Zabezpečení kontejneru](container-security.md) v naší části koncepty.

Azure Security Center pokrývá následující tři aspekty zabezpečení kontejnerů:

- **Správa ohrožení zabezpečení** – pokud jste na standardní cenové úrovni Security Center (viz [ceny),](/azure/security-center/security-center-pricing)můžete prohledávat váš registr kontejnerů Azure založený na ARM při každém stisknutí nové bitové kopie. Skener (poháněný společností Qualys) prezentuje nálezy jako doporučení Security Center.
    Podrobné pokyny najdete v [tématu Hledání chyb zabezpečení v registrech kontejnerů](#scanning-your-arm-based-container-registries-for-vulnerabilities) níže.

- **Posílení zabezpečení hostitelů Dockeru vašich kontejnerů** – Security Center najde nespravované kontejnery hostované na virtuálních počítačích IaaS Linux nebo jiných počítačích s Linuxem se systémem Docker a průběžně porovnává konfigurace kontejnerů s referenčníhodnotou Dockeru centra pro internetovou bezpečnost (CIS). Centrum zabezpečení vás upozorní, pokud vaše kontejnery nesplňují žádný z ovládacích prvků. Nepřetržité sledování bezpečnostních rizik způsobených chybnou konfigurací je klíčovou součástí každého bezpečnostního programu. 
    Podrobné pokyny najdete v tématu [Posílení zabezpečení hostitelů Dockeru vašich kontejnerů](#hardening-your-containers-docker-hosts) níže.

- **Posílení zabezpečení clusterů služby Azure Kubernetes** – Centrum zabezpečení poskytuje doporučení, když najde chyby zabezpečení v konfiguraci clusterů služby Azure Kubernetes. Podrobnosti o konkrétních doporučeních, která se mohou zobrazit, naleznete v [doporučeních kubernetesservice](recommendations-reference.md#recs-containers).

- **Ochrana za běhu** – Pokud jste na standardní cenové úrovni Security Center, získáte ochranu před hrozbami v reálném čase pro kontejnerová prostředí. Security Center generuje výstrahy pro podezřelé aktivity na úrovni hostitele a clusteru AKS. Podrobnosti o příslušných výstrahách zabezpečení, které se mohou zobrazit, najdete v tématu [výstrahy pro clustery služby Azure Kubernetes](alerts-reference.md#alerts-akscluster) a [výstrahy pro kontejnery – oddíly na úrovni hostitele](alerts-reference.md#alerts-containerhost) referenční tabulky výstrah.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Vyhledávání chyb zabezpečení v registrech kontejnerů založených na armu 

1. Povolení prohledává chybu zabezpečení ibi obrázků registru kontejnerů Azure:

    1. Ujistěte se, že jste na standardní cenové úrovni Azure Security Center.

    1. Na stránce **Nastavení cenové &** povolte pro své předplatné ![volitelný balíček registrů kontejnerů: Povolení balíčku registrů kontejnerů](media/monitor-container-security/enabling-container-registries-bundle.png)

        Centrum zabezpečení je nyní připraveno ke skenování bitových kopií, které jsou zatlačeny do registru. 

        >[!NOTE]
        >Tato funkce se účtuje podle obrázku.


1. Chcete-li spustit prohledávací akci, zatlačte ji do registru. 

    Po dokončení skenování (obvykle po přibližně 10 minutách) jsou zjištění k dispozici v doporučeních centra zabezpečení.
    

1. Zjištění zobrazíte na stránce **Doporučení.** Pokud byly zjištěny problémy, zobrazí se následující doporučení:

    ![Doporučení k nápravě problémů ](media/monitor-container-security/acr-finding.png)


1. Vyberte doporučení. 
    Otevře se stránka podrobností o doporučení s dalšími informacemi. Tyto informace zahrnují seznam registrů se zranitelnými obrázky ("Ovlivněné zdroje") a nápravné kroky. 

1. Vyberte konkrétní registr, chcete-li zobrazit úložiště v něm, které mají ohrožené úložiště.

    ![Výběr registru](media/monitor-container-security/acr-finding-select-registry.png)

    Otevře se stránka s podrobnostmi o registru se seznamem ohrožených úložišť.

1. Vyberte konkrétní úložiště, chcete-li zobrazit úložiště v něm, které mají zranitelné obrázky.

    ![Výběr úložiště](media/monitor-container-security/acr-finding-select-repository.png)

    Otevře se stránka s podrobnostmi o úložišti. Uvádí zranitelné obrázky spolu s posouzením závažnosti nálezů.

1. Vyberte konkrétní obrázek, abyste viděli chyby zabezpečení.

    ![Výběr obrázků](media/monitor-container-security/acr-finding-select-image.png)

    Otevře se seznam nálezů vybraného obrázku.

    ![Seznam zjištění](media/monitor-container-security/acr-findings.png)

1. Chcete-li se dozvědět více o hledání, vyberte hledání. 

    Otevře se podokno podrobností o zjištěních.

    [![Podokno podrobností o hledání](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Toto podokno obsahuje podrobný popis problému a odkazy na externí prostředky, které pomáhají zmírnit hrozby.

1. Postupujte podle pokynů v části náprava v tomto podokně.

1. Pokud jste provedli kroky potřebné k nápravě problému se zabezpečením, nahraďte bitovou kopii v registru:

    1. Posuňte aktualizovaný obrázek. Tím se spustí skenování. 
    
    1. Na stránce doporučení najdete doporučení "Chyby zabezpečení v ibi objektů registru kontejnerů Azure by měly být opraveny". 
    
        Pokud se doporučení stále zobrazuje a obrázek, který jste zpracovali, se stále zobrazuje v seznamu ohrožených obrázků, zkontrolujte kroky nápravy znovu.

    1. Pokud jste si jisti, že aktualizovaný obrázek byl posunut, naskenován a již se v doporučení nezobrazuje, odstraňte "starou" zranitelnou bitovou kopii z registru.


## <a name="hardening-your-containers-docker-hosts"></a>Posílení zabezpečení hostitelů Dockeru vašich kontejnerů

Security Center neustále monitoruje konfiguraci vašich hostitelů Dockeru a generuje doporučení zabezpečení, která odrážejí oborové standardy.

Zobrazení doporučení zabezpečení Azure Security Center pro hostitele Dockeru vašich kontejnerů:

1. Na navigačním panelu Centra zabezpečení otevřete **výpočetní & aplikace** a vyberte kartu **Kontejnery.**

1. Volitelně můžete filtrovat seznam prostředků kontejneru hostitelům hostitelů kontejnerů.

    ![Kontejnerové prostředky, filtr](media/monitor-container-security/container-resources-filter.png)

1. Ze seznamu hostitelských počítačů kontejneru vyberte jeden, který chcete dále prozkoumat.

    ![Doporučení hostitele kontejneru](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    Otevře **se stránka s informacemi o hostiteli kontejneru** s podrobnostmi o hostiteli a seznamem doporučení.

1. Ze seznamu doporučení vyberte doporučení pro další zkoumání.

    ![Seznam doporučení hostitele kontejneru](media/monitor-container-security/container-host-rec.png)

1. Volitelně si přečtěte popis, informace, hrozby a nápravné kroky. 

1. V dolní části stránky vyberte **Provést akci.**

    [![Tlačítko Provést akci](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Otevře se služba Log Analytics s vlastní operací připravenou ke spuštění. Výchozí vlastní dotaz obsahuje seznam všech chybných pravidel, která byla posouzena, spolu s pokyny, které vám pomohou vyřešit problémy.

    [![Akce Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Vyladit parametry dotazu a vyberte **Spustit,** když jste si jisti, že je připraven pro hostitele. 



## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak používat funkce zabezpečení kontejneru Centra zabezpečení. 

Další související materiály naleznete na následujících stránkách: 

- [Doporučení Centra zabezpečení pro kontejnery](recommendations-reference.md#recs-containers)
- [Výstrahy pro úroveň clusteru AKS](alerts-reference.md#alerts-akscluster)
- [Výstrahy pro úroveň hostitele kontejneru](alerts-reference.md#alerts-containerhost)
