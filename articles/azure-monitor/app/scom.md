---
title: Integrací scom – pomocí nástroje Application Insights | Dokumentace Microsoftu
description: Pokud patříte mezi uživatele SCOM, monitorujte výkon a diagnostikovat problémy s Application Insights. Komplexní řídicí panely, inteligentní výstrahy, výkonné diagnostické nástroje a dotazy analýz.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: a08a67a78362325e29b1002b44f390a94e78888a
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028268"
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Sledování výkonu aplikací pomocí Application Insights pro SCOM
Pokud ke správě serverů používáte System Center Operations Manager (SCOM), můžete monitorovat výkon a diagnostikovat problémy s výkonem pomocí [Azure Application Insights](../../azure-monitor/app/asp-net.md). Application Insights monitoruje webové aplikace příchozí požadavky, odchozí REST a volání, výjimkami a trasami protokolu SQL. Poskytuje řídicí panely s grafy metrik a inteligentní výstrah, jakož i výkonné diagnostické vyhledávání a analytických dotazů nad tuto telemetrii. 

Můžete přepnout na monitorování pomocí Application Insights s použitím sada SCOM management pack.

> [!IMPORTANT]
> Tato System Center Operations Manager Management Pack je nyní **zastaralé**. Nepodporuje nejnovější sady SDK služby Application Insights a už se nedoporučuje.

## <a name="before-you-start"></a>Než začnete
Předpokládáme:

* Seznamte se s SCOM, a použít ve verzi SCOM 2012 R2 nebo 2016 ke správě vaší služby IIS webových serverů.
* Jste již nainstalovali na vašich serverech webovou aplikaci, kterou chcete monitorovat pomocí Application Insights.
* Verze rozhraní framework aplikace je rozhraní .NET 4.5 nebo novější.
* Máte přístup k předplatnému v [Microsoft Azure](https://azure.com) a přihlásit [webu Azure portal](https://portal.azure.com). Vaše organizace může mít předplatné a do něj může přidat svůj účet Microsoft.

(Může vytvořit vývojovému týmu [Application Insights SDK](../../azure-monitor/app/asp-net.md) do webové aplikace. Tento čas sestavení instrumentace je poskytuje větší flexibilitu při psaní vlastní telemetrii. Nicméně, nezáleží: provedením kroků popsaných v tomto poli s nebo bez SDK integrovanou.)

## <a name="one-time-install-application-insights-management-pack"></a>(Jednou) Nainstalujte sadu management pack služby Application Insights
Na počítači, kde je spuštěn nástroj Operations Manager:

1. Odinstalujte všechny starší verze sady management pack:
   1. V nástroji Operations Manager otevřete správu sad Management Pack. 
   2. Odstraňte staré verze.
2. Stáhněte a nainstalujte sadu management pack z katalogu.
3. Restartujte nástroj Operations Manager.

## <a name="create-a-management-pack"></a>Vytvoření sady management pack
1. V nástroji Operations Manager otevřete **Authoring**, **.NET... pomocí Application Insights**, **Průvodce přidáním monitorování**a znovu zvolte **.NET... s aplikací Insights**.
   
    ![](./media/scom/020.png)
2. Název konfigurace po vaší aplikaci. (Budete muset instrumentovat aplikaci jeden po druhém.)
   
    ![](./media/scom/030.png)
3. Na stejné stránce průvodce vytvořte novou sadu management pack, nebo vyberte balíček, který jste vytvořili dříve pro službu Application Insights.
   
     (Application Insights [sady management pack](https://technet.microsoft.com/library/cc974491.aspx) je šablona, ze kterého můžete vytvořit instanci. Můžete opakovaně použít stejnou instanci později.)

    ![Na kartě Obecné vlastnosti zadejte název aplikace. Klikněte na nový a zadejte název pro sadu management pack. Klikněte na tlačítko OK a pak klikněte na tlačítko Další.](./media/scom/040.png)

1. Zvolte jednu aplikaci, která chcete monitorovat. Funkce hledání vyhledává mezi aplikace nainstalované na serverech.
   
    ![Na kartě položky k monitorování klikněte na tlačítko Přidat, zadejte část názvu aplikace, klikněte na tlačítko hledání, zvolte aplikaci a pak přidat OK.](./media/scom/050.png)
   
    Volitelné pole Obor sledování lze zadat podmnožinu serverů, pokud nechcete monitorovat aplikace ve všech serverech.
2. Na další stránku průvodce musíte nejdřív zadat svoje přihlašovací údaje pro přihlášení k Microsoft Azure.
   
    Na této stránce vyberte prostředek Application Insights, kde chcete telemetrických dat, které se analyzují a zobrazují. 
   
   * Pokud aplikace byla nakonfigurována pro službu Application Insights během vývoje, vyberte jeho existující prostředek.
   * V opačném případě vytvořte nový prostředek s názvem aplikace. Pokud existují další aplikace, které jsou součástí stejného systému, vytvořte z nich ve stejné skupině prostředků, chcete-li zajistit přístup k telemetrii usnadňují správu.
     
     Toto nastavení lze později změnit.
     
     ![Na kartě Nastavení Application Insights klikněte na tlačítko "přihlásit" a zadejte přihlašovací údaje účtu Microsoft Azure. Zvolte předplatné, skupinu prostředků a prostředků.](./media/scom/060.png)
3. Dokončete průvodce.
   
    ![Kliknutí na Vytvořit](./media/scom/070.png)

Tento postup opakujte pro každou aplikaci, kterou chcete monitorovat.

Pokud potřebujete změnit nastavení později, otevřete znovu vlastnosti monitorování z okna pro vytváření obsahu.

![Ve vytváření obsahu, vyberte monitorování výkonu aplikací .NET pomocí nástroje Application Insights, vyberte monitor a klikněte na možnost Vlastnosti.](./media/scom/080.png)

## <a name="verify-monitoring"></a>Ověření monitorování
Monitorování, že jste nainstalovali vyhledávání pro vaši aplikaci na každém serveru. Pokud zjistí, aplikaci a nakonfiguruje monitorování stavu Application Insights pro monitorování aplikace. V případě potřeby nainstaluje nejprve monitorování stavu na serveru.

Můžete ověřit, která instance aplikace bylo zjištěno:

![V monitorování, otevřete službu Application Insights](./media/scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Zobrazení telemetrie v Application Insights
V [webu Azure portal](https://portal.azure.com), přejděte k prostředku pro vaši aplikaci. Můžete [zobrazili grafy znázorňující telemetrie](../../azure-monitor/app/app-insights-dashboards.md) z vaší aplikace. (Pokud ho ještě zobrazeny na hlavní stránce ještě, klikněte na Live Metrics Stream.)

## <a name="next-steps"></a>Další postup
* [Nastavit řídicí panel](../../azure-monitor/app/app-insights-dashboards.md) spojit nejdůležitější grafy monitorování tato a další aplikace.
* [Další informace o metrikách](../../azure-monitor/app/metrics-explorer.md)
* [Nastavení výstrah](../../azure-monitor/app/alerts.md)
* [Diagnostika problémů s výkonem](../../azure-monitor/app/detect-triage-diagnose.md)
* [Výkonné analytické dotazy](../../azure-monitor/app/analytics.md)
* [Testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md)

