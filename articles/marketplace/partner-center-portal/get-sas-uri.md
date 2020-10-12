---
title: Získání identifikátoru URI SAS pro bitovou kopii virtuálního počítače – Azure Marketplace
description: Vygenerujte identifikátor URI sdíleného přístupového podpisu (SAS) pro vaše virtuální pevné disky (VHD) v Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89144490"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>Získání identifikátoru URI SAS pro vaši image virtuálního počítače

Během procesu publikování musíte pro každý virtuální pevný disk, který je přidružený k vašim plánům (dříve označovaným jako SKU), zadat identifikátor URI SAS (Shared Access Signature). Společnost Microsoft potřebuje během procesu certifikace přístup k těmto virtuálním pevným diskům. Tento identifikátor URI zadáte na kartě **plány** v partnerském centru.

Generování identifikátorů URI SAS pro vaše virtuální pevné disky má tyto požadavky:

- Podporují jenom nespravované virtuální pevné disky.
- Vyžadují se jenom oprávnění list a čtení. Nezadávejte přístup pro zápis ani odstranění.
- Doba trvání přístupu (datum vypršení platnosti) by měla být minimálně tři týdny od okamžiku, kdy se identifikátor URI SAS vytvoří.
- Pro ochranu proti změnám času UTC nastavte počáteční datum na jeden den před aktuálním datem. Pokud je aktuální datum například 16. června 2020, vyberte 6/15/2020.

## <a name="generate-the-sas-address"></a>Vygenerovat adresu SAS

Existují dva běžné nástroje, které slouží k vytvoření adresy SAS (URL):

1. **Microsoft Průzkumník služby Storage** – grafický nástroj dostupný pro Windows, MacOS a Linux.
2. **Microsoft Azure CLI** – doporučeno pro operační systémy jiné než Windows a automatizované nebo nepřetržité integrační prostředí.

### <a name="using-tool-1-microsoft-storage-explorer"></a>Použití nástroje 1: Microsoft Průzkumník služby Storage

1. Stáhněte a nainstalujte [Průzkumník služby Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
2. Otevřete Průzkumníka a v nabídce vlevo vyberte **Přidat účet**.
3. V dialogovém okně **připojit k Azure Storage** vyberte **Přidat účet Azure** a přihlaste se ke svému účtu Azure.
4. V levém podokně Průzkumník rozbalte uzel **účty úložiště** .
5. Klikněte pravým tlačítkem na virtuální pevný disk a vyberte **získat přístupový podpis sdílení**.
6. V dialogovém okně **sdílený přístupový podpis** vyplňte následující pole:

    1. Čas spuštění – datum zahájení oprávnění pro přístup VHD. Zadejte datum, které je před aktuálním datem jeden den.
    2. Čas vypršení platnosti – datum vypršení platnosti oprávnění pro přístup VHD. Zadejte datum nejméně tři týdny po aktuálním datu.
    3. Oprávnění – vyberte oprávnění číst a seznam.
    4. Na úrovni kontejneru – zaškrtněte políčko Generovat identifikátor URI sdíleného přístupového podpisu na úrovni kontejneru.

    ![Dialogové okno sdílený přístupový podpis.](media/vm/create-sas-uri-storage-explorer.png)

7. Pokud chcete vytvořit přidružený identifikátor URI SAS pro tento virtuální pevný disk, vyberte **vytvořit**. Dialogové okno se aktualizuje a zobrazí podrobnosti o této operaci.

8. Zkopírujte identifikátor URI a uložte ho do textového souboru v zabezpečeném umístění.

    ![Identifikátor URI se kopíruje.](media/vm/create-sas-uri-shared-access-signature-details.png)

    Tento vygenerovaný identifikátor URI SAS je pro přístup na úrovni kontejneru. Pokud to chcete udělat, upravte textový soubor a přidejte název VHD.

9. Po řetězci VHD v identifikátoru URI SAS vložte název virtuálního pevného disku (včetně lomítka). Výsledný identifikátor URI SAS by měl vypadat takto:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. Tento postup opakujte pro každý virtuální pevný disk v plánech, které budete publikovat.

### <a name="using-tool-2-azure-cli"></a>Použití nástroje 2: Azure CLI

1. Stáhněte a nainstalujte [Microsoft Azure CL](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)I. Verze jsou k dispozici pro Windows, macOS a různé distribucey systému Linux.
2. Vytvořte soubor PowerShellu (Přípona souboru. ps1), zkopírujte následující kód a pak ho uložte místně.

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Upravte soubor tak, aby používal následující hodnoty parametrů. Zadejte data ve formátu UTC UTC, například 2020-04-01T00:00:00Z.

    - název účtu – název účtu úložiště Azure.
    - klíč účtu – klíč účtu úložiště Azure.
    - název virtuálního pevného disku – název virtuálního pevného disku.
    - počáteční datum – počáteční datum oprávnění pro přístup VHD. Zadejte datum jeden den před aktuálním datem.
    - Datum vypršení platnosti – datum vypršení platnosti oprávnění pro přístup VHD. Zadejte datum nejméně tři týdny po aktuálním datu.

    Tady je příklad správných hodnot parametrů (v době psaní tohoto zápisu):

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. Uložte změny.
2. Pomocí jedné z následujících metod spusťte tento skript s oprávněními správce a vytvořte připojovací řetězec SAS pro přístup na úrovni kontejneru:

    - Spusťte skript z konzoly nástroje. V systému Windows klikněte pravým tlačítkem na skript a vyberte **Spustit jako správce**.
    - Spusťte skript z editoru skriptu prostředí PowerShell, jako je například [Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Tato obrazovka ukazuje vytvoření připojovacího řetězce SAS v rámci tohoto editoru:

    [![vytvoření připojovacího řetězce SAS v editoru prostředí PowerShell](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Zkopírujte připojovací řetězec SAS a uložte ho do textového souboru v zabezpečeném umístění. Úpravou tohoto řetězce přidejte informace o umístění virtuálního pevného disku pro vytvoření konečného identifikátoru URI SAS.
7. V Azure Portal přejdete do úložiště objektů blob, které obsahuje virtuální pevný disk přidružený k novému identifikátoru URI.
8. Zkopírujte adresu URL koncového bodu služby thebBlob:

    ![Kopíruje se adresa URL koncového bodu služby BLOB Service.](media/vm/create-sas-uri-blob-endpoint.png)

9. Upravte textový soubor s připojovacím řetězcem SAS z kroku 6. Vytvořte úplný identifikátor URI SAS v tomto formátu:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Ověření identifikátoru URI SAS

Před publikováním v partnerském centru Ověřte identifikátor URI SAS, abyste se vyhnuli jakýmkoli problémům souvisejícím s identifikátorem URI SAS po odeslání žádosti. Tento postup je nepovinný, ale doporučuje se.

- Identifikátor URI zahrnuje název souboru bitové kopie VHD, včetně přípony názvu souboru `.vhd` .
- `Sp=rl` zobrazí se poblíž středu vašeho identifikátoru URI. Tento řetězec zobrazuje zadaný přístup pro čtení a seznam.
- Když `sr=c` se zobrazí, znamená to, že je určený přístup na úrovni kontejneru.
- Zkopírování a vložení identifikátoru URI do prohlížeče pro testování – stažení objektu BLOB (tuto operaci můžete zrušit před dokončením stahování).

## <a name="next-step"></a>Další krok

- Přečtěte si téma [Vytvoření nabídky virtuálních počítačů Azure](azure-vm-create-offer.md).
