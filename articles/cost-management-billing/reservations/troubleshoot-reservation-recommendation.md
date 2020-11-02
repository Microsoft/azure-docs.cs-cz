---
title: Řešení potíží s doporučeními pro rezervace Azure
description: Tento článek vám pomůže pochopit a řešit problémy s doporučeními rezervací Azure zobrazenými na webu Azure Portal.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492228"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Řešení potíží s doporučeními pro rezervace Azure

Tento článek vám pomůže pochopit a řešit problémy s doporučeními rezervací Azure zobrazenými na webu Azure Portal. Může se stát, že se nezobrazí žádná doporučení nebo že doporučení neodpovídají vašemu očekávání. Můžete například už mít rezervovanou instanci pro konkrétní konfiguraci virtuálního počítače. Očekáváte, že uvidíte doporučení pro podobnou konfiguraci virtuálního počítače.

## <a name="symptoms"></a>Příznaky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a přejděte k položce **Rezervace** .
2. Vyberte **+ Přidat** a pak vyberte požadovaný produkt.
3. Může se stát, že se na kartě **Doporučené** nezobrazí žádná doporučení nebo že doporučení neodpovídají vašemu očekávání.

## <a name="cause"></a>Příčina

Seznam doporučených produktů je vygenerovaný podle objemu využití pro daný rozsah (sdílený nebo samostatný), v porovnání s náklady na rezervaci pro příslušný produkt. Pokud modul doporučení zjistí potenciální úspory, doporučí produkt k zakoupení. Pokud ale modul žádné úspory nezjistí, žádný produkt nedoporučuje.

Když spouštíte prostředek s určitou konfigurací, není možné zaručit, že zakoupením rezervaci pro tuto konfiguraci ušetříte. Je například možné, že jde jenom o občasné využití. Pokud ano, nemusí vám celkové náklady na rezervaci za rezervační období nic ušetřit.

Je také důležité pochopit, jak výběr rozsahu ovlivňuje doporučení. Když je rozsah rezervace nastavený jako **sdílený** , doporučení v seznamu zobrazují rezervované instance, u kterých Azure zjistí úspory pro celou registraci, která je přidružená k předplatnému pro fakturaci. Pokud je rozsah rezervace nastavený jako **samostatný** , doporučení v seznamu se vztahují jenom na prostředky spuštěné v příslušném předplatném. Je možné, že se některé velikosti virtuálních počítačů doporučí pro jeden rozsah, ale pro jiný ne. Můžete mít napříč vaší registrací například agregované využití **Standard_B1ls** , které je dostatečně vysoké, aby odůvodnilo náklady na nákup rezervace v rozsahu registrace. Samotné jedno předplatné v této registraci ale nemusí mít dostatečné využití, aby se odůvodnily náklady na zakoupení rezervace v daném rozsahu. Změna rozsahu ze **sdíleného** na **samostatný** a naopak může vést k různým doporučením.

Na základě zjištěné úspory nákladů může Azure doporučit nákup rezervace pro určitá období, ale pro jiná ne. Konkrétně u tříletých období jsou slevy vyšší než u ročních. Je pravděpodobnější, že Azure najde úspory na tříleté období než na období jednoleté.

Pokud chcete zjistit, proč Azure doporučuje konkrétní velikosti a množství prostředků, vyberte **&lt;Množství&gt; Zobrazit podrobnosti** . Zobrazí se podrobná vizualizace s uvedením potenciálních úspor pro dané období.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Příklad ukazující odkaz Zobrazit podrobnosti pro doporučení rezervací" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Řešení

Můžete si koupit libovolný objem rezervací na libovolné období. Nákup rezervace s množstvím a termínem, které se liší od doporučení, bude mít nejspíš za následek úspory a využití, které jsou horší než optimální.

## <a name="next-steps"></a>Další kroky

- Další informace o doporučeních pro rezervace najdete v tématu [Doporučení k nákupu rezervací](determine-reservation-purchase.md).
