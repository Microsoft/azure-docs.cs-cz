---
title: Řešení potíží s agentem IoT Micro v Defenderu (Preview)
description: Naučte se zvládnout neočekávané nebo nevysvětlující chyby.
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 3d52c4093c01d7e449c68b1c8143249b51f7061a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011414"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Řešení potíží s agentem IoT Micro v Defenderu (Preview)

Pokud dojde k neočekávané chybě, můžete tyto metody řešení potíží použít při pokusu o vyřešení problému. Podle potřeby můžete také kontaktovat produktového týmu Azure Defender pro IoT.   

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

Zjistíte, že dojde k chybě služby, pokud je doba provozu procesu kratší než 2 minuty. Chcete-li tento problém vyřešit, je nutné [zkontrolovat protokoly](#review-the-logs).

## <a name="validate-micro-agent-root-privileges"></a>Ověření kořenových oprávnění Micro agentů

Pomocí následujícího příkazu ověřte, že je služba Defender IoT Micro agent spuštěná s oprávněními root.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Ověřte, že je Defender pro službu IoT Micro agent spuštěný s oprávněními root.":::
## <a name="review-the-logs"></a>Kontrola protokolů 

Chcete-li zkontrolovat protokoly, použijte následující příkaz:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Rychlá kontrola protokolu

Pokud při spuštění mikroagenta dojde k problému, můžete agenta mikroagenta spustit v dočasném stavu, který vám umožní zobrazit protokoly pomocí následujícího příkazu:

```azurecli
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Restartujte službu.

Chcete-li službu restartovat, použijte následující příkaz: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Další kroky

Podívejte se na [podporu funkcí a vyřazení](edge-security-module-deprecation.md)z provozu.
