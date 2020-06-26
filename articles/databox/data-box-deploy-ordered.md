---
title: Kurz pro objednání Azure Data Box | Microsoft Docs
description: Seznamte se s požadavky na nasazení a postupem objednání Azure Data Boxu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392471"
---
# <a name="tutorial-order-azure-data-box"></a>Kurz: Objednání Azure Data Boxu

Azure Data Box Disk je hybridní řešení, které umožňuje rychle, snadno a spolehlivě importovat místní data do Azure. Svá data přenesete do 80TB (využitelná kapacita) úložného zařízení od Microsoftu a pak toto zařízení odešlete zpět. Data se potom nahrají do Azure.

Tento kurz popisuje, jak objednat Azure Data Box. V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky na nasazení Data Boxu
> * Objednání Data Boxu
> * Sledování objednávky
> * Zrušení objednávky

## <a name="prerequisites"></a>Požadavky

# <a name="portal"></a>[Azure Portal](#tab/portal)

Před nasazením zařízení dokončete následující požadavky na konfiguraci pro Data Box službu a zařízení:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Můžete se přihlásit k Azure a spustit příkazy rozhraní příkazového řádku Azure CLI jedním ze dvou způsobů:

* Můžete nainstalovat rozhraní příkazového řádku a spustit příkazy rozhraní příkazového řádku místně.
* Příkazy rozhraní příkazového řádku můžete spustit z Azure Portal v Azure Cloud Shell.

K tomuto kurzu používáme Azure CLI prostřednictvím prostředí Windows PowerShell, ale můžete si vybrat jednu z možností.

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

