---
title: Vytvoření zóny redundantní Azure application gateway
description: Naučte se vytvořit bránu redundantní aplikace zóny, který nevyžaduje seperarte brána je v každé zóny.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937753"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>Vytvoření zóny redundantní Azure application gateway - privátní Preview verzi

Redundantní platformu aplikací brány zóny je nové SKU, nabízí mnoho vylepšení než v existující aplikace brány SKU, včetně:
- **Odolnost proti chybám zóny** – brána nasazení aplikace můžete nyní span několika zón dostupnosti, nemusíte zřizovat a kód pin samostatné Aplikační brána instancí v každé zóně s traffic Manageru. Můžete vybrat zónu jednoho nebo několika zón kde instance brány aplikace nasazena, proto zajistit odolnost selhání zóny. Fondu back-end aplikace mohou být podobně distribuovány na dostupnost zóny.
- **Vylepšení výkonu**
- **Statické VIP** -služby application gateway VIP výhradně teď podporuje typ statické virtuální IP adresy. Tím se zajistí, že virtuální IP adresa spojená s aplikační brány nezmění po restartu.
- **Integrace trezoru klíčů pro certifikáty SSL zákazníka**
- **Rychlejší nasazení a aktualizace**

> [!NOTE]
> Zóny redundantní application gateway je aktuálně v soukromém náhledu. Tato verze preview je k dispozici bez smlouvu o úrovni služeb a se nedoporučuje pro produkční zatížení. Některé funkce nemusí být podporována nebo může mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Podporované oblasti

V oblasti Východ USA 2 jsou aktuálně podporované zóny redundantní application Gateway. Brzy se přidají další oblasti.

## <a name="topologies"></a>Topologie

V aktuální verzi musíte už vytvořit zónu připnutý application Gateway potřebujete oblastmi redundance. Stejné brány nasazení aplikace můžete nyní span několika zón.

Alespoň tři instance jsou nezbytné k zajištění, že jsou rozloženy všechny tři zóny. Aplikační brána rozděluje instance na zóny při přidávání více instancí.

Předchozí zóny redundantní topologie hledá jako na následujícím diagramu:

![Původní redundantní topologie](media/create-zone-redundant/old-redundant.png)

Nové zóny redundantní topologie vypadá tohoto diagramu:

