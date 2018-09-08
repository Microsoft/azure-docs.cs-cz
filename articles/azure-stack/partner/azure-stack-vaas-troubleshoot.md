---
title: Řešení potíží s ověření služby Azure Stack jako služba | Dokumentace Microsoftu
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
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: ed070ac4fdf9ccca1b1b4b99b8031bc3fd035779
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160145"
---
# <a name="troubleshoot-validation-as-a-service"></a>Řešení potíží s ověření jako služba

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Tady jsou běžné problémy, které nesouvisí se vydaných verzí softwaru a jejich řešení.

## <a name="the-portal-shows-local-agent-in-debug-mode"></a>Na portálu se zobrazí místní agent je v režimu ladění

To je pravděpodobné, protože agent není schopen odeslat prezenční signály ke službě kvůli připojení k síti nestabilní. Prezenční signál odesílána každých pět minut. Pokud služba nepřijímá prezenční signál pro 15 minut, služba bude považovat za agenta neaktivní a testů bude naplánována už na něm. Vrátit se změnami chybová zpráva *Agenthost.log* soubor umístěný v adresáři začínali agenta.

> [!Note] 
> Bude dál běžet, všechny testy, které už běží na agentovi, ale pokud není prezenční signál obnoven před ukončením testu, agent se nezdaří k aktualizaci stavu testu nebo odeslat protokoly. Test se vždy zobrazí jako **systémem** a musí být zrušena.

## <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Proces agenta v počítači byla vypnuta při provádění testu. Co můžete očekávat?

Pokud proces agenta je vypnout ungracefully například, restartování počítače, procesu ukončen (CTRL + C v okně agenta se považuje za řádné vypnutí) pak test, který byl spuštěn na něm nadále zobrazovat jako **systémem**. Pokud restartování agenta a agenta se aktualizovat stav testu k **zrušena**. Pokud není restartovat agenta, pak testu se zobrazí jako **systémem** a je nutné ručně zrušit testu

> [!Note] 
> Testy v rámci pracovního postupu jsou naplánovány ke spuštění postupně. **Čekající** nebude získat spuštěny testy, dokud nebudou testy v **systémem** stavu ve stejný pracovní postup dokončený.

## <a name="handle-slow-network-connectivity"></a>Zpracování pomalé síťové připojení

PIR image si můžete stáhnout do sdílené složky v místním datovém centru. A pak můžete zkontrolovat na obrázku.

<!-- This is from the appendix to the Deploy local agent topic. -->

### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Stáhněte si PIR image do místní sdílené složky v případě pomalý přenos v síti

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

### <a name="verifying-pir-image-file-hash-value"></a>Ověření hodnoty hash souboru PIR Image

Můžete použít **Get-HashFile** rutiny pro získání hodnoty hash pro stažené veřejnou image z úložiště souborů obrázků zkontrolujete tak integritu imagí.

| Název souboru | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.VHD | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.VHD | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.VHD | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604 20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

## <a name="next-steps"></a>Další postup

- Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).
