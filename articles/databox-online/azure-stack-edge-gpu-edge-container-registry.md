---
title: Povolení registru hraničního kontejneru na zařízení s grafickým procesorem Azure Stack Edge pro
description: Popisuje postup povolení registru kontejneru místního Edge na zařízení s grafickým procesorem Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: bccb6fa33007082737997c7282fb286c38e3bbd7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466195"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Povolit registr hraničního kontejneru na zařízení s grafickým procesorem Azure Stack Edge pro

Tento článek popisuje, jak povolit registr hraničního kontejneru a použít ho v rámci clusteru Kubernetes na zařízení Azure Stack Edge pro. Příklad použitý v článku podrobně popisuje, jak odeslat image ze zdrojového registru, v tomto případě do registru Microsoft Container Registry na Azure Stack hraničním zařízení, v registru hraničních kontejnerů.

### <a name="about-edge-container-registry"></a>O registru hraničních kontejnerů

Kontejnerové výpočetní aplikace běží na obrázcích kontejneru a tyto image jsou uložené v registrech. Registry můžou být veřejné, například spravované centrum Docker, privátní nebo poskytovatel cloudu, jako je například Azure Container Registry. Další informace najdete v tématu [o registrech, úložištích a obrázcích](../container-registry/container-registry-concepts.md).

Registr hraničního kontejneru poskytuje úložiště na hranici na zařízení Azure Stack Edge pro. Tento registr můžete použít k ukládání a správě privátních imagí kontejnerů.

V prostředí s více uzly se image kontejnerů dají stáhnout a vložit do registru hraničního kontejneru jenom jednou. Všechny hraniční aplikace můžou použít registr hraničního kontejneru pro následná nasazení.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Máte přístup k zařízení Azure Stack Edge pro.

1. Aktivovali jste zařízení Azure Stack Edge pro, jak je popsáno v tématu [aktivace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md).

