---
title: Identifikátor URI sdíleného přístupového podpisu pro image virtuálních počítačů – Azure Marketplace
description: Vygenerujte identifikátor URI sdíleného přístupového podpisu (SAS) pro vaše virtuální pevné disky (VHD) v Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/29/2020
ms.openlocfilehash: 3c5c86f89882654e44f924ce0a19d4d71713144d
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431663"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Získat identifikátor URI sdíleného přístupového podpisu pro vaši image virtuálního počítače

Tento článek popisuje, jak vygenerovat identifikátor URI sdíleného přístupového podpisu (SAS) pro každý virtuální pevný disk (VHD).

Během procesu publikování je nutné zadat identifikátor URI pro každý virtuální pevný disk, který je přidružen k vašim plánům (dříve nazývaném SKU). Společnost Microsoft potřebuje během procesu certifikace přístup k těmto virtuálním pevným diskům. Tento identifikátor URI zadáte na kartě **plány** v partnerském centru.

Při generování identifikátorů URI SAS pro vaše virtuální pevné disky postupujte podle těchto požadavků:

* Podporují se jenom nespravované virtuální pevné disky.
* `List` `Read` Jsou vyžadována pouze oprávnění. Nezadávejte přístup pro zápis ani odstranění.
* Doba trvání přístupu (datum vypršení platnosti) by měla být minimálně tři týdny od okamžiku, kdy se identifikátor URI SAS vytvoří.
* Pro ochranu proti změnám času UTC nastavte počáteční datum na jeden den před aktuálním datem. Pokud je aktuální datum například 6. října 2019, vyberte 10/5/2019.

## <a name="generate-the-sas-address"></a>Vygenerovat adresu SAS

Existují dva běžné nástroje, které slouží k vytvoření adresy SAS (URL):

* **Microsoft Průzkumník služby Storage** – grafický nástroj dostupný v Azure Portal.
* **Microsoft Azure CLI** – doporučeno pro operační systémy jiné než Windows a automatizované nebo nepřetržité integrační prostředí.

### <a name="use-microsoft-storage-explorer"></a>Použití Microsoft Průzkumník služby Storage

1. V Azure Portal přejít na svůj účet úložiště.
2. V podokně Průzkumník na levé straně otevřete nástroj **Průzkumník služby Storage** (Preview).
3. Klikněte pravým tlačítkem na virtuální pevný disk a vyberte **získat sdílený přístupový podpis**.
4. Zobrazí se dialogové okno **sdílený přístupový podpis** . Vyplňte následující pole:

    * **Čas spuštění** – datum zahájení oprávnění pro přístup VHD. Zadejte datum, které je před aktuálním datem jeden den.
    * **Čas** vypršení platnosti – datum vypršení platnosti oprávnění pro přístup VHD. Zadejte datum, které je nejméně tři týdny po aktuálním datu.
    * **Oprávnění** – vyberte oprávnění číst a seznam.
    * **Na úrovni kontejneru** – zaškrtněte políčko **generovat identifikátor URI sdíleného přístupového podpisu na úrovni kontejneru** .

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Ukazuje dialogové okno sdíleného přístupového podpisu.":::

5. Pokud chcete vytvořit přidružený identifikátor URI SAS pro tento virtuální pevný disk, vyberte **vytvořit**. Dialogové okno se aktualizuje a zobrazí podrobnosti o této operaci.
6. Zkopírujte **identifikátor URI** a uložte ho do textového souboru v zabezpečeném umístění.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Ukazuje pole podrobností o sdíleném přístupovém podpisu.":::
7. Tento postup opakujte pro každý virtuální pevný disk v plánech, které budete publikovat.

### <a name="using-azure-cli"></a>Použití Azure CLI

1. Stáhněte a nainstalujte [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Verze jsou k dispozici pro Windows, macOS a různé distribucey systému Linux.
2. Vytvořte soubor PowerShellu (Přípona souboru. ps1), zkopírujte následující kód a pak ho uložte místně.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Upravte soubor tak, aby používal následující hodnoty parametrů. Zadejte data ve formátu UTC DateTime, například `2020-04-01T00:00:00Z` .

    * `<account-name>`– Váš název účtu služby Azure Storage
    * `<account-key>`– Váš klíč účtu úložiště Azure
    * `<vhd-name>`– Název virtuálního pevného disku
    * `<start-date>`– Počáteční datum oprávnění pro přístup VHD. Zadejte datum jeden den před aktuálním datem.
    * `<expiry-date>`– Datum vypršení platnosti oprávnění pro přístup VHD. Zadejte datum nejméně tři týdny po aktuálním datu.

    Tento příklad ukazuje správné hodnoty parametrů (v době psaní tohoto zápisu):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Uložte změny.
5. Pomocí jedné z následujících metod spusťte tento skript s oprávněními správce a vytvořte **připojovací řetězec SAS** pro přístup na úrovni kontejneru:

    * Spusťte skript z konzoly nástroje. V systému Windows klikněte pravým tlačítkem na skript a vyberte **Spustit jako správce**.
    * Spusťte skript z editoru skriptu prostředí PowerShell, jako je například [Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Tato obrazovka ukazuje vytvoření připojovacího řetězce SAS v rámci tohoto editoru:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Ukazuje vytvoření připojovacího řetězce SAS pomocí Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell":::

6. Zkopírujte připojovací řetězec SAS a uložte ho do textového souboru v zabezpečeném umístění. Úpravou tohoto řetězce přidejte informace o umístění virtuálního pevného disku pro vytvoření konečného identifikátoru URI SAS.
7. V Azure Portal přejdete do úložiště objektů blob, které obsahuje virtuální pevný disk přidružený k novému identifikátoru URI.
8. Zkopírujte adresu URL **koncového bodu BLOB Service**, jak je znázorněno na následujícím snímku obrazovky.

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Ukazuje Blob service koncový bod.":::

9. Upravte textový soubor s připojovacím řetězcem SAS z kroku 6. Vytvořte úplný identifikátor URI SAS v tomto formátu:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Například pokud je název VHD `TestRGVM2.vhd` , identifikátor URI SAS bude:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Tento postup opakujte pro každý virtuální pevný disk v plánech, které budete publikovat.

## <a name="verify-the-sas-uri"></a>Ověření identifikátoru URI SAS

Zkontrolujte každý vytvořený identifikátor URI SAS pomocí následujícího kontrolního seznamu a ověřte, že:

* Identifikátor URI vypadá takto:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* Identifikátor URI zahrnuje název souboru bitové kopie VHD, včetně přípony názvu souboru. VHD.
* `sp=rl`zobrazí se poblíž středu vašeho identifikátoru URI. Tento řetězec ukazuje, že `Read` a `List` je určen přístup.
* Když `sr=c` se zobrazí, znamená to, že je určený přístup na úrovni kontejneru.
* Zkopírování a vložení identifikátoru URI do prohlížeče pro testování – stažení objektu BLOB (tuto operaci můžete zrušit před dokončením stahování).

## <a name="next-step"></a>Další krok

Pokud máte potíže s vytvořením identifikátoru URI SAS, přečtěte si téma [běžné problémy s adresou URL SAS](common-sas-uri-issues.md). V opačném případě uložte identifikátory URI SAS do zabezpečeného umístění pro pozdější použití. Budete ho potřebovat k publikování nabídky virtuálních počítačů v partnerském centru.

* [Vytvoření nabídky virtuálních počítačů Azure](azure-vm-create-offer.md)
