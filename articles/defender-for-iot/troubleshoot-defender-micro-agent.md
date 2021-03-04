---
title: Řešení potíží s agentem IoT Micro v programu Defender
titleSuffix: Azure Defender for IoT
description: Naučte se zvládnout neočekávané nebo nevysvětlující chyby.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: dade0d0d5dc4d690ea94f20deaf956b1e079bad7
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124172"
---
# <a name="defender-iot-micro-agent-troubleshooting"></a>Řešení potíží s agentem IoT Micro v programu Defender 

V případě neočekávaných nebo nevysvětlujících chyb použijte při pokusu o vyřešení vašich potíží následující metody řešení potíží. Podle potřeby můžete také kontaktovat produktového týmu Azure Defender pro IoT.   

## <a name="service-status"></a>Stav služby 

Zobrazení stavu služby: 

1. Spusťte následující příkaz

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Zkontrolujte, zda je služba stabilní `active` , a ověřte, zda je a zda je doba provozu v procesu vhodná.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Ujistěte se, že je vaše služba stabilní, a zkontrolujte, že je aktivní a že je doba provozu vhodná.":::

Pokud je služba uvedená jako `inactive` , spusťte službu pomocí následujícího příkazu:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

Pokud je doba provozu procesu moc krátká, budete mít jistotu, že dojde k chybě. Chcete-li tento problém vyřešit, je nutné zkontrolovat protokoly.

## <a name="review-logs"></a>Kontrola protokolů 

Pomocí následujícího příkazu ověřte, že je služba Defender IoT Micro agent spuštěná s oprávněními root.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Ověřte, že je Defender pro službu IoT Micro agent spuštěný s oprávněními root.":::

Chcete-li zobrazit protokoly, použijte následující příkaz:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

Chcete-li službu restartovat, použijte následující příkaz: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Další kroky

Podívejte se na [podporu funkcí a vyřazení](edge-security-module-deprecation.md)z provozu.
