---
title: Seskupit počítače pomocí závislostí počítačů s Azure Migrate | Microsoft Docs
description: Popisuje, jak vytvořit vyhodnocení pomocí závislostí počítačů se službou Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: 96d1c44eb9ecb71684e817a89f9376a07dbe3ccb
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514965"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Nastavení Vizualizace závislostí pro posouzení

Tento článek popisuje, jak nastavit mapování závislostí v Azure Migrate: Server Assessment.

Mapování závislostí pomáhá vizualizovat závislosti mezi počítači, které chcete vyhodnotit a migrovat.

- V Azure Migrate: posouzení serveru shromažďujete počítače společně pro posouzení. Obvykle počítače, které chcete migrovat společně.
- Mapování závislostí se obvykle používá, pokud chcete vyhodnotit skupiny s vyšší úrovní spolehlivosti.
- Mapování závislostí vám umožňuje křížově kontrolovat závislosti počítačů před spuštěním posouzení a migrace.
- Mapování a vizualizace závislostí pomáhají efektivně naplánovat migraci do Azure. Pomáhá zajistit, aby nic nezůstalo na pozadí, takže během migrace se vyhne výpadkům.
- Pomocí mapování můžete zjistit vzájemně závislé systémy, které potřebují migrovat dohromady. Můžete také zjistit, zda běžící systém stále obsluhuje uživatele, nebo je kandidátem na vyřazení z provozu místo migrace.

[Přečtěte si další informace](concepts-dependency-visualization.md#how-does-it-work) o vizualizaci závislostí.

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) Azure Migrate: nástroj Server Assessment Tool.
- Ujistěte se, že jste počítače zjistili v Azure Migrate. to můžete provést nastavením zařízení Azure Migrate pro [VMware](how-to-set-up-appliance-vmware.md) nebo [Hyper-V](how-to-set-up-appliance-hyper-v.md). Zařízení zjišťuje místní počítače a odesílá data o metadatech a výkonu Azure Migrate: posouzení serveru. [Další informace](migrate-appliance.md).


