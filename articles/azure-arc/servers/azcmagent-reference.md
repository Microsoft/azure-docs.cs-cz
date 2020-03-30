---
title: Rozhraní příkazového řádku agenta připojeného počítače Azure
description: Referenční dokumentace pro cli agenta připojeného počítače Azure
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513195"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Rozhraní příkazového řádku agenta připojeného počítače Azure

Nástroj `Azcmagent` (Azure Connected Machine Agent) se používá ke konfiguraci a řešení potíží s připojením počítačů, které nejsou azure, k Azure.

Samotný agent je proces daemon u `himdsd` Linuxu a `himds` služba Windows volána v systému Windows.

Při normálním `azcmagent connect` použití se používá k navázání spojení `azcmagent disconnect` mezi tímto počítačem a Azure a pokud se rozhodnete, že už toto připojení nechcete. Ostatní příkazy jsou pro řešení potíží nebo jiné zvláštní případy.

## <a name="options"></a>Možnosti

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>VIZ TAKÉ

* [azcmagent connect](#azcmagent-connect) – připojí tento počítač k Azure
* [azcmagent odpojit](#azcmagent-disconnect) - Odpojí tento počítač od Azure
* [azcmagent znovu připojit](#azcmagent-reconnect) - znovu připojí tento počítač k Azure
* [azcmagent show](#azcmagent-show) - Získá metadata počítače a stav agenta. To je užitečné především pro řešení potíží.
* [Azcmagent verze](#azcmagent-version) - Zobrazení verze Hybrid Management Agent

## <a name="azcmagent-connect"></a>azcmagent připojit

Připojí tento počítač k Azure

### <a name="synopsis"></a>Synopse

Vytvoří prostředek v Azure představující tento počítač.

To používá možnosti ověřování k vytvoření prostředku ve Správci prostředků Azure představující tento počítač. Prostředek je v požadované skupině předplatného a prostředků a data o počítači se ukládají v oblasti Azure určené parametrem umístění.
Výchozí název prostředku je název hostitele tohoto počítače, pokud není přepsán.

Certifikát odpovídající systémově přiřazené identitě tohoto počítače je pak stažen a uložen místně. Po dokončení tohoto kroku azure **connected machine metadata** service a host configuration agent začít synchronizovat s Cloud Azure.

Možnosti ověřování:

* Přístupový token`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* ID a tajný klíč jistiny služby`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Přihlášení k zařízení (interaktivní)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>azcmagent odpojit

Odpojí tento počítač od Azure

### <a name="synopsis"></a>Synopse

Odstraní prostředek v Azure, který představuje tento server.

Tento příkaz používá možnosti ověřování k odebrání prostředku Azure Resource Manager představující tento počítač. Po tomto okamžiku **azure connected machine metadata service** a host configuration agent bude odpojen. Tento příkaz nezastaví nebo neodebere služby: za tímto účelem balíček odeberete.

Tento příkaz vyžaduje vyšší oprávnění než role "Azure Connected Machine Onboarding".

Po odpojení počítače použijte `azcmagent connect`, `azcmagent reconnect` ne pokud chcete vytvořit nový prostředek pro něj v Azure.

Možnosti ověřování:

* Přístupový token`azcmagent disconnect --access-token <>`
* ID a tajný klíč jistiny služby`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Přihlášení interaktivního zařízení`azcmagent disconnect --tenant-id <>`

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

Znovu připojí tento počítač k Azure

### <a name="synopsis"></a>Synopse

Znovu připojte počítač s neplatnými přihlašovacími údaji do Azure.

Pokud počítač již má prostředek v Azure, ale není možné ověřit na něj, lze znovu připojit pomocí tohoto příkazu. To je možné, pokud byl počítač vypnutý dostatečně dlouho, aby jeho certifikát vypršel (alespoň 45 dní).

Pokud byl počítač odpojen `azcmagent disconnect`s `azcmagent connect` , použijte místo.

Tento příkaz používá možnosti ověřování k načtení nových přihlašovacích údajů odpovídajících prostředku Azure Resource Manager představující tento počítač.

Tento příkaz vyžaduje vyšší oprávnění než role **Připojení k počítači Azure.**

Možnosti ověřování

* Přístupový token`azcmagent reconnect --access-token <>`
* ID a tajný klíč jistiny služby`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Přihlášení interaktivního zařízení`azcmagent reconnect --tenant-id <>`

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

## <a name="azcmagent-show"></a>azcmagent show

Získá metadata počítače a stav agenta. To je užitečné především pro řešení potíží.

### <a name="synopsis"></a>Synopse

Získá metadata počítače a stav agenta. To je užitečné především pro řešení potíží.


### <a name="syntax"></a>Syntaxe

```
azcmagent show [flags]
```

### <a name="options"></a>Možnosti

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent verze

Zobrazení verze agenta hybridní správy

### <a name="synopsis"></a>Synopse

Zobrazení verze agenta hybridní správy

### <a name="syntax"></a>Syntaxe

```none
azcmagent version [flags]
```

### <a name="options"></a>Možnosti

```none
  -h, --help   help for version
```
