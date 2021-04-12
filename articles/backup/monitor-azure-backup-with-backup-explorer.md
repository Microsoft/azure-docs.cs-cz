---
title: Monitorování záloh pomocí Průzkumníka zálohování
description: Tento článek popisuje, jak pomocí Průzkumníka služby Backup provádět sledování záloh v reálném čase napříč trezory, předplatnými, oblastmi a klienty.
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 78d3117d363059b6a298740f7b5daa31e10751c8
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294055"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorování záloh pomocí Průzkumníka zálohování

Protože organizace zálohují více počítačů do cloudu, je stále důležitější monitorovat tyto zálohy efektivně. Nejlepším způsobem, jak začít, je použít centrální umístění pro zobrazení provozních informací napříč velkým majetku.

Průzkumník zálohování je vestavěný Azure Monitor sešit, který poskytuje Azure Backup zákazníkům toto jediné, centrální umístění. Průzkumník zálohování pomáhá sledovat provozní činnosti napříč celou záloze v Azure, zahrnující klienty, umístění, předplatná, skupiny prostředků a trezory. Průzkumník zálohování v podstatě nabízí následující možnosti:

* **Perspektiva v měřítku**: Získejte agregované zobrazení zálohovaných položek, úloh, upozornění, zásad a prostředků, které ještě nejsou nakonfigurované pro zálohování v celé nemovitosti.
* **Analýza procházení podrobností**: zobrazí podrobné informace o jednotlivých úlohách, upozorněních, zásadách a zálohách na jednom místě.
* Užitečná **rozhraní**: po identifikaci problému můžete problém vyřešit tak, že nebudete moct plynule překládat na příslušnou zálohovanou položku nebo prostředek Azure.

Tyto možnosti jsou poskytovány předem pomocí nativní integrace s Azure Resource graphem a sešity Azure Monitor.

> [!NOTE]
>
> * Průzkumník zálohování je momentálně dostupný jenom pro data virtuálních počítačů Azure.
> * Průzkumník služby Backup má být provozní řídicí panel pro zobrazení informací o zálohách za posledních 7 dní (maximum).
> * Průzkumník zálohování se v současné době nepodporuje v národních cloudech.
> * V současné době se nepodporují přizpůsobení šablony Průzkumníka zálohování.
> * Nedoporučujeme psát vlastní automatizaci do dat grafu prostředků Azure.
> * Průzkumník zálohování v současné době umožňuje monitorovat zálohy na maximum 1000 předplatných (mezi klienty).

## <a name="get-started"></a>Začínáme

K Průzkumníku zálohování se dostanete tak, že v podokně **Přehled** převedete na některý z vašich trezorů Recovery Services a vyberete odkaz **Průzkumník zálohování** .

