---
title: Zabezpečení clusteru služby Azure Service Fabric ve Windows pomocí certifikátů | Dokumentace Microsoftu
description: Zabezpečení komunikace v rámci samostatná jednotka nebo v místním clusteru služby Azure Service Fabric, stejně jako mezi klienty a clusteru.
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
ms.openlocfilehash: b23b2c46098fb53a3a08ff86c46cc6b6c9b936bb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228568"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Zabezpečení samostatného clusteru ve Windows pomocí certifikátů X.509
Tento článek popisuje, jak zabezpečit komunikaci mezi různými uzly samostatného clusteru Windows. Také popisuje, jak k ověřování klientů, které se připojují k tomuto clusteru pomocí certifikátů X.509. Ověřování zajišťuje, že můžete jenom Autorizovaní uživatelé přístup ke clusteru a nasazených aplikací a provádění úloh správy. Certifikát zabezpečení musí být povolené na clusteru při vytvoření clusteru.  

Další informace o zabezpečení clusteru, jako je zabezpečení mezi uzly, uzel klienta zabezpečení a řízení přístupu na základě rolí, najdete v části [scénáře zabezpečení clusteru](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Certifikáty, které potřebujete?
Začněte tím [Stáhnout balíček aplikace Service Fabric pro Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) k jednomu z uzlů v clusteru. V staženého balíčku najdete ClusterConfig.X509.MultiMachine.json souboru. Otevřete soubor a projděte si část pro zabezpečení v části vlastnosti:

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

Tato část popisuje certifikáty, které potřebujete k zabezpečení samostatného clusteru Windows. Pokud chcete zadat certifikát clusteru, nastavte hodnotu ClusterCredentialType k _X509_. Pokud zadáte certifikát serveru pro externí připojení, nastavte ServerCredentialType na _X509_. I když není povinné, doporučujeme, abyste měli oba tyto certifikáty pro správně zabezpečený cluster. Pokud tyto hodnoty nastavíte na *X509*, musíte zadat také odpovídající certifikáty nebo vyvolá výjimku, Service Fabric. V některých případech můžete chtít zadat pouze _ClientCertificateThumbprints_ nebo _ReverseProxyCertificate_. V těchto případech není nutné nastavit _ClusterCredentialType_ nebo _ServerCredentialType_ k _X509_.


> [!NOTE]
> A [kryptografický otisk](https://en.wikipedia.org/wiki/Public_key_fingerprint) je primární identitu certifikátu. Kryptografický otisk certifikáty, které vytvoříte, najdete v tématu [načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

V následující tabulce jsou uvedeny certifikáty, které je třeba na vašem nastavení clusteru:

| **Nastavení CertificateInformation** | **Popis** |
| --- | --- |
| ClusterCertificate |Vhodné pro testovací prostředí. Tento certifikát je vyžadován pro zabezpečení komunikace mezi uzly v clusteru. Dva různé certifikáty, primární a sekundární, můžete použít k upgradu. V části kryptografický otisk a že sekundární databáze v proměnné ThumbprintSecondary nastavte kryptografický otisk certifikátu primární. |
| ClusterCertificateCommonNames |Doporučuje se pro produkční prostředí. Tento certifikát je vyžadován pro zabezpečení komunikace mezi uzly v clusteru. Můžete použít jednu nebo dvě clusteru běžné názvy certifikátů. CertificateIssuerThumbprint odpovídá kryptografický otisk vystavitele certifikátu. Pokud se používá více než jeden certifikát se stejným názvem společné, můžete zadat více kryptografických otisků vystavitele.|
| ClusterCertificateIssuerStores |Doporučuje se pro produkční prostředí. Tento certifikát odpovídá vystavitele certifikátu clusteru. Vystavitel zadáte běžný název a odpovídající název úložiště v této části místo určení kryptografický otisk vystavitele v rámci ClusterCertificateCommonNames.  To usnadňuje výměna certifikátů clusteru vystavitele. Pokud více než jeden cluster, certifikát se používá, je možné zadat více vystavitelů. Prázdný tito IssuerCommonName všechny certifikáty v úložišti odpovídající zadané v části X509StoreNames.|
| ServerCertificate |Vhodné pro testovací prostředí. Tento certifikát se zobrazí na klienta při pokusu o připojení k tomuto clusteru. Pro usnadnění práce můžete použít stejný certifikát pro ClusterCertificate a ServerCertificate. Dva certifikáty jiný server, primární a sekundární, můžete použít k upgradu. V části kryptografický otisk a že sekundární databáze v proměnné ThumbprintSecondary nastavte kryptografický otisk certifikátu primární. |
| ServerCertificateCommonNames |Doporučuje se pro produkční prostředí. Tento certifikát se zobrazí na klienta při pokusu o připojení k tomuto clusteru. CertificateIssuerThumbprint odpovídá kryptografický otisk vystavitele certifikátu. Pokud se používá více než jeden certifikát se stejným názvem společné, můžete zadat více kryptografických otisků vystavitele. Pro usnadnění práce můžete použít stejný certifikát pro ClusterCertificateCommonNames a ServerCertificateCommonNames. Můžete použít jednu nebo dvě certifikát běžné názvy serverů. |
| ServerCertificateIssuerStores |Doporučuje se pro produkční prostředí. Tento certifikát odpovídá vystavitele certifikátu serveru. Vystavitel zadáte běžný název a odpovídající název úložiště v této části místo určení kryptografický otisk vystavitele v rámci ServerCertificateCommonNames.  To umožňuje snadno vyměnit certifikáty vystavitele serveru. Více vystavitelů může být zadán, pokud se používá více než jeden certifikát serveru. Prázdný tito IssuerCommonName všechny certifikáty v úložišti odpovídající zadané v části X509StoreNames.|
| ClientCertificateThumbprints |Nainstalujte tuto sadu certifikáty na ověření klienti. Může mít řadu různých klientských certifikátů nainstalován v počítačích, které chcete povolit přístup ke clusteru. Nastavte kryptografický otisk jednotlivých certifikátů v proměnné Miniatura certifikátu. Pokud nastavíte IsAdmin *true*, klient se tento certifikát nainstalován na něj můžete provést správce aktivity správy v clusteru. Pokud je IsAdmin *false*, klient s tímto certifikátem může provádět akce povoluje jenom pro uživatele přístupová práva, obvykle jen pro čtení. Další informace o rolích najdete v tématu [řízení přístupu na základě Role (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Nastavte běžný název první klientský certifikát pro CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk vystavitele certifikátu. Další informace o běžné názvy a vystavitele, naleznete v tématu [práce s certifikáty](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Doporučuje se pro produkční prostředí. Tento certifikát odpovídá vystavitele certifikátu klienta (role správce i bez oprávnění správce). Vystavitel zadáte běžný název a odpovídající název úložiště v této části místo určení kryptografický otisk vystavitele v rámci ClientCertificateCommonNames.  To umožňuje snadno vyměnit certifikáty vystavitele klienta. Více vystavitelů může být zadán, pokud se používá více než jeden certifikát klienta. Prázdný tito IssuerCommonName všechny certifikáty v úložišti odpovídající zadané v části X509StoreNames.|
| ReverseProxyCertificate |Vhodné pro testovací prostředí. Tento volitelný certifikát může být zadán, pokud chcete zabezpečit vaše [reverzní proxy server](service-fabric-reverseproxy.md). Ujistěte se, že reverseProxyEndpointPort je nastavena v nodeTypes, pokud použijete tento certifikát. |
| ReverseProxyCertificateCommonNames |Doporučuje se pro produkční prostředí. Tento volitelný certifikát může být zadán, pokud chcete zabezpečit vaše [reverzní proxy server](service-fabric-reverseproxy.md). Ujistěte se, že reverseProxyEndpointPort je nastavena v nodeTypes, pokud použijete tento certifikát. |

Tady je příklad konfigurace clusteru ve kterém byly zadány cluster, server a klientských certifikátů. Pro certifikáty clusteru/server/reverseProxy kryptografický otisk a běžný název nelze konfigurovat najednou pro stejný typ certifikátu.

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

## <a name="certificate-rollover"></a>Certifikát výměny
Při použití běžný název certifikátu místo kryptografického otisku certifikátu výměny nevyžaduje konfigurace upgradu clusteru. Pro upgrade kryptografický otisk vystavitele Ujistěte se, že se seznamu nový kryptografický otisk protíná se původní seznam. Nejprve budete muset provést upgrade konfigurace s novou kryptografické otisky vystavitele a potom nainstalovat nové certifikáty (certifikát clusteru/serveru a certifikátů vystavitelů) v úložišti. Zachovat původní Vystavitel certifikátu v úložišti certifikátů po dobu nejméně dvě hodiny po instalaci nového certifikátu vystavitele.
Pokud používáte úložiště vystavitele, pak žádný upgrade konfigurace je třeba provést pro vystavitele certifikátu výměny. Odpovídající úložiště certifikátů nainstalujte nový certifikát vydavatele s datem vypršení platnosti druhé a odebrat starý certifikát vystavitele po několik hodin.

## <a name="acquire-the-x509-certificates"></a>Získat certifikáty X.509
Pro zabezpečení komunikace v rámci clusteru, musíte nejprve získat certifikáty X.509 pro uzly clusteru. Kromě toho pokud chcete omezit připojení k tomuto clusteru na autorizované počítače/uživatele, musíte získat a nainstalovat certifikáty pro klientské počítače.

Pro clustery, na kterých běží úlohy v produkčním prostředí, použijte [certifikační autority (CA)](https://en.wikipedia.org/wiki/Certificate_authority)-podepsaný certifikát X.509 k zabezpečení clusteru. Další informace o tom, jak získat tyto certifikáty, najdete v části [jak získat certifikát](https://msdn.microsoft.com/library/aa702761.aspx).

Pro clustery, které používáte pro účely testování můžete použít certifikát podepsaný svým držitelem.

## <a name="optional-create-a-self-signed-certificate"></a>Volitelné: Vytvořit certifikát podepsaný svým držitelem
Chcete-li vytvořit certifikát podepsaný svým držitelem, který může být správně zabezpečená jedním ze způsobů je CertSetup.ps1 skript použít v Service Fabric SDK složku v adresáři C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Upravte tento soubor můžete změnit výchozí název certifikátu. (Hledejte hodnotu CN = ServiceFabricDevClusterCert.) Spusťte tento skript jako `.\CertSetup.ps1 -Install`.

Nyní exportujte certifikát do souboru PFX chráněný heslem. Nejprve získejte kryptografický otisk certifikátu. 
1. Z **Start** nabídky, spusťte **spravovat certifikáty počítače**. 

2. Přejděte **Místní počítač\Osobní** složky a vyhledejte certifikát jste vytvořili. 

3. Dvakrát klikněte na certifikát a otevřít ho, vyberte **podrobnosti** kartu a přejděte dolů k položce **kryptografický otisk** pole. 

4. Odebrat mezery a zkopírujte hodnotu kryptografického otisku do následujícího příkazu Powershellu. 

5. Změnit `String` hodnota, která má vhodná zabezpečené heslo k ochraně ho a spusťte následující příkaz powershellu:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Chcete-li zobrazit podrobnosti o certifikátu nainstalovaného na počítači, spusťte následující příkaz Powershellu:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Případně, pokud máte předplatné Azure, postupujte podle kroků v [vytvořit cluster Service Fabric pomocí Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Nainstalujte certifikáty
Jakmile budete mít certifikáty, můžete je nainstalovat na uzlech clusteru. Uzly musí mít nejnovější prostředí Windows PowerShell 3.x nainstalované. Opakujte tyto kroky na každém uzlu clusteru a certifikáty serveru a všechny sekundární certifikáty.

1. Zkopírujte soubor .pfx nebo souborů do uzlu.

2. Otevřete okno Powershellu jako správce a zadejte následující příkazy. Nahraďte *$pswd* s heslem, které jste použili k vytvoření tohoto certifikátu. Nahraďte *$PfxFilePath* s úplnou cestou soubor .pfx zkopírovány do tohoto uzlu.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Nyní nastavte na tento certifikát řízení přístupu tak, aby proces Service Fabric, který se spouští pod účtem Network Service, můžete použít spuštěním následujícího skriptu. Zadejte kryptografický otisk certifikátu a **síťová služba** pro účet služby. Můžete zkontrolovat, že jsou seznamy ACL na certifikátu správné tak, že otevřete certifikát v **Start** > **spravovat certifikáty počítače** a prohlížení **všechny úkoly**  >  **Spravovat soukromé klíče**.
   
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
4. Opakujte předchozí kroky pro každý certifikát serveru. Tyto kroky můžete použít také k instalaci certifikátů klienta na počítačích, které chcete povolit přístup ke clusteru.

## <a name="create-the-secure-cluster"></a>Vytvoření zabezpečeného clusteru
Po dokončení konfigurace zabezpečení část souboru ClusterConfig.X509.MultiMachine.json můžete přejít k [vytvořit cluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) oddílu konfigurace uzlů a vytvoření samostatného clusteru. Nezapomeňte použít soubor ClusterConfig.X509.MultiMachine.json při vytváření clusteru. Například váš příkaz může vypadat takto:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Jakmile budete mít Windows zabezpečení samostatného clusteru úspěšně spuštěn a nastavili ověření klienti se k němu připojit, postupujte podle kroků v části [připojení ke clusteru pomocí prostředí PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) se k němu připojit. Příklad:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Potom můžete spustit další příkazy prostředí PowerShell pro práci s tímto clusterem. Například můžete spustit [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) zobrazíte seznam uzlů, které na tento zabezpečený cluster.


Odebrání clusteru, připojit k uzlu v clusteru, které jste stáhli balíček Service Fabric, otevřete příkazový řádek a přejděte do složky balíčku. Nyní spusťte následující příkaz:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Chystá se během nasazování clusteru může zabránit konfigurace nesprávný certifikát. Samoobslužné diagnostikovat problémy se zabezpečením, najdete v události prohlížeč skupiny **protokoly aplikací a služeb** > **Microsoft Service Fabric**.
> 
> 

