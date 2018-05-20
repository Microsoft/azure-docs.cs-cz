---
title: Upřesnit skupinu assessment pomocí mapování skupiny závislostí v Azure migrovat | Microsoft Docs
description: Popisuje, jak k zpřesnění posouzení pomocí mapování závislostí skupin ve službě Azure migrovat.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: 690231b938f60f16dcd10656b9a1f99c8c8f29cb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Upřesnit skupiny pomocí mapování skupin závislostí

Tento článek popisuje, jak k zpřesnění skupinu vizualizací závislosti všech počítačů ve skupině. Obvykle použijete tuto metodu, pokud chcete upřesnit členství pro stávající skupinu, kontrola mezi skupiny závislostmi, před spuštěním posouzení. Upřesnění skupinu pomocí vizualizace závislostí mohou usnadní vám to efektivně naplánovat migraci na Azure.You můžete zjistit všechny konkrétní systémy, které je potřeba migrovat společně. Pomáhá zajistit, aby nic je ponecháno a výpadky při neočekávaném nedojde, pokud provádíte migraci na Azure. 


> [!NOTE]
> Skupiny, pro které chcete vizualizovat závislosti nesmí obsahovat více než 10 počítačů. Pokud máte více než 10 počítačů ve skupině, doporučujeme rozdělit na menší skupiny využít funkci vizualizace závislostí.


# <a name="prepare-the-group-for-dependency-visualization"></a>Příprava skupiny pro vizualizaci závislostí
Chcete-li zobrazit závislosti skupiny, musíte stáhnout a nainstalovat agenty na každý na místním počítači, který je součástí skupiny. Kromě toho, pokud máte počítače bez připojení k Internetu, musíte stáhnout a nainstalovat [OMS brány](../log-analytics/log-analytics-oms-gateway.md) na ně.

### <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače
1. V **přehled**, klikněte na tlačítko **spravovat** > **skupiny**, přejděte na požadovanou skupinu.
2. V seznamu počítačů v **agent služby Dependency** sloupce, klikněte na tlačítko **vyžaduje instalaci** zobrazíte pokyny o tom, jak stáhnout a nainstalovat agenty.
3. Na **závislosti** stránky, stáhněte a nainstalujte Microsoft Monitoring Agent (MMA) a agent závislost na každý virtuální počítač, který je součástí skupiny.
4. Zkopírujte ID a klíč pracovního prostoru. Je třeba tyto při instalaci MMA na místní počítače.

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

## <a name="refine-the-group-based-on-dependency-visualization"></a>Upřesnit skupiny založené na vizualizace závislostí
Po instalaci agentů na všech počítačích skupiny, můžete vizualizovat závislosti skupiny a upřesnit pomocí následujících níže uvedených pokynů.

1. Ve službě Azure migrovat projektu, v části **spravovat**, klikněte na tlačítko **skupiny**a vyberte skupinu.
2. Na stránce skupiny klikněte na **zobrazení závislostí**, chcete-li otevřít mapu závislostí skupiny.
3. Mapa závislostí pro skupinu obsahuje následující podrobnosti:
    - Příchozí (klientů) a odchozí připojení TCP (servery) do nebo ze všech počítačů, které jsou součástí skupiny
        - Závislé počítače, které nemají nainstalovaného agenta MMA a závislosti jsou seskupené podle čísla portů
        - Dependenct počítače, které mají MMA a instalaci agenta závislostí se zobrazí jako samostatné pole 
    - Procesy uvnitř počítače, můžete rozšířit každého pole počítač k zobrazení procesů
    - Vlastnosti, například plně kvalifikovaný název domény, operační systém, atd. adresa MAC každého počítače, můžete kliknutím na každého pole počítače chcete zobrazit podrobnosti

     ![Zobrazení skupinových závislostí](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Chcete-li zobrazit podrobnější závislosti, klikněte na tlačítko časový rozsah a upravit ho. Ve výchozím nastavení rozsahu představuje jednu hodinu. Můžete upravit časové rozmezí, nebo zadejte počáteční a koncová data a doby trvání.
4. Ověření závislé počítačů, procesů běžících v rámci každý počítač a identifikovat počítače, které by měla být přidat nebo odebrat ze skupiny.
5. Pomocí kombinace kláves Ctrl + kliknutí vyberte počítače, na mapě přidat nebo je odeberte ze skupiny.
    - Můžete přidat pouze počítače, které byly zjištěny.
    - Přidávání a odebírání počítače ze skupiny zruší platnost po vyhodnocení pro ni.
    - Volitelně můžete vytvořit nové vyhodnocení po úpravě skupiny.
5. Klikněte na tlačítko **OK** uložte skupinu.

    ![Přidání nebo odebrání počítačů](./media/how-to-create-group-dependencies/add-remove.png)

Pokud chcete zkontrolovat závislosti konkrétní počítač, který se zobrazí v mapa závislostí skupiny [nastavit mapování závislostí počítač](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Další postup

[Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
