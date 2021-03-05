---
title: Vytvoření testu přes Internet Analyzer pomocí rozhraní příkazového řádku | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit první test přes Internet Analyzer pomocí Azure CLI.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a9a9fe93ebe302a76d69249dc56933e1bcc924d1
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200083"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Vytvoření testu pomocí rozhraní příkazového řádku (Preview) pro Internet Analyzer

Existují dva způsoby, jak vytvořit prostředek nástroje Internet Analyzer pomocí [Azure Portal](internet-analyzer-create-test-portal.md) nebo pomocí rozhraní příkazového řádku. Tato část vám pomůže vytvořit nový prostředek Azure Internet Analyzer pomocí našeho prostředí CLI. 


> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Verze Public Preview je k dispozici pro použití globálně; úložiště dat je ale ve verzi Preview omezené na *USA – západ 2* .

## <a name="object-model"></a>Objektový model
Rozhraní příkazového řádku pro Internet Analyzer zpřístupňuje následující typy prostředků:
* **Testy** – test porovná výkon koncových uživatelů dvou koncových bodů (a a B) v průběhu času.
* **Profily** – testy se vytvářejí v profilu Internet Analyzer. Profily umožňují seskupit související testy; jeden profil může obsahovat jeden nebo více testů.
* **Předem nakonfigurované koncové body** – nastavili jsme koncové body s řadou konfigurací (oblasti, technologie zrychlení atd.). V testech můžete použít některý z těchto předkonfigurovaných koncových bodů.
* **Scorecardy** – scorecard poskytuje rychlé a smysluplné souhrny výsledků měření. Přečtěte si téma [Interpretace přehledu výkonnostních metrik](internet-analyzer-scorecard.md).
* **Time Series** – časová řada ukazuje, jak se metrika mění v čase.

## <a name="profile-and-test-creation"></a>Vytváření profilů a testů
1. Získat přístup k aplikaci Internet Analyzer Preview pomocí **návody se účastní verze Preview?** pokyny najdete v tématu [Nejčastější dotazy k Azure Internet Analyzer](internet-analyzer-faq.md).
2. [Nainstalujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).
3. Spuštěním `login` příkazu spusťte relaci CLI:
    ```azurecli-interactive
    az login
    ```

    Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.
    V opačném případě otevřete stránku prohlížeče na adrese https://aka.ms/devicelogin a zadejte autorizační kód zobrazený v terminálu.

4. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

5. Vyberte ID předplatného, kterému bylo uděleno oprávnění k přístupu k Internet Analyzer Public Preview.

    Po přihlášení se zobrazí seznam předplatných přidružených k vašemu účtu Azure. Informace o předplatném s `isDefault: true` je aktuálně aktivované předplatné po přihlášení. Pokud chcete vybrat jiné předplatné, přejděte k přepínači pomocí příkazu [AZ Account set](/cli/azure/account#az-account-set) s ID předplatného. Další informace o výběru předplatného najdete v tématu [použití více předplatných Azure](/cli/azure/manage-azure-subscriptions-azure-cli).

    Existují i způsoby, jak se přihlásit neinteraktivně. Ty jsou podrobně popsané v tématu [Přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli).

6. **[Nepovinné]** Vytvořte novou skupinu prostředků Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Nainstalujte rozšíření Azure CLI Internet Analyzer:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Vytvořit nový profil pro Internet Analyzer:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Vypíše všechny předem nakonfigurované koncové body, které jsou k dispozici nově vytvořenému profilu:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Vytvořte nový test pod nově vytvořeným profilem InternetAnalyzer:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    Výše uvedený příkaz předpokládá, že oba a hostují obrázek v obrazovém obraze `www.contoso.com` `www.microsoft.com` ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) v části vlastní cesty. Pokud není cesta k objektu explicitně určena, bude služba Internet Analyzer `/apc/trans.gif` ve výchozím nastavení používat jako cestu k objektu, což je umístění předkonfigurovaných koncových bodů pro obrázek v podobě jednoho pixelu. Všimněte si také, že není nutné zadávat schéma (HTTPS/HTTP); Nástroj Internet Analyzer podporuje pouze koncové body HTTPS, takže se předpokládá protokol HTTPS.

11. Nový test by měl být zobrazen pod profilem nástroje Internet Analyzer:
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

12. Aby bylo možné začít generovat měření, musí být soubor JavaScriptu, na který odkazuje **scriptFileUri** testu, vložen do vaší webové aplikace. Konkrétní pokyny najdete na stránce pro [vložení klienta Internet Analyzer](internet-analyzer-embed-client.md) .

13. Průběh testu můžete monitorovat tak, že si zachováte jeho hodnotu "stav":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Shromážděné výsledky testu můžete zkontrolovat tak, že pro něj vygenerujete časové řady nebo přehledy:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Další kroky

* Úplný seznam podporovaných příkazů a příkladů použití najdete v [referenčních informacích](/cli/azure/ext/internet-analyzer/internet-analyzer) k rozhraní příkazového řádku Internet Analyzer.
* Přečtěte si [Nejčastější dotazy k Internet Analyzer](internet-analyzer-faq.md).
* Přečtěte si další informace o vložení [klienta Internet Analyzer](internet-analyzer-embed-client.md) a vytvoření [vlastního koncového bodu](internet-analyzer-custom-endpoint.md).