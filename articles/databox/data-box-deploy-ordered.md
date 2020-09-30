---
title: Kurz pro objednání Azure Data Box | Microsoft Docs
description: V tomto kurzu se dozvíte o Azure Data Box hybridní řešení, které umožňuje importovat místní data do Azure a jak objednat Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: alkohli
ms.openlocfilehash: 3181b88b0cf49516eb5230585460d0cc91bb4042
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575295"
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

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

Můžete se přihlásit k Azure a spustit příkazy rozhraní příkazového řádku Azure CLI jedním ze dvou způsobů:

* Můžete nainstalovat rozhraní příkazového řádku a spustit příkazy rozhraní příkazového řádku místně.
* Příkazy rozhraní příkazového řádku můžete spustit z Azure Portal v Azure Cloud Shell.

K tomuto kurzu používáme Azure CLI prostřednictvím prostředí Windows PowerShell, ale můžete si vybrat jednu z možností.

### <a name="for-azure-cli"></a>Pro Azure CLI

Než začnete, ujistěte se, že:

#### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

* Nainstalujte [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) verze 2.0.67 nebo novější. Případně můžete [nainstalovat pomocí MSI](https://aka.ms/installazurecliwindows).

**Přihlášení k Azure**

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

**Instalace rozšíření Azure Data Box CLI**

Než budete moci použít příkazy Azure Data Box CLI, je nutné nainstalovat rozšíření. Rozšíření rozhraní příkazového řádku Azure poskytují přístup k experimentálním a předběžným příkazům, které ještě nebyly dodány jako součást základního rozhraní příkazového řádku. Další informace o rozšířeních najdete v tématu [použití rozšíření pomocí Azure CLI](/cli/azure/azure-cli-extensions-overview).

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

#### <a name="use-azure-cloud-shell"></a>Použití služby Azure Cloud Shell

Pomocí [Azure Cloud Shell](https://shell.azure.com/)prostředí Azure Hosted Interactive Shell můžete v prohlížeči spustit příkazy rozhraní příkazového řádku (CLI). Azure Cloud Shell podporuje bash nebo Windows PowerShell se službami Azure. Rozhraní příkazového řádku Azure je předem nainstalované a nakonfigurované pro použití s vaším účtem. V nabídce v pravé horní části Azure Portal vyberte tlačítko Cloud Shell:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Tlačítko spustí interaktivní prostředí, které můžete použít ke spuštění kroků popsaných v tomto článku s postupem.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Pro Azure PowerShell

Než začnete, ujistěte se, že:

* Nainstalujte Windows PowerShell 6.2.4 nebo novější.
* Nainstalujte modul Azure PowerShell (AZ).
* Nainstalujte modul Azure Data Box (AZ. DataBox).
* Přihlaste se k Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Místní instalace Azure PowerShell a modulů

**Instalace nebo upgrade prostředí Windows PowerShell**

Budete muset mít nainstalované prostředí Windows PowerShell verze 6.2.4 nebo novější. Pokud chcete zjistit, jakou verzi PowerShellu máte nainstalovanou, spusťte: `$PSVersionTable` .

Zobrazí se následující výstup:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Pokud je vaše verze nižší než 6.2.4, musíte upgradovat verzi Windows PowerShellu. Pokud chcete nainstalovat nejnovější verzi Windows PowerShellu, přečtěte si článek [instalace Azure PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7).

**Nainstalovat Azure PowerShell a Data Box moduly**

Abyste mohli Azure Data Box použít Azure PowerShell, budete muset nainstalovat Azure PowerShell moduly. Instalace modulů Azure PowerShell:

1. Nainstalujte [Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).
2. Pak pomocí příkazu nainstalujte AZ. DataBox `Install-Module -Name Az.DataBox` .

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete okno příkazového řádku Windows PowerShellu a přihlaste se k Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) :

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Zde je výstup z úspěšného přihlášení:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Podrobné informace o tom, jak se přihlásit k Azure pomocí Windows PowerShellu, najdete v tématu věnovaném [přihlášení pomocí Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Objednání Data Boxu

# <a name="portal"></a>[Azure Portal](#tab/portal)

Chcete-li zařízení objednat, proveďte následující kroky v Azure Portal.

1. Pomocí svých přihlašovacích údajů Microsoft Azure se přihlaste na následující adrese URL: [https://portal.azure.com](https://portal.azure.com).
2. Vyberte **+ Vytvořit prostředek** a vyhledejte *Azure Data Box*. Vyberte **Azure Data Box**.

   ![Snímek obrazovky nové části s Azure Data Box v poli hledání](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Vyberte **Vytvořit**.

   ![Snímek obrazovky s oddílem Azure Data Box pomocí možnosti vytvořit s názvem.](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Podívejte se, zda je služba Data Box dostupná ve vaší oblasti. Zadejte nebo vyberte následující informace a pak vyberte **Použít**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Typ přenosu     | Vyberte **Importovat do Azure**.        |
    |Předplatné     | Vyberte pro službu Data Box předplatné se smlouvou Enterprise, předplatné CSP nebo předplatné Azure Sponsorship. <br> Předplatné je propojeno s vaším fakturačním účtem.       |
    |Skupina prostředků | Vyberte existující skupinu prostředků. Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. |
    |Zdrojová země/oblast    |    Vyberte zemi nebo oblast, ve které jsou data aktuálně umístěná.         |
    |Cílová oblast Azure     |     Vyberte oblast Azure, do které chcete data přenést. <br> Další informace najdete v části věnované [dostupnosti v oblastech](data-box-overview.md#region-availability).            |

    [![Azure Data Box pořadí importu](media/data-box-deploy-ordered/select-data-box-import-04b.png)](media/data-box-deploy-ordered/select-data-box-import-04b.png#lightbox)

5. Vyberte **Data Box**. Maximální použitelná kapacita pro jednu objednávku je 80 TB. V případě větších objemů dat můžete vytvořit více objednávek.

    ![Snímek obrazovky s různými velikostmi dat, které jsou dostupné pro výběr: Data Box Disk 40 terabajty, Data Box 100 terabajty, Data Box Heavy, 1000 terabajty a poslat vlastní disky 1 terabajt.](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. V **uvedeném pořadí**přejdete na kartu **základy** . Zadejte nebo vyberte následující informace a vyberte **Další: cílová Data>**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné      | Předplatné se automaticky vyplní na základě vašeho předchozího výběru.|
    |Skupina prostředků    | Skupina prostředků, kterou jste vybrali dříve. |
    |Název importovaného objednávky | Zadejte popisný název pro sledování objednávky. <br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí.    |

    ![Snímek obrazovky s průvodcem objednávkou znázorňující základní krok průvodce se správnými informacemi, které jsou vyplněny.](media/data-box-deploy-ordered/select-data-box-import-06.png)

    Ve výchozím nastavení je heslo k odemknutí zařízení šifrované pomocí klíče spravovaného společností Microsoft. Po dokončení objednávky můžete přidat spravovaný klíč zákazníka. Spravovaný klíč zákazníka vám umožňuje použít vlastní klíč z klíče trezoru klíčů Azure k ochraně vašeho hesla pro odemknutí zařízení. Další informace najdete v tématu [použití klíčů spravovaných zákazníkem v Azure Key Vault pro Azure Data box](data-box-customer-managed-encryption-key-portal.md).

7. Na kartě **cíl dat** vyberte **cíl dat**.

    Pokud jako cíl úložiště používáte **účty úložiště** , zobrazí se následující snímek obrazovky:

    ![Azure Data Box cíl dat](media/data-box-deploy-ordered/select-data-box-import-07.png)

    Na základě zadané oblasti Azure vyberte z filtrovaného seznamu existujících účtů úložiště jeden nebo více účtů úložiště. Data Box je možné propojit až s 10 účty úložiště. Můžete si také vytvořit nový účet **Pro obecné účely v1**, **Pro obecné účely v2** nebo **účet úložiště objektů blob**.

    Účty úložiště s virtuálními sítěmi se podporují. Pokud chcete službě Data Box povolit práci se zabezpečenými účty úložiště, v nastavení síťové brány firewall účtu úložiště povolte důvěryhodné služby. Další informace najdete v tématu Postup [přidání Azure Data box jako důvěryhodné služby](../storage/common/storage-network-security.md#exceptions).

    Pokud k vytváření **spravovaných disků** z místních virtuálních pevných disků (VHD) používáte data box, budete muset zadat taky tyto informace:

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Skupiny prostředků     | Pokud chcete vytvořit spravované disky z místních virtuálních pevných disků, vytvořte nové skupiny prostředků. Stávající skupinu prostředků můžete použít jenom v případě, že se skupina prostředků vytvořila dříve při vytváření Data Boxho pořadí pro spravovaný disk pomocí služby Data Box. <br> Můžete zadat i více skupin prostředků oddělených středníkem. Podporuje se maximálně 10 skupin prostředků.|

    ![Snímek obrazovky s průvodcem pořadím zobrazujícím krok data a cíle průvodce se správnými informacemi, které jsou vyplněny](media/data-box-deploy-ordered/select-data-box-import-07b.png)

    Účet úložiště zadaný pro spravované disky slouží jako přípravný účet úložiště. Služba Data Box nahraje virtuální pevné disky jako objekty blob stránky do přípravného účtu úložiště, a teprve pak je převede na spravované disky a přesune do skupin prostředků. Další informace najdete v tématu popisujícím [ověření nahrání dat do Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).
   > [!NOTE]
   > Pokud se objekt blob stránky úspěšně nepřevede na spravovaný disk, zůstane v účtu úložiště a bude vám účtováno za úložiště.

    Pokračujte výběrem **Další: zabezpečení** .

1. Pokud chcete povolit šifrování na základě softwaru, vyberte v části **zabezpečení**možnost **Povolit pro objednávku dvojité šifrování**. 

   Šifrování založené na softwaru se provádí kromě šifrování AES-256 dat na Data Box.

   > [!NOTE]
   > Povolení této možnosti může způsobit, že zpracování objednávek a kopírování dat trvá déle. Po vytvoření objednávky tuto možnost nemůžete změnit.

   ![Obrazovka zabezpečení pro import datového boxu, dvojité šifrování](media/data-box-deploy-ordered/select-data-box-import-07c.png)

   Chcete-li pokračovat, vyberte možnost **Další: kontaktní údaje** .

8. V **podrobnostech kontaktu**vyberte **+ Přidat dodací adresu**.

    ![Snímek obrazovky s průvodcem objednávkou znázorňující krok průvodce s podrobnostmi o kontaktu s možností přidat dodací adresu s názvem.](media/data-box-deploy-ordered/select-data-box-import-08a.png)

9. V části **Dodací adresa** zadejte jméno a příjmení, název a poštovní adresu společnosti a platné telefonní číslo. Vyberte **Ověřit adresu**. Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení.

   ![Snímek obrazovky dialogového okna Přidat doručovací adresu s použitím možností a možnosti Přidat adresu pro odeslání s názvem.](media/data-box-deploy-ordered/select-data-box-import-10.png)

   Pokud jste vybrali samoobslužné expedici, obdržíte po úspěšném dokončení objednávky e-mailové oznámení. Další informace o samoobslužném expedici najdete v tématu [použití samostatně spravovaného expedice](data-box-portal-customer-managed-shipping.md).

10. Po úspěšném ověření podrobností o expedici vyberte **Přidat dodací adresu** . Vrátíte se na kartu **Podrobnosti o kontaktu** .

11. Až se vrátíte k **podrobnostem kontaktu** , přidejte jednu nebo víc e-mailových adres. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky.

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

    ![Snímek obrazovky s e-mailem v kroku průvodce objednávka podrobností kontaktu s textovým polem E-mail a další: možnost Revize a objednávka se zavolala.](media/data-box-deploy-ordered/select-data-box-import-08c.png)

12. Projděte si informace v části **Revize + objednávka** týkající se podmínek objednávky, kontaktu, oznámení a ochrany osobních údajů. Zaškrtněte políčko, že souhlasíte s podmínkami ochrany osobních údajů.

13. Vyberte **Objednat**. Vytvoření objednávky trvá několik minut.

    ![Snímek obrazovky s průvodcem objednávkou znázorňující krok revize a objednávka a možnost objednávky s názvem.](media/data-box-deploy-ordered/select-data-box-import-11.png)

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
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz <string>|
   |verbose| Zahrnout podrobné protokolování. | --verbose |

2. V příkazovém řádku příkazu Choice nebo terminálu pomocí [úlohy AZ data box Create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) vytvořte objednávku Azure Data box.

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

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Pomocí Azure PowerShell seřazení zařízení postupujte podle následujících kroků:

1. Před vytvořením pořadí importu musíte získat účet úložiště a uložit objekt účtu úložiště do proměnné.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Zapište si nastavení pro Data Box objednávku. Mezi tato nastavení patří vaše osobní/obchodní informace, název předplatného, informace o zařízení a informace o expedici. Tato nastavení budete muset použít jako parametry při spuštění příkazu PowerShellu k vytvoření pořadí Data Box. Následující tabulka ukazuje nastavení parametrů používané pro [New-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/New-AzDataBoxJob).

    | Nastavení (parametr) | Description |  Ukázková hodnota |
    |---|---|---|
    |ResourceGroupName [povinné]| Použijte existující skupinu prostředků. Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. | myresourcegroup|
    |Název [povinné]| Název vytvářené objednávky. | "mydataboxorder"|
    |Kontakt [povinné]| Název přidružený k dodací adrese. | "Gus Polsko"|
    |PhoneNumber [požadováno]| Telefonní číslo osoby nebo firmy, které obdrží objednávku.| "14255551234"
    |Umístění [povinné]| Nejbližší oblast Azure, na kterou se bude vaše zařízení napravovat.| WestUS|
    |DataBoxType [povinné]| Konkrétní Data Box zařízení, které objednáváte. Platné hodnoty jsou: "DataBox", "DataBoxDisk" a "DataBoxHeavy"| "DataBox" |
    |EmailId [povinné]| E-mailové adresy přidružené k objednávce| "gusp@contoso.com" |
    |StreetAddress1 [povinné]| Adresa ulice, na kterou bude objednávka odeslána | "15700 NE 39th St" |
    |StreetAddress2| Informace o sekundární adrese, například číslo bytu nebo číslo budovy. | "Bld 123" |
    |StreetAddress3| Informace o terciární adrese. | |
    |Město [povinné]| Město, do kterého bude zařízení expedováno. | Redmond |
    |StateOrProvinceCode [povinné]| Stav, ve kterém se zařízení dokončí.| WA |
    |CountryCode [povinné]| Země, kterou bude zařízení dodán. | "USA" |
    |PSČ [povinné]| Poštovní směrovací číslo nebo PSČ přidružené k expediční adrese.| "98052"|
    |CompanyName| Název vaší společnosti, pro kterou pracujete.| Contoso, LTD |
    |StorageAccountResourceId [povinné]| Azure Storage ID účtu, ze kterého chcete importovat data.| <AzStorageAccount>. ID |

3. V příkazovém řádku příkazu Choice nebo terminálu použijte příkaz [New-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/New-AzDataBoxJob) k vytvoření objednávky Azure Data box.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Zde je výstup z spuštění příkazu:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
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
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz <string>|
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
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz <string>|
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
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Sledovat jednu objednávku

Pokud chcete získat informace o sledování jednoho existujícího pořadí Azure Data Box, spusťte rutinu [Get-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/Get-AzDataBoxJob). Příkaz zobrazí informace o takovém pořadí, jako je například, nikoli omezení na: název, skupina prostředků, informace o sledování, ID předplatného, kontaktní informace, typ dodávky a SKU zařízení.

> [!NOTE]
> `Get-AzDataBoxJob` slouží k zobrazení jedné i vícenásobné objednávky. Rozdílem je, že zadáváte název objednávky pro jednotlivé objednávky.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   V následující tabulce jsou uvedeny informace o parametrech pro `Get-AzDataBoxJob` :

   | Parametr | Popis |  Ukázková hodnota |
   |---|---|---|
   |ResourceName (požadováno)| Název skupiny prostředků přidružené k danému pořadí. Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. | myresourcegroup|
   |Název [povinné]| Název objednávky, pro kterou mají být získány informace. | "mydataboxorder"|
   |ResourceId| ID prostředku přidruženého k objednávce |  |

   Tady je příklad příkazu s výstupem:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Zde je výstup z spuštění příkazu:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Vypsat všechny objednávky

Pokud jste objednali více zařízení, můžete spuštěním rutiny [Get-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/Get-AzDataBoxJob) Zobrazit všechny vaše objednávky Azure Data box. Příkaz zobrazí seznam všech objednávek, které patří do určité skupiny prostředků. Zobrazuje se také ve výstupu: název objednávky, stav expedice, oblast Azure, typ doručení, stav objednávky. Zrušené objednávky jsou také zahrnuty v seznamu.
Příkaz také zobrazuje časová razítka jednotlivých objednávek.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Tady je příklad tohoto příkazu:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Zde je výstup z spuštění příkazu:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
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
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz <string>|
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
   |query| Řetězec dotazu JMESPath Další informace najdete v tématu [JMESPath](http://jmespath.org/). | --dotaz <string>|
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

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Zrušení objednávky

Chcete-li zrušit Azure Data Box pořadí, spusťte rutinu [stop-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/stop-azdataboxjob). Je nutné zadat důvod pro zrušení objednávky.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

V následující tabulce jsou uvedeny informace o parametrech pro `Stop-AzDataBoxJob` :

| Parametr | Popis |  Ukázková hodnota |
|---|---|---|
|ResourceName (požadováno)| Název skupiny prostředků přidružené k objednávce, která se má zrušit. Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. | myresourcegroup|
|Název [povinné]| Název objednávky, která se má odstranit | "mydataboxorder"|
|Důvod [povinné]| Důvod zrušení objednávky. | "Zadali jste chybné informace a museli jste objednávku zrušit." |
|Force | Vynutí spuštění rutiny bez potvrzení uživatele. | -Force |

Tady je příklad příkazu s výstupem:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Zde je výstup z spuštění příkazu:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Odstranění objednávky

Pokud jste zrušili Azure Data Boxou objednávku, můžete ji odstranit spuštěním příkazu [Remove-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/remove-azdataboxjob) .

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

V následující tabulce jsou uvedeny informace o parametrech pro `Remove-AzDataBoxJob` :

| Parametr | Popis |  Ukázková hodnota |
|---|---|---|
|ResourceName (požadováno)| Název skupiny prostředků přidružené k objednávce, která se má odstranit Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. | myresourcegroup|
|Název [povinné]| Název objednávky, která se má odstranit | "mydataboxorder"|
|Force | Vynutí spuštění rutiny bez potvrzení uživatele. | -Force |

Tady je příklad příkazu s výstupem:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Zde je výstup z spuštění příkazu:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
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
