---
title: Přístup SSH pro kontejnery platformy Linux
description: Můžete otevřít relaci SSH k kontejneru Linux v Azure App Service. Vlastní kontejnery Linux jsou podporovány s některými úpravami vlastní image.
keywords: Azure App Service, Web App, Linux, OSS
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/23/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8e9dd76b60d05b9fa5e3a4aaf7ccc6663f4a969b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709032"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Otevřete relaci SSH k kontejneru Linux v Azure App Service

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) se běžně používá ke vzdálenému spouštění příkazů pro správu z terminálu příkazového řádku. App Service v systému Linux poskytuje podporu SSH do kontejneru aplikace. 

![Linux App Service SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

Ke kontejneru se můžete připojit také přímo z místního vývojového počítače pomocí SSH a SFTP.

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Použití podpory SSH s vlastními imagemi Docker

Viz [Konfigurace SSH ve vlastním kontejneru](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Otevřít relaci SSH ze vzdáleného prostředí

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview.
>

Pomocí tunelového propojení TCP můžete vytvořit síťové připojení mezi vývojovým počítačem a Web App for Containers přes ověřované připojení protokolu WebSocket. Umožňuje otevřít relaci SSH s kontejnerem spuštěným v App Service z klienta podle vašeho výběru.

Chcete-li začít, je nutné nainstalovat rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). Pokud chcete zjistit, jak funguje bez instalace rozhraní příkazového řádku Azure, otevřete [Azure Cloud Shell](../cloud-shell/overview.md). 

Pomocí příkazu [AZ WebApp Remote-Connection Create](/cli/azure/ext/webapp/webapp/remote-connection#ext-webapp-az-webapp-remote-connection-create) spusťte vzdálené připojení k aplikaci. Zadejte _\<subscription-id>_ , _\<group-name>_ a \_ \<app-name> _ pro vaši aplikaci.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` na konci příkazu je jenom pro pohodlí, pokud používáte Cloud Shell. Spustí proces na pozadí, takže můžete spustit další příkaz ve stejném prostředí.

> [!NOTE]
> Pokud se tento příkaz nepovede, ujistěte se, že je [vzdálené ladění](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) *zakázané* , a to pomocí následujícího příkazu:
>
> ```azurecli-interactive
> az webapp config set --resource-group <resource-group-name> -n <app-name> --remote-debugging-enabled=false
> ```

Výstup příkazu vám poskytne informace, které potřebujete k otevření relace SSH.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Pomocí místního portu otevřete v kontejneru relaci SSH s vámi zvoleným klientem. Následující příklad používá výchozí příkaz [SSH](https://ss64.com/bash/ssh.html) :

```bash
ssh root@127.0.0.1 -p <port>
```

Po zobrazení výzvy zadejte, že `yes` Chcete pokračovat v připojování. Pak se zobrazí výzva k zadání hesla. Použijte `Docker!` , které jste si zobrazili dříve.

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

Jakmile budete ověřeni, měli byste vidět úvodní obrazovku relace.

<pre>
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  &gt;
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
</pre>

Nyní jste připojeni k vašemu konektoru.  

Zkuste spustit [horní](https://ss64.com/bash/top.html) příkaz. Měli byste být schopni zobrazit proces vaší aplikace v seznamu procesů. V níže uvedeném příkladu je to s `PID 263` .

<pre>
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
</pre>

## <a name="next-steps"></a>Další kroky

Otázky a připomínky můžete publikovat na [fóru Azure](/answers/topics/azure-webapps.html).

Další informace o Web App for Containers najdete v tématech:

* [Představujeme vzdálené ladění aplikací Node.js v Azure App Service z VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Rychlý Start: spuštění vlastního kontejneru v App Service](quickstart-custom-container.md?pivots=container-linux)
* [Použití Ruby ve službě Azure App Service v Linuxu](quickstart-ruby.md)
* [Azure App Service Web App for Containers – Nejčastější dotazy](faq-app-service-linux.md)