![Rychlý odkaz na trezor](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Když vyberete odkaz, otevře se Průzkumník zálohování, který poskytuje agregované zobrazení napříč všemi trezory a předplatnými, ke kterým máte přístup. Pokud používáte účet Azure Lighthouse, můžete zobrazit data ve všech klientech, ke kterým máte přístup. Další informace najdete v části "zobrazení mezi klienty" na konci tohoto článku.

![Cílová stránka Průzkumníka zálohování](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Případy použití Průzkumníka zálohování

Průzkumník zálohování zobrazuje několik karet, přičemž každý z nich poskytuje podrobné informace o konkrétním artefaktu zálohování (například zálohovanou položku, úlohu nebo zásady). V této části najdete stručný přehled jednotlivých karet. Videa poskytují ukázkové případy použití pro každý artefakt zálohy spolu s popisy dostupných ovládacích prvků.

### <a name="the-summary-tab"></a>Karta souhrn

Karta **Souhrn** poskytuje rychlý přehled o celkovém stavu vaší nemovitosti k zálohování. Můžete například zobrazit počet chráněných položek, počet položek, pro které ochrana nebyla povolena nebo kolik úloh bylo za posledních 24 hodin úspěšné.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Karta zálohované položky

Jednotlivé zálohované položky můžete filtrovat a zobrazit podle předplatného, trezoru a dalších vlastností. Když vyberete název zálohované položky, můžete pro tuto položku otevřít podokno Azure. V tabulce můžete například všimnout, že poslední zálohování selhalo pro položku *X*. Výběrem *X* můžete otevřít podokno **zálohování** položky, kde můžete aktivovat operaci zálohování na vyžádání.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Karta úlohy

Vyberte kartu **úlohy** pro zobrazení podrobností o všech úlohách, které byly aktivovány během posledních 7 dnů. Tady můžete filtrovat podle *operace úlohy*, *stavu úlohy* a *kódu chyby* (u neúspěšných úloh).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Karta výstrahy

Výběrem karty **výstrahy** zobrazíte podrobnosti o všech výstrahách, které byly vygenerovány v trezorech za posledních 7 dnů. Výstrahy můžete filtrovat podle typu (Chyba *zálohování* nebo *obnovení*), aktuální stav (*aktivní* nebo *Vyřešeno*) a závažnost (*kritická*, *Upozornění* nebo *informace*). Můžete také vybrat odkaz pro přechod na virtuální počítač Azure a provést všechny potřebné akce.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Karta Zásady

Výběrem karty **zásady** můžete zobrazit klíčové informace o všech zásadách zálohování, které byly vytvořeny v rámci služby zálohování. Můžete zobrazit počet položek přidružených k jednotlivým zásadám spolu s rozsahem uchování a četností zálohování určenou zásadami.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Karta nepovolenou zálohu

Zálohování by mělo být povolené pro všechny počítače, které vyžadují ochranu. Pomocí Průzkumníka zálohování můžou správci zálohování rychle určit, které počítače v organizaci ještě nejsou chráněné pomocí zálohování. Chcete-li zobrazit tyto informace, vyberte kartu **zálohování není povoleno** .

V podokně **zálohování není povoleno** je zobrazena tabulka se seznamem nechráněných počítačů. Vaše organizace může přiřazovat různé značky do produkčních počítačů a testovacích počítačů nebo do počítačů, které slouží k nejrůznějším funkcím. Vzhledem k tomu, že každá třída počítačů potřebuje samostatnou zásadu zálohování, filtrování podle značek vám pomůže zobrazit informace, které jsou specifické pro každý z nich. Když vyberete název libovolného počítače, budete přesměrováni do podokna **Konfigurace zálohování** tohoto počítače, kde se můžete rozhodnout použít příslušné zásady zálohování.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportovat do aplikace Excel

Obsah libovolné tabulky nebo grafu můžete exportovat jako excelovou tabulku. Obsah se Vyexportuje tak, jak je, s použitými existujícími filtry. Chcete-li exportovat další řádky tabulky, můžete zvýšit počet řádků, které se mají zobrazit na stránce, a to pomocí rozevíracího seznamu **řádky na stránce** v horní části každé karty.

## <a name="pin-to-the-dashboard"></a>Připnout na řídicí panel

Můžete vybrat ikonu "připnout" v horní části každé tabulky nebo grafu, abyste ji mohli připnout na řídicí panel Azure Portal. Připnutí těchto informací vám pomůže vytvořit přizpůsobený řídicí panel, který zobrazí informace, které jsou pro vás nejdůležitější.

## <a name="cross-tenant-views"></a>Zobrazení mezi klienty

Pokud jste uživatelem Azure Lighthouse, který má delegovaný přístup k předplatným napříč více klientskými prostředími, můžete použít výchozí filtr předplatného. Můžete zobrazit předplatná, pro která chcete zobrazit data, a to tak, že vyberete ikonu filtr v pravém horním rohu Azure Portal. Když použijete tuto funkci, Průzkumník služby Backup agreguje informace o všech trezorech v rámci vybraných předplatných. Další informace najdete v tématu [co je Azure Lighthouse?](../lighthouse/overview.md).

## <a name="next-steps"></a>Další kroky

[Naučte se používat Azure Monitor pro získání přehledů o vašich zálohovaných datech.](./backup-azure-monitoring-use-azuremonitor.md)
