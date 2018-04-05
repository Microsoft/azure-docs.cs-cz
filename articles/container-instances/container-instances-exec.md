---
title: Spuštění příkazů v spuštěných kontejnerů v Azure kontejner instancí
description: Zjistěte, jak provést příkaz kontejner, který je aktuálně spuštěna v Azure kontejner instancí
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.openlocfilehash: 539c1dadf74923e255e1d07e6010290d52665290
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Spuštění příkazu v instanci spuštěná Azure kontejneru

Azure instancí kontejnerů podporuje spouštění příkazu v běžící kontejneru. Spuštění příkazu v kontejneru, který jste už začali se hodí zejména při vývoji aplikací a řešení potíží. Tato funkce slouží nejčastěji Spusťte interaktivní prostředí tak, že ladíte problémy v běžící kontejneru.

## <a name="run-a-command-with-azure-cli"></a>Spuštění příkazu pomocí rozhraní příkazového řádku Azure

Spuštění příkazu v kontejneru spuštěné s [az kontejneru exec] [ az-container-exec] v [rozhraní příkazového řádku Azure][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Chcete-li například spustit prostředí Bash v kontejner Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Příklad níže ve výstupu, je spustit prostředí Bash ve spuštěné Linux kontejner, poskytuje terminálu, ve kterém `ls` se spustí:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

V tomto příkladu se spustí příkazový řádek v běžící Nanoserver kontejneru:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Skupiny více kontejnerů

Pokud vaše [skupina kontejneru](container-instances-container-groups.md) má více kontejnerů, například kontejner aplikace a něho protokolování, zadejte název kontejneru, ve kterém se má spustit příkaz s `--container-name`.

Například ve skupině kontejneru *mynginx* jsou dva kontejnery *nginx aplikace* a *protokolovač*. Spustit prostředí na *nginx aplikace* kontejneru:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Omezení

Azure instancí kontejnerů aktuálně podporuje spouštění jeden proces s [az kontejneru exec][az-container-exec], a nemůžete předat argumenty příkazu. Například nelze řetězu příkazy, jako je v `sh -c "echo FOO && echo BAR"`, nebo spuštění `echo FOO`.

## <a name="next-steps"></a>Další postup

Další informace o dalších nástrojů pro řešení problémů a běžné problémy při nasazení v [potíží kontejneru a nasazení v Azure kontejner instancí](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure