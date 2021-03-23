---
title: Nastavte analýzu závislostí založenou na agentech v Azure Migrate
description: Tento článek popisuje, jak nastavit analýzu závislostí založenou na agentech v Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 84a672f76de4b11558f2b39bf417a3eda2e31a36
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786528"
---
# <a name="set-up-dependency-visualization"></a>Nastavení Vizualizace závislostí

Tento článek popisuje, jak nastavit analýzu závislostí založenou na agentech v Azure Migrate: zjišťování a posouzení. [Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat a pochopit závislosti mezi servery, které chcete vyhodnotit a migrovat do Azure.

## <a name="before-you-start"></a>Než začnete

- Projděte si požadavky na podporu a nasazení pro analýzu závislostí na základě agenta pro:
    - [Servery v prostředí VMware](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Fyzické servery](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Servery v prostředí Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Ujistěte se, že:
    - Mít Azure Migrate projekt. Pokud to neuděláte, [vytvořte](./create-manage-projects.md) ho hned teď.
    - Ověřte, že jste [přidali](how-to-assess.md) nástroj Azure Migrate: Discovery and Assessment Tool do projektu.
    - Nastavte [zařízení Azure Migrate](migrate-appliance.md) pro zjišťování místních serverů. Zařízení zjišťuje místní servery a odesílá data o metadatech a výkonu Azure Migrate: zjišťování a posouzení. Nastavení zařízení pro:
        - [Servery v prostředí VMware](how-to-set-up-appliance-vmware.md)
        - [Servery v prostředí Hyper-V](how-to-set-up-appliance-hyper-v.md)
        - [Fyzické servery](how-to-set-up-appliance-physical.md)
- Pokud chcete použít vizualizaci závislostí, přidružte [Log Analytics pracovní prostor](../azure-monitor/logs/manage-access.md) k projektu Azure Migrate:
    - Pracovní prostor můžete připojit až po nastavení zařízení Azure Migrate a zjišťování serverů v projektu Azure Migrate.
    - Ujistěte se, že máte pracovní prostor v předplatném, které obsahuje Azure Migrate projektu.
    - Pracovní prostor se musí nacházet v oblastech Východní USA, jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.
    - Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
    - K projektu Azure Migrate můžete přidružit nový nebo existující Log Analytics pracovní prostor.
    - Pracovní prostor se připojuje při prvním nastavení vizualizace závislosti pro server. Pracovní prostor pro Azure Migrate projekt nelze po přidání změnit.
    - V Log Analytics je pracovní prostor přidružený k Azure Migrate označený klíčem projektu migrace a názvem projektu.

## <a name="associate-a-workspace"></a>Přidružení pracovního prostoru

1. Po zjištění serverů pro posouzení klikněte na **servery**  >  **Azure Migrate: zjišťování a posouzení** na **Přehled**.  
2. V **Azure Migrate: zjišťování a hodnocení** klikněte na **základy**.
3. V **pracovním prostoru OMS** klikněte na **vyžaduje konfiguraci**.

     ![Nakonfigurujete pracovní prostor služby Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. V **oblasti Konfigurace pracovního prostoru OMS** určete, jestli chcete vytvořit nový pracovní prostor, nebo použijte existující.
    - Můžete vybrat existující pracovní prostor ze všech pracovních prostorů v předplatném projektu.
    - K tomuto pracovnímu prostoru potřebujete přístup čtenářů, abyste ho mohli přidružit.
5. Pokud vytváříte nový pracovní prostor, vyberte jeho umístění.

    ![Přidat nový pracovní prostor](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače

Na každém serveru, který chcete analyzovat, nainstalujte agenty.

> [!NOTE]
> Pro servery monitorované nástrojem System Center Operations Manager 2012 R2 nebo novějším není nutné instalovat agenta MMA. Service Map se integruje s Operations Manager. [Postupujte podle](../azure-monitor/vm/service-map-scom.md#prerequisites) pokynů pro integraci.

1. V **Azure Migrate: zjišťování a hodnocení** klikněte na **zjištěné servery**.
2. U každého serveru, který chcete analyzovat pomocí vizualizace závislosti, klikněte ve sloupci **závislosti** na možnost **vyžaduje instalaci agenta**.
3. Na stránce **závislosti** Stáhněte agenta MMA a závislostí pro systém Windows nebo Linux.
4. V části **Konfigurovat agenta MMA** zkopírujte ID a klíč pracovního prostoru. Budete je potřebovat při instalaci agenta MMA.

    ![Instalace agentů](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalace agenta MMA

Nainstalujte MMA na každý server se systémem Windows nebo Linux, který chcete analyzovat.

### <a name="install-mma-on-a-windows-server"></a>Instalace MMA na Windows Server

Instalace agenta na Windows Server:

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **licenční podmínky** **kliknutím na Souhlasím** přijměte licenci.
3. V části **cílová složka** ponechejte nebo upravte výchozí instalační složku > **Další**.
4. V **Možnosti nastavení agenta** vyberte **Azure Log Analytics**  >  **Další**.
5. Kliknutím na **Přidat** přidejte nový pracovní prostor Log Analytics. Vložte do ID a klíče pracovního prostoru, který jste zkopírovali z portálu. Klikněte na **Next** (Další).

Agenta můžete nainstalovat z příkazového řádku nebo pomocí automatizované metody, jako je Configuration Manager nebo [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [Přečtěte si další informace](../azure-monitor/agents/log-analytics-agent.md#installation-options) o použití těchto metod k instalaci agenta MMA.
- Agenta MMA je možné nainstalovat také pomocí tohoto [skriptu](https://github.com/brianbar-MSFT/Install-MMA).
- [Přečtěte si další informace](../azure-monitor/agents/agents-overview.md#supported-operating-systems) o operačních systémech Windows podporovaných nástrojem MMA.

### <a name="install-mma-on-a-linux-server"></a>Instalace MMA na server Linux

Instalace MMA na server Linux:

1. Přeneste příslušný balíček (x86 nebo x64) do počítače se systémem Linux pomocí spojovacího bodu služby a SFTP.
2. Nainstalujte sadu prostředků pomocí argumentu--Install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Přečtěte si další informace](../azure-monitor/agents/agents-overview.md#supported-operating-systems) o seznamu operačních systémů Linux, které podporuje MMA. 

## <a name="install-the-dependency-agent"></a>Instalace agenta závislostí

1. Chcete-li nainstalovat agenta závislostí na Windows Server, dvakrát klikněte na instalační soubor a postupujte podle pokynů průvodce.
2. Chcete-li nainstalovat agenta závislostí na server Linux, nainstalujte jako kořenový adresář pomocí následujícího příkazu:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Přečtěte si další informace](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) o tom, jak můžete pomocí skriptů nainstalovat agenta závislostí.
- [Přečtěte si další informace](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) o operačních systémech podporovaných agentem závislostí.


## <a name="create-a-group-using-dependency-visualization"></a>Vytvoření skupiny pomocí Vizualizace závislostí

Nyní vytvořte skupinu pro posouzení. 


> [!NOTE]
> Skupiny, pro které chcete vizualizovat závislosti, by neměly obsahovat více než 10 serverů. Pokud máte více než 10 serverů, rozdělte je do menších skupin.

1. V **Azure Migrate: zjišťování a hodnocení** klikněte na **zjištěné servery**.
2. Ve sloupci **závislosti** klikněte na možnost **Zobrazit závislosti** pro každý server, který chcete zkontrolovat.
3. Na mapě závislostí vidíte následující:
    - Příchozí (klienti) a odchozí (servery) připojení TCP, k serveru a ze serveru.
    - Závislé servery, které nemají nainstalované agenty závislostí, jsou seskupeny podle čísel portů.
    - Závislé servery s nainstalovanými agenty závislostí se zobrazují jako samostatná pole.
    - Procesy běžící v rámci serveru. Rozbalte jednotlivá pole serveru a zobrazte procesy.
    - Vlastnosti serveru (včetně plně kvalifikovaného názvu domény, operačního systému, adresy MAC). Kliknutím na každé serverové pole zobrazíte podrobnosti.

4. Závislosti pro různá časová období můžete sledovat kliknutím na časový interval v popisku časového rozsahu.
    - Ve výchozím nastavení je rozsah hodina. 
    - Můžete změnit časový rozsah nebo zadat počáteční a koncové datum a dobu trvání.
    - Časový rozsah může být až hodinu. Pokud potřebujete delší rozsah, použijte Azure Monitor k dotazování závislých dat po delší dobu.

5. Po identifikaci závislých serverů, které chcete seskupovat, můžete pomocí Ctrl + kliknutí vybrat na mapě více serverů a kliknout na **Seskupit počítače**.
6. Zadejte název skupiny.
7. Ověřte, zda jsou závislé servery zjištěny Azure Migrate.

    - Pokud se nezjistí závislý Server Azure Migrate: zjišťování a posouzení, nemůžete ho přidat do skupiny.
    - Chcete-li přidat server, spusťte znovu zjišťování a ověřte, zda je Server zjištěn.

8. Pokud chcete pro tuto skupinu vytvořit vyhodnocení, zaškrtněte políčko pro vytvoření nového vyhodnocení pro skupinu.
8. Kliknutím na tlačítko **OK** skupinu uložte.

Po vytvoření skupiny doporučujeme, abyste nainstalovali agenty na všechny servery ve skupině a pak vizualizujete závislosti pro celou skupinu.

## <a name="query-dependency-data-in-azure-monitor"></a>Dotazování na data závislostí v Azure Monitor

Data závislosti zachycená Service Map můžete dotazovat v pracovním prostoru Log Analytics přidruženém k projektu Azure Migrate. Log Analytics slouží k zápisu a spouštění dotazů protokolu Azure Monitor.

- [Naučte se](../azure-monitor/vm/service-map.md#log-analytics-records) Hledat Service map data v Log Analytics.
- [Získejte přehled](../azure-monitor/logs/get-started-queries.md)  o psaní dotazů protokolu v [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Spusťte dotaz na data závislostí následujícím způsobem:

1. Po instalaci agentů přejděte na portál a klikněte na **Přehled**.
2. V **Azure Migrate: zjišťování a hodnocení** klikněte na **Přehled**. Kliknutím na šipku dolů rozbalte **základy**.
3. V **pracovním prostoru OMS** klikněte na název pracovního prostoru.
3. Na stránce Log Analytics pracovní prostor > **Obecné** klikněte na **protokoly**.
4. Napište dotaz a klikněte na **Spustit**.

### <a name="sample-queries"></a>Ukázkové dotazy

Tady je několik ukázkových dotazů, které můžete použít k extrakci dat závislostí.

- Můžete upravit dotazy pro extrakci preferovaných datových bodů.
- [Prohlédněte si](../azure-monitor/vm/service-map.md#log-analytics-records) úplný seznam záznamů dat závislosti.
- [Projděte si](../azure-monitor/vm/service-map.md#sample-log-searches) další Ukázkové dotazy.

#### <a name="sample-review-inbound-connections"></a>Ukázka: Kontrola příchozích připojení

Zkontrolujte příchozí připojení pro sadu serverů.

- Záznamy v tabulce pro metriky připojení (VMConnection) nepředstavují jednotlivá fyzická síťová připojení.
- Několik fyzických síťových připojení se seskupí do logického připojení.
- [Přečtěte si další informace](../azure-monitor/vm/service-map.md#connections) o tom, jak se data fyzického připojení k síti agreguje v VMConnection.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Ukázka: sumarizace odeslaných a přijímaných dat

Tato ukázka shrnuje objem dat odesílaných a přijatých na příchozích připojeních mezi sadou serverů.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Další kroky

[Vytvořte posouzení](how-to-create-assessment.md) pro skupinu.
