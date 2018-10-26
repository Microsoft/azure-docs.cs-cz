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
ms.openlocfilehash: e1dc637d2f629b5002c2e8796fbd29a95478c392
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50035227"
---
Azure Virtual Machines (VM), projděte si různé stavy, které lze zařadit do *zřizování* a *power* stavy. Cílem tohoto článku je popisují tyto stavy a konkrétně zvýraznit, když se zákazníkům účtuje pro instanci využití. 

## <a name="power-states"></a>Stavy napájení

Stav napájení představuje poslední známý stav virtuálního počítače.

![Diagram stavu napájení virtuálního počítače](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Následující tabulka obsahuje popis jednotlivých stavech instanci a označuje, zda se účtuje za využití instance nebo ne.

<table>
<tr>
<th>
Stav
</th>
<th>
Popis
</th>
<th>
Instance fakturaci využití
</th>
</tr>
<tr>
<td>
<p><b>Spuštění</b></p>
</td>
<td>
<p>Virtuální počítač se spouští.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Není účtuje.</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Spuštění</b></p>
</td>
<td>
<p>Normální funkčního stavu pro virtuální počítač</p>
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
<p><b>Zastavení</b></p>
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
<p><b>Zastavení</b></p>
</td>
<td>
<p>Zastavení virtuálního počítače dolů z v rámci hostovaného operačního systému nebo pomocí rozhraní API stavu PowerOff.</p>
<p>Hardware je pořád ještě přidělená k virtuálnímu počítači a zůstane na hostiteli. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Není účtuje.&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Rušení přidělení</b></p>
</td>
<td>
<p>Přechodný stav. Po dokončení, virtuální počítač se zobrazí jako **přidělení zrušeno**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Není účtuje.&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>přidělení</b></p>
</td>
<td>
<p>Virtuální počítač byla úspěšně zastavena a odebrat z hostitele. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Není účtuje.</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Některé prostředky Azure, jako je například disků a sítí, budou účtovat poplatky za bez ohledu na stav instance. 

## <a name="provisioning-states"></a>Stavy zřizování

Stav zřizování je stav uživatelem iniciované, rovina řízení operace na virtuálním počítači. Tyto stavy jsou oddělené od stav napájení virtuálního počítače.

- **Vytvoření** – vytvoření virtuálního počítače.

- **Aktualizace** – aktualizace modelu pro existující virtuální počítač. Některé nemodelová změní k virtuálnímu počítači, jako je spusťte nebo restartujte také spadají pod aktualizace.

- **Odstranit** – odstranění virtuálního počítače.

- **Zrušit přidělení** – je, když je virtuální počítač zastavit a odebrat z hostitele. Rušení přidělení virtuálního počítače se považuje za aktualizace, proto se zobrazí zřizování stavy související s aktualizací.



Tady jsou stavy přechodné operaci po platformu přijal akce zahájená uživatelem:

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
<p><b>Aktualizace</b></p>
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
<p><b>Odstraňuje se</b></p>
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
<p>Pokud virtuální počítač se vytvoří pomocí image operačního systému a ne s specializované image, může být dodržen následující dílčí stavy:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; virtuální počítač běží a probíhá instalace hostovaného operačního systému. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; krátkodobou stavu. Virtuální počítač rychle přejde do **úspěch** Pokud žádná rozšíření je potřeba nainstalovat. Instalace rozšíření může trvat dobu. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Poznámka:</b>: zřizování operačního systému můžete přejít na **neúspěšné** Pokud dojde k selhání operačního systému nebo v čase není nainstalován operační systém. Zákazníkům se účtuje nasazených virtuálních počítačů na infrastruktuře.</p>
</td>
</tr>
</table>


Po dokončení operace virtuálního počítače přejde do jedné z následujících stavů:

- **Úspěšné** – dokončení akce zahájená uživatelem.

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

 

- **Nepovedlo** – představuje neúspěšnou operaci. Odkazovat na kódy chyb, chcete-li získat další informace a možná řešení.

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

Zobrazení instance rozhraní API poskytuje informace o stavu spuštění virtuálních počítačů. Další informace najdete v tématu [Virtual Machines – zobrazení Instance](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) dokumentace k rozhraní API.

Průzkumník prostředků Azure poskytuje jednoduché uživatelské rozhraní pro zobrazení stavu spuštění virtuálního počítače: [Průzkumníka prostředků](https://resources.azure.com/).

Stavy zřizování jsou viditelná ve vlastnostech virtuálního počítače a instance zobrazení. Stavy napájení jsou k dispozici v zobrazení instance virtuálního počítače. 

