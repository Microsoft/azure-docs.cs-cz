---
title: Azure Monitor aplikace změnit analýza - zjišťovat změny, které může mít vliv na problémy živého webu/výpadků aplikací Azure Monitor změnit analýzy | Dokumentace Microsoftu
description: Řešení potíží s aplikací problémy živého webu v Azure App Service s analýzou změnit aplikaci Azure Monitor
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415857"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Azure Monitor aplikace změnit analýzy (verze public preview)

Pokud dojde k problému/výpadku živého webu, rychle určování příčin je velmi důležité. Standardní řešení monitorování může pomoct rychle zjistit, že dojde k nějakému problému, a často i která komponenta selhává. Ale to není vždy vést k okamžité zdůvodnění Proč dochází k selhání. Váš web pracoval pěti minutami, nyní je poškozen. Co se změnilo v posledních pět minut? Toto je otázky, nové funkce Azure Monitor analýza změnu aplikace slouží k zodpovězení. Vytvořením sílu [Azure Graph prostředků](https://docs.microsoft.com/azure/governance/resource-graph/overview) změnu analýza aplikací nabízí pohled na změny aplikace služby Azure App Service ke zvýšení observability a snížení MTTR (střední doby na opravu).

> [!IMPORTANT]
> Azure Monitor aplikace změnit analýza je momentálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-do-i-use-application-change-analysis"></a>Použití změn analýza aplikace

Azure Monitor aplikace změnit analýzy je aktuálně součástí samoobslužný **diagnostikovat a řešit problémy** prostředí, která je přístupná z **přehled** oddílu adresáře služby Azure App Service aplikace:

![Snímek obrazovky s Azure App Service přehled stránka červená pole kolem tlačítka Přehled a diagnostikovat a řešit problémy tlačítko](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>Povolit změnu analýzu

1. Vyberte **dostupnost a výkon**

    ![snímek obrazovky dostupnost a výkon, možnosti Poradce při potížích](./media/change-analysis/availability-and-performance.png)

2. Klikněte na tlačítko **aplikace spadne** dlaždici.

   ![Snímek obrazovky s dlaždicí selhání aplikace](./media/change-analysis/application-crashes-tile.png)

3. Povolit **změnu analýzy** vyberte **povolit teď**.

   ![snímek obrazovky dostupnost a výkon, možnosti Poradce při potížích](./media/change-analysis/application-crashes.png)

4. Provést analýzu funkce sady změn výhod kompletní **změnit analýzy**, **vyhledat změny kódu**, a **vždy na** k **na** a vyberte **Uložit**.

    ![Snímek obrazovky s Azure App Service umožňují změnit analýzy uživatelského rozhraní](./media/change-analysis/change-analysis-on.png)

    Pokud **změnu analýzy** je povoleno, bude možné zjistit změny na úrovni prostředků. Pokud **vyhledat změny kódu** je povolený, uvidíte také soubory nasazení a změn konfigurace lokality. Povolení **vždy na** optimalizuje změna kontroly výkonu, ale může způsobit dodatečné náklady z hlediska fakturace.

5.  Po povolení všechno, co vyberete **diagnostikovat a řešit problémy** > **dostupnost a výkon** > **aplikace spadne** vám umožní přístup k prostředí analýzy změnit. Typ změny, ke kterým došlo v čase spolu s podrobnostmi o těchto změnách bude summerize grafu:

     ![Snímek obrazovky změnit zobrazení rozdílu](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unable-to-fetch-change-analysis-information"></a>Nepovedlo se načíst informace změnit analýzy.

Toto je dočasný problém aktuální začátků ve verzi preview. Alternativní řešení se skládá z nastavení skryté klíčové slovo ve webové aplikaci a pak aktualizovat stránku:

   ![Snímek obrazovky skryté změnu značky](./media/change-analysis/hidden-tag.png)

Chcete-li nastavit skryté značky pomocí prostředí PowerShell:

1. Otevřete Azure Cloud Shell:

    ![Snímek obrazovky změnu Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

2. Změňte typ prostředí PowerShell:

   ![Snímek obrazovky změnu Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

3. Spusťte následující příkaz:

```powershell
$webapp=Get-AzWebApp -Name <name_of_your_webapp>
$tags = $webapp.Tags
$tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
```

> [!NOTE]
> Po přidání skrytých značka může stále třeba zpočátku počkat až 4 hodiny se nejdřív zobrazit změny. To je způsobeno freqeuncy 4 hodiny, používající analytická služba změnu kontrolovat vaše webová aplikace zároveň omezit dopad na výkon skenování.

## <a name="next-steps"></a>Další postup

- Vylepšit monitorování služby Azure App Services [tím, že funkce Application Insights](azure-web-apps.md) služby Azure Monitor.
- Přehled o [Azure Graph prostředků](https://docs.microsoft.com/azure/governance/resource-graph/overview) což pomáhá aplikace pro monitorování Azure power změnit analýzy. 
