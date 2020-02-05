---
title: Monitorování záloh pomocí Průzkumníka zálohování
description: Článek popisující použití Průzkumníka služby Backup k provádění sledování záloh v reálném čase napříč trezory, předplatnými, regiony a klienty
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992180"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorování záloh pomocí Průzkumníka zálohování

Protože organizace zálohují více počítačů do cloudu, je důležité mít centrální umístění, kde si můžete zobrazit provozní informace o zálohování napříč velkým majetku, abyste mohli efektivně monitorovat zálohy.

Sešit Backup Exploreru je vestavěný Azure Monitor sešit, který umožňuje zákazníkům se zálohováním v rámci provádění provozních aktivit monitorování souvisejících se zálohováním napříč celou záloze v Azure (zahrnující klienty, umístění, předplatná, skupiny prostředků a trezory), a to vše z centrálního místa. V podstatě nabízí následující možnosti:

* **V perspektivě škálované** – agregované zobrazení zálohovaných položek, úloh, upozornění, zásad a prostředků, které nejsou nakonfigurované pro zálohování napříč celou záloze. 
* **Procházení hierarchie podrobností** – můžete si vybrat, jak získat podrobné informace o každé entitě – úlohy, výstrahy, zásady a zálohované položky, a to vše z jednoho místa.
* Užitečná **rozhraní** – Jakmile zjistíte problém, můžete provést akce tak, že na zálohovanou položku nebo prostředek Azure přejdete bez problémů.

Výše uvedené funkce jsou poskytovány předem pomocí nativní integrace s Azure Resource graphem a sešity Azure Monitor.

> [!NOTE]
> * Průzkumník zálohování je momentálně dostupný jenom pro data virtuálních počítačů Azure.
> * Průzkumník zálohování má být provozní řídicí panel pro zobrazení informací o vašich zálohách za posledních 7 dní (maximum).
> * V současné době se přizpůsobení šablony Průzkumníka zálohování nepodporuje. V současné době nedoporučujeme psát vlastní automatizace do dat grafu prostředků Azure.

## <a name="getting-started"></a>Začínáme

K Průzkumníku zálohování se dostanete tak, že přejdete do libovolného z vašich Recovery Servicesch trezorů a kliknete na odkaz **zálohovat Průzkumníka** na stránce **Přehled** .

