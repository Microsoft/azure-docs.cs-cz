---
title: Konfigurace Network Performance Monitor pro okruhy ExpressRoute – Azure | Dokumentace Microsoftu
description: Konfigurace monitorování (NPM) pro okruhy Azure ExpressRoute sítě založené na cloudu. Toto téma zahrnuje monitorování privátního partnerského vztahu ExpressRoute a partnerský vztah Microsoftu.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 93fd42739e0ec8ca9230688274b31fac5edf216d
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098574"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurace Network Performance Monitoru pro ExpressRoute

Tento článek vám pomůže nakonfigurovat rozšíření Network Performance Monitor k monitorování ExpressRoute. Network Performance Monitor (NPM) je řešení, která monitoruje připojení mezi nasazeními v cloudu Azure a místními umístěními (firemní pobočky, atd.) pro monitorování sítě založené na cloudu. NPM je součástí služby Log Analytics. NPM nabízí rozšíření pro ExpressRoute, který vám umožní monitorovat výkon sítě přes okruhy ExpressRoute, které jsou nakonfigurovány pro použití soukromého partnerského vztahu a partnerského vztahu Microsoftu. Když konfigurujete NPM pro ExpressRoute, můžete zjistit, identifikovat a eliminovat problémy se sítí. Tato služba je také k dispozici pro Azure Government Cloud.

Můžete:

* Monitorování ztrát a latence mezi různými virtuálními sítěmi a nastavení výstrah

* Monitorování všech cest (včetně redundantní cesty) v síti

* Řešení potíží s problémy se sítí přechodné a bodu v čase, které se těžko replikují

* Určení konkrétního segmentu sítě, který je důvodem sníženého výkonu

* Zjištění propustnosti na virtuální síť (Pokud máte agenty instalované v každé virtuální síti)

* Zobrazit stav systému ExpressRoute z předchozího bodu v čase

## <a name="workflow"></a>Pracovní postup

Instalace agentů monitorování na více serverech, místně i v Azure. Agenti komunikovat mezi sebou, ale neodesílat data, jejich odeslat pakety pro metodu handshake protokolu TCP. Umožňuje komunikaci mezi agenty Azure k mapování síťové topologie a cestu, může trvat provoz.

1. Vytvoření pracovního prostoru NPM. To je stejný jako pracovní prostor Log Analytics.
2. Instalace a konfigurace agentů softwaru. (Pokud chcete monitorovat přes Microsoft Peering, nepotřebujete k instalaci a konfiguraci agentů softwaru.): 
    * Instalace agentů na místních serverech a virtuálních počítačů Azure (pro soukromý partnerský vztah) monitorování.
    * Konfigurace nastavení na monitorování agent servery, které chcete povolit monitorování agenty ke komunikaci. (Otevřete porty brány firewall atd.)
3. Konfigurace sítě pravidla skupiny zabezpečení (NSG) aby monitorovací agent nainstalovaný na virtuálních počítačích Azure ke komunikaci s místní agentů monitorování.
4. Nastavení monitorování: Automaticky zjišťovat a spravovat, které sítě jsou viditelné v NPM.

Pokud už používáte Network Performance Monitor k monitorování jiných objektů nebo služeb a už máte pracovní prostor v jednom z podporovaných oblastí, můžete přeskočit krok 1 a 2 a začít s vaší konfigurací v kroku 3.

## <a name="configure"></a>Krok 1: Vytvořit pracovní prostor

Vytvořte pracovní prostor v rámci předplatného, který má propojení virtuálních sítí pro okruhy ExpressRoute.

