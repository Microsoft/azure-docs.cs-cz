---
title: Upravit skupinu posouzení pomocí mapování závislostí skupin ve službě Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak Upřesnit hodnocení využitím mapování závislostí skupiny ve službě Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 007f7fe95be77a2b1661cd6c82118eb875401f24
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472571"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Upřesnění skupiny s využitím mapování závislostí skupin

Tento článek popisuje, jak upřesnění skupiny s vizualizací závislostí všech počítačů ve skupině. Obvykle použijete tuto metodu, když budete chtít Upřesnit členství pro stávající skupinu, kontrola křížové skupiny závislostmi, před spuštěním posouzení. Upřesnění skupiny pomocí vizualizace závislostí můžete efektivně naplánovat migraci do Azure. Je možné vyhledat všechny vzájemně závislých systémů, které je potřeba migrovat společně. To vám pomůže zajistit, že nic se zachovají a překvapením, že není dojde k výpadku při migraci do Azure.


> [!NOTE]
> Skupiny, pro které chcete vizualizace závislostí nesmí obsahovat více než 10 počítačů. Pokud máte více než 10 počítačů ve skupině, doporučujeme ho rozdělte do menších skupin využívat funkce vizualizace závislostí.


## <a name="prepare-for-dependency-visualization"></a>Příprava pro vizualizace závislostí
Azure Migrate využívá řešení Service Map ve službě Log Analytics umožňuje vizualizace závislostí počítačů.

> [!NOTE]
> Funkce vizualizace závislostí není k dispozici ve službě Azure Government.

### <a name="associate-a-log-analytics-workspace"></a>Přiřadit pracovní prostor Log Analytics
Využití vizualizace závislostí, je potřeba přiřadit pracovní prostor Log Analytics, novou nebo existující s projekt Azure Migrate. Můžete pouze vytvářet nebo připojit pracovní prostor v rámci stejného předplatného, ve kterém se vytvoří projekt migrace.

