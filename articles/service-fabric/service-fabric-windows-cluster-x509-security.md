---
title: Zabezpečení clusteru ve Windows pomocí certifikátů
description: Zabezpečená komunikace v rámci samostatného nebo místního clusteru Azure Service Fabric a také mezi klienty a clusterem.
ms.topic: conceptual
ms.date: 10/15/2017
ms.openlocfilehash: d75c644be47ea44f6a8a6ccac91b785af0132833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791033"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Zabezpečení samostatného clusteru ve Windows pomocí certifikátů X. 509
Tento článek popisuje, jak zabezpečit komunikaci mezi různými uzly samostatného clusteru se systémem Windows. Popisuje také způsob ověřování klientů, kteří se připojují k tomuto clusteru pomocí certifikátů X. 509. Ověřování zajišťuje, že přístup ke clusteru a nasazeným aplikacím a provádění úloh správy bude mít jenom autorizovaní uživatelé. V případě vytvoření clusteru by mělo být v clusteru povoleno zabezpečení certifikátů.  

Další informace o zabezpečení clusterů, jako je zabezpečení mezi uzly, zabezpečení mezi klienty a uzly a řízení přístupu na základě rolí, najdete v tématu [scénáře zabezpečení clusteru](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Které certifikáty potřebujete?
Pokud chcete začít, [Stáhněte si balíček Service Fabric pro Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) do jednoho z uzlů v clusteru. Ve staženém balíčku najdete ClusterConfig.X509.MultiMachine.jsv souboru. Otevřete soubor a podívejte se na téma zabezpečení v části vlastnosti:

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

Tato část popisuje certifikáty, které potřebujete k zabezpečení samostatného clusteru Windows. Pokud zadáte certifikát clusteru, nastavte hodnotu ClusterCredentialType na _x509_. Pokud zadáte certifikát serveru pro vnější připojení, nastavte ServerCredentialType na _x509_. I když to není povinné, doporučujeme, abyste oba tyto certifikáty nastavili pro správný zabezpečený cluster. Pokud nastavíte tyto hodnoty na hodnotu *x509*, musíte také zadat odpovídající certifikáty nebo Service Fabric vyvolá výjimku. V některých scénářích může být vhodné zadat pouze _ClientCertificateThumbprints_ nebo _ReverseProxyCertificate_. V těchto scénářích není nutné nastavovat _ClusterCredentialType_ ani _ServerCredentialType_ na hodnotu _x509_.


> [!NOTE]
> [Kryptografický otisk](https://en.wikipedia.org/wiki/Public_key_fingerprint) je primární identita certifikátu. Kryptografický otisk certifikátů, které vytvoříte, najdete v tématu [Načtení kryptografického otisku certifikátu](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
> 
> 

V následující tabulce jsou uvedené certifikáty, které v instalaci clusteru potřebujete:

| **Nastavení CertificateInformation** | **Popis** |
| --- | --- |
| ClusterCertificate |Doporučuje se pro testovací prostředí. Tento certifikát je nutný k zabezpečení komunikace mezi uzly v clusteru. Pro upgrade můžete použít dva různé certifikáty, primární a sekundární. Nastavte kryptografický otisk primárního certifikátu v části kryptografický otisk a na sekundárním v proměnné ThumbprintSecondary. |
| ClusterCertificateCommonNames |Doporučuje se pro produkční prostředí. Tento certifikát je nutný k zabezpečení komunikace mezi uzly v clusteru. Můžete použít jeden nebo dva běžné názvy certifikátů clusteru. CertificateIssuerThumbprint odpovídá kryptografický otisk vystavitele tohoto certifikátu. Pokud se používá víc než jeden certifikát se stejným běžným názvem, můžete zadat víc kryptografických otisků vystavitele.|
| ClusterCertificateIssuerStores |Doporučuje se pro produkční prostředí. Tento certifikát odpovídá vystaviteli certifikátu clusteru. V této části můžete zadat běžný název vystavitele a odpovídající název úložiště místo určení kryptografického otisku vystavitele v ClusterCertificateCommonNames.  To usnadňuje výměnu certifikátů vystavitelů clusteru. Pokud je použit více než jeden certifikát clusteru, lze zadat více vystavitelů. Prázdná IssuerCommonName povoluje všechny certifikáty v odpovídajících úložištích určených v rámci X509StoreNames.|
| ServerCertificate |Doporučuje se pro testovací prostředí. Tento certifikát se zobrazí klientovi, když se pokusí připojit k tomuto clusteru. Pro usnadnění práce se můžete rozhodnout použít stejný certifikát pro ClusterCertificate a ServerCertificate. Pro upgrade můžete použít dva různé certifikáty serveru, primární a sekundární. Nastavte kryptografický otisk primárního certifikátu v části kryptografický otisk a na sekundárním v proměnné ThumbprintSecondary. |
| ServerCertificateCommonNames |Doporučuje se pro produkční prostředí. Tento certifikát se zobrazí klientovi, když se pokusí připojit k tomuto clusteru. CertificateIssuerThumbprint odpovídá kryptografický otisk vystavitele tohoto certifikátu. Pokud se používá víc než jeden certifikát se stejným běžným názvem, můžete zadat víc kryptografických otisků vystavitele. Pro usnadnění práce se můžete rozhodnout použít stejný certifikát pro ClusterCertificateCommonNames a ServerCertificateCommonNames. Můžete použít jeden nebo dva běžné názvy certifikátů serveru. |
| ServerCertificateIssuerStores |Doporučuje se pro produkční prostředí. Tento certifikát odpovídá vystaviteli certifikátu serveru. V této části můžete zadat běžný název vystavitele a odpovídající název úložiště místo určení kryptografického otisku vystavitele v ServerCertificateCommonNames.  Díky tomu je možné snadno vyměnit certifikáty vystavitelů serverů. Pokud je použit více než jeden certifikát serveru, lze zadat více vystavitelů. Prázdná IssuerCommonName povoluje všechny certifikáty v odpovídajících úložištích určených v rámci X509StoreNames.|
| ClientCertificateThumbprints |Nainstalujte tuto sadu certifikátů na ověřené klienty. V počítačích, ve kterých chcete povolený přístup ke clusteru, můžete mít nainstalovanou řadu různých klientských certifikátů. Nastavte kryptografický otisk každého certifikátu v proměnné CertificateThumbprint. Pokud nastavíte možnost Správce na *hodnotu true*, může klient s tímto certifikátem nainstalovaným v tomto clusteru provádět aktivity správy správců. Pokud je hodnota správce *false*, může klient s tímto certifikátem provádět akce, které jsou povoleny pouze pro uživatelská práva, obvykle jen pro čtení. Další informace o rolích najdete v tématu [Service Fabric řízení přístupu na základě role](service-fabric-cluster-security.md#service-fabric-role-based-access-control). |
| ClientCertificateCommonNames |Nastavte běžný název prvního klientského certifikátu pro CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk pro vystavitele tohoto certifikátu. Další informace o běžných názvech a vystaviteli najdete v tématu [práce s certifikáty](/dotnet/framework/wcf/feature-details/working-with-certificates). |
| ClientCertificateIssuerStores |Doporučuje se pro produkční prostředí. Tento certifikát odpovídá vystaviteli klientského certifikátu (role správce i bez role správce). V této části můžete zadat běžný název vystavitele a odpovídající název úložiště místo určení kryptografického otisku vystavitele v ClientCertificateCommonNames.  To usnadňuje výměnu certifikátů vystavitelů klientů. Pokud je použit více než jeden certifikát klienta, lze zadat více vystavitelů. Prázdná IssuerCommonName povoluje všechny certifikáty v odpovídajících úložištích určených v rámci X509StoreNames.|
| ReverseProxyCertificate |Doporučuje se pro testovací prostředí. Tento volitelný certifikát může být zadán, pokud chcete zabezpečení [reverzního proxy serveru](service-fabric-reverseproxy.md). Pokud použijete tento certifikát, ujistěte se, že je reverseProxyEndpointPort nastavené v uzlu nodeType. |
| ReverseProxyCertificateCommonNames |Doporučuje se pro produkční prostředí. Tento volitelný certifikát může být zadán, pokud chcete zabezpečení [reverzního proxy serveru](service-fabric-reverseproxy.md). Pokud použijete tento certifikát, ujistěte se, že je reverseProxyEndpointPort nastavené v uzlu nodeType. |

Tady je příklad konfigurace clusteru, kde jsou k dispozici cluster, server a klientské certifikáty. Pro certifikáty Cluster/Server/reverseProxy nejde kryptografický otisk a běžný název nakonfigurovat společně pro stejný typ certifikátu.

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

## <a name="certificate-rollover"></a>Změna certifikátu
Pokud místo kryptografického otisku použijete běžný název certifikátu, změna certifikátu nevyžaduje upgrade konfigurace clusteru. Pro upgrady kryptografických otisků vystavitele se ujistěte, že se nový seznam kryptografických otisků protíná se starým seznamem. Nejdřív musíte provést upgrade konfigurace s novými kryptografickými otisky vystavitele a potom do úložiště nainstalovat nové certifikáty (certifikát clusteru/serveru a certifikáty vystavitele). Ponechte původní certifikát vystavitele v úložišti certifikátů aspoň dvě hodiny po instalaci nového certifikátu vystavitele.
Pokud používáte úložiště vystavitelů, není třeba provést upgrade konfigurace pro výměnu certifikátu vystavitele. Nainstalujte nový certifikát vystavitele s pozdějším datem vypršení platnosti v příslušném úložišti certifikátů a odeberte starý certifikát vystavitele po několika hodinách.

## <a name="acquire-the-x509-certificates"></a>Získání certifikátů X. 509
Aby bylo možné zabezpečit komunikaci v rámci clusteru, musíte nejprve získat certifikáty X. 509 pro uzly clusteru. Chcete-li kromě toho omezit připojení k tomuto clusteru na autorizované počítače nebo uživatele, je třeba získat a nainstalovat certifikáty pro klientské počítače.

Pro clustery, na kterých běží produkční úlohy, použijte k zabezpečení clusteru certifikát X. 509 podepsaný certifikační [autoritou (CA)](https://en.wikipedia.org/wiki/Certificate_authority). Další informace o tom, jak tyto certifikáty získat, najdete v tématu [Jak získat certifikát](/dotnet/framework/wcf/feature-details/how-to-obtain-a-certificate-wcf). 

K dispozici je řada vlastností, které musí mít certifikát, aby bylo možné správně fungovat:

* Poskytovatel certifikátu musí být od **společnosti Microsoft vylepšený zprostředkovatel kryptografických služeb RSA a AES** .

* Při vytváření klíče RSA se ujistěte, že je klíč **2048 bitů**.

* Rozšíření použití klíče má hodnotu **digitální podpis, zakódování klíče (a0)** .

* Rozšíření použití rozšířeného klíče má hodnoty **ověřování serveru** (OID: 1.3.6.1.5.5.7.3.1) a **ověření klienta** (OID: 1.3.6.1.5.5.7.3.2).

Pro clustery, které používáte pro účely testování, můžete použít certifikát podepsaný svým držitelem.

Další otázky najdete v [nejčastějších](./cluster-security-certificate-management.md#troubleshooting-and-frequently-asked-questions)dotazech k certifikátu.

## <a name="optional-create-a-self-signed-certificate"></a>Volitelné: vytvoření certifikátu podepsaného svým držitelem
Jedním ze způsobů, jak vytvořit certifikát podepsaný svým držitelem, který se dá správně zabezpečit, je použít skript CertSetup.ps1 ve složce sady Service Fabric SDK v adresáři C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Úpravou tohoto souboru můžete změnit výchozí název certifikátu. (Podívejte se na hodnotu CN = ServiceFabricDevClusterCert.) Spusťte tento skript jako `.\CertSetup.ps1 -Install` .

Nyní exportujte certifikát do souboru. pfx s chráněným heslem. Nejprve Získejte kryptografický otisk certifikátu. 
1. Z nabídky **Start** spusťte **spravovat certifikáty počítače**. 

2. Přejdete do **místní složky počítač \ osobní** a vyhledáte certifikát, který jste vytvořili. 

3. Poklikejte na certifikát pro otevření, vyberte kartu **Podrobnosti** a přejděte dolů k poli **kryptografický otisk** . 

4. Odeberte mezery a zkopírujte hodnotu kryptografického otisku do následujícího příkazu PowerShellu. 

5. Změňte `String` hodnotu na vhodné zabezpečené heslo pro ochranu a spusťte následující příkaz v PowerShellu:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Chcete-li zobrazit podrobnosti o certifikátu nainstalovaném v počítači, spusťte následující příkaz prostředí PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Případně, pokud máte předplatné Azure, postupujte podle kroků v části [Vytvoření clusteru Service Fabric pomocí Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Instalace certifikátů
Po nainstalování certifikátů je můžete nainstalovat na uzly clusteru. Uzly musí mít na počítačích nainstalované nejnovější prostředí Windows PowerShell 3. x. Tento postup opakujte na každém uzlu pro certifikáty clusteru i serveru a všechny sekundární certifikáty.

1. Zkopírujte soubor. pfx nebo soubory do uzlu.

2. Otevřete okno PowerShellu jako správce a zadejte následující příkazy. Nahraďte *$PSWD* heslem, které jste použili k vytvoření tohoto certifikátu. Nahraďte *$PfxFilePath* úplnou cestou k souboru. pfx zkopírovaného do tohoto uzlu.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Teď pro tento certifikát nastavte řízení přístupu, aby proces Service Fabric, který se spouští pod účtem síťové služby, mohl použít tento skript spuštěním následujícího skriptu. Zadejte kryptografický otisk certifikátu a **síťové služby** pro účet služby. Seznam ACL na certifikátu můžete ověřit tak, že otevřete certifikát v **nabídce Start**  >  **spravovat certifikáty počítače** a vyhledáte **všechny úlohy**  >  **Správa privátních klíčů**.
   
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
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
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
4. Opakujte předchozí kroky pro každý certifikát serveru. Pomocí těchto kroků můžete také nainstalovat klientské certifikáty do počítačů, u kterých chcete zpřístupnit přístup ke clusteru.

## <a name="create-the-secure-cluster"></a>Vytvoření zabezpečeného clusteru
Až nakonfigurujete část zabezpečení ClusterConfig.X509.MultiMachine.jsv souboru, můžete přejít k části [Vytvoření clusteru](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) a nakonfigurovat uzly a vytvořit samostatný cluster. Nezapomeňte při vytváření clusteru použít ClusterConfig.X509.MultiMachine.jsv souboru. Například váš příkaz může vypadat takto:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Po úspěšném spuštění zabezpečeného samostatného clusteru Windows a nastavování ověřených klientů pro připojení použijte postup v části [připojení ke clusteru pomocí PowerShellu](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) , který se k němu připojí. Například:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Pak můžete spustit další příkazy PowerShellu pro práci s tímto clusterem. Pomocí rutiny [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) můžete například zobrazit seznam uzlů v tomto zabezpečeném clusteru.


Pokud chcete cluster odebrat, připojte se k uzlu v clusteru, do kterého jste stáhli balíček Service Fabric, otevřete příkazový řádek a potom do složky Package (balíček). Nyní spusťte následující příkaz:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Nesprávná konfigurace certifikátu může zabránit tomu, aby se cluster během nasazení dostal. Chcete-li provést samočinnou diagnostiku problémů se zabezpečením, podívejte se do části Prohlížeč událostí **aplikace a služby**  >  **společnosti Microsoft-Service Fabric**.
> 
> 
