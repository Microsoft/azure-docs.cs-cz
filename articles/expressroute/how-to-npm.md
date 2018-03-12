---
title: "Konfigurace programu Sledování výkonu sítě pro okruhy Azure ExpressRoute | Microsoft Docs"
description: "Konfigurace monitorování sítě založené na cloudu pro okruhy Azure ExpressRoute."
documentationcenter: na
services: expressroute
author: ajaycode
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: agummadi
ms.openlocfilehash: 36cd2f106c39f1a6bdcb6ee33b96209974a06336
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurace programu Sledování výkonu sítě pro ExpressRoute

Monitorování výkonu v síti (NPM) je síť cloudové řešení monitorování, které monitoruje připojení mezi nasazení cloudu Azure a místní umístění (firemní pobočky, atd.). NPM je součástí nástroje Microsoft Operations Management Suite (OMS). NPM teď nabízí rozšíření pro ExpressRoute, který vám umožní monitorovat výkon sítě přes okruhy ExpressRoute, které jsou nakonfigurovány pro použití soukromého partnerského vztahu. Když konfigurujete NPM pro ExpressRoute, můžete zjistit problémy se síťovým k identifikaci a odstranění.

Můžete:

* Sledovat ztrátě a latence v rámci různých virtuálních sítí a nastavit výstrahy

* Monitorovat všechny cesty (včetně redundantní cesty) v síti

* Řešení potíží přechodný a v daném okamžiku sítě, které je obtížné replikovat

* Zjistit s konkrétním segmentem v síti, která je zodpovědná za snížení výkonu

* Získat propustnosti na jednu virtuální síť (Pokud máte agenty nainstalované v každé virtuální sítě).

* Zobrazit stav systému ExpressRoute z předchozího bodu v čase

## <a name="regions"></a>Podporované oblasti

Okruhy ExpressRoute na celém světě můžete monitorovat pomocí pracovního prostoru, který je hostován v jednom z následujících oblastí:

* Západní Evropa
* Západní střed USA
* Východ USA 
* Jihovýchodní Asie 
* Východ Jižní Austrálie

>[!NOTE]
>Podpora pro monitorování okruhy ExpressRoute připojit k virtuálním sítím v cloudu Azure Government je plánovaná pro 2018 Dotaz č. 2.   
>

## <a name="workflow"></a>pracovní postup

Monitorovací agenty jsou nainstalovány na více serverech, jak místně a v Azure. Agenti vzájemně komunikovat, ale neodesílají data, odesílání paketů TCP metody handshake. Komunikace mezi agenty umožňuje Azure k mapování síťové topologie a cestu, kterou může trvat provoz.

