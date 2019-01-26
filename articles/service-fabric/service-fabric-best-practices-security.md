---
title: Azure Service Fabric osvědčené postupy zabezpečení | Dokumentace Microsoftu
description: Osvědčené postupy pro zabezpečení Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 587478b7b53a6ddafcda9980dfa4c2a00daeea16
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913779"
---
# <a name="azure-service-fabric-security"></a>Zabezpečení služby Azure Service Fabric 

Další informace o [osvědčené postupy zabezpečení Azure](https://docs.microsoft.com/azure/security/), zkontrolujte [osvědčené postupy zabezpečení Azure Service Fabric](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices)

## <a name="key-vault"></a>Key Vault

[Služba Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) je doporučené tajné klíče služby správy pro aplikace Azure Service Fabric a clustery.
> [!NOTE]
> Pokud certifikáty a tajné kódy z trezoru klíčů se nasadí do virtuálního počítače Škálovací sady jako tajného nastavit škálování virtuálního počítače a pak služby Key Vault a Škálovací sady virtuálních počítačů musí být umístěny společně.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Vytvoření certifikační autority vystaví certifikát Service Fabric

Na server certifikát služby Azure Key Vault můžou vytvořit nebo importovat do služby Key Vault. Když se vytvoří certifikátem Key Vaultu, privátní klíč se vytvoří ve službě Key Vault a nikdy vystavený certifikát vlastníka. Tady je způsoby, jak vytvořit certifikát ve službě Key Vault:

- Vytvořte certifikát podepsaný svým držitelem k vytvoření páru veřejného a privátního klíče a přidružte jej k certifikátu. Certifikát podepíše svůj vlastní klíč. 
- Vytvořte nový certifikát ručně k vytvoření páru veřejného a privátního klíče a vygenerování žádosti o podepsání certifikátu X.509. Žádost o podepsání může být podepsány registrační autority nebo certifikační autorita. Pair – x509 podepsaný certifikát lze sloučit s probíhající klíč k dokončení KV certifikátu ve službě Key Vault. I když tato metoda vyžaduje další kroky, se poskytují větší zabezpečení vzhledem k tomu, že privátní klíč je vytvořen a s omezením pomocí specifikátoru do služby Key Vault. To je vysvětleno v následujícím diagramu. 

Kontrola [metod vytváření certifikátů trezor klíčů Azure](https://docs.microsoft.com/azure/key-vault/create-certificate) další podrobnosti.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Nasazení certifikátů služby Key Vault do škálovací sady virtuálních počítačů clusteru Service Fabric

K nasazení certifikátů z společně umístěné keyvault pro Škálovací sady virtuálních počítačů, použijte Škálovací sadu virtuálních počítačů [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). Toto jsou vlastnosti šablony Resource Manageru:

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
> Trezor musí být povolena pro nasazení šablony Resource Manageru.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Použít seznam řízení přístupu (ACL) pro váš certifikát pro váš cluster Service Fabric

[Rozšíření Škálovací sady virtuálních počítačů](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) vydavatele Microsoft.Azure.ServiceFabric slouží ke konfiguraci zabezpečení uzly.
Pokud chcete seznam ACL použít u certifikátů pro váš Service Fabric Cluster procesy, použijte následující vlastnosti šablony Resource Manageru:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Zabezpečení pomocí běžného názvu certifikátu clusteru Service Fabric

K zabezpečení vašeho clusteru Service Fabric pomocí certifikátu `Common Name`, použijte vlastnost šablony Resource Manageru [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), následujícím způsobem:

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
> Clustery Service Fabric použije první nalezené v úložišti certifikátů hostitele vašeho platný certifikát. Na Windows bude certifikát s nejnovější, u nichž vyprší platnost data, která odpovídá vaší běžné jméno a vystavitele kryptografický otisk.

Domén Azure, například *\<YOUR SUBDOMÉNU\>. cloudapp.azure.com nebo \<YOUR SUBDOMÉNU\>. trafficmanager.net, jsou majetkem společnosti Microsoft. Certifikační autority nevydá certifikáty pro domény neoprávněným uživatelům. Většina uživatelů bude nutné koupit od doménového registrátora domény nebo být správcem domény autorizovaný pro certifikační autority k vystavení certifikátu s tímto názvem běžné.

Pro další podrobnosti o tom, jak nakonfigurovat služba DNS přeložit vaši doménu na Microsoft IP adres, přečtěte si, jak nakonfigurovat [Azure DNS k hostování vaší domény](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Po přidělení názvové servery vaší domény na názvové servery vaší zóny Azure DNS, přidejte následující dva záznamy do zóny DNS:
> - ' ' Záznamu pro doménu VRCHOLU, který není `Alias record set` všem IP adresám se vyřeší vaše vlastní doména.
> - "C" záznam pro Microsoft sub domény jste zřídili, které nejsou `Alias record set`. Můžete například použít Traffic Manager nebo název DNS pro vyrovnávání zatížení.

Chcete-li aktualizovat portál k zobrazení vlastního názvu DNS pro váš Cluster Service Fabric `"managementEndpoint"`, aktualizovat následující vlastnosti šablony správce prostředků clusteru Service Fabric:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Šifrování hodnoty tajných kódů balíčku Service Fabric

Běžné hodnoty, které jsou šifrované v balíčky Service Fabric zahrnují přihlašovací údaje služby Azure Container Registry (ACR), proměnné prostředí, nastavení a klíče účtu úložiště Azure svazku modulu plug-in.

K [nastavit šifrovací certifikát a šifrování tajných kódů v clusterech Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Vygenerujte certifikát podepsaný svým držitelem pro šifrování váš tajný klíč:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Postupujte podle pokynů v [certifikáty nasadit služby Key Vault do virtuálního počítače clusteru Service Fabric škálovací sady](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) nasazení Key Vault Certificates do clusteru služby Service Fabric Virtual Machine Scale Sets.

Šifrování váš tajný klíč, pomocí následujícího příkazu prostředí PowerShell a pak aktualizujte manifest aplikace Service Fabric s zašifrovanou hodnotu:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

K [nastavit šifrovací certifikát a šifrování tajných kódů na clusterech s Linuxem](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Vygenerujte certifikát podepsaný svým držitelem pro šifrování tajných klíčů:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Postupujte podle pokynů v [certifikáty nasadit služby Key Vault do virtuálního počítače clusteru Service Fabric škálovací sady](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) do clusteru služby Service Fabric Virtual Machine Scale Sets.

Šifrování váš tajný klíč, pomocí následujících příkazů a pak aktualizujte vaše Manifest aplikace Service Fabric s zašifrovanou hodnotu:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Po šifrování vašich chráněných hodnoty [zadejte šifrované tajné klíče v aplikaci Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application), a [dešifrovat šifrované tajné klíče z kódu služby](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Ověření aplikace Service Fabric k prostředkům Azure pomocí Identity spravované služby (MSI)

Další informace o spravovaných identit pro prostředky Azure, najdete v článku [co je spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-it-work).
Clustery Azure Service Fabric hostovaných na Škálovací sady virtuálních počítačů, které podporují [identita spravované služby](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Chcete-li získat seznam služeb tohoto MSI lze použít k ověření, naleznete v tématu [služeb Azure, které podporují ověřování Azure Active Directory](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Chcete-li povolit při vytváření škálovací sady virtuálních počítačů nebo existující škálovací sadě virtuálních počítačů spravovaných identitu přiřazenou systémem, deklarujte následující `"Microsoft.Compute/virtualMachinesScaleSets"` vlastnost:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Zobrazit [co je spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity) Další informace.

Pokud jste vytvořili [uživatelsky přiřazené identity spravované](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), deklarujte následující prostředek do šablony ji přiřadit do škálovací sady virtuálních počítačů. Nahraďte `\<USERASSIGNEDIDENTITYNAME\>` názvem uživatel přiřazenou spravované identity, které jste vytvořili:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Před vaše Service Fabric aplikace můžete provádět pomocí spravované identity, musí být udělena oprávnění, pro prostředky Azure, které potřebuje k ověření pomocí.
Následující příkazy udělit přístup k prostředku Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

V kódu aplikace Service Fabric získáte přístupový token pro Azure Resource Manager tím, že všechny podobné následujícímu REST:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Aplikace Service Fabric pak můžete pomocí tokenu přístupu mohli ověřovat prostředky Azure, které podporují služby Active Directory.
Následující příklad ukazuje, jak to provést u prostředku Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```

## <a name="windows-defender"></a>Windows Defender 

Ve výchozím nastavení je nainstalovaný antivirový program Windows Defender ve Windows serveru 2016. Podrobnosti najdete v tématu [antivirové ochrany Windows Defender ve Windows serveru 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Uživatelské rozhraní je nainstalované ve výchozím nastavení na některých jednotkách SKU, ale není potřeba. Ke snížení výkonu žádné dopad a prostředků spotřeby režii vzniklé v programu Windows Defender a pokud vaše zásady zabezpečení umožňují vyloučit procesy a cesty pro open source softwaru, deklarujte následující prostředek virtuálního počítače Škálovací nastavit rozšíření Vlastnosti šablony správce vyloučit z kontrol vašeho clusteru Service Fabric:


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
> V dokumentaci antimalwaru pro konfiguraci pravidla, pokud nepoužíváte Windows Defender. Program Windows Defender není v Linuxu podporováno.

## <a name="next-steps"></a>Další postup

* Vytvoření clusteru na virtuální počítače nebo počítače s Windows serverem: [Vytvoření clusteru Service Fabric pro Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Vytvoření clusteru na virtuální počítače nebo počítače s Linuxem: [Vytvoření clusteru s Linuxem](service-fabric-cluster-creation-via-portal.md).
* Další informace o [možnosti podpory pro Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png