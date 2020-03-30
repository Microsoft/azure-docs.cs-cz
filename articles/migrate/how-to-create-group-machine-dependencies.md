---
title: Nastavení analýzy závislostí založené na agentovi v azure migrate server assessment
description: Tento článek popisuje, jak nastavit analýzu závislostí založenou na agentovi v Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453611"
---
# <a name="set-up-dependency-visualization"></a>Nastavení vizualizace závislostí

Tento článek popisuje, jak nastavit analýzu závislostí založenou na agentovi v Azure Migrate:Server Assessment. [Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat a pochopit závislosti napříč počítači, které chcete posoudit a migrovat do Azure.

## <a name="before-you-start"></a>Než začnete

- [Další informace o](concepts-dependency-visualization.md#agent-based-analysis) analýze závislostí založené na agentovi.
- Zkontrolujte požadavky na požadavky na požadavky na požadavky na požadavky na požadavky na podporu pro nastavení vizualizace závislostí na základě agenta pro [virtuální zařízení VMware](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), [fyzické servery](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)a [virtuální servery Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Migrace Azure.
- Pokud jste už vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) nástroj Azure Migrate:Server Assessment.
- Ujistěte se, že jste nastavili [zařízení Azure Migrate,](migrate-appliance.md) abyste zjistili vaše místní počítače. Přečtěte si, jak nastavit zařízení pro [servery VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)nebo [fyzické servery](how-to-set-up-appliance-physical.md). Zařízení zjišťuje místní počítače a odesílá metadata, údaje o výkonu do Azure Migrate:Server Assessment.
- Chcete-li použít vizualizaci závislostí, přidružíte [pracovní prostor Analýzy protokolů](../azure-monitor/platform/manage-access.md) k projektu Migrace Azure:
    - Pracovní prostor můžete připojit pouze po nastavení zařízení Azure Migrate a zjišťování počítačů v projektu Migrace Azure.
    - Ujistěte se, že máte pracovní prostor v předplatném, které obsahuje projekt Migrace Azure.
    - Pracovní prostor musí být umístěn v oblastech východní CH VUSA, Jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.
    - Pracovní prostor musí být v oblasti, ve které [je podporována mapa služeb](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
    - Nový nebo existující pracovní prostor Log Analytics můžete přidružit k projektu Migrace Azure.
    - Při prvním nastavení vizualizace závislostí pro počítač připojíte pracovní prostor. Pracovní prostor pro projekt Migrace Azure nelze změnit po jeho přidání.
    - V Log Analytics je pracovní prostor přidružený k migraci Azure označen klíčem Projektu migrace a názvem projektu.

## <a name="associate-a-workspace"></a>Přidružení pracovního prostoru

1. Po objevení počítačů pro hodnocení klikněte v **části Servery** > **Azure Migrate: Server Assessment**klikněte na **Přehled**.  
2. V **Azure Migrate: Vyhodnocení serveru**klikněte na **Základní**.
3. V **pracovním prostoru OMS**klepněte na **položku Vyžaduje konfiguraci**.

     ![Konfigurace pracovního prostoru Analýzy protokolů](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. V **části Konfigurace pracovního prostoru OMS**určete, zda chcete vytvořit nový pracovní prostor nebo použít existující pracovní prostor.
    - Můžete vybrat existující pracovní prostor ze všech pracovních prostorů v příbuzem projektu.
    - Potřebujete přístup čtenáře k pracovnímu prostoru, abyste ho přidružili.
5. Pokud vytvoříte nový pracovní prostor, vyberte pro něj umístění.

    ![Přidání nového pracovního prostoru](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače

Na každém počítači, který chcete analyzovat, nainstalujte agenty.

> [!NOTE]
> U počítačů monitorovaných nástrojem System Center Operations Manager 2012 R2 nebo novějším není nutné instalovat agenta MMA. Mapa služeb se integruje s operations managerem. [Postupujte podle](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) pokynů pro integraci.

1. V **Azure Migrate: Vyhodnocení serveru**klikněte na **Zjištěné servery**.
2. U každého počítače, který chcete analyzovat pomocí vizualizace závislostí, klikněte ve **sloupci Závislosti** na **položku Vyžaduje instalaci agenta**.
3. Na stránce **Závislosti** stáhněte agenta MMA a závislostí pro Windows nebo Linux.
4. V části **Configure MMA agent**a kopírujte ID a klíč pracovního prostoru. Potřebujete je při instalaci agenta MMA.

    ![Instalace agentů](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalace agenta MMA

Nainstalujte MMA na každý počítač se systémem Windows nebo Linux, který chcete analyzovat.

### <a name="install-mma-on-a-windows-machine"></a>Instalace MMA na počítači se systémem Windows

Instalace agenta do počítače se systémem Windows:

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **Licenční podmínky** klikněte na **Souhlasím** a přijměte licenci.
3. V **cílové složce**uchovávejte nebo upravte výchozí instalační složku > **Next**.
4. V **možnostech nastavení agenta**vyberte **Azure Log Analytics** > **Next**.
5. Kliknutím na **Přidat** přidáte nový pracovní prostor Analýzy protokolů. Vložte do ID pracovního prostoru a klíče, které jste zkopírovali z portálu. Klikněte na **Další**.

Agenta můžete nainstalovat z příkazového řádku nebo pomocí automatizované metody, například Configuration Manager nebo [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196).
- [Další informace](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) o použití těchto metod k instalaci agenta MMA.
- Agenta MMA je možné nainstalovat také pomocí tohoto [skriptu](https://go.microsoft.com/fwlink/?linkid=2104394).
- [Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) o operačních systémech Windows podporovaných mma.

### <a name="install-mma-on-a-linux-machine"></a>Instalace MMA na počítači s Linuxem

Instalace MMA na počítač i s Linuxem:

1. Přeneste příslušný balíček (x86 nebo x64) do počítače s Linuxem pomocí scp/sftp.
2. Nainstalujte balíček pomocí argumentu --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) o seznamu podpory operačních systémů Linux od MMA. 

## <a name="install-the-dependency-agent"></a>Instalace agenta závislostí

1. Chcete-li nainstalovat agenta závislostí do počítače se systémem Windows, poklepejte na instalační soubor a postupujte podle pokynů průvodce.
2. Chcete-li nainstalovat agenta závislostí do počítače s Linuxem, nainstalujte jako root pomocí následujícího příkazu:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) o tom, jak můžete pomocí skriptů nainstalovat agenta závislostí.
- [Další informace](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) o operačních systémech podporovaných agentem závislostí.


## <a name="create-a-group-using-dependency-visualization"></a>Vytvoření skupiny pomocí vizualizace závislostí

Nyní vytvořte skupinu pro posouzení. 


> [!NOTE]
> Skupiny, pro které chcete vizualizovat závislosti by nemělobsahovat více než 10 počítačů. Pokud máte více než 10 strojů, rozdělte je do menších skupin.

1. V **Azure Migrate: Vyhodnocení serveru**klikněte na **Zjištěné servery**.
2. Ve sloupci **Závislosti** klikněte na **Zobrazit závislosti** pro každý počítač, který chcete zkontrolovat.
3. Na mapě závislostí vidíte následující:
    - Příchozí (klienti) a odchozí (servery) Připojení TCP do a ze stroje.
    - Závislé počítače, které nemají nainstalovány agenty závislostí, jsou seskupeny podle čísel portů.
    - Závislé počítače s nainstalovanými agenty závislostí jsou zobrazeny jako samostatné krabice.
    - Procesy spuštěné uvnitř stroje. Rozbalte každé pole stroje a zobrazte procesy.
    - Vlastnosti stroje (včetně FQDN, operačního systému, MAC adresy). Klikněte na každé pole stroje pro zobrazení podrobností.

4. Závislosti pro různé doby trvání můžete prohlížet kliknutím na dobu trvání v popisku časového rozsahu.
    - Ve výchozím nastavení je rozsah hodinu. 
    - Můžete upravit časový rozsah nebo zadat počáteční a koncové datum a dobu trvání.
    - Časový rozsah může být až hodinu. Pokud potřebujete delší rozsah, použijte Azure Monitor k dotazu závislých dat po delší dobu.

5. Po identifikaci závislých počítačů, které chcete seskupit, vyberte pomocí kombinace kláves Ctrl+Click více počítačů na mapě a klikněte na **Seskupit počítače**.
6. Zadejte název skupiny.
7. Ověřte, že závislé počítače jsou zjištěny pomocí Migrace Azure.

    - Pokud závislý počítač není zjištěna Migrace Azure: Vyhodnocení serveru, nelze přidat do skupiny.
    - Chcete-li přidat počítač, spusťte zjišťování znovu a ověřte, zda je počítač zjištěn.

8. Pokud chcete vytvořit hodnocení pro tuto skupinu, zaškrtněte políčko pro vytvoření nového hodnocení pro skupinu.
8. Chcete-li skupinu uložit, klepněte na tlačítko **OK.**

Po vytvoření skupiny doporučujeme nainstalovat agenty na všechny počítače ve skupině a potom vizualizovat závislosti pro celou skupinu.

## <a name="query-dependency-data-in-azure-monitor"></a>Dotaz na data závislostí v Azure Monitoru

Data závislostí zachycená mapou služeb můžete dotazovat v pracovním prostoru Log Analytics přidruženém k projektu Migrace Azure. Log Analytics se používá k zápisu a spuštění dotazů protokolu Azure Monitor.

- [Přečtěte si, jak](../azure-monitor/insights/service-map.md#log-analytics-records) vyhledat data mapy služeb v Log Analytics.
- [Získejte přehled](../azure-monitor/log-query/get-started-queries.md) o zápisu dotazů protokolu v [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Spusťte dotaz na data závislostí následujícím způsobem:

1. Po instalaci agentů přejděte na portál a klepněte na tlačítko **Přehled**.
2. V **Azure Migrate: Vyhodnocení serveru**klikněte na **Přehled**. Kliknutím na šipku dolů rozbalte **položku Essentials**.
3. V **pracovním prostoru OMS**klepněte na název pracovního prostoru.
3. Na stránce pracovního prostoru Analýzy protokolů > **obecné**klepněte na **položku Protokoly**.
4. Napište dotaz a klepněte na tlačítko **Spustit**.

### <a name="sample-queries"></a>Ukázkové dotazy

Zde je několik ukázkových dotazů, které můžete použít k extrahování dat závislostí.

- Můžete upravit dotazy extrahovat upřednostňované datové body.
- [Zkontrolujte](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) úplný seznam záznamů dat závislostí.
- [Zkontrolujte](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) další ukázkové dotazy.

#### <a name="sample-review-inbound-connections"></a>Ukázka: Kontrola příchozích připojení

Zkontrolujte příchozí připojení pro sadu virtuálních připojení.

- Záznamy v tabulce pro metriky připojení (VMConnection) nepředstavují jednotlivá fyzická síťová připojení.
- Více fyzických síťových připojení je seskupeno do logického připojení.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) o tom, jak se v připojení VMConnection agregují data fyzického připojení k síti.

```
// the machines of interest
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

#### <a name="sample-summarize-sent-and-received-data"></a>Ukázka: Shrnutí odeslaných a přijatých dat

Tato ukázka shrnuje objem dat odeslaných a přijatých na příchozí připojení mezi sadou počítačů.

```
// the machines of interest
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

[Vytvořte hodnocení](how-to-create-assessment.md) pro skupinu.


