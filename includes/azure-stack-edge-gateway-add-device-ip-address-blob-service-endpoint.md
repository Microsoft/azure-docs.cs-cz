---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185526"
---
1. Přejít do místního webového uživatelského rozhraní vašeho zařízení a přihlaste se k zařízení. Ujistěte se, že je zařízení odemknuté.

2. Přejít na stránku **nastavení sítě** . Poznamenejte si IP adresu zařízení pro síťové rozhraní používané pro připojení ke klientovi.

3. Pokud pracujete se vzdáleným klientem Windows, spusťte **Poznámkový blok** jako správce a pak otevřete soubor Hosts v umístění `C:\Windows\System32\Drivers\etc` .

4. Do souboru hostitelů přidejte následující položku: `<Device IP address> <Blob service endpoint>`

    Dostali jste koncový bod služby BLOB Service z hraničního účtu úložiště vytvořeného v Azure Portal. Budete používat jenom příponu koncového bodu služby BLOB Service.

    Pro referenci použijte následující obrázek. Uložte soubor `hosts`.

    ![Upravit soubor hostitelů na klientovi Windows](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)