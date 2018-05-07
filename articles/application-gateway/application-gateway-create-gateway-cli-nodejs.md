---
title: Vytvoření služby Azure Application Gateway - rozhraní příkazového řádku Azure 1.0 | Microsoft Docs
description: Informace o vytvoření služby Application Gateway pomocí Azure CLI 1.0 ve službě Správce prostředků
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: victorh
ms.openlocfilehash: 3d67e896da5e616e443fc4e1edd9aaafb0f0e2f9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Vytvoření služby application gateway pomocí Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Šablona Azure Resource Manageru](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)
> 
> 

Služba Azure Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7. Poskytuje převzetí služeb při selhání, směrování výkonu požadavků HTTP mezi různými servery, ať už jsou místní nebo v cloudu. Application gateway poskytuje následující funkce doručování aplikací: HTTP načíst vlastní stavu sondy vyrovnávání, spřažení relace na základě souborů cookie a přesměrování zpracování Secure Sockets Layer (SSL) a podpora pro více lokalit.

## <a name="prerequisite-install-the-azure-cli"></a>Předpoklad: Instalace rozhraní příkazového řádku Azure CLI

Chcete-li provést kroky v tomto článku, je potřeba [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)](../xplat-cli-install.md) a potřebujete [Přihlaste se k Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Pokud nemáte účet Azure, budete potřebovat. Zde si můžete zaregistrovat [bezplatnou zkušební verzi](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scénář

V tomto scénáři zjistíte postup vytvoření služby application gateway pomocí portálu Azure.

Tento scénář se:

* Vytvoření střední application gateway se dvěma instancemi.
* Vytvořte virtuální síť s názvem ContosoVNET s vyhrazeným blokem CIDR 10.0.0.0/16.
* Vytvoříte podsíť s názvem subnet01, který používá jako jeho blok CIDR 10.0.0.0/28.

> [!NOTE]
> Další konfigurace aplikační brány, včetně stavu vlastní testy, adresy fondu back-end a dalších pravidlech nakonfigurovány po dokončení konfigurace aplikační brány a ne během počátečního nasazení.

## <a name="before-you-begin"></a>Než začnete

Služba Azure Application Gateway vyžaduje vlastní podsíti. Při vytváření virtuální sítě, ujistěte se, že necháte dostatek adresní prostor tak, aby měl více podsítí. Po nasazení služby application gateway k podsíti, jedinými dodatečnými application Gateway je moct přidat do podsítě.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Otevřete **Microsoft Azure příkazového řádku**a přihlaste se. 

```azurecli-interactive
azure login
```

Jakmile zadáte v předchozím příkladu, je k dispozici kód. Přejděte na https://aka.ms/devicelogin ve prohlížeči a pokračujte v procesu přihlášení.

![cmd zobrazující zařízení přihlášení][1]

V prohlížeči zadejte kód, který jste dostali. Budete přesměrováni na stránku přihlášení.

![prohlížeče k zadání kódu][2]

Jakmile byl zadán kód jste přihlášeni, zavřete prohlížeč pokračovat na tento scénář.

![Úspěšné přihlášení][3]

## <a name="switch-to-resource-manager-mode"></a>Přepnout do režimu Resource Manager

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením služby application gateway, se tak, aby obsahovala služby application gateway vytvoří skupinu prostředků. Následuje ukázka příkazu.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Po vytvoření skupiny prostředků pro službu application gateway se vytvoří virtuální síť.  V následujícím příkladu se adresní prostor jako 10.0.0.0/16, jak jsou definovány v předchozím scénáři poznámky.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Vytvoření podsítě

Po vytvoření virtuální sítě, přidá se podsíť pro aplikační bránu.  Pokud budete chtít aplikační bránu pomocí webové aplikace hostované ve stejné virtuální síti jako službu application gateway, ujistěte se, zda je dostatek místa pro jinou podsíť.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Po vytvoření virtuální sítě a podsítě jsou dokončeny předpoklady pro službu application gateway. Kromě toho jsou požadovány pro následující krok .pfx dříve exportovaný certifikát a heslo pro certifikát: IP adresy pro back-end jsou IP adresy pro back-end serveru. Tyto hodnoty může být buď soukromé IP adresy ve virtuální síti, veřejné IP adresy nebo plně kvalifikované názvy domény pro back-end serverů.

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
> Seznam parametrů, které lze zadat během vytváření, spusťte následující příkaz: **síť azure aplikace gateway vytvořit – pomáhají**.

Tento příklad vytvoří základní aplikační brána s výchozím nastavením pro naslouchací proces, fond back-end, nastavení http back-end a pravidla. Můžete upravit toto nastavení tak, aby vyhovovala vašemu nasazení po úspěšné přidělení přístupových práv.
Pokud již máte webové aplikace definovaná pomocí fondu back-end v předchozích krocích po vytvoření, Vyrovnávání zatížení začne.

## <a name="next-steps"></a>Další postup

Naučte se vytvářet vlastní stavu sondy navštivte stránky [vytvořit vlastní stav testu](application-gateway-create-probe-portal.md)

Zjistěte, jak nakonfigurovat snižování zátěže protokolu SSL a trvat nákladná dešifrování SSL vypnout webových serverů, navštivte stránky [konfigurovat přesměrování zpracování SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
