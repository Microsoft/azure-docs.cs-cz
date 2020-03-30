---
title: Nejčastější dotazy
description: Odpovědi na nejčastější dotazy týkající se služby Azure Container Registry
author: sajayantony
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: c0d51c9c31e4e6859eaedce371efeafaa5fd4f46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403224"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Nejčastější dotazy týkající se registru kontejnerů Azure

Tento článek řeší nejčastější dotazy a známé problémy týkající se registru kontejnerů Azure.

## <a name="resource-management"></a>Správa prostředků

- [Můžu vytvořit registr kontejnerů Azure pomocí šablony Správce prostředků?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Existuje chyba zabezpečení prohledávání obrázků v ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Jak nakonfiguruji Kubernetes pomocí Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Jak získám pověření správce pro registr kontejnerů?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Jak získám přihlašovací údaje správce v šabloně Správce prostředků?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Odstranění replikace se nezdaří se zakázaným stavem, i když se replikace odstraní pomocí azure cli nebo Azure PowerShellu](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Pravidla brány firewall jsou úspěšně aktualizována, ale neprojeví se.](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Můžu vytvořit registr kontejnerů Azure pomocí šablony Správce prostředků?

Ano. Zde je [šablona,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) kterou můžete použít k vytvoření registru.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Existuje chyba zabezpečení prohledávání obrázků v ACR?

Ano. Podívejte se na dokumentaci z [Azure Security Center](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration), [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) a [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Jak nakonfiguruji Kubernetes pomocí Azure Container Registry?

Podívejte se na dokumentaci [k Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) a kroky pro [službu Azure Kubernetes Service](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Jak získám pověření správce pro registr kontejnerů?

> [!IMPORTANT]
> Uživatelský účet správce je určen pro jednoho uživatele pro přístup k registru, hlavně pro účely testování. Nedoporučujeme sdílet přihlašovací údaje účtu správce s více uživateli. Individuální identita je doporučena pro uživatele a instanční objekty pro bezhlavé scénáře. Viz [Přehled ověřování](container-registry-authentication.md).

Než získáte pověření správce, ujistěte se, že je povolen uživatel správce registru.

Získání přihlašovacích údajů pomocí příkazového příkazového příkazu Azure:

```azurecli
az acr credential show -n myRegistry
```

Použití Azure Powershellu:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Jak získám přihlašovací údaje správce v šabloně Správce prostředků?

> [!IMPORTANT]
> Uživatelský účet správce je určen pro jednoho uživatele pro přístup k registru, hlavně pro účely testování. Nedoporučujeme sdílet přihlašovací údaje účtu správce s více uživateli. Individuální identita je doporučena pro uživatele a instanční objekty pro bezhlavé scénáře. Viz [Přehled ověřování](container-registry-authentication.md).

Než získáte pověření správce, ujistěte se, že je povolen uživatel správce registru.

Chcete-li získat první heslo:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Chcete-li získat druhé heslo:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Odstranění replikace se nezdaří se zakázaným stavem, i když se replikace odstraní pomocí azure cli nebo Azure PowerShellu

Chyba se zobrazí, když má uživatel oprávnění k registru, ale nemá oprávnění na úrovni čtečky k předplatnému. Chcete-li tento problém vyřešit, přiřaďte uživateli oprávnění čtečky k předplatnému:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Pravidla brány firewall jsou úspěšně aktualizována, ale neprojeví se.

Šíření změn pravidel brány firewall nějakou dobu trvá. Po změně nastavení brány firewall počkejte několik minut, než tuto změnu ověříte.


## <a name="registry-operations"></a>Operace registru

- [Jak se dostanu k rozhraní HTTP API registru Dockeru V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Jak odstraním všechny manifesty, na které v úložišti neodkazuje žádná značka?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Proč se po odstranění bitových kopií nesnižuje využití kvótregistru?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Jak ověřím změny kvóty úložiště?](#how-do-i-validate-storage-quota-changes)
- [Jak lze ověřit pomocí registru při spuštění velmožového nastavení řízení v kontejneru?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Jak povolit TLS 1.2?](#how-to-enable-tls-12)
- [Podporuje Azure Container Registry důvěryhodnost obsahu?](#does-azure-container-registry-support-content-trust)
- [Jak udělím přístup k vyžádat nebo nabízená bitová kopie bez oprávnění ke správě prostředku registru?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Jak povolím automatickou karanténu bitových obrázků pro registr](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Jak se dostanu k rozhraní HTTP API registru Dockeru V2?

ACR podporuje rozhraní HTTP API registru Dockeru V2. K těmto možnostem lze `https://<your registry login server>/v2/`přistupovat na adrese . Příklad: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Jak odstraním všechny manifesty, na které v úložišti neodkazuje žádná značka?

Pokud jste na bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Pro prostředí Powershell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Poznámka: Do `-y` příkazu odstranit můžete přidat příkaz k přeskočení potvrzení.

Další informace najdete [v tématu Odstranění ibi kontejnerů v Registru kontejnerů Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Proč se po odstranění bitových kopií nesnižuje využití kvótregistru?

K této situaci může dojít, pokud jsou základní vrstvy stále odkazovány jinými image kontejneru. Pokud odstraníte bitovou kopii bez odkazů, využití registru se aktualizuje během několika minut.

### <a name="how-do-i-validate-storage-quota-changes"></a>Jak ověřím změny kvóty úložiště?

Vytvořte obraz s vrstvou 1 GB pomocí následujícího souboru dockeru. Tím je zajištěno, že obraz má vrstvu, která není sdílena žádnou jinou bitovou kopii v registru.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Vytvořte a přesuňte bitovou kopii do registru pomocí cli dockeru.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Měli byste být uvidět, že využití úložiště se zvýšila na webu Azure portal, nebo můžete dotaz ovat využití pomocí příkazového příkazu k obsluze.

```azurecli
az acr show-usage -n myregistry
```

Odstraňte image pomocí azure cli nebo portálu a zkontrolujte aktualizované využití během několika minut.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Jak lze ověřit pomocí registru při spuštění velmožového nastavení řízení v kontejneru?

Je potřeba spustit kontejner rozhraní příkazového příkazu Azure připojením k dockeru soketu:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Do kontejneru `docker`nainstalujte :

```bash
apk --update add docker
```

Poté se ověřte pomocí registru:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Jak povolit TLS 1.2?

Povolte TLS 1.2 pomocí libovolného nedávného klienta dockeru (verze 18.03.0 a vyšší). 

> [!IMPORTANT]
> ledna 2020 bude Azure Container Registry vyžadovat všechna zabezpečená připojení ze serverů a aplikací k použití TLS 1.2. Podpora tls 1.0 a 1.1 bude vyřazena.

### <a name="does-azure-container-registry-support-content-trust"></a>Podporuje Azure Container Registry důvěryhodnost obsahu?

Ano, důvěryhodné image můžete použít v Azure Container Registry, protože [Notář Dockeru](https://docs.docker.com/notary/getting_started/) byl integrovaný a může být povolen. Podrobnosti najdete [v tématu Content Trust v Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Kde je soubor pro kryptografický otisk umístěn?

Podle `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Veřejné klíče a certifikáty všech rolí (kromě rolí `root.json`delegování) jsou uloženy v rozhraní .
* Veřejné klíče a certifikáty role delegování jsou uloženy v souboru `targets.json` JSON jeho nadřazené role (například pro `targets/releases` roli).

Doporučuje se ověřit tyto veřejné klíče a certifikáty po celkovém ověření TUF provedeném klientem Dockeru a Notáře.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Jak udělím přístup k vyžádat nebo nabízená bitová kopie bez oprávnění ke správě prostředku registru?

ACR podporuje [vlastní role,](container-registry-roles.md) které poskytují různé úrovně oprávnění. Konkrétně `AcrPull` a `AcrPush` role umožňují uživatelům vyžádat nebo nabízeny image bez oprávnění ke správě prostředku registru v Azure.

* Portál Azure: Váš registr -> řízení přístupu (IAM) -> Přidat (Vyberte `AcrPull` nebo `AcrPush` pro roli).
* Azure CLI: Najděte ID prostředku registru spuštěním následujícího příkazu:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Potom můžete přiřadit `AcrPull` `AcrPush` roli nebo uživateli (následující `AcrPull`příklad používá ):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Nebo přiřaďte roli k principu služby určenému id aplikace:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Postupník pak může ověřovat a přistupovat k bitovým kopiím v registru.

* Ověření v registru:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Seznam úložišť:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Vytažení obrázku:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Při použití pouze `AcrPull` role `AcrPush` nebo nemá postupník oprávnění ke správě prostředku registru v Azure. Například `az acr list` nebo `az acr show -n myRegistry` nezobrazí registr.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Jak povolím automatickou karanténu obrázků pro registr?

Karanténa obrázků je v současné době funkcí náhledu ACR. Karanténní režim registru můžete povolit tak, aby běžné uživatele byly viditelné pouze obrázky, které úspěšně prošly bezpečnostním skenováním. Podrobnosti naleznete v [úložišti ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics-and-health-checks"></a>Diagnostika a zdravotní kontroly

- [Zkontrolujte stav pomocí`az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull selže s chybou: net/http: požadavek zrušen při čekání na připojení (Client.Timeout byl překročen při čekání na hlavičky)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push úspěšné, ale docker pull selže s chybou: neoprávněné: ověřování povinné](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`úspěšné, ale příkazy dockeru se nezdaří s chybou: neoprávněné: je vyžadováno ověření](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Povolení a získání protokolů ladění demonu dockeru](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Nová uživatelská oprávnění nemusí být účinná ihned po aktualizaci](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Ověřovací informace nejsou uvedeny ve správném formátu při přímých voláních rozhraní REST API.](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Proč na webu Azure Portal nejsou uvedeny všechny moje úložiště nebo značky?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Proč se na webu Azure nedaří načíst úložiště nebo značky?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Proč se nezdaří můj požadavek na vyžádat nebo push snese s nepovolenou operací?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Jak lze shromažďovat trasování http v systému Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Zkontrolujte stav pomocí`az acr check-health`

Informace o řešení běžných problémů s prostředím a registrem najdete [v tématu Kontrola stavu registru kontejnerů Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull selže s chybou: net/http: požadavek zrušen při čekání na připojení (Client.Timeout byl překročen při čekání na hlavičky)

 - Pokud se jedná o přechodný problém, bude opakování úspěšné.
 - Pokud `docker pull` se nezdaří nepřetržitě, pak může být problém s daemon Docker. Problém lze obecně zmírnit restartováním daemon dockeru. 
 - Pokud budete i nadále vidět tento problém po restartování daemon dockeru, pak problém může být některé problémy s připojením k síti s počítačem. Chcete-li zkontrolovat, zda je obecná síť v počítači v pořádku, otestujte připojení koncového bodu následujícím příkazem. Minimální `az acr` verze, která obsahuje tento příkaz kontroly připojení, je 2.2.9. Upgradujte své azure cli, pokud používáte starší verzi.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Vždy byste měli mít mechanismus opakování na všech operacích klienta Dockeru.

### <a name="docker-pull-is-slow"></a>Docker pull je pomalý
Tento [this](http://www.azurespeed.com/Azure/Download) nástroj slouží k testování rychlosti stahování v síti zařízení. Pokud je počítačová síť pomalá, zvažte použití virtuálního počítače Azure ve stejné oblasti jako váš registr. To obvykle dává vyšší rychlost sítě.

### <a name="docker-push-is-slow"></a>Docker push je pomalý
Pomocí [tohoto](http://www.azurespeed.com/Azure/Upload) nástroje můžete otestovat rychlost nahrávání v síti počítače. Pokud je počítačová síť pomalá, zvažte použití virtuálního počítače Azure ve stejné oblasti jako váš registr. To obvykle dává vyšší rychlost sítě.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Nabízení dockeru úspěšné, ale docker pull se nezdaří s chybou: neoprávněné: ověřování povinné

K této chybě může dojít s verzí Daemon `--signature-verification` dockeru Red Hat, kde je ve výchozím nastavení povolena. Možnosti daemonu Dockeru pro Red Hat Enterprise Linux (RHEL) nebo Fedoru můžete zkontrolovat následujícím příkazem:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Například Fedora 28 Server má následující možnosti docker daemon:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

S `--signature-verification=false` chybějící, `docker pull` selže s chybou podobnou:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Chcete-li chybu vyřešit:
1. Přidejte `--signature-verification=false` možnost do konfiguračního `/etc/sysconfig/docker`souboru daemonu Dockeru . Například:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Restartujte službu daemon dockeru spuštěním následujícího příkazu:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Podrobnosti `--signature-verification` lze nalézt `man dockerd`spuštěním .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr přihlášení úspěšné, ale docker selže s chybou: neoprávněné: ověřování povinné

Ujistěte se, že používáte adresu URL `docker push myregistry.azurecr.io/myimage:latest`serveru s velkými písmeny, například , `myRegistry`a to i v případě, že název prostředku registru je velká nebo smíšená písmena, například .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Povolení a získání protokolů ladění daemonu Dockeru  

Začněte `dockerd` `debug` s možností. Nejprve vytvořte konfigurační soubor`/etc/docker/daemon.json`daemonu Dockeru ( `debug` ) pokud neexistuje, a přidejte možnost:

```json
{   
    "debug": true   
}
```

Potom restartujte daemon. Například s Ubuntu 14.04:

```bash
sudo service docker restart
```

Podrobnosti naleznete v [dokumentaci dockeru](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Protokoly mohou být generovány na různých místech, v závislosti na vašem systému. Například pro Ubuntu 14.04 je `/var/log/upstart/docker.log`to .   
Podrobnosti najdete v [dokumentaci k Dockeru.](https://docs.docker.com/engine/admin/#read-the-logs)    

 * Pro Docker pro Windows protokoly jsou generovány pod %LOCALAPPDATA%/docker/. Však nemusí obsahovat všechny informace o ladění dosud.   

   Chcete-li získat přístup k úplnému protokolu daemonu, budete pravděpodobně potřebovat některé další kroky:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Teď máte přístup ke všem souborům `dockerd`spuštěného virtuálního virtuálního serveru . Protokol je `/var/log/docker.log`na .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nová uživatelská oprávnění nemusí být účinná ihned po aktualizaci

Když udělíte nová oprávnění (nové role) instančnímu objektu, změna se nemusí projevit okamžitě. Existují dva možné důvody:

* Zpoždění přiřazení role Služby Azure Active Directory Normálně je to rychlé, ale může to trvat několik minut kvůli zpoždění šíření.
* Zpoždění oprávnění na serveru tokenů ACR. To může trvat až 10 minut. Chcete-li zmírnit, můžete `docker logout` a pak znovu ověřit se stejným uživatelem po 1 minutě:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

V současné době ACR nepodporuje odstranění domácí replikace uživateli. Řešení je zahrnout domácí replikace vytvořit v šabloně, `"condition": false` ale přeskočit jeho vytvoření přidáním, jak je znázorněno níže:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Ověřovací informace nejsou uvedeny ve správném formátu při přímých voláních rozhraní REST API.

Můžete se `InvalidAuthenticationInfo` setkat s `curl` chybou, `-L`zejména `--location` pomocí nástroje s možností , (chcete-li sledovat přesměrování).
Například načítání objektu blob pomocí `curl` možnosti `-L` a základní ověřování:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

může vést k následující odpovědi:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Hlavní příčinou je, `curl` že některé implementace následovat přesměrování s hlavičkami z původního požadavku.

Chcete-li tento problém vyřešit, je třeba postupovat podle přesměrování ručně bez záhlaví. Vytiskněte záhlaví `-D -` odpovědí `curl` s možností a `Location` potom extrahujte: záhlaví:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Proč na webu Azure Portal nejsou uvedeny všechny moje úložiště nebo značky? 

Pokud používáte prohlížeč Microsoft Edge/IE, můžete vidět maximálně 100 úložišť nebo značek. Pokud má váš registr více než 100 úložišť nebo značek, doporučujeme použít prohlížeč Firefox nebo Chrome a uvést je všechny.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Proč se na webu Azure nedaří načíst úložiště nebo značky?

Prohlížeč nemusí být schopen odeslat požadavek na načtení úložišť nebo značek na server. Mohou existovat různé důvody, jako jsou:

* Nedostatečná síťová konektivita
* Brána firewall
* Blokování reklam
* Chyby DNS

Obraťte se na správce sítě nebo zkontrolujte konfiguraci a připojení k síti. Zkuste `az acr check-health -n yourRegistry` spustit pomocí azure cli ke kontrole, pokud vaše prostředí je možné se připojit k registru kontejnerů. Kromě toho můžete také zkusit inkognito nebo soukromou relaci ve vašem prohlížeči, abyste se vyhnuli zatuchlým mezipaměti prohlížeče nebo souborům cookie.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Proč se nezdaří můj požadavek na vyžádat nebo push snese s nepovolenou operací?

Zde jsou některé scénáře, kde operace může být zakázáno:
* Klasické registry již nejsou podporovány. Upgradujte na podporované [skum pomocí](https://aka.ms/acr/skus) [az acr update](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) nebo portálu Azure.
* Obrázek nebo úložiště je možná uzamčeno, takže jej nelze odstranit ani aktualizovat. K zobrazení aktuálních atributů můžete použít příkaz [az acr show repository.](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock)
* Některé operace jsou zakázány, pokud je obrázek v karanténě. Další informace o [karanténě](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Jak lze shromažďovat trasování http v systému Windows?

#### <a name="prerequisites"></a>Požadavky

- Povolit dešifrování https v houslista:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Povolte Dockeru používat proxy server prostřednictvím ui Dockeru:<https://docs.docker.com/docker-for-windows/#proxies>
- Ujistěte se, že se po dokončení vrátíte.  Docker nebude fungovat s tímto povoleno a houslista není spuštěn.

#### <a name="windows-containers"></a>Kontejnery Windows

Konfigurace serveru proxy Dockeru na 127.0.0.1:8888

#### <a name="linux-containers"></a>Linuxové kontejnery

Najděte ip virtuálního přepínače virtuálního virtuálního počítače Dockeru:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurace proxy dockeru pro výstup předchozího příkazu a portu 8888 (například 10.0.75.1:8888)

## <a name="tasks"></a>Úlohy

- [Jak dávkové spuštění storno?](#how-do-i-batch-cancel-runs)
- [Jak mohu zahrnout složku .git do příkazu az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Podporuje úlohy GitLab pro zdrojové aktivační události?](#does-tasks-support-gitlab-for-source-triggers)
- [Jakou službu pro správu git repository úlohy podporují?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Jak dávkové spuštění storno?

Následující příkazy zruší všechny spuštěné úlohy v zadaném registru.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Jak mohu zahrnout složku .git do příkazu az acr build?

Pokud `az acr build` příkazu předáte místní zdrojovou `.git` složku, bude ve výchozím nastavení vyloučena z nahraného balíčku. Můžete vytvořit `.dockerignore` soubor s následujícím nastavením. To říká příkaz obnovit všechny `.git` soubory v rámci v nahraném balíčku. 

`!.git/**`

Toto nastavení platí `az acr run` také pro příkaz.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Podporuje úlohy GitLab pro zdrojové aktivační události?

V současné době nepodporujeme GitLab pro zdrojové spouštěče.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Jakou službu pro správu git repository úlohy podporují?

| Služba Git | Zdrojový kontext | Ruční sestavení | Automatická aktivace sestavení pomocí potvrzení |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Ano | Ano |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Ano | Ano |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Ano | Ne |
| BitBucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Ano | Ne |

## <a name="run-error-message-troubleshooting"></a>Spuštění poradce při potížích s chybovou zprávou

| Chybová zpráva | Průvodce odstraňováním potíží |
|---|---|
|Pro virtuální počítače nebyl nakonfigurován žádný přístup, proto nebyla nalezena žádná předplatná.|K tomu může dojít, pokud používáte `az login --identity` v úkolu ACR. Toto je přechodná chyba a dochází, když přiřazení role spravované identity není rozšířena. Čekání na několik sekund před opakováním funguje.|

## <a name="cicd-integration"></a>Integrace CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Akce GitHubu](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Další kroky

* [Další informace](container-registry-intro.md) o Azure Container Registry.
