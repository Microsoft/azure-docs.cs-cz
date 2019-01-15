---
title: Uzel akcí jednotky škálování ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak zobrazení stavu uzlu a využití výkonu na napájení vypnuto, zakázat a obnovit uzel akce na systémech pro Azure Stack integrované.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 7e01feff1344557c90f23bb006520111f58e437a
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302676"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Uzel akcí jednotky škálování ve službě Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Tento článek popisuje, jak zobrazit stav jednotky škálování. Můžete zobrazit uzly, jednotku. Můžete spustit uzlu akce, jako je například power na power, vypnout, vyprázdnit, obnovit a opravit. Tyto akce uzlu se obvykle používá při nahrazení pole částí nebo které vám pomohu obnovit uzel.

> [!Important]  
> Všechny akce uzlu je popsáno v tomto článku zaměřit jednoho uzlu současně.

## <a name="view-the-node-status"></a>Zobrazení stavu uzlu

Na portálu správce můžete zobrazit stav jednotky škálování a její související uzly.

Zobrazení stavu jednotky škálování:

1. Na **Správa oblastí** dlaždice, vyberte oblast.
2. Na levé straně v části **prostředky infrastruktury**vyberte **jednotek škálování**.
3. Ve výsledcích vyberte jednotky škálování.
4. Na levé straně v části **Obecné**vyberte **uzly**.

  Zobrazte následující informace:

  - Seznam jednotlivých uzlů
  - Operační stav (viz následující seznam)
  - Stav napájení (spuštěná nebo zastavená)
  - model serveru
  - IP adresa řadiče pro správu základní desky (BMC)
  - Celkový počet jader
  - Celková velikost paměti

![Stav jednotky škálování](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Provozní stavy uzlů

| Status | Popis |
|----------------------|-------------------------------------------------------------------|
| Spuštěno | Uzel je aktivně účasti v jednotce škálování. |
| Zastaveno | Uzel není k dispozici. |
| Přidávání | Uzel je aktivně přidává na jednotce škálování. |
| Probíhají opravy | Uzel je aktivně opraví. |
| Údržba | Uzel pozastaví a žádné aktivní uživatel úloha běží. |
| Vyžaduje nápravy | Byla zjištěna chyba, která vyžaduje uzel, který má být opraven. |

## <a name="scale-unit-node-actions"></a>Škálovací jednotku uzlu akce

Při zobrazení informací o uzlu škálovací jednotky můžete také provádět uzlu akce, jako:
 - Spuštění a zastavení (v závislosti na aktuální stav napájení)
 - Zakázat a obnovit (v závislosti na provozní stav)
 - Opravit
 - Shutdown

Operační stav tohoto uzlu Určuje, jaké možnosti jsou k dispozici.

Je potřeba nainstalovat moduly Azure Stack Powershellu. Tyto rutiny jsou v **Azs.Fabric.Admin** modulu. Pokud chcete nainstalovat nebo ověřit instalaci prostředí PowerShell pro Azure Stack, najdete v článku [instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).

## <a name="stop"></a>Zastavit

**Zastavit** akce vypne uzlu. Je stejný jako v případě, že stisknete tlačítko napájení. Odešle signál k vypnutí operačního systému. Pro plánované zastavení operace vždy akci vypnutí nejprve. 

Tato akce se obvykle používá, pokud uzel je ve stavu ukončování "zamrzlých" a už jsou reaguje na požadavky.

Akce zastavení spuštění, otevřete řádku Powershellu se zvýšenými oprávněními a spusťte následující rutinu:

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

V nepravděpodobném případě, že akce zastavení nefunguje, zkuste operaci zopakovat, a pokud selže podruhé použijte BMC webové rozhraní.

Další informace najdete v tématu [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Start

**Start** akce zapne uzlu. Je stejný jako v případě, že stisknete tlačítko napájení. 
 
Ke spuštění spouštěcí akci, otevřete řádku Powershellu se zvýšenými oprávněními a spusťte následující rutinu:

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

V nepravděpodobném případě, že spouštěcí akci nefunguje, zkuste operaci zopakovat, a pokud selže podruhé použijte BMC webové rozhraní.

Další informace najdete v tématu [Start AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Vyprázdnit

**Vyprázdnit** akce přesune na zbývající uzly v této jednotce škálování konkrétní všechny aktivní úlohy.

Tato akce se obvykle používá při nahrazení pole částí, jako je například nahrazení celého uzlu.

> [!Important]
> Ujistěte se, že během naplánovaného časového období údržby, ve kterém byly oznámeny uživatelé používat operace vyprazdňování uzlu. Za určitých podmínek aktivní úlohy může docházet k přerušení.

Spustit akci vyprazdňování, otevřete řádku Powershellu se zvýšenými oprávněními a spusťte následující rutinu:

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Další informace najdete v tématu [zakázat AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Obnovit

**Obnovit** akce zakázané uzlu obnoví a označí je aktivní umísťování úloh. Předchozí úlohy, které byly spuštěny na uzlu není navrácení služeb po obnovení. (Pokud používáte operace vyprazdňování uzlu je nutné vypnout. Když jste zpátky na uzlu, není označena jako aktivní umísťování úloh. Až budete připraveni, musíte použít pokračování akci označit uzel jako aktivní.)

Spustit akce obnovení, otevřete řádku Powershellu se zvýšenými oprávněními a spusťte následující rutinu:

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Další informace najdete v tématu [povolit AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Opravit

**Opravit** akce opraví uzlu. Používejte pouze pro jednu z následujících scénářů:
 - Uzel úplné nahrazení (s nebo bez nových datových disků)
 - Po selhání součásti hardwaru a nahrazení (Pokud se nedoporučuje v dokumentaci k vyměnitelná jednotka (FRU) pole).

> [!Important]  
> Najdete v dokumentaci FRU OEM dodavatele hardwaru vyhledejte přesné kroky, pokud je třeba nahradit uzlu nebo jednotlivých hardwarových součástí. Dokumentace ke službě FRU určí, jestli je potřeba spuštěním akce opravy po nahrazení hardwarová komponenta. 

Když spustíte akci oprava, musíte zadat BMC IP adresu. 

Spuštěním akce opravy, otevřete řádku Powershellu se zvýšenými oprávněními a spusťte následující rutinu:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ````

## <a name="next-steps"></a>Další postup

Další informace o modulu Správce prostředků infrastruktury Azure Stack, najdete v článku [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.5.0).
