---
title: Sledování záloh pomocí Průzkumníka zálohování
description: Tento článek popisuje, jak pomocí Průzkumníka zálohování provádět monitorování záloh v reálném čase napříč trezory, předplatnými, oblastmi a tenanty.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131810"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Sledování záloh pomocí Průzkumníka zálohování

Jak organizace zálohovat více a více počítačů do cloudu, je stále důležitější sledovat tyto zálohy efektivně. Nejlepší způsob, jak začít, je použít centrální umístění pro prohlížení provozních informací na velké nemovitosti.

Průzkumník zálohování je integrovaný sešit Azure Monitor, který zákazníkům azure backup poskytuje toto jediné centrální umístění. Průzkumník zálohování vám pomůže monitorovat provozní aktivity v celém kombi Backup v Azure, zahrnující klienty, umístění, předplatná, skupiny prostředků a trezory. Průzkumník zálohování obecně poskytuje následující funkce:

* **Perspektiva ve velkém měřítku**: Získejte agregované zobrazení položek zálohování, úloh, výstrah, zásad a prostředků, které ještě nejsou nakonfigurovány pro zálohování v celém kombi. 
* **Analýza podrobností**: Zobrazí podrobné informace o jednotlivých úlohách, výstrahách, zásadách a položkách zálohování na jednom místě.
* **Použitelné rozhraní**: Po identifikaci problému, můžete vyřešit bezproblémově přechodem na příslušnou položku zálohování nebo prostředek Azure.

Tyto funkce jsou poskytované out-of-box nativní integrace s Azure Resource Graph a Azure Monitor sešity.

> [!NOTE]
> * Průzkumník zálohování je momentálně dostupný jenom pro data virtuálních počítačů Azure.
> * Průzkumník zálohování má být provozní řídicí panel pro zobrazení informací o zálohách za posledních 7 dní (maximálně).
> * Průzkumník zálohování není aktuálně podporován v národních cloudech.
> * V současné době není přizpůsobení šablony Průzkumníka zálohování podporováno. 
> * Nedoporučujeme psaní vlastní automatizace na data Azure Resource Graph.

## <a name="get-started"></a>Začínáme

K Průzkumníku zálohování můžete přistupovat tak, že přejdete do některého z trezorů služby Recovery Services a v podokně **Přehled** vyberete odkaz **Průzkumník zálohování.**

![Rychlý odkaz trezoru](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Výběrem odkazu otevřete Průzkumník a disponitovu, která poskytuje agregované zobrazení ve všech trezorech a předplatných, ke kterým máte přístup. Pokud používáte účet Azure Lighthouse, můžete zobrazit data napříč všemi tenanty, ke kterým máte přístup. Další informace naleznete v části "Zobrazení mezi tenanty" na konci tohoto článku.

![Vstupní stránka Průzkumníka zálohování](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Případy použití průzkumníka zálohování

Průzkumník zálohování zobrazuje více karet, z nichž každá poskytuje podrobné informace o konkrétním artefaktu zálohování (například položka zálohování, úloha nebo zásady). Tato část obsahuje stručný přehled jednotlivých karet. Videa poskytují ukázkové případy použití pro každý artefakt zálohování spolu s popisem dostupných ovládacích prvků.

### <a name="the-summary-tab"></a>Karta Souhrn

Karta **Souhrn** poskytuje rychlý přehled o celkovém stavu zálohového kombi. Můžete například zobrazit počet chráněných položek, počet položek, pro které nebyla ochrana povolena, nebo počet úspěšných úloh za posledních 24 hodin.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Karta Zálohovat položky

Každou položku zálohy můžete filtrovat a zobrazit podle předplatného, trezoru a dalších charakteristik. Výběrem názvu položky zálohy můžete otevřít podokno Azure pro tuto položku. Například z tabulky můžete pozorovat, že poslední záloha se nezdařila pro položku *X*. Výběrem možnosti *X*můžete otevřít podokno **Zálohování** položky, ve kterém můžete spustit operaci zálohování na vyžádání.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Karta Úlohy

Výběrem karty **Úlohy** zobrazíte podrobnosti o všech úlohách, které byly spuštěny za posledních 7 dní. Zde můžete filtrovat podle *operace projektu*, *stavu úlohy*a *kódu chyby* (pro neúspěšné úlohy).


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Karta Výstrahy

Výběrem karty **Výstrahy** zobrazíte podrobnosti o všech výstrahách, které byly vygenerovány ve vašich trezorech za posledních 7 dní. Výstrahy můžete filtrovat podle typu (*Selhání zálohování* nebo *Selhání obnovení),* aktuálního stavu *(Aktivní* nebo *Vyřešeno)* a závažnosti (*Kritické*, *Upozornění*nebo *Informace*). Můžete také vybrat odkaz pro připojení virtuálního počítače Azure a provést všechny nezbytné akce.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Karta Zásady

Můžete vybrat kartu **Zásady** a zobrazit klíčové informace o všech zásadách zálohování, které byly vytvořeny v celém zálohovacím kombi. Můžete zobrazit počet položek přidružených ke každé zásadě spolu s rozsahem uchování a frekvencí zálohování určenou zásadou.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Karta Zálohování není povolena

Zálohování by mělo být povoleno pro všechny počítače, které vyžadují ochranu. Pomocí Průzkumníka zálohování mohou správci záloh rychle určit, které počítače v organizaci ještě nejsou zálohovány. Chcete-li tyto informace zobrazit, vyberte kartu **Zálohování není povoleno.**

Podokno **Zálohování není povoleno** zobrazuje tabulku se seznamem nechráněných počítačů. Vaše organizace může přiřadit různé značky výrobním počítačům a testovacím strojům nebo počítačům, které obsluhují různé funkce. Vzhledem k tomu, že každá třída počítačů potřebuje samostatné zásady zálohování, filtrování podle značek vám pomůže zobrazit informace, které jsou specifické pro každou z nich. Výběr názvu libovolného počítače přesměruje na panel **Konfigurovat zálohování** tohoto počítače, kde můžete použít příslušnou zásadu zálohování.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportovat do aplikace Excel

Obsah libovolné tabulky nebo grafu můžete exportovat jako tabulku aplikace Excel. Obsah se exportuje tak, jak je, s existujícími filtry. Chcete-li exportovat další řádky tabulky, můžete zvýšit počet řádků, které se mají na stránce zobrazit, pomocí rozevíracího seznamu **Řádky na stránku** v horní části každé karty.

## <a name="pin-to-the-dashboard"></a>Připnutí na řídicí panel

Můžete vybrat ikonu "pin" v horní části každé tabulky nebo grafu a připnout ji na řídicí panel portálu Azure. Připnutí těchto informací vám pomůže vytvořit přizpůsobený řídicí panel, který je přizpůsoben tak, aby zobrazoval informace, které jsou pro vás nejdůležitější.

## <a name="cross-tenant-views"></a>Zobrazení mezi tenanty

Pokud jste uživatel Služby Azure Lighthouse s delegovaným přístupem k předplatným ve více klientských prostředích, můžete použít výchozí filtr předplatného. Odběry, pro které chcete zobrazit data, zobrazíte tak, že vyberete ikonu "filtr" v pravém horním rohu portálu Azure. Při použití této funkce průzkumník zálohování agreguje informace o všech trezorech ve vybraných předplatných. Další informace najdete v tématu [Co je Azure Lighthouse?](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak používat Azure Monitor k získání přehledů o záložních datech.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
