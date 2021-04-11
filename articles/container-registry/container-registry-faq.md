---
title: Nejčastější dotazy
description: Odpovědi na nejčastější dotazy týkající se služby Azure Container Registry
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: 5550c53289228f154fab485b4b7bbff17555aad7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045735"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Nejčastější dotazy týkající se Azure Container Registry

Tento článek popisuje Nejčastější dotazy a známé problémy týkající se Azure Container Registry.

Pokyny k odstraňování potíží registru najdete v těchto tématech:
* [Řešení potíží s přihlášením k registru](container-registry-troubleshoot-login.md)
* [Řešení potíží se sítí pomocí registru](container-registry-troubleshoot-access.md)
* [Řešení potíží s výkonem registru](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Správa prostředků

- [Můžu pomocí šablony Správce prostředků vytvořit službu Azure Container Registry?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Kontroluje se u obrázků v ACR chyba zabezpečení?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Návody nakonfigurovat Kubernetes pomocí Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Návody získat přihlašovací údaje správce pro registr kontejneru?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Návody získat přihlašovací údaje správce v šabloně Správce prostředků?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Odstranění replikace se nezdařilo, stav zakázáno, i když se replikace odstraní pomocí Azure CLI nebo Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Pravidla brány firewall se úspěšně aktualizují, ale neprojeví se.](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Můžu vytvořit Azure Container Registry pomocí šablony Správce prostředků?

Ano. Tady je [Šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) , kterou můžete použít k vytvoření registru.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Kontroluje se u obrázků v ACR chyba zabezpečení?

Ano. Přečtěte si dokumentaci od [Azure Security Center](../security-center/defender-for-container-registries-introduction.md), [TwistLock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) a [azurová](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Návody nakonfigurovat Kubernetes pomocí Azure Container Registry?

Přečtěte si dokumentaci k [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) a krokům pro [službu Azure Kubernetes](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Návody získat přihlašovací údaje správce pro registr kontejneru?

> [!IMPORTANT]
> Uživatelský účet správce je navržený pro jednoho uživatele, který má přístup k registru, hlavně pro účely testování. Nedoporučujeme sdílení přihlašovacích údajů účtu správce s více uživateli. Pro uživatele a instanční objekty se doporučuje použít pro scénáře s doplňováním provozu individuální identitu. Viz [Přehled ověřování](container-registry-authentication.md).

Před získáním přihlašovacích údajů správce se ujistěte, že je povolený uživatel s oprávněními správce registru.

Získání přihlašovacích údajů pomocí Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Pomocí Azure PowerShellu:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Návody získat přihlašovací údaje správce v šabloně Správce prostředků?

> [!IMPORTANT]
> Uživatelský účet správce je navržený pro jednoho uživatele, který má přístup k registru, hlavně pro účely testování. Nedoporučujeme sdílení přihlašovacích údajů účtu správce s více uživateli. Pro uživatele a instanční objekty se doporučuje použít pro scénáře s doplňováním provozu individuální identitu. Viz [Přehled ověřování](container-registry-authentication.md).

Před získáním přihlašovacích údajů správce se ujistěte, že je povolený uživatel s oprávněními správce registru.

První heslo získáte takto:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Druhé heslo získáte takto:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Odstranění replikace se nezdařilo, stav zakázáno, i když se replikace odstraní pomocí Azure CLI nebo Azure PowerShell

Tato chyba se zobrazí, když má uživatel oprávnění k registru, ale nemá oprávnění na úrovni čtenáře k tomuto předplatnému. Chcete-li tento problém vyřešit, přiřaďte uživateli oprávnění ke čtení z předplatného:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Pravidla brány firewall se úspěšně aktualizují, ale neprojeví se.

Rozšiřování změn pravidel brány firewall trvá nějakou dobu. Po změně nastavení brány firewall prosím počkejte několik minut, než ověříte tuto změnu.


## <a name="registry-operations"></a>Operace registru

- [Návody přístup k HTTP API v2 registru Docker?](#how-do-i-access-docker-registry-http-api-v2)
- [Návody odstranit všechny manifesty, na které není odkazováno pomocí žádné značky v úložišti?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Proč se po odstranění imagí nesnižuje využití kvóty registru?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Návody ověřit změny kvóty úložiště?](#how-do-i-validate-storage-quota-changes)
- [Návody ověřování pomocí registru při spuštění CLI v kontejneru?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Jak povolit TLS 1,2?](#how-to-enable-tls-12)
- [Podporuje Azure Container Registry důvěryhodnost obsahu?](#does-azure-container-registry-support-content-trust)
- [Návody udělit přístup k vyžádanému nebo nabízenému obrázku bez oprávnění ke správě prostředku registru?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Návody povolit automatické karantény imagí pro registr?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Návody povolit anonymní přístup pro vyžádání obsahu?](#how-do-i-enable-anonymous-pull-access)
- [Návody do registru vložit nedistribuovatelné vrstvy?](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Návody přístup k HTTP API v2 registru Docker?

ACR podporuje HTTP API v2 v registru Docker. Rozhraní API jsou k dispozici na adrese `https://<your registry login server>/v2/` . Příklad: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Návody odstranit všechny manifesty, na které není odkazováno pomocí žádné značky v úložišti?

Pokud jste na bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Pro PowerShell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Poznámka: `-y` k přeskočení potvrzení můžete přidat v příkazu DELETE.

Další informace najdete v tématu [odstranění imagí kontejneru v Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Proč se po odstranění imagí nesnižuje využití kvóty registru?

K této situaci může dojít, pokud jsou na podkladové vrstvy pořád odkazovány jinými imagemi kontejneru. Pokud odstraníte image bez odkazů, použití registru se během několika minut aktualizuje.

### <a name="how-do-i-validate-storage-quota-changes"></a>Návody ověřit změny kvóty úložiště?

Pomocí následujícího souboru Docker vytvořte image s vrstvou 1 GB. Tím se zajistí, že bitová kopie bude mít vrstvu, která není sdílená žádným jiným obrázkem v registru.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Sestavte a nahrajte image do svého registru pomocí Docker CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Měli byste mít možnost vidět, že využití úložiště se v Azure Portal zvýšilo, nebo můžete využít dotaz na použití rozhraní příkazového řádku.

```azurecli
az acr show-usage -n myregistry
```

Odstraňte Image pomocí Azure CLI nebo portálu a za pár minut si Projděte aktualizované využití.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Návody ověřování pomocí registru při spuštění CLI v kontejneru?

Musíte spustit kontejner Azure CLI připojením k soketu Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

V kontejneru nainstalujte `docker` :

```bash
apk --update add docker
```

Pak proveďte ověření v registru:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Jak povolit TLS 1,2?

Povolte TLS 1,2 pomocí libovolného nedávného klienta Docker (verze 18.03.0 a vyšší). 

> [!IMPORTANT]
> Od 13. ledna 2020 bude Azure Container Registry vyžadovat, aby všechna zabezpečená připojení ze serverů a aplikací používala protokol TLS 1,2. Bude vyřazena podpora TLS 1,0 a 1,1.

### <a name="does-azure-container-registry-support-content-trust"></a>Podporuje Azure Container Registry důvěryhodnost obsahu?

Ano, v Azure Container Registry můžete použít důvěryhodné image, protože je [Docker notář](https://docs.docker.com/notary/getting_started/) integrovaný a dá se povolit. Podrobnosti najdete v tématu [vztah důvěryhodnosti obsahu v Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Kde se nachází soubor pro kryptografický otisk?

V části `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` :

* Veřejné klíče a certifikáty všech rolí (s výjimkou rolí delegování) jsou uloženy v `root.json` .
* Veřejné klíče a certifikáty role delegování jsou uloženy v souboru JSON své nadřazené role (například `targets.json` pro `targets/releases` roli).

Doporučuje se tyto veřejné klíče a certifikáty ověřit po celkovém ověření TUF, které provádí Docker a notář klient.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Návody udělit přístup k vyžádanému nebo nabízenému obrázku bez oprávnění ke správě prostředku registru?

ACR podporuje [vlastní role](container-registry-roles.md) , které poskytují různé úrovně oprávnění. Konkrétně `AcrPull` role a `AcrPush` umožňují uživatelům načítat a vkládat image bez oprávnění ke správě prostředku registru v Azure.

* Azure Portal: registr-> Access Control (IAM) – > přidejte (vyberte `AcrPull` nebo `AcrPush` pro roli).
* Azure CLI: vyhledejte ID prostředku v registru spuštěním následujícího příkazu:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Pak můžete přiřadit `AcrPull` `AcrPush` roli nebo uživateli (Následující příklad používá `AcrPull` ):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Nebo přiřaďte roli k instančnímu objektu identifikovanému jeho ID aplikace:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Nabyvatel pak může ověřit image v registru a získat k nim přístup.

* Ověření v registru:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* K vypsání úložišť:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Postup načtení obrázku:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

V případě použití jenom `AcrPull` `AcrPush` role nebo nemá nabyvatel oprávnění spravovat prostředek registru v Azure. Například, `az acr list` nebo `az acr show -n myRegistry` nezobrazuje registr.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Návody povolit automatické karantény imagí pro registr?

Karanténa obrázku je teď funkcí Preview ACR. Můžete povolit karanténní režim registru, aby byly normálním uživatelům viditelné pouze ty image, které úspěšně prošly kontrolou zabezpečení. Podrobnosti najdete v [úložišti GitHub ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Návody povolit anonymní přístup pro vyžádání obsahu?

Nastavení služby Azure Container registry pro anonymní (neověřené) oprávnění k získání přístupu je aktuálně funkce ve verzi Preview, která je dostupná ve [vrstvách](container-registry-skus.md)Standard a Premium. 

Pokud chcete povolit anonymní přístup pro vyžádání obsahu, aktualizujte registr pomocí Azure CLI (verze 2.21.0 nebo novější) a předejte `--anonymous-pull-enabled` parametr do příkazu [AZ ACR Update](/cli/azure/acr#az_acr_update) :

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

Anonymní přístup k vyžádanému přístupu můžete kdykoli zakázat nastavením `--anonymous-pull-enabled` na `false` .

> [!NOTE]
> * Před pokusem o anonymní operaci vyžádání obsahu se `docker logout` ujistěte, že jste vymazali všechna existující pověření Docker.
> * Pro neověřené klienty jsou k dispozici pouze operace roviny dat.
> * Registr může omezit vysokou míru neověřených požadavků.

> [!WARNING]
> Anonymní přístup k vyžádanému přístupu se teď vztahuje na všechna úložiště v registru. Pokud spravujete přístup k úložišti pomocí [tokenů s rozsahem úložiště](container-registry-repository-scoped-permissions.md), uvědomte si, že všichni uživatelé můžou získat z těchto úložišť v registru, který je povolený pro anonymní vyžádání. Pokud je povolen anonymní přístup k přístupu, doporučujeme odstranit tokeny.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Návody do registru vložit nedistribuovatelné vrstvy?

Nedistribuovatelný vrstva v manifestu obsahuje parametr adresy URL, ze kterého se dá obsah načíst. Některé možné případy použití pro povolení nedistribuovatelných nabízených vrstev jsou pro Registry s omezenou sítí, gapped Registry s omezeným přístupem nebo pro Registry bez připojení k Internetu.

Pokud máte třeba pravidla NSG nastavená tak, aby virtuální počítač mohl vyžádat image jenom z Azure Container Registry, Docker bude načítat chyby pro cizí a nedistribuovatelné vrstvy. Například bitová kopie Windows serveru by obsahovala v manifestu službu Azure Container Registry odkazy na cizí vrstvy a v tomto scénáři by se v tomto scénáři nepodaří načíst.

Chcete-li povolit vkládání nedistribuovatelných vrstev:

1. Upravte `daemon.json` soubor, který se nachází `/etc/docker/` na hostitelích se systémem Linux a v `C:\ProgramData\docker\config\daemon.json` systému Windows Server. Za předpokladu, že soubor byl dřív prázdný, přidejte následující obsah:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > Hodnota je pole adres registru oddělené čárkami.

2. Soubor uložte a zavřete.

3. Restartujte Docker.

Při nahrávání obrázků do registrů v seznamu se jejich nedistribuovatelné vrstvy vloží do registru.

> [!WARNING]
> Nedistribuovatelný artefakty mají obvykle omezení, jak a kde je lze distribuovat a sdílet. Tuto funkci lze použít pouze pro vložení artefaktů do privátních registrů. Ujistěte se, že dodržujete podmínky, které se týkají opětovné distribuce nedistribuovatelných artefaktů.

## <a name="diagnostics-and-health-checks"></a>Diagnostika a kontroly stavu

- [Ověřit stav pomocí `az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull se nepovedlo s chybou: NET/http: žádost se zrušila během čekání na připojení (Client. Timeout se překročila při očekávaných hlavičkách).](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [nabízení Docker Push je úspěšné, ale docker pull se nezdaří s chybou: Neautorizováno: vyžadováno ověřování](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` je úspěšné, ale příkazy Docker selžou s chybou: Neautorizováno: vyžaduje se ověření.](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Povolit a získat protokoly ladění démona Docker](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Nová oprávnění uživatele nemůžou být účinná hned po aktualizaci.](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Informace o ověřování nejsou uvedené ve správném formátu při přímém REST API volání.](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Proč Azure Portal neobsahují seznam všech úložišť nebo značek?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Proč Azure Portal nepodaří načíst úložiště nebo značky?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Proč dojde k selhání žádosti o přijetí změn nebo nabízení oznámení s nepovolenou operací?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Formát úložiště je neplatný nebo nepodporovaný.](#repository-format-is-invalid-or-unsupported)
- [Návody shromažďovat trasování http ve Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Ověřit stav pomocí `az acr check-health`

Pokud chcete řešit běžné problémy s prostředím a registrací, přečtěte si téma o [kontrole stavu služby Azure Container Registry](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull se nepovedlo s chybou: NET/http: žádost se zrušila během čekání na připojení (Client. Timeout se překročila při očekávaných hlavičkách).

 - Pokud je tato chyba přechodným problémem, pak to bude úspěšné.
 - Pokud `docker pull` dojde k chybě nepřetržitě, může se jednat o problém s démonem Docker. Problém se může obecně zmírnit restartováním démona Docker. 
 - Pokud se tento problém bude i nadále zobrazovat po restartování procesu Docker, může dojít k potížím s připojením k síti v počítači. Pokud chcete zkontrolovat, jestli je obecná síť v počítači v pořádku, spusťte následující příkaz, který otestuje připojení ke koncovému bodu. Minimální `az acr` verze, která obsahuje tento příkaz pro kontrolu připojení, je 2.2.9. Pokud používáte starší verzi, upgradujte rozhraní příkazového řádku Azure.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Vždy byste měli mít mechanismus opakování u všech operací klienta Docker.

### <a name="docker-pull-is-slow"></a>Vyžádané čtení Docker je pomalé.
[Tento](http://www.azurespeed.com/Azure/Download) nástroj slouží k otestování rychlosti stahování ze sítě počítače. Pokud je síť počítačů pomalá, zvažte použití virtuálního počítače Azure ve stejné oblasti jako registr. To obvykle zajišťuje rychlejší rychlost sítě.

### <a name="docker-push-is-slow"></a>Nabízený zápis Docker je pomalý
[Tento](http://www.azurespeed.com/Azure/Upload) nástroj slouží k otestování rychlosti nahrávání počítače v síti. Pokud je síť počítačů pomalá, zvažte použití virtuálního počítače Azure ve stejné oblasti jako registr. To obvykle zajišťuje rychlejší rychlost sítě.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Nabízení Docker Push je úspěšné, ale docker pull se nezdaří s chybou: Neautorizováno: vyžadováno ověřování

K této chybě může dojít ve verzi Red Hat démona Docker, kde `--signature-verification` je ve výchozím nastavení povolená. Možnosti démona Docker pro Red Hat Enterprise Linux (RHEL) nebo Fedora můžete ověřit spuštěním tohoto příkazu:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Například Fedora 28 Server má následující možnosti Docker démon:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Pokud `--signature-verification=false` chybí, `docker pull` dojde k chybě s podobnou chybou:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Řešení této chyby:
1. Přidejte možnost `--signature-verification=false` do konfiguračního souboru démona Docker `/etc/sysconfig/docker` . Například:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Restartujte službu Docker démon spuštěním následujícího příkazu:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Podrobnosti o `--signature-verification` lze nalézt spuštěním `man dockerd` .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>AZ ACR Login se zdaří, ale Docker selže s chybou: Neautorizováno: vyžaduje se ověření.

Ujistěte se, že používáte adresu URL serveru s malými písmeny, například `docker push myregistry.azurecr.io/myimage:latest` , a to i v případě, že je název prostředku registru velká nebo smíšená velká písmena, například `myRegistry` .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Povolit a získat protokoly ladění démona Docker    

Začněte `dockerd` s `debug` možností. Nejdřív vytvořte konfigurační soubor démona Docker ( `/etc/docker/daemon.json` ), pokud neexistuje, a přidejte `debug` možnost:

```json
{    
    "debug": true    
}
```

Pak restartujte démona. Například s Ubuntu 14,04:

```bash
sudo service docker restart
```

Podrobnosti najdete v [dokumentaci k Docker](https://docs.docker.com/engine/admin/#enable-debugging).    

 * Protokoly se můžou generovat na různých místech v závislosti na vašem systému. Například pro Ubuntu 14,04 je to `/var/log/upstart/docker.log` .    
Podrobnosti najdete v [dokumentaci k Docker](https://docs.docker.com/engine/admin/#read-the-logs) .    

 * V případě Docker for Windows se protokoly generují v% LOCALAPPDATA%/Docker/. Nemusí však obsahovat všechny informace o ladění.    

   Pro přístup k úplnému protokolu démona možná budete potřebovat některé další kroky:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Teď máte přístup ke všem souborům virtuálního počítače, na kterém běží `dockerd` . Protokol je na adrese `/var/log/docker.log` .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nová oprávnění uživatele nemůžou být účinná hned po aktualizaci.

Když udělíte novému instančnímu objektu nová oprávnění (nové role), tato změna se nemusí projevit okamžitě. Existují dva možné důvody:

* Azure Active Directory zpoždění přiřazení role Obvykle je rychlá, ale může trvat několik minut, než se zpoždění šíření.
* Zpoždění oprávnění na serveru tokenu ACR. Může to trvat až 10 minut. Pro zmírnění se můžete `docker logout` znovu ověřit se stejným uživatelem za 1 minutu:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

V současné době ACR nepodporuje odstranění domácích replikací uživateli. Alternativním řešením je, aby se v šabloně vytvořila Domovská replikace, ale přeskočili jsme její vytváření přidáním `"condition": false` , jak je znázorněno níže:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Informace o ověřování nejsou uvedené ve správném formátu při přímém REST API volání.

Může dojít k `InvalidAuthenticationInfo` chybě, zejména pomocí `curl` nástroje s možností `-L` `--location` (pro následné přesměrování).
Například načtení objektu BLOB pomocí `curl` `-L` Možnosti with a základního ověřování:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

může vést k následující reakci:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Hlavní příčinou je, že některé `curl` implementace následují po přesměrování se záhlavími původní žádosti.

Chcete-li tento problém vyřešit, je nutné provést přesměrování ručně bez hlaviček. Vytiskněte hlavičky odpovědi s `-D -` možností `curl` a potom rozbalte: `Location` Hlavička:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Proč Azure Portal neobsahují seznam všech úložišť nebo značek? 

Pokud používáte prohlížeč Microsoft Edge/IE, můžete vidět maximálně 100 úložišť nebo značek. Pokud má váš registr více než 100 úložišť nebo značek, doporučujeme, abyste k jejich vypsání použili prohlížeč Firefox nebo Chrome.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Proč Azure Portal nepodaří načíst úložiště nebo značky?

Prohlížeč nemusí být schopný odeslat žádost o načtení úložišť nebo značek na server. Mohou nastat různé důvody:

* Nedostatečné síťové připojení
* Brána firewall
* Blokování služby AD
* Chyby služby DNS

Obraťte se prosím na správce sítě nebo Zkontrolujte konfiguraci sítě a připojení. Zkuste spustit pomocí rozhraní příkazového `az acr check-health -n yourRegistry` řádku Azure CLI a ověřte, jestli se vaše prostředí může připojit k Container Registry. Kromě toho můžete také vyzkoušet anonymním nebo soukromou relaci v prohlížeči, abyste se vyhnuli zastaralým mezipaměťm nebo souborům cookie v prohlížeči.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Proč dojde k selhání žádosti o přijetí změn nebo nabízení oznámení s nepovolenou operací?

Tady je několik scénářů, ve kterých může být operace zakázaná:
* Klasické Registry už nejsou podporované. Upgradujte prosím na podporovanou [úroveň služby](./container-registry-skus.md) pomocí [AZ acr Update](/cli/azure/acr#az-acr-update) nebo Azure Portal.
* Bitová kopie nebo úložiště je možná zamčené, aby se nemohlo odstranit ani aktualizovat. Aktuální atributy můžete zobrazit pomocí příkazu [AZ ACR show úložištì](./container-registry-image-lock.md) .
* Pokud je obrázek v karanténě, některé operace jsou zakázané. Přečtěte si další informace o [karanténě](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).
* Je možné, že váš registr dosáhl svého [limitu úložiště](container-registry-skus.md#service-tier-features-and-limits).

### <a name="repository-format-is-invalid-or-unsupported"></a>Formát úložiště je neplatný nebo nepodporovaný.

Pokud se zobrazí chyba, například "nepodporovaný formát úložiště", "neplatný formát" nebo "požadovaná data neexistují" při zadávání názvu úložiště v operacích úložiště, zkontrolujte pravopis a případ názvu. Platné názvy úložišť můžou obsahovat jenom malé alfanumerické znaky, tečky, pomlčky, podtržítka a lomítka. 

Úplná pravidla pojmenování úložiště najdete v tématu věnovaném [specifikaci distribuce pro iniciativu](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)v rámci služby Open container.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Návody shromažďovat trasování http ve Windows?

#### <a name="prerequisites"></a>Požadavky

- Povolit dešifrování HTTPS v Fiddler:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Povolit Docker pro použití proxy serveru prostřednictvím uživatelského rozhraní Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Nezapomeňte po dokončení vrátit zpět.  Docker nebude fungovat s povoleným a Fiddler, který neběží.

#### <a name="windows-containers"></a>Kontejnery Windows

Konfigurovat proxy Docker na 127.0.0.1:8888

#### <a name="linux-containers"></a>Linuxové kontejnery

Vyhledejte IP adresu virtuálního přepínače Docker VM:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Nakonfigurujte proxy Docker na výstup předchozího příkazu a portu 8888 (například 10.0.75.1:8888).

## <a name="tasks"></a>Úlohy

- [Návody Batch zruší spuštění?](#how-do-i-batch-cancel-runs)
- [Návody do příkazu AZ ACR Build zahrnout složku. Git?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Podporují úlohy GitLab pro aktivační události zdroje?](#does-tasks-support-gitlab-for-source-triggers)
- [Jakou službu správy úložišť Git podporují úlohy?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Návody Batch zruší spuštění?

Následující příkazy zruší všechny spuštěné úlohy v zadaném registru.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Návody do příkazu AZ ACR Build zahrnout složku. Git?

Pokud předáte do příkazu místní zdrojovou složku `az acr build` , ve `.git` výchozím nastavení se složka z nahraného balíčku vyloučí. Můžete vytvořit `.dockerignore` soubor s následujícím nastavením. Sdělí příkazu, aby obnovil všechny soubory v rámci `.git` nahraného balíčku. 

`!.git/**`

Toto nastavení platí také pro `az acr run` příkaz.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Podporují úlohy GitLab pro aktivační události zdroje?

V tuto chvíli nepodporujeme GitLab pro aktivační události zdroje.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Jakou službu správy úložišť Git podporují úlohy?

| Služba Git | Zdrojový kontext | Ruční sestavení | Automatické sestavení prostřednictvím aktivační události potvrzení |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Ano | Yes |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Yes | Yes |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Yes | No |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Yes | No |

## <a name="run-error-message-troubleshooting"></a>Řešení potíží s chybovou zprávou

| Chybová zpráva | Průvodce odstraňováním potíží |
|---|---|
|Pro virtuální počítač se nenakonfiguroval přístup, proto se nenašly žádné odběry.|K tomu může dojít, pokud používáte `az login --identity` v úloze ACR. Jedná se o přechodnou chybu a nastane, když se přiřazení role spravované identity nerozšíří. Počkejte několik sekund, než se znovu pokusí pracovat.|

## <a name="cicd-integration"></a>Integrace CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace](container-registry-intro.md) o Azure Container Registry.
