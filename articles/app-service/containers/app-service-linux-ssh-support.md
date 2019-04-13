---
title: Podpora SSH pro službu App Service v Linuxu – Azure | Dokumentace Microsoftu
description: Další informace o používání SSH se službou Azure App Service v Linuxu.
keywords: azure app service, web app, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2d84a4dd0b69ce9ca7fc594dffce3238c620c426
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543969"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Podpora SSH pro Azure App Service v Linuxu

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) se běžně používá ke spuštění příkazů pro správu vzdáleně z příkazového řádku terminálu. App Service v Linuxu podporuje SSH do kontejneru pro aplikace s jednotlivými integrované Image Dockeru pro zásobník modulu Runtime nových webových aplikací. 

![Zásobníky modulu runtime](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Pro vlastní Image Dockeru, tím, že nakonfigurujete SSH server do své vlastní image.

Do tohoto kontejneru můžete také připojit přímo ze svého místního vývojového počítače pomocí SSH a SFTP.

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Podpora SSH pomocí vlastní Image Dockeru

Zobrazit [konfigurace SSH do vlastního kontejneru](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Otevřete relaci SSH ze vzdáleného prostředí

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview.
>

Pomocí protokolu TCP tunelové propojení, které můžete vytvořit síťové připojení mezi počítačem pro vývoj a Web App for Containers přes ověřené připojení soketu WebSocket. Umožňuje otevřít relaci SSH s vašeho kontejneru ve službě App Service z klienta podle vašeho výběru.

Abyste mohli začít, je potřeba nainstalovat [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Chcete-li zjistit, jak to funguje, bez instalace rozhraní příkazového řádku Azure, otevřete [Azure Cloud Shell](../../cloud-shell/overview.md). 

Vytvořit vzdálené připojení k aplikaci pomocí [az webapp-připojení ke vzdálené vytvoření](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) příkazu. Zadejte  _\<id předplatného >_,  _\<název skupiny >_ a \__ < název aplikace > pro vaši aplikaci.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` na konci příkazu je pouze ke zvýšení pohodlí Pokud používáte Cloud Shell. Spustí proces na pozadí, takže můžete spuštěním následujícího příkazu ve stejném prostředí.

Výstup tohoto příkazu obsahuje informace, že budete muset otevřít relaci SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Otevřete relaci SSH s kontejnerech s klientem nástroje podle vašeho výběru použitím místní port. Následující příklad používá výchozí [ssh](https://ss64.com/bash/ssh.html) příkaz:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Jakmile se zobrazí výzva, zadejte `yes` pokračujte v připojování. Zobrazí se výzva k zadání hesla. Použití `Docker!`, které se zobrazilo, dříve.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Jakmile je ověřen, byste měli vidět na úvodní obrazovce relace.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Nyní jste připojeni k vašemu konektoru.  

Zkuste spustit [horní](https://ss64.com/bash/top.html) příkazu. Byste měli vidět procesu vaší aplikace v seznamu proces. Následující ukázkový výstup je se `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Další postup

Otázky a připomínky můžete publikovat na [fórum Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Další informace o Web App for Containers naleznete v tématu:

* [Úvod do vzdáleného ladění aplikací v Node.js ve službě Azure App Service z VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Jak používat vlastní image Dockeru pro službu Web App for Containers](quickstart-docker-go.md)
* [Použití .NET Core ve službě Azure App Service v Linuxu](quickstart-dotnetcore.md)
* [Použití Ruby ve službě Azure App Service v Linuxu](quickstart-ruby.md)
* [Azure App Service Web App for Containers – Nejčastější dotazy](app-service-linux-faq.md)
