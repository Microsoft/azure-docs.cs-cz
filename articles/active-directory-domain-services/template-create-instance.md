---
title: Povolení služby Azure DS Domain Services pomocí šablony | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat a povolit službu Azure Active Directory Domain Services pomocí šablony Azure Resource Manageru
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: 2daadb539bc08df37f15c187866b735e45309288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612795"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Vytvoření spravované domény služby Azure Active Directory Domain Services pomocí šablony Správce prostředků Azure

Služba Azure Active Directory Domain Services (Azure AD DS) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, ověřování LDAP, Kerberos/NTLM, které je plně kompatibilní se službou Active Directory systému Windows Server. Tyto služby domény spotřebováváte bez nutnosti nasazovat, spravovat a opravovat řadiče domény sami. Azure AD DS integruje s vaším stávajícím tenantem Azure AD. Tato integrace umožňuje uživatelům přihlásit pomocí jejich podnikových přihlašovacích údajů a můžete použít existující skupiny a uživatelské účty k zabezpečení přístupu k prostředkům.

Tento článek ukazuje, jak povolit Azure AD DS pomocí šablony Azure Resource Manager. Podpůrné prostředky se vytvářejí pomocí Azure PowerShellu.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento článek dokončit, potřebujete následující zdroje:

* Instalace a konfigurace Azure Powershellu.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure PowerShell a připojte se k předplatnému Azure](/powershell/azure/install-az-ps).
    * Ujistěte se, že se přihlásíte k předplatnému Azure pomocí rutiny [Connect-AzAccount.][Connect-AzAccount]
* Nainstalujte a nakonfigurujte Azure AD PowerShell.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure AD PowerShell a připojte se k Azure AD](/powershell/azure/active-directory/install-adv2).
    * Ujistěte se, že se přihlásíte ke svému tenantovi Azure AD pomocí rutiny [Connect-AzureAD.][Connect-AzureAD]
* K povolení služby Azure AD DS potřebujete oprávnění *globálního správce* ve vašem tenantovi Azure AD.
* K vytvoření požadovaných prostředků Azure AD DS potřebujete oprávnění *přispěvatele* ve vašem předplatném Azure.

## <a name="dns-naming-requirements"></a>Požadavky na pojmenování DNS

Při vytváření instance Azure AD DS zadáte název DNS. Při výběru tohoto názvu DNS jsou některé důležité:

* **Vestavěné doménové jméno:** Ve výchozím nastavení se používá vestavěný název domény adresáře (přípona *.onmicrosoft.com).* Chcete-li povolit zabezpečený přístup LDAP ke spravované doméně přes Internet, nelze vytvořit digitální certifikát pro zabezpečení připojení k této výchozí doméně. Společnost Microsoft vlastní doménu *.onmicrosoft.com,* takže certifikační autorita (CA) certifikát nevystaví.
* **Vlastní názvy domén:** Nejběžnějším přístupem je zadání vlastního názvu domény, obvykle takového, který již vlastníte a je směrovatelný. Při použití směrovatelné, vlastní domény, provoz může správně toku podle potřeby pro podporu aplikací.
* **Nesměrovatelné přípony domény:** Obecně doporučujeme vyhnout se nesměrovatelné příponě názvu domény, například *contoso.local*. Přípona *LOCAL* není směrovatelná a může způsobit problémy s rozlišením DNS.

> [!TIP]
> Pokud vytvoříte vlastní název domény, dbát na existující obory názvů DNS. Doporučujeme používat název domény oddělený od existujícího oboru názvů Azure nebo místního webu DNS.
>
> Pokud například máte existující obor názvů DNS *contoso.com*, vytvořte spravovanou doménu Služby Azure AD DS s vlastním názvem domény *aaddscontoso.com*. Pokud potřebujete použít zabezpečené protokol LDAP, musíte se zaregistrovat a vlastnit tento vlastní název domény, abyste vygenerovali požadované certifikáty.
>
> Možná budete muset vytvořit další záznamy DNS pro jiné služby ve vašem prostředí nebo podmíněné servery pro předávání DNS mezi existujícími obory názvů DNS ve vašem prostředí. Pokud například spustíte webový server, který hostuje web pomocí kořenového názvu DNS, mohou existovat konflikty názvů, které vyžadují další položky DNS.
>
> V těchto kurzech a články s postupy vlastní doména *aaddscontoso.com* se používá jako krátký příklad. Ve všech příkazech zadejte vlastní název domény.

