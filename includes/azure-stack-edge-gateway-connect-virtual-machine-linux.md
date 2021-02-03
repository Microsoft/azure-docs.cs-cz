---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 92688051cf591470246094dd0d8ea1d436a8efb1
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99499984"
---
Připojte se k virtuálnímu počítači pomocí privátní IP adresy, kterou jste předali při vytváření virtuálního počítače.

Otevřete relaci SSH pro připojení k IP adrese.

`ssh -l <username> <ip address>`

Po zobrazení výzvy zadejte heslo, které jste nastavili při vytváření virtuálního počítače.

Pokud potřebujete zadat klíč SSH, použijte tento příkaz.

`ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236`

Tady je ukázkový výstup při připojování k virtuálnímu počítači:

```powershell
PS C:\07-30-2020\linux> ssh -l Administrator 10.126.68.186
Administrator@10.126.68.186's password:
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 5.0.0-1027-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Jul 30 22:56:11 UTC 2020

  System load:  0.0               Processes:           105
  Usage of /:   5.6% of 28.90GB   Users logged in:     0
  Memory usage: 12%               IP address for eth0: 10.126.68.186
  Swap usage:   0%

 * Are you ready for Kubernetes 1.19? It's nearly here! Try RC3 with
   sudo snap install microk8s --channel=1.19/candidate --classic

   https://www.microk8s.io/ has docs and details.

68 packages can be updated.
0 updates are security updates.


*** System restart required ***

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

Administrator@mylinuxvm:
```

Pokud jste při vytváření virtuálního počítače použili veřejnou IP adresu, můžete k ní připojit virtuální počítač pomocí této IP adresy. Získání veřejné IP adresy: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
Veřejná IP adresa v tomto případě bude stejná jako soukromá IP adresa, kterou jste předali během vytváření rozhraní virtuální sítě.