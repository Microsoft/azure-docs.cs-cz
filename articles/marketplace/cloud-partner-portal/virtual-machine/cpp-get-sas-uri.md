---
title: Získání sdíleného přístupového podpisu URI pro vaši image virtuálního počítače založené na Azure Microsoftu | Dokumentace Microsoftu
description: Vysvětluje, jak získat sdílený přístupový podpis (SAS) identifikátor URI pro vaši image virtuálního počítače.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: dcfe744cc8ca6f3b3cd201898a79fcce3f24f8d5
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639622"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Získání sdíleného přístupového podpisu URI pro vaši image virtuálního počítače

Během procesu publikování je nutné zadat identifikátor URI (URI) pro každý virtuální pevný disk (VHD) přidružené k skladové jednotky. Microsoft během certifikačního procesu potřebuje přístup k těmto virtuálním pevným diskům. Tento článek vysvětluje, jak vygenerovat sdílený přístupový podpis (SAS) identifikátor URI pro každý virtuální pevný disk. Zadejte, který bude tento identifikátor URI v **skladové položky** karta na portálu Cloud Partner. 

Při generování identifikátorů URI SAS pro vaše virtuální pevné disky, splnit následující požadavky:

- Podporují se jenom nespravované virtuální pevné disky.
- `List` a `Read` jsou dostatečná oprávnění. Proveďte *není* poskytují `Write` nebo `Delete` přístup.
- Doba trvání přístupu (*datum vypršení platnosti*) by měla být minimálně tří týdnů od vytvoření SAS URI.
- Aby se předešlo variace čas UTC, nastavte počáteční datum na jeden den před aktuálním datem. Například pokud je aktuální datum 6. října 2014, vyberte 10 5. 2014.

## <a name="generate-the-sas-url"></a>Generování adresy URL SAS

Adresa URL SAS můžete vygenerovat dvě běžné možnosti, jak pomocí následujících nástrojů:

-   Průzkumník služby Microsoft Storage – grafické nástroje, které jsou k dispozici pro Windows, macOS a Linux
-   Microsoft Azure CLI – doporučuje se pro Windows OSs a prostředí pro automatizované nebo průběžné integrace


### <a name="azure-cli"></a>Azure CLI

Pomocí následujících kroků vygenerovat identifikátor URI SAS pomocí Azure CLI.

