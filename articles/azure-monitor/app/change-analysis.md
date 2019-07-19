---
title: Použití analýzy změn aplikace v Azure Monitor k nalezení problémů s webovými aplikacemi | Microsoft Docs
description: Pomocí analýzy změn aplikace v Azure Monitor můžete řešit problémy s aplikacemi na živých webech na Azure App Service.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 3efa26a1eaea8f522d9717efb0de0ec8e1682e0e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875114"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Použití analýzy změn aplikace (Preview) v Azure Monitor

Když dojde k problému nebo výpadku živého webu, rychle určit hlavní příčinu je kritická. Standardní řešení monitorování vás můžou upozornit na problém. Můžou dokonce označovat, která součást selhává. Tato výstraha ale nebude vždycky okamžitě vysvětlit příčinu selhání. Věděli jste, že váš web pracoval před pěti minutami a teď je porušený. Co se změnilo za posledních pět minut? Jedná se o otázku, kterou analýza změn aplikace je navržená tak, aby odpovídala v Azure Monitor.

Při sestavování výkonného [grafu prostředků Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)vám analýza změn poskytuje přehledy o změnách aplikací Azure, aby se zvýšila pozorování a snižovala MTTR (což znamená, jak dlouho se má opravit).

> [!IMPORTANT]
> Změna analýzy je aktuálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb. Tato verze se nedoporučuje pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="overview"></a>Přehled

Změna analýzy detekuje různé typy změn, od vrstvy infrastruktury po nasazení aplikace. Jedná se o poskytovatele prostředků Azure na úrovni předplatného, který kontroluje změny prostředků v předplatném. Změna analýz poskytuje data různých diagnostických nástrojů, které uživatelům pomůžou pochopit, jaké změny mohly způsobovat problémy.

Následující diagram znázorňuje architekturu analýzy změn:

![Diagram architektury, jak analýza změn získává data změny a poskytuje je klientským nástrojům](./media/change-analysis/overview.png)

V současné době se analýza změn integruje do **diagnostiky a řešení problémů** v App Service webové aplikaci. Pokud chcete povolit detekci změn a zobrazit změny ve webové aplikaci, přečtěte si část *Analýza změn pro Web Apps funkce* dále v tomto článku.

### <a name="azure-resource-manager-deployment-changes"></a>Změny nasazení Azure Resource Manager

