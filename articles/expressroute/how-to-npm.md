---
title: 'Azure ExpressRoute: Konfigurace protokolu NPM pro okruhy'
description: Konfigurace cloudového monitorování sítě (NPM) pro okruhy Azure ExpressRoute. To zahrnuje monitorování přes ExpressRoute soukromého partnerského vztahu a partnerského vztahu Microsoftu.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.openlocfilehash: 54fa3dcbfbbcb3153f81407a9bc9b52511405390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076592"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurace Network Performance Monitoru pro ExpressRoute

Tento článek vám pomůže nakonfigurovat rozšíření sledování výkonu sítě pro sledování ExpressRoute. Network Performance Monitor (NPM) je cloudové řešení pro monitorování sítě, které monitoruje připojení mezi cloudovými nasazeními Azure a místními umístěními (pobočky atd.). NPM je součástí protokolů služby Azure Monitor. NPM nabízí rozšíření pro ExpressRoute, které umožňuje monitorovat výkon sítě přes okruhy ExpressRoute nakonfigurované tak, aby používaly soukromé partnerské vztahy nebo partnerské vztahy Microsoftu. Po konfiguraci NPM pro ExpressRoute můžete detekovat problémy se sítí a následně je identifikovat a odstranit. Tato služba je k dispozici také pro cloud Azure Government.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Můžete:

* Sledování ztrát a latence napříč různými virtuálními sítěmi a nastavení výstrah

* Sledování všech cest (včetně redundantních cest) v síti

* Řešení potíží s přechodnou sítí a problémy se sítí v okamžiku, které je obtížné replikovat

* Pomozte určit konkrétní segment v síti, který je zodpovědný za snížený výkon

* Získání propustnosti na virtuální síť (Pokud máte v každé virtuální síti nainstalované agenty)

* Zobrazení stavu systému ExpressRoute z předchozího bodu v čase

## <a name="workflow"></a><a name="workflow"></a>Pracovního postupu

Agenti monitorování se instalují na více serverech, místně i v Azure. Agenti vzájemně komunikují, ale neodesílají data, odesílají pakety tcp handshake. Komunikace mezi agenty umožňuje Azure mapovat topologii sítě a cestu, kterou může provoz trvat.

1. Vytvořte pracovní prostor NPM. To je stejné jako pracovní prostor Analýzy protokolů.
2. Nainstalujte a nakonfigurujte softwarové agenty. (Pokud chcete sledovat pouze přes Microsoft Peering, není nutné instalovat a konfigurovat softwarové agenty.): 
    * Nainstalujte agenty monitorování na místní servery a virtuální počítače Azure (pro privátní partnerský vztah).
    * Nakonfigurujte nastavení na serverech agenta monitorování, aby agenti monitorování mohli komunikovat. (Otevřít porty brány firewall atd.)
3. Nakonfigurujte pravidla skupiny zabezpečení sítě (NSG), aby agent monitorování nainstalovaný na virtuálních počítačích Azure mohl komunikovat s místními agenty monitorování.
4. Nastavení monitorování: Automatické zjišťování a správa sítí, které jsou viditelné v npm.

Pokud již používáte nástroj Sledování výkonu sítě ke sledování jiných objektů nebo služeb a pracovní prostor již máte v jedné z podporovaných oblastí, můžete přeskočit krok 1 a krok 2 a zahájit konfiguraci krokem 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Krok 1: Vytvoření pracovního prostoru

Vytvořte pracovní prostor v předplatném, které má propojení virtuálních sítí s okruhy ExpressRoute.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte předplatné, které má virtuální počítače peered do okruhu ExpressRoute. Potom vyhledejte v seznamu služeb na **webu Marketplace** možnost Sledování výkonu sítě. V návratu klepnutím otevřete stránku **Sledování výkonu sítě.**

   >[!NOTE]
   >Můžete vytvořit nový pracovní prostor nebo použít existující pracovní prostor. Pokud chcete použít existující pracovní prostor, musíte se ujistit, že pracovní prostor byl migrován do nového dotazovacího jazyka. [Více informací...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portál](./media/how-to-npm/3.png)<br><br>
