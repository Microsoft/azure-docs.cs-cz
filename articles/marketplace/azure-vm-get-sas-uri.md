---
title: Vygenerování identifikátoru URI SAS pro bitovou kopii virtuálního počítače – Azure Marketplace
description: Vygenerujte identifikátor URI sdíleného přístupového podpisu (SAS) pro virtuální pevné disky (VHD) v Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 1/5/2021
ms.openlocfilehash: 560699296b8cae83413c36820106eedf7fef7414
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914157"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Jak vygenerovat identifikátor URI SAS pro image virtuálního počítače

Během procesu publikování musíte pro každý virtuální pevný disk, který je přidružený k vašim plánům (dříve označovaným jako SKU), zadat identifikátor URI SAS (Shared Access Signature). Společnost Microsoft potřebuje během procesu certifikace přístup k těmto virtuálním pevným diskům. Tento identifikátor URI zadáte na kartě **plány** v partnerském centru.

Generování identifikátorů URI SAS pro vaše virtuální pevné disky má tyto požadavky:

- Podporují jenom nespravované virtuální pevné disky.
- Vyžadují se jenom oprávnění list a čtení. Nezadávejte přístup pro zápis ani odstranění.
- Doba trvání přístupu (datum vypršení platnosti) by měla být minimálně tři týdny od okamžiku, kdy se identifikátor URI SAS vytvoří.
- Pro ochranu proti změnám času UTC nastavte počáteční datum na jeden den před aktuálním datem. Pokud je aktuální datum například 16. června 2020, vyberte 6/15/2020.

## <a name="generate-the-sas-address"></a>Vygenerovat adresu SAS

Existují dva běžné nástroje, které slouží k vytvoření adresy SAS (URL):

1. **Průzkumník služby Azure Storage** – k dispozici v Azure Portal.
2. Rozhraní příkazového **řádku Azure** – doporučuje se pro operační systémy jiné než Windows a automatizované nebo nepřetržité integrační prostředí.

### <a name="using-tool-1-azure-storage-explorer"></a>Použití nástroje 1: Průzkumník služby Azure Storage

1. Přejít na **účet úložiště**.
1. Otevřete **Průzkumník služby Storage**.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Okno účtu úložiště.":::

3. V **kontejneru** klikněte pravým tlačítkem na soubor VHD a vyberte **získat přístupový podpis sdílení**.
4. V dialogovém okně **sdílený přístupový podpis** vyplňte následující pole:

    1. Čas spuštění – datum zahájení oprávnění pro přístup VHD. Zadejte datum, které je před aktuálním datem jeden den.
    2. Čas vypršení platnosti – datum vypršení platnosti oprávnění pro přístup VHD. Zadejte datum nejméně tři týdny po aktuálním datu.
    3. Oprávnění – vyberte oprávnění číst a seznam.
    4. Na úrovni kontejneru – zaškrtněte políčko Generovat identifikátor URI sdíleného přístupového podpisu na úrovni kontejneru.

    ![Dialogové okno sdílený přístupový podpis.](media/vm/create-sas-uri-storage-explorer.png)

5. Pokud chcete vytvořit přidružený identifikátor URI SAS pro tento virtuální pevný disk, vyberte **vytvořit**.
6. Zkopírujte identifikátor URI a uložte ho do textového souboru v zabezpečeném umístění. Tento vygenerovaný identifikátor URI SAS je pro přístup na úrovni kontejneru. Pokud to chcete udělat, upravte textový soubor a přidejte název VHD.
7. Po řetězci VHD v identifikátoru URI SAS vložte název virtuálního pevného disku (včetně lomítka). Výsledný identifikátor URI SAS by měl vypadat takto:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Tento postup opakujte pro každý virtuální pevný disk v plánech, které budete publikovat.

### <a name="using-tool-2-azure-cli"></a>Použití nástroje 2: Azure CLI

1. Stáhněte a nainstalujte [Microsoft Azure CL](/cli/azure/install-azure-cli)I. Verze jsou k dispozici pro Windows, macOS a různé distribucey systému Linux.
2. Vytvořte soubor PowerShellu (Přípona souboru. ps1), zkopírujte následující kód a pak ho uložte místně.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Upravte soubor tak, aby používal následující hodnoty parametrů. Zadejte data ve formátu UTC UTC, například 2020-04-01T00:00:00Z.

    - název účtu – název účtu úložiště Azure.
    - klíč účtu – klíč účtu úložiště Azure.
    - počáteční datum – počáteční datum oprávnění pro přístup VHD. Zadejte datum jeden den před aktuálním datem.
    - Datum vypršení platnosti – datum vypršení platnosti oprávnění pro přístup VHD. Zadejte datum nejméně tři týdny po aktuálním datu.

    Tady je příklad správných hodnot parametrů (v době psaní tohoto zápisu):

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. Uložte změny.
2. Pomocí jedné z následujících metod spusťte tento skript s oprávněními správce a vytvořte připojovací řetězec SAS pro přístup na úrovni kontejneru:

    - Spusťte skript z konzoly nástroje. V systému Windows klikněte pravým tlačítkem na skript a vyberte **Spustit jako správce**.
    - Spusťte skript z editoru skriptu prostředí PowerShell, jako je například [Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Tato obrazovka ukazuje vytvoření připojovacího řetězce SAS v rámci tohoto editoru:

    [![vytvoření připojovacího řetězce SAS v editoru prostředí PowerShell](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Zkopírujte připojovací řetězec SAS a uložte ho do textového souboru v zabezpečeném umístění. Úpravou tohoto řetězce přidejte informace o umístění virtuálního pevného disku pro vytvoření konečného identifikátoru URI SAS.
7. V Azure Portal přejdete do úložiště objektů blob, které obsahuje virtuální pevný disk přidružený k novému identifikátoru URI.
8. Zkopírujte adresu URL koncového bodu služby BLOB Service:

    ![Kopíruje se adresa URL koncového bodu služby BLOB Service.](media/vm/create-sas-uri-blob-endpoint.png)

9. Upravte textový soubor s připojovacím řetězcem SAS z kroku 6. Vytvořte úplný identifikátor URI SAS v tomto formátu:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Ověření identifikátoru URI SAS

Před publikováním v partnerském centru Ověřte identifikátor URI SAS, abyste se vyhnuli jakýmkoli problémům souvisejícím s identifikátorem URI SAS po odeslání žádosti. Tento postup je nepovinný, ale doporučuje se.

- Identifikátor URI zahrnuje název souboru bitové kopie VHD, včetně přípony názvu souboru `.vhd` .
- `Sp=rl` zobrazí se poblíž středu vašeho identifikátoru URI. Tento řetězec zobrazuje zadaný přístup pro čtení a seznam.
- Když `sr=c` se zobrazí, znamená to, že je určený přístup na úrovni kontejneru.
- Zkopírování a vložení identifikátoru URI do prohlížeče pro testování – stažení objektu BLOB (tuto operaci můžete zrušit před dokončením stahování).

## <a name="next-steps"></a>Další kroky

- Pokud narazíte na problémy, přečtěte si téma [zprávy o selhání SAS virtuálních počítačů](azure-vm-sas-failure-messages.md).
- [Přihlásit se k partnerskému centru](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Vytvoření nabídky virtuálního počítače na Azure Marketplace](azure-vm-create.md)
