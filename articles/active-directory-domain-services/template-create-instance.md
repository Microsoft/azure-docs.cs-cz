---
title: Povolení služby Azure DS Domain Services pomocí šablony | Microsoft Docs
description: Informace o tom, jak nakonfigurovat a povolit Azure Active Directory Domain Services pomocí šablony Azure Resource Manager
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e18825da64d0d200f55ce72985ac843b93b1e612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618786"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Vytvoření spravované domény Azure Active Directory Domain Services pomocí šablony Azure Resource Manager

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP, ověřování Kerberos/NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory. Tyto doménové služby spotřebujete bez nutnosti nasazovat, spravovat a opravovat řadiče domény sami. Služba Azure služba AD DS se integruje s vaším stávajícím tenant Azure AD. Tato integrace umožňuje uživatelům přihlásit se pomocí svých podnikových přihlašovacích údajů a pomocí existujících skupin a uživatelských účtů můžete zabezpečit přístup k prostředkům.

V tomto článku se dozvíte, jak vytvořit spravovanou doménu pomocí šablony Azure Resource Manager. Podpůrné prostředky se vytvářejí pomocí Azure PowerShell.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete tyto prostředky:

* Instalace a konfigurace Azure Powershellu.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure PowerShell a připojte se k předplatnému Azure](/powershell/azure/install-az-ps).
    * Ujistěte se, že se ke svému předplatnému Azure přihlašujete pomocí rutiny [Connect-AzAccount][Connect-AzAccount] .
* Nainstalujte a nakonfigurujte Azure AD PowerShell.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure AD PowerShell a připojte se ke službě Azure AD](/powershell/azure/active-directory/install-adv2).
    * Pomocí rutiny [Connect-AzureAD][Connect-AzureAD] se ujistěte, že se přihlašujete k TENANTOVI Azure AD.
* Abyste mohli Azure služba AD DS povolit, potřebujete ve svém tenantovi Azure AD oprávnění *globálního správce* .
* Abyste mohli vytvořit požadované prostředky Azure služba AD DS, potřebujete oprávnění *přispěvatele* v předplatném Azure.

## <a name="dns-naming-requirements"></a>Požadavky na názvy DNS

Při vytváření spravované domény Azure služba AD DS zadáte název DNS. Při volbě tohoto názvu DNS máte nějaké okolnosti:

* **Název předdefinované domény:** Ve výchozím nastavení se používá integrovaný název domény adresáře (přípona *. onmicrosoft.com* ). Pokud chcete povolit přístup přes Internet k spravované doméně pomocí protokolu Secure LDAP, nemůžete vytvořit digitální certifikát pro zabezpečení připojení s touto výchozí doménou. Společnost Microsoft vlastní doménu *. onmicrosoft.com* , takže certifikační autorita (CA) certifikát nevydá.
* **Vlastní názvy domén:** Nejběžnějším přístupem je zadat vlastní název domény, obvykle ten, který už vlastníte a který je směrovatelný. Když použijete směrovatelný, vlastní doménu, může provoz správně přesměrovat podle potřeby na podporu vašich aplikací.
* **Přípony domén bez směrování:** Obecně doporučujeme, abyste se vyhnuli příponě názvu domény, která není směrovatelný, například *contoso. Local*. Přípona *. Local* není směrovatelný a může způsobit problémy s překladem názvů DNS.

> [!TIP]
> Pokud vytváříte vlastní název domény, je třeba dbát na stávající obory názvů DNS. Doporučuje se použít název domény oddělený od existujícího prostoru názvů Azure nebo místního DNS.
>
> Pokud máte například existující obor názvů DNS *contoso.com*, vytvořte spravovanou doménu s vlastním názvem domény *aaddscontoso.com*. Pokud potřebujete použít zabezpečený protokol LDAP, musíte tento vlastní název domény pro vygenerování požadovaných certifikátů zaregistrovat a vlastnit.
>
> Možná budete muset vytvořit některé další záznamy DNS pro další služby ve vašem prostředí nebo podmíněné služby DNS pro přeposílání mezi stávajícími obory názvů DNS ve vašem prostředí. Pokud například spustíte webový server, který je hostitelem lokality pomocí kořenového názvu DNS, může dojít ke konfliktům názvů, které vyžadují další položky DNS.
>
> V této ukázce a v článcích s návody se vlastní doména *aaddscontoso.com* používá jako krátký příklad. Ve všech příkazech zadejte vlastní název domény.

