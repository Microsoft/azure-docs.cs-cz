---
title: Akcelerátory řešení IoT referenční architektuře – Azure | Dokumentace Microsoftu
description: Další informace o intranetu referenční akcelerátory řešení Azure IoT. Existující akcelerátory řešení využívat tuto referenční architekturu. Referenční architektury můžete použít také při vytváření vlastních řešení IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: ba5eb50dcf800c186124db348ac584ff6f55cebb
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467311"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Úvod do referenční architektura Azure IoT

Tento článek představuje [referenční architektura Azure IoT](https://aka.ms/iotrefarchitecture) a příkladů, jak [akcelerátory řešení Azure IoT](about-iot-accelerators.md) postupujte podle doporučení.

Open source [vzdálené monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md) a [připojená továrna](iot-accelerators-connected-factory-sample-walkthrough.md) akcelerátory řešení podle doporučení referenční architektury. Akcelerátory řešení můžete použít jako výchozí bod pro řešení IoT nebo jako výukové nástroje.

## <a name="overview"></a>Přehled

Referenční architektura popisuje elementy řešení IoT, jako je například technologických principů, složení služby Azure IoT a zařízení. Tato architektura také poskytuje doporučení o implementaci technologií.

Na nejvyšší úrovni se zobrazí jako se skládá z řešení IoT:

* Věci, které generování a odesílání telemetrických dat, jako jsou měření a události. V akcelerátoru řešení vzdáleného monitorování zařízení, jako jsou trucks nebo výtahy jsou možnosti, které se odesílají telemetrická data.
* Přehledy vytvořené z telemetrická data odesílaná z akcí. V akcelerátoru řešení vzdáleného monitorování generuje pravidlo přehledů. Pravidla můžete například určit teploty v modul dosáhne prahové hodnoty.
* Akce na základě v přehledy, které pomáhají zlepšit podnikový proces nebo proces. V akcelerátoru řešení vzdáleného monitorování může upozornit e-mailové akce operátor o potenciální problém s motoru.

[Referenční architektura Azure IoT](https://aka.ms/iotrefarchitecture) je dokument živých aktualizace, jak se vyvíjí technologie.

## <a name="core-subsystems"></a>Základní subsystémů

Referenční architektura identifikuje subsystémy jader je vidět na následujícím obrázku:

![Základní subsystémů](media/iot-accelerators-architecture-overview/CoreSubsystems.png)

Následující části popisují, jak komponenty akcelerátor řešení vzdálené monitorování mapují na základní subsystémů.

### <a name="iot-devices"></a>Zařízení IoT

Řešení IoT by měl povolit zabezpečených, efektivních a robustní komunikaci mezi téměř jakýkoli druh zařízení a cloudové brány. Zařízení jsou podnikových prostředků, které v rozsahu od jednoduché teploty senzorů do komplexní objekt pro vytváření výrobní linky se stovkami komponenty a senzorů.

Brána pole nebo hraniční zařízení, je specializované zařízení zařízení nebo software pro obecné účely, který může fungovat jako odpověď:

* Aktivátor komunikace pro zpracování protokol převodu.
* Systém správy místních zařízení a zařízení zpracování telemetrie centra. Hraniční zařízení můžete bez komunikace se službami pro cloudové zpracování telemetrických dat místně pro řízení zařízení. Hraniční zařízení můžete také filtrovat nebo telemetrických dat agregované zařízení snížit objem telemetrických dat do cloudu přenáší.

Zařízení v řešení vzdáleného monitorování připojení do služby IoT hub a odesílat telemetrická data pro zpracování. Řešení vzdáleného monitorování umožňuje také operátory spravovat zařízení pomocí úlohy nebo Správa automatické zařízení. Sady SDK pro zařízení Azure IoT můžete použít k implementaci vašeho zařízení.

Řešení vzdáleného monitorování můžete nasadit a spravovat zařízení IoT Edge. Zpracování na hraničních zařízeních pomáhá snížit objem telemetrická data odesílaná do cloudu a zrychlit odezvu na události zařízení.

### <a name="cloud-gateway"></a>Cloudová brána

Cloudová brána umožňuje komunikaci do a ze zařízení a hraničními zařízeními. Tato zařízení může být v mnoha vzdálenými lokalitami.

Gateway spravuje komunikaci zařízení, včetně připojení, správy, ochranu cesty komunikace, ověřování a autorizace. Brána také vynucuje připojení a propustnosti kvóty a shromažďuje telemetrická data používá k fakturaci, Diagnostika a jiných úloh monitorování.

Řešení vzdáleného monitorování nasadí poskytnout zabezpečený koncový bod pro zařízení, aby odesílala telemetrická data do služby IoT hub. Centrum IoT také:

* Obsahuje úložiště identit zařízení ke správě zařízení povolené připojení k řešení.
* Povolí řešení k odesílání příkazů do zařízení. Chcete-li například otevřete ventil pro uvolnění tlaku.
* Umožňuje Hromadná Správa zařízení. Chcete-li například upgrade firmwaru na sadu zařízení.

### <a name="stream-processing"></a>Zpracování streamů

Jako řešení ingestuje telemetrická data, je důležité pochopit, jak Tok zpracování telemetrických dat se může lišit. V závislosti na scénáři záznamů dat probíhá přes různé fáze:

* Úložiště, jako jsou trvalé archivy, mezipaměti v paměti a dočasný fronty.

* Analýzu, spustit vstupní telemetrická data prostřednictvím sady podmínek a můžete vytvořit jiný výstupní datových záznamů. Vstupní telemetrie kódována Avro může například vrátit telemetrie výstup kódovaný ve formátu JSON.

* Původní vstupní telemetrická data a analýzy výstup záznamy jsou obvykle uložená a k dispozici pro zobrazení. Telemetrie vstupní a výstupní záznamy můžou také aktivovat akce, jako je e-mailů, incidentu lístků a příkazy zařízení.

Směrování můžete odesílat telemetrii na jeden nebo více koncových bodů úložiště, procesy analýzy a akce. Řešení může kombinovat fází v různém pořadí a jejich zpracování s souběžných paralelními úlohami.

Řešení vzdáleného monitorování využívá [Azure Stream Analytics](/azure/stream-analytics/) ke zpracování datových proudů. Stroj pravidel v řešení dotazů Stream Analytics používá k vygenerování výstrahy a akce. Například můžete použít řešení dotazu k identifikaci průměrná teplota v prostoru úložiště truck více než pět minut klesne pod 36 stupňů.

### <a name="storage"></a>Storage

Řešení IoT může generovat velké objemy dat, což se často stává dat časových řad. Tato data musí být uložena, kde je možné pro vizualizaci a vytváření sestav. Řešení také potřebovat přístup k datům později pro analýzu nebo další zpracování. Je běžné mít data rozdělit do data warm i cold úložišť. Horké úložiště obsahuje nejnovější data pro přístup s nízkou latencí. Úložiště pro studená data obvykle ukládá historická data.

Řešení vzdáleného monitorování využívá [Azure Time Series Insights](/azure/time-series-insights/) jako jeho warm data úložiště a Cosmos DB jako své úložiště pro studená data.

### <a name="ui-and-reporting-tools"></a>Uživatelské rozhraní a nástroje pro generování sestav

Řešení může poskytnout uživatelského rozhraní:

* Přístup k a vizualizace dat a analýza výsledků zařízení.
* Zjišťování zařízení prostřednictvím registru.
* Příkazy a ovládání zařízení
* Pracovní postupy zřizování zařízení.
* Výstrahy a oznámení.
* Integrace s živý, interaktivní řídicí panely a zobrazení dat z velkého počtu zařízení.  
* S ohledem na geografickou služby a geografického umístění.

Řešení vzdáleného monitorování obsahuje webové uživatelské rozhraní, aby poskytování těchto funkcí. Webové uživatelské rozhraní zahrnuje:

* Interaktivní mapu k zobrazení umístění zařízení.
* Přístup do Průzkumníka služby Time Series Insights pro dotazy a analýzu telemetrie.

### <a name="business-integration"></a>Obchodní integrace

Integrace vrstvy obchodní zpracovává integrace řešení IoT s podnikovým systémům, jako je například CRM, ERP a -obchodní aplikace. Mezi příklady patří service fakturace, podpory zákazníků, a zadat náhradní díly.

Řešení vzdáleného monitorování používá k odesílání e-mailů, pokud je splněna podmínka pravidla. Například řešení může upozornit operátora teploty v nákladní vozidlo klesne pod 36 stupňů.

## <a name="next-steps"></a>Další postup

V tomto článku se dozvěděli o referenční architektura Azure IoT a viděli některé příklady jak akcelerátor řešení vzdálené monitorování následuje svá doporučení. Dalším krokem je ke čtení [referenční architektura IoT v Microsoft Azure](https://aka.ms/iotrefarchitecture).