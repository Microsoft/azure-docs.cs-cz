---
title: Osvědčené postupy zabezpečení pro Azure Service Fabric | Microsoft Docs
description: Osvědčené postupy pro zabezpečení Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 75edb385a86be849ec7c165759d3b451eab804f6
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828510"
---
# <a name="azure-service-fabric-security"></a>Zabezpečení služby Azure Service Fabric 

Další informace o [osvědčených postupech zabezpečení Azure](https://docs.microsoft.com/azure/security/)najdete v článku [osvědčené postupy pro Azure Service Fabric Security](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) .

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) je doporučená služba pro správu tajných kódů pro aplikace a clustery Azure Service Fabric.
> [!NOTE]
> Pokud jsou certifikáty/tajné klíče z Key Vault nasazené do sady škálování virtuálního počítače jako tajný klíč sady virtuálních počítačů, musí se Key Vault a sada škálování virtuálního počítače umístit společně.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Vytvořit certifikát vydávající certifikační autority Service Fabric

Certifikát Azure Key Vault může být buď vytvořen, nebo importován do Key Vault. Při vytvoření certifikátu Key Vault se privátní klíč vytvoří v rámci Key Vault a nikdy se nezveřejňuje vlastníkovi certifikátu. Tady jsou způsoby, jak vytvořit certifikát v Key Vault:

- Vytvořte certifikát podepsaný svým držitelem pro vytvoření páru veřejného klíče a přidružte ho k certifikátu. Certifikát bude podepsán vlastním klíčem. 
- Vytvořte nový certifikát ručně, abyste vytvořili pár veřejného a privátního klíče a vygenerovali žádost o podepsání certifikátu X. 509. Žádost o podepsání může podepsat autorita pro registraci nebo certifikační autorita. Podepsaný certifikát x509 se dá sloučit s neřízeným párem klíčů, aby se mohl dokončit certifikát KV v Key Vault. I když tato metoda vyžaduje více kroků, nabízí větší zabezpečení, protože privátní klíč je vytvořený v a omezený na Key Vault. To je vysvětleno v diagramu níže. 

