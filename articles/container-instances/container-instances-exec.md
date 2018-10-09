---
title: Spusťte příkazy v spuštěných kontejnerů ve službě Azure Container Instances
description: Zjistěte, jak spustit příkaz do kontejneru, který aktuálně běží ve službě Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: 577e2386c352798bc21a2c78b22726128ac7cf0a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854080"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Provedení příkazu do funkční Azure container instance

Služba Azure Container Instances podporuje spuštění příkazu v spuštěný kontejner. Spuštění příkazu v kontejneru, který jste už začali se hodí zejména při vývoji aplikací a řešení potíží. Nejběžnější použití této funkce je ke spuštění interaktivní prostředí, takže můžete ladit problémy v spuštěný kontejner.

## <a name="run-a-command-with-azure-cli"></a>Spuštění příkazu pomocí rozhraní příkazového řádku Azure

Spuštění příkazu v spuštěný kontejner s [az container exec] [ az-container-exec] v [rozhraní příkazového řádku Azure][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Chcete-li například spusťte prostředí Bash v kontejneru Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Následující ukázkový výstup, prostředí Bash spouští ve složce spuštěný kontejner Linuxu, poskytování terminál, ve kterém `ls` spuštění:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

V tomto příkladu se spuštěný kontejner Nanoserver spouští příkazový řádek:

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

Pokud vaše [skupinu kontejnerů](container-instances-container-groups.md) má více kontejnery, jako je kontejner aplikace a sajdkáry protokolování, zadejte název kontejneru, ve kterém chcete spustit příkaz s `--container-name`.

Například ve skupině kontejnerů *mynginx* jsou dva kontejnery *nginx aplikace* a *protokolovací nástroj*. Ke spuštění prostředí *nginx aplikace* kontejneru:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Omezení

Služba Azure Container Instances v současné době podporuje spuštění jednoho procesu s [az container exec][az-container-exec], a nemůžete předat argumenty příkazu. Nelze například zřetězení příkazů, jako jsou v `sh -c "echo FOO && echo BAR"`, nebo když spouští `echo FOO`.

## <a name="next-steps"></a>Další postup

Další informace o dalších nástrojů pro odstraňování problémů a běžným problémům s nasazením v [kontejnerů a nasazení řešení problémů s ve službě Azure Container Instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure