---
title: Spuštění příkazů ve spuštěné instanci kontejneru
description: Zjistěte, jak spustit příkaz v kontejneru, který je aktuálně spuštěný v azure container instances
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: de48e6ac246e2b0751561b4c60bb63d88b599bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247199"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Spuštění příkazu v spuštěné instanci kontejneru Azure

Služba Azure Container Instances podporuje spuštění příkazu ve spuštěném kontejneru. Spuštění příkazu v kontejneru, který jste už spustili, je užitečné zejména při vývoji aplikací a při řešení potíží. Nejběžnější použití této funkce je spuštění interaktivního prostředí, které umožňuje ladit problémy ve spuštěném kontejneru.

## <a name="run-a-command-with-azure-cli"></a>Spuštění příkazu pomocí azure cli

Spusťte příkaz v běžícím kontejneru s [externím kontejnerem az][az-container-exec] v [příkazovém příkazovém příkazu Azure][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Chcete-li například spustit shell Bash v kontejneru Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

V ukázkovém výstupu níže je prostředí Bash spuštěno v běžícím kontejneru Linuxu, který poskytuje terminál, ve kterém `ls` je spuštěn:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

V tomto příkladu je příkazový řádek spuštěn ý v kontejneru Nanoserver:

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
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

Pokud vaše [skupina kontejnerů](container-instances-container-groups.md) obsahuje více kontejnerů, jako je například kontejner aplikace a sajdkárna protokolování, zadejte název kontejneru, ve kterém chcete spustit příkaz s `--container-name`.

Například ve skupině kontejnerů *mynginx* jsou dva kontejnery, *nginx-app* a *logger*. Spuštění prostředí v kontejneru *nginx-app:*

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Omezení

Azure Container Instances aktuálně podporuje spuštění jednoho procesu s [exec kontejneru a][az-container-exec]nelze předat argumenty příkazu. Nelze například řetězit příkazy `sh -c "echo FOO && echo BAR"`jako `echo FOO`v aplikaci nebo spustit .

## <a name="next-steps"></a>Další kroky

Další informace o dalších nástrojích pro řešení potíží a běžných problémech s nasazením v [tématu Poradce při potížích s kontejnery a problémy s nasazením v instanci kontejnerů Azure](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure