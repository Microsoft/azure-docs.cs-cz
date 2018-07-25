---
title: Přidat škálování uzlů Azure Stack | Dokumentace Microsoftu
description: Přidáte uzly do jednotek škálování ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: thoroet
ms.openlocfilehash: 15c46bb0fee4689b614636d8c9e01e36f7f0be3d
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228003"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Přidat další škálovací jednotku uzly ve službě Azure Stack

Operátoři Azure stacku zvýšit celkové kapacity existující škálovací jednotku tak, že přidáte další fyzického počítače. Fyzický počítač se také označuje jako uzel jednotek škálování. Každý nový uzel škálovací jednotky, které přidáte, musí být homogenní v typ procesoru, paměti a číslo disku a velikost na uzly, které jsou již přítomny v jednotce škálování.  

> [!NOTE]  
Je nutné spustit Azure Stack 1807 nebo novější, chcete-li přidat další škálovací jednotku uzly.

Přidat uzel jednotek škálování, fungují ve službě Azure Stack a spuštění nástroje od výrobce hardwaru zařízení (OEM). Nástroje pro výrobce OEM běží na hostiteli životního cyklu hardwaru (HLH) zajistit, aby že nové fyzického počítače odpovídá stejnou úroveň firmware jako stávající uzly.

Následující vývojový diagram zobrazuje obecný postup k přidání uzlu jednotek škálování.

