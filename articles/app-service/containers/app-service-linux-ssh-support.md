---
title: Podpora SSH pro Azure App Service v systému Linux | Microsoft Docs
description: Další informace o použití SSH s Azure App Service v systému Linux.
keywords: služby Azure app service, webové aplikace, linux, operačních systémů
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: c2beb67a27b667d31402b903f38dbf116e9425d0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Podpora SSH pro Azure App Service v systému Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) se běžně používá k provedení příkazů pro správu vzdáleně z příkazového řádku terminálu. Aplikační služby v systému Linux poskytuje podporu SSH do kontejneru aplikace s jednotlivými předdefinované Docker obrázků použitých pro modul Runtime zásobník nové webové aplikace. 

![Zásobníky modulu runtime](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Pro vlastní Image Docker, konfigurací SSH serveru ve vaší vlastní image.

Můžete také připojit ke kontejneru přímo z místní vývojovém počítači pomocí protokolu SSH a SFTP.

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

Chcete-li připojení klienta SSH pomocí vašeho kontejneru, by měl běžet vaší aplikace.

Vložte následující adresu URL do prohlížeče a nahradit \<app_name > s názvem vaší aplikace:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Pokud již nejsou ověřené, je nutné k ověření s předplatným Azure připojit. Po ověření, uvidíte prostředí v prohlížeči, kde můžete spouštět příkazy uvnitř vaší kontejneru.

![Připojení SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Podpora použití SSH s vlastními obrázky Docker

Aby pro vlastní image Docker pro podporu SSH komunikace mezi klienta a kontejneru na portálu Azure proveďte následující kroky pro Docker image.

Tyto kroky jsou uvedeny v úložišti Azure App Service jako [příklad](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Zahrnout `openssh-server` instalace v [ `RUN` instrukce](https://docs.docker.com/engine/reference/builder/#run) v soubor Docker pro bitovou kopii a heslo pro kořenový účet sady `"Docker!"`.

    > [!NOTE]
    > Tato konfigurace nepovoluje externí připojení ke kontejneru. SSH lze přistupovat pouze prostřednictvím Kudu nebo webu SCM, což je ověřen pomocí přihlašovací údaje pro publikování.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Přidat [ `COPY` instrukce](https://docs.docker.com/engine/reference/builder/#copy) na soubor Docker zkopírovat [sshd_config](http://man.openbsd.org/sshd_config) do souboru */atd/ssh/* adresáře. Konfigurační soubor by měla být založena na sshd_config souboru v úložišti GitHub službě Azure-App-Service [zde](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > *Sshd_config* nebo připojení selže, soubor musí zahrnovat následující: 
    > * `Ciphers` musí obsahovat alespoň jednu z následujících: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` musí obsahovat alespoň jednu z následujících: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Zahrnout port 2222 v [ `EXPOSE` instrukce](https://docs.docker.com/engine/reference/builder/#expose) pro soubor Docker. I když je známé kořenové heslo, port 2222 není přístupný z internetu. Je k interní pouze portu přístupné pouze pomocí kontejnery v rámci sítě most privátní virtuální sítě.

    ```docker
    EXPOSE 2222 80
    ```

1. Zajistěte, aby se spustit službu SSH pomocí skriptu prostředí (podívejte se na příklad [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Soubor Docker používá [ `ENTRYPOINT` instrukce](https://docs.docker.com/engine/reference/builder/#entrypoint) pro spuštění skriptu.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Otevřete relaci SSH ze vzdálené prostředí

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview.
>

Pomocí protokolu TCP tunelové propojení, které můžete vytvořit síťové připojení mezi vývojovém počítači a webové aplikace pro kontejnery přes ověřené připojení protokolu WebSocket. Umožňuje otevřít relaci SSH kontejner ve službě App Service spuštěn z klienta podle vašeho výběru.

Abyste mohli začít, musíte nainstalovat [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Chcete-li zjistit, jak to funguje bez instalace rozhraní příkazového řádku Azure, otevřete [prostředí cloudu Azure](../../cloud-shell/overview.md). 

Přidejte nejnovější rozšíření služby App Service spuštěním [přidat rozšíření az](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add -–name webapp
```

Pokud jste již spustili `az extension add` před, spusťte [aktualizace rozšíření az](/cli/azure/extension?view=azure-cli-latest#az-extension-update) místo:

```azurecli-interactive
az extension update -–name webapp
```

Otevřete připojení ke vzdálené aplikace pomocí [vytvořit připojení vzdáleného webapp az](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) příkaz. Zadejte  _\<skupiny\_název >_ a \_< aplikace\_name > _ pro vaše aplikace a nahradit \<port > s číslem místního portu.

```azurecli-interactive
az webapp remote-connection create --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> `&` na konci příkazu platí jenom pro usnadnění práce, pokud používáte cloudové prostředí. Spustí proces na pozadí, takže další příkaz můžete spustit ve stejném prostředí.

Výstup příkazu získáte informace, které je třeba otevřít relaci SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Otevřete relaci SSH kontejner s klienta podle svého výběru pomocí místního portu. Následující příklad používá výchozí [ssh](https://ss64.com/bash/ssh.html) příkaz:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Jakmile se zobrazí výzva, zadejte `yes` pokračujte připojení. Zobrazí se výzva k zadání hesla. Použití `Docker!`, který byl dříve zobrazí vám.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Jakmile jste se ověřit, měli byste vidět na úvodní obrazovce relace.

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

Nyní jste připojeni k vaší konektor. 

Pokuste se o spuštění [horní](https://ss64.com/bash/top.html) příkaz. Nyní byste měli mít najdete v části procesu vaší aplikace v seznamu proces. Příklad níže ve výstupu, je to se `PID 263`.

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

Otázky a aspekty můžete zveřejnit na [fórum Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Další informace o webové aplikace pro kontejnery najdete v části:

* [Představení vzdálené ladění aplikací Node.js v Azure App Service z kódu VS](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Jak používat vlastní image Dockeru pro službu Web App for Containers](quickstart-docker-go.md)
* [Použití .NET Core ve službě Azure App Service v Linuxu](quickstart-dotnetcore.md)
* [Použití Ruby ve službě Azure App Service v Linuxu](quickstart-ruby.md)
* [Azure App Service Web App for Containers – Nejčastější dotazy](app-service-linux-faq.md)