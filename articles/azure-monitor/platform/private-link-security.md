---
title: Použití Azure Private Linku k bezpečnému propojení sítí k Azure Monitoru
description: Použití Azure Private Linku k bezpečnému propojení sítí k Azure Monitoru
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: a7464216649d6b482893693a1f182af5cf6e77ac
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508969"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Použití Azure Private Linku k bezpečnému propojení sítí k Azure Monitoru

[Privátní odkaz Azure](../../private-link/private-link-overview.md) umožňuje bezpečně propojit služby Azure PaaS s vaší virtuální sítí pomocí privátních koncových bodů. U mnoha služeb stačí nastavit koncový bod na prostředek. Azure Monitor je ale Constellation různých vzájemně propojených služeb, které vzájemně spolupracují za účelem monitorování vašich úloh. V důsledku toho jsme vytvořili prostředek s názvem Azure Monitor obor privátního propojení (AMPLS). AMPLS umožňuje definovat hranice sítě pro monitorování a připojit se k virtuální síti. Tento článek popisuje, kdy použít a jak nastavit obor privátního odkazu Azure Monitor.

## <a name="advantages"></a>Výhody

Pomocí privátního odkazu můžete:

- Připojte soukromě k Azure Monitor bez nutnosti otevírat přístup k veřejné síti.
- Ujistěte se, že jsou k datům pro monitorování dostupná jenom prostřednictvím autorizovaných privátních sítí.
- Zabránění exfiltrace dat ze svých privátních sítí definováním konkrétních prostředků Azure Monitor, které se připojují prostřednictvím privátního koncového bodu
- Bezpečně připojte soukromou místní síť k Azure Monitor pomocí ExpressRoute a privátního odkazu.
- Zachovat veškerý provoz v síti Microsoft Azure páteřní sítě

