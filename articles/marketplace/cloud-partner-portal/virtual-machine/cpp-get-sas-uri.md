---
title: Získat identifikátor URI sdíleného přístupového podpisu pro vaši image virtuálního počítače založené na Microsoft Azure | Azure Marketplace
description: Vysvětluje, jak získat identifikátor URI sdíleného přístupového podpisu (SAS) pro vaši image virtuálního počítače.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: cb6f1772c7c6f9abd268a8cb58550b253f095dbf
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132455"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Získat identifikátor URI sdíleného přístupového podpisu pro vaši image virtuálního počítače

Během procesu publikování musíte zadat identifikátor URI (Uniform Resource Identifier) pro každý virtuální pevný disk (VHD) přidružený k vašim SKU. Microsoft během certifikačního procesu potřebuje přístup k těmto virtuálním pevným diskům. Tento článek vysvětluje, jak vygenerovat identifikátor URI sdíleného přístupového podpisu (SAS) pro každý virtuální pevný disk. Tento identifikátor URI zadáte na kartě **SKU** v portál partnerů cloudu.

Při generování identifikátorů URI SAS pro vaše virtuální pevné disky postupujte podle následujících požadavků:

- Podporují se jenom nespravované virtuální pevné disky.
- jsou dostatečná oprávnění `List` a `Read`. Neposkytněte *`Write`* ani přístup k `Delete`.
- Doba trvání přístupu (*Datum vypršení platnosti*) by měla být minimálně tři týdny od okamžiku, kdy se identifikátor URI SAS vytvoří.
- Pro ochranu proti variacím času UTC nastavte počáteční datum na jeden den před aktuálním datem. Pokud je aktuální datum například 6. října 2014, vyberte 10/5/2014.

## <a name="generate-the-sas-url"></a>Vygenerovat adresu URL SAS

Adresu URL SAS lze vytvořit dvěma běžnými způsoby pomocí následujících nástrojů:

-   Microsoft Průzkumník služby Storage – grafický nástroj dostupný pro Windows, macOS a Linux
-   Microsoft Azure CLI – doporučené pro prostředí, která nejsou ve Windows OSs a automatizované nebo kontinuální integrace


### <a name="azure-cli"></a>Azure CLI

Pomocí následujících kroků vygenerujte identifikátor URI SAS pomocí Azure CLI.

