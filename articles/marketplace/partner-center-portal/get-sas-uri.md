---
title: Získání identifikátoru URI sdíleného přístupového podpisu pro obrázek virtuálního počítače | Azure Marketplace
description: Tento článek vysvětluje, jak získat identifikátor URI sdílený přístupový podpis (SAS) pro každý virtuální pevný disk (VHD).
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732642"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Získání identifikátoru URI sdíleného přístupového podpisu pro bitovou kopii virtuálního počítače

> [!IMPORTANT]
> Přesouváme správu nabídek virtuálních strojů Azure z portálu partnerů cloudu do Centra partnerů. Dokud se vaše nabídky nebudou migrovat, postupujte podle pokynů v části [Získat identifikátor URI sdíleného přístupového podpisu pro image virtuálního počítače](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) pro portál cloudových partnerů pro správu nabídek.

Tento článek popisuje, jak generovat identifikátor jednotného prostředku (SAS) sdíleného přístupového podpisu (SAS) pro každý virtuální pevný disk (VHD).

Během procesu publikování je nutné zadat identifikátor URI pro každý virtuální pevný disk, který je přidružen k vašim plánům. Tyto plány byly dříve označovány jako skladové jednotky nebo skladové jednotky. Společnost Microsoft potřebuje přístup k těmto virtuálním serverům během procesu certifikace. Tento identifikátor URI zadáte na kartě **Plány** v Centru partnerů.

Při generování identifikátorů URI SAS pro virtuální počítače postupujte podle následujících požadavků:

* Podporovány jsou pouze nespravované virtuální disponály.
* Pouze `List` `Read` a oprávnění jsou vyžadována. Nezaručujte přístup k zápisu ani odstranění.
* Doba trvání přístupu (datum vypršení platnosti) by měla být minimálně tři týdny od vytvoření identifikátoru URI SAS.
* Chcete-li se chránit před změnami času UTC, nastavte počáteční datum na jeden den před aktuálním datem. Pokud je například aktuální datum 6.

## <a name="generate-the-sas-address"></a>Generovat adresu SAS

Existují dva běžné nástroje používané k vytvoření adresy SAS (URL):

* **Microsoft Storage Explorer** – Grafický nástroj dostupný pro Windows, macOS a Linux.
* **Rozhraní příkazového odpočitatelství Microsoft Azure** – doporučeno pro operační systémy, které nejsou systémy Windows, a pro automatizovaná nebo průběžná integrační prostředí.

### <a name="use-microsoft-storage-explorer"></a>Použití Průzkumníka úložišť Microsoft

