---
title: Zabezpečení clusteru v systému Windows pomocí certifikátů
description: Zabezpečená komunikace v rámci samostatného nebo místního clusteru Azure Service Fabric, stejně jako mezi klienty a clusterem.
author: dkkapur
ms.topic: conceptual
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 5a18f957dfb7143f403d5ac30ea184023021f12c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75613920"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Zabezpečení samostatného clusteru v systému Windows pomocí certifikátů X.509
Tento článek popisuje, jak zabezpečit komunikaci mezi různými uzly samostatného clusteru Windows. Popisuje také, jak ověřit klienty, kteří se připojují k tomuto clusteru pomocí certifikátů X.509. Ověřování zajišťuje, že ke clusteru a nasazeným aplikacím mají přístup pouze oprávnění uživatelé a provádějí úlohy správy. Zabezpečení certifikátů by mělo být v clusteru povoleno při vytvoření clusteru.  

Další informace o zabezpečení clusteru, jako je například zabezpečení mezi uzly, zabezpečení klient-uzel a řízení přístupu založené na rolích, naleznete [v tématu Scénáře zabezpečení clusteru](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Jaké certifikáty potřebujete?
Chcete-li začít s, [stáhněte service fabric pro Windows Server balíček](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) do jednoho z uzlů v clusteru. Ve staženém balíčku najdete soubor ClusterConfig.X509.MultiMachine.json. Otevřete soubor a zkontrolujte část zabezpečení v části vlastnosti:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

Tato část popisuje certifikáty, které potřebujete k zabezpečení samostatného clusteru Windows. Pokud zadáte certifikát clusteru, nastavte hodnotu Typu ClusterCredentialType na _X509_. Pokud zadáte certifikát serveru pro vnější připojení, nastavte typ serverových pověření na _X509_. I když není povinné, doporučujeme mít oba tyto certifikáty pro správně zabezpečený cluster. Pokud nastavíte tyto hodnoty *X509*, musíte také zadat odpovídající certifikáty nebo Service Fabric vyvolá výjimku. V některých případech můžete chtít zadat pouze _ClientCertificateThumbprints_ nebo _ReverseProxyCertificate_. V těchto scénářích není nutné nastavit _Typ clusteruCredentialType_ nebo _ServerCredentialType_ na _X509_.


> [!NOTE]
> [Kryptografický otisk](https://en.wikipedia.org/wiki/Public_key_fingerprint) je primární identita certifikátu. Chcete-li zjistit kryptografický otisk certifikátů, které vytvoříte, naleznete [v tématu Načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

V následující tabulce jsou uvedeny certifikáty, které potřebujete v nastavení clusteru:

| **Nastavení Informace o certifikátech** | **Popis** |
| --- | --- |
| ClusterCertificate |Doporučeno pro testovací prostředí. Tento certifikát je nutný k zabezpečení komunikace mezi uzly v clusteru. Pro upgrade můžete použít dva různé certifikáty, primární a sekundární. Nastavte kryptografický otisk primárního certifikátu v části Kryptografický otisk a sekundární v thumbprintsekundární proměnné. |
| ClusterCertificateCommonNames |Doporučeno pro produkční prostředí. Tento certifikát je nutný k zabezpečení komunikace mezi uzly v clusteru. Můžete použít jeden nebo dva běžné názvy certifikátů clusteru. CertificateIssuerThumbprint odpovídá kryptografickému otisku vystavittele tohoto certifikátu. Pokud je použit více než jeden certifikát se stejným běžným názvem, můžete zadat více vystavitelkryvné otisky.|
| ClusterCertificateIssuerStores |Doporučeno pro produkční prostředí. Tento certifikát odpovídá vystavitteru certifikátu clusteru. Můžete zadat obecný název vystavittele a odpovídající název obchodu v této části namísto zadání kryptografického otisku v části ClusterCertificateCommonNames.  To usnadňuje převrácení certifikátů vystavitetele clusteru. Pokud je použito více certifikátů clusteru, lze zadat více vystavitelů. Prázdný VydavatelCommonName whitelists všechny certifikáty v odpovídajících úložištích zadaných v části X509StoreNames.|
| Serverový certifikát |Doporučeno pro testovací prostředí. Tento certifikát je klientovi předložen při pokusu o připojení k tomuto clusteru. Pro větší pohodlí můžete použít stejný certifikát pro ClusterCertificate a ServerCertificate. Pro upgrade můžete použít dva různé serverové certifikáty, primární a sekundární. Nastavte kryptografický otisk primárního certifikátu v části Kryptografický otisk a sekundární v thumbprintsekundární proměnné. |
| Název ServerCertificateCommonNames |Doporučeno pro produkční prostředí. Tento certifikát je klientovi předložen při pokusu o připojení k tomuto clusteru. CertificateIssuerThumbprint odpovídá kryptografickému otisku vystavittele tohoto certifikátu. Pokud je použit více než jeden certifikát se stejným běžným názvem, můžete zadat více vystavitelkryvné otisky. Pro větší pohodlí můžete použít stejný certifikát pro ClusterCertificateCommonNames a ServerCertificateCommonNames. Můžete použít jeden nebo dva běžné názvy certifikátů serveru. |
| ServerCertificateIssuerStores |Doporučeno pro produkční prostředí. Tento certifikát odpovídá vystavitteru certifikátu serveru. Můžete zadat obecný název vystavittele a odpovídající název obchodu v této části namísto zadání kryptografického otisku v části ServerCertificateCommonNames.  To usnadňuje převrácení certifikáty vystavitele serveru. Více vystavitelů lze zadat, pokud je použito více než jeden certifikát serveru. Prázdný VydavatelCommonName whitelists všechny certifikáty v odpovídajících úložištích zadaných v části X509StoreNames.|
| ClientCertificateThumbprints |Nainstalujte tuto sadu certifikátů na ověřené klienty. V počítačích můžete mít nainstalováno několik různých klientských certifikátů, které chcete povolit přístup ke clusteru. Nastavte kryptografický otisk každého certifikátu v proměnné CertificateThumbprint. Pokud nastavíte isadmin na *hodnotu true*, může klient s tímto certifikátem nainstalovaným v něm provést aktivity správy správce v clusteru. Pokud je isadmin *false*, klient s tímto certifikátem můžete provádět akce povolené pouze pro práva přístupu uživatele, obvykle jen pro čtení. Další informace o rolích naleznete v [tématu Řízení přístupu na základě rolí (RBAC).](service-fabric-cluster-security.md#role-based-access-control-rbac) |
| ClientCertificateCommonNames |Nastavte běžný název prvního klientského certifikátu pro název CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk pro vystavittele tohoto certifikátu. Další informace o běžných názvech a vystaviteli najdete v tématu [Práce s certifikáty](https://msdn.microsoft.com/library/ms731899.aspx). |
| Úložiště KlientCertificateIssuerStores |Doporučeno pro produkční prostředí. Tento certifikát odpovídá vystavitelovi klientského certifikátu (role správce i role bez správce). Můžete zadat obecný název vystavitele a odpovídající název obchodu v této části namísto zadání kryptografického otisku v části ClientCertificateCommonNames.  To usnadňuje převrácení certifikáty vystavitele klienta. Více vystavitelů lze zadat, pokud je použito více než jeden klientský certifikát. Prázdný VydavatelCommonName whitelists všechny certifikáty v odpovídajících úložištích zadaných v části X509StoreNames.|
| Reverzníproxycertificate |Doporučeno pro testovací prostředí. Tento volitelný certifikát lze zadat, pokud chcete zabezpečit [reverzní proxy](service-fabric-reverseproxy.md)server . Ujistěte se, že reverseProxyEndpointPort je nastavena v nodeTypes, pokud používáte tento certifikát. |
| Název ReverseProxyCertificateCommon |Doporučeno pro produkční prostředí. Tento volitelný certifikát lze zadat, pokud chcete zabezpečit [reverzní proxy](service-fabric-reverseproxy.md)server . Ujistěte se, že reverseProxyEndpointPort je nastavena v nodeTypes, pokud používáte tento certifikát. |

Zde je příklad konfigurace clusteru, kde byly poskytnuty certifikáty clusteru, serveru a klienta. U certifikátů cluster/server/reverseProxy nelze kryptografický otisk a běžný název společně nakonfigurovat pro stejný typ certifikátu.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Převrácení certifikátu
Při použití běžného názvu certifikátu namísto kryptografického otisku nevyžaduje přechod certifikátu upgrade konfigurace clusteru. Pro upgrade kryptografický otisk vystavitho na vystavit, ujistěte se, že nový seznam kryptografický otisk protíná se starým seznamem. Nejprve je třeba provést upgrade konfigurace pomocí nových kryptografických otisků vystavittela a potom nainstalovat nové certifikáty (certifikáty clusteru/serveru i certifikáty vystavitenek) do úložiště. Ponechat starý certifikát vystavittele v úložišti certifikátů po dobu nejméně dvou hodin po instalaci nového certifikátu vystavittele.
Pokud používáte úložiště vystavite, není třeba provádět upgrade konfigurace pro přechod na certifikát vystavitho certifikátu. Nainstalujte nový certifikát vystavittele s datem vypršení platnosti do odpovídajícího úložiště certifikátů a po několika hodinách odeberte starý certifikát vystavittele.

## <a name="acquire-the-x509-certificates"></a>Získání certifikátů X.509
Chcete-li zabezpečit komunikaci v rámci clusteru, musíte nejprve získat certifikáty X.509 pro uzly clusteru. Chcete-li omezit připojení k tomuto clusteru na autorizované počítače/uživatele, je třeba získat a nainstalovat certifikáty pro klientské počítače.

Pro clustery, ve kterých jsou spuštěny produkční úlohy, použijte k zabezpečení clusteru certifikát X.509 podepsaný [certifikační autoritou.](https://en.wikipedia.org/wiki/Certificate_authority) Další informace o získání těchto certifikátů naleznete [v tématu Jak získat certifikát](https://msdn.microsoft.com/library/aa702761.aspx).

Pro clustery, které používáte pro testovací účely, můžete použít certifikát podepsaný svým držitelem.

## <a name="optional-create-a-self-signed-certificate"></a>Volitelné: Vytvoření certifikátu podepsaného svým držitelem
Jedním ze způsobů, jak vytvořit certifikát podepsaný svým držitelem, který lze správně zabezpečit, je použití skriptu CertSetup.ps1 ve složce Service Fabric SDK v adresáři C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Chcete-li změnit výchozí název certifikátu, upravte tento soubor. (Vyhledejte hodnotu CN=ServiceFabricDevClusterCert.) Spusťte `.\CertSetup.ps1 -Install`tento skript jako .

Nyní exportujte certifikát do souboru .pfx s chráněným heslem. Nejprve získat kryptografický otisk certifikátu. 
1. V nabídce **Start** spusťte **spravovat certifikáty počítače**. 

2. Přejděte do složky **Místní počítač\Osobní** a vyhledejte certifikát, který jste vytvořili. 

3. Poklepáním na certifikát otevřete, vyberte kartu **Podrobnosti** a posuňte se dolů do pole **Kryptografický otisk.** 

4. Odeberte mezery a zkopírujte hodnotu kryptografického otisku do následujícího příkazu Prostředí PowerShell. 

5. Změňte `String` hodnotu na vhodné zabezpečené heslo k jeho ochraně a v Prostředí PowerShell spusťte následující:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Chcete-li zobrazit podrobnosti o certifikátu nainstalovaném v počítači, spusťte následující příkaz prostředí PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Případně pokud máte předplatné Azure, postupujte podle pokynů v [části Vytvoření clusteru Service Fabric pomocí Správce prostředků Azure](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Instalace certifikátů
Po vytvoření certifikátů je můžete nainstalovat do uzlů clusteru. Uzly musí mít nainstalovaný nejnovější systém Windows PowerShell 3.x. Opakujte tyto kroky na každém uzlu pro clusterové i serverové certifikáty a všechny sekundární certifikáty.

1. Zkopírujte soubor pfx nebo soubory do uzlu.

2. Otevřete okno PowerShellu jako správce a zadejte následující příkazy. Nahraďte *$pswd* heslem, které jste použili k vytvoření tohoto certifikátu. Nahraďte *$PfxFilePath* úplnou cestou .pfx zkopírovanou do tohoto uzlu.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Nyní nastavte řízení přístupu k tomuto certifikátu tak, aby proces Service Fabric, který běží pod účtem síťové služby, jej mohl použít spuštěním následujícího skriptu. Zadejte kryptografický otisk certifikátu a **síťové služby** pro účet služby. Seznamy ACL na certifikátu jsou správné, pokud otevřete certifikát v seznamu > **Spouštět spravovat certifikáty počítače** a podíváte se na všechny **úkoly** > **spravovat soukromé klíče**. **Start**
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Opakujte předchozí kroky pro každý certifikát serveru. Pomocí těchto kroků můžete také nainstalovat klientské certifikáty do počítačů, které chcete povolit přístup ke clusteru.

## <a name="create-the-secure-cluster"></a>Vytvoření zabezpečeného clusteru
Po konfiguraci části zabezpečení souboru ClusterConfig.X509.MultiMachine.json můžete přejít k části [Vytvořit cluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) a nakonfigurovat uzly a vytvořit samostatný cluster. Nezapomeňte při vytváření clusteru použít soubor ClusterConfig.X509.MultiMachine.json. Váš příkaz může vypadat například takto:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Po úspěšném spuštění zabezpečeného samostatného clusteru Windows a nastavení ověřených klientů pro připojení k němu postupujte podle pokynů v části [Připojení ke clusteru pomocí prostředí PowerShell.](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) Například:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Pro práci s tímto clusterem pak můžete spustit další příkazy prostředí PowerShell. Můžete například spustit [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) a zobrazit seznam uzlů v tomto zabezpečeném clusteru.


Chcete-li cluster odebrat, připojte se k uzlu v clusteru, do kterého jste stáhli balíček Service Fabric, otevřete příkazový řádek a přejděte do složky balíčku. Nyní spusťte následující příkaz:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Nesprávná konfigurace certifikátu může zabránit vzniku clusteru během nasazení. Chcete-li problémy se zabezpečením diagnostikovat sami, podívejte se do skupiny Prohlížeč událostí **Aplikace a protokoly** > služeb**Microsoft-Service Fabric**.
> 
> 

