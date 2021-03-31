---
title: Správa bodů obnovení
description: Informace o tom, jak služba Azure Backup spravuje body obnovení pro virtuální počítače
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94428628"
---
# <a name="manage-recovery-points"></a>Správa bodů obnovení

Tento článek popisuje, jak funguje uchovávání pro virtuální počítače. Pokaždé, když dojde k zálohování, vytvoří se body obnovení, ze kterých se dají provádět operace obnovení.

U virtuálních počítačů je prvotní záloha úplná záloha a následné zálohy jsou přírůstkové zálohy.

## <a name="recovery-points-and-retention"></a>Body obnovení a uchovávání

### <a name="scheduled-initial-and-incremental-backup"></a>Plánované počáteční a přírůstkové zálohování

Pojďme se k virtuálnímu počítači *v1* pořizovat pomocí datového disku složeného ze čtyř bloků: Block 1, Block 2, Block 3 a Block 4. Každý blok má velikost 16 KB.

![Virtuální počítač se čtyřmi bloky](./media/manage-recovery-points/four-blocks.png)

**Krok 1 – prvotní zálohování:** Prvotní záloha je úplná záloha. Funguje jako základní hodnota, na které se aplikují následné přírůstkové zálohy. Předpokládejme, že se na zdrojovém virtuálním počítači zapsala data do bloku 1 a Block 2. Stejná data budou replikována jako D1 a D2 v úložišti Recovery Services trezoru.

![Prvotní zálohování se replikuje.](./media/manage-recovery-points/initial-backup.png)

**Krok 2 – přírůstkové zálohování 1:** Vezměte v úvahu nová data přidaná do bloku 3 virtuálního počítače. Stejná data budou replikována v dalším přírůstkovém zálohování a pouze změněný blok je uložen jako D3.  V průběhu každého kroku, i když se 1 KB bloku změní, do bodu obnovení se nahraje celý blok o 16 KB.

![První přírůstkové zálohování](./media/manage-recovery-points/first-incremental-backup.png)

**Krok 3 – přírůstkové zálohování 2:**  Nyní zvažte, že se na zdrojovém virtuálním počítači změnily data na bloku 3 a na bloku 2. Tyto změny budou replikovány při dalším přírůstkovém zálohování jako D3 a D2.