**Funkce** | **Poznámka**
--- | ---
Dostupnost | Vizualizace závislostí není v Azure Government k dispozici.
Mapa služeb | Vizualizace závislostí používá Service Map řešení v Azure Monitor. [Service map](../azure-monitor/insights/service-map.md) automaticky zjišťuje a zobrazuje spojení mezi servery.
Agenti | Pokud chcete použít vizualizaci závislostí, nainstalujte na počítačích, které chcete namapovat, následující agenty:<br/> Agent [agenta - Log Analytics](../azure-monitor/platform/log-analytics-agent.md) (dříve označovaný jako Microsoft Monitoring Agent (MMA).<br/> [Agent závislostí - Service map](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent).<br/><br/> K automatizaci instalace agenta můžete použít nástroj pro nasazení, jako je například Configuration Manager, který má řešení nasazení agenta pro Azure Migrate.
Agent závislostí | Projděte si [podporu agenta závislostí](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) pro systémy Windows a Linux.<br/><br/> [Přečtěte si další informace](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) o použití skriptů k instalaci agenta závislostí.
Agent Log Analytics (MMA) | [Přečtěte si další informace](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent) o metodách instalace MMA.<br/><br/> Pro počítače monitorované nástrojem System Center Operations Manager 2012 R2 nebo novějším není nutné instalovat agenta MMA. Service Map se integruje s Operations Manager. Integraci můžete povolit pomocí pokynů [zde](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Upozorňujeme však, že na tyto počítače bude nutné nainstalovat agenta závislostí.<br/><br/> [Přečtěte si](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) operační systémy Linux podporované agentem Log Analytics.
Skupiny posouzení | Skupiny, pro které chcete vizualizovat závislosti, by neměly obsahovat více než 10 počítačů. Pokud máte více než 10 počítačů, rozdělte je do menších skupin a vizualizujte závislosti.

## <a name="associate-a-log-analytics-workspace"></a>Přidružit Log Analytics pracovní prostor

Chcete-li použít vizualizaci závislostí, je třeba přidružit [Log Analytics pracovní prostor](../azure-monitor/platform/manage-access.md) k projektu Azure Migrate.

- Pracovní prostor můžete připojit pouze v předplatném Azure Migrate projektu.
- Můžete připojit existující pracovní prostor nebo vytvořit nový.
- Pracovní prostor se připojuje při prvním nastavení vizualizace závislosti pro určitý počítač.
- Pracovní prostor můžete připojit až po zjištění počítačů v Azure Migrate projektu. To můžete provést nastavením zařízení Azure Migrate pro [VMware](how-to-set-up-appliance-vmware.md) nebo [Hyper-V](how-to-set-up-appliance-hyper-v.md). Zařízení zjišťuje místní počítače a odesílá data o metadatech a výkonu Azure Migrate: posouzení serveru. [Další informace](migrate-appliance.md).

Pracovní prostor připojte následujícím způsobem:

1. V **Azure Migrate: posouzení serveru**, klikněte na **Přehled**. Pokud jste ještě nepřidali Nástroj pro vyhodnocení serveru, [udělejte to jako první](how-to-assess.md).
2. V části **Přehled**klikněte na šipku dolů a rozbalte **základy**.
3. V **pracovním prostoru OMS**klikněte na **vyžaduje konfiguraci**.
4. V **oblasti konfigurovat pracovní prostor**určete, jestli chcete vytvořit nový pracovní prostor, nebo použijte existující:

    ![Přidat pracovní prostor](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Po zadání názvu pro nový pracovní prostor můžete zvolit [oblast](https://azure.microsoft.com/global-infrastructure/regions/) , ve které bude pracovní prostor vytvořen.
    - Když připojíte existující pracovní prostor, můžete vybrat ze všech dostupných pracovních prostorů ve stejném předplatném jako projekt migrace.
    - K pracovnímu prostoru potřebujete přístup čtenář, abyste ho mohli připojit.
    - Po připojení nelze upravit pracovní prostor přidružený k projektu.

## <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače

Stáhněte a nainstalujte agenty na každý místní počítač, který chcete vizualizovat pomocí mapování závislostí.

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Pro každý počítač, pro který chcete používat vizualizaci závislostí, klikněte na **vyžaduje instalaci agenta**.
3. Na stránce **závislosti** v počítači > **Stáhněte a nainstalujte MMA**, Stáhněte si příslušného agenta a nainstalujte ho, jak je popsáno níže.
4. V části **Stažení a instalace agenta závislostí**Stáhněte příslušný agent a nainstalujte jej, jak je popsáno níže.
5. V části **Konfigurovat agenta MMA**zkopírujte ID a klíč pracovního prostoru. Budete je potřebovat při instalaci agenta MMA.

### <a name="install-the-mma"></a>Instalace agenta MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Instalace agenta na počítač s Windows

Instalace agenta na počítač s Windows:

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **Licenční podmínky** kliknutím na **Souhlasím** přijměte licenci.
3. V části **cílová složka**ponechejte nebo upravte výchozí instalační složku > **Další**.
4. V **Možnosti nastavení agenta**vyberte **Azure Log Analytics** > **Další**.
5. Kliknutím na **Přidat** přidejte nový pracovní prostor Log Analytics. Vložte do ID a klíče pracovního prostoru, který jste zkopírovali z portálu. Klikněte na **Další**.

Agenta můžete nainstalovat z příkazového řádku nebo pomocí automatizované metody, jako je Configuration Manager nebo [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196). [Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) o použití těchto metod k instalaci agenta MMA. Agenta MMA je možné nainstalovat také pomocí tohoto [skriptu](https://go.microsoft.com/fwlink/?linkid=2104394).

[Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) o operačních systémech Windows podporovaných nástrojem MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Instalace agenta do počítače se systémem Linux

Instalace agenta na počítač se systémem Linux:

1. Přeneste příslušný balíček (x86 nebo x64) do počítače se systémem Linux pomocí spojovacího bodu služby a SFTP.
2. Nainstalujte sadu prostředků pomocí argumentu--Install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) o seznamu operačních systémů Linux, které podporuje MMA. 

### <a name="install-the-dependency-agent"></a>Instalace agenta závislostí
1. Chcete-li nainstalovat agenta závislostí na počítač se systémem Windows, dvakrát klikněte na instalační soubor a postupujte podle pokynů průvodce.
2. Chcete-li nainstalovat agenta závislostí na počítač se systémem Linux, nainstalujte jako kořenový adresář pomocí následujícího příkazu:

    ```sh InstallDependencyAgent-Linux64.bin```

[Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) o tom, jak můžete pomocí skriptů nainstalovat agenta závislostí.

[Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) o operačních systémech podporovaných agentem závislostí.


## <a name="create-a-group-using-dependency-visualization"></a>Vytvoření skupiny pomocí Vizualizace závislostí

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Ve sloupci **závislosti** klikněte na možnost **Zobrazit závislosti** pro každý počítač, který chcete zkontrolovat.
3. Na mapě závislostí vidíte následující:
    - Příchozí (klienti) a odchozí (servery) připojení TCP, k počítači a z něj.
    - Závislé počítače, které nemají nainstalované agenty závislosti, jsou seskupeny podle čísel portů.
    - Závislé počítače s nainstalovanými agenty závislostí se zobrazují jako samostatná pole.
    - Procesy běžící v počítači. Rozbalením jednotlivých polí počítače zobrazíte procesy.
    - Vlastnosti počítače (včetně plně kvalifikovaného názvu domény, operačního systému, adresy MAC) Kliknutím na jednotlivá pole počítačů zobrazíte podrobnosti.

4. Závislosti pro různá časová období můžete sledovat kliknutím na časový interval v popisku časového rozsahu. Ve výchozím nastavení je rozsah hodina. Můžete změnit časový rozsah nebo zadat počáteční a koncové datum a dobu trvání.

    > [!NOTE]
    > Časový rozsah může být až hodinu. Pokud potřebujete delší rozsah, použijte Azure Monitor k dotazování závislých dat po delší dobu.

5. Po zjištění závislých počítačů, které chcete seskupovat, můžete pomocí Ctrl + kliknutí vybrat na mapě více počítačů a kliknout na **Seskupit počítače**.
6. Zadejte název skupiny.
7. Ověřte, zda jsou závislé počítače zjištěny Azure Migrate.

    - Pokud se nezjistí závislý počítač Azure Migrate: posouzení serveru, nemůžete ho přidat do skupiny.
    - Chcete-li přidat počítač, spusťte znovu zjišťování a ověřte, zda je počítač zjištěn.

8. Pokud chcete pro tuto skupinu vytvořit vyhodnocení, zaškrtněte políčko pro vytvoření nového vyhodnocení pro skupinu.
8. Kliknutím na tlačítko **OK** skupinu uložte.

Po vytvoření skupiny doporučujeme nainstalovat agenty do všech počítačů ve skupině a potom vizualizovat závislosti pro celou skupinu.

## <a name="query-dependency-data-in-azure-monitor"></a>Dotazování na data závislostí v Azure Monitor

Data závislosti zachycená Service Map můžete dotazovat v pracovním prostoru Log Analytics přidruženém k vašemu projektu Azure Migrate. Log Analytics slouží k zápisu a spouštění dotazů protokolu Azure Monitor.

- [Naučte se](../azure-monitor/insights/service-map.md#log-analytics-records) Hledat Service map data v Log Analytics.
- [Získejte přehled](../azure-monitor/log-query/get-started-queries.md) o psaní dotazů protokolu v [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Spusťte dotaz na data závislostí následujícím způsobem:

1. Po instalaci agentů přejděte na portál a klikněte na **Přehled**.
2. V **Azure Migrate: posouzení serveru**, klikněte na **Přehled**. Kliknutím na šipku dolů rozbalte **základy**.
3. V **pracovním prostoru OMS**klikněte na název pracovního prostoru.
3. Na stránce Log Analytics pracovní prostor > **Obecné**klikněte na **protokoly**.
4. Napište dotaz a klikněte na **Spustit**.

### <a name="sample-queries"></a>Ukázkové dotazy

Poskytujeme počet ukázkových dotazů, které můžete použít k extrakci dat závislostí.

- Můžete upravit dotazy pro extrakci preferovaných datových bodů.
- [Prohlédněte si](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) úplný seznam záznamů dat závislosti.
- [Projděte si](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) další Ukázkové dotazy.

#### <a name="sample-review-inbound-connections"></a>Ukázka: Kontrola příchozích připojení

Zkontrolujte příchozí připojení pro sadu virtuálních počítačů.

- Záznamy v tabulce pro metriky připojení (VMConnection) nepředstavují jednotlivá fyzická síťová připojení.
- Několik fyzických síťových připojení se seskupí do logického připojení.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) o tom, jak se data fyzického připojení k síti agreguje v VMConnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Ukázka: sumarizace odeslaných a přijímaných dat

Tato ukázka shrnuje objem dat odesílaných a přijatých na příchozích připojeních mezi sadou počítačů.

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

[Vytvořte posouzení](how-to-create-assessment.md) pro skupinu.
