---
title: Ověřování agenta zabezpečení (Preview)
description: Ověřování pomocí mikroagentů provádí dvě možné metody.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: 8dd8abaedaaababf4d84330c5bf8cb030bac55bd
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779235"
---
# <a name="micro-agent-authentication-methods-preview"></a>Metody ověřování mikroagentů (Preview)

Existují dvě možnosti ověřování pomocí programu Defender pro IoT Micro agent: 

- Připojovací řetězec 

- Certifikát 

## <a name="authentication-using-a-connection-string"></a>Ověřování pomocí připojovacího řetězce 

Aby bylo možné použít připojovací řetězec, je nutné přidat soubor, který používá připojovací řetězec kódovaný v kódování UTF-8 v adresáři agenta programu Defender v souboru s názvem `connection_string.txt` . Třeba

```azurecli
echo “<connection string>” > connection_string.txt 
```

Až to uděláte, měli byste službu restartovat pomocí tohoto příkazu.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Ověřování pomocí certifikátu 


Provedení ověřování pomocí certifikátu: 

1. Uložte veřejnou část certifikátu s kódováním PEM do adresáře agenta programu Defender v souboru s názvem `certificate_public.pem` .
1. Privátní klíč kódovaný v PEM umístěte do adresáře agent v programu Defender do souboru s názvem `certificate_private.pem` .
1. Umístěte příslušný připojovací řetězec do souboru s názvem `connection_string.txt` . Třeba

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Tato akce indikuje, že agent Defenderu očekává, že se má pro ověření zadat certifikát. 

1. Restartujte službu pomocí následujícího kódu: 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Ujistěte se, že Micro Agent běží správně. 

1. Spusťte následující příkaz: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Ověřte, že je služba stabilní, tím, že je **aktivní** a že je doba provozu tohoto procesu vhodná: 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Ujistěte se, že je vaše služba stabilní, tím, že je aktivní.":::

## <a name="next-steps"></a>Další kroky

Projděte si [Test zabezpečení stav – CIS](concept-security-posture.md).
