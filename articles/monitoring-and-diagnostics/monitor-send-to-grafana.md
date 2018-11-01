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
ms.component: ''
ms.openlocfilehash: 75b1edf80f1dad5f0db48c11329effe080760820
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413141"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorování služeb Azure v Grafana
Teď můžete také monitorovat služby a aplikace z Azure [Grafana](https://grafana.com/) pomocí [zdroje dat monitorování Azure plugin](https://grafana.com/plugins/grafana-azure-monitor-datasource). Modul plug-in shromažďuje údaje o výkonu aplikace shromážděných službou Application Insights SDK, jakož i data infrastruktury poskytovaný Azure Monitor. Tato data pak můžete zobrazit na řídicím panelu Grafana.

Modul plug-in je aktuálně ve verzi preview.

Následujícím postupem nastavit server Grafany z Azure Marketplace a sestavovat řídicí panely pro metriky z Azure monitoru a Application Insights.

## <a name="set-up-a-grafana-instance"></a>Nastavit instanci služby Grafana
1. Přejít na Azure Marketplace a vyberte Grafana pomocí služby Grafana Labs.

2. Zadejte názvy a podrobnosti. Vytvořte novou skupinu prostředků. Udržovat přehled o hodnoty, které jste vybrali pro uživatelské jméno virtuálního počítače, heslo virtuálního počítače a služby Grafana heslo správce serveru.  

3. Zvolte velikost virtuálního počítače a účtu úložiště.

4. Konfigurace nastavení konfigurace sítě.

5. Zobrazit souhrn a vyberte **vytvořit** po přijetí podmínek použití.

## <a name="log-in-to-grafana"></a>Přihlaste se do Grafany
1. Po dokončení nasazení vyberte **přejít ke skupině prostředků**. Zobrazí seznam nově vytvořené prostředky.

    ![Grafana objektů skupiny prostředků](media/monitor-send-to-grafana/grafana1.png)

    Pokud vyberete skupinu zabezpečení sítě (*grafana-nsg* v tomto případě), uvidíte, že je pro přístup k serveru Grafana používá port 3000.

2. Vraťte se do seznamu prostředků a vyberte **veřejnou IP adresu**. Použitím hodnot na této obrazovce, zadejte *http://<IP address>: 3000* nebo  *<DNSName>: 3000* v prohlížeči. Měli byste vidět přihlašovací stránku pro server Grafana, kterou jste právě vytvořili.

    ![Grafana přihlašovací obrazovky](media/monitor-send-to-grafana/grafana2.png)

3. Přihlaste se pomocí uživatelské jméno jako *správce* a heslo správce serveru Grafana jste vytvořili dříve.

## <a name="configure-data-source-plugin"></a>Konfigurace modulu plug-in zdroje dat

Po úspěšném přihlášení, měli byste vidět, že modul plug-in Azure Monitor datový zdroj je již součástí.

![Grafana ukazuje modul plug-in Azure Monitor](media/monitor-send-to-grafana/grafana3.png)

1. Vyberte **přidat zdroj dat** ke konfiguraci Azure Monitor a Application Insights.

2. Vyberte název zdroje dat a vyberte **Azure Monitor** jako zdroj dat z rozevíracího seznamu.


## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Grafana instanční objekt Azure Active Directory používá pro připojení k rozhraní API služby Azure Monitor a shromažďovat data metrik. Můžete třeba vytvořit instanční objekt můžete spravovat přístup k prostředkům Azure.

1. Zobrazit [tyto pokyny](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření instančního objektu. Zkopírujte a uložte vašeho tenanta, ID, ID klienta a tajný kód klienta.

2. Zobrazit [přiřazení aplikace k roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) přiřazení k aplikaci Azure Active Directory roli Čtenář.     

3. Pokud používáte Application Insights, můžete použít také rozhraní API Application Insights a ID aplikace shromažďovat metriky na základě služby Application Insights. Další informace najdete v tématu [získání klíče rozhraní API a ID aplikace](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. Až zadáte všechny tyto informace, vyberte **Uložit** a Grafany testy rozhraní API. Zobrazí se zpráva podobná následující.  

    ![Grafana ukazuje modul plug-in Azure Monitor](media/monitor-send-to-grafana/grafana4-1.png)

> [!NOTE]
> Při konfiguraci modulu plug-in můžete určit, který Cloud Azure (veřejný, Azure US Government, Azure Germany nebo Azure China) chcete modul plug-in nakonfigurovat proti.
>
>

## <a name="build-a-grafana-dashboard"></a>Vytvoření řídicího panelu služby Grafana

1. Přejít na domovskou stránku a vybrat **nový řídicí panel**.

2. Na nový řídicí panel, vyberte **grafu**. Můžete vyzkoušet další možnosti vytváření grafů, ale tento článek používá *grafu* jako příklad.

    ![Nový řídicí panel služby Grafana](media/monitor-send-to-grafana/grafana5.png)

3. Prázdný graf se zobrazí na řídicím panelu.

4. Klikněte na název panelu a vyberte **upravit** a zadejte podrobnosti data chcete zobrazit v tomto grafu grafu.

5. Až zvolíte správné virtuální počítače, můžete začít zobrazení metriky na řídicím panelu.

Tady je jednoduchý řídicí panel se dva grafy. Na levé straně znázorňuje procentuální využití procesoru dva virtuální počítače. Graf na pravé straně zobrazuje transakce v účtu služby Azure Storage rozdělené podle typu transakcí API.

![Příklad Grafana dva grafy](media/monitor-send-to-grafana/grafana6.png)


## <a name="optional-create-dashboard-playlists"></a>Volitelné: Vytvoření řídicího panelu seznamy stop

Jednou z mnoho užitečných funkcí Grafana je řídicí panel seznamu testů. Můžete vytvořit více řídicích panelů a přidat je do seznamu stop Konfigurace intervalu pro každý řídicí panel k zobrazení. Vyberte **Přehrát** zobrazíte řídicí panely cyklicky procházet. Můžete zobrazit na velké wall monitorování, které poskytují "stav panelu" pro vaši skupinu.

![Příklad seznamu stop Grafana](media/monitor-send-to-grafana/grafana7.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Volitelné: Sledování vaší vlastní metriky na stejném serveru služby Grafana

Můžete také nainstalovat Telegraf a InfluxDB ke shromáždění a vykreslování vlastního i agenta na základě metrik ve stejné instanci služby Grafana. Existuje mnoho moduly plug-in data zdroje, které vám umožní spojit tyto metriky v řídicím panelu.

Můžete také znovu použít toto nastavení chcete zahrnout metriky z Prometheus serveru. Použijte modul plug-in Prometheus datový zdroj v galerii Grafana modul plug-in.

Tady je dobrá reference články týkající se použití Telegraf, InfluxDB, Prometheus a Dockeru
 - [Jak monitorovat systémové metriky se zásobníkem značek na Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Monitorujte metriky prostředků Dockeru s Grafana InfluxDB a Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Řešení monitorování pro hostitele Docker, kontejnery a kontejnerizovaných služeb](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Tady je obrázek úplné řídicím panelem Grafana, který má metriky z Azure monitoru a Application Insights.
![Příklad Grafana metriky](media/monitor-send-to-grafana/grafana8.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Bude se vám účtovat, když virtuální počítače jsou spuštěné, jestli je používáte nebo ne. Vyhnout účtovat další poplatky, odstraňte skupinu prostředků v rámci tohoto článku vytvořili.

1. V nabídce vlevo na webu Azure Portal klikněte na **skupiny prostředků** a potom klikněte na tlačítko **Grafana**.
2. Na stránce skupiny prostředků, klikněte na tlačítko **odstranit**, typ **Grafana** textového pole a pak klikněte na **odstranit**.

## <a name="next-steps"></a>Další postup
* [Přehled metrik Azure monitoru](monitoring-overview-metrics.md)
