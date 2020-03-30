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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174946"
---
Virtuální počítače Azure (VM) procházejí různými stavy, které lze kategorizovat do *zřizování* a *stavy napájení.* Účelem tohoto článku je popsat tyto stavy a konkrétně zvýraznit, když se zákazníkům účtují za použití instance. 

## <a name="power-states"></a>Stavy napájení

Stav napájení představuje poslední známý stav virtuálního soudu.

![Diagram stavu napájení virtuálního připojení](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

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
Fakturace využití instance
</th>
</tr>
<tr>
<td>
<p><b>Spuštění</b></p>
</td>
<td>
<p>Virtuální mísa se spouští.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Nefakturováno</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Spuštěno</b></p>
</td>
<td>
<p>Normální pracovní stav pro virtuální hod</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Účtovány</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zastavování</b></p>
</td>
<td>
<p>Jedná se o přechodný stav. Po dokončení se zobrazí jako **Zastaveno**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Účtovány</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zastaveno</b></p>
</td>
<td>
<p>Virtuální ho virtuálního se stavu vypínal z hostovaného operačního prostředí nebo pomocí poweroff ských api.</p>
<p>Hardware je stále přidělena virtuálního počítači a zůstane na hostiteli. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Fakturovaná&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Rušení přidělení</b></p>
</td>
<td>
<p>Přechodný stav. Po dokončení se virtuální virtuální město zobrazí jako **deallocated**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nefakturované&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Přidělení zrušeno</b></p>
</td>
<td>
<p>Virtuální ho virtuálního žena byla úspěšně zastavena a odebrána z hostitele. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nefakturováno</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Některé prostředky Azure, jako jsou disky a sítě, účtují poplatky. Za softwarové licence v instanci se neúčtují poplatky.

## <a name="provisioning-states"></a>Zřizovací státy

Stav zřizování je stav uživatelem iniciovaného, řízeného roviny operace na virtuálním počítači. Tyto stavy jsou oddělené od stavu napájení virtuálního soudu.

- **Vytvořit** – vytvoření virtuálního pracovního procesu.

- **Aktualizace** – aktualizuje model pro existující virtuální hod. Některé změny virtuálního počítače, které nejsou modelové, jako je například spuštění a restartování, také spadají pod aktualizaci.

- **Odstranit** – odstranění virtuálního mísa.

- **Navrátit** – je místo, kde je virtuální ms zastavena a odebrána z hostitele. Zrušení přidělení virtuálního soudu se považuje za aktualizaci, takže se zobrazí stavy zřizování související s aktualizací.



Zde jsou přechodné provozní stavy poté, co platforma přijala akci inicionouji uživatelem:

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
<p><b>Odstranění</b></p>
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
<p><b>Stavy zřizování operačního spoje</b></p>
</td>
<td width="366">
<p>Pokud virtuální ho virtuální ho samalý a ne se specializovanou bitovou kopii, lze pozorovat následující dílčí stavy:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; Virtuální ho diář i instalace hostovaného operačního systému probíhá. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; Krátkodobý stav. Virtuální ho virtuálního zařízení rychle přechází na **úspěch,** pokud není nutné nainstalovat žádná rozšíření. Instalace rozšíření může nějakou dobu trvat. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Poznámka:</b>Zřizování operačního systému může přejít na **Selhání,** pokud dojde k selhání operačního systému nebo operační systém se nenainstaluje včas. Zákazníkům se bude účtovat nasazený virtuální počítač v infrastruktuře.</p>
</td>
</tr>
</table>


Po dokončení operace virtuální ho dispozice přejde do jednoho z následujících stavů:

- **Úspěšné** – akce iniciované uživatelem byly dokončeny.

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

 

- **Nezdařilo se** – představuje neúspěšnou operaci. Další informace a možná řešení naleznete v kódech chyb.

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



## <a name="vm-instance-view"></a>Zobrazení instance virtuálního virtuálního virtuálního soudu

Rozhraní API zobrazení instance poskytuje informace o stavu spouštění virtuálního soudu. Další informace najdete v dokumentaci [k rozhraní API virtuálních počítačů – instance.](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview)

Průzkumník prostředků Azure poskytuje jednoduché rozhraní pro zobrazení stavu spuštěného virtuálního počítače: [Průzkumník prostředků](https://resources.azure.com/).

Stavy zřizování jsou viditelné ve vlastnostech virtuálních počítačů a zobrazení instance. Stavy napájení jsou k dispozici například zobrazení virtuálního mísu. 