Platí taky následující omezení názvů DNS:

* **Omezení prefixu domény:** Nelze vytvořit spravovanou doménu s předponou delší než 15 znaků. Předpona zadaného názvu domény (například *aaddscontoso* v názvu domény *aaddscontoso.com* ) musí obsahovat nejvýše 15 znaků.
* **Konflikty síťových názvů:** Název domény DNS pro spravovanou doménu už ve virtuální síti neexistuje. Konkrétně se podívejte na následující scénáře, které by mohly vést ke konfliktu názvů:
    * Pokud již máte doménu služby Active Directory se stejným názvem domény DNS ve službě Azure Virtual Network.
    * Pokud má virtuální síť, ve které plánujete povolit spravovanou doménu, připojení VPN s vaší místní sítí. V tomto scénáři se ujistěte, že nemáte doménu se stejným názvem domény DNS ve vaší místní síti.
    * Pokud máte existující cloudovou službu Azure s tímto názvem ve službě Azure Virtual Network.

## <a name="create-required-azure-ad-resources"></a>Vytvoření požadovaných prostředků Azure AD

Služba Azure služba AD DS vyžaduje instanční objekt a skupinu Azure AD. Tyto prostředky umožňují, aby spravovaná doména synchronizoval data, a definovali, kteří uživatelé mají oprávnění správce ve spravované doméně.

