---
title: 'Azure ExpressRoute: Konfigurace NPM pro okruhy'
description: Nakonfigurujte cloudové monitorování sítě (NPM) pro okruhy Azure ExpressRoute. To pokrývá monitorování přes privátní partnerský vztah ExpressRoute a partnerské vztahy Microsoftu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 7810afffd5da6d46439ff27ddb3f5b0aafdc2341
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981314"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurace Network Performance Monitoru pro ExpressRoute

Tento článek vám pomůže nakonfigurovat rozšíření Network Performance Monitor pro monitorování ExpressRoute. Network Performance Monitor (NPM) je cloudové řešení pro monitorování sítě, které monitoruje připojení mezi cloudovými nasazeními Azure a místními umístěními (pobočky atd.). NPM je součástí protokolů služby Azure Monitor. NPM nabízí rozšíření pro ExpressRoute, které umožňuje monitorovat výkon sítě přes okruhy ExpressRoute nakonfigurované tak, aby používaly soukromé partnerské vztahy nebo partnerské vztahy Microsoftu. Po konfiguraci NPM pro ExpressRoute můžete detekovat problémy se sítí a následně je identifikovat a odstranit. Tato služba je k dispozici také pro cloud Azure Government.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Můžete:

* Monitorování ztrát a latence v různých virtuální sítě a nastavení výstrah

* Monitorovat všechny cesty (včetně redundantních cest) v síti

* Řešení přechodných a neúspěšných síťových problémů, které je obtížné replikovat

* Pomůže určit konkrétní segment v síti, který je zodpovědný za snížený výkon.

* Získání propustnosti na virtuální síť (Pokud máte agenty nainstalovanou v každé virtuální síti)

* Zobrazit stav systému ExpressRoute z předchozího bodu v čase

## <a name="workflow"></a><a name="workflow"></a>Pracovní postup

Agenti monitorování se instalují na víc serverech, a to v místním prostředí i v Azure. Agenti spolu komunikují, ale neodesílají data, odesílají pakety TCP handshake. Komunikace mezi agenty umožňuje službě Azure mapovat topologii sítě a cestu, kterou může provoz trvat.

1. Vytvořte pracovní prostor NPM. Toto je stejný jako pracovní prostor Log Analytics.
2. Instalace a konfigurace softwarových agentů. (Pokud chcete monitorovat jenom přes partnerský vztah Microsoftu, nemusíte instalovat a konfigurovat softwarové agenty.): 
    * Nainstalujte agenty monitorování na místních serverech a virtuálních počítačích Azure (pro privátní partnerské vztahy).
    * Nakonfigurujte nastavení na serverech agenta monitorování, aby agenti monitorování umožnili komunikaci. (Otevřené porty brány firewall atd.)
3. Nakonfigurujte pravidla skupiny zabezpečení sítě (NSG), která povolí agentovi monitorování nainstalovanému na virtuálních počítačích Azure komunikovat s místními agenty monitorování.
4. Nastavení monitorování: automatické zjišťování a správa sítí, které jsou v NPM viditelné.

Pokud již používáte Network Performance Monitor k monitorování jiných objektů nebo služeb a již máte pracovní prostor v jedné z podporovaných oblastí, můžete přeskočit krok 1 a krok 2 a zahájit konfiguraci pomocí kroku 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Krok 1: vytvoření pracovního prostoru

Vytvořte pracovní prostor v předplatném, který má odkaz virtuální sítě na okruhy ExpressRoute.

