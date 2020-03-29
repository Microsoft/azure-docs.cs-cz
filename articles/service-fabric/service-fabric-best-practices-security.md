---
title: Osvědčené postupy zabezpečení Azure Service Fabric
description: Osvědčené postupy a aspekty návrhu pro zabezpečení clusterů a aplikací Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: dcdc338bdcdb2c04f6b8894ccb358bc773b95c07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258925"
---
# <a name="azure-service-fabric-security"></a>Zabezpečení služby Azure Service Fabric 

Další informace o [doporučených postupech zabezpečení Azure](https://docs.microsoft.com/azure/security/)najděte [na upřesnění zabezpečení Azure Service Fabric.](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) je doporučená služba správy tajných klíčů pro aplikace a clustery Azure Service Fabric.
> [!NOTE]
> Pokud jsou certifikáty nebo tajné klíče z trezoru klíčů nasazeny do škálovací sady virtuálních strojů jako tajný klíč sady škálování virtuálního počítače, musí být společně umístěna sada key vault a virtual machine scale set.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Vytvořit certifikát vydaný certifikátservice fabric vydané certifikační autoritou

Certifikát Azure Key Vault lze vytvořit nebo importovat do trezoru klíčů. Při vytvoření certifikátu trezoru klíčů je soukromý klíč vytvořen uvnitř trezoru klíčů a nikdy není vystaven vlastníkovi certifikátu. Zde jsou způsoby, jak vytvořit certifikát v trezoru klíčů:

- Vytvořte certifikát podepsaný svým držitelem, který vytvoří dvojici klíčů veřejného a soukromého sektoru a přidruží jej k certifikátu. Certifikát bude podepsán vlastním klíčem. 
- Vytvořte nový certifikát ručně, abyste vytvořili dvojici veřejných a soukromých klíčů a vygenerovali žádost o podepsání certifikátu X.509. Žádost o podepsání může podepsat registrační úřad nebo certifikační úřad. Podepsaný certifikát x509 lze sloučit s čekajícím párem klíčů a dokončit certifikát KV v trezoru klíčů. Přestože tato metoda vyžaduje další kroky, poskytuje větší zabezpečení, protože soukromý klíč je vytvořen v trezoru klíčů a je omezen na něj. To je vysvětleno v diagramu níže. 

Další podrobnosti najděte [metody vytváření certifikátů Azure Keyvault.](https://docs.microsoft.com/azure/key-vault/create-certificate)

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Nasazení certifikátů trezoru klíčů do škálovacích sad virtuálních strojů clusteru Service Fabric

Chcete-li nasadit certifikáty ze společně umístěného trezoru keyvault do škálovací sady virtuálních strojů, použijte [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)sady velikosti virtuálního počítače . Následují vlastnosti šablony Správce prostředků:

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
> Úschovna musí být povolena pro nasazení šablony Správce prostředků.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Použití seznamu řízení přístupu (ACL) na certifikát clusteru Service Fabric

[Vydavatel rozšíření sady škálování virtuálních strojů](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) Microsoft.Azure.ServiceFabric se používá ke konfiguraci zabezpečení uzlů.
Chcete-li použít seznam ACL pro certifikáty pro procesy clusteru Service Fabric, použijte následující vlastnosti šablony Správce prostředků:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Zabezpečení certifikátu clusteru Service Fabric běžným názvem

Chcete-li zabezpečit cluster `Common Name`Service Fabric podle certifikátu , použijte certifikát vlastnosti Správce [prostředkůCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)takto:

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
> Clustery Service Fabric budou používat první platný certifikát, který najde v úložišti certifikátů hostitele. V systému Windows se jedná o certifikát s nejnovějším datem vypršení platnosti, které odpovídá vašemu běžnému názvu a kryptografickému otisku vystavitela.

Domény Azure, například\<*\>VAŠE SUBDOMÉNA .cloudapp.azure.com nebo \<VAŠE SUBDOMÉNA\>.trafficmanager.net, jsou vlastněny společností Microsoft. Certifikační autority nebudou vydávat certifikáty pro domény neoprávněným uživatelům. Většina uživatelů si bude muset zakoupit doménu od registrátora nebo být autorizovaným správcem domény, aby vám certifikační autorita vydala certifikát s tímto běžným názvem.

Další podrobnosti o konfiguraci služby DNS tak, aby přeřadila vaši doménu na IP adresu Microsoftu, najděte, jak nakonfigurovat [Azure DNS tak, aby hostoval vaši doménu](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Po delegování názvových serverů domén na názvové servery zóny Azure DNS přidejte do zóny DNS následující dva záznamy:
> - Záznam 'A' pro doménu `Alias record set` APEX, který není pro všechny IP adresy, které vaše vlastní doména vyřeší.
> - Záznam C pro subdomény společnosti Microsoft, které jste `Alias record set`zřídit, které nejsou . Můžete například použít správce provozu nebo název DNS nástroje pro vyrovnávání zatížení.

Chcete-li aktualizovat portál tak, aby se `"managementEndpoint"`zobrazil vlastní název DNS clusteru Service Fabric , aktualizujte vlastnosti šablony Správce prostředků clusteru Service Fabric:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Šifrování tajných hodnot balíčku Service Fabric

Běžné hodnoty, které jsou šifrované v balíčcích Service Fabric, zahrnují přihlašovací údaje registru kontejnerů Azure (ACR), proměnné prostředí, nastavení a klíče účtu úložiště modulů plug-in svazku Azure.

Nastavení [šifrovacího certifikátu a šifrování tajných kódů v clusterech systému Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Vygenerujte certifikát podepsaný svým držitelem pro šifrování tajného klíče:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Pomocí pokynů v [části Nasazení certifikátů trezoru klíčů můžete škálovat sady virtuálních strojů clusteru Service Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) k nasazení certifikátů trezoru klíčů do škálovacích sad virtuálních strojů clusteru Service Fabric.

Zašifrujte svůj tajný klíč pomocí následujícího příkazu PowerShellu a pak aktualizujte manifest aplikace Service Fabric pomocí šifrované hodnoty:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Nastavení [šifrovacího certifikátu a šifrování tajných kódů v clusterech Linuxu](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Vygenerujte certifikát podepsaný svým držitelem pro šifrování tajných kódů:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Pomocí pokynů v [části Nasazení certifikátů trezoru klíčů můžete škálovat sady virtuálních strojů clusteru Service Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) clusteru pro virtuální počítače do škálovacích sad virtuálních strojů clusteru Service Fabric.

Zašifrujte tajný klíč pomocí následujících příkazů a potom aktualizujte manifest aplikace Service Fabric pomocí šifrované hodnoty:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Po zašifrování chráněných hodnot [zadejte šifrované tajné klíče v aplikaci Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)application a [dešifrujte šifrované tajné klíče z kódu služby](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Zahrnout certifikát do aplikací Service Fabric

Chcete-li aplikaci udělit přístup k tajným kódům, zahrňte certifikát přidáním prvku **SecretsCertificate** do manifestu aplikace.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Ověření aplikací Service Fabric k prostředkům Azure pomocí identity spravované služby (MSI)

Informace o spravovaných identitách pro prostředky Azure najdete v tématu [Co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).
Clustery Azure Service Fabric jsou hostované na škálovacích sadách virtuálních strojů, které podporují [identitu spravované služby](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Pokud chcete získat seznam služeb, ke kterým se msi dá použít, přečtěte si část [Služby Azure, které podporují ověřování Azure Active Directory](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Chcete-li povolit systém přiřazenou spravovanou identitu během vytváření škálovací sady `"Microsoft.Compute/virtualMachinesScaleSets"` virtuálních počítačů nebo existující škálovací sady virtuálních počítačů, deklarujte následující vlastnost:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Další informace najdete v tématu [Co je spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity)

Pokud jste [vytvořili spravovanou identitu přiřazenou uživatelem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), deklarujte v šabloně následující prostředek, který ho přiřadí k škálovací sadě virtuálních strojů. Nahraďte `\<USERASSIGNEDIDENTITYNAME\>` název uživatelem přiřazené spravované identity, kterou jste vytvořili:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Předtím, než vaše aplikace Service Fabric může využít spravované identity, musí být udělena oprávnění k prostředkům Azure, které potřebuje k ověření.
Následující příkazy udělují přístup k prostředku Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

V kódu aplikace Service Fabric [získáte přístupový token](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) pro Azure Resource Manager tím, že rest všechny podobné následující:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Aplikace Service Fabric pak můžete použít přístupový token k ověření prostředků Azure, které podporují službu Active Directory.
Následující příklad ukazuje, jak to udělat pro zdroj Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Směrné plány zabezpečení systému Windows
[Doporučujeme implementovat standardní konfiguraci, která je obecně známá a dobře otestovaná, například směrné plány zabezpečení společnosti Microsoft, na rozdíl od vytvoření směrného plánu sami](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines); možnost pro zřizování těchto na škálovací sady virtuálních počítačů je použití Azure konfigurace požadovaného stavu (DSC) obslužná rutina rozšíření, ke konfiguraci virtuálních počítačů, jakmile se stanou online, takže jsou spuštěny produkční software.

## <a name="azure-firewall"></a>Brána Azure Firewall
[Azure Firewall je spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky virtuální sítě Azure. Jedná se o plně stavovou bránu firewall jako službu s vestavěnou vysokou dostupností a neomezenou škálovatelností cloudu.](https://docs.microsoft.com/azure/firewall/overview) To umožňuje omezit odchozí provoz HTTP/S na zadaný seznam plně kvalifikovaných názvů domén (FQDN) včetně zástupných znaků. Tato funkce nevyžaduje ukončení protokolu SSL. Doporučuje se, abyste využili [značky Azure Firewall Pro Condn](https://docs.microsoft.com/azure/firewall/fqdn-tags) pro aktualizace systému Windows a aby byl povolen síťový provoz koncovým bodům služby Microsoft Windows Update, které mohou protékat bránou firewall. [Nasazení brány Azure Firewall pomocí šablony](https://docs.microsoft.com/azure/firewall/deploy-template) poskytuje ukázku pro definici šablony prostředků Microsoft.Network/azureFirewalls. Pravidla brány firewall společná pro aplikace Service Fabric je povolit následující pro vaše clustery virtuální sítě:

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

Tato pravidla brány firewall doplňují povolené odchozí skupiny zabezpečení sítě, které by zahrnovaly ServiceFabric a Storage, jako povolené cíle z vaší virtuální sítě.

## <a name="tls-12"></a>TLS 1.2
[Zts](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Ve výchozím nastavení je antivirový program Windows Defender nainstalován v systému Windows Server 2016. Podrobnosti naleznete v tématu [Windows Defender Antivirus na windows server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Uživatelské rozhraní je ve výchozím nastavení nainstalováno na některých sku, ale není vyžadováno. Chcete-li snížit dopad na výkon a režii na spotřeba prostředků, které systém Windows Defender vynaloží, a pokud zásady zabezpečení umožňují vyloučit procesy a cesty pro software s otevřeným zdrojovým kódem, deklarujte následující prostředek rozšíření škálování virtuálních strojů Vlastnosti šablony správce pro vyloučení clusteru Service Fabric z prohledávačů:


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
> Pokud nepoužíváte program Windows Defender, přečtěte si dokumentaci k antimalwaru. Program Windows Defender není v Linuxu podporován.

## <a name="platform-isolation"></a>Izolace platformy
Ve výchozím nastavení service fabric aplikace jsou udělen přístup k service fabric runtime sám, který se projevuje v různých formách: [proměnné prostředí](service-fabric-environment-variables-reference.md) ukazující na cesty k souboru na hostiteli odpovídající aplikace a fabric soubory, koncový bod komunikace mezi procesy, který přijímá požadavky specifické pro aplikaci a klientský certifikát, který Fabric očekává, že aplikace použít k ověření sám. V případě, že služba hostuje sám nedůvěryhodný kód, je vhodné zakázat tento přístup k sf runtime - pokud není výslovně potřeba. Přístup k běhu je odebrán pomocí následující deklarace v části Zásady manifestu aplikace: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Další kroky

* Vytvořte cluster na virtuálních počítačích nebo počítačích se systémem Windows Server: [Service Fabric, který vytváří cluster pro systém Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Vytvořte cluster na virtuálních počítačích nebo počítačích se systémem Linux: [Vytvořte cluster Linux](service-fabric-cluster-creation-via-portal.md).
* Další informace o [možnostech podpory service fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
