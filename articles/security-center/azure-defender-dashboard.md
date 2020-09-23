---
title: Řídicí panel a jeho funkce v programu Azure Defender
description: Přečtěte si o funkcích řídicího panelu Azure Defenderu.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 268781f7f14ad93ca63cbbcbef1fb697d46b33a3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977337"
---
# <a name="the-azure-defender-dashboard"></a>Řídicí panel Azure Defender

Řídicí panel Azure Defender poskytuje:

- Viditelnost svého pokrytí v programu Azure Defender napříč různými typy prostředků
- Odkazy na konfiguraci pokročilých funkcí ochrany před internetovými útoky
- Stav připojování a instalace agenta
- Výstrahy detekce hrozeb v programu Azure Defender 

Přístup k řídicímu panelu Azure Defenderu získáte tak, že v nabídce Security Center v části zabezpečení cloudu vyberete **Azure Defender** .

## <a name="whats-shown-on-the-dashboard"></a>Co se zobrazuje na řídicím panelu?

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Příklad řídicího panelu Azure Defenderu" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

Řídicí panel obsahuje následující oddíly:

1. **Pokrytí v Azure Defenderu** – tady vidíte typy prostředků, které jsou ve vašem předplatném a mají nárok na ochranu pomocí Azure Defenderu. Ať už je to důležité, budete mít možnost upgradovat. Pokud chcete upgradovat všechny možné opravňující prostředky, vyberte možnost **upgradovat vše**.

1. **Oblast výstrahy zabezpečení** – když Azure Defender detekuje hrozbu v jakékoli oblasti vašeho prostředí, vygeneruje výstrahu. Tyto výstrahy popisují podrobnosti o ovlivněných prostředcích, navrhovaných krocích pro nápravu a v některých případech možnost aktivovat aplikaci logiky v reakci. Když vyberete libovolné místo v tomto grafu, otevře se **Stránka výstrahy zabezpečení**.

1. **Pokročilá ochrana** – Azure Defender zahrnuje mnoho pokročilých funkcí ochrany před internetovými útoky pro virtuální počítače, databáze SQL, kontejnery, webové aplikace, síť a další. V této části rozšířené ochrany uvidíte stav prostředků ve vybraných předplatných pro každou z těchto ochrany. Vyberte libovolné z nich, pokud chcete přejít přímo do oblasti konfigurace pro daný typ ochrany.

1. **Přehledy** – Toto souhrnné podokno novinek, navrhovaných čtení a výstrah s vysokou prioritou Security Center poskytuje přehled o tom, co je potřeba k tomu, aby se zobrazovaly informace o tom, jaké jsou důležité pro vás a vaše předplatné Najdete ho v podokně přehledy na **řídicím panelu Azure Defender**, ať už se jedná o seznam s vysokou závažností CVEs zjištěným na vašich virtuálních počítačích pomocí nástroje pro analýzu ohrožení zabezpečení nebo nového blogového příspěvku, který je členem týmu Security Center.




## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o řídicím panelu Azure Defender. 

Další informace o Azure Defenderu najdete v tématu [Úvod do Azure Defenderu](azure-defender.md) .

> [!div class="nextstepaction"]
> [Povolit Azure Defender](security-center-pricing.md)