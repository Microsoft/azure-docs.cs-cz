---
title: Požadavky na certifikáty a řešení potíží s Azure Stack Edge pro | Microsoft Docs
description: Popisuje požadavky na certifikáty a řešení chyb certifikátů pomocí Azure Stackho zařízení Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 9184a3f429804ac383f137de49c5391e2e1db80f
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439234"
---
# <a name="certificate-requirements"></a>Požadavky na certifikáty

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje požadavky na certifikáty, které musí být splněné, aby bylo možné nainstalovat certifikáty do zařízení Azure Stack Edge pro. Požadavky se týkají certifikátů PFX, vydávající autority, názvu subjektu certifikátu a alternativního názvu subjektu a podporovaných algoritmů certifikátů.

## <a name="certificate-issuing-authority"></a>Autorita vydávající certifikát

Požadavky na vystavování certifikátů jsou následující:

* Certifikáty se musí vydávat buď z interní certifikační autority, nebo z veřejné certifikační autority.

* Použití certifikátů podepsaných svým držitelem není podporováno.

* Vydaná položka certifikátu *pro:* pole nesmí být shodná s polem *vydaným:* s výjimkou certifikátů kořenových certifikačních autorit.


## <a name="certificate-algorithms"></a>Algoritmy certifikátů

Algoritmy certifikátů musí splňovat následující požadavky:

* Certifikáty musí používat algoritmus RSA Key.

* Podporují se jenom certifikáty RSA se zprostředkovatelem kryptografických služeb Microsoft RSA/SChannel.

* Algoritmus podpisu certifikátu nemůže být SHA1.

* Minimální velikost klíče je 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Název subjektu certifikátu a alternativní název subjektu

Certifikáty musí obsahovat následující název předmětu a alternativní název předmětu:

* Můžete buď použít jeden certifikát, který pokrývá všechny názvové prostory v polích alternativní název předmětu certifikátu (SAN). Alternativně můžete použít jednotlivé certifikáty pro každý obor názvů. Oba přístupy vyžadují použití zástupných znaků pro koncové body, pokud je to nutné, například binární rozsáhlý objekt (BLOB).

* Ujistěte se, že názvy subjektů (běžný název v názvu subjektu) jsou součástí alternativních názvů předmětu v příponě alternativního názvu subjektu.

* Můžete použít jeden certifikát se zástupným znakem, který pokrývá všechny názvové prostory v polích SAN certifikátu.

* Při vytváření certifikátu koncového bodu použijte následující tabulku:

    |Typ |Název subjektu (SN)  |Alternativní název subjektu (SAN)  |Příklad názvu subjektu |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Místní uživatelské rozhraní| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Jeden certifikát pro více sítí SAN pro oba koncové body|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Uzel|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |Síť VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate je pevně zakódované.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>Certifikát PFX

Certifikáty PFX nainstalované na zařízení Azure Stack Edge pro by měly splňovat následující požadavky:

* Po získání certifikátů od autority SSL se ujistěte, že jste pro certifikáty získali úplný řetěz podepisování.

* Pokud exportujete certifikát PFX, ujistěte se, že jste vybrali možnost **Zahrnout všechny certifikáty do řetězce, pokud je to možné** .

* Použijte certifikát PFX pro koncový bod, místní uživatelské rozhraní, uzel, síť VPN a Wi-Fi jako veřejné i privátní klíče jsou vyžadovány pro Azure Stack Edge pro. Privátní klíč musí mít nastaven atribut klíč místního počítače.

* Šifrování PFX certifikátu by mělo být 3DES. Toto je výchozí šifrování používané při exportu z úložiště certifikátů klienta Windows 10 nebo Windows serveru 2016. Další informace týkající se 3DES najdete v části [Triple DES](https://en.wikipedia.org/wiki/Triple_DES).

* Soubory PFX certifikátu musí mít v poli *použití klíče* platný *digitální podpis* a hodnoty *KeyEncipherment* .

* Soubory PFX certifikátu musí mít v poli *použití rozšířeného klíče* k dispozici hodnoty *ověřování serveru (1.3.6.1.5.5.7.3.1)* a *ověřování klientů (1.3.6.1.5.5.7.3.2)* .

* Pokud používáte nástroj pro kontrolu připravenosti Azure Stack, musí být hesla pro všechny soubory PFX certifikátu v době nasazení stejná. Další informace najdete v tématu [Vytvoření certifikátů pro Azure Stack Edge pro pomocí nástroje pro kontrolu připravenosti centra Azure Stack](azure-stack-edge-gpu-create-certificates-tool.md).

* Heslo k certifikátu PFX musí být složité heslo. Toto heslo si poznamenejte, protože se používá jako parametr nasazení.

* Používejte jenom certifikáty RSA se zprostředkovatelem kryptografických služeb Microsoft RSA/SChannel.

Další informace najdete v tématu [export certifikátů PFX s privátním klíčem](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

## <a name="next-steps"></a>Další kroky

[Použití certifikátů s Azure Stack Edge pro](azure-stack-edge-gpu-manage-certificates.md)

[Vytvoření certifikátů pro Azure Stack Edge pro pomocí nástroje pro kontrolu připravenosti centra Azure Stack](azure-stack-edge-gpu-create-certificates-tool.md)

[Export certifikátů PFX pomocí privátního klíče](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Řešení chyb certifikátů](azure-stack-edge-gpu-certificate-troubleshooting.md)