Platí také následující omezení názvů DNS:

* **Omezení předpony domény:** Spravovanou doménu s předponou delší než 15 znaků nelze vytvořit. Předpona zadaného názvu domény (například *aaddscontoso* v *aaddscontoso.com* názvu domény) musí obsahovat 15 nebo méně znaků.
* **Konflikty názvů sítí:** Název domény DNS pro spravovanou doménu by už neměl ve virtuální síti existovat. Konkrétně zkontrolujte následující scénáře, které by vedly ke konfliktu názvů:
    * Pokud už máte doménu služby Active Directory se stejným názvem domény DNS ve virtuální síti Azure.
    * Pokud virtuální síť, ve které plánujete povolit spravovanou doménu, má připojení VPN k místní síti. V tomto scénáři ujistěte se, že nemáte doménu se stejným názvem domény DNS v místní síti.
    * Pokud máte existující cloudovou službu Azure s tímto názvem ve virtuální síti Azure.

## <a name="create-required-azure-ad-resources"></a>Vytvoření požadovaných prostředků Azure AD

Azure AD DS vyžaduje instanční objekt a skupinu Azure AD. Tyto prostředky umožňují spravované doméně Azure AD DS synchronizovat data a definovat, kteří uživatelé mají oprávnění správce ve spravované doméně.