1. Vytvořit pracovní prostor služby NPM do jedné z [podporované oblasti](#regions).
2. Instalace a konfigurace agentů softwaru: 
    * Nainstalujte monitorování agenty na místní servery a virtuální počítače Azure.
    * Nakonfigurujte nastavení na serveru agenta monitorování umožňující monitorovací agenty ke komunikaci. (Otevřít porty brány firewall, atd.)
3. Konfigurace skupiny (NSG) pravidla zabezpečení sítě umožňuje monitorování agent nainstalovaný na virtuálních počítačích Azure ke komunikaci s místní monitorování agentů.
4. Nastavení monitorování: automaticky zjistit a spravovat, které sítě jsou viditelné v NPM.

Pokud už používáte nástroj Sledování výkonu sítě k monitorování jiných objektů nebo služeb a už máte pracovní prostor v jednom z podporovaných oblastí, můžete přeskočit krok 1 a 2 krok a zahájit konfiguraci kroku 3.

## <a name="configure"></a>Krok 1: Vytvoření pracovního prostoru

Vytvořte pracovní prostor v odběru, který má propojení virtuálních sítí ExpressRoute circuit(s).

1. V [portál Azure](https://portal.azure.com), vyberte odběr, který má virtuální sítě peered pro váš okruh ExpressRoute. Potom v seznamu služeb v Hledat **Marketplace** pro sledování výkonu sítě. V vrácení, klikněte na tlačítko Otevřít **sledování výkonu sítě** stránky.

>[!NOTE]
>Můžete vytvořit nový pracovní prostor nebo použít stávající pracovní prostor.  Pokud chcete použít existujícímu pracovnímu prostoru, je nutné zajistit, že v pracovním prostoru se migroval na nový dotazovací jazyk. [Další informace...](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-log-search-upgrade)
>

  ![portál](.\media\how-to-npm\3.png)<br><br>
2. V dolní části hlavní **sledování výkonu sítě** klikněte na tlačítko **vytvořit** otevřete **sledování výkonu sítě - vytvořit nové řešení** stránky. Klikněte na tlačítko **pracovním prostorem OMS - vyberte pracovní prostor** chcete otevřít stránku pracovní prostory. Klikněte na tlačítko **+ vytvořit nový pracovní prostor** chcete otevřít stránku pracovní prostor.
3. Na **pracovním prostorem OMS** vyberte **vytvořit nový** a nakonfigurujte následující nastavení:

  * Pracovní prostor OMS – zadejte název pracovního prostoru.
  * Předplatné – Pokud máte více předplatných, vyberte ten, který chcete přidružit nový pracovní prostor.
  * Skupiny prostředků – vytvořte skupinu prostředků, nebo použijte existující.
  * Umístění –, je nutné vybrat [podporované oblasti](#regions).
  * Cenová úroveň - vyberte 'volné.
  
  >[!NOTE]
  >Okruh ExpressRoute může být kdekoliv na světě a nemusí být ve stejné oblasti jako pracovním prostoru.
  >
  
  ![Pracovní prostor](.\media\how-to-npm\4.png)<br><br>
4. Klikněte na tlačítko **OK** uložte a nasaďte nastavení šablonu. Jakmile ověří šablony, klikněte na možnost **vytvořit** k nasazení v pracovním prostoru.
5. Po nasazení pracovním prostoru, přejděte na **NetworkMonitoring(name)** prostředek, který jste vytvořili. Ověřte nastavení a pak klikněte na **řešení vyžaduje další konfiguraci**.

  ![další konfigurace](.\media\how-to-npm\5.png)

## <a name="agents"></a>Krok 2: Instalace a konfigurace agentů

### <a name="download"></a>2.1: Stáhněte si instalační soubor agenta

1. Přejděte na **společná nastavení** kartě **konfigurace monitorování výkonu sítě** stránky prostředku. Klikněte na možnost agent, který odpovídá procesor váš server od **instalovat agenty OMS** části a stáhněte si instalační soubor.

 
2. Zkopírujte **ID pracovního prostoru** a **primární klíč** do poznámkového bloku.
3. Z **konfigurace OMS agentů pro monitorování pomocí protokolu TCP** část, stáhněte skript prostředí Powershell. Skript prostředí PowerShell můžete otevřít port brány firewall pro transakce TCP.

  ![Skript PowerShellu](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: nainstalujte agenta monitorování na každém serveru monitorování (na každý virtuální sítě, který chcete monitorovat)

Doporučujeme nainstalovat aspoň dva agenty na každé straně připojení ExpressRoute (tj, místní, virtuálních sítí Azure) pro redundanci. Použijte následující postup k instalaci agentů:
  
  >[!NOTE]
  >Agent musí být nainstalován na serveru systému Windows (2008 SP1 nebo novější). Monitorování okruhy ExpressRoute pomocí operačního systému Windows Desktop a operační systém Linux není podporováno. 
  >
  >
  
  >[!NOTE]
  >SCOM agentů nemusí být schopna zjistit konzistentně, pokud jsou hostované v Azure.  Doporučujeme vám, že nepoužijete SCOM agentů ve virtuálních sítí Azure monitorovat ExpressRoute.
  >
  >

1. Spustit **instalace** instalace agenta na každém serveru, který chcete použít pro sledování ExpressRoute. Server, který použijete pro monitorování může být virtuální počítač nebo místní a musí mít přístup k Internetu. Musíte nainstalovat alespoň jeden místního agenta a jednoho agenta v každém segmentu sítě, který chcete monitorovat v Azure.
2. Na **úvodní** stránce klikněte na **Další**.
3. Na **licenční podmínky** si přečtěte licenční a pak klikněte na tlačítko **souhlasím**.
4. Na **cílovou složku** stránky, změnit nebo ponechat výchozí instalační složku a pak klikněte na tlačítko **Další**.
5. Na **možnosti instalace agenta** stránky, můžete k připojení agenta k Azure Log Analytics (OMS) nebo nástroje Operations Manager. Nebo můžete nechat volby prázdné Pokud chcete provést konfiguraci agenta později. Po provedení vybrané položky, klikněte na **Další**.

  * Pokud jste zvolili pro připojení k **Azure Log Analytics (OMS)**, vložte **ID pracovního prostoru** a **klíč pracovního prostoru** (primární klíč), že jste zkopírovali do poznámkového bloku v předchozí části. Pak klikněte na **Další**.

    ![ID a klíč](.\media\how-to-npm\8.png)
  * Pokud jste zvolili pro připojení k **nástroje Operations Manager**na **konfigurace skupiny pro správu** stránky, zadejte **název skupiny pro správu**, **serveru pro správu** a **Port serveru pro správu**. Pak klikněte na **Další**.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * Na **účet akce agenta** vyberte buď **místní systém** účtu, nebo **domény nebo místní účet počítače**. Pak klikněte na **Další**.

    ![Účet](.\media\how-to-npm\10.png)
6. Na **připraveno k instalaci** stránka, zkontrolujte vybrané možnosti a pak klikněte na tlačítko **nainstalovat**.
7. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.
8. Po dokončení se zobrazí v ovládacím panelu Microsoft Monitoring Agent. Můžete zkontrolovat konfiguraci existuje a ověřte, zda agent je připojena k provozní přehledy (OMS). Při připojení k OMS, agent zobrazí zpráva s oznámením: **Microsoft Monitoring Agent úspěšně připojil ke službě Microsoft Operations Management Suite**.

9. Prosím tento postup opakujte pro každý virtuální síť, že je potřeba sledovat.

### <a name="proxy"></a>2.3: Konfigurace nastavení proxy serveru (volitelné)

Pokud používáte webový proxy server pro přístup k Internetu, použijte následující postup ke konfiguraci nastavení proxy serveru pro službu Microsoft Monitoring Agent. Proveďte tyto kroky pro každý server. pokud máte mnoho serverů, které je nutné nakonfigurovat, může být jednodušší použít skript, který tento proces zautomatizuje. Pokud ano, najdete v části [ke konfiguraci nastavení proxy serveru pro službu Microsoft Monitoring Agent pomocí skriptu](../log-analytics/log-analytics-windows-agent.md).

Konfigurace nastavení proxy serveru pro službu Microsoft Monitoring Agent pomocí ovládacího panelu:

1. Otevřete **ovládací panely**.
2. Otevřete **Microsoft Monitoring Agent**.
3. Klikněte na kartu **Nastavení proxy serveru**.
4. Vyberte **použít proxy server** a zadejte adresu URL a číslo portu, pokud je zapotřebí. Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro přístup k proxy serveru.

  ![proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: ověření připojení agenta

Snadno můžete ověřit, zda jsou komunikaci agenty.

1. Na serveru s agent monitorování, otevřete **ovládací panely**.
2. Otevřete **agenta Microsoft Monitoring Agent**.
3. Klikněte **Azure Log Analytics (OMS)** kartě.
4. V **stav** sloupce, měli byste vidět úspěšně připojení agenta ke službě Operations Management Suite.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: otevřít porty brány firewall na serveru agenta monitorování

Pokud chcete používat protokol TCP, je třeba otevřít porty brány firewall, aby mohl komunikovat monitorovací agenty.

Můžete spustit skript prostředí PowerShell, který vytváří klíče registru, vyžaduje nástroj Sledování výkonu sítě, jakož i vytváření pravidel brány Windows Firewall povolit sledování agentů k vytvoření připojení TCP mezi sebou. Klíče registru vytvořený skript také určete, zda protokoly pro ladění a cesta k souboru protokoly protokolu. Definuje také agent TCP port používaný pro komunikaci. Hodnoty pro tyto klíče se nastaví automaticky ve skriptu, takže byste neměli měnit ručně tyto klíče.

Ve výchozím nastavení je otevřen port 8084. Poskytnutím parametru 'ČísloPortu' skript, můžete použít vlastní port. Ale pokud tak učiníte, musíte zadat stejný port pro všechny servery, na kterých spustíte skript.

>[!NOTE]
>Skript prostředí PowerShell 'EnableRules' nakonfiguruje pravidla brány Windows Firewall pouze na serveru, kde je skript spuštěn. Pokud máte síťovou bránu firewall, měli byste si ověřit, že umožňuje, aby provoz určený pro port TCP používán sledování výkonu sítě.
>
>

Na serverech agenta otevřete okno prostředí PowerShell s oprávněními správce. Spustit [EnableRules](https://aka.ms/npmpowershellscript) skript prostředí PowerShell (který jste předtím stáhli). Nepoužívejte žádné parametry.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>Krok 3: Konfigurace pravidel skupiny zabezpečení sítě

Pro sledování agenta servery, které jsou v Azure, je nutné nakonfigurovat skupinu (NSG) pravidla zabezpečení sítě chcete povolit přenosy TCP na portu používané NPM pro syntetické transakce. Výchozí port je 8084. To umožňuje monitorování agent nainstalovaný na virtuálním počítači Azure ke komunikaci s místní monitorování agenta.

Další informace o NSG najdete v tématu [skupin zabezpečení sítě](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Zajistěte, aby nainstalovali agenty (místní server agent a Azure server agenta) a spuštění skriptu prostředí PowerShell před pokračováním se tento krok.
>
>


## <a name="setupmonitor"></a>Krok 4: Konfigurace NPM pro monitorování ExpressRoute

Po dokončení předchozích sekcí, můžete nastavit monitorování.

1. Přejděte na dlaždici s přehledem monitorování výkonu síťového přechodem na **všechny prostředky** stránky a kliknutím na seznam povolených adres NPM prostoru.

  ![pracovní prostor npm](.\media\how-to-npm\npm.png)
2. Klikněte **sledování výkonu sítě** dlaždici s přehledem se zprovoznit řídicího panelu. Řídicí panel obsahuje stránku ExpressRoute, který ukazuje, že je ExpressRoute v do nenakonfigurovaného stavu. Klikněte na tlačítko **instalace funkce** otevřete stránku konfigurace sledování výkonu sítě.

  ![Instalace funkce](.\media\how-to-npm\npm2.png)
3. Na stránce konfigurace přejděte na kartu 'ExpressRoute partnerských vztahů', nachází na levé straně panelu. Klikněte na tlačítko **zjistit teď**.

  ![Zjistit](.\media\how-to-npm\13.png)
4. Po dokončení zjišťování zobrazí pravidla pro jedinečný název okruhu a název virtuální sítě. Na začátku tato pravidla jsou zakázány. Povolit pravidla a potom vyberte monitorování agentů a prahové hodnoty.

  ![pravidla](.\media\how-to-npm\14.png)
5. Po povolení pravidla a výběr hodnoty a agentů, které chcete monitorovat, je Počkejte přibližně 30 – 60 minut pro hodnoty k zahájení naplňování a **ExpressRoute monitorování** dlaždice k dispozici. Jakmile najdete v části monitorování dlaždice okruhů ExpressRoute a připojení prostředky jsou monitorovány pomocí NPM.

  ![dlaždice monitorování](.\media\how-to-npm\15.png)

## <a name="explore"></a>Krok 5: Zobrazení monitorování dlaždice

### <a name="dashboard"></a>Stránka Sledování výkonu sítě

Stránka NPM obsahuje na stránce pro ExpressRoute, který ukazuje přehled stavu okruhy ExpressRoute a partnerských vztahů.

  ![Řídicí panel](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Seznam okruhy

Chcete-li zobrazit seznam všech monitorovaných okruhy ExpressRoute, klikněte na **okruhy ExpressRoute** dlaždici. Můžete vybrat okruhu a zobrazit její stav, trend grafy pro ztráta paketů, využití šířky pásma a latence. Grafy, jsou interaktivní. Můžete vybrat vlastní časový interval pro vykreslení grafy. Přes oblast můžete přetáhnout myší na graf přiblížení a zobrazit podrobné datových bodů.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Trend ztrátě, latence a propustnosti

Šířku pásma, latence a ztráta grafy, jsou interaktivní. Můžete zvětšení všechny části tyto grafy, použití ovládacích prvků myši. Je také zobrazit šířku pásma, latence a ztráta dat pro další intervaly klepnutím **datum a čas**umístění níže na tlačítko akce v levé horní části.

![trend](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Seznam partnerských vztahů

Kliknutím na **soukromé partnerské vztahy** dlaždice na řídicím panelu zobrazí seznam všech připojení k virtuálním sítím přes soukromého partnerského vztahu. Tady můžete vybrat virtuální síťové připojení a zobrazit její stav, trend grafy pro ztráta paketů, využití šířky pásma a latence.

  ![okruh seznamu](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>Okruh topologie

Chcete-li zobrazit okruh topologie, klikněte na **topologie** dlaždici. Tím přejdete na zobrazení topologie vybrané okruhu nebo partnerský vztah. Diagram topologie poskytuje latence pro každý segment v síti a každé směrování vrstvy 3 je reprezentována uzlu diagramu. Kliknutím na směrování, zobrazí se další podrobnosti o směrování.
Můžete zvýšit úroveň viditelnosti zahrnout směrování místní přesunutím posuvníku níže **filtry**. Přesunutí jezdce doleva nebo doprava, zvýšení nebo snížení počtu směrování v grafu topologie. Latence v každém segmentu je viditelná, což umožňuje rychlejší izolace vysokou latencí segmentů ve vaší síti.

![Filtry](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Podrobné zobrazení topologie okruhu

Toto zobrazení uvádí připojení mezi sítěmi VNet.
![Podrobné topologie](.\media\how-to-npm\17.png)
