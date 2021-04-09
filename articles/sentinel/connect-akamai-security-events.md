---
title: Připojte kolektor událostí zabezpečení Akamai ke službě Azure Sentinel | Microsoft Docs
description: Naučte se, jak pomocí konektoru událostí zabezpečení Akamai vyžádat protokoly zabezpečení Akamai Solutions do Azure Sentinel. Zobrazení dat Akamai v sešitech, vytváření výstrah a zlepšení šetření.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 8aa5a52a06713b4f00b43205a57148049a8ef8da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711956"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Připojte kolektor událostí zabezpečení Akamai k Sentinel Azure.

> [!IMPORTANT]
> Konektor událostí zabezpečení Akamai je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit kolektor událostí zabezpečení Akamai ke službě Azure Sentinel. Datový konektor událostí zabezpečení Akamai vám umožňuje snadno připojit protokoly Akamai ke službě Azure Sentinel, abyste mohli zobrazit data v sešitech, dotazovat se na ně a vytvořit vlastní výstrahy a začlenit je ke zlepšení šetření. Integrace mezi kolektorem událostí zabezpečení Akamai a službou Azure Sentinel využívá CEF syslog, server pro překládání protokolů a agenta Log Analytics. Používá také uživatelsky sestavený analyzátor protokolů založený na funkci Kusto.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů. [Přečtěte si další informace o klíčích pracovních prostorů](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Posílání protokolů událostí zabezpečení Akamai do Azure Sentinel

Pokud se chcete přihlásit do Azure Sentinel, nakonfigurujte kolektor událostí zabezpečení Akamai tak, aby odesílal zprávy syslog ve formátu CEF na server pro předávání protokolů založený na systému Linux (se systémem rsyslog nebo syslog-ng). Na tomto serveru bude nainstalován agent Log Analytics a Agent přepošle protokoly do vašeho pracovního prostoru služby Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **Akamai události zabezpečení (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na kartě **pokyny** v části **Konfigurace**:

    1. V části **1. Konfigurace agenta syslog pro Linux** – tento krok proveďte, pokud ještě nemáte spuštěný Server pro překládání protokolů, nebo pokud ho potřebujete. Viz [Krok 1: nasazení služby pro](connect-cef-agent.md) překládání protokolů v dokumentaci k ověřovacím protokolům Azure, kde najdete informace o velikosti, podrobnějších pokynech a podrobném vysvětlení.

    1. V části **2. Protokol CEF (Common Event Format) do agenta SYSLOG** – podle pokynů Akamai [NAKONFIGURUJTE integraci Siem](https://developer.akamai.com/tools/integrations/siem) a [nastavte konektor CEF](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). Tento konektor přijímá události zabezpečení z řešení Akamai prakticky v reálném čase pomocí rozhraní API OPEN SIEM a převádí je z formátu JSON do formátu CEF.
    
        Tato konfigurace by měla obsahovat následující prvky:
    
        - Cíl protokolu – název hostitele nebo IP adresa serveru pro předávání protokolů
        - Protokol a port – TCP 514 (Pokud je to možné jinak, nezapomeňte provést paralelní změnu v procesu démona syslog na serveru pro předávání protokolů)
        - Formát protokolu – CEF
        - Typy protokolů – všechny dostupné

    1. V části **3. Ověřit připojení** – ověřte příjem dat zkopírováním příkazu na stránce konektoru a jeho spuštěním na serveru pro přeposílání protokolů. Podrobnější pokyny a vysvětlení najdete v části [Krok 3: ověření připojení](connect-cef-verify.md) v dokumentaci k ověřovacím službám Azure.

        Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **Azure Sentinel** v tabulce *CommonSecurityLog* .

Tento datový konektor závisí na analyzátoru založeném na funkci Kusto, která bude fungovat podle očekávání. Pomocí následujících kroků nastavte funkci **AkamaiSIEMEvent** Kusto pro použití v dotazech a sešitech.

1. V nabídce navigace v Azure Sentinel vyberte **protokoly**.

1. Zkopírujte následující dotaz a vložte ho do okna dotazu.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Klikněte na rozevírací seznam **Uložit** a pak klikněte na **Uložit**. V panelu **uložení**

    1. Do **pole název** zadejte **AkamaiSIEMEvent**.

    1. V části **Uložit jako** vyberte **funkce**.

    1. V části **alias funkce** zadejte **AkamaiSIEMEvent**.

    1. V části **kategorie** zadejte **Functions**.

    1. Klikněte na **Uložit**.

    Aktivace aplikací funkcí obvykle trvá od 10 do 15 minut.

Nyní jste připraveni k dotazování na data Akamai zadáním `AkamaiSIEMEvent` do horního řádku okna dotazu.

Další ukázky dotazů najdete na kartě **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit události zabezpečení Akamai ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.