---
title: Škálování akcí jednotky uzlu v zásobníku Azure | Microsoft Docs
description: Zjistěte, jak zobrazit stav uzlu a pomocí napájení na vypnutí vyprazdňování a obnovit uzel akce v systému Azure zásobníku integrované.
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
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 202854157dee28f3ab3dc73c6f22508a8bf510b3
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Uzel akcí jednotky škálování v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Tento článek popisuje, jak zobrazit stav jednotky škálování a jeho přidružené uzly a jak používat akce k dispozici uzel. Uzel akce zahrnují zapnutí napájení vypnuto, vyprazdňování, obnovit a opravit. Tyto akce uzlu se obvykle používá při nahrazení pole částí nebo pro scénáře obnovení uzlu.

> [!Important]  
> Všechny uzlu akce popsané v tomto článku měli jenom target jednoho uzlu současně.


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Zobrazení stavu jednotky škálování a jeho uzly

Na portálu správce můžete snadno zobrazit stav jednotky škálování a jeho přidružené uzly.

Chcete-li zobrazit stav jednotky škálování:

1. Na **oblast správy** dlaždici, vyberte oblast.
2. Na levé straně v části **prostředky infrastruktury**, vyberte **jednotek škálování**.
3. Ve výsledcích vyberte jednotky škálování.
 
Zde můžete zobrazit následující informace:

- Název oblasti
- Typ systému
- Celkový počet logických jader
- celkové paměti
- seznam jednotlivé uzly a jejich stav; buď spuštěná nebo zastavená.

![Dlaždice jednotky škálování zobrazením stavu spuštění pro každý uzel](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Zobrazení informací o uzlu jednotky škálování

Pokud vyberete jednotlivé uzly, můžete zobrazit následující informace:

- Název oblasti
- model serveru
- IP adresa řadiče pro správu základní desky (BMC)
- provozní stav
- Celkový počet jader
- Celková velikost paměti
 
![Dlaždice jednotky škálování zobrazením stavu spuštění pro každý uzel](media/azure-stack-node-actions/NodeActions.PNG)

Můžete také provést akcí uzlu jednotky škálování z tohoto umístění.

## <a name="scale-unit-node-actions"></a>Akcí uzlu jednotky škálování

Při zobrazení informací o uzlu jednotek škálování, můžete nastavit také uzlu akce, jako:

- Zapnutí a vypnutí
- vyprazdňování a pokračovat
- Oprava

Provozní stav uzlu Určuje, které možnosti jsou k dispozici.

### <a name="power-off"></a>Napájení vypnuto

**Vypnutí** akce vypne uzlu. Je stejný jako v případě, že stisknutím tlačítka napájení. Provede **není** odesílat signál k vypnutí operačního systému. Pro plánované vypnutí operace zajistěte, aby že nejprve vyprazdňování uzlu jednotky škálování.

Tato akce se obvykle používá, pokud uzel je ve stavu "zamrzlých" a už reaguje na požadavky.

> [!Important] 
> Tato funkce je dostupná prostřednictvím rozhraní PowerShell jenom. Bude k dispozici na portálu Azure zásobníku správce znovu později.


Spuštění vypnutí akce pomocí prostředí PowerShell:

````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
```` 

V případě nepravděpodobné, že vypnutí akce nepomůže použijte místo toho webové rozhraní BMC.

### <a name="power-on"></a>Zapnutí

**Zapnout** akce zapne uzlu. Je stejný jako v případě, že stisknutím tlačítka napájení. 

> [!Important] 
> Tato funkce je dostupná prostřednictvím rozhraní PowerShell jenom. Bude k dispozici na portálu Azure zásobníku správce znovu později.

Možnosti spuštění pro akce pomocí prostředí PowerShell:

````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
````

V případě nepravděpodobné, že zapnutí akce nepomůže použijte místo toho webové rozhraní BMC.

### <a name="drain"></a>Vyprazdňování

**Vyprazdňování** akce evacuates podle jejich distribuci mezi zbývající uzly v této jednotce škálování konkrétní všechny aktivní úlohy.

Tato akce se obvykle používá při nahrazení pole částí, jako je například nahrazení celého uzlu.

> [!IMPORTANT]
> Zkontrolujte, zda uzel vyprázdnit pouze během plánované údržby, kde uživatelé byli upozorněni. Za určitých podmínek můžete zaznamenat aktivní úlohy přerušení.

Spuštění akce vyprazdňování pomocí prostředí PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Obnovit

**Obnovit** akce Vyprázdněná uzlu obnoví a označí je aktivní pro umísťování úloh. Starší úlohy, které byly spuštěné na uzlu není navrácení služeb po obnovení. (Pokud jste vyprazdňování uzlů a potom vypnout, při výkonu uzlu zpět na, není označena jako aktivní umísťování úloh. Až bude připravený, musíte použít akce pokračovat k označení uzel jako aktivní.)

Spuštění akce obnovení pomocí prostředí PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Opravit

**Repair** akce opraví uzlu. Používejte pouze pro jednu z následujících scénářů:

- Nahrazení úplné uzlu (s nebo bez nové datové disky)
- Po selhání součásti hardwaru a nahrazení (Pokud se nedoporučuje v dokumentaci k pole replaceable jednotka (FRU)).

> [!IMPORTANT]
> Dokumentaci dodavatele hardwaru OEM FRU přesné kroky při potřebujete nahradit uzel nebo jednotlivých hardwarových součástí. V dokumentaci FRU bude zadejte, jestli je potřeba spuštěním akce opravy po nahrazení hardwarová komponenta.  

Když spustíte akci oprava, budete muset zadat BMC IP adresu. 

Chcete-li spustit opravu pomocí prostředí PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


