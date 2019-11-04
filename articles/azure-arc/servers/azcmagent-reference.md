---
title: Rozhraní CLI agenta připojeného počítače Azure
description: Referenční dokumentace pro rozhraní příkazového řádku Azure Connected Machine Agent
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513195"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Rozhraní CLI agenta připojeného počítače Azure

Nástroj `Azcmagent` (agent připojení počítače Azure) se používá ke konfiguraci a odstraňování potíží s připojením počítačů mimo Azure do Azure.

Samotný Agent je proces démona nazvaný `himdsd` v systému Linux a služba systému Windows s názvem `himds` ve Windows.

Při běžném používání se `azcmagent connect` používá k navázání spojení mezi tímto počítačem a Azure a `azcmagent disconnect`, pokud se rozhodnete, že už toto připojení nechcete. Ostatní příkazy jsou pro řešení potíží nebo jiné zvláštní případy.

## <a name="options"></a>Možnosti

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>VIZ TAKÉ

* [azcmagent Connect](#azcmagent-connect) – připojí tento počítač k Azure.
* [azcmagent odpojit](#azcmagent-disconnect) – odpojí tento počítač od Azure.
* [azcmagent reconnect](#azcmagent-reconnect) – znovu připojí tento počítač k Azure.
* [azcmagent show](#azcmagent-show) -Získá metadata počítače a stav agenta. To je užitečné hlavně při řešení potíží.
* [verze azcmagent](#azcmagent-version) – zobrazení verze hybridního agenta pro správu

## <a name="azcmagent-connect"></a>azcmagent připojit

Připojí tento počítač k Azure.

### <a name="synopsis"></a>Stručný obsah

Vytvoří prostředek v Azure, který představuje tento počítač.

Tato možnost využívá možnosti ověřování, které jsou k dispozici pro vytvoření prostředku v Azure Resource Manager představuje tento počítač. Prostředek se nachází v předplatném a skupině prostředků a data o počítači se ukládají v oblasti Azure určené parametrem Location.
Výchozí název prostředku je název hostitele tohoto počítače, pokud není přepsán.

Certifikát, který odpovídá identitě přiřazené systémem tohoto počítače, se pak stáhne a uloží místně. Po dokončení tohoto kroku se služba **metadat připojeného počítače Azure** a Agent konfigurace hosta začnou synchronizovat s cloudem Azure.

Možnosti ověřování:

* `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>` přístupového tokenu
* ID objektu služby a tajný kód `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Přihlášení zařízení (interaktivní) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Syntaxe

```none
azcmagent connect [flags]
```

### <a name="options"></a>Možnosti

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent odpojení

Odpojí tento počítač od Azure.

### <a name="synopsis"></a>Stručný obsah

Odstraní prostředek v Azure, který představuje tento server.

Tento příkaz používá možnosti ověřování, které jsou k dispozici pro odebrání prostředku Azure Resource Manager představujícího tento počítač. Po této fázi se **počítač s připojením k Azure metadata Service** a Agent konfigurace hosta se odpojí. Tento příkaz neukončí ani neodebere služby: Pokud to chcete udělat, odstraňte balíček.

Tento příkaz vyžaduje vyšší oprávnění než role "Připojování počítačů připojených k Azure".

Když je počítač odpojený, použijte `azcmagent connect`, ne `azcmagent reconnect`, pokud chcete pro něj vytvořit nový prostředek v Azure.

Možnosti ověřování:

* `azcmagent disconnect --access-token <>` přístupového tokenu
* ID objektu služby a tajný kód `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Přihlášení `azcmagent disconnect --tenant-id <>` interaktivního zařízení

### <a name="syntax"></a>Syntaxe

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Možnosti

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent znovu připojit

Znovu připojí tento počítač k Azure.

### <a name="synopsis"></a>Stručný obsah

Připojte počítač k Azure pomocí neplatných přihlašovacích údajů.

Pokud už počítač obsahuje prostředek v Azure, ale nedá se k němu ověřit, dá se ho pomocí tohoto příkazu znovu připojit. To je možné v případě, že byl počítač dostatečně dlouhý, aby jeho platnost vypršela (nejméně 45 dní).

Pokud byl počítač odpojený pomocí `azcmagent disconnect`, místo toho použijte `azcmagent connect`.

Tento příkaz používá dostupné možnosti ověřování pro načtení nových přihlašovacích údajů odpovídajících prostředku Azure Resource Manager, který představuje tento počítač.

Tento příkaz vyžaduje vyšší oprávnění než role registrace **počítače připojeného k Azure** .

Možnosti ověřování

* `azcmagent reconnect --access-token <>` přístupového tokenu
* ID objektu služby a tajný kód `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Přihlášení `azcmagent reconnect --tenant-id <>` interaktivního zařízení

### <a name="syntax"></a>Syntaxe

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Možnosti

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent zobrazit

Získá metadata počítače a stav agenta. To je užitečné hlavně při řešení potíží.

### <a name="synopsis"></a>Stručný obsah

Získá metadata počítače a stav agenta. To je užitečné hlavně při řešení potíží.


### <a name="syntax"></a>Syntaxe

```
azcmagent show [flags]
```

### <a name="options"></a>Možnosti

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>verze azcmagent

Zobrazit verzi hybridního agenta pro správu

### <a name="synopsis"></a>Stručný obsah

Zobrazit verzi hybridního agenta pro správu

### <a name="syntax"></a>Syntaxe

```none
azcmagent version [flags]
```

### <a name="options"></a>Možnosti

```none
  -h, --help   help for version
```