1. V [Azure Portal](https://portal.azure.com)vyberte předplatné, které má virtuální sítě partnerský vztah k vašemu okruhu ExpressRoute. Pak vyhledejte v seznamu služeb na **webu Marketplace** pro ' Network Performance Monitor '. Kliknutím na tlačítko Zpět otevřete stránku **Network Performance Monitor** .

   >[!NOTE]
   >Můžete vytvořit nový pracovní prostor nebo použít existující pracovní prostor. Pokud chcete použít existující pracovní prostor, musíte zajistit, aby byl pracovní prostor migrován do nového dotazovacího jazyka. [Další informace...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portál](./media/how-to-npm/3.png)<br><br>
2. V dolní části stránky hlavní **Network Performance Monitor** klikněte na **vytvořit** . otevře se stránka **Network Performance Monitor-vytvořit nové řešení** . Klikněte na **Log Analytics pracovní prostor – vyberte pracovní prostor** a otevřete stránku pracovní prostory. Kliknutím na **+ vytvořit nový pracovní prostor** otevřete stránku pracovní prostor.
3. Na stránce **Log Analytics pracovní prostor** vyberte **vytvořit novou**a pak nakonfigurujte následující nastavení:

   * Log Analytics pracovní prostor – zadejte název pracovního prostoru.
   * Předplatné – Pokud máte více předplatných, vyberte tu, kterou chcete přidružit k novému pracovnímu prostoru.
   * Skupina prostředků – vytvořte skupinu prostředků nebo použijte existující.
   * Umístění – toto umístění se používá k určení umístění účtu úložiště, který se používá pro protokoly připojení agenta.
   * Cenová úroveň – vyberte cenovou úroveň.
  
     >[!NOTE]
     >Okruh ExpressRoute může být kdekoli na světě. Nemusí být ve stejné oblasti jako pracovní prostor.
     >
  
     ![pracovní prostor](./media/how-to-npm/4.png)<br><br>
4. Kliknutím na tlačítko **OK** uložte a nasaďte šablonu nastavení. Až se šablona ověří, kliknutím na **vytvořit** nasadíte pracovní prostor.
5. Po nasazení pracovního prostoru přejděte na prostředek **NetworkMonitoring (název)** , který jste vytvořili. Ověřte nastavení a pak klikněte na **řešení vyžaduje další konfiguraci**.

   ![Další konfigurace](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Krok 2: instalace a konfigurace agentů

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2,1: Stáhněte si instalační soubor agenta.

1. Přejít na kartu **Společná nastavení** na stránce **Konfigurace Network Performance Monitor** pro váš prostředek. V části **instalovat agenty Log Analytics** klikněte na agenta, který odpovídá procesoru vašeho serveru, a stáhněte instalační soubor.
2. Potom zkopírujte ID a **primární klíč** **pracovního prostoru** do poznámkového bloku.
3. V části **Konfigurace agentů Log Analytics pro monitorování pomocí protokolu TCP** Stáhněte skript PowerShellu. PowerShellový skript vám pomůže otevřít příslušný port brány firewall pro transakce TCP.

   ![Skript prostředí PowerShell](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2,2: nainstalujte agenta monitorování do každého monitorovacího serveru (na každé virtuální síti, kterou chcete monitorovat).

Doporučujeme nainstalovat alespoň dva agenty na každou stranu připojení ExpressRoute pro zajištění redundance (například místně, Azure virtuální sítě). Agent musí být nainstalovaný na Windows serveru (2008 SP1 nebo novější). Monitorování okruhů ExpressRoute pomocí systému Windows Desktop OS a operačního systému Linux není podporováno. K instalaci agentů použijte následující postup:
   
  >[!NOTE]
  >Agenti, kteří jsou nabízeni SCOM (včetně [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)), nemusí být schopni konzistentně detekovat své umístění, pokud jsou hostovány v Azure. Doporučujeme, abyste tyto agenty nepoužívali v Azure virtuální sítě k monitorování ExpressRoute.
  >

1. Spuštěním **instalačního programu** nainstalujte agenta na každý server, který chcete použít pro monitorování ExpressRoute. Server, který používáte pro monitorování, může být buď virtuální počítač, nebo místní, a musí mít přístup k Internetu. Je nutné nainstalovat alespoň jednoho agenta místně a jednoho agenta v každém segmentu sítě, který chcete monitorovat v Azure.
2. Na **úvodní** stránce klikněte na **Další**.
3. Na stránce **licenční podmínky** si přečtěte licenci a pak klikněte na **Souhlasím**.
4. Na stránce **cílová složka** změňte nebo ponechte výchozí instalační složku a pak klikněte na **Další**.
5. Na stránce **Možnosti instalace agenta** se můžete rozhodnout připojit agenta k Azure monitor protokolů nebo Operations Manager. Nebo můžete nechat volby prázdné, pokud chcete agenta nakonfigurovat později. Po provedení výběru klikněte na tlačítko **Další**.

   * Pokud se rozhodnete připojit k **Azure Log Analytics**, vložte do předchozího oddílu **ID pracovního prostoru** a **klíč pracovního prostoru** (primární klíč), který jste zkopírovali do poznámkového bloku. Pak klikněte na tlačítko **Další**.

     ![ID a klíč](./media/how-to-npm/8.png)
   * Pokud se rozhodnete připojit k **Operations Manager**, na stránce **Konfigurace skupiny pro správu** zadejte **název skupiny pro**správu, **Server pro správu**a **port serveru pro správu**. Pak klikněte na tlačítko **Další**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Na stránce **účet akce agenta** vyberte účet **místní systém** nebo  **účet domény nebo místního počítače**. Pak klikněte na tlačítko **Další**.

     ![Účet](./media/how-to-npm/10.png)
6. Na stránce **připraveno k instalaci** zkontrolujte volby a pak klikněte na **nainstalovat**.
7. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.
8. Po dokončení se Microsoft Monitoring Agent zobrazí v Ovládacích panelech. Můžete zkontrolovat svou konfiguraci a ověřit, zda je agent připojen k Azure Monitor protokolů. Po připojení se Agent zobrazí zpráva informující o: **Microsoft Monitoring Agent se úspěšně připojil ke službě Microsoft Operations Management Suite**.

9. Tento postup opakujte pro každou virtuální síť, kterou potřebujete monitorovat.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2,3: Konfigurace nastavení proxy serveru (volitelné)

Pokud používáte webový proxy server pro přístup k Internetu, použijte následující postup ke konfiguraci nastavení proxy serveru pro Microsoft Monitoring Agent. Proveďte tyto kroky pro každý server. pokud máte mnoho serverů, které je nutné nakonfigurovat, může být jednodušší použít skript, který tento proces zautomatizuje. Pokud ano, přečtěte si téma [Konfigurace nastavení proxy serveru pro Microsoft Monitoring Agent pomocí skriptu](../log-analytics/log-analytics-windows-agent.md).

Konfigurace nastavení proxy serveru pro Microsoft Monitoring Agent pomocí ovládacích panelů:

1. Otevřete **Ovládací panely**.
2. Otevřete **Microsoft Monitoring Agent**.
3. Klikněte na kartu **Nastavení proxy serveru**.
4. Vyberte **použít proxy server** a zadejte adresu URL a číslo portu, pokud je to potřeba. Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro přístup k proxy serveru.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2,4: ověření připojení agenta

Můžete snadno ověřit, jestli agenti komunikují.

1. Na serveru s agentem monitorování otevřete **Ovládací panely**.
2. Otevřete **Microsoft Monitoring Agent**.
3. Klikněte na kartu **Azure Log Analytics** .
4. Ve sloupci **stav** byste měli vidět, že se agent úspěšně připojil k Azure monitor protokolů.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2,5: otevřete porty brány firewall na serverech agenta monitorování.

Chcete-li použít protokol TCP, je nutné otevřít porty brány firewall, aby bylo zajištěno, že agenti monitorování budou moci komunikovat.

Můžete spustit skript prostředí PowerShell pro vytvoření klíčů registru, které jsou vyžadovány Network Performance Monitor. Tento skript také vytvoří pravidla brány Windows Firewall, která agentům monitorování umožní vytvářet připojení TCP mezi sebou. Klíče registru vytvořené skriptem určují, jestli se mají protokolovat protokoly ladění, a cestu k souboru protokolů. Definuje také port TCP agenta používaný pro komunikaci. Hodnoty těchto klíčů jsou automaticky nastaveny pomocí skriptu. Tyto klíče byste neměli ručně měnit.

Ve výchozím nastavení je otevřený port 8084. Vlastní port můžete použít zadáním parametru "číslo_portu" do skriptu. Pokud to ale uděláte, musíte zadat stejný port pro všechny servery, na kterých spouštíte skript.

>[!NOTE]
>Skript PowerShellu pro EnableRules konfiguruje pravidla brány Windows Firewall jenom na serveru, na kterém se skript spouští. Pokud máte bránu firewall sítě, měli byste se ujistit, že umožňuje provoz určený pro port TCP používaný Network Performance Monitor.
>
>

Na serverech agentů otevřete okno PowerShellu s oprávněními správce. Spusťte skript prostředí PowerShell [EnableRules](https://aka.ms/npmpowershellscript) (který jste si stáhli dříve). Nepoužívejte žádné parametry.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Krok 3: Konfigurace pravidel skupiny zabezpečení sítě

Pokud chcete monitorovat servery agenta v Azure, musíte nakonfigurovat pravidla skupiny zabezpečení sítě (NSG) tak, aby povolovala přenosy TCP na portu, který používá NPM pro syntetické transakce. Výchozí port je 8084. To umožňuje, aby byl agent monitorování nainstalovaný na virtuálním počítači Azure ke komunikaci s místním agentem monitorování.

Další informace o NSG najdete v tématu [skupiny zabezpečení sítě](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Ujistěte se, že jste nainstalovali agenty (místní server a Agent Azure serveru) a předtím, než budete pokračovat v tomto kroku, spusťte skript PowerShellu.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Krok 4: zjištění připojení partnerských vztahů

1. Přejděte na stránku s přehledem Network Performance Monitor tak, že přejdete na stránku **všechny prostředky** a potom kliknete na npm pracovní prostor na seznamu povolených.

   ![pracovní prostor npm](./media/how-to-npm/npm.png)
2. Řídicí panel zobrazíte kliknutím na dlaždici s přehledem **Network Performance Monitor** . Řídicí panel obsahuje stránku ExpressRoute, která ukazuje, že ExpressRoute je ve stavu Unconfigured. Kliknutím na **Nastavení funkcí** otevřete stránku konfigurace Network Performance Monitor.

   ![nastavení funkcí](./media/how-to-npm/npm2.png)
3. Na stránce konfigurace přejděte na kartu partnerské vztahy ExpressRoute, která se nachází na levé straně panelu. Potom klikněte na možnost **zjistit nyní**.

   ![vyhledávání](./media/how-to-npm/13.png)
4. Po dokončení zjišťování se zobrazí seznam obsahující následující položky:
   * Všechna připojení partnerského vztahu Microsoftu v okruhech ExpressRoute, která jsou přidružená k tomuto předplatnému.
   * Všechna připojení privátních partnerských vztahů, která se připojují ke službě virtuální sítě přidružené k tomuto předplatnému.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Krok 5: Konfigurace monitorování

V této části nakonfigurujete monitorování. Postupujte podle kroků pro typ partnerského vztahu, který chcete monitorovat: **privátní partnerské vztahy**nebo **partnerské vztahy Microsoftu**.

### <a name="private-peering"></a>Soukromý partnerský vztah

U privátního partnerského vztahu se po dokončení zjišťování zobrazí pravidla pro jedinečný **název okruhu** a **název virtuální**sítě. Zpočátku jsou tato pravidla zakázaná.

![pravidla](./media/how-to-npm/14.png)

1. Zaškrtněte políčko **sledovat tohoto partnerského vztahu** .
2. Zaškrtněte políčko **Povolit monitorování stavu pro tento partnerský vztah**.
3. Vyberte podmínky monitorování. Můžete nastavit vlastní prahové hodnoty pro generování událostí stavu zadáním prahových hodnot. Pokaždé, když hodnota podmínky překročí zvolenou prahovou hodnotu pro vybranou dvojici síť nebo podsítě, vygeneruje se událost stavu.
4. Kliknutím na tlačítko AGENTi ON-PREM AGENTs **Add Agents** přidejte místní servery, ze kterých chcete monitorovat připojení privátního partnerského vztahu. Ujistěte se, že jste zvolili jenom agenty, kteří mají připojení ke koncovému bodu služby Microsoftu, který jste zadali v části pro krok 2. Místní agenti musí být schopni kontaktovat koncový bod pomocí připojení ExpressRoute.
5. Uložte nastavení.
6. Po povolení pravidel a výběru hodnot a agentů, které chcete monitorovat, je k dispozici počkat přibližně 30-60 minut, než se hodnoty začnou naplňovat, a dlaždice **monitorování ExpressRoute** budou k dispozici.

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu

U partnerského vztahu Microsoftu klikněte na partnerské vztahy Microsoftu, které chcete monitorovat, a nakonfigurujte nastavení.

1. Zaškrtněte políčko **sledovat tohoto partnerského vztahu** . 
2. Volitelné Můžete změnit cílový koncový bod služby Microsoft. Ve výchozím nastavení NPM zvolí koncový bod služby společnosti Microsoft jako cíl. NPM monitoruje připojení z vašich místních serverů do tohoto cílového koncového bodu prostřednictvím ExpressRoute. 
    * Chcete-li změnit tento cílový koncový bod, klikněte na odkaz **(Upravit)** v části **cíl:** a v seznamu adres URL vyberte jiný cílový koncový bod služby společnosti Microsoft.
      ![Upravit cíl](./media/how-to-npm/edit_target.png)<br>

    * Můžete použít vlastní adresu URL nebo IP adresu. Tato možnost je zvláště důležitá, pokud používáte partnerské vztahy Microsoftu k navázání připojení ke službám Azure PaaS, jako jsou Azure Storage, databáze SQL a websites, které jsou nabízeny na veřejných IP adresách. Provedete to tak, že kliknete na odkaz **(místo toho použít vlastní adresu URL nebo IP adresu)** v dolní části seznamu adres URL a pak zadáte veřejný koncový bod služby Azure PaaS, který je připojený prostřednictvím partnerského vztahu Microsoftu pro ExpressRoute.
    ![vlastní adresa URL](./media/how-to-npm/custom_url.png)<br>

    * Pokud používáte Tato volitelná nastavení, ujistěte se, že je zde vybraný jenom koncový bod služby Microsoft. Koncový bod musí být připojený k ExpressRoute a dosažitelný místními agenty.
3. Zaškrtněte políčko **Povolit monitorování stavu pro tento partnerský vztah**.
4. Vyberte podmínky monitorování. Můžete nastavit vlastní prahové hodnoty pro generování událostí stavu zadáním prahových hodnot. Pokaždé, když hodnota podmínky překročí zvolenou prahovou hodnotu pro vybranou dvojici síť nebo podsítě, vygeneruje se událost stavu.
5. Kliknutím na tlačítko AGENTi ON-PREM AGENTs **Add Agents** přidejte místní servery, ze kterých chcete monitorovat připojení partnerského vztahu Microsoftu. Ujistěte se, že jste vybrali pouze agenty, kteří mají připojení ke koncovým bodům služby společnosti Microsoft, které jste zadali v části pro krok 2. Místní agenti musí být schopni kontaktovat koncový bod pomocí připojení ExpressRoute.
6. Uložte nastavení.
7. Po povolení pravidel a výběru hodnot a agentů, které chcete monitorovat, je k dispozici počkat přibližně 30-60 minut, než se hodnoty začnou naplňovat, a dlaždice **monitorování ExpressRoute** budou k dispozici.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Krok 6: zobrazení dlaždic monitorování

Po zobrazení dlaždic monitorování se vaše okruhy ExpressRoute a prostředky připojení monitorují pomocí NPM. Kliknutím na dlaždici partnerského vztahu Microsoftu můžete přejít k podrobnostem o stavu připojení partnerských vztahů Microsoftu.

![monitorování dlaždic](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Stránka Network Performance Monitor

Stránka NPM obsahuje stránku pro ExpressRoute, která zobrazuje přehled stavu ExpressRoute okruhů a partnerských vztahů.

![Snímek obrazovky zobrazující řídicí panel s přehledem stavu okruhů ExpressRoute a partnerských vztahů.](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Seznam okruhů

Pokud chcete zobrazit seznam všech sledovaných okruhů ExpressRoute, klikněte na dlaždici **okruhy ExpressRoute** . Můžete vybrat okruh a zobrazit jeho stav, diagramy trendů pro ztrátu paketů, využití šířky pásma a latenci. Grafy jsou interaktivní. Můžete vybrat vlastní časový interval pro vykreslení grafů. Přetažením myši na oblast v grafu můžete přiblížit a zobrazit jemně odstupňované datové body.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Trend ztrát, latence a propustnosti

Grafy šířky pásma, latence a ztrát jsou interaktivní. Pomocí ovládacích prvků myši můžete přiblížit libovolný oddíl těchto grafů. Můžete také zobrazit data o šířce pásma, latenci a ztrátě pro další intervaly kliknutím na položku **Datum a čas**, která se nachází pod tlačítkem akce v levém horním rohu.

![trend](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Seznam partnerských vztahů

Pokud chcete zobrazit seznam všech připojení k virtuálním sítím přes privátní partnerský vztah, klikněte na dlaždici **privátní partnerské vztahy** na řídicím panelu. Tady můžete vybrat připojení k virtuální síti a zobrazit jeho stav, diagramy trendů pro ztrátu paketů, využití šířky pásma a latenci.

![seznam okruhů](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Zobrazení uzlů

Pokud chcete zobrazit seznam všech odkazů mezi místními uzly a virtuálními počítači Azure nebo koncovými body služby Microsoftu pro zvolené připojení partnerského vztahu ExpressRoute, klikněte na **Zobrazit odkazy na uzly**. Můžete zobrazit stav každého propojení a také trend ztráty a latence, ke které jsou přidruženy.

![zobrazení uzlů](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Topologie okruhů

Pokud chcete zobrazit topologii okruhů, klikněte na dlaždici **topologie** . Tím přejdete k zobrazení topologie vybraného okruhu nebo partnerského vztahu. Diagram topologie poskytuje latenci pro jednotlivé segmenty v síti. Jednotlivé segmenty vrstvy 3 jsou reprezentovány uzlem diagramu. Kliknutím na směrování Odhalte další podrobnosti o směrování.

Můžete zvýšit úroveň viditelnosti tak, aby zahrnovala místní směrování, přesunutím posuvníku pod **filtry**. Přesunutí posuvníku doleva nebo doprava zvýší nebo sníží počet segmentů směrování v grafu topologie. Latence napříč každým segmentem je viditelná, což umožňuje rychlejší izolaci vysoce čekacích segmentů ve vaší síti.

![filtry](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Detailní zobrazení topologie okruhu

Toto zobrazení ukazuje připojení virtuální sítě.
![podrobná topologie](./media/how-to-npm/17.png)