2. V dolní části hlavní stránky **Sledování výkonu sítě** sem otevřete stránku Sledování výkonu sítě – vytvořit novou stránku **řešení.** **Create** Klikněte na **Pracovní prostor Analýzy protokolů – vyberte pracovní prostor,** který otevře stránku Pracovní prostory. Kliknutím **na + Vytvořit nový pracovní prostor** otevřete stránku Pracovní prostor.
3. Na stránce **pracovního prostoru Log Analytics** vyberte **Vytvořit nový**a nakonfigurujte následující nastavení:

   * Pracovní prostor Analýzy protokolů – zadejte název pracovního prostoru.
   * Předplatné – Pokud máte více předplatných, vyberte ten, který chcete přidružit k novému pracovnímu prostoru.
   * Skupina prostředků – Vytvořte skupinu prostředků nebo použijte existující.
   * Umístění – toto umístění se používá k určení umístění účtu úložiště, který se používá pro protokoly připojení agenta.
   * Cenová úroveň – vyberte cenovou úroveň.
  
     >[!NOTE]
     >Okruh ExpressRoute může být kdekoli na světě. Nemusí být ve stejné oblasti jako pracovní prostor.
     >
  
     ![Pracovní prostor](./media/how-to-npm/4.png)<br><br>
4. Kliknutím na **OK** uložte a nasaďte šablonu nastavení. Po ověření šablony klikněte na **Vytvořit** a nasadit pracovní prostor.
5. Po nasazení pracovního prostoru přejděte na prostředek **NetworkMonitoring(název),** který jste vytvořili. Ověřte nastavení a klepněte na tlačítko **Řešení vyžaduje další konfiguraci**.

   ![další konfigurace](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Krok 2: Instalace a konfigurace agentů

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2.1: Stažení instalačního souboru agenta

1. Přejděte na kartu **Společné nastavení** na stránce **Konfigurace monitoru výkonu sítě** pro váš prostředek. Klikněte na agenta, který odpovídá procesoru serveru, v části **Install Log Analytics Agents** a stáhněte si instalační soubor.
2. Dále zkopírujte **ID pracovního prostoru** a **primární klíč** do poznámkového bloku.
3. V části **Konfigurovat agenty analýzy protokolů pro monitorování pomocí protokolu TCP** stáhněte skript Powershell. Skript prostředí PowerShell vám pomůže otevřít příslušný port brány firewall pro transakce TCP.

   ![Skript PowerShellu](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2.2: Instalace agenta monitorování na každý monitorovací server (na každou virtuální síť, kterou chcete monitorovat)

Doporučujeme nainstalovat alespoň dva agenty na každé straně připojení ExpressRoute pro redundanci (například místní virtuální chod Azure). Agent musí být nainstalován na systému Windows Server (2008 SP1 nebo novější). Monitorování obvodů ExpressRoute pomocí operačního systému Windows Desktop A operačního systému Linux není podporováno. K instalaci agentů použijte následující kroky:
   
  >[!NOTE]
  >Agenti prosazovaní SCOM (včetně [MMA)](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)nemusí být schopni konzistentně zjistit jejich umístění, pokud jsou hostované v Azure. Doporučujeme nepoužívat tyto agenty v Azure VNETs ke sledování ExpressRoute.
  >

1. Spusťte **instalační program** a nainstalujte agenta na každý server, který chcete použít pro sledování expressroute. Server, který používáte pro monitorování, může být virtuální počítač nebo místní a musí mít přístup k Internetu. Musíte nainstalovat alespoň jednoho místního agenta a jednoho agenta v každém segmentu sítě, který chcete v Azure monitorovat.
2. Na **úvodní** stránce klikněte na **Další**.
3. Na stránce **Licenční podmínky** si přečtěte licenci a klikněte na **souhlasím**.
4. Na stránce **Cílová složka** změňte nebo ponechejte výchozí instalační složku a klepněte na tlačítko **Další**.
5. Na stránce **Možnosti nastavení agenta** můžete agenta připojit k protokolům Azure Monitor nebo Operations Manageru. Nebo můžete ponechat možnosti prázdné, pokud chcete později nakonfigurovat agenta. Po provedení výběru klikněte na **Další**.

   * Pokud jste se rozhodli připojit k **Azure Log Analytics**, vložte **ID pracovního prostoru** a klíč **pracovního prostoru** (primární klíč), který jste zkopírovali do poznámkového bloku v předchozí části. Potom klepněte na tlačítko **Další**.

     ![ID a klíč](./media/how-to-npm/8.png)
   * Pokud jste se rozhodli připojit k **nástroji Operations Manager**, zadejte na stránce Konfigurace **skupiny pro správu** **název skupiny pro správu**, **server pro správu**a **port serveru pro správu**. Potom klepněte na tlačítko **Další**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Na stránce **Účet akce agenta** zvolte buď **účet místního systému,** nebo **účet domény nebo místního počítače**. Potom klepněte na tlačítko **Další**.

     ![Účet](./media/how-to-npm/10.png)
6. Na stránce **Připraveno k instalaci** zkontrolujte své volby a klikněte na **Nainstalovat**.
7. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.
8. Po dokončení se v Ovládacích panelech zobrazí agent microsoft monitoring. Můžete zkontrolovat konfiguraci tam a ověřte, že agent je připojen k protokolům Azure Monitor. Po připojení se zobrazí zpráva s informací: **Agent monitorování společnosti Microsoft se úspěšně připojil ke službě Microsoft Operations Management Suite**.

9. Tento postup opakujte pro každou virtuální síť, kterou je třeba monitorovat.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2.3: Konfigurace nastavení proxy serveru (volitelné)

Pokud používáte webový proxy server pro přístup k Internetu, použijte následující kroky ke konfiguraci nastavení proxy serveru pro agenta Microsoft Monitoring Agent. Proveďte tyto kroky pro každý server. pokud máte mnoho serverů, které je nutné nakonfigurovat, může být jednodušší použít skript, který tento proces zautomatizuje. Pokud ano, [přečtěte si informace o konfiguraci nastavení serveru proxy pro agenta sledování společnosti Microsoft pomocí skriptu](../log-analytics/log-analytics-windows-agent.md).

Postup konfigurace nastavení proxy serveru pro agenta monitorování společnosti Microsoft pomocí Ovládacích panelů:

1. Otevřete **Ovládací panely**.
2. Otevřete **Microsoft Monitoring Agent**.
3. Klikněte na kartu **Nastavení proxy serveru**.
4. Vyberte **Použít proxy server** a v případě potřeby zadejte adresu URL a číslo portu. Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro přístup k proxy serveru.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2.4: Ověření připojení agenta

Můžete snadno ověřit, zda vaši agenti komunikují.

1. Na serveru s agentem monitorování otevřete **Ovládací panely**.
2. Otevřete **agenta Microsoft Monitoring Agent**.
3. Klikněte na kartu **Azure Log Analytics.**
4. Ve sloupci **Stav** byste měli vidět, že agent úspěšně připojen k protokolům Azure Monitor.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2.5: Otevření portů brány firewall na serverech agenta monitorování

Chcete-li použít protokol TCP, musíte otevřít porty brány firewall, abyste zajistili, že agenti monitorování mohou komunikovat.

Můžete spustit skript prostředí PowerShell a vytvořit klíče registru, které jsou vyžadovány programem Sledování výkonu sítě. Tento skript také vytvoří pravidla brány Windows Firewall, která umožňují agentům monitorování vytvářet vzájemně připojení TCP. Klíče registru vytvořené skriptem určují, zda mají protokolovat protokoly ladění a cesta k souboru protokolu. Definuje také port TCP agenta používaný pro komunikaci. Hodnoty těchto klíčů jsou automaticky nastaveny skriptem. Tyto klíče byste neměli měnit ručně.

Port 8084 je otevřen ve výchozím nastavení. Vlastní port můžete použít poskytnutím parametru "portNumber" skriptu. Pokud tak však učiníte, je nutné zadat stejný port pro všechny servery, na kterých skript spustíte.

>[!NOTE]
>Skript PowerShell "EnableRules" konfiguruje pravidla brány Windows Firewall pouze na serveru, na kterém je skript spuštěn. Pokud máte síťovou bránu firewall, měli byste se ujistit, že umožňuje přenosy určené pro port TCP používané sledováním výkonu sítě.
>
>

Na serverech agenta otevřete okno prostředí PowerShell s oprávněními správce. Spusťte skript [EnableRules](https://aka.ms/npmpowershellscript) PowerShell (který jste stáhli dříve). Nepoužívejte žádné parametry.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Krok 3: Konfigurace pravidel skupiny zabezpečení sítě

Chcete-li monitorovat servery agentů, které jsou v Azure, musíte nakonfigurovat pravidla skupiny zabezpečení sítě (NSG), aby byl povolen přenos Protokolu TCP na portu používaném npm pro syntetické transakce. Výchozí port je 8084. To umožňuje agentovi monitorování nainstalovanému na virtuálním počítači Azure komunikovat s místním agentem monitorování.

Další informace o skupině zabezpečení sítě naleznete v [tématu Skupiny zabezpečení sítě](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Ujistěte se, že jste nainstalovali agenty (místního agenta serveru i agenta serveru Azure) a než budete pokračovat v tomto kroku, spusťte skript Prostředí PowerShell.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Krok 4: Zjištění připojení partnerských společností

1. Přejděte na dlaždici přehledu sledování výkonu sítě tak, že přejdete na stránku **Všechny prostředky** a kliknete na pracovní prostor NPM na seznamu povolených.

   ![pracovní prostor npm](./media/how-to-npm/npm.png)
2. Kliknutím na dlaždici **Přehled sledování výkonu sítě** zobrazíte řídicí panel. Řídicí panel obsahuje stránku ExpressRoute, která ukazuje, že ExpressRoute je v "nenakonfigurovaném stavu". Klepnutím na **položku Nastavení funkce** otevřete konfigurační stránku Sledování výkonu sítě.

   ![nastavení funkce](./media/how-to-npm/npm2.png)
3. Na konfigurační stránce přejděte na kartu "Partnerské partnerské partnerské partnery ExpressRoute" na levém bočním panelu. Dále klepněte na tlačítko **Objevit nyní**.

   ![Objevte](./media/how-to-npm/13.png)
4. Po dokončení zjišťování se zobrazí seznam obsahující následující položky:
   * Všechna připojení partnerského vztahu Microsoftu v okruhu ExpressRoute, které jsou přidruženy k tomuto předplatnému.
   * Všechna privátní partnerská připojení, která se připojují k virtuálním sítím přidruženým k tomuto předplatnému.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Krok 5: Konfigurace monitorů

V této části nakonfigurujete monitory. Postupujte podle pokynů pro typ partnerského vztahu, který chcete sledovat: **soukromý partnerský vztah**nebo partnerský vztah **Microsoftu**.

### <a name="private-peering"></a>Soukromý partnerský vztah

Pro soukromý partnerský vztah se po dokončení zjišťování zobrazí pravidla pro jedinečný **název okruhu** a **název virtuální sítě**. Zpočátku jsou tato pravidla zakázána.

![pravidla](./media/how-to-npm/14.png)

1. Zaškrtněte políčko **Sledovat tento partnerský vztah.**
2. Zaškrtněte políčko **Povolit monitorování stavu pro tento partnerský vztah**.
3. Zvolte podmínky monitorování. Můžete nastavit vlastní prahové hodnoty pro generování událostí stavu zadáním prahových hodnot. Vždy, když hodnota podmínky překročí vybranou prahovou hodnotu pro vybraný pár sítě/podsítě, je vygenerována událost stavu.
4. Kliknutím na tlačítko ON-PREM AGENTI **Přidat agenty** přidejte místní servery, ze kterých chcete sledovat připojení soukromého partnerského vztahu. Ujistěte se, že jste vybrali pouze agenty, kteří mají připojení ke koncovému bodu služby Společnosti Microsoft, který jste zadali v části kroku 2. Místní agenti musí být schopni dosáhnout koncového bodu pomocí připojení ExpressRoute.
5. Uložte nastavení.
6. Po povolení pravidel a výběru hodnot a agentů, které chcete sledovat, je čekání přibližně 30-60 minut pro hodnoty začít naplnění a **ExpressRoute monitorovací** dlaždice k dispozici.

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu

V partnerské síti Microsoft klepněte na připojení partnerského vztahu Microsoftu, které chcete sledovat, a nakonfigurujte nastavení.

1. Zaškrtněte políčko **Sledovat tento partnerský vztah.** 
2. (Nepovinné) Cílový koncový bod služby Microsoft můžete změnit. Ve výchozím nastavení npm zvolí koncový bod služby společnosti Microsoft jako cíl. NPM monitoruje připojení z místních serverů k tomuto cílovému koncovému bodu prostřednictvím ExpressRoute. 
    * Chcete-li tento cílový koncový bod změnit, klepněte na odkaz **(upravit)** v části **Cíl:** a ze seznamu adres URL vyberte jiný cílový koncový bod služby Microsoft.
      ![upravit cíl](./media/how-to-npm/edit_target.png)<br>

    * Můžete použít vlastní adresu URL nebo adresu IP. Tato možnost je zvláště důležité, pokud používáte partnerský vztah Microsoftu k navázání připojení ke službám Azure PaaS, jako je Azure Storage, databáze SQL a weby, které jsou nabízeny na veřejných IP adresách. Chcete-li to provést, klikněte na odkaz **(Použít vlastní adresu URL nebo IP adresu)** v dolní části seznamu adres URL a zadejte veřejný koncový bod služby Azure PaaS, která je připojena prostřednictvím partnerského vztahu Microsoftu ExpressRoute.
    ![vlastní adresa URL](./media/how-to-npm/custom_url.png)<br>

    * Pokud používáte tato volitelná nastavení, ujistěte se, že je zde vybrán pouze koncový bod služby společnosti Microsoft. Koncový bod musí být připojen k ExpressRoute a dosažitelné místními agenty.
3. Zaškrtněte políčko **Povolit monitorování stavu pro tento partnerský vztah**.
4. Zvolte podmínky monitorování. Můžete nastavit vlastní prahové hodnoty pro generování událostí stavu zadáním prahových hodnot. Vždy, když hodnota podmínky překročí vybranou prahovou hodnotu pro vybraný pár sítě/podsítě, je vygenerována událost stavu.
5. Kliknutím na tlačítko ON-PREM AGENTI **Přidat agenty** přidejte místní servery, ze kterých chcete sledovat připojení partnerského vztahu Microsoftu. Ujistěte se, že jste vybrali pouze agenty, kteří mají připojení k koncovým bodům služby Společnosti Microsoft, které jste zadali v části krok 2. Místní agenti musí být schopni dosáhnout koncového bodu pomocí připojení ExpressRoute.
6. Uložte nastavení.
7. Po povolení pravidel a výběru hodnot a agentů, které chcete sledovat, je čekání přibližně 30-60 minut pro hodnoty začít naplnění a **ExpressRoute monitorovací** dlaždice k dispozici.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Krok 6: Zobrazení dlaždic monitorování

Jakmile se zobrazí dlaždice monitorování, vaše ExpressRoute obvody a prostředky připojení jsou sledovány NPM. Kliknutím na dlaždici Microsoft Peering můžete přejít k podrobnostem o stavu připojení Microsoft Peering.

![sledování dlaždic](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Stránka Sledování výkonu sítě

Stránka NPM obsahuje stránku expressroute, která zobrazuje přehled stavu okruhů ExpressRoute a partnerských stran.

![Řídicí panel](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Seznam obvodů

Chcete-li zobrazit seznam všech sledovaných okruhů ExpressRoute, klepněte na dlaždici **okruhů ExpressRoute.** Můžete vybrat okruh a zobrazit jeho stav, grafy trendů pro ztrátu paketů, využití šířky pásma a latenci. Grafy jsou interaktivní. Můžete vybrat vlastní časové okno pro vykreslení grafů. Můžete přetáhnout myší přes oblast v grafu přiblížit a zobrazit jemně odstupňované datové body.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Trend ztráty, latence a propustnosti

Grafy šířky pásma, latence a ztráty jsou interaktivní. Můžete přiblížit libovolnou část těchto grafů pomocí ovládacích prvků myši. Můžete také zobrazit šířku pásma, latence a ztráty data pro jiné intervaly kliknutím **na datum a čas**, který se nachází pod tlačítko Akce v levém horním rohu.

![Trend](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Seznam partnerských stran

Chcete-li zobrazit seznam všech připojení k virtuálním sítím přes privátní partnerský vztah, klikněte na dlaždici **Soukromé partnerské vztahy** na řídicím panelu. Zde můžete vybrat připojení k virtuální síti a zobrazit její stav, grafy trendů pro ztrátu paketů, využití šířky pásma a latenci.

![seznam okruhů](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Zobrazení uzlů

Chcete-li zobrazit seznam všech propojení mezi místními uzly a koncovými body služby Virtuální počítače Azure/Microsoft pro vybrané připojení partnerského vztahu ExpressRoute, klikněte na **zobrazit odkazy uzlů**. Můžete zobrazit stav každého odkazu, stejně jako trend ztráty a latence s nimi spojené.

![zobrazení uzlů](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Topologie obvodu

Chcete-li zobrazit topologii obvodu, klepněte na dlaždici **Topologie.** Tím přejdete do zobrazení topologie vybraného okruhu nebo partnerského vztahu. Diagram topologie poskytuje latenci pro každý segment v síti. Každý směrování vrstvy 3 je reprezentováno uzlem diagramu. Kliknutím na hop odhalí více informací o hop.

Úroveň viditelnosti můžete zvýšit tak, aby zahrnovala místní směrování přesunutím posuvníku pod **filtry**. Posunutím posuvníku doleva nebo doprava se zvýší/sníží počet směrování v grafu topologie. Latence v každém segmentu je viditelná, což umožňuje rychlejší izolaci segmentů s vysokou latencí v síti.

![filtry](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Detailní pohled na topologii obvodu

Toto zobrazení zobrazuje připojení virtuální sítě.
![podrobná topologie](./media/how-to-npm/17.png)
