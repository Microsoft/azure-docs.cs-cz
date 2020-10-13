---
title: Monitorování metrik a nezpracovaných protokolů pro Azure CDN od Microsoftu
description: Tento článek popisuje Azure CDN z metrik a nezpracovaných protokolů Microsoft Monitoring.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/25/2020
ms.author: allensu
ms.openlocfilehash: c41bf8bc6e5aa3749786bc1189343dfdebdc1508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321136"
---
# <a name="monitoring-metrics-and-raw-logs-for-azure-cdn-from-microsoft"></a>Monitorování metrik a nezpracovaných protokolů pro Azure CDN od Microsoftu
Pomocí Azure CDN od Microsoftu můžete sledovat prostředky následujícími způsoby, které vám pomůžou při odstraňování problémů, sledování a ladění problémů. 

* Nezpracované protokoly poskytují bohatou informaci o všech požadavcích, které CDN přijímá. Nezpracované protokoly se liší od protokolů aktivit. Protokoly aktivit poskytují přehled o operacích provedených v prostředcích Azure.
* Metriky, které zobrazují čtyři klíčové metriky v síti CDN, včetně poměru přístupů do bajtů, počtu požadavků, velikosti odpovědí a celkové latence. Poskytuje také různé dimenze pro rozdělení metrik.
* Výstraha, která zákazníkům umožňuje nastavit výstrahu pro klíčové metriky
* Další metriky, které zákazníkům umožňují použít Azure Log Analytics k povolení dalších metriky hodnoty. Poskytujeme také ukázky dotazů pro několik dalších metrik v rámci Azure Log Analytics.

> [!IMPORTANT]
> Pro Azure CDN od Microsoftu je k dispozici funkce HTTP holých protokolů.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Konfigurace – Azure Portal

Konfigurace nezpracovaných protokolů pro Azure CDN z profilu Microsoftu: 

1. V nabídce Azure Portal vyberte **všechny prostředky**  >>  **\<your-CDN-profile>** .

2. V části **monitorování**vyberte **nastavení diagnostiky**.

3. Vyberte **+ Přidat nastavení diagnostiky**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::
    
    > [!IMPORTANT]
    > Nezpracované protokoly jsou k dispozici pouze na úrovni profilu, přestože jsou k dispozici agregované protokoly stavového kódu HTTP na úrovni koncového bodu.

4. V části **nastavení diagnostiky**zadejte název nastavení diagnostiky v části **název nastavení diagnostiky**.

5. Vyberte **AzureCdnAccessLog** a nastavte dobu uchování ve dnech.

6. Vyberte **Podrobnosti o cíli**. Možnosti cíle jsou:
    * **Odeslání do Log Analytics**
        * Vyberte **předplatné** a **Log Analytics pracovní prostor**.
    * **Archivace do účtu úložiště**
        * Vyberte **předplatné** a **účet úložiště**.
    * **Streamování do centra událostí**
        * Vyberte **předplatné**, **obor názvů centra událostí**, **název centra událostí (nepovinné)** a **název zásady centra událostí**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::

7. Vyberte **Uložit**.

## <a name="configuration---azure-powershell"></a>Konfigurace – Azure PowerShell

Pro konfiguraci nastavení diagnostiky nezpracovaných protokolů použijte [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) .

Data uchovávání jsou definována parametrem **-RetentionInDays** v příkazu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Povolení diagnostických protokolů v účtu úložiště

1. Přihlaste se k Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Pokud chcete povolit diagnostické protokoly v účtu úložiště, zadejte tyto příkazy. Nahraďte proměnné hodnotami:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Povolit protokoly diagnostiky pro Log Analytics pracovní prostor

1. Přihlaste se k Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Pokud chcete povolit protokoly diagnostiky pro Log Analytics pracovní prostor, zadejte tyto příkazy. Nahraďte proměnné hodnotami:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Povolit diagnostické protokoly pro obor názvů centra událostí

1. Přihlaste se k Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Pokud chcete povolit diagnostické protokoly pro obor názvů centra událostí, zadejte tyto příkazy. Nahraďte proměnné hodnotami:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Nezpracované vlastnosti protokolů

Azure CDN ze služby společnosti Microsoft aktuálně poskytuje nezpracované protokoly. Nezpracované protokoly obsahují jednotlivé požadavky na rozhraní API s každou položkou, která má následující schéma: 

