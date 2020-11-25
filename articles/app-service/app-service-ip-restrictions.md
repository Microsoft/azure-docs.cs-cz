---
title: Omezení přístupu Azure App Service
description: Naučte se zabezpečit aplikaci v Azure App Service nastavením omezení přístupu.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010176"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Nastavení omezení přístupu Azure App Service

Nastavením omezení přístupu můžete definovat seznam povolených a zakázaných priorit, který řídí síťový přístup k vaší aplikaci. Seznam může zahrnovat IP adresy nebo podsítě Azure Virtual Network. Pokud existuje aspoň jedna položka, na konci seznamu se nachází implicitní *zamítnutí* .

Funkce omezení přístupu funguje se všemi úlohami hostovanými v Azure App Service. Úlohy můžou zahrnovat webové aplikace, aplikace API, aplikace pro Linux, aplikace pro kontejner pro Linux a funkce.

Po podání žádosti do vaší aplikace se adresa z vyhodnotí na základě pravidel IP adres v seznamu omezení přístupu. Pokud je adresa v podsíti, která je nakonfigurovaná s koncovými body služby na Microsoft. Web, zdrojová podsíť se porovná s pravidly virtuální sítě v seznamu omezení přístupu. Pokud adresa nepovoluje přístup na základě pravidel v seznamu, služba odpoví pomocí kódu stavu [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

Funkce omezení přístupu je implementována v App Service rolí front-end, které jsou v nadřazeném hostiteli pracovními procesy, kde je spuštěn kód. Proto omezení přístupu jsou efektivními seznamy řízení přístupu k síti (ACL).

Možnost omezit přístup k vaší webové aplikaci z Azure Virtual Network je povolená u [koncových bodů služby][serviceendpoints]. Pomocí koncových bodů služby můžete omezit přístup k víceklientské službě z vybraných podsítí. Nefunguje, aby se omezil provoz na aplikace, které jsou hostované ve App Service Environment. Pokud jste v App Service Environment, můžete řídit přístup k aplikaci tím, že použijete pravidla IP adresy.

> [!NOTE]
> Koncové body služby musí být povolené jak na straně sítě, tak i ve službě Azure, se kterou jsou povolené. Seznam služeb Azure, které podporují koncové body služby, najdete v tématu [Virtual Network koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md).
>

![Diagram toku omezení přístupu](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>Přidání nebo úprava pravidel omezení přístupu na portálu

Pokud chcete do aplikace přidat pravidlo pro omezení přístupu, postupujte takto:

1. Přihlaste se k webu Azure Portal.

1. V levém podokně vyberte **sítě**.

1. V podokně **síť** v části **omezení přístupu** vyberte **konfigurovat omezení přístupu**.

   ![Snímek obrazovky podokna možností App Service sítě v Azure Portal.](media/app-service-ip-restrictions/access-restrictions.png)  

1. Na stránce **omezení přístupu** si Projděte seznam pravidel omezení přístupu, která jsou definována pro vaši aplikaci.

   ![Snímek obrazovky se stránkou omezení přístupu v Azure Portal zobrazující seznam pravidel omezení přístupu definovaných pro vybranou aplikaci.](media/app-service-ip-restrictions/access-restrictions-browse.png)

   Seznam obsahuje všechna aktuální omezení, která se aplikují na aplikaci. Pokud máte v aplikaci omezení virtuální sítě, v tabulce se zobrazí, jestli jsou koncové body služby povolené pro Microsoft. Web. Pokud se v aplikaci nedefinují žádná omezení, aplikace je přístupná odkudkoli.  

### <a name="add-an-access-restriction-rule"></a>Přidat pravidlo omezení přístupu

Pokud chcete do aplikace přidat pravidlo pro omezení přístupu, vyberte v podokně **omezení přístupu** možnost **Přidat pravidlo**. Po přidání pravidla se to projeví okamžitě. 

Pravidla se vynutila v pořadí podle priority počínaje nejnižším číslem ve sloupci **Priorita** . Implicitní *Odepřít vše* platí po přidání dokonce jednoho pravidla.

V podokně **Přidat omezení IP adresy** při vytváření pravidla udělejte toto:

1. V části **Akce** vyberte možnost **Povolit** nebo **Odepřít**.  

   ![Snímek obrazovky s podoknem přidat omezení IP adres](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. V případě potřeby zadejte název a popis pravidla.  
1. V rozevíracím seznamu **typ** vyberte typ pravidla.  
1. Do pole **Priorita** zadejte hodnotu priority.  
1. V rozevíracích seznamech **odběr**, **Virtual Network** a **podsíť** vyberte, ke kterým chcete omezit přístup.  

### <a name="set-an-ip-address-based-rule"></a>Nastavení pravidla na základě IP adresy

Postupujte podle pokynů uvedených v předchozí části, ale s následující variací:
* V kroku 3 v rozevíracím seznamu **typ** vyberte **IPv4** nebo **IPv6**. 

Zadejte IP adresu v zápisu CIDR (Classless Inter-Domain Routing) pro adresy IPv4 i IPv6. Pokud chcete zadat adresu, můžete použít něco jako *1.2.3.4/32*, kde první čtyři oktety reprezentují vaši IP adresu a */32* je maska. Zápis IPv4 protokolu IPv4 pro všechny adresy je 0.0.0.0/0. Další informace o zápisu CIDR najdete v tématu [směrování Inter-Domain bez třídy](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="use-service-endpoints"></a>Použití koncových bodů služby

Pomocí koncových bodů služby můžete omezit přístup k vybraným podsítím virtuální sítě Azure. Chcete-li omezit přístup k určité podsíti, vytvořte pravidlo omezení s typem **Virtual Network** . Pak můžete vybrat předplatné, virtuální síť a podsíť, pro které chcete povolit nebo odepřít přístup. 

Pokud koncové body služby již nejsou povoleny s Microsoft. Web pro vybranou podsíť, budou automaticky povoleny, pokud nevyberete zaškrtávací políčko **ignorovat chybějící koncové body Microsoft. Web Service** . Scénář, ve kterém možná budete chtít povolit koncové body služby, ale ne podsíť, závisí hlavně na tom, jestli máte oprávnění k jejich povolení v podsíti. 

Pokud k povolení koncových bodů služby v podsíti potřebujete někoho jiného, zaškrtněte políčko **Ignorovat koncové body Microsoft. Web Service** . Vaše aplikace se nakonfiguruje pro koncové body služby při předvídání, že jsou povolené později v podsíti. 

![Snímek obrazovky s vybraným typem Virtual Network v podokně Přidat omezení IP adres](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Koncové body služby nemůžete použít k omezení přístupu k aplikacím, které běží v App Service Environment. Když je vaše aplikace v App Service Environment, můžete k ní řídit přístup použitím pravidel přístupu IP. 

Pomocí koncových bodů služby můžete nakonfigurovat aplikaci pomocí aplikačních bran nebo jiných zařízení WAF (Web Application firewall). Můžete také konfigurovat vícevrstvé aplikace s využitím zabezpečených back-endy. Další informace najdete v tématech [síťové funkce a App Service](networking-features.md) a [Application Gateway integraci s koncovými body služby](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - Koncové body služby se aktuálně nepodporují u webových aplikací, které používají virtuální IP adresu IP SSL (Secure Sockets Layer) (VIP).
> - Omezení počtu řádků na úrovni protokolu IP nebo Service-Endpoint může být 512. Pokud požadujete více než 512 řádků omezení, doporučujeme zvážit instalaci samostatného produktu zabezpečení, jako jsou například přední vrátka Azure, Azure App Gateway nebo WAF.
>

## <a name="manage-access-restriction-rules"></a>Správa pravidel omezení přístupu

Existující pravidlo omezení přístupu můžete upravit nebo odstranit.

### <a name="edit-a-rule"></a>Upravit pravidlo

1. Pokud chcete začít upravovat stávající pravidlo omezení přístupu, na stránce **omezení přístupu** poklikejte na pravidlo, které chcete upravit.

1. V podokně **Upravit omezení IP adres** proveďte požadované změny a pak vyberte **aktualizovat pravidlo**. Úpravy jsou okamžitě platné, včetně změn v pořadí podle priority.

   ![Snímek obrazovky s podoknem Upravit omezení IP adresy v Azure Portal zobrazující pole pro existující pravidlo omezení přístupu](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > Když upravujete pravidlo, nemůžete přepínat mezi pravidlem IP adresy a pravidlem virtuální sítě. 

   ![Snímek obrazovky s podoknem Upravit omezení IP adresy v Azure Portal, ve kterém se zobrazuje nastavení pro pravidlo virtuální sítě](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>Odstranění pravidla

Pokud chcete pravidlo odstranit, na stránce **omezení přístupu** vyberte tři tečky (**...**) vedle pravidla, které chcete odstranit, a pak vyberte **Odebrat**.

![Snímek obrazovky se stránkou omezení přístupu, kde se zobrazují tři tečky Remove vedle pravidla omezení přístupu, které se má odstranit.](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>Blokovat jednu IP adresu

Když přidáte první pravidlo omezení IP adres, služba přidá explicitní pravidlo *Odepřít všechna* pravidla s prioritou 2147483647. V praxi je explicitní pravidlo *Odepřít všechna* pravidla, které se má spustit, a blokuje přístup k jakékoli IP adrese, která není explicitně povolená pravidlem *Povolit* .

V případě scénáře, kdy chcete explicitně zablokovat jednu IP adresu nebo blok IP adres, ale umožnit přístup všem ostatním uživatelům, přidejte explicitní pravidlo *povolení všech* .

![Snímek obrazovky se stránkou omezení přístupu v Azure Portal, kde se zobrazuje jedna blokovaná IP adresa.](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>Omezení přístupu k webu SCM 

Kromě toho, že je možné řídit přístup k vaší aplikaci, můžete omezit přístup k webu SCM, který používá vaše aplikace. Web SCM je koncovým bodem nasazení webu a konzolou Kudu. K webu SCM z aplikace můžete přiřadit omezení přístupu samostatně nebo použít stejnou sadu omezení pro aplikaci i web SCM. Když vyberete **stejná omezení jako \<app name>** zaškrtávací políčko, vše je prázdné. Pokud zaškrtnutí políčka zrušíte, vaše nastavení webu SCM se znovu nasadí. 

![Snímek obrazovky se stránkou omezení přístupu v Azure Portal, která ukazuje, že pro web SCM nebo aplikaci nejsou nastavená žádná omezení přístupu.](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>Správa pravidel omezení přístupu programově

Omezení přístupu programově můžete přidat jedním z následujících způsobů: 

* Použijte [rozhraní příkazového řádku Azure](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true). Například:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Použijte [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true). Například:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

Hodnoty můžete také nastavit ručně jedním z následujících způsobů:

* Použijte operaci Put služby [Azure REST API](/rest/api/azure/) pro konfiguraci aplikace v Azure Resource Manager. Umístění pro tyto informace v Azure Resource Manager:

  **ID předplatného** Management.Azure.com/Subscriptions//resourceGroups/**skupiny prostředků**/Providers/Microsoft.Web/Sites/**název webové aplikace**/config/Web? API-Version = 2018-02-01

* Použijte šablonu ARM. Jako příklad můžete použít resources.azure.com a upravit blok ipSecurityRestrictions pro přidání požadovaného formátu JSON.

  Syntaxe JSON pro předchozí příklad je:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```

## <a name="set-up-azure-functions-access-restrictions"></a>Nastavení omezení přístupu Azure Functions

Pro aplikace Function App jsou k dispozici také omezení přístupu se stejnými funkcemi jako App Service plány. Pokud povolíte omezení přístupu, zakážete tím také Azure Portal Editor kódu pro jakékoli nepovolené IP adresy.

## <a name="next-steps"></a>Další kroky
[Omezení přístupu pro Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Integrace Application Gateway s koncovými body služby](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
