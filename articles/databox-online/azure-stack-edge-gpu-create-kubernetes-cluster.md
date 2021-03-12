---
title: Vytvoření a Správa clusteru Kubernetes na zařízení GPU pro Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak vytvořit a spravovat cluster Kubernetes na zařízení GPU pro Azure Stack Edge pro pomocí rozhraní Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: ddbf3c99845f4c38f511260f4dec274903a9d586
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631948"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Připojení a Správa clusteru Kubernetes přes kubectl na zařízení GPU Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Na zařízení Azure Stack Edge pro se vytvoří cluster Kubernetes při konfiguraci výpočetní role. Po vytvoření clusteru Kubernetes se můžete připojit k místnímu clusteru a spravovat ho z klientského počítače pomocí nativního nástroje, jako je třeba *kubectl*.

Tento článek popisuje, jak se připojit ke clusteru Kubernetes na zařízení Azure Stack Edge pro a pak ho spravovat pomocí *kubectl*. 


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Máte přístup k zařízení Azure Stack Edge pro.

2. Aktivovali jste zařízení Azure Stack Edge pro, jak je popsáno v tématu [aktivace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md).

3. Na zařízení jste povolili výpočetní roli. V zařízení se vytvořil i cluster Kubernetes, když jste na zařízení nakonfigurovali výpočetní výkon podle pokynů v tématu [Konfigurace výpočetních prostředků na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md).

4. K přístupu k zařízení máte přístup k klientskému systému Windows, na kterém běží PowerShell 5,0 nebo novější. Můžete mít i jiné klienty s [podporovaným operačním systémem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . 

5. Máte koncový bod rozhraní Kubernetes API ze stránky **zařízení** vašeho místního webového uživatelského rozhraní. Další informace najdete v pokynech v tématu [získání koncového bodu rozhraní Kubernetes API](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) .


## <a name="connect-to-powershell-interface"></a>Připojení k rozhraní PowerShell

Po vytvoření clusteru Kubernetes můžete k tomuto clusteru přistupovat, abyste mohli vytvářet obory názvů a uživatele a přiřazovat uživatele k oborům názvů. To bude vyžadovat, abyste se připojili k rozhraní PowerShellu zařízení. Postupujte podle těchto kroků na klientu Windows s běžícím prostředím PowerShell.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>Konfigurace přístupu k clusteru přes Kubernetes RBAC

Po vytvoření clusteru Kubernetes můžete použít *kubectl* prostřednictvím funkce cmdline k přístupu ke clusteru. 

V tomto postupu vytvoříte obor názvů a uživatele. Pak přidružíte uživatele k oboru názvů. Musíte taky získat *konfigurační* soubor, který umožňuje používat klienta Kubernetes ke komunikaci přímo s clusterem Kubernetes, který jste vytvořili, aniž byste se museli připojovat k rozhraní PowerShellu zařízení Azure Stack Edge pro.

1. Vytvořte obor názvů. Zadejte:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > Pro obor názvů i uživatelská jména platí [konvence pojmenování subdomén DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

    Zde je ukázkový výstup:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Vytvoří uživatele a získá konfigurační soubor. Zadejte:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > *Aseuser* se nedá použít jako uživatelské jméno, protože je vyhrazené pro výchozího uživatele přidruženého k oboru názvů IoT pro Azure Stack Edge pro.

    Tady je ukázkový výstup konfiguračního souboru:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. V prostém textu se zobrazí konfigurační soubor. Zkopírujte tento soubor a uložte ho jako *konfigurační* soubor. 

    > [!IMPORTANT]
    > Neukládejte konfigurační soubor jako soubor *. txt* a uložte ho bez přípony souboru.

4. Konfigurační soubor by měl být živý ve `.kube` složce vašeho uživatelského profilu na místním počítači. Zkopírujte soubor do této složky v profilu uživatele.

    ![Umístění konfiguračního souboru na klientovi](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)

5. Přidružte obor názvů k uživateli, který jste vytvořili. Zadejte:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Zde je ukázkový výstup:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    Až budete mít konfigurační soubor, nepotřebujete ke clusteru fyzický přístup. Pokud se váš klient může pomocí příkazu testovat protokol IP Azure Stack Edge pro zařízení, měli byste být schopni směrovat cluster pomocí příkazů *kubectl* .

6. Spusťte na svém klientovi novou relaci PowerShellu. Nemusíte být připojeni k rozhraní zařízení. Nyní se můžete `kubectl` na klienta nainstalovat pomocí následujícího příkazu:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Například pokud hlavní uzel Kubernetes běžel v 1.15.2, nainstalujte v 1.15.2 klienta.

    > [!IMPORTANT]
    > Stáhněte si klienta, který se od hlavní verze neliší o více než jednu podverzi. Verze klienta, ale může vést k hlavnímu serveru až k jedné dílčí verzi. Například hlavní server v 1.3 by měl pracovat s uzly v 1.1, v 1.2 a v 1.3 a musí fungovat s klienty v 1.2, v 1.3 a v 1.4. Další informace o verzi klienta Kubernetes najdete v tématu [zásady podpory pro verzi Kubernetes a pro zkosení verzí](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew). Další informace o verzi Kubernetes serveru na Azure Stack Edge pro najdete na webu získání Kubernetes verze serveru.<!-- insert link-->
    > V některých případech `kubectl` je v systému předinstalována, pokud používáte Docker for Windows nebo jiné nástroje. Aby bylo možné pracovat s tímto clusterem Kubernetes, je důležité stáhnout konkrétní verzi nástroje `kubectl` , jak je uvedeno v této části. 

    Instalace trvá několik minut.

7. Ověřte, že nainstalovaná verze je ta, kterou jste si stáhli. Měli byste zadat absolutní cestu, kam byla v `kubectl.exe` systému nainstalována.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Další informace o `kubectl` příkazech používaných ke správě clusteru Kubernetes najdete v článku [Přehled služby kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

8. Přidejte položku DNS do souboru Hosts ve vašem systému. 

    1. Spusťte Poznámkový blok jako správce a otevřete `hosts` soubor umístěný na adrese `C:\windows\system32\drivers\etc\hosts` . 
    2. Použijte informace, které jste uložili ze stránky **zařízení** v místním uživatelském rozhraní v předchozím kroku, a vytvořte položku v souboru Hosts. 

        Například zkopírováním tohoto koncového bodu `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` vytvoříte následující položku s IP adresou zařízení a doménou DNS: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Chcete-li ověřit, zda se můžete připojit k Kubernetes lusky, zadejte:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
Nyní můžete své aplikace nasadit v oboru názvů a pak je zobrazit v těchto aplikacích a jejich protokolech.

> [!IMPORTANT]   
> Existuje mnoho příkazů, které nebudete moci spustit, například příkazy, které vyžadují přístup správce. Můžete provádět pouze operace, které jsou v daném oboru názvů povolené.


## <a name="remove-kubernetes-cluster"></a>Odebrat cluster Kubernetes

Chcete-li odebrat cluster Kubernetes, bude nutné odebrat konfiguraci IoT Edge.

Podrobné pokyny najdete v tématu [Správa konfigurace IoT Edge](azure-stack-edge-gpu-manage-compute.md#manage-iot-edge-configuration).
   

## <a name="next-steps"></a>Další kroky

- [Nasaďte bezstavovou aplikaci na Azure Stack Edge pro](azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
