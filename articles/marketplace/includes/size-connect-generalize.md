---
title: zahrnout soubor
description: " – soubor"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: ecbafe0d3f39b1bd6f7c494695ea17e067f0c79e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129271"
---
## <a name="generalize-the-image"></a>Generalizace bitové kopie

Všechny obrázky v Azure Marketplace musí být obecně znovu použitelné. K tomu je potřeba, aby byl virtuální pevný disk operačního systému zobecněný, operace, která odebere všechny identifikátory jednotlivých instancí a softwarové ovladače z virtuálního počítače.

### <a name="for-windows"></a>Pro Windows

Disky s operačním systémem Windows jsou zobecněny pomocí nástroje [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Pokud později aktualizujete nebo znovu nakonfigurujete operační systém, musíte znovu spustit nástroj Sysprep.

> [!WARNING]
> Po spuštění nástroje Sysprep vypněte virtuální počítač, dokud nebude nasazen, protože aktualizace mohou být spouštěny automaticky. Toto vypnutí zabrání následným aktualizacím v provádění změn specifických pro konkrétní instance v operačním systému nebo nainstalovaných službách. Další informace o spuštění nástroje Sysprep najdete v tématu [postup generalizace VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Pro Linux

Následující proces generalizuje virtuální počítač Linux a znovu ho nasadí jako samostatný virtuální počítač. Podrobnosti najdete v tématu [Postup vytvoření image virtuálního počítače nebo virtuálního pevného disku](../../virtual-machines/linux/capture-image.md). V případě, že se dostanete k části s názvem vytvoření virtuálního počítače ze zaznamenané bitové kopie, můžete zastavit.

1. Odeberte agenta Azure Linux.
    1. Připojte se k VIRTUÁLNÍmu počítači se systémem Linux pomocí klienta SSH.
    2. V okně SSH zadejte tento příkaz: `sudo waagent –deprovision+user` .
    3. Zadejte Y pro pokračování (můžete přidat parametr-Force k předchozímu příkazu, abyste se vyhnuli potvrzovacímu kroku).
    4. Po dokončení příkazu zadejte **Exit** a zavřete tak klienta ssh.
2. Zastavte virtuální počítač.
    1. V Azure Portal vyberte skupinu prostředků (RG) a zrušte přidělení virtuálního počítače.
    2. Váš virtuální počítač je teď zobecněný a můžete vytvořit nový virtuální počítač pomocí tohoto disku virtuálního počítače.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Pořízení snímku disku virtuálního počítače

1. Přihlaste se na [Azure Portal](https://ms.portal.azure.com/).
2. V levém horním rohu vyberte **vytvořit prostředek** , vyhledejte a vyberte **snímek** .
3. V okně snímek vyberte  **vytvořit** .
4. Zadejte **název** snímku.
5. Vyberte existující skupinu prostředků nebo zadejte název nového.
6. Pro **zdrojový disk** vyberte spravovaný disk, který se má snímek.
7. Vyberte **typ účtu** , který chcete použít k uložení snímku. Použijte **HDD úrovně Standard** , pokud ho nepotřebujete, aby byl uložený na disku SSD s vysokou úrovní.
8. Vyberte **Vytvořit** .

#### <a name="extract-the-vhd"></a>Extrakce VHD

Pomocí následujícího skriptu exportujte snímek do virtuálního pevného disku v účtu úložiště.

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $ snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vygenerování identifikátoru URI SAS pro snímek a zkopírování základního virtuálního pevného disku do účtu úložiště pomocí identifikátoru URI SAS. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
| --- | --- |
| az disk grant-access | Vygeneruje sdílený přístupový podpis jen pro čtení, který se použije ke zkopírování základního souboru VHD do účtu úložiště nebo jeho stažení do místního prostředí.
| az storage blob copy start | Asynchronně zkopíruje objekt BLOB z jednoho účtu úložiště do druhého. Slouží `az storage blob show` ke kontrole stavu nového objektu BLOB. |
|