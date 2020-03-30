---
title: Monitorování služeb a aplikací Azure pomocí Grafany
description: Trasujte data Azure Monitor a Application Insights, abyste je mohli zobrazit v Grafaně.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 142e3e19c13710963d239a75bc237b63713c29cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672204"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Sledování služeb Azure v Grafaně
Teď můžete sledovat služby Azure a aplikace z [Grafany](https://grafana.com/) pomocí [modulu plug-in zdroje dat Azure Monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). Plugin shromažďuje data o výkonu aplikací shromážděná službou Azure Monitor, včetně různých protokolů a metrik. Tato data pak můžete zobrazit na řídicím panelu Grafana.

Pomocí následujících kroků nastavte server Grafana a sestavte řídicí panely pro metriky a protokoly z Azure Monitoru.

## <a name="set-up-a-grafana-server"></a>Nastavení serveru Grafana

### <a name="set-up-grafana-locally"></a>Nastavit Grafana místně
Chcete-li nastavit místní server Grafana, [stáhněte a nainstalujte grafanu do místního prostředí](https://grafana.com/grafana/download). Pokud chcete použít integraci Azure Monitoru, nainstalujte Grafana verze 5.3 nebo vyšší.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Nastavení Grafany v Azure prostřednictvím Azure Marketplace
1. Přejděte na Azure Marketplace a vyberte Grafana by Grafana Labs.

2. Vyplňte jména a podrobnosti. Vytvořte novou skupinu prostředků. Mějte přehled o hodnotách, které zvolíte pro uživatelské jméno virtuálního počítače, heslo virtuálního počítače a heslo správce serveru Grafana.  

3. Zvolte velikost virtuálního počítače a účet úložiště.

4. Nakonfigurujte nastavení konfigurace sítě.

5. Zobrazte souhrn a po přijetí podmínek použití vyberte **Vytvořit.**

6. Po dokončení nasazení vyberte **Přejít na skupinu prostředků**. Zobrazí se seznam nově vytvořených prostředků.

    ![Objekty skupiny prostředků Grafana](media/grafana-plugin/grafana1.png)

    Pokud vyberete skupinu zabezpečení sítě (*grafana-nsg* v tomto případě), uvidíte, že port 3000 se používá pro přístup k serveru Grafana.

7. Získejte veřejnou IP adresu serveru Grafana – vraťte se do seznamu zdrojů a vyberte **Veřejnou IP adresu**.

## <a name="sign-in-to-grafana"></a>Přihlásit se do Grafany

1. Pomocí IP adresy vašeho serveru otevřete stránku Přihlášení na *adrese\<http:// IP adresa\>:3000* nebo * \<DNSName>\:3000* ve vašem prohlížeči. Zatímco 3000 je výchozí port, všimněte si, že jste během instalace vybrali jiný port. Měli byste vidět přihlašovací stránku pro server Grafana, který jste vytvořili.

    ![Grafana přihlašovací obrazovka](./media/grafana-plugin/grafana-login-screen.png)

2. Přihlaste se pomocí *správce* uživatelského jména a hesla správce serveru Grafana, které jste vytvořili dříve. Pokud používáte místní nastavení, bude výchozím *heslem správce*a při prvním přihlášení budete požádáni o jeho změnu.

## <a name="configure-data-source-plugin"></a>Konfigurace pluginu zdroje dat

Po úspěšném přihlášení, měli byste vidět, že azure monitor zdroj dat plugin je již zahrnuta.

![Grafana obsahuje plugin Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Vyberte **Přidat zdroj dat,** chcete-li přidat a nakonfigurovat zdroj dat Azure Monitor.

2. Vyberte název zdroje dat a v rozevíracím souboru vyberte **Azure Monitor** jako typ.

3. Vytvoření instančního objektu – Grafana používá instanční objekt služby Azure Active Directory pro připojení k rozhraní mALINK Azure Monitor a shromažďování dat. Ke správě přístupu k prostředkům Azure musíte vytvořit nebo použít existující instanční objekt.
    * V [těchto pokynech](../../azure-resource-manager/resource-group-create-service-principal-portal.md) vytvořte instanční objekt. Zkopírujte a uložte ID klienta (ID adresáře), ID klienta (ID aplikace) a tajný klíč klienta (hodnota klíče aplikace).
    * Viz [Přiřazení aplikace k roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) pro přiřazení role Čtečky k aplikaci Azure Active Directory v rámci předplatného, skupiny prostředků nebo prostředku, který chcete monitorovat. 
    Rozhraní API analýzy protokolů vyžaduje [roli Čtečka analýzy protokolů](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), která zahrnuje oprávnění role čtečky a přidává k ní.

4. Zadejte podrobnosti o připojení k nastavení API, která chcete použít. Můžete se připojit ke všem nebo k některým z nich. 
    * Pokud se připojíte k metriky a protokoly v Azure Monitor, můžete znovu použít stejné přihlašovací údaje výběrem **stejné podrobnosti jako Rozhraní API Azure Monitor**.
    * Při konfiguraci pluginu můžete určit, který Azure Cloud chcete, aby plugin monitoroval (Public, Azure US Government, Azure Germany nebo Azure China).
    * Pokud používáte Application Insights, můžete také zahrnout rozhraní API pro přehledy aplikací a ID aplikace ke shromažďování metrik založených na přehledech aplikací. Další informace naleznete [v tématu Získání klíče rozhraní API a ID aplikace](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Některá pole zdroje dat jsou pojmenována jinak než jejich korelované nastavení Azure:
        > * ID klienta je ID adresáře Azure
        > * ID klienta je ID aplikace služby Azure Active Directory
        > * Tajný klíč klienta je hodnota klíče aplikace služby Azure Active Directory

5. Pokud používáte Application Insights, můžete také zahrnout rozhraní API pro přehledy aplikací a ID aplikace ke shromažďování metrik založených na přehledech aplikací. Další informace naleznete [v tématu Získání klíče rozhraní API a ID aplikace](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Vyberte **Uložit**a Grafana otestuje přihlašovací údaje pro každé rozhraní API. Měla by se zobrazit zpráva podobná následující.  
    ![Grafana zdroj dat config schválen](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Sestavení řídicího panelu Grafana

1. Přejděte na domovskou stránku Grafana a vyberte **Nový řídicí panel**.

2. V novém řídicím panelu vyberte **graf**. Můžete zkusit jiné možnosti vytváření grafů, ale tento článek používá *graf* jako příklad.

3. Na řídicím panelu se zobrazí prázdný graf. Klikněte na název panelu a vyberte **Upravit,** chcete-li do tohoto grafu zadat podrobnosti o datech, která chcete vykreslit.
    ![Grafana nový graf](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Vyberte zdroj dat Azure Monitor, který jste nakonfigurovali.
   * Shromažďování metrik Azure Monitor – v rozevíracím souboru služby vyberte **Azure Monitor.** Zobrazí se seznam selektorů, kde můžete vybrat zdroje a metriku, které chcete v tomto grafu sledovat. Ke shromažďování metrik z virtuálního počítače použijte obor názvů **Microsoft.Compute/VirtualMachines**. Po výběru virtuálních aplikací a metriky, můžete začít zobrazovat jejich data na řídicím panelu.
     ![Konfigurace grafu grafana pro Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Shromažďování dat protokolu Azure Monitor – v rozevíracím souboru služby vyberte **Azure Log Analytics.** Vyberte pracovní prostor, na který chcete zadávat dotaz, a nastavte text dotazu. Zde můžete zkopírovat libovolný dotaz protokolu, který již máte, nebo vytvořit nový. Při psaní dotazu se zobrazí technologie IntelliSense a navrhne možnosti automatického dokončování. Vyberte typ vizualizace, **tabulku časových** **řad** a spusťte dotaz.
    
     > [!NOTE]
     >
     > Výchozí dotaz dodaný s pluginem používá dvě makra: "$__timeFilter() a $__interval. 
     > Tato makra umožňují grafaně dynamicky vypočítat časový rozsah a časové zrnitosti při přiblížení části grafu. Tato makra můžete odebrat a použít filtr standardního času, například *TimeGenerated > ago(1h),* ale to znamená, že graf nebude podporovat funkci zvětšení.
    
     ![Grafana graf config pro Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Následuje jednoduchý řídicí panel se dvěma grafy. Ten vlevo zobrazuje procento procesoru dvou virtuálních počítačů. Graf vpravo zobrazuje transakce v účtu Azure Storage rozdělené podle typu rozhraní API transakce.
    ![Grafana Příklad dvou grafů](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Volitelné: Sledování vlastních metrik na stejném serveru Grafana

Můžete také nainstalovat Telegraf a InfluxDB shromažďovat a vykreslovat vlastní a agent založené metriky stejné instance Grafana. Existuje mnoho modulů plug-in zdroje dat, které můžete použít k vytvoření těchto metrik na řídicím panelu.

Toto nastavení můžete také znovu použít tak, aby zahrnovalo metriky ze serveru Prometheus. Použijte plugin pro zdroj dat Prometheus v galerii pluginů Grafany.

Zde jsou dobré referenční články o tom, jak používat Telegraf, InfluxDB, Prometheus a Docker
 - [Jak sledovat systémové metriky s TICK Stack na Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Řešení monitorování pro hostitele Dockeru, kontejnery a kontejnerové služby](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Tady je obrázek celého řídicího panelu Grafana, který obsahuje metriky z Azure Monitor a Application Insights.
![Grafana Příklad Metriky](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Pokročilé funkce Grafany

### <a name="variables"></a>Proměnné
Některé hodnoty dotazu lze vybrat prostřednictvím rozevíracích dat ui a aktualizovat v dotazu. Jako příklad zvažte následující dotaz:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Můžete nakonfigurovat proměnnou, která zobrazí seznam všech dostupných hodnot **řešení,** a potom aktualizovat dotaz tak, aby ji používal.
Chcete-li vytvořit novou proměnnou, klepněte na tlačítko Nastavení řídicího panelu v pravé horní části, vyberte **Proměnné**a potom **na Nový**.
Na stránce proměnné definujte zdroj dat a dotaz, který chcete spustit, abyste získali seznam hodnot.
![Grafana konfigurovat proměnnou](./media/grafana-plugin/grafana-configure-variable-dark.png)

Po vytvoření upravte dotaz tak, aby používal vybrané hodnoty, a grafy budou odpovídajícím způsobem reagovat:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana používat proměnné](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Vytvoření seznamů stop řídicího panelu

Jednou z mnoha užitečných funkcí Grafany je seznam skladeb na palubní desce. Můžete vytvořit více řídicích panelů a přidat je do seznamu stop, který konfiguruje interval pro každý řídicí panel, který se má zobrazit. Vyberte **Přehrát,** chcete-li zobrazit cyklus řídicích panelů. Můžete je zobrazit na velkém monitoru na stěně, abyste mohli vytvořit stavovou vývěsku pro vaši skupinu.

![Grafana Playlist Příklad](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste nastavili prostředí Grafana v Azure, budete se účtovat, když virtuální počítače běží, ať už je používáte nebo ne. Chcete-li se vyhnout vzniku dalších poplatků, vyčistěte skupinu prostředků vytvořenou v tomto článku.

1. V levé nabídce na webu Azure Portal klikněte na **Skupiny prostředků** a potom klikněte na **Grafana**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **Grafanu** a potom klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky
* [Přehled metrik Azure Monitor](data-platform.md)

