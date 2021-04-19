---
title: Použití Azure Private Linku k bezpečnému propojení sítí k Azure Monitoru
description: Použití Azure Private Linku k bezpečnému propojení sítí k Azure Monitoru
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 97e589755602c14a11873fee5288ee8c6e24ba83
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714273"
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

* Privátní koncový bod ve vaší virtuální síti umožňuje IT přístup k Azure Monitor koncovým bodům prostřednictvím privátních IP adres z fondu vaší sítě namísto použití s veřejnými IP adresami těchto koncových bodů. Díky tomu můžete dál používat prostředky Azure Monitor, aniž byste museli otevřít virtuální síť, aby nevyžadovala odchozí provoz. 
* Provoz z privátního koncového bodu do vašich prostředků Azure Monitor bude přecházet přes páteřní síť Microsoft Azure a nebude směrován do veřejných sítí. 
* Můžete nakonfigurovat jednotlivé pracovní prostory nebo komponenty a povolit nebo odepřít příjem a dotazy z veřejných sítí. Poskytuje ochranu na úrovni prostředků, abyste mohli řídit provoz na konkrétní prostředky.

> [!NOTE]
> Jeden prostředek Azure Monitor může patřit do více AMPLSs, ale nemůžete připojit jednu virtuální síť k více než jednomu AMPLS. 

## <a name="planning-your-private-link-setup"></a>Plánování nastavení privátního propojení

Před nastavením Azure Monitor nastavení privátního propojení zvažte svoji topologii sítě a specifickou topologii směrování DNS. 

### <a name="the-issue-of-dns-overrides"></a>Problém přepsání DNS
Některé Azure Monitor služby používají globální koncové body, což znamená, že obsluhují požadavky zaměřené na libovolný pracovní prostor nebo komponentu. Několika příklady jsou koncový bod ingestování Application Insights a koncový bod dotazu obou Application Insights a Log Analytics.

Když nastavíte připojení k privátnímu propojení, vaše služba DNS se aktualizuje, aby mapovala Azure Monitor koncové body na privátní IP adresy z rozsahu IP adres vaší virtuální sítě. Tato změna přepíše všechna předchozí mapování těchto koncových bodů, což může mít smysluplné důsledky, které jsou níže uvedené. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor privátní odkaz platí pro všechny prostředky Azure Monitor – to je vše nebo nic
Vzhledem k tomu, že některé koncové body Azure Monitor jsou globální, není možné vytvořit připojení privátního propojení pro konkrétní součást nebo pracovní prostor. Místo toho, když nastavíte privátní odkaz na jednu součást Application Insights nebo pracovní prostor Log Analytics, budou se záznamy DNS aktualizovat pro **všechny** Application Insights komponenty. Jakékoli pokusy o ingestování nebo dotazování komponenty procházejí přes privátní odkaz a pravděpodobně selžou. S ohledem na Log Analytics jsou koncové body pro ingestování a konfiguraci specifické pro pracovní prostory, což znamená, že nastavení privátního odkazu bude platit jenom pro zadané pracovní prostory. Ingestování a konfigurace dalších pracovních prostorů se přesměrují na výchozí koncové body veřejné Log Analytics.

![Diagram přepsání DNS v jedné virtuální síti](./media/private-link-security/dns-overrides-single-vnet.png)

