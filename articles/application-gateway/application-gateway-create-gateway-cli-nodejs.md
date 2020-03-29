---
title: Vytvoření aplikační brány Azure – klasické cli Azure
description: Zjistěte, jak vytvořit aplikační bránu pomocí klasického příkazového příkazového příkazu Azure ve Správci prostředků
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 7107f45253c4f13b3378489726bf5034e104fa30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "62095978"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Vytvoření aplikační brány pomocí nastavení příkazového příkazu Azure

Služba Azure Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7. Poskytuje převzetí služeb při selhání, směrování výkonu požadavků HTTP mezi různými servery, ať už jsou místní nebo v cloudu. Aplikační brána má následující funkce pro doručování aplikací: vyrovnávání zatížení HTTP, spřažení relací na základě souborů cookie a snímání ssl (Secure Sockets Layer), vlastní sondy stavu a podpora pro více webů.

## <a name="prerequisite-install-the-azure-cli"></a>Předpoklad: Instalace příkazového příkazového příkazu Azure

Chcete-li provést kroky v tomto článku, musíte [nainstalovat azure cli](../xplat-cli-install.md) a musíte [se přihlásit do Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Pokud nemáte účet Azure, potřebujete ho. Zde si můžete zaregistrovat [bezplatnou zkušební verzi](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Scénář

V tomto scénáři se dozvíte, jak vytvořit aplikační bránu pomocí portálu Azure.

Tento scénář bude:

* Vytvořte střední aplikační bránu se dvěma instancemi.
* Vytvořte virtuální síť s názvem ContosoVNET s vyhrazeným blokem CIDR 10.0.0.0/16.
* Vytvořte podsíť nazvanou podsíť01, která používá jako blok CIDR 10.0.0.0/28.

> [!NOTE]
> Další konfigurace aplikační brány, včetně vlastních sond stavu, adres back-endového fondu a dalších pravidel, jsou nakonfigurována po konfiguraci brány aplikace, a nikoli během počátečního nasazení.

## <a name="before-you-begin"></a>Než začnete

Azure Application Gateway vyžaduje vlastní podsíť. Při vytváření virtuální sítě se ujistěte, že ponecháte dostatek adresního prostoru pro více podsítí. Po nasazení aplikační brány do podsítě lze do podsítě přidat pouze další aplikační brány.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete **příkazový řádek Microsoft Azure**a přihlaste se.

```azurecli-interactive
az login
```

Jakmile zadáte předchozí příklad, je k dispozici kód. Chcete-li pokračovat v procesu přihlašování, přejděte https://aka.ms/devicelogin v prohlížeči.

![cmd zobrazující přihlášení zařízení][1]

V prohlížeči zadejte kód, který jste obdrželi. Budete přesměrováni na přihlašovací stránku.

![prohlížeč pro zadání kódu][2]

Po zadání kódu, kdy jste přihlášeni, zavřete prohlížeč a pokračujte v tomto scénáři.

![úspěšně přihlášeni][3]

## <a name="switch-to-resource-manager-mode"></a>Přepnutí do režimu Správce prostředků

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením aplikační brány je vytvořena skupina prostředků, která bude obsahovat aplikační bránu. Následuje ukázka příkazu.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Po vytvoření skupiny prostředků se vytvoří virtuální síť pro aplikační bránu.  V následujícím příkladu byl adresní prostor jako 10.0.0.0/16, jak je definováno v předchozích poznámkách ke scénáři.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Vytvoření podsítě

Po vytvoření virtuální sítě je přidána podsíť pro aplikační bránu.  Pokud máte v plánu používat aplikační bránu s webovou aplikací hostovohoujicí ve stejné virtuální síti jako aplikační brána, ponechte dostatek místa pro jinou podsíť.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Po vytvoření virtuální sítě a podsítě jsou požadavky pro aplikační bránu dokončeny. Pro následující krok jsou navíc vyžadován dříve exportovaný certifikát .pfx a heslo pro certifikát: Ip adresy používané pro back-end jsou IP adresy pro back-endový server. Tyto hodnoty mohou být soukromé IP adresy ve virtuální síti, veřejné IP adresy nebo plně kvalifikované názvy domén pro back-endové servery.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Pro seznam parametrů, které mohou být poskytnuty při vytváření spustit následující příkaz: **azure network application-gateway create --help**.

Tento příklad vytvoří základní aplikační bránu s výchozím nastavením pro naslouchací proces, back-endový fond, nastavení http back-endu a pravidla. Tato nastavení můžete upravit tak, aby vyhovovala vašemu nasazení, jakmile je zřizování úspěšné.
Pokud již máte webovou aplikaci definovanou s back-endovým fondem v předchozích krocích, po vytvoření začne vyrovnávání zatížení.

## <a name="next-steps"></a>Další kroky

Naučte se, jak vytvořit vlastní sondy stavu na stránce [Vytvořit vlastní sondu stavu](application-gateway-create-probe-portal.md)

Naučte se, jak nakonfigurovat snižování zátěže SSL a odstranění nákladného dešifrování SSL z webových serverů na stránce [Konfigurace snižování zátěže Protokolu SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