Nejdřív Zaregistrujte poskytovatele prostředků Azure AD Domain Services pomocí rutiny [Register-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Vytvořte instanční objekt služby Azure AD pomocí rutiny [New-AzureADServicePrincipal][New-AzureADServicePrincipal] pro Azure služba AD DS, abyste mohli komunikovat a ověřovat sám sebe. Konkrétní ID aplikace se používá s názvem *služby řadiče domény* s ID *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Neměňte toto ID aplikace.

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

Nyní pomocí rutiny [New-AzureADGroup][New-AzureADGroup] vytvořte skupinu Azure AD s názvem *AAD DC Administrators* . Uživatelům přidaným do této skupiny se pak udělí oprávnění k provádění úloh správy ve spravované doméně.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Když je vytvořená skupina *AAD DC Administrators* , přidejte uživatele do skupiny pomocí rutiny [Add-AzureADGroupMember][Add-AzureADGroupMember] . Nejprve pomocí rutiny [Get-][Get-AzureADUser] [AzureADGroup][Get-AzureADGroup] Získejte ID objektu skupiny *AAD DC Administrators* a pak ID objektu požadovaného uživatele.

V následujícím příkladu je ID objektu uživatele pro účet s hlavním názvem uživatele (UPN) `admin@contoso.onmicrosoft.com` . Nahraďte tento uživatelský účet uživatelským jménem uživatele, kterého chcete přidat do skupiny *správců řadiče domény AAD* :

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Nakonec vytvořte skupinu prostředků pomocí rutiny [New-AzResourceGroup][New-AzResourceGroup] . V následujícím příkladu má skupina prostředků název *myResourceGroup* a je vytvořená v oblasti *westus* . Použijte svůj vlastní název a požadovanou oblast:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Pokud zvolíte oblast, která podporuje Zóny dostupnosti, prostředky Azure služba AD DS se rozdělují mezi zóny, aby se mohla zvýšit redundance. Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Kvůli odolnosti ve všech aktivovaných oblastech existují minimálně tři samostatné zóny.

Není tu nic, co byste mohli nakonfigurovat pro Azure služba AD DS k distribuci mezi zónami. Platforma Azure automaticky zpracovává distribuci prostředků v zóně. Další informace a informace o dostupnosti oblastí najdete v tématu [co jsou zóny dostupnosti v Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Definice prostředků pro Azure služba AD DS

V rámci definice prostředků Správce prostředků jsou potřeba následující konfigurační parametry:

| Parametr               | Hodnota |
|-------------------------|---------|
| domainName              | Název domény DNS pro spravovanou doménu, který bere v úvahu předchozí body při pojmenovávání předpon a konfliktů. |
| filteredSync            | Azure služba AD DS umožňuje synchronizovat *všechny* uživatele a skupiny, které jsou dostupné ve službě Azure AD, nebo jenom *vymezenou* synchronizaci jenom konkrétních skupin.<br /><br /> Další informace o vymezené synchronizaci najdete v tématu [Azure AD Domain Services s vymezeným rozsahem synchronizace][scoped-sync].|
| notificationSettings    | Pokud se ve spravované doméně generují nějaké výstrahy, můžou se e-mailová oznámení poslat. <br /><br />U těchto oznámení je možné *Povolit* *globální správce* tenanta Azure a členové skupiny *Správci AAD DC* .<br /><br /> V případě potřeby můžete přidat další příjemce pro oznámení, pokud se zobrazí výstrahy, které vyžadují pozornost.|
| domainConfigurationType | Ve výchozím nastavení je spravovaná doména vytvořena jako doménová struktura *uživatelů* . Tento typ doménové struktury synchronizuje všechny objekty z Azure AD, včetně všech uživatelských účtů vytvořených v místním služba AD DS prostředí. Pro vytvoření doménové struktury uživatele není nutné zadávat hodnotu *domainConfiguration* .<br /><br /> Doménová struktura *prostředků* synchronizuje jenom uživatele a skupiny vytvořené přímo ve službě Azure AD. Nastavte hodnotu na *ResourceTrusting* , aby se vytvořila doménová struktura prostředků.<br /><br />Další informace o doménových strukturách *prostředků* , včetně důvodů, proč je můžete použít a jak vytvořit vztahy důvěryhodnosti doménové struktury s místními služba AD DS doménami, najdete v tématu [Přehled doménových struktur Azure služba AD DS][resource-forests].|

Následující definice zhuštěných parametrů ukazuje, jak jsou tyto hodnoty deklarovány. Uživatelská doménová struktura s názvem *aaddscontoso.com* se vytvoří se všemi uživateli z Azure AD synchronizovanými do spravované domény:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Následující typ prostředku šablony Správce prostředků slouží k definování a vytvoření spravované domény. Virtuální síť Azure a podsíť už musí existovat nebo být vytvořené jako součást šablony Správce prostředků. Spravovaná doména je připojena k této podsíti.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Tyto parametry a typ prostředku lze použít jako součást širší Správce prostředků šablony pro nasazení spravované domény, jak je znázorněno v následující části.

## <a name="create-a-managed-domain-using-sample-template"></a>Vytvoření spravované domény pomocí ukázkové šablony

Následující ukázková šablona Správce prostředků vytvoří spravovanou doménu a podporuje pravidla skupiny zabezpečení virtuální sítě, podsítě a sítě. K zabezpečení spravované domény jsou nutná pravidla skupiny zabezpečení sítě a zajišťují, že provoz může správně Flow. Vytvoří se doménová struktura uživatele s názvem DNS *aaddscontoso.com* se všemi uživateli synchronizovanými z Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Tuto šablonu můžete nasadit pomocí upřednostňované metody nasazení, jako je [Azure Portal][portal-deploy], [Azure POWERSHELL][powershell-deploy]nebo kanál CI/CD. V následujícím příkladu je použita rutina [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] . Zadejte název vlastní skupiny prostředků a název souboru šablony:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Vytvoření prostředku a vrácení řízení do příkazového řádku PowerShellu trvá několik minut. Spravovaná doména je nadále zřízena na pozadí a může trvat až hodinu, než se nasazení dokončí. V Azure Portal zobrazuje stránka **Přehled** pro spravovanou doménu aktuální stav v rámci této fáze nasazení.

Když Azure Portal ukáže, že se dokončilo zřizování spravované domény, musí se dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Pokud chcete nakonfigurovat DNS, vyberte spravovanou doménu na portálu. V okně **Přehled** se zobrazí výzva k automatické konfiguraci těchto nastavení DNS.
* [Povolte synchronizaci hesel do Azure služba AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) tak, aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="next-steps"></a>Další kroky

Pokud chcete spravovanou doménu zobrazit v akci, můžete se [připojit k virtuálnímu počítači s Windows][windows-join], [nakonfigurovat zabezpečený protokol LDAP][tutorial-ldaps]a [nakonfigurovat synchronizaci hodnot hash hesel][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment