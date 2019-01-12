---
title: Řešení potíží s Azure Stack ověření jako služba | Dokumentace Microsoftu
description: Řešení potíží s ověření jako služba pro službu Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 0fb1afb1a07b31f7e261c958b8a03bec3b299433
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245289"
---
# <a name="troubleshoot-validation-as-a-service"></a>Řešení potíží s ověření jako služba

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Tady jsou běžné problémy, které nesouvisí se vydaných verzí softwaru a jejich řešení.

## <a name="local-agent"></a>Místní agent

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>Na portálu se zobrazí místní agent je v režimu ladění

To je pravděpodobné, protože agent není schopen odeslat prezenční signály ke službě kvůli připojení k síti nestabilní. Prezenční signál odesílána každých pět minut. Pokud služba nepřijímá prezenční signál pro 15 minut, služba bude považovat za agenta neaktivní a testů bude naplánována už na něm. Vrátit se změnami chybová zpráva *Agenthost.log* soubor umístěný v adresáři začínali agenta.

> [!Note]
> Bude dál běžet, všechny testy, které už běží na agentovi, ale pokud není prezenční signál obnoven před ukončením testu, agent se nezdaří k aktualizaci stavu testu nebo odeslat protokoly. Test se vždy zobrazí jako **systémem** a musí být zrušena.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Proces agenta v počítači byla vypnuta při provádění testu. Co můžete očekávat?

Pokud proces agenta je vypnout ungracefully například, restartování počítače, procesu ukončen (CTRL + C v okně agenta se považuje za řádné vypnutí) pak test, který byl spuštěn na něm nadále zobrazovat jako **systémem**. Pokud restartování agenta a agenta se aktualizovat stav testu k **zrušena**. Pokud není restartovat agenta, pak testu se zobrazí jako **systémem** a je nutné ručně zrušit testu.

> [!Note]
> Testy v rámci pracovního postupu jsou naplánovány ke spuštění postupně. **Čekající** nebude získat spuštěny testy, dokud nebudou testy v **systémem** stavu ve stejný pracovní postup dokončený.

## <a name="vm-images"></a>Image virtuálních počítačů

### <a name="handle-slow-network-connectivity"></a>Zpracování pomalé síťové připojení

PIR image si můžete stáhnout do sdílené složky v místním datovém centru. A pak můžete zkontrolovat na obrázku.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Stáhněte si PIR image do místní sdílené složky v případě pomalý přenos v síti

1. Stáhněte si nástroj AzCopy z: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. Extrahovat AzCopy.zip a přejděte do adresáře, který obsahuje AzCopy.exe

3. Otevřete Windows PowerShell řádku se zvýšenými oprávněními. Spusťte následující příkazy:

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare je cesta ke sdílené složce nebo místní cestu.

#### <a name="verifying-pir-image-file-hash-value"></a>Ověření hodnoty hash souboru PIR Image

Můžete použít **Get-HashFile** rutiny pro získání hodnoty hash pro stažené veřejnou image z úložiště souborů obrázků zkontrolujete tak integritu imagí.

| Název souboru | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.VHD | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.VHD | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.VHD | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604 20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Když nahrajete image virtuálního počítače v dojde k selhání `VaaSPreReq` skriptu

Nejprve zkontrolujte, že prostředí není v pořádku:

1. Z DVM / jump box, zkontrolujte, že můžete úspěšně přihlásit na portál pro správu pomocí přihlašovacích údajů správce.
1. Potvrďte, že neexistují žádné výstrahy ani upozornění.

Pokud prostředí je v pořádku, ručně odešlete 5 Imagí virtuálních počítačů, které jsou potřebné pro VaaS testovacích běhů:

1. Přihlaste se jako správce služby na portálu pro správu. Můžete najít na portálu pro správu adresy URL z úložiště OSN nebo váš soubor s informacemi o razítko. Pokyny najdete v tématu [parametry prostředí](azure-stack-vaas-parameters.md#environment-parameters).
1. Vyberte **další služby** > **poskytovatelů prostředků** > **Compute** > **Imagí virtuálních počítačů**.
1. Vyberte **+ přidat** tlačítko v horní části **Imagí virtuálních počítačů** okno.
1. Změnit nebo zkontrolovat hodnoty z těchto polí pro první image virtuálního počítače:
    > [!IMPORTANT]
    > Ne všechny výchozí hodnoty jsou správné pro existující položku Marketplace.

    | Pole  | Hodnota  |
    |---------|---------|
    | Vydavatel | MicrosoftWindowsServer |
    | Nabídka | WindowsServer |
    | OS Type | Windows |
    | Skladová jednotka (SKU) | 2012-R2-Datacenter |
    | Verze | 1.0.0 |
    | Identifikátor URI objektu Blob disku s operačním systémem | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Vyberte tlačítko **Vytvořit**.
1. Opakujte pro zbývající imagí virtuálních počítačů.

Vlastnosti všech 5 imagí virtuálních počítačů jsou následující:

| Vydavatel  | Nabídka  | OS Type | Skladová jednotka (SKU) | Verze | Identifikátor URI objektu Blob disku s operačním systémem |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>Další postup

- Kontrola [zpráva k vydání verze pro ověření jako služba](azure-stack-vaas-release-notes.md) změny v nejnovější vydané verzi.