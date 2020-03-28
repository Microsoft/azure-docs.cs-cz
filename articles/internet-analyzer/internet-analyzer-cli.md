---
title: Vytvoření testu internetového analyzátoru pomocí cli | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak vytvořit první test nástroje Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74184266"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Vytvoření testu internetového analyzátoru pomocí cli (Preview)

Existují dva způsoby, jak vytvořit prostředek Internet Analyzer – pomocí [portálu Azure](internet-analyzer-create-test-portal.md) nebo pomocí příkazového příkazového příkazu. Tato část vám pomůže vytvořit nový prostředek Azure Internet Analyzer pomocí našeho prostředí příkazového příkazového příkazu. 


> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Verze Public Preview je k dispozici pro použití globálně. úložiště dat je však omezena na *US West 2* během náhledu.

## <a name="object-model"></a>Objektový model
Uživatel příkazového příkazu k analyzátoru Internetu zveřejňuje následující typy prostředků:
* **Testy** – test porovnává výkon koncového uživatele dvou koncových bodů internetu (A a B) v čase.
* **Profily** - Testy jsou vytvářeny pod profilem nástroje Internet Analyzer. Profily umožňují seskupit související testy; jeden profil může obsahovat jeden nebo více testů.
* **Předkonfigurované koncové body** – nastavili jsme koncové body s různými konfiguracemi (oblasti, akcelerační technologie atd.). Můžete použít některý z těchto předkonfigurovaných koncových bodů v testech.
* **Přehledy výkonnostních metrik** – Přehled výkonnostních metrik poskytuje rychlé a smysluplné souhrny výsledků měření. Informace o interpretaci přehledu výkonnostních metrik naleznete v části [Interpretace přehledu výkonnostních metrik](internet-analyzer-scorecard.md).
* **Časové řady** – časové řady ukazují, jak se metrika v průběhu času mění.

## <a name="profile-and-test-creation"></a>Vytvoření profilu a testu
1. Získejte přístup k náhledu nástroje Internet Analyzer podle pokynů **How do I participate in preview?** z [nejčastějších dotazů nástroje Azure Internet Analyzer](internet-analyzer-faq.md).
2. [Nainstalujte příkazové příkazové nebo tonové zaokrocené.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
3. Spuštěnípříkazu `login` pro spuštění relace příkazového příkazu:
    ```azurecli-interactive
    az login
    ```

    Pokud příkazové příkazové příkazy můžete otevřít výchozí prohlížeč, bude tak učinit a načíst přihlašovací stránku Azure.
    V opačném případě otevřete stránku prohlížeče a https://aka.ms/devicelogin zadejte autorizační kód zobrazený ve vašem terminálu.

4. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

5. Vyberte ID předplatného, kterému byl udělen přístup k verzi Internet Analyzer public preview.

    Po přihlášení se zobrazí seznam předplatných přidružených k vašemu účtu Azure. Informace o `isDefault: true` předplatném s je aktuálně aktivované předplatné po přihlášení. Chcete-li vybrat jiné předplatné, použijte příkaz [az účet nastavit](https://docs.microsoft.com/cli/azure/account#az-account-set) s ID předplatného přepnout na. Další informace o výběru předplatného najdete v tématu [Použití více předplatných Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    Existují i způsoby, jak se přihlásit neinteraktivně. Ty jsou podrobně popsané v tématu [Přihlášení pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

6. **[Nepovinné]** Vytvořte novou skupinu prostředků Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Instalace rozšíření Azure CLI Internet Analyzer:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Vytvořte nový profil internetového analyzátoru:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Seznam všech předkonfigurovaných koncových bodů, které jsou k dispozici pro nově vytvořený profil:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Vytvořte nový test v rámci nově vytvořeného profilu InternetAnalyzer:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    Výše uvedený příkaz předpokládá, že oba `www.contoso.com` a `www.microsoft.com` jsou hostování obrazu s jedním obrazem[(trans.gif)](https://fpc.msedge.net/apc/trans.gif)pod vlastní cesty. Pokud není explicitně zadána cesta k objektu, internetová analyzer použije `/apc/trans.gif` jako cestu k objektu ve výchozím nastavení, což je místo, kde předkonfigurované koncové body jsou hostitelem bitové kopie s jedním obrazem. Všimněte si také, že schéma (https/http) není nutné zadat; Internet Analyzer podporuje pouze koncové body HTTPS, takže se předpokládá protokol HTTPS.

11. Nový test by se měl zobrazit pod profilem Internet Analyzer:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Příklad výstupu:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Chcete-li začít generovat měření, musí být soubor JavaScript upozorpaný **skriptem FileUri** testu vložen do webové aplikace. Konkrétní pokyny naleznete na stránce [Klient embed internet analyzer.](internet-analyzer-embed-client.md)

13. Průběh testu můžete sledovat sledováním jeho hodnoty "stav":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Shromážděné výsledky testu můžete zkontrolovat tak, že pro něj vygenerujete časové řady nebo přehledy výkonnostních metrik:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Další kroky

* Projděte si [odkaz na příkaz CLI analyzátoru Internetu](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest) pro úplný seznam podporovaných příkazů a příkladů použití.
* Přečtěte si [nejčastější dotazy k internetovému analyzátoru](internet-analyzer-faq.md).
* Přečtěte si další informace o vložení [klienta Nástroje pro analýzu Internetu](internet-analyzer-embed-client.md) a vytvoření [vlastního koncového bodu](internet-analyzer-custom-endpoint.md). 