![Nové zóny redundantní topologie](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>Nasazení

### <a name="prerequisites"></a>Požadavky

Aktuálně redundantní schopností zóny je dostupná jenom v privátní Preview verzi. Musí e-mailu appgwxzone@microsoft.com jako seznam povolených adres. Jakmile se zobrazí potvrzení, můžete pokračovat na další kroky. Zahrnout e-mailu pro vytvoření seznamu povolených následující informace:

- ID předplatného
- Název oblasti
- Přibližný počet aplikačních bran požadované

### <a name="resource-manager-template-deployment"></a>Nasazení šablony Resource Manageru

1. Ověřte, zda je odběr, který můžete použít seznam povolených adres popsaná výše.
2. Po přijetí potvrzení, přihlaste se k účtu Azure a vyberte příslušné předplatné, pokud je k dispozici více než jedno předplatné. Ujistěte se, že vyberte odběr, který byl seznam povolených adres.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. Vytvoření nové skupiny prostředků

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. Stahování šablon z [Githubu](https://github.com/amitsriva/CrossZonePreview) a poznamenejte si složku, kam jste uložili.
5. Vytvořte nové nasazení ve skupině prostředků, kterou jste vytvořili. Upravte soubor šablony a parametry správně před nasazením. 

   Následující diagram ukazuje, kde můžete načíst ID klienta na portálu Azure:

   ![ID klienta z portálu.](media/create-zone-redundant/tenant-id.png)

Šablona vytváří v následujících zdrojích informací:

- **Identita uživatele přiřazené** – používá se pro povolení aplikace instance brány pro přístup k trezoru klíčů a načítat certifikáty uložené uživatelem.
- **KeyVault** – Key Vault, ve kterém je uložený certifikát uživatele. To může být také existující Key Vault.
- **Tajný klíč** – privátního klíče, který je uložen v Key Vault.
- **Zásada přístupu** – zásady přístupu použije v Key Vault, která uděluje oprávnění pomocí přiřazené identitu uživatele, aby brána nasazení aplikace můžete načítat certifikáty uživatelů.
- **Veřejná IP adresa** – vyhrazené IP adresu, která se používá pro přístup k službě application gateway. Tato IP adresa změní nikdy pro životní cyklus aplikační brány.
- **Skupin zabezpečení sítě** – na skupiny NSG automaticky vytvořené na podsítě služby application gateway, které se otevře port provoz na nakonfigurované naslouchací proces. To je explicitně vytvořit a spravovat v nové SKU ve srovnání s předchozí verze SKU, kde je tato skupina NSG implicitní.
- **Virtuální síť** – virtuální síť, kde je nasazená Aplikační brána a aplikace.
- **Aplikační brána** – vytvoří aplikační brány s instancemi v požadované zóny. Ve výchozím nastavení jsou vybrány všechny zóny (1,2,3). Název SKU se změní na *Standard_v2*. Tento název SKU se mohou podléhat změnám. Konfigurace automatického škálování má v současné době min a max nastavena na požadovaný počet instancí. Jakmile povolíte automatické škálování to lze upravit.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>Nasazení prostředí PowerShell

1. Zkontrolujte, zda předplatné použité jako výše uvedený v požadavky seznam povolených adres.
2. Stáhněte a nainstalujte privátní MSI prostředí PowerShell z [Githubu](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi).
3. Stáhněte soubor zip privátní prostředí PowerShell z umístění uvedených ve verzi preview registrace potvrzení e-mailu. Rozbalte soubor na disk a poznamenejte si umístění.
4. Jakmile je povoleno ve verzi preview, načte náhled moduly nejprve před přihlásit ke svému účtu:

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Přihlaste se k účtu Azure a vyberte požadované předplatné, pokud je k dispozici více než jedno předplatné. Zkontrolujte, zda že vyberte odpovídající předplatné, který byl seznam povolených adres.
5. Spusťte následující příkazy k vytvoření běžné konstanty, které zahrnují názvy pro většinu entity, které vytváří. 

   Upravte položky podle potřeby pojmenování dle svých představ.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. Vytvořte skupinu prostředků:

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Vytvořte přiřazené Identity uživatele používané pro přístup k službě application gateway k načtení certifikátů z trezoru klíčů.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. Vytvořte Key Vault, používá k ukládání certifikátů:

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. Nahrajte certifikát do Key Vault jako tajný klíč:

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. Přiřazení zásad přístupu do služby Key Vault pomocí přiřazené identitu uživatele. To umožňuje tajný instance brány aplikaci pro přístup k Key Vault:

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. Vytvořte skupinu zabezpečení sítě (NSG) pro povolení přístupu k podsítě služby application gateway na portech, které jsou vytvořeny nové naslouchací procesy.

    Například pro protokol HTTP/HTTPS na výchozí porty NSG umožní příchozí přístup k 80 a 443 a 65200 – 65535. pro operace správy.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. Vytvoření virtuální sítě a podsítě:

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. Vytvoření veřejné IP adresy typu vyhrazená v statických:

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. Vytvoření aplikační brány:

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. Načtení veřejná IP adresa brány vytvořená aplikace:

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

-  Bude možné účtován pro službu application gateway ve verzi preview?

   Verzi Preview není nijak zpoplatněn. Vám budou účtovány poplatky za prostředky než aplikační brány, jako je například Key Vault, virtuální počítače atd.
- Jaké oblasti je dostupná v verzi preview?

   V oblasti Východ USA 2 je aktuálně k dispozici ve verzi preview. Brzy se přidají další oblasti.
- Je podporovaný na portálu ve verzi preview?

   Ne, je podpora omezená na privátní modul prostředí PowerShell a šablony Resource Manageru během privátní Preview verzi.

- Je podporováno produkční zatížení během privátní Preview verzi?

   Ne, neexistuje SLA nebo podporu během privátní Preview verzi. Není doporučeno put úlohy v produkčním prostředí během verze Preview. Podpora je omezena na přímé interakce s produktu skupiny pomocí e-mailový alias pro verzi preview.

- Jak se hlášení problémů?

   Privátní Preview verzi může obsahovat chyby a může mít nasazení časté kódu. Použití aliasu podporu appgwxzone@microsoft.com pro vytváření sestav, problémy a pomoc.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení


|Problém  |Podrobnosti  |
|---------|---------|---------|
|Fakturace     |Žádné fakturace aktuálně|
|Diagnostické protokoly (ne metriky)     |Protokoly požadavků a odpovědí a výkonu nezobrazí aktuálně|
|Portál nebo rozhraní příkazového řádku nebo sady SDK     |Žádná podpora pro portálu, rozhraní příkazového řádku nebo sady SDK. Na portálu se nesmí používat k vydání aktualizace náhled brány.|
|Příležitostné selhání aktualizace prostřednictvím šablony     |Je to z důvodu časování se zásadami přístupu KeyVault|Po vytvoření Key Vault a přiřadit identitu uživatele, může být odebrán z šablony a pouze odkazy na sdílený tajný klíč a identity jsou nezbytné v šabloně.|
|Automatické škálování     |Žádná podpora pro aktuálně automatické škálování|
|WAF     |Aktuálně není podporována firewall webových aplikací|
|Uživatel zadal certifikáty a dynamické virtuální IP adresy     |Tyto nejsou podporovány v novém modelu. Pomocí Key Vault pro ukládání certifikátů a statické virtuální IP adresy.|
|Stejné podsíti pro starý a verze preview aplikační brány     |Podsíť s existující aplikační brána (starý model) nelze použít pro privátní preview verzi.|
|HTTP/2, FIPS režimu, WebSocket, Azure Web Apps jako back-end     |Aktuálně podporované |


## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Pro podporu a zpětnou vazbu, obraťte se na appgwxzone@microsoft.com. Skupina aplikací brány produktu je rádi uslyšíme vaše názory pro vylepšení a obsahují pokyny, pokud je to požadováno.

## <a name="next-steps"></a>Další postup

Další informace o dalších funkcích brány aplikace:

- [Co je Azure Application Gateway?](overview.md)