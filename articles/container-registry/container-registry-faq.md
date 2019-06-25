---
title: Služba Azure Container Registry – nejčastější dotazy
description: Odpovědi na nejčastější dotazy týkající se ke službě Azure Container Registry
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: beeb4986750e398071e3afb6c1f04663f858cec1
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303575"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Nejčastější dotazy k Azure Container Registry

Tento článek obsahuje nejčastější dotazy a známé problémy, o službě Azure Container Registry.

## <a name="resource-management"></a>Správa prostředků

- [Můžete vytvořit registr kontejnerů Azure pomocí šablony Resource Manageru?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Existuje ohrožení zabezpečení, vyhledávání obrázků v ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Jak nakonfigurovat Kubernetes s Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Jak získat přihlašovací údaje správce pro službu container registry?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Jak získat přihlašovací údaje správce v šabloně Resource Manageru?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Odstranění replikace selže s stav Zakázáno, i když se replikace odstraní pomocí Azure CLI nebo Azure Powershellu](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Můžete vytvořit službu Azure Container Registry pomocí šablony Resource Manageru?

Ano. Tady je [šablonu](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) , můžete použít k vytvoření registru.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Existuje ohrožení zabezpečení, vyhledávání obrázků v ACR?

Ano. V dokumentaci od [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) a [Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Jak nakonfigurovat Kubernetes s Azure Container Registry?

Najdete v dokumentaci k [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) a kroků pro [Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Jak získat přihlašovací údaje správce pro službu container registry?

> [!IMPORTANT]
> Uživatelský účet správce je určená pro jednoho uživatele pro přístup k registru, především pro účely testování. Nedoporučujeme přihlašovací údaje účtu správce pro sdílení obsahu s více uživateli. Jednotlivé identity se doporučuje pro uživatele a instančních objektů pro bezobslužné scénáře. Zobrazit [Přehled ověřování](container-registry-authentication.md).

Před získáním přihlašovacích údajů správce, ujistěte se, že uživatel s rolí správce v registru je povolená.

K získání přihlašovacích údajů pomocí rozhraní příkazového řádku Azure:

```azurecli
az acr credential show -n myRegistry
```

Použití Azure Powershellu:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Jak získat přihlašovací údaje správce v šabloně Resource Manageru?

> [!IMPORTANT]
> Uživatelský účet správce je určená pro jednoho uživatele pro přístup k registru, především pro účely testování. Nedoporučujeme přihlašovací údaje účtu správce pro sdílení obsahu s více uživateli. Jednotlivé identity se doporučuje pro uživatele a instančních objektů pro bezobslužné scénáře. Zobrazit [Přehled ověřování](container-registry-authentication.md).

Před získáním přihlašovacích údajů správce, ujistěte se, že uživatel s rolí správce v registru je povolená.

Pokud chcete získat první heslo:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Pokud chcete získat druhý heslo:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Odstranění replikace selže s stav Zakázáno, i když se replikace odstraní pomocí Azure CLI nebo Azure Powershellu

Chyba se zobrazí, pokud uživatel nemá oprávnění na registru, ale nemá oprávnění na úrovni čtenář pro předplatné. Chcete-li vyřešit tento problém, můžete uživateli přiřadíte oprávnění Čtenář pro předplatné:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>Operace registru

- [Jak získám přístup do Docker Registry HTTP rozhraní API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Jak odstranit všechny manifesty, které nemají žádné značky v úložišti?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Proč využití kvóty registru neodečítá po odstranění Image?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Jak ověřit změny kvót úložiště?](#how-do-i-validate-storage-quota-changes)
- [Jak můžu ověřovat pomocí registru při spuštění rozhraní příkazového řádku v kontejneru?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Nabízí Azure Container Registry pouze konfigurace TLS verze 1.2 a povolení protokolu TLS verze 1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure Container Registry podporuje obsahu důvěřovat?](#does-azure-container-registry-support-content-trust)
- [Jak mohu udělit přístup k imagím o přijetí změn nebo push bez oprávnění ke správě prostředků registru?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Jak povolím karantény automatické image registru](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Jak získám přístup do Docker Registry HTTP rozhraní API V2?

ACR podporuje Docker Registry HTTP rozhraní API V2. Rozhraní API můžete získat přístup na adrese `https://<your registry login server>/v2/`. Příklad: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Jak odstranit všechny manifesty, které nemají žádné značky v úložišti?

Pokud používáte bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Pro prostředí Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Poznámka: Můžete přidat `-y` v příkazu delete pro přeskočení potvrzení.

Další informace najdete v tématu [odstranit Image kontejnerů ve službě Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Proč využití kvóty registru neodečítá po odstranění Image?

Tato situace může nastat, pokud vrstvách jsou stále odkazuje další Image kontejneru. Pokud odstraníte bitovou kopii s žádné odkazy, aktualizuje se využití registru za pár minut.

### <a name="how-do-i-validate-storage-quota-changes"></a>Jak ověřit změny kvót úložiště?

Vytvoření image s 1GB vrstvou pomocí následujícího souboru docker. Tím se zajistí, že image má vrstvu, kterou nebude sdílet jiného obrázku v registru.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Vytvoření a nahrání image do registru pomocí rozhraní příkazového řádku dockeru.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Byste měli vidět, že se zvýší využití úložiště na webu Azure Portal nebo můžete dát dotaz na využití pomocí rozhraní příkazového řádku.

```bash
az acr show-usage -n myregistry
```

Odstranit image pomocí rozhraní příkazového řádku Azure nebo portálu a zkontrolujte, jestli se aktualizace za pár minut.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Jak můžu ověřovat pomocí registru při spuštění rozhraní příkazového řádku v kontejneru?

Potřebujete ke spuštění kontejneru Azure CLI tak, že připojí soketu Dockeru:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

V kontejneru, nainstalujte `docker`:

```bash
apk --update add docker
```

Pak ověření pomocí registru:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Nabízí Azure Container Registry pouze konfigurace TLS verze 1.2 a povolení protokolu TLS verze 1.2?

Ano. Povolení protokolu TLS pomocí libovolného poslední klienta dockeru (verze 18.03.0 a vyšší). 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry podporuje obsahu důvěřovat?

Ano, můžete použít důvěryhodné imagí ve službě Azure Container Registry, protože [Docker notář](https://docs.docker.com/notary/getting_started/) integroval a dá se povolit. Podrobnosti najdete v tématu [důvěřovat obsahu ve službě Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Kde je soubor umístěný kryptografický otisk?

V části `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Veřejné klíče a certifikáty všech rolí (s výjimkou role delegování) jsou uloženy v `root.json`.
* Veřejné klíče a certifikáty delegování role jsou uloženy v souboru JSON její nadřazená role (například `targets.json` pro `targets/releases` role).

Doporučujeme, abyste po dokončení celkové TUF ověření provádí klienta Dockeru a notář ověřit tyto veřejné klíče a certifikáty.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Jak mohu udělit přístup k imagím o přijetí změn nebo push bez oprávnění ke správě prostředků registru?

Podporuje ACR [vlastní role](container-registry-roles.md) , které poskytují různé úrovně oprávnění. Konkrétně `AcrPull` a `AcrPush` rolí umožňují uživatelům pull a push obrázků bez oprávnění ke správě prostředků registru v Azure.

* Azure portal: Váš registr -> řízení přístupu (IAM) -> Přidat (vyberte `AcrPull` nebo `AcrPush` pro roli).
* Azure CLI: Najdete ID prostředku registru spuštěním následujícího příkazu:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Můžete přiřadit `AcrPull` nebo `AcrPush` role pro uživatele (následující příklad používá `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Nebo můžete přiřadit role pro instanční objekt pomocí jeho ID aplikace:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Pověřené osoby je pak moct ověřit a přístup k obrázkům v registru.

* K ověření do registru:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Do seznamu úložišť:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Chcete-li získat obrázek:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

S použitím pouze `AcrPull` nebo `AcrPush` role, pověřené osoby nemá oprávnění ke správě prostředků registru v Azure. Například `az acr list` nebo `az acr show -n myRegistry` registru se nezobrazí.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Jak povolím karantény automatické image registru

Obrázek dát do karantény je aktuálně ve verzi preview funkce služby ACR. Můžete povolit režim karantény registru tak, aby pouze Image, které prošly úspěšně kontroly zabezpečení jsou viditelné pro běžné uživatele. Podrobnosti najdete v tématu [úložiště ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics"></a>Diagnostika

- [operace docker pull se nezdaří s chybou: net/http: žádost byla zrušena při čekání na připojení (Client.Timeout překročil během čekání na hlavičky)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push úspěšné, ale operace docker pull se nezdaří s chybou: neoprávněné: je vyžadováno ověření](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Povolit a získat protokoly ladění z démona dockeru](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Nová uživatelská oprávnění nemusí platit okamžitě po aktualizaci](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Informace o ověřování není uveden ve správném formátu na přímá volání rozhraní REST API](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Proč nemá na webu Azure portal v seznamu Moje úložiště nebo značky?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Jak se shromažďovat trasování protokolu http na Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>operace docker pull se nezdaří s chybou: net/http: žádost byla zrušena při čekání na připojení (Client.Timeout překročil během čekání na hlavičky)

 - Pokud tato chyba je dočasný problém, bude opakování úspěšné.
 - Pokud `docker pull` nepodaří nepřetržitě, mohlo dojít k potížím s démonem dockeru. Tento problém lze obvykle možné zmírnit restartováním démona dockeru. 
 - Pokud problém přetrvává i po restartování démona dockeru, může být problém některé problémy se síťovým připojením k počítači. Pokud chcete zkontrolovat, pokud obecné sítě na počítači je v pořádku, zkuste příkaz `ping www.bing.com`.
 - Vždy byste měli mít mechanismus opakování pro všechny operace klienta dockeru.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker push úspěšné, ale operace docker pull se nezdaří s chybou: neoprávněné: je vyžadováno ověření

K této chybě může dojít v Red Hat verzi démona dockeru, ve kterém `--signature-verification` je ve výchozím nastavení povolené. Možnosti démona dockeru pro Red Hat Enterprise Linux (RHEL) nebo Fedora můžete zkontrolovat spuštěním následujícího příkazu:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Například Fedora 28 Server má následující možnosti démona dockeru:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

S `--signature-verification=false` chybí, `docker pull` selže s chybu podobnou této:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Chcete-li vyřešit chybu:
1. Přidat možnost `--signature-verification=false` do konfiguračního souboru démon dockeru `/etc/sysconfig/docker`. Příklad:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Restartujte službu démona dockeru spuštěním následujícího příkazu:

  ```bash
  sudo systemctl restart docker.service
  ```

Podrobnosti o `--signature-verification` můžete najít spuštěním `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Povolit a získat protokoly ladění z démona dockeru  

Spustit `dockerd` s `debug` možnost. Nejprve vytvořte konfigurační soubor démona dockeru (`/etc/docker/daemon.json`) Pokud je neexistuje a přidat `debug` možnost:

```json
{   
    "debug": true   
}
```

Restartujte démona. Například v Ubuntu 14.04:

```bash
sudo service docker restart
```

Podrobnosti najdete v [dokumentace k Dockeru](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Protokoly mohou být generovány v různých umístěních v závislosti na vašem systému. Například pro Ubuntu 14.04 má `/var/log/upstart/docker.log`.   
Zobrazit [dokumentace k Dockeru](https://docs.docker.com/engine/admin/#read-the-logs) podrobnosti.    

 * Pro Docker pro Windows se ve složce % LOCALAPPDATA%/docker/ generují protokoly. Ale neobsahují všechny informace o ladění ještě.   

   Aby bylo možné získat přístup k úplné démon protokolu, budete pravděpodobně potřebovat několik kroků navíc:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Teď máte přístup ke všem souborům virtuálního počítače se systémem `dockerd`. Protokol je při `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nová uživatelská oprávnění nemusí platit okamžitě po aktualizaci

Pokud nový udělíte oprávnění (nové role) instančnímu objektu služby, změny nemusí se projeví okamžitě. Existují dva možné důvody:

* Zpoždění přiřazení role Azure Active Directory. Obvykle je rychlé, ale může to trvat minuty z důvodu zpoždění šíření.
* Zpoždění oprávnění na serveru služby ACR token. To může trvat až 10 minut. Chcete-li omezit rizika, můžete `docker logout` a pak znovu provést ověření pomocí stejného uživatele po jedné minutě:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR v současné době nepodporuje odstranění replikace domácí uživatelé. Alternativním řešením je zahrnout domácí replikace v šabloně vytvořit ale přeskočit jeho vytvoření přidáním `"condition": false` jak je znázorněno níže:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Informace o ověřování není uveden ve správném formátu na přímá volání rozhraní REST API

Může dojít `InvalidAuthenticationInfo` chyba, zejména pomocí `curl` nástroje s možností `-L`, `--location` (Chcete-li držet se přesměrování).
Například načítání objektů blob pomocí `curl` s `-L` možnost a základní ověřování:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

může být výsledkem následující odpověď:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Hlavní příčinou je, že některé `curl` implementace držet se přesměrování hlavičky z původního požadavku.

Chcete-li vyřešit tento problém, budete muset držet se přesměrování ručně bez záhlaví. Tisk hlavičky odpovědi se `-D -` možnost `curl` a extrahujte: `Location` hlavičky:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Proč nemá na webu Azure portal v seznamu Moje úložiště nebo značky? 

Pokud používáte prohlížeč Microsoft Edge, zobrazí se maximálně 100 úložiště nebo značky, které jsou uvedeny. Pokud váš registr má více než 100 úložiště nebo značky, doporučujeme použít Firefoxu nebo chromu prohlížeče pro všechny.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Jak se shromažďovat trasování protokolu http na Windows?

#### <a name="prerequisites"></a>Požadavky

- Povolte dešifrování https ve fiddleru:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Povolte Dockeru k používání proxy serveru pomocí uživatelského rozhraní Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Je potřeba vrátit zpět, jakmile budete hotovi.  Docker nefunguje s tuto funkci povolíte a fiddleru neběží.

#### <a name="windows-containers"></a>Kontejnery Windows

Konfigurace proxy serveru Docker na 127.0.0.1:8888

#### <a name="linux-containers"></a>Linuxové kontejnery

Najdete ip Dockeru virtuální přepínač virtuálního počítače:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurace proxy serveru Docker do výstupního předchozím příkazem a port 8888 (například 10.0.75.1:8888)

## <a name="tasks"></a>Úlohy

- [Jak se provádí dávkové spuštění zrušit?](#how-do-i-batch-cancel-runs)
- [Jak mohu zahrnout složce .git v příkazu az acr sestavení?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Jak se provádí dávkové spuštění zrušit?

Následující příkazy zrušit všechny spuštěné úlohy do zadaného registru.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Jak mohu zahrnout složce .git v příkazu az acr sestavení?

Pokud předáte pro místní zdrojovou složku `az acr build` příkazu `.git` vyloučit složku z nahraného balíčku ve výchozím nastavení. Můžete vytvořit `.dockerignore` soubor s následujícím nastavením. Říká příkazu Obnovit všechny soubory pod `.git` v nahraném balíčku. 

```
!.git/**
```

Toto nastavení platí také pro `az acr run` příkazu.

## <a name="cicd-integration"></a>Integrace CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Akce Githubu](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="error-references-for-az-acr-check-health"></a>Chyba odkazy pro `az acr check-health`

### <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Tato chyba znamená, že tento klient docker pro rozhraní příkazového řádku se nenašel, který vylučuje hledání verze dockeru, vyhodnocování stavu démona dockeru a zajistit, že je možné spustit příkaz docker pull.

*Možná řešení*: instalace klienta dockeru; přidání cestě dockeru k systémovým proměnným.

### <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Tato chyba znamená, že stav démona dockeru není k dispozici nebo že to není dosažitelná pomocí rozhraní příkazového řádku. To znamená, že operace dockeru (například přihlášení, o přijetí změn), bude k dispozici prostřednictvím rozhraní příkazového řádku.

*Možná řešení*: Restartujte démona dockeru nebo ověřit, zda je správně nainstalován.

### <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Tato chyba znamená, že rozhraní příkazového řádku se nepodařilo spustit příkaz `docker --version`.

*Možná řešení*: Zkuste příkaz spustit ručně, ujistěte se, že máte nejnovější verzi rozhraní příkazového řádku a prozkoumejte chybová zpráva.

### <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Tato chyba znamená, že rozhraní příkazového řádku nebyl schopen stáhnout ukázkový obrázek do vašeho prostředí.

*Možná řešení*: Ověřte, že jsou všechny komponenty potřebné k vyžádání obrázku správně spuštěna.

### <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Tato chyba znamená, že tento příkaz helm klient nebyl nalezen pomocí rozhraní příkazového řádku, který vylučuje jiné operace helm.

*Možná řešení*: Ověřte, že helm instalaci klienta, a jeho cesty je přidaný do proměnných prostředí systému.

### <a name="helmversionerror"></a>HELM_VERSION_ERROR

Tato chyba znamená, že rozhraní příkazového řádku nebyl schopen určit nainstalovanou verzi Helm. K tomu může dojít, pokud verze rozhraní příkazového řádku Azure (nebo, pokud verze helm) používá je zastaralý.

*Možná řešení*: aktualizovat na nejnovější verzi rozhraní příkazového řádku Azure nebo na verzi doporučenou helm; ručně spuštěním příkazu a prozkoumat chybová zpráva.

### <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Tato chyba znamená, že DNS pro daného registru přihlašovací server byl příkaz ping, ale nereagovala na, což znamená, že není k dispozici. To může znamenat některé problémy s připojením. Může také znamenat, že registru neexistuje, že uživatel nemá oprávnění k registru (se správně načíst jeho přihlašovací server) nebo, že cíl registru do jiného cloudu než ten, který používá v Azure CLI.

*Možná řešení*: ověření připojení; zkontrolujte pravopis registru a že registru existuje, ověřte, že uživatel má správná oprávnění na něj a, cloudu v registru je stejný, který se používá na rozhraní příkazového řádku Azure.

### <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

To znamená, že challenge koncový bod pro daného registru odpověděl stavem 403 Zakázáno HTTP. To znamená, že uživatelé nemají přístup k registru, pravděpodobně z důvodu konfigurace virtuální sítě.

*Možná řešení*: odebrání pravidla virtuální sítě a přidání aktuální IP adresu klienta do seznamu povolených aplikací.

### <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Tato chyba znamená, že koncový bod challenge cílového registru nevydala příkaz náročné.

*Možná řešení*: Zkuste za nějakou chvíli znovu. Pokud chyba přetrvává, otevřete prosím am problém na https://aka.ms/acr/issues.

### <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Tato chyba znamená, že koncový bod challenge cílového registru vydány složité, ale registru nepodporuje AAD přihlášení.

*Možná řešení*: Zkuste jiným způsobem protokolování služby, například přihlašovací údaje správce. V případě, že uživatel chce, aby se chcete přihlásit pomocí AAD podporu, otevřete prosím problém am na https://aka.ms/acr/issues.

### <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

To znamená, že přihlašovacího serveru registru neodpověděla token obnovení, což znamená, že byl odepřen přístup do registru cíl. To může dojít, pokud uživatel nemá správná oprávnění registru nebo pokud jsou zastaralé přihlašovací údaje uživatele pro Azure CLI.

*Možná řešení*: Ověřte, jestli má uživatel oprávnění na registru; spuštění `az login` aktualizovat oprávnění, tokenů a přihlašovací údaje.

### <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

To znamená, že přihlašovacího serveru registru neodpověděl s přístupovým tokenem, což znamená, že byl odepřen přístup do registru cíl. To může dojít, pokud uživatel nemá správná oprávnění registru nebo pokud jsou zastaralé přihlašovací údaje uživatele pro Azure CLI.

*Možná řešení*: Ověřte, jestli má uživatel oprávnění na registru; spuštění `az login` aktualizovat oprávnění, tokenů a přihlašovací údaje.

### <a name="loginservererror"></a>LOGIN_SERVER_ERROR

To znamená, že rozhraní příkazového řádku se nepodařilo najít přihlašovací server daného registru a pro aktuálního cloudu nebyl nalezen žádný výchozí příponou. Tomu může dojít, pokud v registru neexistuje, pokud uživatel nemá správná oprávnění k registru, pokud v registru a aktuální cloudem Azure CLI se neshodují, nebo pokud je zastaralá verze rozhraní příkazového řádku Azure.

*Možná řešení*: Ověřte správnost pravopis a zda registru existovat; ověřte, zda má uživatel oprávnění na registru a, odpovídají cloudy registru a prostředí rozhraní příkazového řádku; aktualizace příkazového řádku Azure na nejnovější verzi.

## <a name="next-steps"></a>Další postup

* [Další informace](container-registry-intro.md) o Azure Container Registry.