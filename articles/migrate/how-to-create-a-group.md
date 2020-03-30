---
title: Seskupte počítače pro hodnocení pomocí migrace Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak seskupit počítače před spuštěním hodnocení se službou Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301669"
---
# <a name="create-a-group-for-assessment"></a>Vytvoření skupiny pro posouzení

Tento článek popisuje, jak vytvořit skupiny počítačů pro hodnocení pomocí Migrace Azure: Vyhodnocení serveru.

[Migrace Azure](migrate-services-overview.md) vám pomůže migrovat do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje Azure pro hodnocení a migraci, stejně jako nabídky nezávislého dodavatele softwaru (ISV) třetích stran. 

## <a name="grouping-machines"></a>Seskupovací stroje

Shromažďujete počítače do skupin, abyste vyhodnotili, jestli jsou vhodné pro migraci do Azure, a získejte pro ně odhady velikosti A nákladů Azure. Skupiny můžete vytvořit několika způsoby:

- Pokud znáte počítače, které je potřeba migrovat společně, můžete ručně vytvořit skupinu v Azure Migrate.
- Pokud si nejste jisti, o počítačích, které je třeba seskupit, můžete použít funkce vizualizace závislostí v Azure Migrate k vytvoření skupin. 

> [!NOTE]
> Funkce vizualizace závislostí není v Azure Government dostupná.

## <a name="create-a-group-manually"></a>Ruční vytvoření skupiny

Skupinu můžete vytvořit současně s [vytvořením hodnocení](how-to-create-assessment.md).

Pokud chcete vytvořit skupinu ručně mimo vytvoření hodnocení, postupujte takto:

1. V přehledu projektu Migrace Azure **>** klikněte na Posoudit **a migrovat servery**. V **Azure Migrate: Vyhodnocení serveru**klikněte na **Skupiny**
    - Pokud jste ještě nepřidali nástroj Azure Migrate: Server Assessment, klikněte na něj a přidejte ho. [Další informace](how-to-assess.md).
    - Pokud jste ještě nevytvořili projekt Migrace Azure, [přečtěte si další informace](how-to-add-tool-first-time.md).

    ![Vybrat skupiny](./media/how-to-create-a-group/select-groups.png)

2. Klikněte na ikonu **Skupiny.**
3. Ve **skupině Create**zadejte název skupiny a v poli Název **zařízení**vyberte zařízení Azure Migrate, které používáte pro zjišťování počítače.
1. Ze seznamu strojů vyberte počítače, které chcete přidat do skupiny > **Vytvořit**.

    ![Vytvoření skupiny](./media/how-to-create-a-group/create-group.png)

Tuto skupinu nyní můžete použít při [vytváření hodnocení](how-to-create-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Upřesnění skupiny pomocí mapování závislostí

Mapování závislostí vám pomůže vizualizovat závislosti napříč počítači. Mapování závislostí se obvykle používá, pokud chcete posoudit skupiny strojů s vyšší úrovní spolehlivosti.
- Pomáhá vám před spuštěním hodnocení křížově kontrolovat závislosti počítače. 
- Pomáhá také efektivně plánovat migraci do Azure tím, že zajišťuje, že nic nezůstane pozadu, a tím se zabrání překvapivým výpadkům během migrace.
- Můžete zjistit vzájemně závislé systémy, které je třeba migrovat společně, a zjistit, zda spuštěný systém stále obsluhuje uživatele nebo zda je kandidátem na vyřazení z provozu namísto migrace.

Pokud jste již [nastavili mapování závislostí](how-to-create-group-machine-dependencies.md)a chcete zpřesnit existující skupinu, postupujte takto:

1. Na kartě **Servery** klikněte na dlaždici **Migrace: Vyhodnocení serveru** na **položku Skupiny**.
2. Klikněte na skupinu, kterou chcete upřesnit.
    - Pokud jste ještě nenastavili mapování závislostí, ve **sloupci Závislosti** se zobrazí stav **Vyžaduje instalaci.** U každého virtuálního virtuálního aplikace, pro který chcete vizualizovat závislosti, klikněte na **položku Vyžaduje instalaci**. Nainstalujte několik agentů na každý virtuální počítač, než budete moct mapovat závislosti počítače. [Další informace](how-to-create-group-machine-dependencies.md).

        ![Přidání mapování závislostí](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Pokud jste už mapování závislostí nastavili, klikněte na stránce **skupiny** na Zobrazit závislosti a otevřete mapu závislostí skupiny.

3. Po klepnutí na tlačítko **Zobrazit závislosti**se na mapě závislostí skupin zobrazí následující:

    - Příchozí (klienti) a odchozí (servery) TCP připojení a ze všech počítačů ve skupině, které mají nainstalovány agenty závislostí.
    - Závislé počítače, které nemají nainstalovány agenty závislostí, jsou seskupeny podle čísel portů.
    - Závislé počítače s nainstalovanými agenty závislostí jsou zobrazeny jako samostatné krabice.
    - Procesy spuštěné uvnitř stroje. Rozbalte každé pole stroje a zobrazte procesy.
    - Vlastnosti stroje (včetně FQDN, operačního systému, MAC adresy). Klikněte na každé pole stroje pro zobrazení podrobností.

4. Chcete-li zobrazit závislosti v časovém intervalu podle vašeho výběru, upravte časový rozsah (ve výchozím nastavení hodinu) zadáním počátečního a koncového data nebo doby trvání.

    > [!NOTE]
    > Časový rozsah může být až hodinu. Pokud potřebujete delší rozsah, použijte [Azure Monitor k dotazu závislých dat](how-to-create-group-machine-dependencies.md) po delší dobu.

5. Po identifikaci závislostí, které chcete přidat do skupiny nebo odebrat ze skupiny, můžete skupinu upravit. Pomocí kombinace kláves Ctrl+Click můžete přidat nebo odebrat počítače ze skupiny.

    - Můžete přidat pouze počítače, které byly objeveny.
    - Přidání a odebrání počítačů zruší platnost předchozích hodnocení pro skupinu.
    - Volitelně můžete vytvořit nové hodnocení při úpravě skupiny.


## <a name="next-steps"></a>Další kroky

Zjistěte, jak nastavit a použít [mapování závislostí](how-to-create-group-machine-dependencies.md) k vytvoření skupin s vysokou spolehlivostí.