1. Na zařízení jste povolili výpočetní roli. V zařízení se vytvořil i cluster Kubernetes, když jste na zařízení nakonfigurovali výpočetní výkon podle pokynů v tématu [Konfigurace výpočetních prostředků na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Máte koncový bod rozhraní Kubernetes API ze stránky **zařízení** vašeho místního webového uživatelského rozhraní. Další informace najdete v pokynech v tématu [získání koncového bodu rozhraní API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Máte přístup k klientskému systému s [podporovaným operačním systémem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Při použití klienta systému Windows by měl systém při přístupu k zařízení spustit PowerShell 5,0 nebo novější.

    1. Pokud chcete načíst a vložit vlastní image kontejnerů, ujistěte se, že je v systému nainstalovaný klient Docker. Pokud používáte klienta Windows, [nainstalujte na Windows Docker Desktop](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Povolit registr kontejneru jako doplněk

Prvním krokem je povolení registru hraničního kontejneru jako doplňku.

1. [Připojte se k rozhraní PowerShell zařízení](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. Chcete-li povolit registr kontejneru jako doplněk, zadejte: 

    `Set-HcsKubernetesContainerRegistry`
    
    Dokončení této operace může trvat několik minut.

    Zde je ukázkový výstup tohoto příkazu:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Podrobnosti o registru kontejneru získáte tak, že zadáte:

    `Get-HcsKubernetesContainerRegistryInfo`

    Tady je ukázka z tohoto příkazu:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Poznamenejte si uživatelské jméno a heslo z výstupu `Get-HcsKubernetesContainerRegistryInfo` . Tyto přihlašovací údaje se používají pro přihlášení k registru hraničního kontejneru při vkládání imagí.         


## <a name="manage-container-registry-images"></a>Správa imagí registru kontejneru

Je možné, že budete chtít získat přístup k registru kontejnerů mimo vaše Azure Stack hraniční zařízení. Je také možné, že budete chtít do registru vložit Image nebo je načíst.

Pro přístup k registru hraničního kontejneru použijte tento postup:

1. Získejte podrobnosti o koncovém bodu pro registr kontejneru Edge.
    1. V místním uživatelském rozhraní zařízení, přejít na **zařízení**.
    1. Vyhledejte **koncový bod registru hraničního kontejneru**.
        ![Koncový bod registru kontejneru Edge na stránce zařízení](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Zkopírujte tento koncový bod a vytvořte odpovídající položku DNS do `C:\Windows\System32\Drivers\etc\hosts` souboru klienta pro připojení ke koncovému bodu registru hraničního kontejneru. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![Přidat položku DNS pro koncový bod registru kontejneru Edge](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Stáhněte si certifikát služby Edge Container Registry z místního uživatelského rozhraní. 
    1. V místním uživatelském rozhraní zařízení, přejít na **certifikáty**.
    1. Vyhledejte položku pro **certifikát Edge Container Registry**. Napravo od této položky vyberte **stažení** a Stáhněte si certifikát Edge Container Registry do vašeho klientského systému, který budete používat pro přístup k vašemu zařízení. 

        ![Stáhnout certifikát koncového bodu registru hraničního kontejneru](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Nainstalujte stažený certifikát na klienta. Pokud používáte klienta Windows, postupujte takto: 
    1. Vyberte certifikát a v **Průvodci importem certifikátu** vyberte Uložit umístění jako **místní počítač**. 

        ![Nainstalovat certifikát 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Nainstalujte certifikát do místního počítače v důvěryhodném kořenovém úložišti. 

        ![Instalace certifikátu 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. Po instalaci certifikátu restartujte na svém systému klienta Docker.

1. Přihlaste se k registru hraničního kontejneru. Zadejte:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Zadejte koncový bod registru hraničního kontejneru ze stránky **zařízení** a uživatelské jméno a heslo, které jste získali z výstupu `Get-HcsKubernetesContainerRegistryInfo` . 

1. Použijte příkazy Docker push nebo Pull pro vložení nebo stažení imagí kontejneru z registru kontejneru.
 
    1. Načíst obrázek z image Microsoft Container Registry Zadejte:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Vytvořte alias obrázku, který jste vyžádali, pomocí plně kvalifikované cesty k vašemu registru.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Nahrajte image do svého registru.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Spusťte obrázek, který jste vložili do registru.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Tady je ukázkový výstup příkazů pro vyžádání a vložení:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Přejděte k `http://localhost:8080` zobrazení běžícího kontejneru. V tomto případě se zobrazí Nginx Server, na kterém běží.

    ![Zobrazení běžícího kontejneru](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Chcete-li zastavit a odebrat kontejner, stiskněte klávesu `Control+C` .

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Použití imagí v registru hraničního kontejneru přes Kubernetes lusky

Teď můžete nasadit image, kterou jste vložili do registru hraničních kontejnerů, z Kubernetes lusků.

1. K nasazení image musíte nakonfigurovat přístup ke clusteru přes *kubectl*. Vytvoření oboru názvů, uživatele, udělení přístupu uživatele k oboru názvů a získání *konfiguračního* souboru. Ujistěte se, že se můžete připojit k Kubernetes lusky. 
    
    Postupujte podle všech kroků v části [připojení a Správa clusteru Kubernetes přes kubectl na zařízení GPU Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). 

    Tady je ukázkový výstup pro obor názvů na zařízení, ze kterého může uživatel přistupovat ke clusteru Kubernetes.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. Tajné klíče pro vyžádání obsahu jsou už nastavené ve všech oborech názvů Kubernetes na vašem zařízení. Tajné kódy můžete získat pomocí `get secrets` příkazu. Zde je ukázkový výstup:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Nasaďte pod svůj obor názvů pomocí kubectl. Použijte následující `yaml` . 

    Nahraďte Image: `<image-name>` s imagí vloženou do registru kontejneru. Přečtěte si tajné kódy v oborech názvů pomocí imagePullSecrets s názvem: `ase-ecr-credentials` .
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Použijte nasazení v oboru názvů, který jste vytvořili pomocí příkazu použít. Ověřte, že je kontejner spuštěný. Zde je ukázkový výstup:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Odstranění imagí registru kontejneru

Služba Edge Container Registry Storage je hostovaná v rámci vašeho zařízení Azure Stack Edge pro, které je omezené dostupným úložištěm na zařízení. Je vaší zodpovědností odstranit nepoužívané image Docker z registru kontejneru pomocí rozhraní Docker HTTP v2 API ( https://docs.docker.com/registry/spec/api/) .  

Chcete-li odebrat jednu nebo více imagí kontejneru, použijte následující postup:

1. Nastavte název obrázku na obrázek, který chcete odstranit.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Nastavení uživatelského jména a hesla registru kontejneru jako přihlašovacích údajů PS

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. Vypíše značky přidružené k imagi.

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Vypíše výtah přidružený ke značce, kterou chcete odstranit. To používá $tags z výstupu výše uvedeného příkazu. Pokud máte více značek, vyberte jednu z nich a použijte ji v dalším příkazu.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Odstranění obrázku pomocí výtahu obrázku: Značka

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

Po odstranění nepoužívaných imagí se místo přidružené k neodkazované bitové kopii automaticky uvolní procesem, který běží v noci. 

## <a name="next-steps"></a>Další kroky

- [Nasaďte bezstavovou aplikaci na Azure Stack Edge pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
