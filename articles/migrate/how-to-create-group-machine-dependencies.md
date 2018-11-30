---
title: Seskupit počítače s využitím závislostí počítačů pomocí služby Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak vytvořit posouzení závislostí počítačů pomocí služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/21/2018
ms.author: raynew
ms.openlocfilehash: 2755cc4e8e0e5a1b2a0e491b00fc73530dd9b958
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635675"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Seskupení počítačů s využitím mapování závislostí počítačů

Tento článek popisuje, jak vytvořit skupinu počítačů pro [Azure Migrate](migrate-overview.md) posouzení vizualizací závislostí počítačů. Obvykle použijete tuto metodu, pokud chcete posouzení skupiny virtuálních počítačů s vyšší úrovní spolehlivosti mezi Kontrola závislostí počítačů, před spuštěním posouzení. Vizualizace závislostí můžete efektivně naplánovat migraci do Azure. To vám pomůže zajistit, že nic se zachovají a překvapením, že není dojde k výpadku při migraci do Azure. Je možné vyhledat všechny vzájemně závislých systémů, které je potřeba migrovat společně a zjistit, jestli spuštěný systém stále poskytuje uživatelům nebo je kandidátem pro vyřazení z provozu místo migrace.


## <a name="prepare-for-dependency-visualization"></a>Příprava pro vizualizace závislostí
Azure Migrate využívá řešení Service Map ve službě Log Analytics umožňuje vizualizace závislostí počítačů.

### <a name="associate-a-log-analytics-workspace"></a>Přiřadit pracovní prostor Log Analytics
Využití vizualizace závislostí, je potřeba přiřadit pracovní prostor Log Analytics, novou nebo existující s projekt Azure Migrate. Můžete pouze vytvářet nebo připojit pracovní prostor v rámci stejného předplatného, ve kterém se vytvoří projekt migrace.