1. Stáhněte a nainstalujte [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Otevřete průzkumníka a v levé nabídce vyberte **Přidat účet**. Zobrazí se dialogové okno **Připojit k úložišti Azure.**
3. Vyberte **Přidat účet Azure** a pak se **Přihlásit**. Proveďte požadované kroky pro přihlášení k účtu Azure.
4. V levém podokně**Průzkumníkpřejděte** na **účty úložiště** a rozbalte tento uzel.
5. Klikněte pravým tlačítkem myši na virtuální pevný disk a potom vyberte **získat podpis ke sdílené položce**.
6. Zobrazí se dialogové okno **Sdílený přístupový podpis.** Vyplňte následující pole:

    * **Čas zahájení** – datum zahájení oprávnění pro přístup k virtuálnímu pevnému disku. Zadejte datum, které je jeden den před aktuálním datem.
    * **Doba platnosti** – datum vypršení platnosti oprávnění pro přístup k virtuálnímu pevnému disku. Zadejte datum, které je nejméně tři týdny po aktuálním datu.
    * **Oprávnění** – Vyberte oprávnění ke čtení a seznamu.
    * **Úroveň kontejneru** – zaškrtněte políčko Generovat identifikátor **URI na úrovni sdíleného přístupového podpisu na úrovni kontejneru.**

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Ilustruje dialogové okno Sdílený přístupový podpis.":::

7. Chcete-li vytvořit přidružený identifikátor URI s as pro tento virtuální pevný disk, vyberte **příkaz Vytvořit**. Dialogové okno aktualizuje a zobrazuje podrobnosti o této operaci.
8. Zkopírujte **identifikátor URI** a uložte jej do textového souboru na bezpečném místě.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Znázorňuje pole Podrobnosti o sdíleném přístupovém podpisu.":::

    Tento generovaný identifikátor URI s názvem SAS je určen pro přístup na úrovni kontejneru. Chcete-li, aby byl specifický, upravte textový soubor a přidejte název virtuálního pevného disku (další krok).

9. Za řetězec vhds vložte název virtuálního pevného disku do identifikátoru URI srozhraním SAS (včetně lomítka). Konečný identifikátor URI SAS by měl vypadat takto:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Pokud je `TestRGVM2.vhd`například název VDH , bude výsledný identifikátor URI SAS:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Opakujte tyto kroky pro každý virtuální pevný disk v plánech, které budete publikovat.

### <a name="using-azure-cli"></a>Použití Azure CLI

1. Stáhněte a nainstalujte [rozhraní příkazového příkazu Kodmicrosoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Verze jsou k dispozici pro Windows, macOS a různé distribuce Linuxu.
2. Vytvořte soubor powershellu (přípona souboru PS1), zkopírujte v následujícím kódu a uložte ho místně.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Upravte soubor tak, aby používal následující hodnoty parametrů. Zadejte data ve formátu Datetime `2020-04-01T00:00:00Z`uTC, například .

    * `<account-name>`– Název účtu úložiště Azure
    * `<account-key>`– Klíč účtu úložiště Azure
    * `<vhd-name>`– Vaše jméno VHD
    * `<start-date>`– Datum zahájení oprávnění pro přístup k Virtuálnímu pevnému disku. Zadejte datum jeden den před aktuálním datem.
    * `<expiry-date>`– Datum vypršení platnosti oprávnění pro přístup k virtuálnímu pevnému disku. Zadejte datum nejméně tři týdny po aktuálním datu.

    Tento příklad ukazuje správné hodnoty parametrů (v době psaní tohoto:

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Uložte změny.
5. Pomocí jedné z následujících metod spusťte tento skript s oprávněními správce a vytvořte **připojovací řetězec SAS** pro přístup na úrovni kontejneru:

    * Spusťte skript z konzole. V systému Windows klepněte pravým tlačítkem myši na skript a vyberte **příkaz Spustit jako správce**.
    * Spusťte skript z editoru skriptů prostředí PowerShell, jako je [prostředí Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Tato obrazovka zobrazuje vytvoření připojovacího řetězce SAS v tomto editoru:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Ilustruje vytvoření připojovacího řetězce SAS pomocí prostředí Windows PowerShell ISE.":::

6. Zkopírujte připojovací řetězec SAS a uložte jej do textového souboru na bezpečném místě. Upravte tento řetězec a přidejte informace o umístění virtuálního pevného disku a vytvořte poslední identifikátor URI SAS.
7. Na webu Azure Portal přejděte do úložiště objektů blob, které zahrnuje virtuální pevný disk přidružený k novému identifikátoru URI.
8. Zkopírujte adresu URL **koncového bodu služby Blob**, jak je znázorněno na následujícím snímku obrazovky

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Ilustruje koncový bod služby Blob.":::

9. Upravte textový soubor pomocí připojovacího řetězce SAS z kroku 6. Vytvořte úplný identifikátor URI s pomocí tohoto formátu:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Pokud je `TestRGVM2.vhd`například název virtuálního pevného disku , identifikátor URI SAS bude:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Opakujte tyto kroky pro každý virtuální pevný disk v sku, které chcete publikovat.

## <a name="verify-the-sas-uri"></a>Ověření identifikátoru URI SAS

Zkontrolujte každý vytvořený identifikátor URI s postupem od SAS pomocí následujícího kontrolního seznamu a ověřte, zda:

* Identifikátor URI vypadá takto:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* Identifikátor URI obsahuje název souboru obrázku v pevném rozlišení, včetně přípony názvu souboru ".vhd".
* `sp=rl`se zobrazí uprostřed identifikátoru URI. Tento řetězec `Read` ukazuje, že a `List` přístup je zadán.
* Pokud `sr=c` se zobrazí, znamená to, že je zadán přístup na úrovni kontejneru.
* Zkopírujte a vložte identifikátor URI do prohlížeče a otestujte a stáhněte objekt blob (operaci můžete před dokončením stahování zrušit).

## <a name="next-step"></a>Další krok

Pokud máte potíže s vytvořením identifikátoru URI SAS, přečtěte [si téma Běžné problémy s adresou URL SAS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues). V opačném případě uložte identifikátory URI SAS do zabezpečeného umístění pro pozdější použití. Budete ji potřebovat k publikování nabídky virtuálních počítačích v Partnerském centru.

* [Vytvoření nabídky virtuálních počítačů Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