Další podrobnosti najdete v [metodách vytváření certifikátů služby Azure datatrezor](https://docs.microsoft.com/azure/key-vault/create-certificate) .

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Nasazení certifikátů Key Vault do Service Fabric cluster Virtual Machine Scale Sets

K nasazení certifikátů z společně umístěného trezoru klíčů do sady škálování virtuálních počítačů použijte [OsProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)Virtual Machine Scale set. Níže jsou uvedené vlastnosti šablony Správce prostředků:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> Pro nasazení šablony Správce prostředků musí být povolený trezor.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Použití seznamu Access Control (ACL) pro váš certifikát pro Service Fabric cluster

Rozšíření Microsoft. Azure. ServiceFabric, která slouží ke konfiguraci zabezpečení uzlů, se používá pro [rozšíření sady škálování virtuálního počítače](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) .
Pokud chcete použít seznam řízení přístupu pro vaše certifikáty pro procesy Service Fabric clusteru, použijte následující vlastnosti šablony Správce prostředků:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Zabezpečení certifikátu Service Fabric clusteru podle běžného názvu

Pokud chcete svůj Cluster Service Fabric zabezpečit pomocí certifikátu `Common Name`, použijte vlastnost šablony Správce prostředků [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)následujícím způsobem:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Clustery Service Fabric budou používat první platný certifikát, který nalezne v úložišti certifikátů hostitele. Ve Windows se jedná o certifikát s nejpozdějším datem vypršení platnosti, který odpovídá vašemu společnému názvu a kryptografickému otisku vystavitele.

Domény Azure, jako je například * \<YOUR SUBDOMAIN\>.cloudapp.azure.com nebo \<YOUR SUBDOMAIN\>.trafficmanager.net, jsou vlastněny společností Microsoft. Certifikační autority nebudou vydávat certifikáty pro domény neautorizovaným uživatelům. Většina uživatelů bude muset koupit doménu od registrátora nebo mít oprávnění správce domény, aby certifikační autorita mohla vydat certifikát s tímto běžným názvem.

Další podrobnosti o tom, jak nakonfigurovat službu DNS k překladu vaší domény na IP adresu Microsoftu, najdete v tématu Jak nakonfigurovat [Azure DNS pro hostování vaší domény](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Po delegování vašich názvových serverů domén na servery Azure DNS zóny přidejte do zóny DNS tyto dva záznamy:
> - Záznam A pro vrchol domény, který není `Alias record set` pro všechny IP adresy, které vaše vlastní doména vyřeší.
> - Záznam C pro poddomény Microsoftu, které jste zřídili, nejsou `Alias record set`. Můžete například použít Traffic Manager nebo název DNS Load Balancer.

Chcete-li aktualizovat portál tak, aby zobrazoval vlastní název DNS pro Service Fabric clusteru `"managementEndpoint"`, aktualizujte následující vlastnosti šablony Správce prostředků Service Fabric clusteru:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Šifrování Service Fabricch tajných hodnot balíčku

Mezi běžné hodnoty, které jsou zašifrované v Service Fabric balíčky, patří přihlašovací údaje Azure Container Registry (ACR), proměnné prostředí, nastavení a klíče účtu úložiště modulu plug-in Azure Volume plugin.

[Nastavení šifrovacího certifikátu a šifrování tajných klíčů v clusterech Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Vygenerujte certifikát podepsaný svým držitelem pro šifrování vašeho tajného kódu:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Postupujte podle pokynů v tématu [nasazení Key Vault certifikátů, které Service Fabric cluster Virtual Machine Scale Sets](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) k nasazení Key Vault certifikátů do Virtual Machine Scale Sets clusteru Service Fabric.

Šifrujte svůj tajný klíč pomocí následujícího příkazu PowerShellu a pak aktualizujte svůj Service Fabric manifest aplikace pomocí zašifrované hodnoty:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

[Nastavení šifrovacího certifikátu a šifrování tajných klíčů v clusterech se systémem Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Vygenerovat certifikát podepsaný svým držitelem pro šifrování tajných kódů:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Pomocí pokynů v části [nasazení Key Vault certifikátů můžete Service Fabric cluster Virtual Machine Scale Sets](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) do Virtual Machine Scale Sets clusteru Service Fabric.

Šifrujte svůj tajný klíč pomocí následujících příkazů a pak aktualizujte svůj Service Fabric manifest aplikace pomocí zašifrované hodnoty:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Po zašifrování chráněných hodnot [Zadejte šifrované tajné klíče v aplikaci Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)a [dešifrujte šifrované tajné kódy z kódu služby](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Zahrnout certifikát do aplikací Service Fabric

Chcete-li dát vaší aplikaci přístup k tajným klíčům, zahrňte certifikát přidáním elementu **SecretsCertificate** do manifestu aplikace.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Ověřování aplikací Service Fabric k prostředkům Azure pomocí Identita spravované služby (MSI)

Další informace o spravovaných identitách pro prostředky Azure najdete v tématu [co jsou spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-it-work).
Clustery Azure Service Fabric se hostují v Virtual Machine Scale Sets, které podporují [Identita spravované služby](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Seznam služeb, které se dají použít k ověřování pomocí MSI, najdete v tématu [služby Azure, které podporují ověřování Azure Active Directory](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Pokud chcete povolit spravovanou identitu přiřazenou systémem během vytváření sady škálování virtuálních počítačů nebo existující sady škálování virtuálních počítačů, deklarujte následující vlastnost `"Microsoft.Compute/virtualMachinesScaleSets"`:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity) .

Pokud jste vytvořili [spravovanou identitu přiřazenou uživatelem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), deklarujete v šabloně následující prostředek, který mu přiřadí sadu škálování virtuálního počítače. Nahraďte `\<USERASSIGNEDIDENTITYNAME\>` názvem uživatelsky přiřazené spravované identity, kterou jste vytvořili:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Předtím, než může vaše aplikace Service Fabric využít spravovanou identitu, musí být udělena oprávnění k prostředkům Azure, které potřebuje k ověřování pomocí.
Následující příkazy udělují přístup k prostředku Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

V kódu aplikace Service Fabric [Získejte přístupový token](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) pro Azure Resource Manager tím, že si ponechá všechna podobná následující:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Vaše aplikace Service Fabric potom může pomocí přístupového tokenu ověřit u prostředků Azure, které podporují službu Active Directory.
Následující příklad ukazuje, jak to provést pro Cosmos DB prostředek:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Základní hodnoty zabezpečení systému Windows
Doporučujeme [, abyste implementovali standardní standardní konfiguraci, která je široce známá a dobře testována, jako jsou například standardní hodnoty zabezpečení společnosti Microsoft, a to na rozdíl od vytvoření směrného plánu](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines). možnost, kterou si můžete zřídit v Virtual Machine Scale Sets, je použít obslužnou rutinu rozšíření DSC (Konfigurace požadovaného stavu) Azure ke konfiguraci virtuálních počítačů při jejich práci online, takže běží produkční software.

## <a name="azure-firewall"></a>Azure Firewall
[Azure firewall je spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky Azure Virtual Network. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností cloudu.](https://docs.microsoft.com/azure/firewall/overview) Díky tomu je možné omezit odchozí přenosy HTTP/S na zadaný seznam plně kvalifikovaných názvů domény (FQDN), včetně zástupných karet. Tato funkce nevyžaduje ukončení protokolu SSL. Doporučuje se využít [Azure firewall značek plně kvalifikovaného názvu domény](https://docs.microsoft.com/azure/firewall/fqdn-tags) pro aktualizace systému Windows a povolit směrování síťového provozu do Microsoft Web Windows Update koncových bodů prostřednictvím brány firewall. [Nasazení Azure firewall pomocí šablony](https://docs.microsoft.com/azure/firewall/deploy-template) poskytuje ukázku pro definici šablony prostředků Microsoft. Network/azureFirewalls. Mezi pravidla brány firewall společná pro Service Fabric aplikací je umožněno, aby pro virtuální síť clusterů byly následující:

- \* download.microsoft.com
- \* servicefabric.azure.com
- *. core.windows.net

Tato pravidla brány firewall doplňují vaše povolené odchozí skupiny zabezpečení sítě, které by zahrnovaly ServiceFabric a úložiště jako povolené cíle z vaší virtuální sítě.

## <a name="tls-12"></a>TLS 1,2
[TSG](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Ve výchozím nastavení je antivirová ochrana v programu Windows Defender nainstalovaná v systému Windows Server 2016. Podrobnosti najdete v tématu [antivirová ochrana v programu Windows Defender na Windows serveru 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Uživatelské rozhraní je ve výchozím nastavení nainstalováno u některých SKU, ale není vyžadováno. Chcete-li snížit veškerý dopad na výkon a nároky na spotřebu prostředků, které vykonává program Windows Defender, a pokud vaše zásady zabezpečení umožňují vyloučit procesy a cesty pro open source software, deklarujte následující prostředek rozšíření sady škálování virtuálních počítačů Vlastnosti šablony správce, které vyloučí Cluster Service Fabric v prověřování:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Pokud nepoužíváte Windows Defender, přečtěte si dokumentaci k antimalwaru pro pravidla konfigurace. Program Windows Defender není podporován v systému Linux.

## <a name="platform-isolation"></a>Izolace platformy
Ve výchozím nastavení mají Service Fabric aplikacím udělen přístup k samotnému modulu runtime Service Fabric, který manifestuje sám sebe v různých formulářích: [proměnné prostředí](service-fabric-environment-variables-reference.md) odkazující na cesty k souborům na hostiteli, které odpovídají souborům aplikace a prostředků infrastruktury, koncový bod komunikace mezi procesy, který přijímá požadavky specifické pro aplikaci a klientský certifikát, který Fabric očekává, že aplikace používá k ověření. V případě, že je služba hostitelem samotného nedůvěryhodného kódu, je vhodné zakázat tento přístup k modulu runtime SF, pokud je výslovně nepotřebujete. Přístup k modulu runtime se odebere pomocí následující deklarace v oddílu policies manifestu aplikace: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Další kroky

* Vytvořte cluster na virtuálních počítačích nebo počítačích s Windows serverem: [Service Fabric vytvoření clusteru pro Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Vytvořte cluster na virtuálních počítačích nebo počítačích se systémem Linux: [vytvořte cluster se systémem Linux](service-fabric-cluster-creation-via-portal.md).
* Přečtěte si o [možnostech podpory Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
