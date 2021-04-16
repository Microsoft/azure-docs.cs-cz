---
title: Azure Monitor pro poskytovatele řešení SAP | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se služby Azure monitor pro poskytovatele řešení SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 54ce9ca0ddffe074f5a343d192b4599b3449a855
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538737"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure monitor pro poskytovatele řešení SAP (Preview)

## <a name="overview"></a>Přehled  

V kontextu Azure Monitor pro řešení SAP odkazuje *Typ poskytovatele* na konkrétního *poskytovatele*. Například *SAP HANA*, který je nakonfigurován pro konkrétní součást v rámci technologie SAP na šířku, jako je SAP HANA databáze. Poskytovatel obsahuje informace o připojení pro odpovídající komponentu a pomáhá shromažďovat data telemetrie z této součásti. Jeden Azure Monitor pro prostředek řešení SAP (označovaný také jako prostředek monitorování SAP) je možné nakonfigurovat s více zprostředkovateli stejného typu nebo několika zprostředkovateli s více typy zprostředkovatelů.
   
Zákazníci si můžou zvolit konfiguraci různých typů zprostředkovatelů, které umožní shromažďování dat z odpovídající komponenty v jejich prostředí SAP. Zákazníci můžou například nakonfigurovat jednoho poskytovatele pro typ poskytovatele SAP HANA, jiného poskytovatele pro typ poskytovatele clusteru s vysokou dostupností a tak dále.  

Zákazníci si také můžou zvolit, že mají nakonfigurovat více poskytovatelů určitého typu zprostředkovatele, aby mohli znovu použít stejný prostředek sledování SAP a přidruženou spravovanou skupinu. Zjistěte více o spravované skupině prostředků. Ve verzi Public Preview jsou podporovány následující typy zprostředkovatelů:   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- Cluster s vysokou dostupností
- Operační systém