Pomocí [Azure Resource graphu](https://docs.microsoft.com/azure/governance/resource-graph/overview)vám analýza změn poskytuje historický záznam o tom, jak se prostředky Azure, které hostují vaši aplikaci, v průběhu času změnily. Změna analýz může například rozpoznat změny v pravidlech konfigurace protokolu IP, spravovaných identit a nastavení SSL. Takže pokud je do webové aplikace přidána značka, změny se projeví v analýze. Tyto informace jsou k dispozici, pokud `Microsoft.ChangeAnalysis` je poskytovatel prostředků povolený v předplatném Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Změny v nasazení a konfiguraci webové aplikace

Změna analýz zachytí stav nasazení a konfigurace aplikace každé 4 hodiny. Může zjistit například změny v proměnných prostředí aplikace. Nástroj počítá rozdíly a prezentuje, co se změnilo. Na rozdíl od Správce prostředků změn nemusí být informace o změně nasazení kódu v nástroji k dispozici okamžitě. Chcete-li zobrazit nejnovější změny v rámci analýzy změn, vyberte **nyní možnost Prohledat změny**.

![Snímek obrazovky s tlačítkem "Vyhledat změny nyní"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Změny závislosti

Změny závislostí prostředků mohou také způsobovat problémy ve webové aplikaci. Například pokud webová aplikace volá do mezipaměti Redis, může být SKU Redis Cache ovlivněn výkon webové aplikace. Pokud chcete zjistit změny v závislostech, změňte analýzu na záznam DNS webové aplikace. Tímto způsobem identifikuje změny ve všech součástech aplikace, které by mohly způsobovat problémy.

## <a name="change-analysis-for-the-web-apps-feature"></a>Změna analýzy pro funkci Web Apps

V Azure Monitor je analýza změn v současnosti integrovaná v rámci samoobslužné **diagnostiky a řešení problémů** . K tomuto prostředí se dostanete ze stránky **Přehled** vaší aplikace App Service.

![Snímek obrazovky s tlačítkem "Přehled" a "diagnostikovat a řešit problémy"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Povolení analýzy změn v nástroji Diagnostika a řešení problémů

1. Vyberte **dostupnost a výkon**.

    ![Snímek možností Poradce při potížích s dostupností a výkonem](./media/change-analysis/availability-and-performance.png)

1. Vyberte **změny aplikace**. Tato funkce není k dispozici také při **selhání aplikace**.

   ![Snímek obrazovky s tlačítkem pro zhroucení aplikace](./media/change-analysis/application-changes.png)

1. Pokud chcete povolit analýzu změn, vyberte **Povolit nyní**.

   ![Snímek obrazovky s možnostmi zhroucení aplikací](./media/change-analysis/enable-changeanalysis.png)

1. Zapněte **analýzu změn** a vyberte **Uložit**.

    ![Snímek obrazovky s uživatelským rozhraním povolit analýzu změn](./media/change-analysis/change-analysis-on.png)


1. Chcete-li získat přístup k analýze změn, vyberte možnost **Diagnostika a řešení problémů s** > **dostupností a výkonem** > **aplikace**výkonu. Zobrazí se graf, který shrnuje typ změn v průběhu času spolu s podrobnostmi o těchto změnách:

     ![Snímek obrazovky se zobrazením rozdílů změn](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Povolit škálovatelnou analýzu změn

Pokud vaše předplatné obsahuje mnoho webových aplikací, povolení služby na úrovni webové aplikace bude neefektivní. V takovém případě postupujte podle těchto alternativních pokynů.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Zaregistrujte poskytovatele prostředků analýzy změn pro vaše předplatné.

1. Zaregistrujte příznak funkce změny analýzy (Preview). Vzhledem k tomu, že příznak funkce je ve verzi Preview, musíte ho zaregistrovat, aby bylo možné ho zviditelnit v rámci vašeho předplatného:

   1. Otevřete službu [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Snímek obrazovky se změnou Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Změňte typ prostředí na **PowerShell**.

      ![Snímek obrazovky se změnou Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Spusťte následující příkaz PowerShellu:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. Zaregistrujte poskytovatele prostředků analýzy změn pro toto předplatné.

   - Přejděte na **odběry**a vyberte předplatné, které chcete ve službě Change Service povolit. Pak vyberte poskytovatelé prostředků:

        ![Snímek obrazovky s informacemi o tom, jak zaregistrovat poskytovatele prostředků pro změnu analýzy](./media/change-analysis/register-rp.png)

       - Vyberte **Microsoft. ChangeAnalysis**. Pak v horní části stránky vyberte **Registrovat**.

       - Jakmile je poskytovatel prostředků povolený, můžete na webové aplikaci nastavit skrytou značku a zjišťovat tak změny v úrovni nasazení. Chcete-li nastavit skrytou značku, postupujte podle pokynů v části **nelze načíst informace o analýze změn**.

   - Případně můžete k registraci poskytovatele prostředků použít skript prostředí PowerShell:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Pokud chcete použít PowerShell k nastavení skryté značky na webové aplikaci, spusťte následující příkaz:

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Po přidání skryté značky možná budete muset počkat až 4 hodiny, než začnete zobrazovat změny. Výsledky jsou zpožděny, protože analýza změn kontroluje webové aplikace pouze každé 4 hodiny. Plán na 4 hodiny omezí dopad kontroly na výkon.

## <a name="next-steps"></a>Další postup

- Povolí Application Insights pro [aplikace Azure App Services](azure-web-apps.md).
- Povolte Application Insights pro virtuální počítače [Azure a Azure Virtual Machine Scale set pro aplikace hostované službou IIS](azure-vm-vmss-apps.md).
- Přečtěte si další informace o [Azure Resource graphu](https://docs.microsoft.com/azure/governance/resource-graph/overview), který pomáhá analyzovat změny napájení.
