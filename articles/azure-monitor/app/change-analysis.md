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
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226286"
---
# <a name="application-change-analysis-public-preview"></a>Analýza změny aplikace (public preview)

Pokud dojde k problému/výpadku živého webu, rychle určování příčin je velmi důležité. Standardní řešení monitorování může pomoct rychle zjistit, že dojde k nějakému problému, a často i která komponenta selhává. Ale to není vždy vést k okamžité zdůvodnění Proč dochází k selhání. Váš web pracoval pěti minutami, nyní je poškozen. Co se změnilo v posledních pět minut? Toto je otázky, nové funkce Azure Monitor analýza změnu aplikace slouží k zodpovězení. Vytvořením sílu [Azure Graph prostředků](https://docs.microsoft.com/azure/governance/resource-graph/overview) změnu analýza aplikací nabízí pohled na změny aplikace Azure ke zvýšení observability a snížení MTTR (střední doby na opravu).

> [!IMPORTANT]
> Azure Monitor aplikace změnit analýza je momentálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-change-analysis-service"></a>Přehled změn Analysis service
Služba analýzy změnu zjistí různé typy změn z vrstvy infrastruktury až po nasazení aplikace. Je předplatné poskytovatele prostředků s úrovní Azure, který vypadá do změn prostředků v předplatném a poskytuje data pro různé diagnostické nástroje, které pomáhají uživatelům pomoct pochopit, jaké změny může způsobit problémy s.

Následující diagram znázorňuje architekturu změnu analytická služba: ![Diagram architektury, jak změnit analytická služba získává Změna dat a zdrojem dat pro klienta nástroje](./media/change-analysis/overview.png)

Nástroj je momentálně integrována do App Services, webové aplikace s diagnostikou a řešením problémů prostředí. V tématu *změnu analytická služba pro webové aplikace služby App Services* část o tom, jak povolit a zobrazit změny provedené do webové aplikace.

### <a name="azure-resource-manager-deployment-changes"></a>Změny nasazení Azure Resource Manageru
Využití [Azure Graph prostředků](https://docs.microsoft.com/azure/governance/resource-graph/overview) nástroj pro analýzu změnu obsahuje Historický záznam o tom, jak Azure prostředky, které jsou hostiteli aplikace mění v průběhu času. Například pokud webovou aplikaci do ní přidat značku, změny se projeví v nástroji pro analýzu změnit.
Tyto informace jsou vždy k dispozici za předpokladu, `Microsoft.ChangeAnalysis` poskytovatele prostředků se připojili k předplatnému Azure.

### <a name="web-application-deployment-and-configuration-changes"></a>Změny nasazení a konfiguraci webové aplikace
Nástroj pro analýzu změnu zaznamenat stav nasazení a konfiguraci aplikace každé 4 hodiny compute rozdíly a zobrazit, co se změnilo. Příklady tyto změny: změny proměnné prostředí aplikace, změny pravidel konfigurace protokolu IP, změny Identity spravované služby, změny nastavení SSL a tak dále.
Na rozdíl od změny Resource Manageru nemusí být k dispozici okamžitě v nástroji pro tento typ informace o změně. A zobrazte nejnovější změny, použijte tlačítko "Kontrola změní nyní" v nástroji.

![Snímek obrazovky se kontroly změn teď tlačítko v diagnostikovat a řešit problémy nástroj analýzy integrací změn pro webové aplikace služby app service](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Změny závislostí
Závislosti prostředků mohou být také příčiny problémů. Například volá-li webovou aplikaci do mezipaměti redis cache, výkonu webových aplikací může mít vliv mezipaměti Redis SKU. Změnou analysis service jsou k dispozici také závislosti informace o změně identifikovat změny ve všech součástí aplikace, která by mohla způsobit problémy s podívat na záznam DNS webové aplikace.


## <a name="change-analysis-service-for-app-services-web-app"></a>Změnit službu Analysis Services pro webová aplikace služby App Services

Azure Monitor aplikace změnit analýzy je aktuálně součástí samoobslužný **diagnostikovat a řešit problémy** prostředí, která je přístupná z **přehled** oddílu adresáře služby Azure App Service aplikace:

![Snímek obrazovky s Azure App Service přehled stránka červená pole kolem tlačítka Přehled a diagnostikovat a řešit problémy tlačítko](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>Povolit analýzu změn v diagnostikovat a řešit problémy nástroje

1. Vyberte **dostupnost a výkon**

    ![snímek obrazovky dostupnost a výkon, možnosti Poradce při potížích](./media/change-analysis/availability-and-performance.png)

2. Klikněte na tlačítko **aplikace spadne** dlaždici.

   ![Snímek obrazovky s dlaždicí selhání aplikace](./media/change-analysis/application-crashes-tile.png)

3. Povolit **změnu analýzy** vyberte **povolit teď**.

   ![snímek obrazovky dostupnost a výkon, možnosti Poradce při potížích](./media/change-analysis/application-crashes.png)

4. Provést analýzu funkce sady změn výhod kompletní **změnit analýzy**, **vyhledat změny kódu**, a **vždy na** k **na** a vyberte **Uložit**.

    ![Snímek obrazovky s Azure App Service umožňují změnit analýzy uživatelského rozhraní](./media/change-analysis/change-analysis-on.png)

    Pokud **změnu analýzy** je povoleno, bude možné zjistit změny na úrovni prostředků. Pokud **vyhledat změny kódu** je povolený, uvidíte také soubory nasazení a změn konfigurace lokality. Povolení **vždy na** optimalizuje změna kontroly výkonu, ale může způsobit dodatečné náklady z hlediska fakturace.

5.  Po povolení všechno, co vyberete **diagnostikovat a řešit problémy** > **dostupnost a výkon** > **aplikace spadne** vám umožní přístup k prostředí analýzy změnit. Představuje typ změny, ke kterým došlo v čase spolu s podrobnostmi o těchto změnách souhrn grafu:

     ![Snímek obrazovky změnit zobrazení rozdílu](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>Povolit změnu Analysis service ve velkém měřítku
Pokud máte spoustu webových aplikací ve vašem předplatném, povolte na službu na úrovni webové aplikace bude neefektivní. Tady jsou některé pokyny alternativního registrace.

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>Registrace poskytovatele prostředků Analysis změnu pro vaše předplatné

1. Zaregistrujte analýzy změnit příznak funkce ve verzi preview

    Protože tato funkce je ve verzi preview, budete muset nejprve zaregistrovat příznak funkce, aby se staly viditelnými pro vaše předplatné.
    - Otevřete službu [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

    ![Snímek obrazovky změnu Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

    - Změňte typ prostředí PowerShell:

    ![Snímek obrazovky změnu Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

    - Spusťte následující příkaz PowerShellu:

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. Registrace poskytovatele prostředků Analysis změnu pro předplatné

    - Přejděte na předplatné, vyberte předplatné, které chcete připojit službu změnu a klikněte na poskytovatele prostředků:

        ![Snímek obrazovky registrace poskytovatele prostředků Analysis změnit v okně předplatná](./media/change-analysis/register-rp.png)

    - Vyberte *Microsoft.ChangeAnalysis* a klikněte na tlačítko *zaregistrovat* horní části stránky.

    - Jakmile poskytovatel prostředků připojili, postupujte podle pokynů v *se nepovedlo načíst informace o změně analýzy* níže se nastavit skryté značky ve webové aplikaci povolit nasazení úroveň změnit zjišťování ve webové aplikaci.

3. Můžete také ke kroku 2 výše si můžete zaregistrovat poskytovatele prostředků pomocí skriptu prostředí PowerShell:

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. Chcete-li nastavit skryté klíčové slovo ve webové aplikaci pomocí prostředí PowerShell, spusťte následující příkaz:

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