![Azure Monitor pro poskytovatele řešení SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Zákazníkům se doporučuje nakonfigurovat alespoň jednoho poskytovatele z dostupných typů poskytovatele v době nasazení prostředku sledování SAP. Když zákazníci nakonfigurují poskytovatele, zahájí shromažďování dat z odpovídající komponenty, pro kterou je poskytovatel nakonfigurovaný.   

Pokud zákazníci nekonfigurují žádné zprostředkovatele v době nasazení prostředku monitorování SAP, i když se prostředek monitorování SAP úspěšně nasadí, nebudou shromažďována žádná data telemetrie. Zákazníci mají možnost Přidat poskytovatele po nasazení prostřednictvím prostředku sledování SAP v rámci Azure Portal. Zákazníci mohou kdykoli přidat nebo odstranit poskytovatele z prostředku monitorování SAP.

## <a name="provider-type-sap-netweaver"></a>Typ poskytovatele: SAP NetWeaver

Zákazníci mohou nakonfigurovat jednoho nebo více poskytovatelů typu SAP NetWeaver pro povolení shromažďování dat z vrstvy SAP NetWeaver. Zprostředkovatel AMS NetWeaver využívá existující rozhraní [SAPControl WebService](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) k načtení příslušných informací o telemetrie.

Pro aktuální verzi níže jsou standardní předem připravené webové metody SOAP vyvolané AMS.

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

Ve verzi Public Preview můžou zákazníci očekávat, že se zprostředkovatelem SAP NetWeaver zobrazí následující data: 
- Dostupnost systému a instance
- Využití pracovních procesů
- Využití fronty
- Zařadit do fronty statistiku zámku.

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>Typ poskytovatele: SAP HANA

Zákazníci mohou nakonfigurovat jednoho nebo více poskytovatelů typu zprostředkovatele *SAP HANA* pro povolení shromažďování dat z databáze SAP HANA. Poskytovatel SAP HANA se připojuje k databázi SAP HANA přes port SQL, získává data telemetrie z databáze a přenáší je do pracovního prostoru Log Analytics v rámci předplatného zákazníka. Poskytovatel SAP HANA shromažďuje data každé 1 minuty z databáze SAP HANA.  

Ve verzi Public Preview můžou zákazníci zobrazit následující data s poskytovatelem SAP HANA: základní využití infrastruktury, SAP HANA stav hostitele, SAP HANA replikace systému a SAP HANA data telemetrie zálohování. Chcete-li nakonfigurovat poskytovatele SAP HANA, je třeba zadat IP adresu hostitele, číslo portu HANA SQL a uživatelské jméno a heslo SYSTEMDB. Zákazníkům se doporučuje nakonfigurovat SAP HANA poskytovatele na SYSTEMDB, ale více poskytovatelů můžete nakonfigurovat i pro ostatní klienty databáze.

![Azure Monitor pro poskytovatele řešení SAP – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>Typ poskytovatele: Microsoft SQL Server

Zákazníci mohou nakonfigurovat jednoho nebo více poskytovatelů typu zprostředkovatele *Microsoft SQL Server* Povolit shromažďování dat z [SQL Server na virtuálních počítačích](https://azure.microsoft.com/services/virtual-machines/sql-server/). Poskytovatel SQL Server se připojuje k Microsoft SQL Server přes port SQL, získává data telemetrie z databáze a přenáší je do pracovního prostoru Log Analytics v rámci předplatného zákazníka. SQL Server musí být nakonfigurovaná pro ověřování SQL a přihlášení SQL Server, aby se databáze SAP DB jako výchozí databáze pro poskytovatele vytvořila. Poskytovatel SQL Server shromažďuje data mezi každých 60 sekund až do každé hodiny z SQL serveru.  

Ve verzi Public Preview můžou zákazníci zobrazit následující data s poskytovatelem SQL Server: základní využití infrastruktury, hlavní příkazy SQL, nejvyšší největší tabulka, problémy zaznamenané v protokolech chyb SQL Server, blokující procesy a další.  

Chcete-li nakonfigurovat poskytovatele Microsoft SQL Server, jsou požadovány identifikátory systému SAP, IP adresa hostitele, SQL Server číslo portu a SQL Server přihlašovací jméno a heslo.

![Azure Monitor pro poskytovatele řešení SAP – SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>Typ poskytovatele: cluster s vysokou dostupností
Zákazníci můžou nakonfigurovat jednoho nebo více poskytovatelů typu clusteru s *vysokou dostupností* , aby se povolilo shromažďování dat z clusteru Pacemaker v rámci technologie SAP na šířku. Zprostředkovatel clusteru s vysokou dostupností se připojuje k Pacemaker, pomocí [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) koncového bodu, získává data telemetrie z databáze a přenáší je do pracovního prostoru Log Analytics v rámci předplatného zákazníka. Poskytovatel clusteru s vysokou dostupností shromažďuje data každých 60 sekund od Pacemaker.  

Ve verzi Public Preview můžou zákazníci zobrazit následující data s poskytovatelem clusteru s vysokou dostupností:   
 - Stav clusteru vyjádřený jako souhrn stavů uzlů a prostředků 
 - [účastníky](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor pro poskytovatele řešení SAP – cluster s vysokou dostupností](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Ke konfiguraci poskytovatele clusteru s vysokou dostupností jsou zapojené dva primární kroky:

1. Nainstalujte [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) do *každého* uzlu v clusteru Pacemaker.

   Pro instalaci ha_cluster_exporter máte dvě možnosti:
   
   - Pomocí skriptů Azure Automation nasaďte cluster s vysokou dostupností. Skripty se nainstalují [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) na každý uzel clusteru.  
   - Proveďte [ruční instalaci](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Nakonfigurujte poskytovatele clusteru s vysokou dostupností pro *každý* uzel v rámci clusteru Pacemaker.

   Pokud chcete nakonfigurovat poskytovatele clusteru s vysokou dostupností, vyžadují se tyto informace:
   
   - **Název:** Název tohoto poskytovatele. Měl by být jedinečný pro tento Azure Monitor instance řešení SAP.
   - **Koncový bod Prometheus** http \: // \<servername or ip address\> : 9664/metriky.
   - **Identifikátor SID**. Pro systémy SAP použijte protokol SAP SID. Pro jiné systémy (například clustery NFS) použijte pro cluster název se třemi znaky. Identifikátor SID musí být odlišný od jiných monitorovaných clusterů.   
   - **Název clusteru** Název clusteru, který se používá při vytváření clusteru. Název clusteru najdete ve vlastnosti clusteru `cluster-name` .
   - **Název hostitele**. Název hostitele Linux virtuálního počítače.  

## <a name="provider-type-os-linux"></a>Typ poskytovatele: operační systém (Linux)
Zákazníci můžou nakonfigurovat jednoho nebo více poskytovatelů typu operačního systému (Linux), aby povolili shromažďování dat z BareMetal nebo uzlu virtuálního počítače. Poskytovatel operačního systému (Linux) se připojuje k BareMetal nebo uzlům virtuálních počítačů pomocí [Node_Exporterho](https://github.com/prometheus/node_exporter)   koncového bodu, získává data telemetrie z uzlů a přenáší je do pracovního prostoru Log Analytics v rámci zákaznického předplatného. Poskytovatel operačního systému (Linux) shromažďuje data každých 60 sekund pro většinu metrik z uzlů. 

Ve verzi Public Preview můžou zákazníci očekávat, že se s poskytovatelem operačního systému (Linux) zobrazí následující data: 
   - Využití CPU, využití procesoru procesem 
   - Využití disku, vstupně-výstupní operace čtení & zápisu 
   - Distribuce paměti, využití paměti, prohození využití paměti 
   - Využití sítě, podrobnosti odchozího provozu síťového příchozího &. 

Ke konfiguraci poskytovatele operačního systému (Linux) jsou zapojené dva primární kroky:
1. Nainstalujte [Node_Exporter](https://github.com/prometheus/node_exporter)   na každý uzel BareMetal nebo VM.
   Pro instalaci [Node_exporter](https://github.com/prometheus/node_exporter)máte dvě možnosti: 
      - Pro automatizaci instalace pomocí Ansible použijte [Node_Exporter](https://github.com/prometheus/node_exporter) na jednotlivých uzlech BAREMETAL nebo VM k instalaci poskytovatele operačního systému (Linux).  
      - Proveďte [ruční instalaci](https://prometheus.io/docs/guides/node-exporter/).

2. Nakonfigurujte poskytovatele operačního systému (Linux) pro každou instanci uzlu BareMetal nebo virtuálního počítače ve vašem prostředí. 
   Pokud chcete nakonfigurovat poskytovatele operačního systému (Linux), vyžadují se tyto informace: 
      - Název. Název tohoto poskytovatele. Měl by být jedinečný pro tento Azure Monitor instance řešení SAP. 
      - Koncový bod exportéra uzlu Obvykle http:// <servername or ip address> : 9100/metriky 

> [!NOTE]
> 9100 je port vystavený pro Node_Exporter koncový bod.

> [!Warning]
> Zajistěte, aby export uzlů běžel po restartování uzlu. 

## <a name="next-steps"></a>Další kroky

- Projděte si [kroky připojování](./azure-monitor-sap-quickstart.md) a vytvořte první Azure monitor pro prostředek řešení SAP.
- Máte dotazy týkající se Azure Monitor pro řešení SAP? Podívejte se na část [Nejčastější dotazy](./azure-monitor-faq.md) .
