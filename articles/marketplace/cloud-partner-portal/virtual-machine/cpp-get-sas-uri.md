---
title: Získání identifikátoru URI podpisu sdíleného přístupu pro bitovou kopii virtuálního počítače založeného na Microsoft Azure | Azure Marketplace
description: Vysvětluje, jak získat identifikátor URI sdíleného přístupového podpisu (SAS) pro bitovou kopii virtuálního počítače.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 6fe15fb18d8865911363a4696e44dd7fe1d90c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277799"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Získání identifikátoru URI sdíleného přístupového podpisu pro bitovou kopii virtuálního počítače

Během procesu publikování je nutné zadat jednotný identifikátor prostředku (URI) pro každý virtuální pevný disk (VHD) přidružený k vašim skum. Společnost Microsoft potřebuje přístup k těmto virtuálním serverům během procesu certifikace. Tento článek vysvětluje, jak generovat identifikátor URI sdílený přístupový podpis (SAS) pro každý virtuální pevný disk. Tento identifikátor URI zadáte na kartě **SKU** na portálu partnerů cloudu.

Při generování identifikátorů URI SAS pro virtuální počítače dodržujte následující požadavky:

- Podporovány jsou pouze nespravované virtuální disponály.
- `List`a `Read` oprávnění jsou dostatečná. *Neposkytují* `Write` ani `Delete` přístup.
- Doba trvání přístupu (*datum vypršení platnosti)* by měla být minimálně tři týdny od vytvoření identifikátoru URI SAS.
- Chcete-li chránit před změnami času UTC, nastavte počáteční datum na jeden den před aktuálním datem. Pokud je například aktuální datum 6.

## <a name="generate-the-sas-url"></a>Generovat adresu URL SAS

Adresu URL SAS lze vygenerovat dvěma běžnými způsoby pomocí následujících nástrojů:

- Microsoft Storage Explorer – grafický nástroj dostupný pro Windows, macOS a Linux
- Rozhraní příkazového nastavení Microsoft Azure – doporučeno pro operační systémy, které nejsou s Windows, a pro automatizovaná nebo průběžná integrační prostředí

### <a name="azure-cli"></a>Azure CLI

Pomocí následujících kroků vygenerujte identifikátor URI SAS pomocí azure CLI.

