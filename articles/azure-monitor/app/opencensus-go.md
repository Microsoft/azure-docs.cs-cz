---
title: OpenCensus přejít ke sledování pomocí Azure Application Insights | Microsoft Docs
description: Poskytuje pokyny pro integraci trasování OpenCensus na cestách s místním doposíláním a Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.openlocfilehash: ef67801fee574ae43d83af650d6b715e12426c7e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432545"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Shromažďovat distribuované trasování z webu přejít (Preview)

Application Insights teď podporuje distribuované trasování aplikací v cestách prostřednictvím integrace s [OpenCensus](https://opencensus.io) a naší novou [místní službou pro předávání](./opencensus-local-forwarder.md). Tento článek vás seznámí s postupem nastavení OpenCensus pro přechod a získávání dat trasování pro Application Insights.

## <a name="prerequisites"></a>Požadavky

- Mít předplatné Azure.
- Chcete-li nainstalovat nástroj, použijte verzi 1,11 [ke stažení](https://golang.org/dl/).
- Postupujte podle pokynů k instalaci [místního serveru pro přeposílání jako služby systému Windows](./opencensus-local-forwarder.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Vytvořit prostředek Application Insights

Nejdřív je potřeba vytvořit prostředek Application Insights, který vygeneruje klíč instrumentace (ikey). Ikey se pak použije ke konfiguraci místního předávacího serveru pro odesílání distribuovaných trasování z vaší OpenCensus instrumentované aplikace, aby bylo možné Application Insights.   

1. Vyberte **vytvořit prostředek** > **vývojářské nástroje** > **Application Insights**.

   ![Přidání prostředku Application Insights](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >Pokud vytvoříte prostředek Application Insightse poprvé, můžete se dozvědět víc v článku [vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

   | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název identifikující aplikaci, kterou monitorujete |
   | **Skupina prostředků**     | myResourceGroup      | Název pro novou skupinu prostředků, která bude hostovat data App Insights |
   | **Umístění** | Východní USA | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

2. Klikněte na **Vytvořit**.

## <a name="configure-local-forwarder"></a>Konfigurace místního serveru pro směrování

1. Vyberte **Přehled** > **Základy** a zkopírujte **instrumentační klíč** vaší aplikace.

   ![Snímek obrazovky klíč instrumentace](./media/opencensus-Go/0003-instrumentation-key.png)

2. Upravte soubor `LocalForwarder.config` a přidejte svůj klíč instrumentace. Pokud jste postupovali podle pokynů v části [požadavky](./opencensus-local-forwarder.md) , je soubor umístěný na adrese `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Restartujte službu pro zakládání **místní** aplikace.

## <a name="opencensus-go-packages"></a>Balíčky OpenCensus přejít

1. Nainstalujte otevřené balíčky sčítání pro přechod z příkazového řádku:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Přidejte následující kód do souboru. přejít a pak Sestavte a spusťte. (Tento příklad je odvozen od oficiálních pokynů pro OpenCensus s přidaným kódem, který usnadňuje integraci s místním přesměrováním).

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Po spuštění aplikace Simple směřuje přejděte na `http://localhost:50030`. Každá aktualizace prohlížeče vygeneruje text "Hello World", spolu s odpovídajícími daty, která jsou vybrána místním serverem pro přeposílání.

4. Chcete-li potvrdit, že **místní předávací server** vybírá trasování, zkontrolujte soubor `LocalForwarder.config`. Pokud jste postupovali podle kroků v [předpokladech](https://docs.microsoft.com/azure/application-insights/local-forwarder), bude se nacházet v `C:\LF-WindowsServiceHost`.

    Na obrázku níže v souboru protokolu vidíte, že před spuštěním druhého skriptu, kde jsme přidali exportéra `OpenCensus input BatchesReceived`, byl 0. Po spuštění aktualizovaného skriptu `BatchesReceived` zvýšený o počet zadaných hodnot:
    
    ![Formulář Nový prostředek App Insights](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Nyní můžete znovu otevřít stránku **přehled** Application Insights v Azure Portal a zobrazit podrobnosti o aktuálně spuštěné aplikaci. Vyberte **živý stream metriky**.

   ![Snímek obrazovky s aktivním datovým proudem metriky vybraným v červeném poli](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Pokud znovu spustíte aplikaci s druhým přechodem a začnete aktualizovat prohlížeč pro `http://localhost:50030`, zobrazí se živá data trasování, která se dostanou do Application Insights z místního služby pro doručování.

   ![Snímek obrazovky živého streamu metrik se zobrazenými daty o výkonu](./media/opencensus-go/0006-stream.png)

3. Přejděte zpět na stránku **Přehled** a vyberte **Mapa aplikace** pro vizuální rozložení vztahů závislosti a časování volání mezi komponentami vaší aplikace.

    ![Snímek obrazovky se základní mapou aplikace](./media/opencensus-go/0007-application-map.png)

    Vzhledem k tomu, že jsme provedli pouze trasování jednoho volání metody, není naše mapa aplikace zajímavá. Mapa aplikace se ale může škálovat a vizualizovat mnohem více distribuovaných aplikací:

   ![Mapa aplikace](media/opencensus-go/application-map.png)

4. Vyberte možnost **prozkoumat výkon** a proveďte podrobnou analýzu výkonu a určete původní příčinu pomalého výkonu.

    ![Snímek obrazovky s podoknem výkonu](./media/opencensus-go/0008-performance.png)

5. Když vyberete **ukázky** a pak kliknete na kteroukoli z ukázek, které se zobrazí v pravém podokně, spustí se prostředí s podrobnostmi o koncových transakcích. I když naše ukázková aplikace zobrazí jenom jednu událost, složitější aplikace vám umožní prozkoumat koncovou transakci dolů na úroveň zásobníku volání jednotlivých událostí.

     ![Snímek obrazovky s koncovým rozhraním transakce](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>OpenCensus trasování pro přejít

Pokryli jsme základní informace o integraci OpenCensus for a k místnímu serveru pro posílání a Application Insights. Oficiální témata týkající se [používání OpenCensus najdete](https://godoc.org/go.opencensus.io) v tématu popisujícím pokročilé postupy.

## <a name="next-steps"></a>Další kroky

* [Mapa aplikace](./../../azure-monitor/app/app-map.md)
* [Monitorování výkonu na konci](./../../azure-monitor/learn/tutorial-performance.md)
