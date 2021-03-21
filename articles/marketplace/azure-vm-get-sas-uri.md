---
title: Vygenerování identifikátoru URI SAS pro bitovou kopii virtuálního počítače – Azure Marketplace
description: Vygenerujte identifikátor URI sdíleného přístupového podpisu (SAS) pro virtuální pevné disky (VHD) v Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 976d1dd4044649c9ef5307431c744ff3c2068bca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103233561"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Jak vygenerovat identifikátor URI SAS pro image virtuálního počítače

> [!NOTE]
> K publikování virtuálního počítače nepotřebujete identifikátor URI SAS. Obrázek můžete jednoduše sdílet v části centrum. Informace najdete v tématu [Vytvoření virtuálního počítače pomocí schválené základny](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-approved-base) nebo [Vytvoření virtuálního počítače s využitím vlastních pokynů k imagi](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-own-image) .

Generování identifikátorů URI SAS pro vaše virtuální pevné disky má tyto požadavky:

- Podporují jenom nespravované virtuální pevné disky.
- Vyžadují se jenom oprávnění list a čtení. Nezadávejte přístup pro zápis ani odstranění.
- Doba trvání přístupu (datum vypršení platnosti) by měla být minimálně tři týdny od okamžiku, kdy se identifikátor URI SAS vytvoří.
- Pro ochranu proti změnám času UTC nastavte počáteční datum na jeden den před aktuálním datem. Pokud je aktuální datum například 16. června 2020, vyberte 6/15/2020.

## <a name="extract-vhd-from-a-vm"></a>Extrakce virtuálního pevného disku z virtuálního počítače

> [!NOTE]
> Tento krok můžete přeskočit, pokud už máte nahraný VHD v účtu úložiště.

K extrakci virtuálního pevného disku z virtuálního počítače je potřeba pořídit snímek disku virtuálního počítače a extrahovat z něj virtuální pevný disk.

Začněte pořizováním snímku disku virtuálního počítače:

1. Přihlaste se k webu Azure Portal.
2. V levém horním rohu vyberte vytvořit prostředek, vyhledejte a vyberte snímek.
3. V okně snímek vyberte vytvořit.
4. Zadejte název snímku.
5. Vyberte existující skupinu prostředků nebo zadejte název nového.
6. Pro zdrojový disk vyberte spravovaný disk, který se má snímek.
7. Vyberte typ účtu, který chcete použít k uložení snímku. Použijte HDD úrovně Standard, pokud ho nepotřebujete, aby byl uložený na disku SSD s vysokou úrovní.
8. Vyberte Vytvořit.

### <a name="extract-the-vhd"></a>Extrakce VHD

Pomocí následujícího skriptu exportujte snímek do virtuálního pevného disku v účtu úložiště.

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá následující příkazy k vygenerování identifikátoru URI SAS pro snímek a zkopírování základního virtuálního pevného disku do účtu úložiště pomocí identifikátoru URI SAS. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.


|Příkaz  |Poznámky  |
|---------|---------|
| az disk grant-access    |     Vygeneruje sdílený přístupový podpis jen pro čtení, který se použije ke zkopírování základního souboru VHD do účtu úložiště nebo jeho stažení do místního prostředí.    |
|  az storage blob copy start   |    Asynchronně zkopíruje objekt BLOB z jednoho účtu úložiště do druhého. Použijte AZ Storage BLOB show a ověřte stav nového objektu BLOB.     |
|

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