1. Stáhněte a nainstalujte [rozhraní příkazového příkazu KOnZ Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Verze jsou k dispozici pro Windows, macOS a různé distribuce Linuxu.
2. Vytvořte soubor PowerShellu (příponu),`.ps1` zkopírujte v následujícím kódu a uložte ho místně.

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Upravte soubor a zadám následující hodnoty parametrů.  Data by měla být uvedena ve formátu UTC datetime, například `2016-10-25T00:00:00Z`.
   - `<account-name>`- Název účtu úložiště Azure
   - `<account-key>`- Klíč účtu úložiště Azure
   - `<vhd-name>`- Vaše jméno VHD
   - `<start-date>`- Datum zahájení oprávnění pro přístup k Virtuálnímu pevnému disku. Zadejte datum jeden den před aktuálním datem.
   - `<expiry-date>`- Datum vypršení platnosti oprávnění pro přístup k Virtuálnímu pevnému disku.  Zadejte datum nejméně tři týdny po aktuálním datu.

   Následující příklad ukazuje správné hodnoty parametrů (v době psaní tohoto článku).

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Uložte změny tohoto skriptu prostředí PowerShell.
5. Spusťte tento skript pomocí oprávnění správce a vygenerujte *připojovací řetězec SAS* pro přístup na úrovni kontejneru.  Můžete použít dva základní přístupy:
   - Spusťte skript z konzole.  Například v systému Windows klikněte na skript a vyberte **Spustit jako správce**.
   - Spusťte skript z editoru skriptů prostředí PowerShell, jako je [například prostředí Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise), pomocí oprávnění správce.
     Následující ukazuje připojovací řetězec SAS generované v rámci tohoto editoru.

     ![Generování identifikátoru URI sas v prostředí PowerShell ISE](./media/publishvm_032.png)

6. Zkopírujte výsledný připojovací řetězec SAS a uložte jej do textového souboru na bezpečném místě.  Tento řetězec upravíte a přidáte do něj přidružené informace o umístění virtuálního pevného disku a vytvoříte tak konečný identifikátor URI s rozhraním SAS.
7. Na webu Azure Portal přejděte na úložiště objektů blob, které obsahuje virtuální pevný disk přidružený k nově generovanému identifikátoru URI.
8. Zkopírujte hodnotu adresy URL **koncového bodu služby Objektů blob**, jak je znázorněno níže.

    ![Koncový bod služby objektů blob na webu Azure Portal](./media/publishvm_033.png)

9. Upravte textový soubor pomocí připojovacího řetězce SAS z kroku 6.  Celý identifikátor URI s postupem do aplikace SAS vytvoříte v následujícím formátu:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Pokud je `TestRGVM2.vhd`například název VDH , bude výsledný identifikátor URI SAS:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Opakujte tyto kroky pro každý virtuální pevný disk v sku, které chcete publikovat.

### <a name="microsoft-storage-explorer"></a>Průzkumník úložišť Microsoft

Pomocí následujících kroků vygenerujte identifikátor URI SAS pomocí Průzkumníka úložiště Microsoft Azure.

1. Stáhnout a nainstalovat [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Otevřete průzkumníka a v levém panelu nabídky klikněte na ikonu **Přidat účet.**  Zobrazí se dialogové okno **Připojit k úložišti Azure.**
3. Vyberte **Přidat účet Azure** a klikněte na **Přihlásit se**.  Pokračujte v požadovaných krocích pro přihlášení k účtu Azure.
4. V levém podokně **Průzkumníka** přejděte na **účty úložiště** a rozbalte tento uzel.
5. Klikněte pravým tlačítkem myši na virtuální pevný disk a v místní nabídce vyberte **Získat přístup ke sdílené položce.**

    ![Získání položky SAS v Azure Exploreru](./media/publishvm_034.png)

6. Zobrazí se dialogové okno **Sdílený přístupový podpis.** Zadejte hodnoty pro následující pole:
   - **Čas zahájení** – datum zahájení oprávnění pro přístup k virtuálnímu pevnému disku. Zadejte datum, které je jeden den před aktuálním datem.
   - **Doba platnosti** - datum vypršení platnosti oprávnění pro přístup k virtuálnímu pevnému disku.  Zadejte datum nejméně tři týdny po aktuálním datu.
   - **Oprávnění** – `Read` vyberte `List` a oprávnění.

     ![Dialogové okno SAS v Průzkumníkovi Azure](./media/publishvm_035.png)

7. Kliknutím na **Vytvořit** vytvořte přidružený identifikátor URI SAS pro tento virtuální pevný disk.  Dialogové okno nyní zobrazuje podrobnosti o této operaci.
8. Zkopírujte hodnotu **adresy URL** a uložte ji do textového souboru na bezpečném místě.

    ![Vytvoření identifikátoru URI sas v Průzkumníkovi Azure](./media/publishvm_036.png)

    Tato vygenerovaná adresa URL SAS je pro přístup na úrovni kontejneru.  Aby byl název přidruženého virtuálního pevného disku konkrétní, musí k němu být přidán přidružený název virtuálního pevného disku.

9. Upravte textový soubor. Za řetězec vložte `vhds` název virtuálního pevného disku do adresy URL SAS (včetně úvodního lomítka).  Konečný identifikátor URI SAS by měl mít formát:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Pokud je `TestRGVM2.vhd`například název VDH , bude výsledný identifikátor URI SAS:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Opakujte tyto kroky pro každý virtuální pevný disk v sku, které chcete publikovat.

## <a name="verify-the-sas-uri"></a>Ověření identifikátoru URI SAS

Zkontrolujte a ověřte každý vygenerovaný identifikátor URI s rozhraním SAS pomocí následujícího kontrolního seznamu.  Ověřte, zda:

- Identifikátor URI je formuláře: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` +`<sas-connection-string>`
- Identifikátor URI obsahuje název souboru obrázku v pevném rozlišení, včetně přípony názvu souboru ".vhd".
- Směrem ke středu identifikátoru URI se `sp=rl` zobrazí. Tento řetězec `Read` označuje, že a `List` přístup je zadán.
- Po tomto `sr=c` bodě se také objeví. Tento řetězec označuje, že je zadán přístup na úrovni kontejneru.
- Zkopírujte a vložte identifikátor URI do prohlížeče a začněte stahovat přidružený objekt blob.  (Operaci můžete zrušit před dokončením stahování.)

## <a name="next-steps"></a>Další kroky

Pokud máte potíže s generováním identifikátoru URI SAS, přečtěte [si téma Běžné problémy s adresou URL SAS](./cpp-common-sas-url-issues.md).  V opačném případě uložte identifikátory URI SAS do zabezpečeného umístění pro pozdější použití. Bude nutné [publikovat nabídku virtuálních počítačích](./cpp-publish-offer.md) na portálu partnerů cloudu.
