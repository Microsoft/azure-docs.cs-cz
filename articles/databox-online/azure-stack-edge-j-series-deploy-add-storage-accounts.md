---
title: Kurz přenosu dat do účtu úložiště s grafickým procesorem Azure Stack Edge pro | Microsoft Docs
description: Naučte se přidávat a připojovat se k místním a hraničním účtům úložiště na zařízeních s grafickým procesorem Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: c24970b30e522d593a491194dbbabb0421ad7f4b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891054"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>Kurz: přenos dat prostřednictvím účtů úložiště s grafickým procesorem Azure Stack Edge pro 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

V tomto kurzu se dozvíte, jak přidat účty úložiště a připojit se k nim na zařízení Azure Stack Edge pro. Po přidání účtů úložiště může Azure Stack Edge pro přenést data do Azure.

Dokončení této procedury může trvat přibližně 30 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat účet úložiště
> * Připojení k účtu úložiště

 
## <a name="prerequisites"></a>Požadavky

Než přidáte účty úložiště do Azure Stack Edge pro, ujistěte se, že:

- Nainstalovali jste fyzické zařízení, jak je popsáno v tématu [instalace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-install.md).

- Aktivovali jste fyzické zařízení, jak je popsáno v tématu [aktivace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md).


## <a name="add-an-edge-storage-account"></a>Přidat hraniční účet úložiště

Chcete-li vytvořit hraniční účet úložiště, proveďte následující postup:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Připojení k účtu úložiště Edge

Nyní se můžete připojit k rozhraní REST API pro úložiště Edge přes *protokol HTTP* nebo *https*.

- *Protokol HTTPS* je zabezpečený a doporučený postup.
- *Protokol HTTP* se používá při připojování přes důvěryhodné sítě.

## <a name="connect-via-http"></a>Připojit přes http

Připojení k hraničnímu úložišti REST API přes protokol HTTP vyžaduje následující kroky:

- Přidejte virtuální IP adresu služby Azure konzistentní a koncový bod služby BLOB Service ke vzdálenému hostiteli.
- Ověření připojení 

Každý z těchto kroků je popsaný v následujících částech.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Přidat IP adresu zařízení a koncový bod služby BLOB Service do vzdáleného klienta

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Ověření připojení

K ověření připojení byste obvykle potřebovali následující informace (mohou se lišit), které jste shromáždili v předchozím kroku:

- Název účtu úložiště
- Přístupový klíč účtu úložiště.
- Blob service koncový bod.

Už máte název účtu úložiště a koncový bod služby BLOB Service. Přístup ke klíči účtu úložiště můžete získat tak, že se připojíte k zařízení prostřednictvím Azure Resource Manager pomocí klienta Azure PowerShell.

Postupujte podle kroků v části [připojení k zařízení prostřednictvím Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md). Po přihlášení k rozhraním API místních zařízení prostřednictvím Azure Resource Manager získat seznam účtů úložiště v zařízení. Spusťte následující rutinu:

`Get-AzureRMStorageAccount`

V seznamu účtů úložiště v zařízení Identifikujte účet úložiště, pro který budete potřebovat přístupový klíč. Poznamenejte si název účtu úložiště a skupinu prostředků.

Ukázkový výstup je uveden níže:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

Přístupovou klávesu získáte spuštěním následující rutiny:

`Get-AzureRmStorageAccountAccessKey`

Ukázkový výstup je uveden níže:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Zkopírujte a uložte tento klíč. Tento klíč použijete k ověření připojení pomocí Průzkumník služby Azure Storage.

Pokud chcete ověřit, že se připojení úspěšně navázalo, připojte se k externímu účtu úložiště pomocí Průzkumník služby Storage. Pokud nemáte Průzkumník služby Storage, [stáhněte Průzkumník služby Storage](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409).

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Připojit prostřednictvím protokolu https

Připojení k rozhraní REST API služby Azure Blob Storage přes HTTPS vyžaduje následující kroky:

- Získání certifikátu koncového bodu objektu BLOB
- Import certifikátu na klienta nebo vzdáleném hostiteli
- Přidání koncového bodu služby IP zařízení a služby BLOB Service do klienta nebo vzdáleného hostitele
- Konfigurace a ověření připojení

Každý z těchto kroků je popsaný v následujících částech.

### <a name="get-certificate"></a>Získat certifikát

Přístup k úložišti objektů BLOB přes HTTPS vyžaduje certifikát SSL pro zařízení. Tento certifikát také nahrajete do zařízení Azure Stack Edge pro jako soubor *. pfx* s připojeným privátním klíčem. Další informace o tom, jak vytvořit (jenom pro účely testování a vývoje) a nahrát tyto certifikáty do zařízení Azure Stack Edge pro, najdete tady:

- [Vytvořte certifikát koncového bodu objektu BLOB](azure-stack-edge-j-series-manage-certificates.md#create-certificates-optional).
- [Nahrajte certifikát koncového bodu objektu BLOB](azure-stack-edge-j-series-manage-certificates.md#upload-certificates).
- [Importujte certifikáty na klienta](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device), který přistupuje k zařízení.

### <a name="import-certificate"></a>Import certifikátu

Pokud se pomocí Průzkumník služby Azure Storage připojit k účtům úložiště v zařízení, musíte také importovat certifikát do Průzkumník služby Storage ve formátu PEM. V prostředí Windows je kódování Base-64 kódované *. cer* stejné jako formát PEM.

Pro import certifikátů v Průzkumník služby Azure Storage proveďte následující kroky:

1. Ujistěte se, že Průzkumník služby Azure Storage cílí na rozhraní Azure Stack API. Přejít na **upravit > cílová Azure Stack rozhraní API**. Po zobrazení výzvy restartujte Průzkumník služby Storage, aby se změna projevila.

2. Pokud chcete importovat certifikáty SSL, pokračujte v **úpravách > certifikáty ssl > import certifikátů**.

  
   ![Importovat certifikát do Průzkumník služby Storage](./media/azure-stack-edge-j-series-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. Procházejte a poskytněte podpisový řetěz a certifikáty objektů BLOB. Podpisový řetěz a certifikát BLOB by měly být ve formátu PEM, který je stejný jako formát kódovaný ve formátu base64 v systému Windows. Budete upozorněni, že se certifikáty úspěšně naimportovaly.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Přidat IP adresu zařízení a koncový bod služby BLOB Service

[Při připojování přes *protokol HTTP*použijte stejný postup při přidávání IP adresy zařízení a koncového bodu služby BLOB Service](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client).

### <a name="configure-and-verify-connection"></a>Konfigurace a ověření připojení

Postupujte podle pokynů ke [konfiguraci a ověření připojení, které jste použili při připojování přes *protokol HTTP*](#verify-connection). Jediným rozdílem je, že byste měli nechat *možnost použít protokol HTTP* nezaškrtnutou.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s následujícími tématy Azure Stack Edge pro:

> [!div class="checklist"]
> * Přidat účet úložiště
> * Připojení k účtu úložiště

Pokud se chcete dozvědět, jak transformovat data pomocí Azure Stack Edge pro, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Transformace dat pomocí Azure Stack Edge pro](./azure-stack-edge-j-series-deploy-configure-compute.md)


