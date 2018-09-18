---
title: Přidat škálování uzlů Azure Stack | Dokumentace Microsoftu
description: Přidáte uzly do jednotek škálování ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: 3ce74cdb610f2902133459b913f53bb7809cb4b7
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982993"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Přidat další škálovací jednotku uzly ve službě Azure Stack

Operátoři Azure stacku zvýšit celkové kapacity existující škálovací jednotku tak, že přidáte další fyzického počítače. Fyzický počítač se také označuje jako uzel jednotek škálování. Každý nový uzel škálovací jednotky, které přidáte, musí být homogenní v typ procesoru, paměti a číslo disku a velikost na uzly, které jsou již přítomny v jednotce škálování.

> [!NOTE]  
Je nutné spustit Azure Stack 1807 nebo novější, chcete-li přidat další škálovací jednotku uzly.

Přidat uzel jednotek škálování, fungují ve službě Azure Stack a spuštění nástroje od výrobce hardwaru zařízení (OEM). Nástroje pro výrobce OEM běží na hostiteli životního cyklu hardwaru (HLH) zajistit, aby že nové fyzického počítače odpovídá stejnou úroveň firmware jako stávající uzly.

Následující vývojový diagram zobrazuje obecný postup k přidání uzlu jednotek škálování.

![Přidat škálovací jednotku tok](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *Určuje, zda OEM dodavatele hardwaru představuje umístění fyzického serveru do racku a aktualizuje firmware se liší v závislosti na vaší smlouvu o podpoře.*

Chcete-li přidat nový uzel může trvat několik hodin nebo dní.

> [!Note]  
> Nepokoušejte kteroukoli z následujících operací, zatímco již probíhá operace přidání jednotky škálování uzlu:
>
>  - Aktualizace služby Azure Stack
>  - Rotování certifikátů
>  - Zastavení služby Azure Stack
>  - Oprava škálovací jednotku uzlu


## <a name="add-scale-unit-nodes"></a>Přidání uzlů jednotky škálování

Následující kroky jsou základní přehled o tom, jak přidat uzel. Není podle těchto kroků bez první odkazující na dokumentaci k rozšíření kapacity poskytnutou výrobcem OEM.

1. Umístěte nového fyzického serveru do racku a zapojení odpovídajícím způsobem. 
2. Povolte porty fyzických přepínačů a upravit seznamy řízení přístupu (ACL), pokud je k dispozici.
3. Nakonfigurujte správnou IP adresou v řadiče pro správu základní desky (BMC) a použít všechna nastavení systému BIOS na dokumentaci k poskytnutou výrobcem OEM.
4. Pomocí nástrojů, které jsou k dispozici od výrobce hardwaru, které běží na HLH použijte aktuální firmware základní pro všechny součásti.
5. Spusťte operaci přidat uzel na portálu správy Azure Stack.
6. Ověřte, že úspěšné operaci přidat uzel. Uděláte to tak, zkontrolujte, [ **stav** jednotky škálování](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Přidání uzlu

Portál pro správu nebo prostředí PowerShell slouží k přidání nových uzlů. Operace přidání uzlu nejprve přidá nový uzel jednotek škálování jako dostupné výpočetní kapacitu a automaticky rozšíří kapacitu úložiště. Kapacita rozbalí automaticky, protože Azure Stack je hyperkonvergovaného systému kde *compute* a *úložiště* škálovat společně.

### <a name="use-the-admin-portal"></a>Použití portálu pro správu

1. Přihlaste se k portálu pro správu služby Azure Stack jako operátory Azure stacku.
2. Přejděte do **+ vytvořit prostředek** > **kapacity** > **uzel jednotek škálování**.
   ![Uzel jednotek škálování](media/azure-stack-add-scale-node/select-node1.png)
3. Na **přidat uzel** podokně, vyberte *oblasti*a pak vyberte *jednotka škálování* , že chcete přidat uzel, který má. Zadat také *IP adresa řadiče BMC* pro přidáváte uzel jednotek škálování. Najednou můžete přidat pouze jeden uzel.
   ![Přidat podrobnosti o uzlu](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>Použití prostředí PowerShell

Použití **New-AzsScaleUnitNodeObject** rutiny pro přidání uzlu.  

Než použijete některý z následujících ukázkových skriptech Powershellu, nahraďte hodnoty *názvy wwnn* a *IP adresy* s hodnotami z vašeho prostředí Azure Stack.

  > [!Note]  
  > Když pojmenováváte uzlu je nutné zachovat název víc než 15 znaků. Také nelze použít název, který obsahuje mezery nebo obsahuje některý z následujících znaků: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`,` }`, `_`.

**Přidání uzlu:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>Monitorování přidat uzel operace 
Portál pro správu nebo prostředí PowerShell můžete použít k získání stavu operace přidání uzlu. Přidání uzlu operace může trvat několik hodin až dní na provedení.

### <a name="use-the-admin-portal"></a>Použití portálu pro správu 
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
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Scénář 3:** obdržíte výstrahu, která označuje neúspěšná úloha horizontální navýšení kapacity úložiště.  
- Náprava: V tomto případě plánovaného úložiště se nezdařilo. Tento problém, musíte se obrátit na podporu.


## <a name="next-steps"></a>Další postup 
[Přidání veřejné IP adresy](azure-stack-add-ips.md) 
