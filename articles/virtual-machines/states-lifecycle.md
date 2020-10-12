---
title: Životní cyklus a stavy virtuálního počítače v Azure
description: Přehled životního cyklu virtuálního počítače v Azure, včetně popisů různých stavů, které virtuální počítač může být v jakémkoli okamžiku.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: cd104a31fdba932481889db198ae3cc4998a4466
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88261884"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Životní cyklus a stavy virtuálních počítačů

Azure Virtual Machines (virtuální počítače) procházejí různými stavy, které je možné zařadit do stavu *zřizování* a *napájení* . Účelem tohoto článku je popsání těchto stavů a konkrétně zdůraznění, kdy se zákazníkům účtuje použití instance. 

## <a name="power-states"></a>Stavy napájení

Stav napájení představuje poslední známý stav virtuálního počítače.

![Diagram stavu napájení virtuálního počítače](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Následující tabulka obsahuje popis každého stavu instance a označuje, zda se účtuje za použití instance nebo ne.

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="":::

   **Popis**

   :::column-end:::
   :::column span="":::

   **Využití instance se účtuje**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Spouštění**

   :::column-end:::
   :::column span="":::

   Spouští se virtuální počítač.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **Neúčtuje se**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Spuštěno**

   :::column-end:::
   :::column span="":::

   Normální pracovní stav pro virtuální počítač

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **Účtuje**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Zastavování**

   :::column-end:::
   :::column span="":::

   Toto je přechodný stav. Po dokončení se zobrazí jako **Zastaveno**.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Účtuje**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Zastaveno**

   :::column-end:::
   :::column span="":::

   Virtuální počítač se vypnul v hostovaném operačním systému nebo pomocí rozhraní stavu PowerOff API.

   Hardware je stále přidělen k virtuálnímu počítači a zůstane na hostiteli.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Účtuje***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Rušení přidělení**

   :::column-end:::
   :::column span="":::

   Přechodný stav. Po dokončení se virtuální počítač zobrazí jako **navrácené**.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Neúčtuje se***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Přidělení zrušeno**

   :::column-end:::
   :::column span="":::

   Virtuální počítač se úspěšně zastavil a odebral z hostitele.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Neúčtuje se**

   :::column-end:::
:::row-end:::


&#42; některých prostředků Azure, jako jsou disky a sítě, se účtují poplatky. K softwarovým licencím na instanci se neúčtují poplatky.

## <a name="provisioning-states"></a>Stavy zřizování

Stav zřizování je stav operace iniciované uživatelem na úrovni virtuálního počítače. Tyto stavy jsou oddělené od stavu napájení virtuálního počítače.

- **Vytvoření** – vytvoření virtuálního počítače

- **Update** – aktualizuje model pro existující virtuální počítač. Některé nemodelované změny virtuálních počítačů, jako je spuštění nebo restartování, spadají do části aktualizace.

- **Odstranění** – odstranění virtuálního počítače.

- Zrušení **přidělení** – znamená, že se virtuální počítač zastaví a odebere z hostitele. Zrušení přidělení virtuálního počítače se považuje za aktualizaci, aby se zobrazily stavy zřizování související s aktualizací.



Zde jsou přechodné stavy operace poté, co platforma přijala akci iniciované uživatelem:

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="2":::

   **Popis**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Vytváření**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Doplnění**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **odstraňování**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Stavy zřizování operačního systému**
   
   :::column-end:::
   :::column span="2":::

   **Popis**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   Pokud se vytvoří virtuální počítač s bitovou kopií operačního systému, a ne s specializovanou imagí, můžete dodržovat tyto podstavy:

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   Virtuální počítač je spuštěný a probíhá instalace hostovaného operačního systému.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   Krátkodobý stav. Virtuální počítač se rychle přepřechodem na **úspěch** , pokud není potřeba instalovat nějaká rozšíření. Instalace rozšíření může chvíli trvat.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **Poznámka**: zřizování operačního systému může přejít na **neúspěšné** , pokud dojde k selhání operačního systému nebo pokud se operační systém neinstaluje v čase. Zákazníkům se bude účtovat nasazený virtuální počítač v infrastruktuře.

   :::column-end:::

:::row-end:::

Po dokončení operace se virtuální počítač převede do jednoho z následujících stavů:

- **Úspěch** – akce iniciované uživatelem byly dokončeny.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Chyba** – představuje neúspěšnou operaci. Pokud chcete získat další informace a možná řešení, přečtěte si kódy chyb.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Zobrazení instance virtuálního počítače

Rozhraní API pro zobrazení instancí poskytuje informace o běhu virtuálního počítače. Další informace najdete v dokumentaci k rozhraní API pro [zobrazení Virtual Machines-instance](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) .

Průzkumník prostředků Azure poskytuje jednoduché uživatelské rozhraní pro zobrazení stavu spuštěného virtuálního počítače: [Průzkumník prostředků](https://resources.azure.com/).

Stavy zřizování jsou viditelné pro vlastnosti virtuálního počítače a zobrazení instance. Stavy napájení jsou k dispozici v zobrazení instance virtuálního počítače.

Pokud chcete načíst stav napájení všech virtuálních počítačů ve vašem předplatném, použijte [Virtual Machines – Vypíše všechna rozhraní API](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall) s parametrem **statusOnly** nastavenou na *hodnotu true*.

## <a name="next-steps"></a>Další kroky

Další informace o monitorování virtuálního počítače najdete v tématu [monitorování virtuálních počítačů v Azure](../azure-monitor/insights/monitor-vm-azure.md).