![Druhé přírůstkové zálohování](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Zálohování na vyžádání

Zálohování virtuálního počítače na vyžádání můžete spustit kdykoli po nastavení ochrany.

- Zálohování na vyžádání bude úplné zálohování, pokud se spustí před prvním naplánovaným prvotním zálohováním.
- Pokud je počáteční záloha dokončená a aktivuje se zálohování na vyžádání, jedná se o přírůstkové zálohování.
- Doba uchování bodů obnovení vytvořených pro zálohování na vyžádání je hodnota uchování, kterou určíte při aktivaci zálohy.

### <a name="storage-cost"></a>Náklady na úložiště

**Bod obnovení** vytvořený pro prvotní zálohování obsahuje všechny bloky, které mají data. Následné přírůstkové body obnovení se skládají pouze z bloků, u kterých došlo ke změně dat. Náklady na úložiště odpovídají součtu všech bloků, které jsou rozloženy napříč všemi body obnovení.

Pomocí výše uvedeného příkladu porozumíte nákladům na úložiště po jednotlivých krocích:

|Krok  |Typ zálohy  |Změněné bloky  |Typ úložiště |
|------|---------|---------|---------|
|1     |     Prvotní zálohování    | Blok 1, blok 2        |    Odpovídající bodu obnovení 1 (D1 + D2)     |
|2     |  Přírůstkové zálohování 1       |  Blok 3       |   Odpovídá bodu obnovení 1 (D1 + D2) + bod obnovení 2 (D3).      |
|3     |    Přírůstkové zálohování 2     |    Blok 2, blok 3     |   Odpovídá bodu obnovení 1 (D1 + D2) + bod obnovení 2 (D3) + bod obnovení 3 (D2 + D3).      |

### <a name="recovery-point-expiration"></a>Vypršení platnosti bodu obnovení

Každý bod obnovení má dobu uchování, jak je uvedeno v zásadách zálohování. K vyčištění dojde v pravidelných intervalech a všechny body obnovení, jejichž platnost vypršela, se vyčistí.

Po vypršení platnosti bodu obnovení bude buď odstraněn, nebo sloučen.

### <a name="case-1-initial-recovery-point-expires"></a>Případ 1: počáteční bod obnovení vyprší

Po vypršení počátečního bodu obnovení se sloučí s dalším přírůstkovým bodem obnovení. Všechny bloky dat, které jsou přepsány v přírůstkovém bodu obnovení, se odstraní a zbývající jsou sloučeny. Přírůstková záloha se pak bude nacházet jako s prvotním úplným zálohováním. Pojďme se podívat na příklad:

- *Bod obnovení 1* , který se vytvoří během prvotního zálohování, má úplnou zálohu virtuálního počítače.
- V případě vypršení platnosti *bodu obnovení 1* je dalším úplným zálohováním *bod obnovení 2* .
- Blok D1 se sloučí s *bodem obnovení 2* a D2 se odstraní, protože data v bloku 2 se přepíší v *bodu obnovení 2*. Tato změna je zachycena jako blok D2.
- Blok D1 se uchová v po sobě jdoucích bodech obnovení, a to až do chvíle, kdy došlo k žádným změnám před dalším zálohováním.

![První případ](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>Případ 2: konec platnosti mezi přírůstkovým bodem obnovení

- Pokud *bod obnovení 2* vyprší před *bodem obnovení 1*, data z *bodu obnovení 2* se sloučí s dalším dostupným bodem obnovení: *bod obnovení 3*. Proto je zablokování D3 sloučeno s *bodem obnovení 3*.
- *Bod obnovení 1* je stále úplným zálohováním s Block D1 a D2.

![Druhý případ](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>Případ 3: konec platnosti bodu obnovení na vyžádání

V tomto příkladu je naplánované spuštění zásady plánu (denního zálohování) s dobou uchování *n* dnů.  Pokud se před dalším naplánovaným zálohováním spustí zálohování na vyžádání a jeho doba uchování se zadává jako 10 dní, pak bude stále přírůstkovou zálohou. Bod obnovení (*RP1 na vyžádání*) bude vytvořen za *bodem obnovení 3* a před *bodem obnovení 4*.  Na konci dne 14 vypršela platnost bodu obnovení na vyžádání (*RP1 na vyžádání*) a bude sloučena s dalším dostupným bodem obnovení. Bloky dat, které jsou stále k dispozici na serveru, jsou sloučeny, zatímco bloky dat, které se změnily (přepsané nebo odstraněné), jsou odstraněny z bodu obnovení s prošlou platností.

![Třetí případ](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>Dopad změny zásad na body obnovení

Když se zásada upraví, použije se pro nové i existující body obnovení. Další informace najdete v tématu [vliv změny zásad na body obnovení](backup-architecture.md#impact-of-policy-change-on-recovery-points).

### <a name="impact-of-stop-protection-on-recovery-points"></a>Dopad zastavení ochrany na body obnovení

Existují dva způsoby, jak zastavit ochranu virtuálního počítače:

- **Zastavte ochranu a odstraňte zálohovaná data.** Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany virtuálního počítače a odstraní všechny body obnovení. Pokud je povolené [obnovitelné odstranění](backup-azure-security-feature-cloud.md) , Odstraněná data se uchovávají po dobu 14 dnů. U položek ve stavu obnovitelného odstranění se poplatky neúčtují. Tato data je možné obnovit během období 14 dnů. Pokud nepovolíte obnovitelné odstranění, data se ihned vyčistí a nebudete moct obnovit virtuální počítač ani použít možnost **obnovit zálohu** .
- **Zastavte ochranu a zachovejte data záloh.** Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany virtuálního počítače. Služba Azure Backup však trvale uchová body obnovení, které byly zálohovány. Abyste zachovali body obnovení v trezoru, musíte platit, abyste si ponechali body obnovení v trezoru (podrobnosti najdete [Azure Backup ceny](https://azure.microsoft.com/pricing/details/backup/) ). V případě potřeby budete moct virtuální počítač obnovit. Pokud se rozhodnete obnovit ochranu virtuálního počítače, můžete použít možnost **obnovit zálohu** . Po obnovení zálohování se pravidla uchovávání použijí na body vypršení platnosti. Zálohovaná data můžete také odstranit pomocí možnosti  **Odstranit data záloh** .

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>Dopad odstranění virtuálního počítače bez zastavení ochrany

Odstranění virtuálního počítače bez ochrany má vliv na body obnovení a jedná se o nežádoucí scénář. Před odstraněním virtuálního počítače je třeba zastavit v ideálním zálohování. Vzhledem k tomu, že prostředek neexistuje, naplánované zálohy selžou s [chybou VMNotFoundV2](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). Body obnovení se pravidelně čistí podle zásad uchovávání informací, ale poslední kopie virtuálního počítače zůstane trvale a bude se vám účtovat odpovídajícím způsobem. V závislosti na vašem scénáři máte následující dvě možnosti:

- **Možnost 1:** Obnovte virtuální počítač pomocí některého z bodů obnovení. Pokud chcete odstraněný virtuální počítač obnovit, obnovte ho pomocí stejného názvu a ve stejné skupině prostředků. Pokud obnovíte obnovený virtuální počítač na stejný trezor, existující body obnovení budou automaticky připojeny.
- **Možnost 2:** Přejít do trezoru Recovery Services a zastavte ochranu pomocí odstranit data.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>Vliv vydaných bodů obnovení pro položky v tichém odstraněném stavu

Pokud je pro trezor služby Recovery Services povoleno [obnovitelné odstranění](backup-azure-security-feature-cloud.md) , zůstane bod obnovení v tichém stavu odstraněn a nebude vyčištěn. Neúčtují se žádné poplatky, pokud je bod obnovení ve stavu nepodmíněného odstranění.

### <a name="impact-of-churn-on-backup-performance"></a>Dopad změn na výkon zálohování

Předpokládejme, že celkové úložiště virtuálního počítače je 8 TB a počet změn je 5%. Pak bude příslušné úložiště přírůstkového zálohování 5% z 8 TB, které je 0,4 TB. Vyšší změny odpovídají většímu back-endu úložiště pro následné přírůstkové zálohování. Změny ovlivní výkon zálohování. Čím vyšší je, tím pomalejší proces zálohování a zvýšíte spotřebu back-end úložiště.

Chcete-li pochopit, jak změny ovlivní výkon zálohování, podívejte se v tomto scénáři:

|Virtuální počítače  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|Počet datových disků    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|Velikost každého disku   |      4 TB   | 4 TB        |  4 TB       |
|Četnost záloh zálohovaných dat    |   A1 – 4 TB      | B1 – 1 TB; B2 – 1 TB <br> B3 – 1 TB; B4 – 1 TB  |   C1-2 TB; C4 – 2 TB      |

Výkon zálohování bude v pořadí VM2>VM3>VM1. Důvodem je, že změněná data jsou rozložena mezi různé disky. Vzhledem k tomu, že zálohování disků paralelně probíhá, VM2 zobrazí nejlepší výkon.

## <a name="next-steps"></a>Další kroky

- [Architektura Azure Backup a součásti](backup-architecture.md)
