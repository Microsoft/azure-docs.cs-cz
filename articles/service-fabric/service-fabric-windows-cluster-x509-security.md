---
title: Zabezpečení clusteru služby Azure Service Fabric v systému Windows pomocí certifikátů | Microsoft Docs
description: Zabezpečení komunikace v rámci clusteru samostatné nebo místní služby Azure Service Fabric, a také mezi klienty a clusteru.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 62d821894521c5dea8e7577b75d9590adc829263
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212411"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Zabezpečení samostatné clusteru v systému Windows pomocí certifikátů X.509
Tento článek popisuje, jak pro zabezpečení komunikace mezi různými uzly clusteru Windows samostatné. Také popisuje, jak k ověřování klientů, které se připojují k tomuto clusteru pomocí certifikátů X.509. Ověřování zajistí, že můžete jenom autorizovaným uživatelům přístup ke clusteru a nasazené aplikace a provádět úlohy správy. Certifikát zabezpečení by měly být povoleny v clusteru při vytvoření clusteru.  

Další informace o zabezpečení clusteru, jako jsou například – uzly zabezpečení, klientský uzel zabezpečení a řízení přístupu na základě rolí, najdete v části [clusteru scénáře zabezpečení](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Certifikáty, které potřebujete?
Abyste mohli začít [Stáhnout balíček prostředků infrastruktury služby pro systém Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) s jedním z uzlů v clusteru. V staženého balíčku vyhledejte soubor ClusterConfig.X509.MultiMachine.json. Otevřete soubor a projděte si část pro zabezpečení v části vlastnosti:

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
                "CertificateIssuerThumbprint": "[Thumbprint]",
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

Tato část popisuje certifikáty, které potřebujete k zabezpečení samostatný cluster Windows. Pokud zadáte certifikát clusteru, nastavte hodnotu ClusterCredentialType k _X509_. Pokud zadáte certifikát serveru pro připojení mimo, nastavit ServerCredentialType _X509_. I když není povinné, doporučujeme, abyste měli obě tyto certifikáty pro cluster s podporou správně zabezpečené. Pokud nastavíte tyto hodnoty na *X509*, musíte zadat také odpovídající certifikáty nebo Service Fabric vyvolá výjimku. V některých případech můžete chtít zadat jenom _ClientCertificateThumbprints_ nebo _ReverseProxyCertificate_. V těchto scénářích, nemusíte nastavit _ClusterCredentialType_ nebo _ServerCredentialType_ k _X509_.


> [!NOTE]
> A [kryptografický otisk](https://en.wikipedia.org/wiki/Public_key_fingerprint) je primární Identita certifikátu. Kryptografický otisk certifikáty, které vytvoříte, najdete v tématu [načíst kryptografický otisk certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

Následující tabulka uvádí certifikáty, které musíte na vaše nastavení clusteru:

| **Nastavení CertificateInformation** | **Popis** |
| --- | --- |
| ClusterCertificate |Vhodné pro testovací prostředí. Tento certifikát je vyžadován k zabezpečení komunikace mezi uzly v clusteru. Dva různé certifikáty, primárního a sekundárního, můžete použít pro upgrade. V části kryptografický otisk a u sekundární v proměnné ThumbprintSecondary nastavte kryptografický otisk certifikátu primární. |
| ClusterCertificateCommonNames |Nedoporučuje pro produkční prostředí. Tento certifikát je vyžadován k zabezpečení komunikace mezi uzly v clusteru. Můžete použít jednu nebo dvě clusteru běžné názvy certifikátů. CertificateIssuerThumbprint odpovídá kryptografický otisk tohoto certifikátu vystavitele. Pokud se používá více než jeden certifikát se stejným názvem společné, můžete zadat několik kryptografické otisky vystavitele.|
| ClusterCertificateIssuerStores |Nedoporučuje pro produkční prostředí. Tento certifikát odpovídá vystavitele certifikátu clusteru. Běžné jméno a odpovídající název úložiště v této části místo zadání kryptografický otisk vystavitelů pod ClusterCertificateCommonNames zajistíte vystavitele.  To usnadňuje výměny clusteru vystavitelů certifikátů. Pokud clusteru více než jeden certifikát se používá, je možné zadat více vystavitelů. Prázdný povolených programů IssuerCommonName všechny certifikáty v úložišti odpovídající zadané v položce X509StoreNames.|
| ServerCertificate |Vhodné pro testovací prostředí. Tento certifikát je pro klienta zobrazí při pokusu o připojení k tomuto clusteru. Pro usnadnění práce můžete použít stejný certifikát pro ClusterCertificate a ServerCertificate. Dva certifikáty jiný server, primárního a sekundárního, můžete použít pro upgrade. V části kryptografický otisk a u sekundární v proměnné ThumbprintSecondary nastavte kryptografický otisk certifikátu primární. |
| ServerCertificateCommonNames |Nedoporučuje pro produkční prostředí. Tento certifikát je pro klienta zobrazí při pokusu o připojení k tomuto clusteru. CertificateIssuerThumbprint odpovídá kryptografický otisk tohoto certifikátu vystavitele. Pokud se používá více než jeden certifikát se stejným názvem společné, můžete zadat několik kryptografické otisky vystavitele. Pro usnadnění práce můžete použít stejný certifikát pro ClusterCertificateCommonNames a ServerCertificateCommonNames. Můžete použít jednu nebo dvě certifikát běžné názvy serverů. |
| ServerCertificateIssuerStores |Nedoporučuje pro produkční prostředí. Tento certifikát odpovídá vystavitele certifikátu serveru. Běžné jméno a odpovídající název úložiště v této části místo zadání kryptografický otisk vystavitelů pod ServerCertificateCommonNames zajistíte vystavitele.  To usnadňuje výměny serveru vystavitelů certifikátů. Více vystavitelů může být zadán, pokud se používá více než jeden certifikát. Prázdný povolených programů IssuerCommonName všechny certifikáty v úložišti odpovídající zadané v položce X509StoreNames.|
| ClientCertificateThumbprints |Instalovat tuto sadu certifikátů na ověřené klienty. Může mít řadu různých klientské certifikáty, které jsou nainstalované v počítačích, které chcete povolit přístup ke clusteru. Nastaví kryptografický otisk každý certifikát v proměnnou CertificateThumbprint. Pokud nastavíte IsAdmin na *true*, klient s tímto certifikátem na něm nainstalován, můžete provést správce správy aktivit v clusteru. Pokud je IsAdmin *false*, klient s tímto certifikátem můžete provádět akce povoleno pouze pro uživatele přístupová práva, obvykle jen pro čtení. Další informace o rolích naleznete v tématu [řízení přístupu na základě Role (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Nastavte běžný název první klientský certifikát pro CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk tohoto certifikátu vystavitele. Další informace o běžné názvy a vystavitele, najdete v části [pracovat s certifikáty](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Nedoporučuje pro produkční prostředí. Tento certifikát odpovídá vystavitele certifikátu klienta (role správce a bez oprávnění správce). Běžné jméno a odpovídající název úložiště v této části místo zadání kryptografický otisk vystavitelů pod ClientCertificateCommonNames zajistíte vystavitele.  To usnadňuje výměny klientských vystavitelů certifikátů. Více vystavitelů může být zadán, pokud se používá více než jeden certifikát klienta. Prázdný povolených programů IssuerCommonName všechny certifikáty v úložišti odpovídající zadané v položce X509StoreNames.|
| ReverseProxyCertificate |Vhodné pro testovací prostředí. Tento volitelný certifikát může být zadán, pokud chcete zabezpečit vaše [reverse proxy](service-fabric-reverseproxy.md). Ujistěte se, že reverseProxyEndpointPort je nastavena v nodeTypes, pokud použijete tento certifikát. |
| ReverseProxyCertificateCommonNames |Nedoporučuje pro produkční prostředí. Tento volitelný certifikát může být zadán, pokud chcete zabezpečit vaše [reverse proxy](service-fabric-reverseproxy.md). Ujistěte se, že reverseProxyEndpointPort je nastavena v nodeTypes, pokud použijete tento certifikát. |

Tady je příklad konfigurace clusteru kde byly zadány clusteru, server a klientských certifikátů. Pro certifikáty clusteru nebo serveru nebo reverseProxy kryptografický otisk a běžný název nelze nakonfigurovat společně pro stejný typ certifikátu.

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
        }
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

## <a name="certificate-rollover"></a>Změna certifikátu
Pokud použijete místo kryptografický otisk běžný název certifikátu, nevyžaduje certifikáty vyměnit s upgradem konfigurace clusteru. Pro upgrade kryptografický otisk vystavitelů Ujistěte se, že nový kryptografický otisk seznam protíná s původní seznam. Nejprve budete muset provést upgrade konfigurace s novou kryptografické otisky vystavitele a pak nainstalujte nové certifikáty (certifikát serveru nebo clusteru a certifikátů vystavitelů) v úložišti. Zachovat původní Vystavitel certifikátu v úložišti certifikátů pro alespoň dvě hodiny po instalaci nového vystavitele certifikátu.
Pokud používáte vystavitele úložiště, pak žádná konfigurace upgradu je nutné provést pro vystavitele certifikátu výměny. Nainstalujte nový certifikát vystavitele s pevným datem vypršení platnosti pozdější v úložišti certifikátů odpovídající a odebrat starý certifikát vystavitele po několik hodin.

## <a name="acquire-the-x509-certificates"></a>Získat certifikáty X.509
Pro zabezpečení komunikace v rámci clusteru, musíte nejprve získat certifikáty X.509 pro uzly clusteru. Kromě toho pokud chcete omezit připojení k tomuto clusteru na autorizované počítače nebo uživatele, musíte získat a nainstalovat certifikáty pro klientské počítače.

Pro clustery, které jsou spuštěné úlohy v produkčním prostředí, použijte [certifikátu certifikační autority](https://en.wikipedia.org/wiki/Certificate_authority)-podepsaný certifikát X.509 k zabezpečení clusteru. Další informace o tom, jak získat tyto certifikáty, najdete v části [jak získat certifikát](http://msdn.microsoft.com/library/aa702761.aspx).

Pro clustery, které používáte pro testovací účely můžete použít certifikát podepsaný svým držitelem.

## <a name="optional-create-a-self-signed-certificate"></a>Volitelné: Vytvořit certifikát podepsaný svým držitelem
Jeden způsob, jak vytvořit certifikát podepsaný svým držitelem, které mohly být zabezpečeny správně, je použít skript CertSetup.ps1 ve složce Service Fabric SDK v adresáři C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Upravte tento soubor a změnit výchozí název certifikátu. (Vyhledejte hodnotu CN = ServiceFabricDevClusterCert.) Spusťte tento skript jako `.\CertSetup.ps1 -Install`.

Nyní exportu certifikátu do souboru .pfx chráněný heslem. Nejdřív získáte kryptografický otisk certifikátu. 
1. Z **spustit** nabídce Spustit **spravovat certifikáty počítače**. 

2. Přejděte na **Místní počítač\Osobní** složky a najít certifikát, které jste vytvořili. 

3. Dvakrát klikněte na certifikát, který chcete otevřít, vyberte **podrobnosti** kartě a přejděte dolů k položce **kryptografický otisk** pole. 

4. Odebrat mezery a zkopírujte hodnotu kryptografického otisku do následujícího příkazu Powershellu. 

5. Změna `String` hodnotu na vhodný zabezpečené heslo, aby ho ochránil a spusťte následující příkazy v prostředí PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Pokud chcete zobrazit podrobnosti o certifikát nainstalovaný na počítači, spusťte následující příkaz prostředí PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Případně, pokud máte předplatné Azure, postupujte podle kroků v [vytvořit cluster Service Fabric pomocí Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Nainstalujte certifikáty
Až budete mít certifikáty, můžete je nainstalovat na uzlech clusteru. Uzly musí mít nejnovější prostředí Windows PowerShell 3.x na nich být nainstalovaný. Opakujte tyto kroky na každém uzlu clusteru a certifikáty serveru a všechny sekundární certifikáty.

1. Zkopírujte soubor .pfx nebo soubory do uzlu.

2. Otevřete okno prostředí PowerShell jako správce a zadejte následující příkazy. Nahraďte *$pswd* s heslem, které jste použili k vytvoření tohoto certifikátu. Nahraďte *$PfxFilePath* s úplnou cestou .pfx zkopíruje na tento uzel.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Teď nastavte řízení přístupu na tento certifikát tak, aby Service Fabric procesu, který běží pod účtem Network Service, můžete použít tak, že spustíte následující skript. Zadejte kryptografický otisk certifikátu a **síťové služby** pro účet služby. Můžete zkontrolovat, zda jsou seznamy ACL v certifikátu správné otevřením certifikátu v **spustit** > **spravovat certifikáty počítače** a prohlížení **všechny úlohy** > **spravovat privátní klíče**.
   
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
4. Opakujte předchozí kroky pro každý certifikát serveru. Tyto kroky můžete použít také k instalaci klientské certifikáty na počítačích, které chcete povolit přístup ke clusteru.

## <a name="create-the-secure-cluster"></a>Vytvoření zabezpečeného clusteru
Po dokončení konfigurace zabezpečení části souboru ClusterConfig.X509.MultiMachine.json, můžete pokračovat [vytvoření clusteru](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) oddílu konfigurace uzlů a vytvořit samostatný cluster. Nezapomeňte použít soubor ClusterConfig.X509.MultiMachine.json při vytváření clusteru. Například váš příkaz může vypadat následovně:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Až budete mít Windows zabezpečené samostatný cluster úspěšně systémem a nastavili klienty ověřené připojení k tomuto, postupujte podle kroků v části [připojení ke clusteru pomocí prostředí PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) připojení k tomuto. Příklad:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Potom můžete spustit další příkazy prostředí PowerShell pro práci s tímto clusterem. Například můžete spustit [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode.md?view=azureservicefabricps) zobrazíte seznam uzlů na tomto zabezpečení clusteru.


Chcete-li odebrat cluster, připojit k uzlu v clusteru, kam jste stáhli balíček Service Fabric, otevřete příkazový řádek a přejděte do složky balíčku. Nyní spusťte následující příkaz:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Nesprávný certifikát konfigurace může zabránit objevuje během nasazení clusteru. Samoobslužné diagnostikovat problémy se zabezpečením, podívejte se události prohlížeč skupiny **protokoly aplikací a služeb** > **Microsoft Service Fabric**.
> 
> 

