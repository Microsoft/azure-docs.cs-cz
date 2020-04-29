---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174946"
---
Azure Virtual Machines (virtuální počítače) procházejí různými stavy, které je možné zařadit do stavu *zřizování* a *napájení* . Účelem tohoto článku je popsání těchto stavů a konkrétně zdůraznění, kdy se zákazníkům účtuje použití instance. 

## <a name="power-states"></a>Stavy napájení

Stav napájení představuje poslední známý stav virtuálního počítače.

![Diagram stavu napájení virtuálního počítače](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Následující tabulka obsahuje popis každého stavu instance a označuje, zda se účtuje za použití instance nebo ne.

<table>
<tr>
<th>
Stav
</th>
<th>
Popis
</th>
<th>
Fakturace využití instancí
</th>
</tr>
<tr>
<td>
<p><b>Začátek</b></p>
</td>
<td>
<p>Spouští se virtuální počítač.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Neúčtuje se</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Spuštěno</b></p>
</td>
<td>
<p>Normální pracovní stav pro virtuální počítač</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Účtuje</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zastavování</b></p>
</td>
<td>
<p>Toto je přechodný stav. Po dokončení se zobrazí jako **Zastaveno**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Účtuje</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zastaveno</b></p>
</td>
<td>
<p>Virtuální počítač se vypnul v hostovaném operačním systému nebo pomocí rozhraní stavu PowerOff API.</p>
<p>Hardware je stále přidělen k virtuálnímu počítači a zůstane na hostiteli. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Fakturované&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Rušení přidělení</b></p>
</td>
<td>
<p>Přechodný stav. Po dokončení se virtuální počítač zobrazí jako **navrácené**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nefakturovatelné&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Přidělení zrušeno</b></p>
</td>
<td>
<p>Virtuální počítač se úspěšně zastavil a odebral z hostitele. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Neúčtuje se</b></p>
</td>
</tr>
</tbody>
</table>


&#42;některých prostředků Azure, jako jsou disky a sítě, se účtují poplatky. K softwarovým licencím na instanci se neúčtují poplatky.

## <a name="provisioning-states"></a>Stavy zřizování

Stav zřizování je stav operace iniciované uživatelem na úrovni virtuálního počítače. Tyto stavy jsou oddělené od stavu napájení virtuálního počítače.

- **Vytvoření** – vytvoření virtuálního počítače

- **Update** – aktualizuje model pro existující virtuální počítač. Některé nemodelované změny virtuálních počítačů, jako je spuštění nebo restartování, spadají do části aktualizace.

- **Odstranění** – odstranění virtuálního počítače.

- Zrušení **přidělení** – znamená, že se virtuální počítač zastaví a odebere z hostitele. Zrušení přidělení virtuálního počítače se považuje za aktualizaci, aby se zobrazily stavy zřizování související s aktualizací.



Zde jsou přechodné stavy operace poté, co platforma přijala akci iniciované uživatelem:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Stavy</b></p>
</td>
<td width="366">
<p>Popis</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Vytváření</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Doplnění</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Odstraňuje</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Stavy zřizování operačního systému</b></p>
</td>
<td width="366">
<p>Pokud se vytvoří virtuální počítač s bitovou kopií operačního systému, a ne s specializovanou imagí, můžete dodržovat tyto podstavy:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; , že virtuální počítač běží a probíhá instalace hostovaného operačního systému. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; krátkodobý stav. Virtuální počítač se rychle přepřechodem na **úspěch** , pokud není potřeba instalovat nějaká rozšíření. Instalace rozšíření může chvíli trvat. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Poznámka</b>: zřizování operačního systému může přejít na **neúspěšné** , pokud dojde k selhání operačního systému nebo pokud se operační systém neinstaluje v čase. Zákazníkům se bude účtovat nasazený virtuální počítač v infrastruktuře.</p>
</td>
</tr>
</table>


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