To platí nejen pro konkrétní virtuální síť, ale pro všechny virtuální sítě, které sdílejí stejný server DNS (viz [problém přepsání DNS](#the-issue-of-dns-overrides)). Například požadavek na ingestování protokolů do jakékoli Application Insights komponenty se vždycky pošle prostřednictvím trasy privátního odkazu. Součásti, které nejsou propojené s AMPLS, selžou ověřování privátních odkazů a neprojde se.

> [!NOTE]
> K uzavření: Když nastavíte připojení privátního propojení k jednomu prostředku, vztahuje se na prostředky Azure Monitor napříč vaší sítí. U Application Insightsch prostředků se jedná o vše nebo nic. To znamená, že byste měli přidat všechny Application Insights prostředky do vaší sítě k vašemu AMPLS nebo žádné z nich.
> 
> Abychom mohli zpracovávat rizika exfiltraceí dat, doporučujeme, abyste do svého AMPLS přidali všechny prostředky Application Insights a Log Analytics a zablokovali tak co nejvíc síťových přenosů.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor privátní odkaz platí pro celou síť.
Některé sítě se skládají z několika virtuální sítě. Pokud virtuální sítě používá stejný server DNS, přepíše mapování DNS mezi sebou a může také přerušit komunikaci mezi sebou pomocí Azure Monitor (podívejte [se na problémy s přepsáními DNS](#the-issue-of-dns-overrides)). V konečném případě bude moci komunikovat s Azure Monitor pouze poslední síť VNet, protože služba DNS namapuje Azure Monitor koncových bodů na privátní IP adresy z tohoto virtuální sítě rozsahu (které nemusí být dosažitelné z jiných virtuální sítě).

![Diagram přepsání DNS ve více virtuální sítě](./media/private-link-security/dns-overrides-multiple-vnets.png)

Ve výše uvedeném diagramu se virtuální síť 10.0.1. x nejdřív připojí k AMPLS1 a mapuje Azure Monitor globální koncové body na IP adresy z jejího rozsahu. Později se virtuální síť 10.0.2. x připojí k AMPLS2 a přepíše mapování DNS *stejných globálních koncových bodů* s IP adresami z rozsahu. Vzhledem k tomu, že tyto virtuální sítě nejsou partnerského vztahu, první virtuální síť teď nedokáže dosáhnout těchto koncových bodů.

> [!NOTE]
> Závěr: AMPLS Setup má vliv na všechny sítě sdílející stejné zóny DNS. Aby se předešlo přepisování mapování koncových bodů DNS, je nejlepší nastavit jeden privátní koncový bod v partnerské síti (například virtuální síť rozbočovače) nebo oddělit sítě na úrovni DNS (například Foe pomocí DNS pro přeposílání nebo samostatné servery DNS).

### <a name="hub-spoke-networks"></a>Sítě s rozbočovačem a paprsky
Topologie centra s paprsky se můžou vyhnout problémům s přepsáními DNS nastavením privátního odkazu v centrální virtuální síti (hlavní), namísto nastavení privátního propojení pro každou virtuální síť zvlášť. Tato instalace dává smysl zvláště tehdy, když se sdílejí prostředky Azure Monitor používané paprskem virtuální sítě. 

![Střed a-paprsek – jednoduché – PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Můžete záměrně chtít vytvořit samostatné soukromé odkazy pro virtuální sítě paprsků, například umožníte každé virtuální síti přístup k omezené sadě prostředků monitorování. V takových případech můžete pro každou virtuální síť vytvořit vyhrazený soukromý koncový bod a AMPLS, ale musíte taky ověřit, jestli nesdílejí stejné zóny DNS, aby se předešlo přepsání DNS.


### <a name="consider-limits"></a>Zvážit omezení

Jak je uvedeno v [omezeních a omezeních](#restrictions-and-limitations), objekt AMPLS má několik omezení zobrazených v následující topologii:
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

2. Vyberte předplatné, skupinu prostředků a název koncového bodu a oblast, ve které by měla být aktivní. Oblast musí být stejná jako virtuální síť, ke které ji připojujete.

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

    ![Snímek obrazovky s podrobnostmi o výběru privátního koncového bodu](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Nyní jste vytvořili nový privátní koncový bod, který je připojen k tomuto AMPLS.

## <a name="review-and-validate-your-private-link-setup"></a>Kontrola a ověření nastavení privátního odkazu

### <a name="reviewing-your-endpoints-dns-settings"></a>Kontrola nastavení DNS koncového bodu
Pro soukromý koncový bod, který jste vytvořili, by teď měly být nakonfigurované čtyři zóny DNS:

[![Snímek obrazovky zóny DNS privátního koncového bodu.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor – Azure-com
* privatelink-OMS-o statistice provozu-Azure-com
* privatelink-ODS-o statistice provozu-Azure-com
* privatelink-agentsvc-Azure-Automation-NET

> [!NOTE]
> Každá z těchto zón mapuje konkrétní koncové body Azure Monitor na soukromé IP adresy z fondu IP adres. Adresy IP uvedené na následujících obrázcích jsou pouze příklady. Místo toho by měla vaše konfigurace zobrazovat soukromé IP adresy z vaší vlastní sítě.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor – Azure-com
Tato zóna pokrývá globální koncové body, které používá Azure Monitor, což znamená, že tyto koncové body slouží k zvažování všech prostředků, nikoli konkrétního. Tato zóna by měla mít namapované koncové body pro:
* `in.ai` -Application Insights koncový bod ingestování (globální i místní položka)
* `api` -Application Insights a Log Analytics koncový bod rozhraní API
* `live` -Application Insights aktivní koncový bod metriky
* `profiler` -Application Insights koncový bod profileru
* `snapshot`-Application Insights snímky obrazovky koncového bodu [ ![ privátní DNS sledování zóny – Azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-OMS-o statistice provozu-Azure-com
Tato zóna pokrývá mapování specifického pracovního prostoru na koncové body OMS. Měla by se zobrazit položka pro každý pracovní prostor propojený s AMPLS připojeným k tomuto privátnímu koncovému bodu.
[![Snímek obrazovky s Privátní DNSovou zónou OMS – o statistice provozu-Azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ODS-o statistice provozu-Azure-com
Tato zóna pokrývá mapování specifického pracovního prostoru do koncových bodů ODS – koncový bod příjmu Log Analytics. Měla by se zobrazit položka pro každý pracovní prostor propojený s AMPLS připojeným k tomuto privátnímu koncovému bodu.
[![Snímek obrazovky s Privátní DNSovou zónou ODS – o statistice provozu-Azure-com](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-Azure-Automation-NET
Tato zóna pokrývá mapování specifického pracovního prostoru do koncových bodů služby Agent Automation. Měla by se zobrazit položka pro každý pracovní prostor propojený s AMPLS připojeným k tomuto privátnímu koncovému bodu.
[![Snímek obrazovky Privátní DNS agenta zóny svc-Azure-Automation-NET.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-BLOB-Core-Windows-NET
Tato zóna nakonfiguruje připojení k účtu úložiště balíčků globálních agentů. Prostřednictvím IT agenti mohou stahovat nové nebo aktualizované balíčky řešení (označované také jako sady Management Pack). Pro obsluhu Log Analyticsch agentů se vyžaduje jenom jedna položka bez ohledu na to, kolik pracovních prostorů se používá.
[![Snímek obrazovky Privátní DNS objektů BLOB zóny – Core-Windows-NET](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Tato položka je přidána pouze do nastavení privátních odkazů vytvořených v 19. dubna 2021.


### <a name="validating-you-are-communicating-over-a-private-link"></a>Ověřování, které komunikujete prostřednictvím privátního odkazu
* Chcete-li ověřit, že vaše požadavky jsou nyní odesílány prostřednictvím privátního koncového bodu a privátních koncových bodů mapovaných pomocí protokolu IP, můžete je zkontrolovat pomocí nástroje pro sledování sítě nebo dokonce i v prohlížeči. Například při pokusu o dotazování pracovního prostoru nebo aplikace se ujistěte, že je žádost odeslána privátní IP adresa mapovaná na koncový bod rozhraní API, v tomto příkladu je to *172.17.0.9*.

    Poznámka: některé prohlížeče můžou používat další nastavení DNS (viz [nastavení DNS v prohlížeči](#browser-dns-settings)). Ujistěte se, že platí nastavení DNS.

* Abyste se ujistili, že váš pracovní prostor nebo komponenta nedostává požadavky z veřejných sítí (nepřipojených prostřednictvím AMPLS), nastavte veřejnou ingestování prostředků a příznaky dotazů na *ne* , jak je vysvětleno v tématu [Správa přístupu mimo rozsahy privátních odkazů](#manage-access-from-outside-of-private-links-scopes).

* Z klienta v chráněné síti použijte `nslookup` některý z koncových bodů uvedených v zónách DNS. Měl by ho přeložit váš server DNS na mapované privátní IP adresy místo veřejných IP adres, které se používají ve výchozím nastavení.


## <a name="configure-log-analytics"></a>Konfigurace služby Log Analytics

Přejděte na Azure Portal. V nabídce prostředku Log Analytics pracovního prostoru existuje položka s názvem **izolace sítě** na levé straně. Z této nabídky můžete řídit dva různé stavy.

![Izolace sítě LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Připojené Azure Monitor obory privátního propojení
Na této obrazovce se zobrazí všechny rozsahy připojené k pracovnímu prostoru. Připojení k oborům (AMPLSs) umožňuje přístup k tomuto pracovnímu prostoru ze sítě z virtuální sítě připojené ke každému AMPLS. Vytvoření připojení prostřednictvím tohoto umístění má stejný účinek jako nastavení v oboru, stejně jako v souvislosti s [připojením Azure Monitorch prostředků](#connect-azure-monitor-resources). Chcete-li přidat nové připojení, vyberte možnost **Přidat** a vyberte Azure monitor obor privátních odkazů. Pokud ho chcete připojit, vyberte **použít** . Všimněte si, že se pracovní prostor může připojit k 5 AMPLS objektům, jak je uvedeno v [omezeních a omezeních](#restrictions-and-limitations). 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Správa přístupu mimo rozsahy privátních odkazů
Nastavení v dolní části této stránky řídí přístup z veřejných sítí, což znamená sítě nepřipojené přes uvedené obory (AMPLSs). Nastavení **povoluje přístup k veřejné síti pro** ingestování **bez** blokování přijímání protokolů z počítačů mimo připojené obory. Nastavení **povoluje přístup k veřejné síti pro dotazy** **bez** dotazů na bloky přicházející z počítačů mimo rozsah. Který zahrnuje dotazy spouštěné prostřednictvím sešitů, řídicích panelů, prostředí klienta založeného na rozhraní API, přehledy Azure Portal a dalších možností. Prostředí spuštěné mimo Azure Portal a dotaz Log Analytics data musí být spuštěná také v rámci virtuální sítě s privátním propojením.

### <a name="exceptions"></a>Výjimky
Omezení přístupu, jak je vysvětleno výše, se nevztahuje na Azure Resource Manager a má proto tato omezení:
* Přístup k datům – při blokování nebo povolování dotazů z veřejných sítí se používá pro většinu Log Analyticsch prostředí, některá prostředí dotazují data prostřednictvím Azure Resource Manager a proto se nebudou moci dotazovat na data, pokud se u Správce prostředků nepoužijí nastavení privátního propojení (funkce už brzy). Příklady jsou Azure Monitor řešení, sešity a přehledy a konektor LogicApp.
* Správa pracovního prostoru – změny nastavení pracovního prostoru a konfigurace (včetně zapnutí nebo vypnutí těchto nastavení přístupu) se spravují pomocí Azure Resource Manager. Omezte přístup ke správě pracovních prostorů pomocí příslušných rolí, oprávnění, síťových ovládacích prvků a auditování. Další informace najdete v tématu [Azure monitor role, oprávnění a zabezpečení](../roles-permissions-security.md).

> [!NOTE]
> Protokoly a metriky nahrané do pracovního prostoru prostřednictvím [nastavení diagnostiky](../essentials/diagnostic-settings.md) přecházejí přes zabezpečený privátní kanál Microsoft a tato nastavení se nekontrolují.

### <a name="log-analytics-solution-packs-download"></a>Stažení balíčků řešení Log Analytics
Log Analytics agenti potřebují přístup k globálnímu účtu úložiště, aby mohli stahovat balíčky řešení. Nastavení privátních odkazů, která se vytvořila během 19. dubna 2021, se můžou dostat do úložiště balíčků řešení pro agenty prostřednictvím privátního propojení. To je umožněno prostřednictvím nové zóny DNS vytvořené pro [BLOB.Core.Windows.NET](#privatelink-blob-core-windows-net).

Pokud se vaše nastavení privátního odkazu vytvořilo před 19. dubna 2021, nedosáhnete úložiště balíčků řešení prostřednictvím privátního odkazu. Chcete-li zvládnout, že můžete provést jednu z následujících akcí:
* Opětovné vytvoření AMPLS a připojení privátního koncového bodu
* Umožněte vašim agentům přístup k účtu úložiště prostřednictvím jeho veřejného koncového bodu přidáním následujících pravidel do brány firewall povolených:

    | Cloudové prostředí | Prostředek agenta | Porty | Směr |
    |:--|:--|:--|:--|
    |Veřejný partnerský vztah Azure     | scadvisorcontent.blob.core.windows.net         | 443 | Odchozí
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Odchozí
    |Azure (Čína) 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Odchozí


## <a name="configure-application-insights"></a>Konfigurace Application Insights

Přejděte na Azure Portal. V prostředku součásti Azure Monitor Application Insights je na levé straně položka nabídky **izolace sítě** . Z této nabídky můžete řídit dva různé stavy.

![Izolace sítě AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Nejdřív můžete připojit tento prostředek Application Insights, abyste Azure Monitor obory privátních odkazů, ke kterým máte přístup. Vyberte **Přidat** a vyberte **Azure monitor obor privátních odkazů**. Pokud ho chcete připojit, vyberte použít. Na této obrazovce se zobrazí všechny připojené obory. Díky tomu může toto připojení umožňovat přístup k této komponentě v síťovém provozu propojených virtuálních sítí a má stejný účinek jako připojení k oboru, protože jsme provedli [připojení Azure Monitorch prostředků](#connect-azure-monitor-resources). 

Pak můžete řídit, jak se tento prostředek dá oslovit mimo obory privátních odkazů (AMPLS) uvedené dříve. Pokud nastavíte možnost **umožní přístup k veřejné síti pro** ingestování na **ne**, počítače nebo sady SDK mimo připojené obory nemůžou do této součásti nahrávat data. Pokud nastavíte možnost **povolí přístup k veřejné síti pro dotazy** na **ne**, počítače mimo rozsah nebudou mít přístup k datům v tomto Application Insights prostředku. Tato data zahrnují přístup k protokolům APM, metrikám a živému streamu metrik a také k prostředí postaveným nahoře, jako jsou sešity, řídicí panely, prostředí klientů založené na rozhraní API, přehledy v Azure Portal a další. 

> [!NOTE]
> V prostředích, které není na portálu, se musí také spouštět na virtuální síti propojené s privátní sítí, která obsahuje monitorovaná zatížení.

K privátnímu odkazu budete muset přidat prostředky hostující monitorovaná zatížení. Podívejte se například na téma [použití privátních koncových bodů pro webovou aplikaci Azure](../../app-service/networking/private-endpoint.md).

Přístup tímto způsobem se omezuje jenom na data v prostředku Application Insights. Změny konfigurace, včetně zapnutí nebo vypnutí těchto nastavení přístupu, se však spravují pomocí Azure Resource Manager. Proto byste měli omezit přístup k Správce prostředků pomocí příslušných rolí, oprávnění, síťových ovládacích prvků a auditování. Další informace najdete v tématu [Azure monitor role, oprávnění a zabezpečení](../roles-permissions-security.md).

> [!NOTE]
> Aby bylo možné plně zabezpečit Application Insights na základě pracovních prostorů, musíte uzamknout přístup k prostředkům Application Insights a také k příslušnému pracovnímu prostoru Log Analytics.
>
> Diagnostika na úrovni kódu (Profiler/ladicí program) vyžaduje [poskytnutí vlastního účtu úložiště](../app/profiler-bring-your-own-storage.md) pro podporu privátního odkazu.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Zpracování libovolné povahy privátních odkazů
Jak je vysvětleno v tématu [Plánování nastavení privátního propojení](#planning-your-private-link-setup), nastavení privátního propojení i pro jeden prostředek má vliv na všechny prostředky Azure monitor v těchto sítích a v dalších sítích, které sdílejí stejné DNS. Toto chování může být náročný proces připojování. Vezměte v úvahu následující možnosti:

* Vše v rámci – nejjednodušší a nejbezpečnější přístup je přidání všech Application Insights komponent do AMPLS. U součástí, ke kterým chcete i nadále přistupovat z jiných sítí, ponechte příznaky "" možnost "nastavit veřejný internetový přístup pro ingestování/dotaz" nastavené na Ano (výchozí).
* Izolace sítí – Pokud jste (nebo je můžete v souladu s) pomocí paprsku virtuální sítě, postupujte podle pokynů v [síťové topologii centra – paprsky v Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Pak nastavte samostatné nastavení privátního propojení v příslušném paprsku virtuální sítě. Nezapomeňte také oddělit zóny DNS, protože sdílení zón DNS s jinými sítěmi paprsků způsobí [přepsání DNS](#the-issue-of-dns-overrides).
* Použití vlastních zón DNS pro konkrétní aplikace – toto řešení umožňuje přístup k výběru Application Insights komponent prostřednictvím privátního propojení a udržování všech ostatních přenosů přes veřejné trasy.
    - Nastavte si [vlastní privátní ZÓNU DNS](../../private-link/private-endpoint-dns.md)a dejte jí jedinečný název, třeba Internal.monitor.Azure.com.
    - Vytvořte AMPLS a soukromý koncový bod **a vyberte** neautomatickou integraci s PRIVÁTNÍm DNS.
    - Přejít na soukromý koncový bod – > konfiguraci DNS a zkontrolovat navrhované mapování plně kvalifikovaných názvů domén.
    - Zvolte Přidání konfigurace a výběr zóny internal.monitor.azure.com, kterou jste právě vytvořili.
    - Přidat záznamy pro výše ![ nakonfigurované zóny DNS z výše uvedeného snímku obrazovky](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Přejít na komponentu Application Insights a zkopírovat její [připojovací řetězec](../app/sdk-connection-string.md).
    - Aplikace nebo skripty, které chtějí volat tuto komponentu přes privátní odkaz, by měly používat připojovací řetězec s EndpointSuffix = Internal. monitor. Azure. com.
* Mapování koncových bodů prostřednictvím souborů hostitelů místo DNS – k přístupu privátního odkazu jenom z konkrétního počítače nebo virtuálního počítače v síti:
    - Nastavte AMPLS a privátní koncový bod **a vyberte** neautomatickou integraci s PRIVÁTNÍm DNS. 
    - Konfigurace výše uvedených záznamů v počítači, na kterém je aplikace spuštěná, v souboru Hosts


## <a name="use-apis-and-command-line"></a>Použití rozhraní API a příkazového řádku

Proces popsaný výše můžete automatizovat pomocí Azure Resource Manager šablon, REST a rozhraní příkazového řádku.

Pokud chcete vytvořit a spravovat obory privátních odkazů, použijte [REST API](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) nebo [Azure CLI (AZ monitor Private-Link-Scope)](/cli/azure/monitor/private-link-scope).

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

Podrobnější přezkoumání těchto omezení najdete v tématu [zvážení omezení](#consider-limits) .

### <a name="agents"></a>Agenti

Nejnovější verze agentů systému Windows a Linux je nutné použít k podpoře zabezpečení příjmu Log Analytics pracovních prostorů. Starší verze nemůžou nahrávat data monitorování přes soukromou síť.

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

### <a name="querying-data"></a>Dotazování na data
[ `externaldata` Operátor](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) se nepodporuje prostřednictvím privátního propojení, protože čte data z účtů úložiště, ale nezaručuje, že se úložiště bude získávat soukromě.

### <a name="programmatic-access"></a>Programový přístup

Pokud chcete použít REST API, [CLI](/cli/azure/monitor) nebo PowerShell s Azure monitor v privátních sítích, přidejte do brány firewall [značky služby](../../virtual-network/service-tags-overview.md)  **azureactivedirectory selhala** a **AzureResourceManager** .

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Stažení sady SDK Application Insights ze sítě pro doručování obsahu

Vytvořte ve svém skriptu kód JavaScriptu tak, aby se prohlížeč nepokoušel stáhnout kód ze sítě CDN. Příklad je k dispozici na [GitHubu](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup) .

### <a name="browser-dns-settings"></a>Nastavení DNS prohlížeče

Pokud se připojujete k prostředkům Azure Monitor prostřednictvím privátního propojení, musí provoz na tyto prostředky projít privátním koncovým bodem, který je ve vaší síti nakonfigurovaný. Pokud chcete povolit privátní koncový bod, aktualizujte nastavení DNS tak, jak je vysvětleno v tématu [připojení k privátnímu koncovému bodu](#connect-to-a-private-endpoint). Některé prohlížeče používají vlastní nastavení DNS místo těch, která jste nastavili. Prohlížeč se může pokusit připojit k Azure Monitor veřejných koncových bodů a obejít zcela soukromý odkaz. Ověřte, že nastavení prohlížeče nepřepisuje ani neukládá do mezipaměti staré nastavení DNS. 

## <a name="next-steps"></a>Další kroky

- Informace o [privátním úložišti](private-storage.md)
