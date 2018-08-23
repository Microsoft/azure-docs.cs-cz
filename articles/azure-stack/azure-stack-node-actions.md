---
title: Uzel akcí jednotky škálování ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit stav uzlu a využít na vypnutí vyprazdňování a obnovení uzlu akce na systémech pro Azure Stack integrované.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 1f59f2ce6e3bf8d34ce225aa93da76ad523775e0
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42056598"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Uzel akcí jednotky škálování ve službě Azure Stack

*Platí pro: integrované systémy Azure Stack*

Tento článek popisuje, jak zobrazit stav jednotky škálování a jeho přidružené uzly a jak pomocí akce k dispozici uzel. Uzlu akce zahrnují zapnout napájení vypnuto, vyprázdnit, obnovení a opravit. Tyto akce uzlu se obvykle používá při nahrazení pole částí nebo pro scénáře obnovení uzlu.

> [!Important]  
> Všechny akce uzlu je popsáno v tomto článku se má pouze jeden uzel cílové najednou.


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Zobrazit stav jednotky škálování a jeho uzly

Na portálu správce můžete snadno zobrazit stav jednotky škálování a jeho přidružené uzly.

Chcete-li zobrazit stav jednotky škálování:

1. Na **Správa oblastí** dlaždice, vyberte oblast.
2. Na levé straně v části **prostředky infrastruktury**vyberte **jednotek škálování**.
3. Ve výsledcích vyberte jednotky škálování.
 
Tady se zobrazí následující informace:

- Název oblasti. Název oblasti je odkazována pomocí **– umístění** v modulu prostředí PowerShell.
- Typ systému
- Celkový počet logických jader
- Celková paměť
- seznam jednotlivých uzlů a jejich stav; buď **systémem** nebo **zastavena**.

![Škálovací jednotku dlaždici s údaji o stavu spuštění pro každý uzel](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Zobrazit informace o škálování jednotek uzlu

Pokud vyberete jednotlivých uzlů, může zobrazit následující informace:

- Název oblasti
- model serveru
- IP adresa řadiče pro správu základní desky (BMC)
- provozní stav
- Celkový počet jader
- Celková velikost paměti
 
![Škálovací jednotku dlaždici s údaji o stavu spuštění pro každý uzel](media/azure-stack-node-actions/NodeActions.PNG)

Odsud můžete provést také akcí uzel jednotek škálování.

## <a name="scale-unit-node-actions"></a>Škálovací jednotku uzlu akce

Při zobrazení informací o uzlu škálovací jednotky můžete také provádět uzlu akce, jako:

- Akce odhlášení a opětovné spuštění
- Opravit

Operační stav tohoto uzlu Určuje, jaké možnosti jsou k dispozici.

### <a name="power-off"></a>Napájení vypnuto

**Vypnutí** akce vypne uzlu. Je stejný jako v případě, že stisknete tlačítko napájení. Provádí **není** odesílat signál k vypnutí operačního systému. Plánované vypnutí operace ujistěte, že nejprve Vyprázdnit uzel jednotek škálování.

Tato akce se obvykle používá, pokud uzel je ve stavu ukončování "zamrzlých" a už jsou reaguje na požadavky.

> [!Important] 
> Tato funkce je pouze dostupná přes PowerShell. Bude k dispozici na portálu Správce služby Azure Stack znovu později.


Pokud chcete spustit vypnout akci s pomocí prostředí PowerShell:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

V nepravděpodobném případě vypnout akce nefunguje místo toho použijte BMC webové rozhraní.

### <a name="power-on"></a>Zapnutí

**Power on** akce zapne uzlu. Je stejný jako v případě, že stisknete tlačítko napájení. 

> [!Important] 
> Tato funkce je pouze dostupná přes PowerShell. Bude k dispozici na portálu Správce služby Azure Stack znovu později.

Spuštění výkon v akci s pomocí prostředí PowerShell:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

V nepravděpodobném případě zapnutí akce nefunguje místo toho použijte BMC webové rozhraní.

### <a name="drain"></a>Vyprázdnit

**Vyprázdnit** akce evacuates díky distribuci mezi zbývající uzly v této jednotce škálování konkrétní všechny aktivní úlohy.

Tato akce se obvykle používá při nahrazení pole částí, jako je například nahrazení celého uzlu.

> [!IMPORTANT]  
> Ujistěte se, že uzel vyprázdnit pouze během naplánovaného časového období údržby, ve kterém byly oznámeny uživatelů. Za určitých podmínek aktivní úlohy může docházet k přerušení.

Spuštění akcí vyprazdňování prostřednictvím Powershellu:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Obnovit

**Resume** akce Vyprázdněná uzlu obnoví a označí je aktivní umísťování úloh. Předchozí úlohy, které byly spuštěny na uzlu není navrácení služeb po obnovení. (Pokud jste vyprazdňování uzlu a pak vypnout, při výkonu uzel zpátky na, není označena jako aktivní umísťování úloh. Až budete připraveni, musíte použít pokračování akci označit uzel jako aktivní.)

Spuštění akce obnovení prostřednictvím prostředí PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Opravit

**Opravit** akce opraví uzlu. Používejte pouze pro jednu z následujících scénářů:

- Uzel úplné nahrazení (s nebo bez nových datových disků)
- Po selhání součásti hardwaru a nahrazení (Pokud se nedoporučuje v dokumentaci k vyměnitelná jednotka (FRU) pole).

> [!IMPORTANT]  
> Najdete v dokumentaci FRU OEM dodavatele hardwaru vyhledejte přesné kroky, pokud je třeba nahradit uzlu nebo jednotlivých hardwarových součástí. Dokumentace ke službě FRU určí, jestli je potřeba spuštěním akce opravy po nahrazení hardwarová komponenta.  

Když spustíte akci oprava, musíte zadat BMC IP adresu. 

Pokud chcete spustit opravu prostřednictvím prostředí PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````

## <a name="next-steps"></a>Další postup

Další informace o modulu Správce prostředků infrastruktury Azure Stack, najdete v článku [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.4.0).