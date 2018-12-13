---
title: Nejčastější dotazy k Azure Monitor pro virtuální počítače (preview) | Dokumentace Microsoftu
description: Azure Monitor pro virtuální počítače (preview) je řešení v Azure, která kombinuje monitorování stavu a výkonu operačního systému virtuálního počítače Azure. Automaticky zjišťuje komponenty aplikace a závislosti s jiným prostředkům a mapuje komunikace mezi nimi. Tento článek obsahuje odpovědi na nejčastější dotazy.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184385"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>Azure Monitor pro virtuální počítače (preview) – nejčastější dotazy
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Monitor pro virtuální počítače. Pokud máte nějaké další dotazy ohledně řešení, přejděte [diskusním fóru Azure](https://feedback.azure.com/forums/34192--general-feedback) a zveřejněte své dotazy. Jakmile dotazy jsou často budete vyzváni, přidáme je do tohoto článku, jsou dostupné rychlé a snadné.

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>Můžete nasadit virtuální počítače do existujícího pracovního prostoru?
Pokud vaše virtuální počítače jsou již připojeni k pracovnímu prostoru Log Analytics, vám může nadále používat tento pracovní prostor při jejich nasazení do Azure monitoru pro virtuální počítače. Pracovní prostor musí existovat v jednom z podporovaných oblastech uvedených v části "Požadavky" [nasazení Azure Monitor pro virtuální počítače (preview)](vminsights-onboard.md#prerequisites).

Během nasazování nakonfigurujeme čítače výkonu pro pracovní prostor. Tato akce způsobí, že virtuální počítače, které zobrazují data sestavy pro pracovní prostor a začněte shromažďovat informace a analýzy ve službě Azure Monitor pro virtuální počítače. V důsledku toho se zobrazí data o výkonu ze všech virtuálních počítačů, které jsou připojené k vybranému pracovnímu prostoru. Stav a mapování funkce jsou povolené jenom pro virtuální počítače, které jste zadali pro nasazení.

Další informace o výkonu, které jsou povoleny čítače, naleznete v tématu [nasazení Azure Monitor pro virtuální počítače (preview)](vminsights-onboard.md).

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>Můžete nasadit virtuální počítače na nový pracovní prostor? 
Pokud vaše virtuální počítače momentálně nejste připojení k existující pracovní prostor Log Analytics, musíte vytvořit nový pracovní prostor pro ukládání vašich dat. Můžete vytvořit jednu automaticky nakonfigurováním jednoho virtuálního počítače pro monitorování Azure pro virtuální počítače na webu Azure Portal.

Pokud se rozhodnete používat metodu založenou na skript, naleznete v tématu [nasazení Azure Monitor pro virtuální počítače (preview)](vminsights-onboard.md). 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Co mám dělat, když je můj virtuální počítač už hlásí do existujícího pracovního prostoru?
Pokud jste již shromažďování dat z vašich virtuálních počítačů, možná jste už nakonfigurovali je sestava data do existujícího pracovního prostoru Log Analytics. Za předpokladu, tento pracovní prostor je v jednom z našich podporovaných oblastí, můžete povolit Azure Monitor pro virtuální počítače předem existujícího pracovního prostoru. Aktivně pracujeme na podporu dalších oblastech.

>[!NOTE]
>Nakonfigurujeme čítače výkonu pro pracovní prostor, který ovlivňuje všechny virtuální počítače hlásit do pracovního prostoru, jestli jste se rozhodli nasadíte do služby Azure Monitor pro virtuální počítače. Další informace o konfiguraci čítačů výkonu pro pracovní prostor, najdete v části "Konfigurace čítače" [Windows a Linuxem zdroje dat výkonu do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md). Informace o čítačích nakonfigurovaná pro monitorování Azure pro virtuální počítače najdete v tématu [nasazení Azure Monitor pro virtuální počítače (preview)](vminsights-onboard.md). 

## <a name="why-did-my-vm-deployment-fail"></a>Proč moje nasazení virtuálního počítače selže?
Při nasazení virtuálního počítače Azure na webu Azure Portal, dojde k následujícím událostem:

* Výchozí pracovní prostor Log Analytics se vytvoří, pokud, který jste vybrali možnost.
* Čítače výkonu jsou nakonfigurované pro vybraný pracovní prostor. Pokud tento krok nezdaří, některé výkonu grafů a tabulek není zobrazovat data pro virtuální počítač, který jste nasadili. Tento problém můžete vyřešit spuštěním skriptu prostředí PowerShell, která je popsána v části "Povolit pomocí Powershellu" [nasazení Azure Monitor pro virtuální počítače (preview)](vminsights-onboard.md#enable-with-powershell).
* Agenta Log Analytics je nainstalován na virtuálních počítačích Azure s rozšířením virtuálního počítače, pokud je to požadováno. 
* Azure Monitor pro virtuální počítače mapu závislostí agenta je nainstalován na virtuálních počítačích Azure s příponou, pokud je to požadováno. 
* Konfigurace Azure monitorování součástí, které podporují funkci stavu, v případě potřeby a virtuální počítač je nakonfigurovaný k datům o stavu sestavy.

Během nasazení jsme zkontrolovat stav pro každý z předchozích kroků a vrátí stav oznámení na portálu. Konfigurace pracovního prostoru a instalace agenta obvykle trvá 5 až 10 minut. Data monitorování a stav zobrazení na webu Azure Portal provést další 5 až 10 minut. 

Pokud jste inicializovali nasazení a zobrazit zprávy, která udává, že virtuální počítač musí být nasazený, povolte až 30 minut, než virtuální počítač k dokončení procesu. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Se mi nezobrazují některé nebo všech dat v grafech výkonu pro virtuální počítač
Pokud v tabulce disku nebo grafy výkonu nezobrazí údaje o výkonu, čítače výkonu nemusí být nakonfigurované v pracovním prostoru. Pokud chcete tento problém vyřešit, spusťte skript Powershellu, která je popsána v části "Povolit pomocí Powershellu" [nasazení Azure Monitor pro virtuální počítače (preview)](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Čím se liší od mapy služeb monitorování Azure pro virtuální počítače mapování funkce?
Monitorování Azure pro virtuální počítače mapování funkce je založená na mapě služby, ale nemá tyto věci:

* V podokně virtuálního počítače a z Azure monitoru mají přístup zobrazení mapy pro virtuální počítače v části Azure Monitor.
* Připojení v mapě jsou nyní kliknout a zobrazení dat metrik připojení v postranní panel.
* Nové rozhraní API slouží k vytvoření mapy pro lepší podporu složitější mapy.
* Monitorované virtuální počítače jsou nyní v uzlu skupiny klienta a prstencový graf zobrazuje poměr monitorovaných pro nemonitorované virtuální počítače. Můžete také filtrovat seznam počítačů, když je skupina rozbalena.
* Monitorovaných virtuálních počítačů jsou teď v uzlech skupiny portů serveru a prstencový graf zobrazuje poměr monitorovaných pro nemonitorované počítačů. Můžete také filtrovat seznam počítačů, když je skupina rozbalena.
* Styl mapa byla aktualizována byly konzistentnější s Mapa aplikace ze služby Azure Application Insights.
* Boční panely byly aktualizovány, ale ještě nemají kompletní integrace podporované v Service Map: Update Management, Change Tracking, zabezpečení a oddělení služeb. 
* Aktualizovali jsme možnost pro výběr skupin a počítačů pro mapování. Teď podporuje předplatná, skupiny prostředků, škálovací sady virtuálních počítačů Azure a cloudových služeb.
* Nelze vytvořit nové skupiny počítačů řešení Service Map ve službě Azure Monitor pro funkci mapování virtuálních počítačů. 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Proč se Moje grafy výkonu zobrazit tečkované čáry

Grafy výkonu zobrazují tečkované čáry místo plné čáry pro z několika důvodů:
* Můžou nastat mezeru v kolekci data. 

* Ve výchozím nastavení vzorkování dat se každých 60 sekund. Pokud zvolíte úzký časový rozsah pro graf a vzorkovací frekvence je menší než velikost kbelíku používaných v grafu, může se zobrazit tečkované čáry. Řekněme, že jste se rozhodli vzorkovací frekvence 10 minut a každý blok v grafu je 5 minut. V takovém případě vyberete širší časový rozsah pro zobrazení by se měl čáry grafu se zobrazí jako čar spíše než tečky.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Skupiny podporovaných službou Azure Monitor pro virtuální počítače?
Ano, po instalaci agenta závislostí můžeme shromažďovat informace z virtuálních počítačů k zobrazení skupin na základě předplatného, skupiny prostředků, virtuálního počítače škálovací sady a cloudové služby. Pokud jste dosud používali řešení Service Map a vytvořili skupiny počítačů, zobrazí se také tyto skupiny. Pokud jste je vytvořili pro pracovní prostor, který zobrazujete, skupiny počítačů se také zobrazí ve filtru skupiny. 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Jak se dají zobrazit podrobnosti o co ovlivňuje 95. percentil řádek v agregované grafy výkonu?
Ve výchozím nastavení je seznam seřazen k zobrazení virtuálních počítačů, které mají nejvyšší hodnotu 95. percentil pro vybranou metriku. Výjimka je **dostupnou paměť** graf, který zobrazuje počítače s nejnižší hodnotou páté percentil. Vyberte graf, otevřete **N nejlepších** zobrazení s odpovídající vybranou metrikou.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>Jak funkce mapy zpracovává duplicitní IP adresy v různých virtuálních sítí a podsítí?
Pokud jste duplikování rozsahy IP adres pomocí buď virtuální počítače nebo škálovací sady virtuálních počítačů Azure mezi podsítěmi a virtuálními sítěmi, může to způsobit monitorování Azure pro virtuální počítače mapování funkce Zobrazit nesprávné informace. Jsme si tohoto problému vědomi a zkoumá možnosti na zlepšení uživatelského rozhraní.

## <a name="does-the-map-feature-support-ipv6"></a>Podporuje funkci Mapa IPv6?
Mapování funkce v současné době podporuje pouze protokol IPv4 a zkoumá podpory pro IPv6. Podporují se i IPv4, který je tunelovým propojením uvnitř IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>Když načíst mapu pro skupinu prostředků nebo další velké skupině, proč je mapa ztěžovalo prohlížení?
I když jsme vylepšili mapování funkcí pro zpracování velkých a složitých konfigurací, Uvědomujeme si, že mapa může mít mnoho uzlů, připojení a uzly fungují jako cluster. Naší prioritou je pokračováním vylepšit podporu o zvýšení škálovatelnosti.  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>Proč grafu sítě na kartě Výkon vypadat jinak z grafu sítě na stránce Přehled virtuálních počítačů Azure?

Stránka s přehledem pro virtuální počítač Azure zobrazuje grafy založené na hostitele měření aktivity ve virtuálním počítači hosta. Grafu sítě na stránce Přehled virtuálních počítačů Azure se zobrazují pouze síťový provoz, který se bude účtovat. Toto zobrazení neobsahuje provozu mezi virtuálními sítěmi. Data a grafy zobrazují pro monitorování Azure pro virtuální počítače je na základě dat z hosta virtuálního počítače a sítě grafu se zobrazují všechny TCP/IP provoz, který obsahuje vstupní a výstupní tomuto virtuálnímu počítači, včetně přenosů mezi virtuálními sítěmi.

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Jaká jsou omezení v Log Analytics zdarma cenový plán?
Pokud jste nakonfigurovali Azure Monitor s pracovním prostorem Log Analytics s využitím *Free* cenovou úroveň, monitorování Azure pro virtuální počítače mapování funkce podporuje připojení pouze pět počítačů do pracovního prostoru. 

Řekněme například, že máte pět virtuální počítače připojené k bezplatný pracovní prostor. Je-li odpojit jeden virtuální počítač a pak připojit nový nového virtuálního počítače není monitorovat a uvedených na stránce mapy. V tomto scénáři při otevření nového virtuálního počítače se zobrazí výzva k použití **vyzkoušet** možnost a vyberte **Insights (preview)** v levém podokně, i když je nainstalovaný na virtuálním počítači. Však můžete nezobrazí výzva s možností je obvykle by byla-li se virtuální počítač nasazený do Azure monitoru pro virtuální počítače. 

## <a name="next-steps"></a>Další postup
Pochopení požadavků a metody pro povolení monitorování virtuálních počítačů, najdete v tématu [nasazení Azure Monitor pro virtuální počítače (preview)](vminsights-onboard.md).