![Rychlý odkaz na trezor](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Když kliknete na odkaz, otevře se Průzkumník zálohování, který poskytuje agregované zobrazení napříč všemi trezory a předplatnými, ke kterým máte přístup. Pokud používáte účet Azure Lighthouse, můžete zobrazit data ve všech klientech, ke kterým máte přístup (Další informace najdete v části v tématu zobrazení mezi klienty).

![Cílová stránka Průzkumníka](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Případy použití Průzkumníka zálohování

Průzkumník zálohování se skládá z několika karet. Každá karta poskytuje podrobné informace o konkrétním druhu záložního artefaktu, například zálohované položky, úlohy, zásady atd. V této části najdete stručný přehled jednotlivých karet. Tato videa poskytují ukázkové případy použití pro každou kartu spolu s popisem různých ovládacích prvků dostupných pro uživatele.

### <a name="summary"></a>Souhrn

Karta souhrn vám umožní rychlý přehled o celkovém stavu vaší nemovitosti k zálohování. Můžete zobrazit informace, jako je počet chráněných položek, počet položek, pro které není povolena ochrana, kolik úloh bylo úspěšné za posledních 24 hodin atd. 

Každá z ostatních karet představuje odlišnou entitu – například: zálohované položky, úlohy zálohování, výstrahy zálohování a zásady zálohování. Můžete si také prohlédnout informace o počítačích, pro které není nakonfigurované zálohování. Kliknutím na některou z těchto karet zobrazíte informace, které jsou specifické pro danou entitu.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>Zálohované položky

Můžete zobrazit všechny zálohované položky filtrované podle předplatného, trezoru a dalších parametrů. Kliknutím na název zálohované položky můžete otevřít okno Azure pro danou zálohovanou položku. V tabulce můžete například všimnout, že poslední zálohování selhalo pro položku ' X '. Kliknutím na X se otevře okno zálohování pro tuto položku, kde můžete aktivovat operaci zálohování na vyžádání.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>Úlohy

Podrobnosti o všech úlohách, které se aktivovaly během posledních sedmi dnů, můžete zobrazit tak, že přejdete na kartu úlohy. Tady můžete filtrovat podle operace úlohy, stavu úlohy a kódu chyby (v případě neúspěšných úloh).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>Výstrahy

Kliknutím na kartu Výstrahy můžete zobrazit podrobnosti o všech výstrahách, které byly aktivovány v trezorech za posledních sedm dní. Tady můžete filtrovat záznamy podle typu výstrahy (například Chyba zálohování, selhání obnovení), aktuální stav výstrahy (například aktivní nebo Vyřešeno) a závažnost výstrahy (například kritická, upozornění, informace). Můžete také přejít na virtuální počítač Azure kliknutím na odkaz na virtuální počítač a provést potřebnou akci.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>Zásady

Kliknutím na kartu zásady můžete zobrazit klíčové informace o všech zásadách zálohování, které byly vytvořeny v rámci služby zálohování. Můžete zjistit, kolik položek je k jednotlivým zásadám přidružené, spolu s rozsahem uchování a četností zálohování určenou jednotlivými zásadami.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>Zálohování není povolené.

Je důležité, aby správce zálohování organizace rychle identifikoval, které počítače v organizaci ještě nemají zapnutou zálohu, aby bylo možné povolit zálohování pro všechny počítače, které potřebují ochranu. Kliknutím na zálohování na kartě **nepovolíte** tuto úlohu.

Na této kartě se zobrazí tabulka obsahující seznam nechráněných položek. Pokud vaše organizace postupuje podle postupu přiřazení různých značek k produkčním počítačům a testovacím počítačům nebo počítačům, které slouží k různým funkcím, z nichž každá může potřebovat samostatnou zásadu zálohování, filtrování podle značek vám pomůže zobrazit informace, které jsou specifické pro určitá třída počítačů. Kliknutím na název libovolné položky vás přesměrujete na okno **Konfigurace zálohování** pro danou položku, kde se můžete rozhodnout tuto položku Zálohovat s příslušnými zásadami zálohování.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>Export do aplikace Excel

Kliknutí na tlačítko se šipkou dolů v pravém horním rohu libovolného widgetu (tabulka/graf) exportuje obsah tohoto widgetu jako excelový list, protože se používá existující filtry. Chcete-li exportovat více řádků tabulky do aplikace Excel, můžete zvýšit počet řádků zobrazených na stránce pomocí rozevíracího seznamu **řádky na stránce** v horní části každé karty.

## <a name="pinning-to-dashboard"></a>Připnutí na řídicí panel

Kliknutím na ikonu připnutí v horní části každého widgetu můžete tuto pomůcku připnout na řídicí panel Azure Portal. To vám pomůže vytvořit přizpůsobené řídicí panely, které budou zobrazovat nejdůležitější informace, které potřebujete.

## <a name="cross-tenant-views"></a>Zobrazení mezi klienty

Pokud jste uživatelem Azure Lighthouse s delegovaným přístupem k předplatným napříč více klientskými prostředími, můžete použít výchozí filtr předplatného (kliknutím na ikonu filtru v pravém horním rohu Azure Portal) a vybrat všechna předplatná, která chcete. viz data pro. Tím umožníte, aby Průzkumník zálohování agregován informace ve všech trezorech v rámci těchto předplatných. Další informace o Azure Lighthouse [najdete tady](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Další kroky

[Naučte se používat Azure Monitor pro získání přehledů o vašich zálohovaných datech.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)