* Nainstalujte [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) verze 2.0.67 nebo novější. Případně můžete [nainstalovat pomocí MSI](https://aka.ms/installazurecliwindows).

#### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete okno příkazového řádku Windows PowerShellu a přihlaste se k Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) :

```azurecli
PS C:\Windows> az login
```

Zde je výstup z úspěšného přihlášení:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>Instalace rozšíření Azure Data Box CLI

Než budete moci použít příkazy Azure Data Box CLI, je nutné nainstalovat rozšíření. Rozšíření Azure CLI poskytují přístup k experimentálním a předběžným příkazům, které ještě nebyly dodány jako součást základního rozhraní příkazového řádku. Další informace o rozšířeních najdete v tématu [použití rozšíření pomocí Azure CLI](/cli/azure/azure-cli-extensions-overview).

Chcete-li nainstalovat rozšíření pro Azure Data Box, spusťte následující příkaz: `az extension add --name databox` :

```azurecli

    PS C:\Windows> az extension add --name databox
```

Pokud je rozšíření nainstalovaná úspěšně, zobrazí se následující výstup:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>Použití služby Azure Cloud Shell

Pomocí [Azure Cloud Shell](https://shell.azure.com/)prostředí Azure Hosted Interactive Shell můžete v prohlížeči spustit příkazy rozhraní příkazového řádku (CLI). Azure Cloud Shell podporuje bash nebo Windows PowerShell se službami Azure. Rozhraní příkazového řádku Azure je předem nainstalované a nakonfigurované pro použití s vaším účtem. V nabídce v pravé horní části Azure Portal klikněte na tlačítko Cloud Shell:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Tlačítko spustí interaktivní prostředí, které můžete použít ke spuštění kroků popsaných v tomto článku s postupem.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>Objednání Data Boxu

# <a name="portal"></a>[Azure Portal](#tab/portal)

Chcete-li zařízení objednat, proveďte následující kroky v Azure Portal.

1. Pomocí svých přihlašovacích údajů Microsoft Azure se přihlaste na následující adrese URL: [https://portal.azure.com](https://portal.azure.com).
2. Klikněte na **+ Vytvořit prostředek** a vyhledejte *Azure Data Box*. Klikněte na **Azure Data Box**.

   [![Vyhledání Azure Data Boxu 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Klikněte na **Vytvořit**.

4. Podívejte se, zda je služba Data Box dostupná ve vaší oblasti. Zadejte nebo vyberte následující informace a pak vyberte **Použít**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné     | Vyberte pro službu Data Box předplatné se smlouvou Enterprise, předplatné CSP nebo předplatné Azure Sponsorship. <br> Předplatné je propojeno s vaším fakturačním účtem.       |
    |Typ přenosu     | Vyberte **Importovat do Azure**.        |
    |Zdrojová země/oblast    |    Vyberte zemi nebo oblast, ve které jsou data aktuálně umístěná.         |
    |Cílová oblast Azure     |     Vyberte oblast Azure, do které chcete data přenést.        |

5. Vyberte **Data Box**. Maximální použitelná kapacita pro jednu objednávku je 80 TB. V případě větších objemů dat můžete vytvořit více objednávek.

      [![Vyberte možnost Data Box 1.](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. V části **Objednávka** zadejte **Podrobnosti objednávky**. Zadejte nebo vyberte následující informace a pak vyberte **Další**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název     |  Zadejte popisný název pro sledování objednávky. <br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí.      |
    |Skupina prostředků     |    Použijte existující skupinu prostředků, nebo vytvořte novou. <br> Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně.         |
    |Cílová oblast Azure     | K účtu úložiště přiřaďte oblast. <br> Další informace najdete v části věnované [dostupnosti v oblastech](data-box-overview.md#region-availability).        |
    |Cíl úložiště     | Zvolte účet úložiště, spravované disky nebo obojí. <br> Na základě zadané oblasti Azure vyberte z filtrovaného seznamu existujících účtů úložiště jeden nebo více účtů úložiště. Data Box je možné propojit až s 10 účty úložiště. <br> Můžete si také vytvořit nový účet **Pro obecné účely v1**, **Pro obecné účely v2** nebo **účet úložiště objektů blob**. <br>Účty úložiště s virtuálními sítěmi se podporují. Pokud chcete službě Data Box povolit práci se zabezpečenými účty úložiště, v nastavení síťové brány firewall účtu úložiště povolte důvěryhodné služby. Další informace najdete v tématu Postup [přidání Azure Data box jako důvěryhodné služby](../storage/common/storage-network-security.md#exceptions).|

    Pokud jako cíl úložiště používáte účet úložiště, podívejte se na následující snímek obrazovky:

    ![Pořadí Data Box pro účet úložiště](media/data-box-deploy-ordered/order-storage-account.png)

    Pokud používáte Data Box k vytváření spravovaných disků z místních virtuálních pevných disků (VHD), budete také muset zadat následující informace:

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Skupiny prostředků     | Pokud chcete vytvořit spravované disky z místních virtuálních pevných disků, vytvořte nové skupiny prostředků. Stávající skupinu prostředků můžete použít jenom v případě, že se skupina prostředků vytvořila dříve při vytváření Data Boxho pořadí pro spravovaný disk pomocí služby Data Box. <br> Můžete zadat i více skupin prostředků oddělených středníkem. Podporuje se maximálně 10 skupin prostředků.|

    ![Pořadí Data Box pro spravovaný disk](media/data-box-deploy-ordered/order-managed-disks.png)

    Účet úložiště zadaný pro spravované disky slouží jako přípravný účet úložiště. Služba Data Box nahraje virtuální pevné disky jako objekty blob stránky do přípravného účtu úložiště, a teprve pak je převede na spravované disky a přesune do skupin prostředků. Další informace najdete v tématu popisujícím [ověření nahrání dat do Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. V části **Dodací adresa** zadejte jméno a příjmení, název a poštovní adresu společnosti a platné telefonní číslo. Klikněte na **Ověřit adresu**. Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení.

   Pokud jste vybrali samoobslužné expedici, obdržíte po úspěšném dokončení objednávky e-mailové oznámení. Další informace o samoobslužném expedici najdete v tématu [použití samostatně spravovaného expedice](data-box-portal-customer-managed-shipping.md).

8. Po úspěšném ověření podrobností o odeslání klikněte na **Další** .

9. Na kartě **Podrobnosti o oznámení** zadejte e-mailové adresy. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky.

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

10. Na kartě **Souhrn** zkontrolujte informace týkající se objednávky, kontaktu, oznámení a podmínkách ochrany osobních údajů. Zaškrtněte políčko, že souhlasíte s podmínkami ochrany osobních údajů.

11. Klikněte na **Objednat**. Vytvoření objednávky trvá několik minut.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Při seřazení zařízení pomocí Azure CLI proveďte následující kroky:

1. Zapište si nastavení pro Data Box objednávku. Mezi tato nastavení patří vaše osobní/obchodní informace, název předplatného, informace o zařízení a informace o expedici. Tato nastavení budete muset použít jako parametry při spuštění příkazu CLI k vytvoření pořadí Data Box. Následující tabulka ukazuje nastavení parametrů používané pro `az databox job create` :

   | Nastavení (parametr) | Description |  Ukázková hodnota |
   |---|---|---|
   |resource-group| Použijte existující skupinu prostředků, nebo vytvořte novou. Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. | myresourcegroup|
   |name| Název vytvářené objednávky. | "mydataboxorder"|
   |kontakt – jméno| Název přidružený k dodací adrese. | "Gus Polsko"|
   |Android| Telefonní číslo osoby nebo firmy, které obdrží objednávku.| "14255551234"
   |location| Nejbližší oblast Azure, na kterou se bude vaše zařízení napravovat.| "USA – západ"|
   |skladové| Konkrétní Data Box zařízení, které objednáváte. Platné hodnoty jsou: "DataBox", "DataBoxDisk" a "DataBoxHeavy"| "DataBox" |
   |e-mail – seznam| E-mailové adresy přidružené k objednávce| "gusp@contoso.com" |
   |ulice – Address1| Adresa ulice, na kterou bude objednávka odeslána | "15700 NE 39th St" |
   |Ulice – Address2| Informace o sekundární adrese, například číslo bytu nebo číslo budovy. | "Bld 123" |
   |city| Město, do kterého bude zařízení expedováno. | Redmond |
   |stát nebo provincie| Stav, ve kterém se zařízení dokončí.| WA |
   |country| Země, kterou bude zařízení dodán. | "USA" |
   |poštovní kód| Poštovní směrovací číslo nebo PSČ přidružené k expediční adrese.| "98052"|
   |název společnosti| Název vaší společnosti, pro kterou pracujete.| Contoso, LTD |
   |účet úložiště| Účet Azure Storage, ze kterého chcete importovat data.| mystorageaccount|
   |ladit| Zahrnout informace o ladění do podrobného protokolování  | --ladění |
   |Nápověda| Zobrazí informace o nápovědě pro tento příkaz. | --Help-h |
   |pouze – zobrazit – chyby| Zobrazí pouze chyby, potlačení upozornění. | --pouze-show-Errors |
   |výstup – o| Nastaví výstupní formát.  Povolené hodnoty: JSON, jsonc, None, Table, TSV, YAML, yamlc. Výchozí hodnota je JSON. | --Output "JSON" |
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz<string>|
   |verbose| Zahrnout podrobné protokolování. | --verbose |

2. V příkazovém řádku příkazu Choice nebo terminálu pomocí příkazu [AZ Databox Job Create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) vytvořte objednávku Azure Data box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Tady je příklad použití příkazu:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Zde je výstup z spuštění příkazu:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Všechny příkazy rozhraní příkazového řádku Azure budou ve výchozím nastavení používat jako výstupní formát JSON, pokud ho nezměníte. Výstupní formát lze změnit pomocí globálního parametru `--output <output-format>` . Změnou formátu na Table (tabulka) se zvýší čitelnost výstupů.

   Toto je stejný příkaz, který jsme právě spustili s malým vylepšením, abyste změnili formátování:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Zde je výstup z spuštění příkazu:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>Sledování objednávky

# <a name="portal"></a>[Azure Portal](#tab/portal)

Po odeslání objednávky můžete její stav sledovat na webu Azure Portal. Přejít na Data Box objednávku a pak přejít na **Přehled** a zobrazit stav. Na portálu se objednávka zobrazuje ve stavu **Objednáno**.

Pokud zařízení není k dispozici, dostanete oznámení. Pokud je zařízení k dispozici, Microsoft určí zařízení k odeslání a připraví zásilku. Při přípravě zařízení dojde k těmto akcím:

* Pro každý účet úložiště přidružený k zařízení se vytvoří sdílené složky SMB.
* Pro každou sdílenou složku se vygenerují přihlašovací údaje pro přístup, například uživatelské jméno a heslo.
* Také se vygeneruje heslo zařízení, které pomáhá odemknout zařízení.
* Data Box se uzamkne, aby se zabránilo jakémukoli neoprávněnému přístupu k zařízení.

Jakmile bude zařízení připravené, stav objednávky na portálu se změní na **Zpracováno**.

![Zpracovaná objednávka Data Boxu](media/data-box-overview/data-box-order-status-processed.png)

Microsoft potom připraví a odešle vaše zařízení přes místní přepravní službu. Po odeslání zařízení obdržíte číslo pro sledování zásilky. Stav objednávky na portálu se změní na **Odesláno**.

![Odeslaná objednávka Data Boxu](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Sledovat jednu objednávku

Pokud chcete získat informace o sledování jednoho existujícího pořadí Azure Data Box, spusťte příkaz [AZ Databox Job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show). Příkaz zobrazí informace o takovém pořadí, jako je například, nikoli omezení na: název, skupina prostředků, informace o sledování, ID předplatného, kontaktní informace, typ dodávky a SKU zařízení.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   V následující tabulce jsou uvedeny informace o parametrech pro `az databox job show` :

   | Parametr | Popis |  Ukázková hodnota |
   |---|---|---|
   |prostředek-skupina [povinné]| Název skupiny prostředků přidružené k danému pořadí. Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. | myresourcegroup|
   |název [povinné]| Název objednávky, která se má zobrazit | "mydataboxorder"|
   |ladit| Zahrnout informace o ladění do podrobného protokolování | --ladění |
   |Nápověda| Zobrazí informace o nápovědě pro tento příkaz. | --Help-h |
   |pouze – zobrazit – chyby| Zobrazí pouze chyby, potlačení upozornění. | --pouze-show-Errors |
   |výstup – o| Nastaví výstupní formát.  Povolené hodnoty: JSON, jsonc, None, Table, TSV, YAML, yamlc. Výchozí hodnota je JSON. | --Output "JSON" |
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz<string>|
   |verbose| Zahrnout podrobné protokolování. | --verbose |

   Tady je příklad příkazu s výstupním formátem nastaveným na tabulka:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Zde je výstup z spuštění příkazu:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Pořadí seznamu může být podporováno na úrovni předplatného a díky tomu má skupina prostředků volitelný parametr (spíše než požadovaný parametr).

### <a name="list-all-orders"></a>Vypsat všechny objednávky

Pokud jste objednali více zařízení, můžete spuštěním [AZ Databox Job list](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) Zobrazit všechny vaše objednávky Azure Data box. Příkaz zobrazí seznam všech objednávek, které patří do určité skupiny prostředků. Zobrazuje se také ve výstupu: název objednávky, stav expedice, oblast Azure, typ doručení, stav objednávky. Zrušené objednávky jsou také zahrnuty v seznamu.
Příkaz také zobrazuje časová razítka jednotlivých objednávek.

```azurecli
az databox job list --resource-group <resource-group>
```

V následující tabulce jsou uvedeny informace o parametrech pro `az databox job list` :

   | Parametr | Popis |  Ukázková hodnota |
   |---|---|---|
   |prostředek-skupina [povinné]| Název skupiny prostředků, která obsahuje objednávky. Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. | myresourcegroup|
   |ladit| Zahrnout informace o ladění do podrobného protokolování | --ladění |
   |Nápověda| Zobrazí informace o nápovědě pro tento příkaz. | --Help-h |
   |pouze – zobrazit – chyby| Zobrazí pouze chyby, potlačení upozornění. | --pouze-show-Errors |
   |výstup – o| Nastaví výstupní formát.  Povolené hodnoty: JSON, jsonc, None, Table, TSV, YAML, yamlc. Výchozí hodnota je JSON. | --Output "JSON" |
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz<string>|
   |verbose| Zahrnout podrobné protokolování. | --verbose |

   Tady je příklad příkazu s výstupním formátem nastaveným na tabulka:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Zde je výstup z spuštění příkazu:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>Zrušení objednávky

# <a name="portal"></a>[Azure Portal](#tab/portal)

Tuto objednávku zrušíte tak, že v Azure Portal přejdete na **Přehled** a na panelu příkazů vyberete **Zrušit** .

Objednávku můžete po odeslání kdykoli zrušit, dokud se stav objednávky nezmění na Zpracováno.

Zrušení objednávky odstraníte tak, že přejdete na **Přehled** a na panelu příkazů vyberete **Odstranit** .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Zrušení objednávky

Pokud chcete Azure Data Box pořadí zrušit, spusťte příkaz [AZ Databox Job Cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel). Je nutné zadat důvod pro zrušení objednávky.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   V následující tabulce jsou uvedeny informace o parametrech pro `az databox job cancel` :

   | Parametr | Popis |  Ukázková hodnota |
   |---|---|---|
   |prostředek-skupina [povinné]| Název skupiny prostředků přidružené k objednávce, která se má odstranit Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. | myresourcegroup|
   |název [povinné]| Název objednávky, která se má odstranit | "mydataboxorder"|
   |důvod [povinné]| Důvod zrušení objednávky. | "Zadali jste chybné informace a museli jste objednávku zrušit." |
   |ano| Nedotazovat se na potvrzení | --Yes (-y)| --Ano-y |
   |ladit| Zahrnout informace o ladění do podrobného protokolování | --ladění |
   |Nápověda| Zobrazí informace o nápovědě pro tento příkaz. | --Help-h |
   |pouze – zobrazit – chyby| Zobrazí pouze chyby, potlačení upozornění. | --pouze-show-Errors |
   |výstup – o| Nastaví výstupní formát.  Povolené hodnoty: JSON, jsonc, None, Table, TSV, YAML, yamlc. Výchozí hodnota je JSON. | --Output "JSON" |
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz<string>|
   |verbose| Zahrnout podrobné protokolování. | --verbose |

   Tady je příklad příkazu s výstupem:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Zde je výstup z spuštění příkazu:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Odstranění objednávky

Pokud jste zrušili Azure Data Boxou objednávku, můžete ji odstranit spuštěním příkazu [AZ Databox Job Delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) .

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   V následující tabulce jsou uvedeny informace o parametrech pro `az databox job delete` :

   | Parametr | Popis |  Ukázková hodnota |
   |---|---|---|
   |prostředek-skupina [povinné]| Název skupiny prostředků přidružené k objednávce, která se má odstranit Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. | myresourcegroup|
   |název [povinné]| Název objednávky, která se má odstranit | "mydataboxorder"|
   |předplatné| Název nebo identifikátor (GUID) vašeho předplatného Azure. | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
   |ano| Nedotazovat se na potvrzení | --Yes (-y)| --Ano-y |
   |ladit| Zahrnout informace o ladění do podrobného protokolování | --ladění |
   |Nápověda| Zobrazí informace o nápovědě pro tento příkaz. | --Help-h |
   |pouze – zobrazit – chyby| Zobrazí pouze chyby, potlačení upozornění. | --pouze-show-Errors |
   |výstup – o| Nastaví výstupní formát.  Povolené hodnoty: JSON, jsonc, None, Table, TSV, YAML, yamlc. Výchozí hodnota je JSON. | --Output "JSON" |
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz<string>|
   |verbose| Zahrnout podrobné protokolování. | --verbose |

Tady je příklad příkazu s výstupem:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Zde je výstup z spuštění příkazu:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o Azure Data Box článcích, jako jsou:

> [!div class="checklist"]
>
> * Požadavky na nasazení Data Boxu
> * Objednání Data Boxu
> * Sledování objednávky
> * Zrušení objednávky

V dalším kurzu se dozvíte, jak Data Box nastavit.

> [!div class="nextstepaction"]
> [Nastavení Azure Data Boxu](./data-box-deploy-set-up.md)
