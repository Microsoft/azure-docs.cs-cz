---
title: Pochopení stavu prostředku
titleSuffix: Azure Digital Twins
description: Podívejte se, jak použít Azure Resource Health ke kontrole stavu instance digitálního vlákna Azure.
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 9c31345a4ddaf9ac2b75204172dbc47606cb07db
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681491"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Řešení potíží s digitálními zdroji vláken Azure: Resource Health

[Azure Resource Health](../service-health/resource-health-overview.md) vám pomůže s diagnostikou a získáním podpory pro problémy se službami, které ovlivňují vaše prostředky Azure. Oznamuje stav svých prostředků v aktuálním a minulém stavu.

V tomto článku se dozvíte, jak získat informace o **stavu prostředků** pro instance digitálních vláken Azure.

## <a name="use-azure-resource-health"></a>Použít Azure Resource Health

Azure Resource Health vám může pomáhat sledovat, jestli je instance digitálních vláken Azure v provozu. Můžete ji také použít k tomu, abyste se seznámili s tím, jestli je vliv na stav vaší instance na oblastní výpadky.

Chcete-li zjistit stav instance, postupujte takto:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci digitálních vláken Azure. Můžete ji najít zadáním jejího názvu do panelu hledání na portálu. 

2. V nabídce instance vyberte v části *Podpora a řešení potíží* možnost _**stav prostředku**_ . Tím přejdete na stránku pro zobrazení historie stavu prostředku. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="Snímek obrazovky se stránkou &quot;stav prostředku&quot;. V části Historie stavu se zobrazuje denní sestava za posledních devět dní. Každý den zobrazuje stav &quot;k dispozici&quot;.":::

Na obrázku výše je tato instance zobrazená jako *dostupná* a byla za posledních devět dní. Další informace o *dostupných* stavech a dalších typech stavů, které se mohou zobrazit, najdete v tématu [*Přehled Azure Resource Health*](../service-health/resource-health-overview.md).

Můžete si taky přečíst další informace o různých kontrolách, které přecházejí do stavu prostředků pro různé typy prostředků Azure v [*typech prostředků a kontrolách stavu ve službě Azure Resource Health*](../service-health/resource-health-checks-resource-types.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další způsoby, jak monitorovat instanci digitálních vláken Azure v následujících článcích:
* [*Řešení potíží: zobrazení metrik pomocí Azure Monitor*](troubleshoot-metrics.md)
* [*Řešení potíží: nastavte diagnostiku*](troubleshoot-diagnostics.md).
* [*Řešení potíží: nastavení výstrah*](troubleshoot-alerts.md)