| Vlastnost              | Popis                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Jedinečný referenční řetězec, který identifikuje požadavek, který je obsluhován předními dvířky, je také odeslán jako hlavička X-Azure-ref na klienta. Vyžaduje se pro hledání podrobností v protokolech přístupu pro konkrétní požadavek. |
| HttpMethod            | Metoda HTTP, kterou požadavek používá                                                                                                                                                                     |
| HttpVersion           | Typ požadavku nebo připojení                                                                                                                                                                   |
| RequestUri            | Identifikátor URI přijatého požadavku                                                                                                                                                                         |
| RequestBytes          | Velikost zprávy požadavku HTTP v bajtech, včetně hlaviček požadavků a textu žádosti.                                                                                                   |
| ResponseBytes         | Bajty odeslané back-end serverem jako odpověď                                                                                                                                                    |
| UserAgent             | Typ prohlížeče, který klient použil.                                                                                                                                                               |
| ClientIp              | IP adresa klienta, který odeslal požadavek.                                                                                                                                                  |
| TimeTaken             | Doba, kterou trvala akce, v milisekundách.                                                                                                                                            |
| Tato SecurityProtocol      | Verze protokolu TLS/SSL používaná požadavkem nebo hodnotou null, pokud není šifrování.                                                                                                                           |
| Koncový bod              | Hostitel koncového bodu CDN je nakonfigurovaný v nadřazeném profilu CDN.                                                                                                                                   |
| Název hostitele back-endu     | Název hostitele back-endu nebo původ, ve kterém se požadavky odesílají.                                                                                                                                |
| Odesláno do počátečního štítku </br> (nepoužívané) * **Viz Poznámka k vyřazení níže.** | Pokud má hodnotu true, znamená to, že žádost byla zodpovězena z počáteční mezipaměti ochrany před hraničním rozhraním pop. Počáteční ochrana je nadřazená mezipaměť, která se používá ke zvýšení poměru přístupů do mezipaměti.                                       |
| isReceivedFromClient | Pokud má hodnotu true, znamená to, že požadavek pochází z klienta. Pokud je hodnota false, je požadavek na hranu (podřízený bod POP) neúspěšný a reaguje na počátek ochrany (nadřazený bod POP). 
| HttpStatusCode        | Stavový kód HTTP vrácený z proxy serveru.                                                                                                                                                        |
| HttpStatusDetails     | Výsledný stav žádosti. Význam této řetězcové hodnoty lze nalézt v tabulce odkazů na stav.                                                                                              |
| Výstrah                   | Bod POP hraničního zobrazení, který odpověděl na žádost uživatele Zkratky pro body POP jsou kódy letišť příslušných METROS.                                                                                   |
| Stav mezipaměti          | Označuje, zda byl objekt vrácen z mezipaměti nebo pochází od počátku.                                                                                                             |
> [!NOTE]
> Protokoly lze zobrazit v rámci profilu Log Analytics spuštěním dotazu. Vzorový dotaz by vypadal takto:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Bylo odesláno do původního základu ochrany.
Nezpracovaná vlastnost protokolu **isSentToOriginShield** byla zastaralá a nahrazena novým polem **isReceivedFromClient**. Pokud už používáte zastaralé pole, použijte nové pole. 

Nezpracované protokoly obsahují protokoly vygenerované z hraniční sítě CDN (podřízeného bodu POP) i z počátečního štítku. Počátek štítku odkazuje na nadřazené uzly, které jsou strategicky umístěné na celém světě. Tyto uzly komunikují se zdrojovými servery a omezují zatížení provozu na počátku. 

Pro každý požadavek, který směřuje ke zdroji štítku, existují 2 – položky protokolu:

* Jeden pro hraniční uzly
* Jednu pro zdroj štítku. 

Chcete-li odlišit výstupy nebo odpovědi z hraničních uzlů vs. od počátku, můžete k získání správných dat použít pole **isReceivedFromClient** . 

Pokud je hodnota false, znamená to, že požadavek reaguje od počátku ochrany do hraničních uzlů. Tento přístup je platný pro porovnání nezpracovaných protokolů s fakturačními daty. Neúčtují se poplatky za výstup od počátku ochrany od počátku do hraničních uzlů. Poplatky za výstup z hraničních uzlů do klientů se účtují. 

**Ukázka dotazu Kusto pro vyloučení protokolů vygenerovaných z počátečního štítku v Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> Funkce HTTP RAW logs je k dispozici automaticky pro všechny profily vytvořené nebo aktualizované po **25. únoru 2020**. Pro profily CDN, které jste vytvořili dřív, by se měl po nastavení protokolování aktualizovat koncový bod CDN. Například jedna může přejít na geografická filtrování v rámci koncových bodů CDN a blokovat každou zemi nebo oblast, která není relevantní pro své úlohy, a pak klikněte na Uložit.