- K připojení do pracovního prostoru Log Analytics do projektu, v **přehled**, přejděte na stránku **Essentials** části projektu klikněte na tlačítko **vyžaduje konfiguraci**

    ![Přiřadit pracovní prostor Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Při přiřazení pracovního prostoru, bude mít možnost vytvořit nový pracovní prostor nebo připojení existující:
    - Když vytvoříte nový pracovní prostor, musíte zadat název pracovního prostoru. Pracovní prostor se pak vytvoří v oblasti, ve stejném [zeměpisná oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migrace.
    - Po připojení existujícího pracovního prostoru, můžete vybrat ze všech dostupných pracovních prostorů ve stejném předplatném jako projekt migrace. Všimněte si, že jsou uvedené pouze ty pracovní prostory, které byly vytvořeny v oblasti kde [Service Map je podporována](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Aby bylo možné se připojit s pracovním prostorem, ujistěte se, že máte "Čtečky" přístup do pracovního prostoru.

> [!NOTE]
> Nelze změnit pracovní prostor, přidružené k projektu migrace.

### <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače
Chcete-li zobrazit závislosti skupiny, budete muset stáhnout a nainstalovat agenty na každém v místním počítači, který je součástí skupiny. Kromě toho, pokud máte počítače bez připojení k Internetu, musíte stáhnout a nainstalovat [Log Analytics gateway](../azure-monitor/platform/gateway.md) na ně.

1. V **přehled**, klikněte na tlačítko **spravovat** > **skupiny**, přejděte do požadované skupiny.
2. V seznamu počítačů v **agenta závislostí** sloupce, klikněte na tlačítko **vyžaduje instalaci** zobrazíte pokyny ohledně toho, jak stáhnout a nainstalovat agenty.
3. Na **závislosti** stránce, stáhněte a nainstalujte Microsoft Monitoring Agent (MMA) a agenta závislostí na každém virtuálním počítači, který je součástí skupiny.
4. Zkopírujte ID a klíč pracovního prostoru. Budete je potřebovat při instalaci agenta MMA na místních počítačích.

### <a name="install-the-mma"></a>Instalace agenta MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Nainstalujte agenta na počítači s Windows

Instalace agenta na počítači s Windows:

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **Licenční podmínky** kliknutím na **Souhlasím** přijměte licenci.
3. V **cílovou složku**, udržovat nebo změnit výchozí instalační složku > **Další**.
4. V **možnosti instalace agenta**vyberte **Azure Log Analytics** > **Další**.
5. Klikněte na tlačítko **přidat** přidáte nový pracovní prostor Log Analytics. Vložte ID pracovního prostoru a klíč, který jste zkopírovali z portálu. Klikněte na **Další**.

Agenta můžete nainstalovat z příkazového řádku nebo pomocí automatizovaného metody, jako je Azure Automation DSC, System Center Configuration Manager, nebo pomocí šablony Azure Resource Manageru, pokud jste nasadili Microsoft Azure Stack ve vašem datovém centru. [Další informace](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) o použití těchto metod k instalaci agenta MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Nainstalujte agenta na počítači s Linuxem

Instalace agenta na počítači s Linuxem:

1. Přeneste příslušný balíček (x86 nebo x64) na počítači s Linuxem pomocí příkazu scp/sftp.
2. Instalaci sady pomocí argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>Instalace agenta do počítače monitorované nástrojem System Center Operations Manager

Počítače monitorované pomocí Operations Manageru 2012 R2 nebo novější není nutné k instalaci agenta MMA. Řešení Service Map je integraci s Operations Managerem, které využívá připojení MMA Operations Manager ke shromažďování dat potřebné závislosti. Můžete povolit podle pokynů k integraci [tady](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Všimněte si však, že agenta závislostí je potřeba nainstalovat na těchto počítačích.

### <a name="install-the-dependency-agent"></a>Instalace agenta závislostí
1. Instalace agenta závislostí na počítači s Windows, klikněte dvakrát na instalační soubor a postupujte podle pokynů průvodce.
2. Pokud chcete nainstalovat agenta závislostí na počítači s Linuxem, nainstalujte jako uživatel root pomocí následujícího příkazu:

    ```sh InstallDependencyAgent-Linux64.bin```

Další informace o podpoře agenta závislostí [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) a [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) operačních systémů.

[Další informace](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) o použití skriptů k instalaci agenta závislostí.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Upřesnění skupiny založené na vizualizace závislostí
Po instalaci agentů na všech počítačích skupiny můžete vizualizace závislostí skupiny a zpřesnit jej pomocí následujícího níže uvedených pokynů.

1. Ve službě Azure Migrate projektu, v části **spravovat**, klikněte na tlačítko **skupiny**a vyberte skupinu.
2. Na stránce skupiny, klikněte na tlačítko **zobrazení závislostí**, chcete-li spustit nástroj Mapa závislostí skupiny.
3. Mapa závislostí pro skupinu zobrazí následující podrobnosti:
    - Odchozí připojení (servery) TCP do a ze všech počítačů, které jsou součástí skupiny a příchozí (klientů)
        - Závislé počítače, které nemají nainstalovaného agenta MMA a závislosti jsou seskupené podle čísla portů
        - Závislé počítače, které mají agenta MMA a instalaci agenta závislosti jsou uvedeny v samostatných polích.
    - Spuštěné procesy v rámci počítače, můžete rozbalit každé pole počítače zobrazit procesy
    - Vlastnosti, jako je plně kvalifikovaný název domény, operačního systému, atd. adresu MAC každého počítače, můžete kliknout na každé pole počítače zobrazit tyto podrobnosti

     ![Zobrazení skupinových závislostí](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Chcete-li zobrazit podrobnější závislosti, klikněte na tlačítko časový rozsah jej upravit. Ve výchozím nastavení rozsah je jedna hodina. Můžete upravit časový rozsah, nebo zadat počáteční a koncové datum a dobu trvání.

    > [!NOTE]
      Vizualizace závislostí uživatelského rozhraní v současné době nepodporuje výběr časový rozsah, který je delší než hodinu. Použití Log Analytics k [zadávat dotazy na data závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-a-group#query-dependency-data-from-log-analytics) za delší dobu.

4. Ověření závislých počítačů, proces spuštěný v každém počítači a identifikaci počítačů, které mají přidat nebo odebrat ze skupiny.
5. Vyberte počítače, na mapě, které chcete přidat nebo odebrat ze skupiny pomocí kombinace kláves Ctrl + kliknutí.
    - Mohli byste přidávat pouze počítače, které byly zjištěny.
    - Přidání a odebrání počítače ze skupiny zruší platnost po posouzení pro něj.
    - Volitelně můžete vytvořit nové posouzení při úpravě skupině.
5. Klikněte na tlačítko **OK** uložte skupinu.

    ![Přidání nebo odebrání počítačů](./media/how-to-create-group-dependencies/add-remove.png)

Pokud chcete zkontrolovat závislosti pro konkrétní počítač, který se zobrazí na mapě závislostí skupiny [nastavit mapování závislosti počítačů](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-log-analytics"></a>Dotazování na závislost data ze služby Log Analytics

Závislost data zachycená pomocí mapy služeb je k dispozici pro dotazování v Log Analytics workspare přidružený k projektu Azure Migrate. [Další informace](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) Service Map tabulky dat k dotazování v Log Analytics. 

Pokud chcete spustit dotazy Log Analytics:

1. Po instalaci agentů, přejděte na portál a klikněte na tlačítko **přehled**.
2. V **přehled**, přejděte na stránku **Essentials** části projektu a klikněte na název pracovního prostoru k dispozici vedle **pracovní prostor OMS**.
3. Na stránce pracovního prostoru Log Analytics, klikněte na tlačítko **Obecné** > **protokoly**.
4. Napsat dotaz pro shromažďování dat závislosti pomocí Log Analytics. Ukázkové dotazy pro shromažďování dat závislosti jsou k dispozici [tady](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).
5. Spusťte dotaz kliknutím na spustit. 

[Další informace](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) o tom, jak psát dotazy Log Analytics. 


## <a name="next-steps"></a>Další postup
- [Další informace](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) o nejčastějších dotazech na vizualizaci závislostí.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