Nejprve zaregistrujte zprostředkovatele prostředků služby Azure AD Domain Services pomocí rutiny [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Vytvořte instanční objekt služby Azure AD pomocí rutiny [New-AzureADServicePrincipal][New-AzureADServicePrincipal] pro Azure AD DS ke komunikaci a ověření sám. Konkrétní ID aplikace se používá s názvem *Služby řadiče domény* s ID *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Toto ID aplikace neměňte.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Teď vytvořte skupinu Azure AD s názvem *Správci řadiče domény AAD* pomocí rutiny [New-AzureADGroup.][New-AzureADGroup] Uživatelům přidaným do této skupiny jsou pak udělena oprávnění k provádění úloh správy ve spravované doméně Azure AD DS.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Při vytvoření *skupiny AAD DC Administrators* přidejte uživatele do skupiny pomocí rutiny [Add-AzureADGroupMember.][Add-AzureADGroupMember] Nejprve získáte ID *skupiny skupiny AAD DC Administrators* pomocí rutiny [Get-AzureADGroup,][Get-AzureADGroup] potom id objektu požadovaného uživatele pomocí rutiny [Get-AzureADUser.][Get-AzureADUser]

V následujícím příkladu id objektu uživatele pro účet `admin@aaddscontoso.onmicrosoft.com`s hlavní název uživatele . Nahraďte tento uživatelský účet hlavní skupinou uživatele, kterou chcete přidat do *skupiny Správci řadiče domény AAD:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Nakonec vytvořte skupinu prostředků pomocí rutiny [New-AzResourceGroup.][New-AzResourceGroup] V následujícím příkladu je skupina prostředků s názvem *myResourceGroup* a je vytvořena v oblasti *westus.* Použijte své vlastní jméno a požadovanou oblast:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Pokud zvolíte oblast, která podporuje zóny dostupnosti, prostředky Azure AD DS jsou distribuovány napříč zónami pro další redundanci. Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby byla zajištěna odolnost proti chybám, jsou ve všech povolených oblastech minimálně tři samostatné zóny.

Není nic pro vás nakonfigurovat pro Azure AD DS distribuovat napříč zónami. Platforma Azure automaticky zpracovává zónovou distribuci prostředků. Další informace a informace o dostupnosti oblasti najdete v tématu [Co jsou zóny dostupnosti v Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Definice prostředků pro Azure AD DS

V rámci definice prostředků Správce prostředků jsou požadovány následující parametry konfigurace:

| Parametr               | Hodnota |
|-------------------------|---------|
| Název_domény              | Název domény DNS pro spravovanou doménu s přihlédnutím k předchozím bodům při pojmenování předpon a konfliktů. |
| filteredSync            | Azure AD DS umožňuje synchronizovat *všechny* uživatele a skupiny dostupné ve službě Azure AD nebo synchronizaci s *vymezeným oborem* pouze konkrétní skupiny. Pokud se rozhodnete synchronizovat všechny uživatele a skupiny, nemůžete později zvolit pouze synchronizaci s vymezeným oborem.<br /> Další informace o synchronizaci s vymezeným oborem naleznete v tématu [Synchronizace s oborem služby Azure AD Domain Services][scoped-sync].|
| notificationSettings    | Pokud jsou nějaké výstrahy generované ve spravované doméně Azure AD DS, e-mailová oznámení můžete odeslat. <br />*Globální správci* tenanta Azure a členové *skupiny Správci řadiče domény AAD* mohou být *povoleny* pro tato oznámení.<br /> V případě potřeby můžete přidat další příjemce pro oznámení, pokud existují výstrahy, které vyžadují pozornost.|
| domainConfigurationType | Ve výchozím nastavení se spravovaná doména Azure AD DS vytvoří jako doménová struktura *uživatele.* Tento typ doménové struktury synchronizuje všechny objekty ze služby Azure AD, včetně všech uživatelských účtů vytvořených v místním prostředí služby AD DS. K vytvoření doménové struktury není nutné zadávat hodnotu *domainConfiguration.*<br /> Doménová struktura *prostředků* synchronizuje jenom uživatele a skupiny vytvořené přímo ve službě Azure AD. Doménové struktury prostředků jsou aktuálně ve verzi Preview. Chcete-li vytvořit doménovou strukturu prostředků, nastavte hodnotu na *ResourceTrusting.*<br />Další informace o doménových strukturách *prostředků,* včetně toho, proč můžete použít jeden a jak vytvořit vztahy důvěryhodnosti doménové struktury s místními doménami služby AD DS, najdete v [tématu Přehled doménových struktur prostředků Služby Azure AD DS][resource-forests].|

Následující definice zhuštěných parametrů ukazuje, jak jsou tyto hodnoty deklarovány. Doménová struktura uživatele s názvem *aaddscontoso.com* se vytvoří se všemi uživateli z Azure AD synchronizované do spravované domény Azure AD DS:

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

Následující typ prostředku šablony zhuštěné Resource Manager se pak používá k definování a vytvoření spravované domény Azure AD DS. Virtuální síť Azure a podsíť už musí existovat nebo být vytvořeny jako součást šablony Správce prostředků. Spravovaná doména Azure AD DS je připojená k této podsíti.

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

Tyto parametry a typ prostředku lze použít jako součást širší šablony Správce prostředků k nasazení spravované domény, jak je znázorněno v následující části.

## <a name="create-a-managed-domain-using-sample-template"></a>Vytvoření spravované domény pomocí ukázkové šablony

Následující kompletní ukázková šablona Správce prostředků vytvoří spravovanou doménu Azure AD DS a podpůrná pravidla skupiny zabezpečení virtuální sítě, podsítě a sítě. K zabezpečení spravované domény a zajištění správného toku přenosu jsou vyžadována pravidla skupiny zabezpečení sítě. Vytvoří se doménová struktura uživatele s názvem DNS *aaddscontoso.com* se všemi uživateli synchronizovanými ze služby Azure AD:

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

Tuto šablonu lze nasadit pomocí upřednostňované metody nasazení, jako je [například portál Azure][portal-deploy]Portal , [Azure PowerShell][powershell-deploy]nebo kanál CI/CD. Následující příklad používá rutinu [New-AzResourceGroupDeployment.][New-AzResourceGroupDeployment] Zadejte vlastní název skupiny prostředků a název souboru šablony:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Vytvoření prostředku a vrácení ovládacího prvku do výzvy prostředí PowerShell trvá několik minut. Spravovaná doména Azure AD DS se nadále zřaží na pozadí a dokončení nasazení může trvat až hodinu. Na webu Azure Portal stránka **Přehled** pro spravovanou doménu Azure AD DS zobrazuje aktuální stav v této fázi nasazení.

Když portál Azure ukazuje, že spravovaná doména Azure AD DS dokončila zřizování, je třeba dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Chcete-li nakonfigurovat službu DNS, vyberte na portálu spravovanou doménu Azure AD DS. V okně **Přehled** budete vyzváni k automatické konfiguraci těchto nastavení DNS.
* [Povolte synchronizaci hesel do služby Azure AD Domain Services,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit spravovanou doménu Azure AD DS v akci, můžete [se připojit k virtuálnímu počítači windows][windows-join], [nakonfigurovat zabezpečené LDAP][tutorial-ldaps]a [nakonfigurovat synchronizaci hash hesel][tutorial-phs].

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
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