Další informace najdete v tématu  [klíčové výhody privátního propojení](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Jak to funguje

Azure Monitor obor privátních odkazů (AMPLS) spojuje soukromé koncové body (a virtuální sítě, které jsou obsaženy v) do jednoho nebo více prostředků Azure Monitor Log Analytics pracovní prostory a Application Insights komponenty.

![Diagram základní topologie prostředků](./media/private-link-security/private-link-basic-topology.png)

> [!NOTE]
> Jeden prostředek Azure Monitor může patřit do více AMPLSs, ale nemůžete připojit jednu virtuální síť k více než jednomu AMPLS. 

### <a name="the-issue-of-dns-overrides"></a>Problém přepsání DNS
Log Analytics a Application Insights použít pro některé ze svých služeb globální koncové body, což znamená, že obsluhuje požadavky zaměřené na libovolný pracovní prostor nebo komponentu. Application Insights například používá globální koncový bod pro ingestování protokolů a oba Application Insights i Log Analytics používají globální koncový bod pro požadavky na dotazy.

Když nastavíte připojení k privátnímu propojení, vaše služba DNS se aktualizuje, aby mapovala Azure Monitor koncové body na privátní IP adresy z rozsahu IP adres vaší virtuální sítě. Tato změna přepíše všechna předchozí mapování těchto koncových bodů, což může mít smysluplné důsledky, které jsou níže uvedené. 

## <a name="planning-based-on-your-network-topology"></a>Plánování na základě topologie vaší sítě

Před nastavením Azure Monitor nastavení privátního propojení zvažte svoji topologii sítě a specifickou topologii směrování DNS. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor privátní odkaz platí pro všechny prostředky Azure Monitor – to je vše nebo nic
Vzhledem k tomu, že některé koncové body Azure Monitor jsou globální, není možné vytvořit připojení privátního propojení pro konkrétní součást nebo pracovní prostor. Místo toho se při nastavení privátního odkazu na jednu komponentu Application Insights aktualizace záznamů DNS pro **všechny** Application Insights součásti. Jakýkoli pokus o ingestování nebo dotazování komponenty se pokusí přejít přes privátní odkaz a pravděpodobně selže. Podobně nastavení privátního odkazu na jeden pracovní prostor způsobí, že všechny Log Analytics dotazy procházejí koncovým bodem dotazu privátního propojení (ale ne žádosti ingestování, které mají koncové body specifické pro pracovní prostor).

![Diagram přepsání DNS v jedné virtuální síti](./media/private-link-security/dns-overrides-single-vnet.png)

To platí nejen pro konkrétní virtuální síť, ale pro všechny virtuální sítě, které sdílejí stejný server DNS (viz [problém přepsání DNS](#the-issue-of-dns-overrides)). Například požadavek na ingestování protokolů do jakékoli Application Insights komponenty se vždycky pošle prostřednictvím trasy privátního odkazu. Součásti, které nejsou propojené s AMPLS, selžou ověřování privátních odkazů a neprojde se.

**To znamená, že byste měli připojit všechny prostředky Azure Monitor ve vaší síti k privátnímu odkazu (přidat je do AMPLS) nebo žádný z nich.**

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor privátní odkaz platí pro celou síť.
Některé sítě se skládají z několika virtuální sítě. Pokud tato virtuální sítě používají stejný server DNS, přepíše mapování DNS mezi sebou a může také přerušit komunikaci mezi sebou pomocí Azure Monitor (podívejte [se na problémy s přepsáními DNS](#the-issue-of-dns-overrides)). V konečném případě bude moci komunikovat s Azure Monitor pouze poslední síť VNet, protože služba DNS namapuje Azure Monitor koncových bodů na privátní IP adresy z tohoto virtuální sítě rozsahu (které nemusí být dosažitelné z jiných virtuální sítě).

![Diagram přepsání DNS ve více virtuální sítě](./media/private-link-security/dns-overrides-multiple-vnets.png)

Ve výše uvedeném diagramu se virtuální síť 10.0.1. x nejdřív připojí k AMPLS1 a mapuje Azure Monitor globální koncové body na IP adresy z jejího rozsahu. Později se virtuální síť 10.0.2. x připojí k AMPLS2 a přepíše mapování DNS *stejných globálních koncových bodů* s IP adresami z rozsahu. Vzhledem k tomu, že tyto virtuální sítě nejsou partnerského vztahu, první virtuální síť teď nedokáže dosáhnout těchto koncových bodů.

**Virtuální sítě, které používají stejný server DNS, by měly být partnerského vztahu – buď přímo, nebo prostřednictvím virtuální sítě rozbočovače. Virtuální sítě, které nemají partnerský vztah, by měly také používat jiný server DNS, servery DNS pro přeposílání nebo jiné mechanismy, aby se předešlo kolizování DNS.**

### <a name="hub-spoke-networks"></a>Sítě s rozbočovačem a paprsky
Topologie centra s paprsky se můžou vyhnout problémům s přepsáními DNS nastavením privátního odkazu v centrální virtuální síti (hlavní), namísto nastavení privátního propojení pro každou virtuální síť zvlášť. Tato instalace dává smysl zvláště tehdy, když se sdílejí prostředky Azure Monitor používané paprskem virtuální sítě. 

![Střed a-paprsek – jednoduché – PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Můžete záměrně chtít vytvořit samostatné soukromé odkazy pro virtuální sítě paprsků, například umožníte každé virtuální síti přístup k omezené sadě prostředků monitorování. V takových případech můžete pro každou virtuální síť vytvořit vyhrazený soukromý koncový bod a AMPLS, ale musíte taky ověřit, jestli nesdílejí stejný server DNS, aby se předešlo přepsání DNS.


### <a name="consider-limits"></a>Zvážit omezení

Jak je uvedeno v [omezeních a omezeních](#restrictions-and-limitations), objekt AMPLS má několik omezení znázorněných v níže uvedené topologii:
* Každá virtuální síť se připojuje pouze k **1** AMPLS objektu.
* AMPLS B je připojen k soukromým koncovým bodům dvou virtuální sítě (VNet2 a síti vnet3) pomocí 2 z 10 možných připojení privátního koncového bodu.
* AMPLS se připojí ke dvěma pracovním prostorům a jedné součásti Application Insights pomocí 3 z 50 Azure Monitor dostupných připojení prostředků.
* Workspace2 se připojuje k AMPLS a a AMPLS B pomocí 2 z 5 možných AMPLS připojení.

![Diagram omezení AMPLS](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Příklad připojení

Začněte vytvořením prostředku oboru privátního propojení Azure Monitor.

1. V Azure Portal **vytvořte prostředek** a vyhledejte **Azure monitor oboru privátního odkazu**.

   ![Najít Azure Monitor oboru privátního odkazu](./media/private-link-security/ampls-find-1c.png)

2. Vyberte **Vytvořit**.
3. Vyberte předplatné a skupinu prostředků.
4. Zadejte název AMPLS. Je nejvhodnější použít smysluplný a jasný název, jako je například "AppServerProdTelem".
5. Vyberte **Zkontrolovat a vytvořit**. 

   ![Vytvořit Azure Monitor obor privátních odkazů](./media/private-link-security/ampls-create-1d.png)

6. Potvrďte ověření a pak vyberte **vytvořit**.

### <a name="connect-azure-monitor-resources"></a>Připojit Azure Monitor prostředky

Připojte Azure Monitor prostředky (pracovní prostory Log Analytics a Application Insights komponenty) k vašemu AMPLS.

1. V Azure Monitor oboru privátního propojení vyberte v nabídce vlevo možnost **Azure monitor prostředky** . Vyberte tlačítko **Přidat** .
2. Přidejte pracovní prostor nebo komponentu. Když vyberete tlačítko **Přidat** , zobrazí se dialogové okno, kde můžete vybrat Azure monitor prostředky. Můžete procházet předplatná a skupiny prostředků, nebo můžete zadat jejich jméno, abyste je mohli vyfiltrovat. Vyberte pracovní prostor nebo součást a přidejte je do svého oboru kliknutím na tlačítko **použít** .

    ![Snímek obrazovky s výběrem oboru uživatelského rozhraní](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Odstranění prostředků Azure Monitor vyžaduje, abyste je nejdřív odpojíte od všech objektů AMPLS, ke kterým jsou připojené. Prostředky připojené k AMPLS není možné odstranit.

### <a name="connect-to-a-private-endpoint"></a>Připojení k privátnímu koncovému bodu

Teď, když máte prostředky připojené k AMPLS, vytvořte privátní koncový bod pro připojení k naší síti. Tuto úlohu můžete provést v [Azure Portal privátním linkovém centru](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)nebo v Azure monitor oboru privátních odkazů, jak to udělalo v tomto příkladu.

1. V seznamu prostředek oboru vyberte **připojení privátního koncového bodu** v nabídce prostředky na levé straně. Vyberte **privátní koncový bod** a spusťte tak proces vytváření koncových bodů. Můžete také schválit připojení spuštěná v rámci centra privátních propojení tak, že je vyberete a vyberete **schválit**.

    ![Snímek obrazovky uživatelského rozhraní připojení privátního koncového bodu](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Vyberte předplatné, skupinu prostředků a název koncového bodu a oblast, ve které by měla být aktivní. Region musí být stejná oblast jako virtuální síť, ke které se připojíte.

3. Vyberte **Další: prostředek**. 

4. Na obrazovce prostředek

   a. Vyberte **předplatné** , které obsahuje prostředek privátního oboru Azure monitor. 

   b. Jako **typ prostředku** vyberte **Microsoft. Insights/privateLinkScopes**. 

   c. V rozevíracím seznamu **prostředek** vyberte obor privátního propojení, který jste vytvořili dříve. 

   d. Vyberte **Další: >konfigurace**.
      ![Snímek obrazovky výběru vytvoření privátního koncového bodu](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. V podokně Konfigurace

   a.    Vyberte **virtuální síť** a **podsíť** , ke kterým se chcete připojit, k prostředkům Azure monitor. 
 
   b.    Pro **integraci s privátní zónou DNS** vyberte **Ano** a umožněte tak, aby se automaticky vytvořila nová zóna privátní DNS. Skutečné zóny DNS se mohou lišit od toho, co se zobrazuje na snímku obrazovky níže. 
   > [!NOTE]
   > Pokud zvolíte **ne** a dáváte přednost ruční správě záznamů DNS, nejprve dokončete nastavení privátního koncového bodu a konfigurace AMPLS. Pak nakonfigurujte DNS podle pokynů v tématu [Konfigurace DNS privátního koncového bodu Azure](../../private-link/private-endpoint-dns.md). Při přípravě nastavení služby Private Link se ujistěte, že nevytváříte prázdné záznamy. Záznamy DNS, které vytvoříte, můžou přepsat stávající nastavení a ovlivnit vaše možnosti připojení ke službě Azure Monitor.
 
   c.    Vyberte **Zkontrolovat a vytvořit**.
 
   d.    Nechte ověření Pass. 
 
   e.    Vyberte **Vytvořit**. 

    ![Snímek obrazovky s výběrem vytvořit privátní pro endpoint2 u](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Nyní jste vytvořili nový privátní koncový bod, který je připojen k tomuto AMPLS.

## <a name="configure-log-analytics"></a>Konfigurace služby Log Analytics

Přejděte na Azure Portal. V nabídce prostředku Log Analytics pracovního prostoru existuje položka s názvem **izolace sítě** na levé straně. Z této nabídky můžete řídit dva různé stavy.

![Izolace sítě LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Připojené Azure Monitor obory privátního propojení
Na této obrazovce se zobrazí všechny rozsahy připojené k tomuto pracovnímu prostoru. Připojení k oborům (AMPLSs) umožňuje přístup k tomuto pracovnímu prostoru ze sítě z virtuální sítě připojené ke každému AMPLS. Vytvoření připojení prostřednictvím tohoto umístění má stejný účinek jako nastavení v oboru, stejně jako v souvislosti s [připojením Azure Monitorch prostředků](#connect-azure-monitor-resources). Chcete-li přidat nové připojení, vyberte možnost **Přidat** a vyberte Azure monitor obor privátních odkazů. Pokud ho chcete připojit, vyberte **použít** . Všimněte si, že se pracovní prostor může připojit k 5 AMPLS objektům, jak je uvedeno v [omezeních a omezeních](#restrictions-and-limitations). 

### <a name="access-from-outside-of-private-links-scopes"></a>Přístup mimo rozsahy privátních odkazů
Nastavení v dolní části této stránky řídí přístup z veřejných sítí, což znamená sítě nepřipojené přes výše uvedené obory. Nastavení **povoluje přístup k veřejné síti pro** ingestování **bez** blokování přijímání protokolů z počítačů mimo připojené obory. Nastavení **povoluje přístup k veřejné síti pro dotazy** **bez** dotazů na bloky přicházející z počítačů mimo rozsah. Který zahrnuje dotazy spouštěné prostřednictvím sešitů, řídicích panelů, prostředí klienta založeného na rozhraní API, přehledy Azure Portal a dalších možností. Prostředí spuštěné mimo Azure Portal a dotaz Log Analytics data musí být spuštěná také v rámci virtuální sítě s privátním propojením.

### <a name="exceptions"></a>Výjimky
Omezení přístupu, jak je vysvětleno výše, se nevztahuje na Azure Resource Manager a má proto tato omezení:
* Přístup k datům – při blokování nebo povolování dotazů z veřejných sítí se používá pro většinu Log Analyticsch prostředí, některá prostředí dotazují data prostřednictvím Azure Resource Manager a proto se nebudou moci dotazovat na data, pokud se u Správce prostředků nepoužijí nastavení privátního propojení (funkce už brzy). Patří mezi ně například Azure Monitor řešení, sešity a přehledy a konektor LogicApp.
* Správa pracovního prostoru – změny nastavení pracovního prostoru a konfigurace (včetně zapnutí nebo vypnutí těchto nastavení přístupu) se spravují pomocí Azure Resource Manager. Omezte přístup ke správě pracovních prostorů pomocí příslušných rolí, oprávnění, síťových ovládacích prvků a auditování. Další informace najdete v tématu [Azure monitor role, oprávnění a zabezpečení](roles-permissions-security.md).

> [!NOTE]
> Protokoly a metriky nahrané do pracovního prostoru prostřednictvím [nastavení diagnostiky](diagnostic-settings.md) přecházejí přes zabezpečený privátní kanál Microsoft a tato nastavení se nekontrolují.

### <a name="log-analytics-solution-packs-download"></a>Stažení balíčků řešení Log Analytics

Pokud chcete, aby mohl agent Log Analytics stahovat balíčky řešení, přidejte příslušné plně kvalifikované názvy domény do seznamu povolených bran firewall. 


| Cloudové prostředí | Prostředek agenta | Porty | Směr |
|:--|:--|:--|:--|
|Veřejný partnerský vztah Azure     | scadvisorcontent.blob.core.windows.net         | 443 | Odchozí
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Odchozí
|Azure (Čína) 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Odchozí

## <a name="configure-application-insights"></a>Konfigurace Application Insights

Přejděte na Azure Portal. V prostředku součásti Azure Monitor Application Insights je na levé straně položka nabídky **izolace sítě** . Z této nabídky můžete řídit dva různé stavy.

![Izolace sítě AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Nejdřív můžete připojit tento prostředek Application Insights, abyste Azure Monitor obory privátních odkazů, ke kterým máte přístup. Vyberte **Přidat** a vyberte **Azure monitor obor privátních odkazů**. Pokud ho chcete připojit, vyberte použít. Na této obrazovce se zobrazí všechny připojené obory. Díky tomu může toto připojení umožňovat přístup k této komponentě v síťovém provozu propojených virtuálních sítí a má stejný účinek jako připojení k oboru, protože jsme provedli [připojení Azure Monitorch prostředků](#connect-azure-monitor-resources). 

Za druhé můžete řídit, jak se tento prostředek dá oslovit mimo rozsahy privátních vazeb, které jsou uvedené dřív. Pokud nastavíte možnost **povolí přístup k veřejné síti pro** ingestování na **ne**, počítače nebo sady SDK mimo připojené obory nemůžou do této součásti nahrávat data. Pokud nastavíte možnost **povolí přístup k veřejné síti pro dotazy** na **ne**, počítače mimo rozsah nebudou mít přístup k datům v tomto prostředku Application Insights. Tato data zahrnují přístup k protokolům APM, metrikám a živému streamu metrik a také k prostředí postaveným nahoře, jako jsou sešity, řídicí panely, prostředí klientů založené na rozhraní API, přehledy v Azure Portal a další. 

Všimněte si, že prostředí pro využívání mimo portál musí také běžet v rámci veřejné virtuální sítě, která obsahuje monitorované úlohy. 

K privátnímu odkazu budete muset přidat prostředky hostující monitorovaná zatížení. Zde najdete [dokumentaci](../../app-service/networking/private-endpoint.md) , jak to udělat App Services.

Přístup tímto způsobem se omezuje jenom na data v prostředku Application Insights. Změny konfigurace, včetně zapnutí nebo vypnutí těchto nastavení přístupu, se spravují pomocí Azure Resource Manager. Místo toho omezte přístup k Správce prostředků pomocí příslušných rolí, oprávnění, síťových ovládacích prvků a auditování. Další informace najdete v tématu [Azure monitor role, oprávnění a zabezpečení](roles-permissions-security.md).

> [!NOTE]
> Aby bylo možné plně zabezpečit Application Insights na základě pracovních prostorů, musíte uzamknout přístup k prostředkům Application Insights a také k příslušnému pracovnímu prostoru Log Analytics.
>
> Diagnostika na úrovni kódu (Profiler/ladicí program) vyžaduje poskytnutí vlastního účtu úložiště pro podporu privátního odkazu. Zde najdete [dokumentaci](../app/profiler-bring-your-own-storage.md) , jak to provést.

## <a name="use-apis-and-command-line"></a>Použití rozhraní API a příkazového řádku

Proces popsaný výše můžete automatizovat pomocí Azure Resource Manager šablon, REST a rozhraní příkazového řádku.

Pokud chcete vytvořit a spravovat obory privátních odkazů, použijte [REST API](/rest/api/monitor/private%20link%20scopes%20(preview)) nebo [Azure CLI (AZ monitor Private-Link-Scope)](/cli/azure/monitor/private-link-scope).

Ke správě přístupu k síti použijte příznaky `[--ingestion-access {Disabled, Enabled}]` a `[--query-access {Disabled, Enabled}]` v [Log Analytics pracovní prostory](/cli/azure/monitor/log-analytics/workspace) nebo [Application Insights součásti](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Shromažďování vlastních protokolů a privátního propojení protokolem IIS

Účty úložiště se používají v procesu přijímání vlastních protokolů. Ve výchozím nastavení se používají účty úložiště spravované službou. Pokud ale chcete ingestovat vlastní protokoly na soukromých odkazech, musíte použít vlastní účty úložiště a přidružit je k Log Analyticsm pracovním prostorům. Další informace o tom, jak tyto účty nastavit pomocí [příkazového řádku](/cli/azure/monitor/log-analytics/workspace/linked-storage), najdete v tématu.

Další informace o zavedení vlastního účtu úložiště najdete v tématu [účty úložiště vlastněné zákazníkem pro](private-storage.md) ingestování protokolů.

## <a name="restrictions-and-limitations"></a>Omezení a limity

### <a name="ampls"></a>AMPLS
Objekt AMPLS má několik omezení, které byste měli vzít v úvahu při plánování nastavení privátního propojení:

* Virtuální síť se může připojit jen k 1 AMPLS objektu. To znamená, že objekt AMPLS musí poskytovat přístup ke všem prostředkům Azure Monitor, ke kterým má virtuální síť přístup.
* Prostředek Azure Monitor (pracovní prostor nebo Application Insights komponenta) se může připojit k 5 AMPLSs.
* Objekt AMPLS se může připojit k 50 Azure Monitor prostředkům.
* Objekt AMPLS se může připojit k 10 soukromým koncovým bodům.

Přečtěte si, jak [zvážit omezení](#consider-limits) pro hlubší přezkoumání těchto limitů a jak naplánovat nastavení privátního propojení odpovídajícím způsobem.

### <a name="agents"></a>Agenti

V privátních sítích se musí používat nejnovější verze agentů Windows a Linux, aby bylo možné zajistit zabezpečenou příjem dat pro Log Analytics pracovních prostorů. Starší verze nemůžou nahrávat data monitorování v privátní síti.

**Agent Log Analytics pro Windows**

Použijte Log Analytics agenta verze 10.20.18038.0 nebo novější.

**Linuxový agent Log Analytics**

Použijte agenta verze 1.12.25 nebo novější. Pokud nemůžete, spusťte na svém VIRTUÁLNÍm počítači následující příkazy.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>portál Azure

Chcete-li použít Azure Monitor portálu, jako je Application Insights a Log Analytics, je nutné, aby byla rozšíření Azure Portal a Azure Monitor dostupná v privátních sítích. Do skupiny zabezpečení sítě přidejte [značky služby](../../firewall/service-tags.md) **azureactivedirectory selhala**, **AzureResourceManager**, **AzureFrontDoor. FirstParty** a **AzureFrontDoor. front-endu** .

### <a name="programmatic-access"></a>Programový přístup

Pokud chcete použít REST API, [CLI](/cli/azure/monitor) nebo PowerShell s Azure monitor v privátních sítích, přidejte do brány firewall [značky služby](../../virtual-network/service-tags-overview.md)  **azureactivedirectory selhala** a **AzureResourceManager** .

Přidání těchto značek vám umožní provádět akce, jako je například dotazování dat protokolu, vytváření a Správa Log Analyticsch pracovních prostorů a komponent AI.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Stažení sady SDK Application Insights ze sítě pro doručování obsahu

Vytvořte ve svém skriptu kód JavaScriptu tak, aby se prohlížeč nepokoušel stáhnout kód ze sítě CDN. Příklad je k dispozici na [GitHubu](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup) .

### <a name="browser-dns-settings"></a>Nastavení DNS prohlížeče

Pokud se připojujete k prostředkům Azure Monitor prostřednictvím privátního propojení, musí provoz na tyto prostředky projít privátním koncovým bodem, který je ve vaší síti nakonfigurovaný. Pokud chcete povolit privátní koncový bod, aktualizujte nastavení DNS tak, jak je vysvětleno v tématu [připojení k privátnímu koncovému bodu](#connect-to-a-private-endpoint). Některé prohlížeče používají vlastní nastavení DNS místo těch, která jste nastavili. Prohlížeč se může pokusit připojit k Azure Monitor veřejných koncových bodů a obejít zcela soukromý odkaz. Ověřte, že nastavení prohlížeče nepřepisuje ani neukládá do mezipaměti staré nastavení DNS. 

## <a name="next-steps"></a>Další kroky

- Informace o [privátním úložišti](private-storage.md)