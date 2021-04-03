---
title: Návod k Azure Advisormu virtuálnímu počítači s Windows – Azure
description: Postup řešení Azure Advisor doporučení pro virtuální počítače s Windows
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 760e0212d2d863e6b869c23c2e523a0e056a28ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90033026"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Jak vyřešit Azure Advisor doporučení

Tento článek popisuje, jak můžete vyřešit doporučení, která se zobrazí v Azure Advisor pro virtuální počítače s Windows.

## <a name="no-validation-environment-enabled"></a>"Bez povoleného prostředí pro ověřování"

>[!div class="mx-imgBorder"]
>![Snímek obrazovky Azure Advisor stránky provozní kvality. Doporučení "bez povoleného prostředí pro ověřování" je zvýrazněné červeně.](media/no-validation-environment.png)

Toto doporučení se zobrazí v části vynikající provozní kvalita. Doporučení by se také mělo zobrazit jako upozornění:

"V tomto předplatném nemáte povolené prostředí ověřování. Po provedení fondů hostitelů jste na kartě Vlastnosti vybrali možnost **ne** pro "prostředí ověřování". Pokud chcete zajistit kontinuitu podnikových služeb prostřednictvím nasazení služby Virtual Desktop systému Windows, ujistěte se, že máte alespoň jeden fond hostitelů s ověřovacím prostředím, ve kterém můžete testovat potenciální problémy. "

Tuto zprávu upozornění můžete nastavit tak, že povolíte prostředí ověřování v jednom z fondů hostitelů.

Postup při povolování ověřovacího prostředí:

1. Přejděte na domovskou stránku Azure Portal a vyberte fond hostitelů, který chcete změnit.

2. V dalším kroku vyberte fond hostitelů, který chcete změnit z provozního prostředí, na prostředí ověřování.

3. Ve fondu hostitelů vyberte v levém sloupci položku **vlastnosti** . V dalším kroku se posuňte dolů, dokud se nezobrazí "prostředí pro ověřování". Vyberte **Ano** a pak vyberte **použít**.

>[!div class="mx-imgBorder"]
>![Snímek obrazovky nabídky vlastností "Prostředí pro ověřování" je zvýrazněné červeně a je vybráno "Ano" bublinu.](media/validation-yes.png)

Tyto změny nezpůsobí, že upozornění zmizí okamžitě, ale mělo by zmizet. Azure Advisor aktualizace dvakrát denně. Až potom můžete doporučení odložit nebo zrušit ručně. Doporučujeme, abyste podali doporučení okamžitě. Tímto způsobem vám Azure Advisor umožní zjistit, jestli se při změně nastavení vyskytuje v jakémkoli problému.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"Nedostatečné provozní prostředí (nejedná se o ověřování) je povolené."

Toto doporučení se zobrazí v části vynikající provozní kvalita.

Pro toto doporučení se zobrazí zpráva s upozorněním z jednoho z následujících důvodů:

- V ověřovacím prostředí máte příliš mnoho fondů hostitelů.
- Nemáte žádné fondy produkčních hostitelů.

Doporučujeme, aby uživatelé měli v prostředí ověřování méně než polovinu svých fondů hostitelů.

Řešení tohoto upozornění:

1. Přejít na domovskou stránku Azure Portal.

2. Vyberte fondy hostitelů, které chcete změnit z ověřování na produkční prostředí.

3. Ve fondu hostitelů vyberte kartu **vlastnosti** ve sloupci na pravé straně obrazovky. V dalším kroku se posuňte dolů, dokud se nezobrazí "prostředí pro ověřování". Vyberte **ne** a pak vyberte **použít**.

>[!div class="mx-imgBorder"]
>![Snímek obrazovky nabídky vlastností "Prostředí pro ověřování" je zvýrazněné červeně a je vybráno "ne" bublinu.](media/validation-no.png)

Tyto změny nezpůsobí, že upozornění zmizí okamžitě, ale mělo by zmizet. Azure Advisor aktualizace dvakrát denně. Až potom můžete doporučení odložit nebo zrušit ručně. Doporučujeme, abyste podali doporučení okamžitě. Tímto způsobem vám Azure Advisor umožní zjistit, jestli se při změně nastavení vyskytuje v jakémkoli problému.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"Kvůli úspěšné implementaci virtuálního počítače se odblokovaly nedostatečné odkazy."

Toto doporučení se zobrazí v části vynikající provozní kvalita.

Abyste měli jistotu, že váš virtuální počítač funguje správně, musíte odblokovat konkrétní adresy URL. Seznam můžete zobrazit v [seznamu bezpečných adres URL](safe-url-list.md). Pokud nejsou adresy URL odblokovány, nebude váš virtuální počítač správně fungovat.

Pokud chcete toto doporučení vyřešit, ujistěte se, že jste odblokování všech adres URL v [seznamu bezpečných adres URL](safe-url-list.md). Pomocí značky služby nebo značek plně kvalifikovaného názvu domény můžete také odblokovat adresy URL.

## <a name="propose-new-recommendations"></a>Navrhnout nová doporučení

Můžete nám pomoci vylepšit Azure Advisor odesláním nápadů k doporučením. Vaše doporučení by mohlo pomáhat jinému uživateli na obtížném místě. Pokud chcete odeslat návrh, navštivte [náš fórum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) a vyplňte formulář pro odeslání. Když vyplníte formulář, nezapomeňte nám sdělit, co je co nejvíce podrobností.

## <a name="next-steps"></a>Další kroky

Pokud hledáte podrobnější informace o tom, jak řešit běžné problémy, podívejte se na [Přehled řešení potíží, zpětná vazba a podpora pro virtuální počítače s Windows](troubleshoot-set-up-overview.md).