- K připojení do pracovního prostoru Log Analytics do projektu, v **přehled**, přejděte na stránku **Essentials** části projektu klikněte na tlačítko **vyžaduje konfiguraci**

    ![Přiřadit pracovní prostor Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Když vytvoříte nový pracovní prostor, musíte zadat název pracovního prostoru. Pracovní prostor se pak vytvoří ve stejném předplatném jako projekt migrace a v oblasti, ve stejném [zeměpisná oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migrace.
- **Použít existující** možnost uvádí pouze tyto pracovní prostory, které jsou vytvořené v oblastech, kde je k dispozici řešení Service Map. Pokud máte pracovní prostor v oblasti, kde není k dispozici řešení Service Map, nebudou uvedené v rozevíracím seznamu.

> [!NOTE]
> Nelze změnit pracovní prostor, přidružené k projektu migrace.

### <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače
Po konfiguraci pracovního prostoru, budete muset stáhnout a nainstalovat agenty na každém v místním počítači, který chcete vyhodnotit. Kromě toho, pokud máte počítače bez připojení k Internetu, musíte stáhnout a nainstalovat [Log Analytics gateway](../azure-monitor/platform/gateway.md) na ně.

1. V **přehled**, klikněte na tlačítko **spravovat** > **počítače**a vyberte požadovaný počítač.
2. V **závislosti** sloupce, klikněte na tlačítko **instalovat agenty**.
3. Na **závislosti** stránce, stáhněte a nainstalujte Microsoft Monitoring Agent (MMA) a agenta závislostí na každém virtuálním počítači, na kterou chcete vyhodnotit.
4. Zkopírujte ID a klíč pracovního prostoru. Budete je potřebovat při instalaci agenta MMA na místním počítači.

> [!NOTE]
> K automatizaci instalace agentů můžete použít libovolný nástroj nasazení, jako je System Center Configuration Manager nebo použijte nástroj našich partnerů [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), který obsahuje řešení nasazení agenta ke službě Azure Migrate.

### <a name="install-the-mma"></a>Instalace agenta MMA

Instalace agenta na počítači s Windows:

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **Licenční podmínky** kliknutím na **Souhlasím** přijměte licenci.
3. V **cílovou složku**, udržovat nebo změnit výchozí instalační složku > **Další**.
4. V **možnosti instalace agenta**vyberte **Azure Log Analytics** > **Další**.
5. Klikněte na tlačítko **přidat** přidáte nový pracovní prostor Log Analytics. Vložte ID pracovního prostoru a klíč, který jste zkopírovali z portálu. Klikněte na **Další**.

[Další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) o seznam operačních systémů Windows nepodporuje agenta MMA.

Instalace agenta na počítači s Linuxem:

1. Přeneste příslušný balíček (x86 nebo x64) na počítači s Linuxem pomocí příkazu scp/sftp.
2. Instalaci sady pomocí argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) o seznam operačních systémů Linux nepodporuje agenta MMA.

### <a name="install-the-dependency-agent"></a>Instalace agenta závislostí
1. Instalace agenta závislostí na počítači s Windows, klikněte dvakrát na instalační soubor a postupujte podle pokynů průvodce.
2. Pokud chcete nainstalovat agenta závislostí na počítači s Linuxem, nainstalujte jako uživatel root pomocí následujícího příkazu:

    ```sh InstallDependencyAgent-Linux64.bin```

Další informace o podpoře agenta závislostí [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) a [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) operačních systémů.

[Další informace](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) o použití skriptů k instalaci agenta závislostí.

## <a name="create-a-group"></a>Vytvoření skupiny

1. Po instalaci agentů, přejděte na portál a klikněte na tlačítko **spravovat** > **počítače**.
2. Hledání počítače, kam jste nainstalovali agenty.
3. **Závislosti** sloupec pro počítač by měl nyní zobrazit jako **zobrazit závislosti**. Klikněte na sloupec a zobrazit závislosti počítače.
4. Mapa závislostí pro počítač se zobrazí následující podrobnosti:
    - Odchozí připojení (servery) TCP do/z počítače a příchozí (klientů)
        - Závislé počítače, které nemají nainstalovaného agenta MMA a závislosti jsou seskupené podle čísla portů
        - Závislé počítače, které mají agenta MMA a instalaci agenta závislosti jsou uvedeny v samostatných polích.
    - Spuštěné procesy v rámci počítače, můžete rozbalit každé pole počítače zobrazit procesy
    - Vlastnosti, jako je plně kvalifikovaný název domény, operačního systému, atd. adresu MAC každého počítače, můžete kliknout na každé pole počítače zobrazit tyto podrobnosti

 ![Zobrazení závislostí počítačů](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Po kliknutí na dobu trvání rozsah popisku čas můžete prohlédnout závislosti pro různé dob trvání. Rozsah ve výchozím nastavení je jedna hodina. Můžete upravit časový rozsah, nebo zadat počáteční a koncové datum a dobu trvání.
5. Po identifikaci závislých počítačů, které chcete seskupit dohromady pomocí Ctrl + kliknutí výběr více počítačů na mapě, a klikněte na **skupiny počítačů**.
6. Zadejte název skupiny. Ověřte, že se službou Azure Migrate zjistí závislých počítačů.

    > [!NOTE]
    > Pokud se službou Azure Migrate nenašel se závislý počítač, nelze jej přidat do skupiny. Tyto počítače přidat do skupiny, budete muset spustit proces zjišťování znovu s správný obor v systému vCenter Server a ujistěte se, že na počítači zjišťuje službou Azure Migrate.  

7. Pokud chcete vytvořit posouzení pro tuto skupinu, vyberte zaškrtávací políčko, chcete-li vytvořit nové posouzení pro skupinu.
8. Klikněte na tlačítko **OK** uložte skupinu.

Jakmile se vytvoří skupina, se doporučuje nainstalovat agenty na všech počítačích skupině a skupinu Upřesnit vizualizací závislostí celou skupinu.

## <a name="next-steps"></a>Další postup

- [Další informace](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) o nejčastějších dotazech na vizualizaci závislostí.
- [Zjistěte, jak](how-to-create-group-dependencies.md) pro upřesnění skupiny pomocí vizualizace závislostí skupin.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