1. Stáhněte a nainstalujte [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  Verze jsou k dispozici pro Windows, macOS a různé distribucey systému Linux.
2. Vytvořte soubor PowerShellu (`.ps1` příponu souboru), zkopírujte následující kód a pak ho uložte místně.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Upravte soubor tak, aby poskytoval následující hodnoty parametrů.  Data by měla být zadána ve formátu UTC DateTime, například `2016-10-25T00:00:00Z`.
   - `<account-name>` – název účtu úložiště Azure
   - `<account-key>` – klíč účtu úložiště Azure
   - `<vhd-name>` – název VHD
   - `<start-date>` – počáteční datum oprávnění pro přístup VHD. Zadejte datum jeden den před aktuálním datem.
   - `<expiry-date>` – datum vypršení platnosti oprávnění pro přístup VHD.  Zadejte datum nejméně tři týdny po aktuálním datu.

   Následující příklad ukazuje správné hodnoty parametrů (v době psaní tohoto zápisu).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Uložte změny do tohoto skriptu PowerShellu.
5. Spusťte tento skript s oprávněními správce pro vygenerování *připojovacího řetězce SAS* pro přístup na úrovni kontejneru.  Můžete použít dva základní přístupy:
   - Spusťte skript z konzoly nástroje.  Například v systému Windows klikněte na skript a vyberte **Spustit jako správce**.
   - Spusťte skript z editoru skriptu PowerShell, jako je například [Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise), pomocí oprávnění správce.
     Následující příklad ukazuje připojovací řetězec SAS generovaný v tomto editoru.

     ![Generování identifikátoru URI SAS v prostředí PowerShell ISE](./media/publishvm_032.png)

6. Zkopírujte výsledný připojovací řetězec SAS a uložte ho do textového souboru v zabezpečeném umístění.  Tento řetězec upravíte tak, aby se do něho přidaly informace o umístění virtuálního pevného disku, aby se vytvořil finální identifikátor URI SAS.
7. V Azure Portal přejděte do úložiště objektů blob, které obsahuje virtuální pevný disk přidružený k nově vygenerovanému identifikátoru URI.
8. Zkopírujte hodnotu adresy URL **koncového bodu BLOB Service**, jak je znázorněno níže.

    ![Blob service koncový bod v Azure Portal](./media/publishvm_033.png)

9. Upravte textový soubor s připojovacím řetězcem SAS z kroku 6.  Úplný identifikátor URI SAS vytvoříte pomocí následujícího formátu:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Například pokud je název při `TestRGVM2.vhd`, výsledný identifikátor URI SAS by byl:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Tento postup opakujte pro každý virtuální pevný disk ve skladových položkách, které plánujete publikovat.


### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

Pomocí následujících kroků vygenerujte identifikátor URI SAS s Průzkumník služby Microsoft Azure Storage.

1. Stáhnout a nainstalovat [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Otevřete Průzkumníka a v nabídce na levé straně nabídek klikněte na ikonu **Přidat účet** .  Zobrazí se dialogové okno **připojit k Azure Storage** .
3. Vyberte **Přidat účet Azure** a klikněte na **Přihlásit se**.  Pokračujte podle požadovaných kroků a přihlaste se k účtu Azure.
4. V levém podokně **Průzkumníka** přejděte na **účty úložiště** a rozbalte tento uzel.
5. Klikněte pravým tlačítkem na virtuální pevný disk a v místní nabídce vyberte **získat přístupový podpis sdílení** .

    ![Získat položku SAS v Azure Exploreru](./media/publishvm_034.png)

6. Zobrazí se dialogové okno **sdílený přístupový podpis** . Zadejte hodnoty pro následující pole:
   - **Čas spuštění** – počáteční datum oprávnění pro přístup VHD. Zadejte datum, které je před aktuálním datem jeden den.
   - **Čas** vypršení platnosti oprávnění – datum vypršení platnosti oprávnění pro přístup VHD.  Zadejte datum nejméně tři týdny po aktuálním datu.
   - **Oprávnění** – vyberte `Read` a `List` oprávnění.

     ![Dialogové okno SAS v Azure Exploreru](./media/publishvm_035.png)

7. Kliknutím na **vytvořit** vytvořte přidružený identifikátor URI SAS pro tento virtuální pevný disk.  Dialog teď zobrazí podrobnosti o této operaci.
8. Zkopírujte hodnotu **URL** a uložte ji do textového souboru v zabezpečeném umístění.

    ![Vytvoření identifikátoru URI SAS v Průzkumníkovi Azure](./media/publishvm_036.png)

    Tato vygenerovaná adresa URL SAS je určena pro přístup na úrovni kontejneru.  Aby se to zajistilo, musí se do něj přidat přidružený název virtuálního pevného disku.

9. Upravte textový soubor. Vložte název virtuálního pevného disku za `vhds` řetězec v adrese URL SAS (včetně úvodního lomítka).  Poslední identifikátor URI SAS by měl být ve formátu:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Například pokud je název při `TestRGVM2.vhd`, výsledný identifikátor URI SAS by byl:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Tento postup opakujte pro každý virtuální pevný disk ve skladových položkách, které plánujete publikovat.


## <a name="verify-the-sas-uri"></a>Ověření identifikátoru URI SAS

Pomocí následujícího kontrolního seznamu Zkontrolujte a ověřte každý generovaný identifikátor URI SAS.  Ověřte, že:
- Identifikátor URI má formát: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- Identifikátor URI obsahuje název souboru bitové kopie VHD, včetně přípony názvu souboru. VHD.
- Směrem ke středu identifikátoru URI se zobrazí `sp=rl`. Tento řetězec označuje, že je zadána `Read` a `List` přístup.
- Po tomto okamžiku se `sr=c` také zobrazí. Tento řetězec označuje, že je určen přístup na úrovni kontejneru.
- Zkopírujte identifikátor URI a vložte ho do prohlížeče, abyste mohli začít stahovat přidružený objekt BLOB.  (Operaci můžete zrušit před dokončením stahování.)


## <a name="next-steps"></a>Další kroky

Pokud máte potíže s vygenerováním identifikátoru URI SAS, podívejte se na [časté problémy s adresou URL SAS](./cpp-common-sas-url-issues.md).  V opačném případě uložte identifikátory URI SAS do zabezpečeného umístění pro pozdější použití. Bude se vyžadovat [publikování nabídky virtuálních počítačů](./cpp-publish-offer.md) v portál partnerů cloudu.