![Přidat škálovací jednotku tok](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *Určuje, zda OEM dodavatele hardwaru představuje umístění fyzického serveru do racku a aktualizuje firmware se liší v závislosti na vaší smlouvu o podpoře.*

Chcete-li přidat nový uzel může trvat několik hodin nebo dní.

## <a name="add-scale-unit-nodes"></a>Přidání uzlů jednotky škálování 
Následující kroky jsou základní přehled o tom, jak přidat uzel. Není podle těchto kroků bez první odkazující na dokumentaci k rozšíření kapacity poskytnutou výrobcem OEM.

1. Umístěte nového fyzického serveru do racku a zapojení odpovídajícím způsobem. 
2. Nakonfigurujte správnou IP adresou v řadiče pro správu základní desky (BMC) a použít všechna nastavení systému BIOS na dokumentaci k poskytnutou výrobcem OEM.
3. Pomocí nástrojů, které jsou k dispozici od výrobce hardwaru, které běží na HLH použijte aktuální firmware základní pro všechny součásti.
4. Spusťte operaci přidat uzel na portálu správy Azure Stack.
5. Ověřte, že úspěšné operaci přidat uzel. Uděláte to tak, zkontrolujte, [ **stav** jednotky škálování](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Přidání uzlu 
Konzoly pro správu nebo prostředí PowerShell slouží k přidání nových uzlů. Operace přidání uzlu nejprve přidá nový uzel jednotek škálování jako dostupné výpočetní kapacitu a automaticky rozšíří kapacitu úložiště. Kapacita rozbalí automaticky, protože Azure Stack je hyperkonvergovaného systému kde *compute* a *úložiště* škálovat společně.

### <a name="use-the-admin-console"></a>Použití konzoly pro správu
1. Přihlaste se k portálu pro správu služby Azure Stack jako operátory Azure stacku.
2. Přejděte do **Správa oblastí** > **jednotek škálování**a pak vyberte jednotky škálování, který chcete rozšířit kapacitu pro.  
   ![Vyberte jednotku škálování](media/azure-stack-add-scale-node/select-node1.png)

   ![Zobrazit podrobnosti o jednotce škálování](media/azure-stack-add-scale-node/select-node2.png)
 
3. Jsou vyžadovány následující parametry:  
   - **IP adresa řadiče BMC** pro každý uzel jednotka škálování je přidat. (Jednu IP adresu na řádek.) ![Přidat uzel](media/azure-stack-add-scale-node/add-node.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

Použití **New-AzsScaleUnitNodeObject** rutiny pro přidání jednoho nebo více uzlů.  

Než použijete některý z následujících ukázkových skriptech Powershellu, nahraďte hodnoty *názvy wwnn* a *IP adresy* s hodnotami z vašeho prostředí Azure Stack.

**Pro jednotlivé uzly:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

**Přidejte více uzlů:**
  ```powershell
  ## Add multiple nodes 
  $NewNode1=New-AzsScaleUnitNodeObject -computername "<name_of_new_node01>"  -BMCIPv4Address "<BMCIP_address_of_new_node01>" 
 
  $NewNode2=New-AzsScaleUnitNodeObject -computername "<name_of_new_node02>" -BMCIPv4Address “<BMCIP_address_of_new_node02>”$ 
 
  Add-AzsScaleUnitNode -NodeList @($NewNode1,$NewNode2) -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```

## <a name="monitor-add-node-operations"></a>Monitorování operací přidat uzel 
Portál pro správu nebo prostředí PowerShell můžete použít k získání stavu operace přidání uzlu. Přidání uzlu operace může trvat několik hodin až dní na provedení.

### <a name="use-the-admin-console"></a>Použití konzoly pro správu 
K monitorování přidání nového uzlu, v portálu pro správu zkontrolujte jednotka škálování nebo objekty uzel jednotek škálování. Uděláte to tak, přejděte na **Správa oblastí** > **jednotek škálování**. Potom vyberte jednotku škálování nebo škálovací jednotku uzel, který chcete zkontrolovat. 

### <a name="use-powershell"></a>Použití prostředí PowerShell
Stav jednotky škálování a škálovací jednotku uzly se dá načíst pomocí Powershellu takto:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>Stav uzlu operace přidání 
**Pro jednotku škálování:**
|Status               |Popis  |
|---------------------|---------|
|Spuštěno              |Všechny uzly jsou aktivně účasti v jednotce škálování.|
|Zastaveno              |Uzel jednotka škálování je mimo provoz nebo nedostupný.|
|Rozbalení            |Jeden nebo více uzlů jednotky škálování se aktuálně přidávají jako výpočetní kapacitu.|
|Konfigurace úložiště  |Došlo k rozbalení výpočetní kapacity a konfigurace úložiště je spuštěná.|
|Vyžaduje nápravy |Byla zjištěna chyba, která vyžaduje jeden nebo více uzlů jednotky škálování opravit.|


**Pro uzel jednotky škálování:**
|Status                |Popis  |
|----------------------|---------|
|Spuštěno               |Uzel je aktivně účasti v jednotce škálování.|
|Zastaveno               |Uzel není k dispozici.|
|Přidávání                |Uzel je aktivně přidává na jednotce škálování.|
|Probíhají opravy             |Uzel je aktivně opraví.|
|Údržba           |Uzel pozastaví a žádné aktivní uživatel úloha běží. |
|Vyžaduje nápravy  |Byla zjištěna chyba, která vyžaduje uzel, který má být opraven.|


## <a name="troubleshooting"></a>Řešení potíží
Tady jsou běžné problémy, kterým dochází při přidávání uzlu. 

**Scénář 1:** selže operace uzel jednotky škálování přidat, ale jeden nebo více uzlů, které jsou uvedeny ve stavu Zastaveno.  
- Náprava: Použití operace opravy opravit jeden nebo více uzlů. Najednou lze spustit pouze jeden opravnou operaci.

**Scénář 2:** byly přidány jeden nebo více uzlů jednotek škálování, ale rozšíření úložiště se nezdařilo. V tomto scénáři objekt škálovací jednotky uzel hlásí stav spuštění, ale není spuštěna úloha konfigurace úložiště.  
- Náprava: Použijte privilegovaný koncový bod Pokud chcete zkontrolovat stav úložiště spuštěním následující rutiny Powershellu:
 
**Scénář 3:** obdržíte výstrahu, která označuje neúspěšná úloha horizontální navýšení kapacity úložiště.  
- Náprava: V tomto případě plánovaného úložiště se nezdařilo. Tento problém, musíte se obrátit na podporu.


## <a name="next-steps"></a>Další kroky 
Kontrola [uzlu akce](azure-stack-node-actions.md) 
