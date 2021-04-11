---
title: Stavy a fakturace Virtual Machines Azure
description: Přehled různých stavů, které může virtuální počítač zadat a kdy se uživateli fakturuje.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: c206cba3b23a0bf41ce32481980aa466e869bcde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596293"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Stavy a fakturace Virtual Machines Azure

Azure Virtual Machines (virtuální počítače) procházejí různými stavy, které je možné zařadit do stavu *zřizování* a *napájení* . Účelem tohoto článku je popsání těchto stavů a konkrétně zdůraznění, kdy se zákazníkům účtuje použití instance. 

## <a name="get-states-using-instance-view"></a>Získat stavy pomocí zobrazení instance

Rozhraní API pro zobrazení instancí poskytuje informace o běhu virtuálního počítače. Další informace najdete v dokumentaci k rozhraní API pro [zobrazení Virtual Machines-instance](/rest/api/compute/virtualmachines/instanceview) .

Průzkumník prostředků Azure poskytuje jednoduché uživatelské rozhraní pro zobrazení stavu spuštěného virtuálního počítače: [Průzkumník prostředků](https://resources.azure.com/).

Stavy zřizování jsou viditelné pro vlastnosti virtuálního počítače a zobrazení instance. Stavy napájení jsou k dispozici v zobrazení instance virtuálního počítače.

Pokud chcete načíst stav napájení všech virtuálních počítačů ve vašem předplatném, použijte [Virtual Machines – Vypíše všechna rozhraní API](/rest/api/compute/virtualmachines/listall) s parametrem **statusOnly** nastavenou na *hodnotu true*.

> [!NOTE]
> [Virtual Machines – Vypíše všechna rozhraní API](/rest/api/compute/virtualmachines/listall) s parametrem **statusOnly** nastavenou na hodnotu true načte stavy napájení všech virtuálních počítačů v rámci předplatného. V některých vzácných situacích ale nemusí být stav napájení k dispozici kvůli přerušovaným problémům v procesu načítání. V takových situacích doporučujeme opakovat použití stejného rozhraní API nebo pomocí [Azure Resource Health](../service-health/resource-health-overview.md) nebo [grafu prostředků Azure](..//governance/resource-graph/overview.md) ke kontrole stavu napájení vašich virtuálních počítačů.
 
## <a name="power-states-and-billing"></a>Stavy napájení a fakturace

Stav napájení představuje poslední známý stav virtuálního počítače.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Obrázek znázorňuje diagram stavů napájení, ke kterým může virtuální počítač přejít. ":::

Následující tabulka obsahuje popis každého stavu instance a označuje, zda se účtuje za použití instance nebo ne.

| Stav napájení | Description | Fakturace |  
|---|---|---|
| Spouštění| Virtuální počítač je právě zapnutý. |Bez fakturace * | 
| Spuštěno | Virtuální počítač je plně zapnutý. Toto je standardní pracovní stav. | Účtuje | 
| Zastavování | Toto je přechodný stav mezi spuštěním a zastavením. | Účtuje| 
|Zastaveno | Virtuální počítač se vypnul v hostovaném operačním systému nebo pomocí rozhraní stavu PowerOff API. V tomto stavu je ve virtuálním počítači stále zapůjčení základního hardwaru. Tento stav se také označuje jako *Zastaveno (přiděleno)*. | Účtuje | 
| Rušení přidělení | Toto je přechodný stav mezi spuštěním a navráceným přidělením. | Bez fakturace * | 
| Přidělení zrušeno | Virtuální počítač uvolnil zapůjčení základního hardwaru a je zcela vypnutý. Tento stav se také označuje jako *Zastaveno (přidělení zrušeno)*. | Bez fakturace * | 

&#42; některých prostředků Azure, jako jsou [disky](https://azure.microsoft.com/pricing/details/managed-disks) a [sítě](https://azure.microsoft.com/pricing/details/bandwidth/) , se budou dál účtovat poplatky.


## <a name="provisioning-states"></a>Stavy zřizování

Stav zřizování je stav operace iniciované uživatelem na úrovni virtuálního počítače. Tyto stavy jsou oddělené od stavu napájení virtuálního počítače.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="Obrázek ukazuje stavy zřizování, které může virtuální počítač projít.":::

| Stav zřizování | Description | Stav napájení | Fakturace | 
|---|---|---|---|
| Vytvořit | Vytváření virtuálních počítačů. | Spouštění | Bez fakturace * | 
| Aktualizace | Aktualizuje model pro existující virtuální počítač. Některé nemodelované změny virtuálního počítače, jako je spuštění a restartování, spadají do stavu aktualizace. | Spuštěno | Účtuje | 
| Odstranit | Odstranění virtuálního počítače. | Rušení přidělení | Bez fakturace * |
| Uvolnění | Virtuální počítač je plně zastaven a odebrán z podkladového hostitele. Zrušení přidělení virtuálního počítače se považuje za aktualizaci a zobrazí stav zřizování podobný aktualizaci. | Rušení přidělení | Bez fakturace * | 

&#42; některých prostředků Azure, jako jsou [disky](https://azure.microsoft.com/pricing/details/managed-disks) a [sítě](https://azure.microsoft.com/pricing/details/bandwidth/) , se budou dál účtovat poplatky.

## <a name="os-provisioning-states"></a>Stavy zřizování operačního systému
Stavy zřizování operačního systému platí jenom pro virtuální počítače vytvořené pomocí image operačního systému. Specializované obrázky nebudou tyto stavy zobrazovat. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="Image zobrazuje stav zřizování operačního systému, přes který může virtuální počítač přejít.":::

| Stav zřizování operačního systému | Description | Stav napájení | Fakturace | 
|---|---|---|---|
| OSProvisioningInProgress | Virtuální počítač je spuštěný a probíhá instalace hostovaného operačního systému. | Spuštěno | Účtuje | 
| OSProvisioningComplete | Toto je krátkodobý stav. Virtuální počítač rychle přejde z tohoto stavu na **úspěch**. Pokud stále probíhá instalace rozšíření, bude tento stav nadále zobrazen, dokud nebudou dokončeny. | Spuštěno | Účtuje | 
| Úspěšný | Akce iniciované uživatelem byly dokončeny. | Spuštěno | Účtuje | 
| Neúspěšný | Představuje neúspěšnou operaci. Další informace a možná řešení naleznete v kódu chyby. | Spuštěno  | Účtuje | 


## <a name="next-steps"></a>Další kroky
- Přečtěte si [dokumentaci Azure cost management a fakturace](../cost-management-billing/index.yml)
- Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) naplánujete nasazení.
- Další informace o monitorování virtuálního počítače najdete v tématu [monitorování virtuálních počítačů v Azure](../azure-monitor/vm/monitor-vm-azure.md).