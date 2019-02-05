---
title: Monitorování služby Azure a aplikací pomocí služby Grafana
description: Trasy Azure Monitor a Application Insights data, můžete je zobrazit v Grafana.
services: azure-monitor
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: ''
ms.openlocfilehash: fc963987b45751aab33035a83b2b477129e9a756
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730896"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorování služeb Azure v Grafana
Teď můžete monitorovat služby a aplikace z Azure [Grafana](https://grafana.com/) pomocí [zdroje dat monitorování Azure plugin](https://grafana.com/plugins/grafana-azure-monitor-datasource). Modul plug-in shromažďuje data o výkonu aplikace shromážděná službou Azure Monitor, včetně různé protokoly a metriky. Tato data pak můžete zobrazit na řídicím panelu Grafana.

Modul plug-in je aktuálně ve verzi preview.

Následujícím postupem nastavit Grafana server a sestavovat řídicí panely pro metriky a protokoly z Azure monitoru.

## <a name="set-up-a-grafana-server"></a>Nastavení serveru Grafana

### <a name="set-up-grafana-locally"></a>Místní nastavení služby Grafana
Nastavit místní server Grafana [stáhněte a nainstalujte v místním prostředí Grafana](https://grafana.com/grafana/download). K používání integrace služby Log Analytics modul plug-in, nainstalujte Grafana verze 5.3 nebo novější.
### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Nastavit Grafana v Azure prostřednictvím Azure Marketplace
1. Přejít na Azure Marketplace a vyberte Grafana pomocí služby Grafana Labs.

2. Zadejte názvy a podrobnosti. Vytvořte novou skupinu prostředků. Udržovat přehled o hodnoty, které jste vybrali pro uživatelské jméno virtuálního počítače, heslo virtuálního počítače a služby Grafana heslo správce serveru.  

3. Zvolte velikost virtuálního počítače a účtu úložiště.

4. Konfigurace nastavení konfigurace sítě.

5. Zobrazit souhrn a vyberte **vytvořit** po přijetí podmínek použití.

6. Po dokončení nasazení vyberte **přejít ke skupině prostředků**. Zobrazí seznam nově vytvořené prostředky.

    ![Grafana objektů skupiny prostředků](media/grafana-plugin/grafana1.png)

    Pokud vyberete skupinu zabezpečení sítě (*grafana-nsg* v tomto případě), uvidíte, že je pro přístup k serveru Grafana používá port 3000.

7. Získejte veřejnou IP adresu serveru Grafana - přejděte zpět do seznamu prostředků a vyberte **veřejnou IP adresu**.

## <a name="log-in-to-grafana"></a>Přihlaste se do Grafany

1. Pomocí IP adresy serveru, otevřete přihlašovací stránku na *http://\<IP adresu\>: 3000* nebo  *\<DNSName >\:3000* v prohlížeči. Zatímco 3000 je výchozím portem, mějte na paměti, že je možná zvolen jiný port během instalace. Měli byste vidět přihlašovací stránku pro server Grafana, kterou jste vytvořili.

    ![Grafana přihlašovací obrazovky](./media/grafana-plugin/grafana-login-screen.png)

2. Přihlaste se pomocí uživatelského jména *správce* a heslo správce serveru Grafana jste vytvořili dříve. Pokud používáte místní nastavení, bude výchozí heslo *správce*, a by být odeslán požadavek na změnit při prvním přihlášení.

## <a name="configure-data-source-plugin"></a>Konfigurace modulu plug-in zdroje dat

Po úspěšném přihlášení, měli byste vidět, že modul plug-in Azure Monitor datový zdroj je již součástí.

![Grafana zahrnuje modul plug-in Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Vyberte **přidat zdroj dat** přidání a konfigurace zdroje dat monitorování Azure.

2. Vyberte název zdroje dat a vyberte **Azure Monitor** jako typ z rozevíracího seznamu.

3. Vytvoření instančního objektu – Grafana instanční objekt Azure Active Directory používá pro připojení k rozhraní API služby Azure Monitor a shromažďovat data. Musíte vytvořit nebo použít existující instanční objekt služby, ke správě přístupu k prostředkům Azure.
    * Zobrazit [tyto pokyny](../../azure-resource-manager/resource-group-create-service-principal-portal.md) k vytvoření instančního objektu. Zkopírujte a uložte vašeho tenanta, ID (ID adresáře), ID klienta (ID aplikace) a tajný klíč klienta (hodnota klíče aplikace).
    * Zobrazit [přiřazení aplikace k roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) přiřadit role Čtenář pro aplikace Azure Active Directory pro předplatné, skupinu prostředků nebo prostředek chcete monitorovat. 
    Rozhraní API služby Log Analytics vyžaduje [role Čtenář Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), která zahrnuje všechna oprávnění role čtenáře a přidá do něj.

4. Zadejte podrobnosti připojení k rozhraním API, které chcete použít. Můžete připojit ke všem nebo k některé z nich. 
    * Pokud připojíte k Azure Monitor (Chcete-li shromažďovat metriky) a Azure Log Analytics (pro data protokolu), můžete znovu použít stejné přihlašovací údaje tak, že vyberete **stejné informace jako rozhraní API služby Azure Monitor**.
    * Při konfiguraci modulu plug-in, můžete určit, který Cloud Azure chcete modul plug-in pro sledování (veřejné, Azure US Government, Azure Germany nebo Azure China).
    * Pokud používáte Application Insights, můžete použít také rozhraní API Application Insights a ID aplikace shromažďovat metriky na základě služby Application Insights. Další informace najdete v tématu [získání klíče rozhraní API a ID aplikace](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Některá pole zdroje dat jsou pojmenované jinak než jejich korelační nastavení služby Azure:
        >     * ID tenanta se ID adresáře Azure
        >     * ID klienta je ID aplikace Azure Active Directory
        >     * Tajný kód klienta je hodnota klíče aplikace Azure Active Directory

5. Pokud používáte Application Insights, můžete použít také rozhraní API Application Insights a ID aplikace shromažďovat metriky na základě služby Application Insights. Další informace najdete v tématu [získání klíče rozhraní API a ID aplikace](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Vyberte **Uložit**, a Grafany testovat přihlašovací údaje pro každé rozhraní API. Zobrazí se zpráva podobná následující.  
    ![Konfigurace schválení zdroje dat služby Grafana](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Vytvoření řídicího panelu služby Grafana

1. Přejděte na Grafana domovskou stránku a vybrat **nový řídicí panel**.

2. Na nový řídicí panel, vyberte **grafu**. Můžete vyzkoušet další možnosti vytváření grafů, ale tento článek používá *grafu* jako příklad.

3. Prázdný graf se zobrazí na řídicím panelu. Klikněte na název panelu a vyberte **upravit** a zadejte podrobnosti data chcete zobrazit v tomto grafu grafu.
    ![Nový graf Grafana](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Vyberte zdroj dat monitorování Azure, který jste nakonfigurovali.
    * Shromažďování monitorování Azure vyberte metriky – **Azure Monitor** v rozevírací nabídce služby. Seznam zobrazuje selektory, kde můžete vybrat prostředky a metriku k monitorování v tomto grafu. Chcete-li shromažďovat metriky z virtuálního počítače, použijte obor názvů **Microsoft.Compute/VirtualMachines**. Jakmile vyberete virtuální počítače a metrik, můžete začít zobrazení jejich dat na řídicím panelu.
    ![Grafana grafu konfigurace pro monitorování Azure](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
    * Azure Log Analytics pro shromažďování dat – vyberte **Azure Log Analytics** v rozevírací nabídce služby. Vyberte pracovní prostor, který chcete vyhledat a nastavit text dotazu. Můžete zkopírovat sem jakýkoli dotaz Log Analytics, už máte nebo vytvořte novou. Při zadávání dotazu, technologie IntelliSense se zobrazí a navrhují možnosti automatického dokončování. Vyberte typ vizualizace **časové řady** **tabulky**, a spusťte dotaz.
    
    > [!NOTE]
    >
    > Výchozí dotaz, opatřeného modul plug-in používá dvě makra: "__interval $__timeFilter() a $. 
    > Tato makra umožňují Grafana dynamicky vypočítat časový rozsah a časový interval, při přiblížení na část grafu. Můžete odebrat tato makra a použití filtru (běžný čas), jako například *TimeGenerated > ago(1h)*, ale je, že prostředky grafu by podporuje přiblížení ve funkci.
    
    ![Grafana config grafů pro Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Tady je jednoduchý řídicí panel se dva grafy. Na levé straně znázorňuje procentuální využití procesoru dva virtuální počítače. Graf na pravé straně zobrazuje transakce v účtu služby Azure Storage rozdělené podle typu transakcí API.
    ![Příklad Grafana dva grafy](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Volitelné: Monitorovat vlastní metriky na stejném serveru služby Grafana

Můžete také nainstalovat Telegraf a InfluxDB ke shromáždění a vykreslování vlastního i agenta na základě metrik stejnou instanci Grafana. Existuje mnoho moduly plug-in data zdroje, které vám umožní spojit tyto metriky v řídicím panelu.

Můžete také znovu použít toto nastavení chcete zahrnout metriky z Prometheus serveru. Použijte modul plug-in Prometheus datový zdroj v galerii Grafana modul plug-in.

Tady je dobrá reference články týkající se použití Telegraf, InfluxDB, Prometheus a Dockeru
 - [Jak monitorovat systémové metriky se zásobníkem značek na Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Monitorujte metriky prostředků Dockeru s Grafana InfluxDB a Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Řešení monitorování pro hostitele Docker, kontejnery a kontejnerizovaných služeb](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Tady je obrázek úplné řídicím panelem Grafana, který má metriky z Azure monitoru a Application Insights.
![Příklad Grafana metriky](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Pokročilé funkce Grafana

### <a name="variables"></a>Proměnné
Některé hodnoty dotazu můžete vybrat pomocí rozevíracích seznamech uživatelského rozhraní a aktualizovat v dotazu. Zvažte například následující dotaz:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Můžete nakonfigurovat proměnnou, která se zobrazí seznam všech dostupných **řešení** hodnoty a pak aktualizujte dotaz a použijte ji.
Chcete-li vytvořit nové proměnné, klikněte na tlačítko Nastavení řídicího panelu v horní pravé části, vyberte **proměnné**a potom **nový**.
Na stránce proměnné definujte zdroje dat a dotazu ke spuštění, pokud chcete získat seznam hodnot.
![Proměnné konfigurace Grafana](./media/grafana-plugin/grafana-configure-variable-dark.png)

Po vytvoření upravit dotaz, který bude použit vybrané hodnoty a bude odpovídajícím způsobem reagovat grafy:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Proměnné pro použití Grafana](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Vytvoření řídicího panelu seznamy stop

Jednou z mnoho užitečných funkcí Grafana je řídicí panel seznamu testů. Můžete vytvořit více řídicích panelů a přidat je do seznamu stop Konfigurace intervalu pro každý řídicí panel k zobrazení. Vyberte **Přehrát** zobrazíte řídicí panely cyklicky procházet. Můžete zobrazit na velké wall monitorování, které poskytují panelu stavu pro vaši skupinu.

![Příklad seznamu stop Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste instalační program Grafana prostředí v Azure se účtují, když virtuální počítače jsou spuštěné, jestli je používáte nebo ne. Vyhnout účtovat další poplatky, odstraňte skupinu prostředků v rámci tohoto článku vytvořili.

1. V nabídce vlevo na webu Azure Portal klikněte na **skupiny prostředků** a potom klikněte na tlačítko **Grafana**.
2. Na stránce skupiny prostředků, klikněte na tlačítko **odstranit**, typ **Grafana** textového pole a pak klikněte na **odstranit**.

## <a name="next-steps"></a>Další postup
* [Přehled metrik Azure monitoru](../../azure-monitor/platform/data-collection.md)