## <a name="metrics"></a>Metriky
Azure CDN od Microsoftu je integrovaná do Azure Monitor a zveřejňuje čtyři metriky CDN, které vám pomůžou sledovat problémy a řešit problémy s nimi a ladit je. 

Metriky se zobrazují v grafech a jsou přístupné prostřednictvím PowerShellu, CLI a rozhraní API. Metriky CDN jsou bezplatné.

Azure CDN z měření od Microsoftu a odesílají své metriky v intervalech 60 – sekund. Zobrazení metrik na portálu může trvat až 3 minuty. 

Další informace najdete v tématu [Azure monitor metriky](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

**Metriky podporované Azure CDN od Microsoftu**

| Metriky         | Description                                                                                                      | Rozměr                                                                                   |
|-----------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| Poměr přístupů do bajtů * | Procento výstupu z mezipaměti CDN vypočítané na celkový výstup.                                      | Koncový bod                                                                                    |
| RequestCount    | Počet požadavků klientů poskytovaných službou CDN.                                                                     | Koncový bod </br> Země klienta. </br> Oblast klienta. </br> Stav HTTP. </br> Stavová skupina HTTP. |
| ResponseSize    | Počet bajtů odeslaných jako odpověď z hraniční sítě CDN klientům.                                                  |Koncový bod </br> Země klienta. </br> Oblast klienta. </br> Stav HTTP. </br> Stavová skupina HTTP.                                                                                          |
| TotalLatency    | Celková doba od žádosti klienta přijatá službou CDN, **dokud se nepošle poslední bajt odpovědi z CDN klientovi**. |Koncový bod </br> Země klienta. </br> Oblast klienta. </br> Stav HTTP. </br> Stavová skupina HTTP.                                                                                             |

***Počet úspěšných bajtů v dávce = (výstup z Edge-výstup od počátku)/Egress z Edge**

Počet vyloučených scénářů v bajtech poměru přístupů:

* Explicitně neprovedete konfiguraci žádné mezipaměti ani pomocí stroje pravidel nebo chování mezipaměti řetězců dotazů.
* Direktivu řízení mezipaměti je explicitně konfigurována bez uložení nebo privátní mezipaměti.

### <a name="metrics-configuration"></a>Konfigurace metrik

1. V nabídce Azure Portal vyberte **všechny prostředky**  >>  **\<your-CDN-profile>** .

2. V části **monitorování**vyberte **metriky**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::

3. Vyberte **Přidat metriku**a vyberte metriku, kterou chcete přidat:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::

4. Vyberte **Přidat filtr** a přidejte filtr:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::

5. Pokud chcete zobrazit trend podle různých dimenzí, vyberte **použít** rozdělení:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::

6. Vyberte **Nový graf** a přidejte nový graf:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::

### <a name="alerts"></a>Výstrahy

Můžete nastavit výstrahy pro Microsoft CDN výběrem **Monitoring**  >>  **výstrahy**monitorování.

Vyberte **nové pravidlo výstrahy** pro metriky uvedené v části metriky:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::

Výstraha se bude účtovat na základě Azure Monitor. Další informace o výstrahách naleznete v tématu [Azure monitor Alerts](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

### <a name="additional-metrics"></a>Další metriky
Další metriky můžete povolit pomocí Azure Log Analytics a nezpracovaných protokolů za další náklady.

1. Postupujte podle výše uvedených kroků v možnosti povolení diagnostiky pro odeslání nezpracovaného protokolu do Log Analytics.

2. Vyberte pracovní prostor Log Analytics, který jste vytvořili:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::   

3. V pracovním prostoru Log Analytics vyberte **protokoly** **obecně** .  Pak vyberte **Začínáme**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::   
 
4. Vyberte **profily CDN**.  Vyberte ukázkový dotaz, který se má spustit, nebo zavřete ukázkovou obrazovku a zadejte vlastní dotaz:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true":::   

4. Pokud chcete zobrazit data podle grafu, vyberte **graf**.  Vyberte **Připnout na řídicí panel** , abyste mohli graf připnout na řídicí panel Azure:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Přidejte nastavení diagnostiky pro profil CDN." border="true"::: 

## <a name="next-steps"></a>Další kroky
V tomto článku jste povolili protokoly HTTP Raw pro službu Microsoft CDN.

Další informace o Azure CDN a dalších službách Azure, které jsou uvedené v tomto článku, najdete v tématech:

* [Analyzovat](cdn-log-analysis.md) Azure CDN způsoby použití.

* Přečtěte si další informace o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Nakonfigurujte [Log Analytics v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
