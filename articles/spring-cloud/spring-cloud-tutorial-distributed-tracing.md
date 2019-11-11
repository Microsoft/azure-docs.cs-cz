---
title: 'Kurz: použití distribuovaného trasování u jarního cloudu Azure | Microsoft Docs'
description: Naučte se používat distribuované trasování jarního cloudu prostřednictvím Azure Application Insights
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: a9d2100103cdd5858d0d58cf6ef77a6ccac3745f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607552"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Kurz: použití distribuovaného trasování u jarního cloudu Azure

Nástroje pro distribuované trasování jarního cloudu umožňují snadné ladění a monitorování složitých problémů. Jarní cloud Azure integruje [jarní Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) s využitím Azure [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) , aby poskytoval výkonné možnosti distribuovaného trasování z Azure Portal.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Povolit distribuované trasování v Azure Portal
> * Přidání jarního cloudu Sleuth do vaší aplikace
> * Zobrazení map závislostí pro vaše aplikace mikroslužeb
> * Hledání dat trasování pomocí různých filtrů

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu je potřeba:

* Již zřízené a běžící cloudová služba Azure.  Po dokončení [tohoto rychlého](spring-cloud-quickstart-launch-app-cli.md) startu můžete zřídit a spustit cloudovou službu Azure na jaře.
    
## <a name="add-dependencies"></a>Přidat závislosti

Povolte odesílateli zipkin odeslání na web přidáním následujícího řádku do souboru Application. Properties:

```xml
spring.zipkin.sender.type = web
```

Další krok můžete přeskočit, pokud jste postupovali [podle našeho průvodce a připravujete aplikaci Azure jaře Cloud](spring-cloud-tutorial-prepare-app-deployment.md). V opačném případě přejdete do svého místního vývojového prostředí a upravíte soubor `pom.xml` tak, aby zahrnoval Sleuth závislost pružinového cloudu:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-sleuth</artifactId>
            <version>${spring-cloud-sleuth.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-sleuth</artifactId>
    </dependency>
</dependencies>
```

* Sestavte a nasaďte znovu pro službu jarní cloudovou službu Azure, aby odrážela tyto změny. 

## <a name="modify-the-sample-rate"></a>Úprava vzorkovací frekvence
Můžete změnit rychlost shromažďování telemetrie úpravou vzorkovací frekvence. Například pokud chcete vzorkovat polovinu jako často, přejděte do souboru `application.properties` a změňte následující řádek:

```xml
spring.sleuth.sampler.probability=0.5
```

Pokud již máte vytvořenou a nasazenou aplikaci, můžete upravit vzorkovací frekvenci přidáním výše uvedeného řádku jako proměnné prostředí v Azure CLI nebo na portálu. 

## <a name="enable-application-insights"></a>Povolení Application Insights

1. V Azure Portal přejdete na stránku služby jarní cloud Azure.
1. V části monitorování vyberte **distribuované trasování**.
1. Vyberte **Upravit nastavení** a upravte nebo přidejte nové nastavení.
1. Vytvořte nový dotaz Application Insight nebo můžete vybrat existující.
1. Zvolte, kterou kategorii protokolování chcete monitorovat, a zadejte dobu uchování (ve dnech).
1. Pokud chcete použít nové trasování, vyberte **použít** .

## <a name="view-application-map"></a>Zobrazit mapu aplikace

Vraťte se na stránku distribuované trasování a vyberte **Zobrazit mapu aplikací**. Zkontrolujte vizuální znázornění nastavení aplikace a monitorování. Informace o použití mapy aplikace najdete v [tomto článku](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="search"></a>Hledání

Pomocí vyhledávací funkce můžete zadat dotaz na jiné konkrétní položky telemetrie. Na stránce **distribuované trasování** vyberte **Hledat**. Další informace o tom, jak používat funkci hledání, najdete v [tomto článku](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="application-insights-page"></a>Stránka Application Insights

Application Insights poskytuje kromě mapy a hledání aplikací i možnosti monitorování. Vyhledejte Azure Portal pro název vaší aplikace a potom spusťte Application Insights stránku, kde najdete další informace. Další informace o tom, jak tyto nástroje používat, najdete [v dokumentaci](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).


## <a name="disable-application-insights"></a>Zakázat Application Insights

1. V Azure Portal přejdete na stránku služby jarní cloud Azure.
1. V části monitorování klikněte na **distribuované trasování**.
1. Kliknutím na **Zakázat** zakažte Application Insights

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak povolit a pochopit distribuované trasování v rámci jarního cloudu Azure. Pokud se chcete dozvědět, jak navázat aplikaci na Azure CosmosDB, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Naučte se navazovat aplikace na Azure CosmosDB](spring-cloud-tutorial-bind-cosmos.md).
