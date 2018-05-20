---
title: Skupina počítačů pomocí Azure migrovat počítač závislosti | Microsoft Docs
description: Popisuje postup vytvoření posouzení závislosti počítače pomocí služby Azure migrovat.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: a9850044266ec05cee5e32c6825609bcf969351d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Skupina počítačů pomocí mapování závislostí počítače

Tento článek popisuje, jak vytvořit skupiny počítačů pro [Azure migrovat](migrate-overview.md) assessment vizualizací závislosti počítačů. Obvykle použijete tuto metodu, když chcete posoudit skupiny virtuálních počítačů s vyšší úrovní spolehlivosti kontrola mezi závislostmi počítač, před spuštěním posouzení. Vizualizace závislostí můžete efektivně plánování migrace do Azure. Pomáhá zajistit, aby nic je ponecháno a výpadky při neočekávaném nedojde, pokud provádíte migraci na Azure. Můžete zjistit všechny konkrétní systémy, které je potřeba migrovat společně a zjistit, jestli je spuštěný systém stále obsluhuje uživatelé nebo dojít k vyřazení z provozu místo migrace. 


## <a name="prepare-machines-for-dependency-mapping"></a>Příprava počítače pro mapování závislostí
Chcete-li zobrazit závislosti počítačů, musíte stáhnout a nainstalovat agenty na každý místní počítač, který chcete vyhodnotit. Kromě toho, pokud máte počítače bez připojení k Internetu, musíte stáhnout a nainstalovat [OMS brány](../log-analytics/log-analytics-oms-gateway.md) na ně.

### <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače
1. V **přehled**, klikněte na tlačítko **spravovat** > **počítače**a vyberte požadovaný počítač.
2. V **závislosti** sloupce, klikněte na tlačítko **nainstalovat agenty**. 
3. Na **závislosti** stránky, stáhněte a nainstalujte Microsoft Monitoring Agent (MMA) a agent závislost na jednotlivé virtuální počítače, kterou chcete vyhodnotit.
4. Zkopírujte ID a klíč pracovního prostoru. Je třeba tyto při instalaci MMA v místním počítači.

### <a name="install-the-mma"></a>Instalace agenta MMA

Instalace agenta na počítači s Windows:

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **Licenční podmínky** kliknutím na **Souhlasím** přijměte licenci.
3. V **cílovou složku**, zachovat, nebo upravit výchozí instalační složku > **Další**. 
4. V **možnosti instalace agenta**, vyberte **Azure Log Analytics** > **Další**. 
5. Klikněte na tlačítko **přidat** přidat nový pracovní prostor analýzy protokolů. Vložte ID a klíč, který jste zkopírovali z portálu. Klikněte na **Další**.


Instalace agenta na počítač s Linuxem:

1. Přeneste příslušné sady (x86 nebo x64) do počítače Linux pomocí spojovací bod služby/sftp.
2. Instalaci sady pomocí argument--instalace.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Instalace agenta závislostí
1. K instalaci agenta závislost na počítači s Windows, klikněte dvakrát na instalační soubor a postupujte podle pokynů průvodce.
2. Pro instalaci agenta závislost na počítač s Linuxem nainstalujte jako kořenového adresáře pomocí následujícího příkazu:

    ```sh InstallDependencyAgent-Linux64.bin```

[Další informace](../monitoring/monitoring-service-map-configure.md#supported-operating-systems) o operačních systémech podporovaných produktem agenta závislostí. 

## <a name="create-a-group"></a>Vytvořit skupinu

1. Po instalaci agentů, přejděte na portál a klikněte na tlačítko **spravovat** > **počítače**.
2. Vyhledávání pro počítače, kam jste nainstalovali agenty.
3. **Závislosti** sloupec pro počítač by měl nyní zobrazovat jako **zobrazení závislostí**. Klikněte na sloupec zobrazení závislostí počítače.
4. Mapa závislostí pro počítač obsahuje následující podrobnosti:
    - Příchozí (klientů) a odchozí připojení TCP (servery) do nebo z počítače
        - Závislé počítače, které nemají nainstalovaného agenta MMA a závislosti jsou seskupené podle čísla portů
        - Dependenct počítače, které mají MMA a instalaci agenta závislostí se zobrazí jako samostatné pole 
    - Procesy uvnitř počítače, můžete rozšířit každého pole počítač k zobrazení procesů
    - Vlastnosti, například plně kvalifikovaný název domény, operační systém, atd. adresa MAC každého počítače, můžete kliknutím na každého pole počítače chcete zobrazit podrobnosti

 ![Zobrazení závislostí počítačů](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Kliknutím na doby trvání v popisek rozsahu čas můžete prohlédnout závislosti pro různých dobách trvání. Ve výchozím nastavení je rozsahu jednu hodinu. Můžete upravit časové rozmezí, nebo zadejte počáteční a koncová data a doby trvání.
5. Poté, co jste označený závislé počítače, které chcete seskupit, použijte kombinaci kláves Ctrl + kliknutí vyberte více počítačů na mapě, a klikněte na **skupiny počítačů**.
6. Zadejte název skupiny. Ověřte, že závislé počítače zjistí migraci Azure. 

    > [!NOTE]
    > Pokud závislé počítač není zjištěn Azure migraci, nelze ji přidat do skupiny. Přidat tyto počítače do skupiny, musíte znovu spustit proces zjišťování vpravo oboru v systému vCenter Server a ujistěte se, že je počítač rozpozná migraci Azure.  

7. Pokud chcete vytvořit posouzení pro tuto skupinu, zaškrtněte políčko Vytvořit nové vyhodnocení pro skupinu.
8. Klikněte na tlačítko **OK** uložte skupinu.

Jakmile je skupina vytvořená, se doporučuje nainstalovat agenty na všechny počítače skupiny a upřesněte skupině vizualizací závislost celé skupiny.

## <a name="next-steps"></a>Další postup

- [Zjistěte, jak](how-to-create-group-dependencies.md) upřesněte skupině vizualizací skupiny závislosti
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