1.  Stáhněte a nainstalujte [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  Verze jsou k dispozici pro Windows, macOS a různých distribucích systému Linux. 
2.  Vytvořte soubor prostředí PowerShell (`.ps1` přípony souboru), zkopírujte následující kód a uložte ho místně.

    ``` powershell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```
    
3.  Upravte soubor a zadejte následující hodnoty parametrů.  Data by měla zadat ve formátu data a času UTC, například `10-25-2016T00:00:00Z`.
    - `<account-name>` – Název účtu úložiště Azure
    - `<account-key>` -Klíče účtu úložiště Azure
    - `<vhd-name>` – Název virtuálního pevného disku
    - `<start-date>` -Oprávnění počáteční datum pro přístup k virtuální pevný disk. Zadejte datum o jeden den před aktuálním datem. 
    - `<expiry-date>` -Datum vypršení platnosti oprávnění pro přístup k virtuální pevný disk.  Zadejte datum nejméně tři týdny po aktuálním datu. 
 
    Následující příklad ukazuje hodnoty správné parametrů (v době psaní tohoto textu).

    ``` powershell
        az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
    ```
 
4. Uložte změny tohoto skriptu prostředí PowerShell.
5. Spusťte tento skript k vygenerování s oprávněním správce, *připojovací řetězec SAS* pro úrovně přístupu ke kontejneru.  Můžete použít dva základní přístupy:
    - Spusťte skript z konzoly.  Například ve Windows, zápis kliknutím na skriptu a vyberte **spustit jako správce**.
    - Spusťte skript z editoru skriptů Powershellu, jako [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise), s oprávněním správce. 
  Následující příklad ukazuje připojovací řetězec SAS se vygeneruje v rámci tohoto editoru. 

    ![Generování SAS URI v prostředí PowerShell ISE](./media/publishvm_032.png)

6. Zkopírujte výsledný řetězec připojení SAS a uložte ho do textového souboru v zabezpečeném umístění.  Upravíte tento řetězec přidat související informace pro umístění virtuálního pevného disku k němu chcete-li vytvořit finální identifikátor URI SAS. 
7. Na webu Azure Portal přejděte do úložiště objektů blob, který obsahuje související s nově vygenerovaný identifikátor URI virtuálního pevného disku.
8. Zkopírujte hodnotu adresy URL **koncový bod služby Blob service**, jak je znázorněno níže.

    ![Koncový bod služby BLOB service na webu Azure portal](./media/publishvm_033.png)

9. Upravte textový soubor s připojovacím řetězcem SAS v kroku 6.  Vytvoří úplný identifikátor URI SAS v následujícím formátu:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Například, pokud je název VDH `TestRGVM2.vhd`, pak by byl výsledný identifikátor URI SAS:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Tento postup opakujte pro každý virtuální pevný disk ve skladové položky, které chcete publikovat.


### <a name="microsoft-storage-explorer"></a>Průzkumník úložišť Microsoft

Pomocí následujících kroků vygenerovat identifikátor URI SAS pomocí Průzkumníka služby Microsoft Azure Storage.

1. Stáhnout a nainstalovat [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Otevřete Průzkumníka a na levém řádku nabídek klikněte na **přidat účet** ikonu.  **Připojit ke službě Azure Storage** se zobrazí dialogové okno.
3. Vyberte **Přidat účet Azure** a klikněte na **Přihlásit se**.  Pokračujte kroky potřebné k přihlášení ke svému účtu Azure.
4. V levé straně **Explorer** podokně přejděte do vaší **účty úložiště** a rozbalit tento uzel.
5. Klikněte pravým tlačítkem na virtuální pevný disk a vyberte **získat přístupový podpis sdílenou složku** v místní nabídce. 

    ![Získání položky SAS v Průzkumníku služby Azure](./media/publishvm_034.png)

6. **Sdílený přístupový podpis** se zobrazí dialogové okno. Zadejte hodnoty pro následující pole:
    - **Počáteční čas** -oprávnění počáteční datum pro přístup k virtuální pevný disk. Zadejte datum, který je za jeden den před aktuálním datem.
    - **Čas vypršení platnosti** -datum vypršení platnosti oprávnění pro přístup k virtuální pevný disk.  Zadejte datum nejméně tři týdny po aktuálním datu.
    - **Oprávnění** – tuto možnost vyberte `Read` a `List` oprávnění. 

    ![Dialogové okno SAS v Průzkumníku služby Azure](./media/publishvm_035.png)

7. Klikněte na tlačítko **vytvořit** vytvořit přidružen k identifikátoru URI SAS pro tento virtuální pevný disk.  Dialogové okno se teď zobrazují podrobnosti o této operaci. 
8. Kopírovat **URL** hodnotu a uložte ho do textového souboru v zabezpečeném umístění. 

    ![Identifikátor URI SAS vytvořit v Průzkumníku služby Azure](./media/publishvm_036.png)

    Je tento vygenerovaný adresa URL SAS pro přístup na úrovni kontejneru.  Chcete-li konkrétní, musí název přidružené virtuální pevný disk přidat do ní.

9. Upravte textový soubor. Vložit název vašeho virtuálního pevného disku po `vhds` řetězec v adrese URL SAS (obsahují počáteční lomítko).  Poslední identifikátor URI SAS by měl být ve formátu:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Například, pokud je název VDH `TestRGVM2.vhd`, pak by byl výsledný identifikátor URI SAS:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Tento postup opakujte pro každý virtuální pevný disk ve skladové položky, které chcete publikovat.


## <a name="verify-the-sas-uri"></a>Ověřte, že identifikátor URI SAS

Zkontrolujte a ověřte, že každá vygenerovaná identifikátor URI SAS pomocí následujícího kontrolního seznamu.  Ověřte, že:
- Identifikátor URI je ve formátu:       `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- Identifikátor URI obsahuje název vašeho virtuálního pevného disku obrázku, včetně přípony názvu souboru "VHD".
- Směrem k středu identifikátoru URI `sp=rl` se zobrazí. Tento řetězec znamená, že `Read` a `List` zadaný přístup.
- Od této chvíle `sr=c` se také zobrazí. Tento řetězec označuje, zda je zadán přístup na úrovni kontejneru.
- Zkopírujte a vložte identifikátor URI do prohlížeče, abyste začali stáhnout přidružený objekt blob.  (Můžete zrušit operaci před dokončením stahování.)


## <a name="next-steps"></a>Další postup

Pokud máte potíže při generování identifikátorů URI SAS, přejděte na téma [běžné SAS URL vystavuje](./cpp-common-sas-url-issues.md).  V opačném případě uložte SAS URI(s) do zabezpečeného umístění pro pozdější použití. Bude potřeba [publikování vaší nabídky virtuálních počítačů](./cpp-publish-offer.md) v portál partnerů cloudu.
