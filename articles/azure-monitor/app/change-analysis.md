---
title: Použít změnu analýza aplikace ve službě Azure Monitor k vyhledání problémů s webovou aplikací | Dokumentace Microsoftu
description: Použijte změnu analýzy aplikace ve službě Azure Monitor k řešení potíží aplikace na weby ve službě Azure App Service.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 45df8f9e57223ea60a11c6af2187d362184cae2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443348"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Pomocí analýzy změny aplikace (preview) ve službě Azure Monitor

Pokud dojde k problému živého webu nebo kvůli výpadku, k rychlému určení hlavní příčinou je velmi důležité. Standardní řešení pro monitorování může vás upozorní na problém. Může dokonce označení, která komponenta selhává. Ale tato výstraha nebude vysvětlují vždy okamžitě příčinu jeho selhání. Víte, že se váš web pracoval pěti minutami, a teď je poškozen. Co se změnilo v posledních pět minut? Toto je dotaz, který změnu analýza aplikace je navržená k zodpovězení ve službě Azure Monitor.

Stavíme na sílu [grafu prostředků Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), změna analýzy poskytuje přehledy o změny aplikace Azure ke zvýšení observability a snížení MTTR (průměrný čas potřebný k opravě).

> [!IMPORTANT]
> Analýza změn je aktuálně ve verzi preview. Tato verze preview neposkytujeme bez smlouvu o úrovni služeb. Tato verze se nedoporučuje pro produkční úlohy. Některé funkce nemusí být podporován nebo můžou mít omezené možnosti. Další informace najdete v tématu [dodatečnými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Přehled

Změna analýzy detekuje různé typy změn z vrstvy infrastruktury až po nasazení aplikace. Zprostředkovatel prostředků Azure na úrovni předplatného, který kontroluje změn prostředků v předplatném je. Změna analýzy poskytuje data pro různé diagnostické nástroje, které pomáhají uživatelům pomoct pochopit, jaké změny může způsobit problémy.

Následující diagram znázorňuje architekturu změnu analýzy:

![Diagram architektury toho, jak změnit analýzy získá změny data a poskytuje klienta nástroje](./media/change-analysis/overview.png)

Aktuálně integrovaná analýza změnit **diagnostikovat a řešit problémy** prostředí ve službě App Service webovou aplikaci. Povolení detekce změn a zobrazíte změny ve webové aplikaci, najdete v článku *změnit analýzy pro funkci Web Apps* části dále v tomto článku.

### <a name="azure-resource-manager-deployment-changes"></a>Změny nasazení Azure Resource Manageru

Pomocí [Azure Graph prostředků](https://docs.microsoft.com/azure/governance/resource-graph/overview), změna analýzy obsahuje Historický záznam o tom, jak Azure prostředky, které jsou hostiteli aplikace mění v průběhu času. Může zjistit změny analýzy, například změny v nastavení SSL, spravované identity a pravidla konfigurace protokolu IP. Takže pokud do webové aplikace se přidá značka, změna se projeví změna analýzy. Tyto informace jsou k dispozici za předpokladu, `Microsoft.ChangeAnalysis` je povolen zprostředkovatel prostředků v předplatném Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Změny v nasazení webové aplikace a konfigurace

Změna analýzy zaznamenat stav nasazení a konfiguraci aplikace každé 4 hodiny. Může zjistit, například změny v proměnných prostředí aplikace. Nástroj vypočítá rozdíly a prezentuje, co se změnilo. Na rozdíl od změny Resource Manageru nemusí být k dispozici okamžitě v nástroji pro informace o změně pro nasazení kódu. Chcete-li zobrazit nejnovější změny v analýze změnit, vyberte **kontrola změní nyní**.

![Snímek obrazovky tlačítka "Kontrola změní teď"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Změny závislostí

Změny závislosti prostředků může také způsobit problémy ve webové aplikaci. Například pokud webová aplikace volá do mezipaměti redis cache, Redis cache skladové položky mohou ovlivnit výkonu webových aplikací. Zjišťovat změny v závislosti, ověří změnu analýzy záznam DNS pro webovou aplikaci. Tímto způsobem identifikuje změny ve všech komponent aplikace, které by mohly způsobit problémy.

## <a name="change-analysis-for-the-web-apps-feature"></a>Pro funkci Web Apps změňte analýzy

Ve službě Azure Monitor, změna analýzy aktuálně součástí samoobslužný **diagnostikovat a řešit problémy** prostředí. Přístup k této zkušenosti z **přehled** stránky aplikace služby App Service.

![Snímek obrazovky tlačítka "Přehled" a "diagnostikovat a řešit problémy" tlačítko](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Povolit analýzu změn v diagnostikovat a řešit problémy nástroje

1. Vyberte **dostupnost a výkon**.

    ![Snímek obrazovky "Dostupnosti a výkonu" možnosti Poradce při potížích](./media/change-analysis/availability-and-performance.png)

1. Vyberte **změny aplikace**. Ne, tato funkce je také dostupná v **aplikace spadne**.

   ![Snímek obrazovky tlačítka pro "Aplikaci dojde k chybě"](./media/change-analysis/application-changes.png)

1. Chcete-li změnit analýzy, vyberte **povolit teď**.

   ![Snímek obrazovky s možností "Aplikace dojde k chybě"](./media/change-analysis/enable-changeanalysis.png)

1. Zapnout **změnu analýzy** a vyberte **Uložit**.

    ![Snímek obrazovky uživatelského rozhraní "Povolit analýzu změnu"](./media/change-analysis/change-analysis-on.png)


1. Pro přístup k Analysis změnit, vyberte **diagnostikovat a řešit problémy** > **dostupnost a výkon** > **aplikace spadne**. Zobrazí se graf, který obsahuje souhrn typů změn v čase spolu s podrobnostmi o těchto změnách:

     ![Snímek obrazovky změnit zobrazení rozdílu](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Povolit změnu analýzy ve velkém měřítku

Pokud vaše předplatné zahrnuje mnoho webových aplikací, povolení služby na úrovni webové aplikace by být neefektivní. V takovém případě postupujte podle těchto pokynů alternativní.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Registrace poskytovatele prostředků Analysis změnu pro vaše předplatné

1. Zaregistrujte příznak funkce změnit analýzu (preview). Protože příznak funkce je ve verzi preview, budete muset zaregistrovat ho tak, aby viditelné pro vaše předplatné:

   1. Otevřete službu [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Snímek obrazovky změnu Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Změnit typ prostředí, který má **Powershellu**.

      ![Snímek obrazovky změnu Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Spusťte následující příkaz PowerShellu:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. Registrace poskytovatele prostředků Analysis změnu pro předplatné.

   - Přejděte na **předplatná**a vyberte předplatné, které chcete povolit ve službě změnit. Vyberte poskytovatele prostředků:

        ![Snímek obrazovky ukazující, jak se zaregistrovat poskytovatele prostředků Analysis změn](./media/change-analysis/register-rp.png)

       - Vyberte **Microsoft.ChangeAnalysis**. V horní části stránky vyberte **zaregistrovat**.

       - Po povolení poskytovatele prostředků, můžete nastavit značku skryté na webové aplikace ke zjištění změny na úrovni nasazení. Nastavit značku skryté, postupujte podle pokynů v části **se nepovedlo načíst informace změnit analýzy**.

   - Alternativně můžete použít skript prostředí PowerShell se zaregistrovat poskytovatele prostředků:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Jak nastavit skryté klíčové slovo ve webové aplikaci pomocí prostředí PowerShell, spusťte následující příkaz:

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Poté, co přidáte značku skrytá, může stále muset počkat až 4 hodiny, než začnete, zobrazuje změny. Výsledky jsou zpožděna, protože změna analýzy prohledá webové aplikace pouze každé 4 hodiny. Plán 4hodinového omezení kontroly dopad na výkon.

## <a name="next-steps"></a>Další postup

- Monitorování služby App Service efektivněji pomocí [povolení funkcí Application Insights](azure-web-apps.md) ve službě Azure Monitor.
- Další informace o [Azure Graph prostředků](https://docs.microsoft.com/azure/governance/resource-graph/overview), což pomáhá power změnu analýzy.