1. V [webu Azure portal](https://portal.azure.com), vyberte předplatné, které má virtuální sítě v partnerském vztahu pro váš okruh ExpressRoute. Vyhledejte v seznamu služeb v **Marketplace** pro sledování výkonu sítě. V vrácení, klikněte na tlačítko Otevřít **Network Performance Monitor** stránky.

   >[!NOTE]
   >Můžete vytvořit nový pracovní prostor, nebo použít existující pracovní prostor. Pokud chcete použít existující pracovní prostor, musí se ujistěte, že pracovní prostor se migroval na nový dotazovací jazyk. [Další informace...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portál](./media/how-to-npm/3.png)<br><br>
2. V dolní části hlavního **Network Performance Monitor** klikněte na **vytvořit** otevřete **Network Performance Monitor – vytvořit nové řešení** stránky. Klikněte na tlačítko **pracovní prostor Log Analytics – vyberte pracovní prostor** otevřete stránku pracovních prostorů. Klikněte na tlačítko **+ vytvořit nový pracovní prostor** otevřete stránku pracovního prostoru.
3. Na **pracovní prostor Log Analytics** stránce **vytvořit nový**, nakonfigurujte následující nastavení:

  * Pracovní prostor log Analytics – zadejte název pro váš pracovní prostor.
  * Předplatné – Pokud máte více předplatných, vyberte ten, který chcete přidružit k novému pracovnímu prostoru.
  * Skupina prostředků - vytvořte skupinu prostředků nebo použijte již existující.
  * Umístění – toto umístění slouží k určení umístění účtu úložiště, který se používá pro připojení protokoly agenta.
  * Cenová úroveň – výběr cenové úrovně.
  
    >[!NOTE]
    >Okruh ExpressRoute může být kdekoli na světě. Nemusí být ve stejné oblasti jako pracovní prostor.
    >
  
    ![pracovní prostor](./media/how-to-npm/4.png)<br><br>
4. Klikněte na tlačítko **OK** uložení a nasazení nastavení šablony. Jakmile šablonu ověří, klikněte na tlačítko **vytvořit** nasazení pracovního prostoru.
5. Po nasazení pracovního prostoru, přejděte **NetworkMonitoring(name)** prostředek, který jste vytvořili. Ověřte nastavení a potom klikněte na **řešení vyžaduje další konfiguraci**.

   ![další konfigurace](./media/how-to-npm/5.png)

## <a name="agents"></a>Krok 2: Instalace a konfigurace agentů

### <a name="download"></a>2.1: Stáhněte instalační soubor agenta

1. Přejděte na **obecná nastavení** karty **konfigurace Network Performance monitoru** stránky pro váš prostředek. Klikněte na agenta, který odpovídá procesoru vašeho serveru ze **instalace agentů Log Analytics** části a stáhněte instalační soubor.
2. V dalším kroku zkopírujte **ID pracovního prostoru** a **primární klíč** do poznámkového bloku.
3. Z **konfigurace agentů Log Analytics pro monitorování prostřednictvím protokolu TCP** oddílu, stáhněte si skript prostředí Powershell. Skript prostředí PowerShell umožňuje otevřít port brány firewall pro TCP transakce.

  ![Skript PowerShellu](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2: Nainstalujte agenta monitorování na každou monitorovací server (v každé virtuální síti, která chcete monitorovat)

Doporučujeme nainstalovat aspoň dva agenty na každé straně připojení ExpressRoute pro zajištění redundance (například v místním virtuálním sítím Azure). Musí být agent nainstalovaný v systému Windows Server (2008 SP1 nebo novější). Monitorování pomocí OS plochy Windows a Linux OS okruhy ExpressRoute není podporováno. Použijte následující postup k instalaci agentů:
   
  >[!NOTE]
  >Agenti vynuceně podle SCOM (zahrnuje [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) nemusí být schopna zjistit konzistentně jejich umístění, pokud jsou hostované v Azure. Doporučujeme vám, že je velmi riskantní používat tyto agenty ve virtuálních sítích Azure k monitorování ExpressRoute.
  >

1. Spustit **nastavení** Agent na každém serveru, který chcete použít pro monitorování ExpressRoute. Server, který můžete využít pro monitorování, může být buď virtuální počítač nebo na místě a musí mít přístup k Internetu. Je potřeba nainstalovat aspoň jeden místního agenta a jednoho agenta v každém segmentu sítě, kterou chcete monitorovat v Azure.
2. Na **úvodní** stránce klikněte na **Další**.
3. Na **licenční podmínky** stránce, prostudujte licenční a potom klikněte na tlačítko **souhlasím**.
4. Na **cílovou složku** stránce, změňte nebo ponechte výchozí instalační složku a potom klikněte na tlačítko **Další**.
5. Na **možnosti instalace agenta** stránky, můžete také připojit agenta k Azure Log Analytics nebo Operations Manager. Nebo můžete ponechat volby prázdné Pokud chcete později konfigurovat agenta. Po provedení vybrané položky, klikněte na tlačítko **Další**.

  * Pokud jste zvolili pro připojení k **Azure Log Analytics**, vložte **ID pracovního prostoru** a **klíč pracovního prostoru** (primární klíč), který jste zkopírovali do poznámkového bloku v předchozí části. Pak klikněte na **Další**.

    ![ID a klíč](./media/how-to-npm/8.png)
  * Pokud jste zvolili pro připojení k **nástroje Operations Manager**na **konfigurace skupiny pro správu** stránky, zadejte **název skupiny pro správu**, **serveru pro správu** a **Port serveru pro správu**. Pak klikněte na **Další**.

    ![Operations Manager](./media/how-to-npm/9.png)
  * Na **účet Agent Action Account** zvolte buď **místní systém** účtu, nebo **doménový nebo místní účet počítače**. Pak klikněte na **Další**.

    ![Účet](./media/how-to-npm/10.png)
6. Na **připraveno k instalaci** stránky, zkontrolujte zvolené volby a pak klikněte na tlačítko **nainstalovat**.
7. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.
8. Jakmile budete hotovi, zobrazí se v Ovládacích panelech agenta Microsoft Monitoring Agent. Můžete zkontrolovat svou konfiguraci existuje a ověřit připojení agenta k Azure Log Analytics. Když se připojí, agent zobrazí zprávu: **Microsoft Monitoring Agent úspěšně připojilo ke službě Microsoft Operations Management Suite**.

9. Tento postup opakujte pro každou virtuální síť, které potřebujete k monitorování.

### <a name="proxy"></a>2.3: Konfigurace nastavení proxy serveru (nepovinné)

Pokud používáte webový proxy server pro přístup k Internetu, použijte následující postup ke konfiguraci nastavení proxy serveru pro Microsoft Monitoring Agent. Tyto kroky proveďte pro každý server. pokud máte mnoho serverů, které je nutné nakonfigurovat, může být jednodušší použít skript, který tento proces zautomatizuje. Pokud ano, přečtěte si téma [ke konfiguraci nastavení proxy serveru pro Microsoft Monitoring Agent pomocí skriptu](../log-analytics/log-analytics-windows-agent.md).

Konfigurace nastavení proxy serveru pro Microsoft Monitoring Agent pomocí ovládacích panelů:

1. Otevřít **ovládací panely**.
2. Otevřete **Microsoft Monitoring Agent**.
3. Klikněte na kartu **Nastavení proxy serveru**.
4. Vyberte **používat proxy server** a zadejte adresu URL a číslo portu, pokud je to zapotřebí. Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro přístup k proxy serveru.

  ![Proxy server](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4: Ověřit připojení agenta

Můžete snadno ověřit, zda jsou komunikaci agentů.

1. Na serveru agenta monitorování, otevřete **ovládací panely**.
2. Otevřít **agenta Microsoft Monitoring Agent**.
3. Klikněte na tlačítko **Azure Log Analytics** kartu.
4. V **stav** sloupce, měli byste vidět, že agenta úspěšně připojil ke službě Log Analytics.

  ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5: Otevřete porty brány firewall na serverech monitorování agenta

Pokud chcete použít protokol TCP, je nutné otevřít porty brány firewall, aby mohl komunikovat agentů monitorování.

Můžete spustit skript Powershellu pro vytvoření klíče registru, které jsou vyžadované Network Performance monitoru. Tento skript vytvoří také pravidla brány Windows Firewall pro povolení monitorování agentů k vytvoření připojení TCP mezi sebou. Klíče registru, který je vytvořen skriptem určete, zda protokoly ladění a cestu k souboru protokolů protokolu. Definuje také port TCP agent používá pro komunikaci. Hodnoty pro tyto klíče se automaticky nastaví skript. Neměli ručně měnit tyto klíče.

Ve výchozím nastavení se otevře port 8084. Můžete použít vlastní port zadáním parametru 'číslo_portu"do skriptu. Ale pokud tak učiníte, musíte zadat stejný port pro všechny servery, na které jste spustili skript.

>[!NOTE]
>"EnableRules" Powershellový skript nakonfiguruje pravidla brány Windows Firewall pouze na serveru, ve kterém se skript spouští. Pokud máte síťová brána firewall, by měl Ujistěte se, umožňuje provoz určený pro port TCP, která je používána ve sledování výkonu sítě.
>
>

Na serverech agenta otevřete okno Powershellu s oprávněními správce. Spustit [EnableRules](https://aka.ms/npmpowershellscript) skript prostředí PowerShell (který jste si stáhli dříve). Nepoužívejte žádné parametry.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>Krok 3: Konfigurace pravidla skupiny zabezpečení sítě

Pokud chcete monitorovat servery agenta, které jsou v Azure, je nutné nakonfigurovat skupiny (NSG) pravidla zabezpečení sítě umožňující provoz TCP na portu, používaný NPM pro syntetické transakce. Výchozí port je 8084. To umožňuje monitorování agent nainstalovaný na Virtuálním počítači Azure ke komunikaci s místní agent monitorování.

Další informace o NSG najdete v tématu [skupiny zabezpečení sítě](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Ujistěte se, že jste nainstalovali agenty (místního serveru agenta a agenta Azure server) a jste spustili skript prostředí PowerShell než budete pokračovat s tímto krokem.
>

## <a name="setupmonitor"></a>Krok 4: Zjistit partnerské vztahy virtuálních sítí

1. Přejděte na dlaždici s přehledem Network Performance Monitor tak, že přejdete **všechny prostředky** stránce a potom klikněte na seznam povolených adres NPM pracovního prostoru.

  ![pracovní prostor npm](./media/how-to-npm/npm.png)
2. Klikněte na tlačítko **Network Performance Monitor** dlaždici s přehledem a zobrazte si řídicí panel. Řídicí panel obsahuje stránku ExpressRoute, který ukazuje, že je služba ExpressRoute v do nenakonfigurovaného stavu. Klikněte na tlačítko **nastavení funkce** otevřete stránku konfigurace Network Performance monitoru.

  ![nastavení funkce](./media/how-to-npm/npm2.png)
3. Na stránce konfigurace přejděte na kartu partnerské vztahy ExpressRoute, nachází na levé straně panelu. Klepnutím na tlačítko **zjistit nyní**.

  ![Zjistit](./media/how-to-npm/13.png)
4. Po dokončení zjišťování, zobrazí se seznam obsahující následující položky:
  * Všechny partnerské vztahy Microsoftu v okruhy ExpressRoute, přidružené k tomuto předplatnému.
  * Všechny privátní partnerské vztahy, které se připojují k virtuálním sítím spojené s tímto odběrem.
            
## <a name="configmonitor"></a>Krok 5: Konfigurace monitorování

V této části nakonfigurujete monitorování. Postupujte podle kroků pro typ partnerského vztahu, které chcete sledovat: **soukromého partnerského vztahu**, nebo **partnerský vztah Microsoftu**.

### <a name="private-peering"></a>Soukromý partnerský vztah

Pro soukromý partnerský vztah, když se dokončí zjišťování, zobrazí se pravidla pro jedinečný **názvu okruhu** a **název virtuální sítě**. Na začátku tato pravidla jsou zakázané.

![pravidla](./media/how-to-npm/14.png)

1. Zkontrolujte, **monitorovat tento partnerský vztah** zaškrtávací políčko.
2. Zaškrtněte políčko **povolit monitorování stavu pro tento partnerský vztah**.
3. Vyberte monitorování podmínky. Můžete nastavit vlastní prahové hodnoty k vygenerování události týkající se stavu tak, že zadáte prahové hodnoty. Pokaždé, když se hodnota podmínka překročí jeho zvolená prahová hodnota pro vybranou síť/podsíť dvojici, vygeneruje událost stavu.
4. Klikněte na místní AGENTY **přidat agenty** tlačítko pro přidání na místních serverech, ze kterých chcete monitorovat privátní připojení s partnerským vztahem. Ujistěte se, že vyberte pouze agenty, které mají připojení ke koncovému bodu služby Microsoft, který jste zadali v části v kroku 2. Místní agenty musí být schopný připojit koncového bodu pomocí připojení ExpressRoute.
5. Uložte nastavení.
6. Po povolení pravidla a výběrem hodnoty a agenty, které chcete monitorovat, je Počkejte přibližně 30 – 60 minut pro hodnoty zahájíte, naplnění a **monitorování ExpressRoute** dlaždice k dispozici.

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu

Pro partnerský vztah Microsoftu, klikněte na připojení partnerského vztahu Microsoftu, kterou chcete monitorovat a nakonfigurujte nastavení.

1. Zkontrolujte, **monitorovat tento partnerský vztah** zaškrtávací políčko. 
2. (Volitelné) Můžete změnit cílový koncový bod služby Microsoftu. Ve výchozím nastavení vybere NPM koncového bodu služby Microsoft jako cíl. NPM monitoruje připojení z vašich místních serverů do tohoto cílového koncového bodu prostřednictvím ExpressRoute. 
    * Chcete-li změnit tento cílový koncový bod, klikněte na tlačítko **(Upravit)** odkaz pod **cíl:** a vyberte koncový bod cílové služby Microsoft jiný seznam adres URL.
      ![Upravit cíl](./media/how-to-npm/edit_target.png)<br>

    * Můžete použít vlastní adresu URL nebo IP adresu. Tato možnost je zvláště důležitá, pokud používáte k navázání připojení ke službám Azure PaaS, jako je například Azure Storage, SQL Database a weby, které nabízíme na veřejné IP adresy partnerského vztahu Microsoftu. Chcete-li to provést, klikněte na odkaz **(použijte vlastní adresu URL nebo IP adresu)** v dolní části Seznam adres URL, zadejte veřejný koncový bod služby Azure PaaS, který je připojený prostřednictvím partnerského vztahu ExpressRoute Microsoftu.
    ![Vlastní adresa URL](./media/how-to-npm/custom_url.png)<br>

    * Pokud použijete nastavení jsou volitelná, ujistěte se, že se tady vyberete pouze Microsoft koncový bod služby. Koncový bod musí být připojené k ExpressRoute a je dostupný místní agenty.
3. Zaškrtněte políčko **povolit monitorování stavu pro tento partnerský vztah**.
4. Vyberte monitorování podmínky. Můžete nastavit vlastní prahové hodnoty k vygenerování události týkající se stavu tak, že zadáte prahové hodnoty. Pokaždé, když se hodnota podmínka překročí jeho zvolená prahová hodnota pro vybranou síť/podsíť dvojici, vygeneruje událost stavu.
5. Klikněte na místní AGENTY **přidat agenty** tlačítko pro přidání na místních serverech, ze kterých chcete monitorovat připojení s partnerským vztahem Microsoft. Ujistěte se, že vyberte pouze agenty, které mají připojení ke koncovým bodům služby Microsoft, které jste zadali v části v kroku 2. Místní agenty musí být schopný připojit koncového bodu pomocí připojení ExpressRoute.
6. Uložte nastavení.
7. Po povolení pravidla a výběrem hodnoty a agenty, které chcete monitorovat, je Počkejte přibližně 30 – 60 minut pro hodnoty zahájíte, naplnění a **monitorování ExpressRoute** dlaždice k dispozici.

## <a name="explore"></a>Krok 6: Zobrazení monitorování dlaždice

Jakmile se zobrazí dlaždice monitorování, okruhy ExpressRoute a připojení prostředky jsou monitorovány pomocí NPM. Kliknutím na dlaždici Microsoft Peering a přejít k podrobnostem na stav připojení Microsoft Peering.

![dlaždice monitorování](./media/how-to-npm/15.png)

### <a name="dashboard"></a>Stránka pro sledování výkonu sítě

Na stránce NPM obsahuje stránku pro ExpressRoute, který zobrazuje přehled stavu okruhů ExpressRoute a partnerské vztahy.

![Řídicí panel](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>Seznam okruhy

Chcete-li zobrazit seznam všech monitorované okruhy ExpressRoute, klikněte na tlačítko **okruhy ExpressRoute** dlaždici. Můžete vybrat okruh a zobrazit její stav, grafy trendů využití šířky pásma, latence a ztráta paketů. Grafy jsou interaktivní. Můžete vybrat vlastní časový interval pro vykreslení grafy. Tažení myší přes oblast na graf můžete přiblížit a zobrazit podrobné datových bodů.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>Trend ztráty, latence a propustnosti

Šířka pásma, latence a ztráta grafy jsou interaktivní. Můžete přiblížit libovolné části tyto grafy pomocí myši ovládacích prvků. Můžete také zobrazit šířku pásma, latence a ztráta dat pro další intervaly kliknutím **data a času**, který je umístěn pod tlačítko akce v levém horním rohu.

![trend](./media/how-to-npm/16.png)

### <a name="peerings"></a>Seznam partnerských vztahů

Chcete zobrazit seznam všech připojení k virtuálním sítím přes privátní partnerský vztah, klikněte na tlačítko **privátní partnerské vztahy** dlaždici na řídicím panelu. Tady můžete vybrat virtuální síťové připojení a zobrazit její stav, grafy trendů využití šířky pásma, latence a ztráta paketů.

![seznam okruh](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>Zobrazení uzlů

Chcete zobrazit seznam všech propojení mezi uzly v místním a koncové body služby virtuální počítače Azure nebo Microsoft pro připojení s partnerským vztahem zvolené ExpressRoute, klikněte na tlačítko **zobrazit odkazy na uzly**. Můžete zobrazit stav každého odkazu, stejně jako trend ztrát a latence k nim má přiřazené.

![Zobrazení uzlů](./media/how-to-npm/nodes.png)

### <a name="topology"></a>Topologie okruh

Chcete-li zobrazit topologie okruhů, klikněte na tlačítko **topologie** dlaždici. Tím přejdete na zobrazení topologie zvolené okruhu nebo partnerského vztahu. Diagram topologie poskytuje latence pro každý segment v síti. Každého směrování vrstvy 3 je reprezentován uzlu diagramu. Kliknutím na hop zobrazí další podrobnosti o směrování.

Můžete zvýšit úroveň viditelnosti zahrnout segmenty směrování místní přesunutím posuvníku **filtry**. Přesunout posuvník doleva nebo doprava, zvyšuje nebo snižuje počet segmentů směrování v grafu topologie. Latence v každém segmentu je viditelné, který umožňuje rychlejší izolace segmentů vysoká latence ve vaší síti.

![Filtry](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Podrobné zobrazení topologie okruhu

Toto zobrazení uvádí připojení virtuální sítě.
![Podrobné topologie](./media/how-to-npm/17.png)
