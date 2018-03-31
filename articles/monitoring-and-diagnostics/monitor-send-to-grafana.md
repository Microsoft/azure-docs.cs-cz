---
title: Monitorování služby Azure a aplikace, které používají Grafana | Microsoft Docs
description: Monitorování Azure trasy a Application Insights data tak lze zobrazit v Grafana.
services: monitoring-and-diagnostics
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: 537760554baa542d4cd967d2e1e885f936303175
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="monitor-your-azure-services-in-grafana"></a>Sledování služeb Azure v Grafana
Nyní také můžete monitorovat Azure služeb a aplikací z [Grafana](https://grafana.com/) pomocí [modul plug-in zdroje dat monitorování Azure](https://grafana.com/plugins/grafana-azure-monitor-datasource). Tento modul plug-in shromažďuje údaje o výkonu aplikací získané nástrojem Application Insights SDK, jakož i infrastruktury data poskytnutá Azure monitorování. Pak můžete zobrazit tato data na řídicím panelu Grafana.

Tento modul plug-in je aktuálně ve verzi preview.

Pomocí následujících kroků nastavit server Grafana z Azure Marketplace a vytvořit řídicí panely pro metriky z monitorování Azure a Application Insights.

## <a name="set-up-a-grafana-instance"></a>Nastavit Grafana instance
1. Přejděte na Azure Marketplace a vyberte Grafana podle Grafana laboratoře.

2. Zadejte názvy a podrobnosti. Vytvořte novou skupinu prostředků. Udržování přehledu o hodnoty, které jste vybrali pro virtuální počítač uživatelské jméno, heslo virtuálních počítačů a Grafana heslo správce serveru.  

3. Zvolte velikost virtuálního počítače a účet úložiště.

4. Nakonfigurujte nastavení konfigurace sítě.

5. Zobrazit souhrn a vybrat **vytvořit** po vyjádření souhlasu s podmínkami použití.

## <a name="log-in-to-grafana"></a>Přihlaste se k Grafana
1. Po dokončení nasazení vyberte **přejděte do skupiny prostředků**. Zobrazí seznam nově vytvořené prostředky.

    ![Grafana objektů skupiny prostředků](.\media\monitor-how-to-grafana\grafana1.png)

    Pokud vyberete skupinu zabezpečení sítě (*grafana nsg* v tomto případě), zobrazí se, že se pro přístup k serveru Grafana používá port 3000.

2. Přejděte zpět do seznamu prostředků, a vyberte **veřejnou IP adresu**. Na hodnoty zjištěné v tomto okně zadáním *http://<IP address>: 3000* nebo  *<DNSName>: 3000* v prohlížeči. Měli byste vidět přihlašovací stránku pro Grafana serveru, který jste právě vytvořili.

    ![Grafana přihlašovací obrazovku](.\media\monitor-how-to-grafana\grafana2.png)

3. Přihlaste se pomocí uživatelské jméno jako *správce* a heslo správce serveru Grafana jste vytvořili dříve.

## <a name="configure-data-source-plugin"></a>Nakonfigurovat modul plug-in zdroj dat

Po úspěšném přihlášení, měli byste vidět, že modul plug-in zdroj dat monitorování Azure je už součástí sady.

![Grafana ukazuje modul plug-in Azure monitorování](.\media\monitor-how-to-grafana\grafana3.png)

1. Vyberte **zdroj dat přidat** ke konfiguraci Azure monitorování a Application Insights.

2. Vyberte název zdroje dat a vyberte **Azure monitorování** jako zdroj dat z rozevíracího seznamu.


## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Grafana používá objektu služby Azure Active Directory pro připojení k rozhraní API Správce Azure monitorování a shromažďování dat metriky. Služby musíte vytvořit hlavní můžete spravovat přístup k prostředkům Azure.

1. V tématu [tyto pokyny](../azure-resource-manager/resource-group-create-service-principal-portal.md) k vytvoření objektu služby. Zkopírujte a uložte vašeho klienta ID, ID klienta a tajný klíč klienta.

2. V tématu [přiřazení aplikace k roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) přiřadit role Čtenář do aplikace Azure Active Directory.     

3. Pokud používáte Application Insights, můžete použít také Application Insights API a ID aplikace ke shromažďování metrik Application Insights na základě. Další informace najdete v tématu [získávání klíč rozhraní API a ID aplikace](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. Po zadání všech těchto informací o vyberte **Uložit** a Grafana testy rozhraní API. Měli byste vidět zprávu podobné následujícím.  

    ![Grafana ukazuje modul plug-in Azure monitorování](.\media\monitor-how-to-grafana\grafana4-1.png)

> [!NOTE]
> Při konfiguraci můžete určit, které cloudové Azure (veřejný, Azure US Government, Azure v Německu nebo Azure China) chcete modul plug-in nakonfigurovat proti.
>
>

## <a name="build-a-grafana-dashboard"></a>Řídicí panel Grafana sestavení

1. Přejděte na domovskou stránku a vyberte možnost **novým řídicím panelem**.

2. V nové řídicí panel, vyberte **grafu**. Můžete použít jiné možnosti vytváření grafů, ale tento článek používá *grafu* jako příklad.

    ![Grafana nový řídicí panel](.\media\monitor-how-to-grafana\grafana5.png)

3. Prázdné grafu se zobrazí na řídicím panelu.

4. Klikněte na název panelu a vyberte **upravit** zadat podrobnosti dat, kterou chcete zobrazit v tomto grafu grafu.

5. Jakmile jste vybrali správné virtuální počítače, můžete začít zobrazení metriky v řídicím panelu.

Toto je jednoduchý řídicí panel se dvou grafech. Byl na levé straně ukazuje procento procesoru dva virtuální počítače. Graf na pravé straně zobrazuje transakce v účtu Azure Storage členěné podle typu rozhraní API transakce.

![Příklad Grafana dva grafy](.\media\monitor-how-to-grafana\grafana6.png)


## <a name="optional-create-dashboard-playlists"></a>Volitelné: Vytváření seznamů skladeb velice řídicí panel

Jedním z řady užitečných funkcí Grafana je seznam stop řídicího panelu. Můžete vytvořit více řídicí panely a přidat je do seznamu stop Konfigurace intervalu pro každý řídicí panel k zobrazení. Vyberte **přehrání** zobrazíte cyklicky řídicí panely. Můžete zobrazit na velké wall monitorování, které poskytují "stav panelu" pro vaší skupinu.

![Příklad seznamu stop Grafana](.\media\monitor-how-to-grafana\grafana7.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Volitelné: Monitorujte vlastní metriky na stejném serveru Grafana

Můžete také nainstalovat Telegraf a InfluxDB ke sběru a vykreslení vlastní i založené na agentovi metriky ve stejné instanci Grafana. Existuje mnoho modulů plug-in data zdroje, které můžete použít sloučit tyto metriky v řídicím panelu.

Můžete také opětovně použít nastavit tak, aby zahrnují metriky ze serveru Prometheus. Použijte modul plug-in zdroj dat Prometheus v galerii modulů plug-in na Grafana.

Tady jsou dobrou odkaz články o tom, jak použít Telegraf, InfluxDB, Prometheus a Docker
 - [Postup sledování systému metriky se zásobníkem značek na Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Sledování prostředků metriky Docker s Grafana, InfluxDB a Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Řešení monitorování pro hostitele, kontejnery a kontejnerizované služby Docker](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Zde je obrázek úplné Grafana řídicí panel, který má metriky z monitorování Azure a Application Insights.
![Příklad Grafana metriky](.\media\monitor-how-to-grafana\grafana8.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Budou se vám účtovat, když virtuální počítače jsou spuštěné, zda jsou jejich používání, nebo ne. Účtovány další poplatky, vyčistěte skupiny prostředků vytvořené v tomto článku.

1. Z nabídky na levé straně na portálu Azure, klikněte na tlačítko **skupiny prostředků** a pak klikněte na **Grafana**.
2. Na stránce skupiny prostředků, klikněte na tlačítko **odstranit**, typ **Grafana** textového pole a pak klikněte na **odstranit**.

## <a name="next-steps"></a>Další postup
* [Přehled Azure sledování metrik](monitoring-overview-metrics.md)
