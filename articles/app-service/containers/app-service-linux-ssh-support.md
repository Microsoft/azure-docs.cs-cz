---
title: Podpora SSH pro Azure App Service v Linuxu | Dokumentace Microsoftu
description: Další informace o používání SSH se službou Azure App Service v Linuxu.
keywords: služby Azure app service, webové aplikace, linux a oss
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
ms.openlocfilehash: 049ae78efee0b5ebdf6bfbe207f8c85d94e9238f
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050064"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Podpora SSH pro Azure App Service v Linuxu

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) se běžně používá ke spuštění příkazů pro správu vzdáleně z příkazového řádku terminálu. App Service v Linuxu podporuje SSH do kontejneru pro aplikace s jednotlivými integrované Image Dockeru pro zásobník modulu Runtime nových webových aplikací. 

![Zásobníky modulu runtime](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Pro vlastní Image Dockeru, tím, že nakonfigurujete SSH server do své vlastní image.

Do tohoto kontejneru můžete také připojit přímo ze svého místního vývojového počítače pomocí SSH a SFTP.

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

Chcete-li připojení klienta SSH pomocí vašeho kontejneru, by měl běžet vaše aplikace.

Vložte následující adresu URL do prohlížeče a nahradit \<app_name > názvem vaší aplikace:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Pokud už nejste ověření, je potřeba ověřit ve vašem předplatném Azure připojit. Po ověření, se zobrazí prostředí v prohlížeči, kde můžete spouštět příkazy uvnitř kontejneru.

![Připojení SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Podpora SSH pomocí vlastní Image Dockeru

Aby vlastní image Dockeru, aby mohly podporovat komunikaci SSH mezi kontejnerem a klientem na webu Azure Portal proveďte následující kroky pro vaši image Dockeru.

Tyto kroky jsou uvedeny v úložišti Azure App Service jako [příklad](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Zahrnout `openssh-server` instalace v [ `RUN` instrukce](https://docs.docker.com/engine/reference/builder/#run) v souboru Dockerfile pro vaše image a nastavte heslo pro kořenový účet `"Docker!"`.

    > [!NOTE]
    > Tato konfigurace nepovoluje externí připojení ke kontejneru. SSH lze přistupovat pouze prostřednictvím Kudu / SCM webu, což je ověřený pomocí přihlašovacích údajů pro publikování.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Přidat [ `COPY` instrukce](https://docs.docker.com/engine/reference/builder/#copy) k souboru Dockerfile ke kopírování [sshd_config](http://man.openbsd.org/sshd_config) do souboru */etc/ssh/* adresáře. Konfigurační soubor by měl vycházet souboru sshd_config v úložišti GitHub Azure App Service [tady](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > *Sshd_config* soubor musí obsahovat následující jinak připojení selže: 
    > * `Ciphers` musí obsahovat alespoň jeden z následujících akcí: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` musí obsahovat alespoň jeden z následujících akcí: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Zahrnovat port 2222 [ `EXPOSE` instrukce](https://docs.docker.com/engine/reference/builder/#expose) pro soubor Dockerfile. I když je známé kořenové heslo, port 2222 není přístupný z internetu. Jde interní pouze port přístupný pouze kontejnery v rámci síťového mostu privátní virtuální síť.

    ```docker
    EXPOSE 2222 80
    ```

1. Ujistěte se, že chcete spustit službu SSH pomocí skriptu prostředí (podívejte se na příklad [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Soubor Dockerfile použije [ `ENTRYPOINT` instrukce](https://docs.docker.com/engine/reference/builder/#entrypoint) pro spuštění skriptu.

    ```docker
    COPY init_container.sh /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Otevřete relaci SSH ze vzdáleného prostředí

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview.
>

Pomocí protokolu TCP tunelové propojení, které můžete vytvořit síťové připojení mezi počítačem pro vývoj a Web App for Containers přes ověřené připojení soketu WebSocket. Umožňuje otevřít relaci SSH s vašeho kontejneru ve službě App Service z klienta podle vašeho výběru.

Abyste mohli začít, je potřeba nainstalovat [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Chcete-li zjistit, jak to funguje, bez instalace rozhraní příkazového řádku Azure, otevřete [Azure Cloud Shell](../../cloud-shell/overview.md). 

Přidejte nejnovější rozšíření služby App Service spuštěním [přidat rozšíření az](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add --name webapp
```

Pokud jste již spustili `az extension add` , před spuštěním [aktualizace rozšíření az](/cli/azure/extension?view=azure-cli-latest#az-extension-update) místo:

```azurecli-interactive
az extension update --name webapp
```

Vytvořit vzdálené připojení k aplikaci pomocí [az webapp-připojení ke vzdálené vytvoření](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) příkazu. Zadejte  _\<předplatné\_id >_,  _\<skupiny\_název >_ a \_< aplikace\_name > _ pro vaši aplikaci a nahraďte \<port > se číslo místního portu.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription_id> --resource-group <group_name> -n <app_name> -p <port> &
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
