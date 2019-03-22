---
title: Rychlý start – konfigurace šifrování SSL začátku do konce pomocí Azure Application Gateway – Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak pomocí webu Azure portal k vytvoření služby Azure Application Gateway s šifrováním SSL začátku do konce.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e47a3e1231701f3339057e25ee4388aff0c9fbd7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227561"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurace-kompletního protokolu SSL pomocí Application Gateway pomocí portálu

V tomto článku se dozvíte, jak pomocí webu Azure portal ke konfiguraci šifrování SSL začátku do konce pomocí služby application gateway v1 SKU.  

> [!NOTE]
> SKU brány v2 aplikace vyžaduje pro povolení začátku do konce konfiguraci důvěryhodných kořenových certifikátů. Podporu pro přidání důvěryhodných kořenových certifikátů ještě není k dispozici. Proto v případě V2 SKU najdete v článku [-kompletního protokolu SSL pomocí Powershellu nakonfigurovat](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Před zahájením

Ke konfiguraci-kompletního protokolu SSL s aplikační bránou, certifikát je vyžadován pro bránu a certifikáty jsou nutné k back-end serverů. Certifikát brány se používá k odvození symetrický klíč podle specifikace protokolu SSL. Symetrický klíč se pak používá k šifrování a dešifrování provoz odeslaný na bránu. Pro šifrování SSL začátku do konce back-end musí být povolené ve službě application gateway. K tomuto účelu odešlete veřejný certifikát back-end serverů, označované také jako ověřovací certifikáty, ke službě application gateway. Přidání certifikátu se zajistí, že službu application gateway komunikuje pouze se známými back endových instancí. To dále zabezpečuje komunikaci začátku do konce.

Další informace najdete v tématu [SSL ukončení a -kompletního protokolu SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Vytvořit novou aplikační bránu s protokolem SSL začátku do konce

Pokud chcete vytvořit novou aplikační bránu pomocí šifrování SSL začátku do konce, je potřeba nejdřív povolení ukončení protokolu SSL při vytváření novou aplikační bránu. Tato možnost povolí šifrování SSL pro komunikaci mezi brány klienta a aplikace. Poté je potřeba na seznamu povolených certifikátů pro servery back-end v nastavení HTTP povolit šifrování SSL pro komunikaci mezi aplikační brány a back-end servery, tedy provádění šifrování SSL začátku do konce.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Povolení ukončení protokolu SSL při vytváření novou aplikační bránu

Přečtěte si tento článek pochopit, jak [povolení ukončení protokolu SSL při vytváření novou aplikační bránu](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Seznam povolených certifikáty pro servery back-endu

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. Vyberte **nastavení HTTP** v levé nabídce. Azure automaticky vytvoří výchozí nastavení protokolu HTTP, **appGatewayBackendHttpSettings**, když vytvoříte službu application gateway. 

3. Select **appGatewayBackendHttpSettings**.

4. V části **protokol**vyberte **HTTPS**. Podokno pro **ověřovací certifikáty back-endu** se zobrazí. 

5. V části **ověřovací certifikáty back-endu**, zvolte **vytvořit nový**.

6. Zadejte vhodný **název**.

7. Nahrát certifikát pomocí **CER nahrát certifikát** pole.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Certifikát zadaný v tomto kroku by měl být veřejný klíč certifikátu .pfx, který je k dispozici na back-endu. Exportujte certifikát nainstalovaný na back endového serveru ve formátu deklarace identity, legitimaci a odůvodnění (CER) (nikoliv kořenový certifikát) a použít ho v tomto kroku. Tento krok seznamů povolených back-end pomocí služby application gateway.

8. Vyberte **Uložit**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Povolit-kompletního protokolu SSL pro existující aplikační bráně

Pokud chcete nakonfigurovat existující aplikační bráně pomocí šifrování SSL začátku do konce, je potřeba prvním povolení ukončení protokolu SSL v naslouchací proces. Tato možnost povolí šifrování SSL pro komunikaci mezi brány klienta a aplikace. Poté je potřeba na seznamu povolených certifikátů pro servery back-end v nastavení HTTP povolit šifrování SSL pro komunikaci mezi aplikační brány a back-end servery, tedy provádění šifrování SSL začátku do konce.

Je potřeba použít naslouchací proces protokolu HTTPS a certifikát pro povolení ukončení protokolu SSL. Nelze změnit protokol existující naslouchací proces. Proto buď můžete použít existující naslouchací proces protokolu HTTPS a certifikát, nebo vytvořte nový naslouchací proces. V případě, že nejprve zvolíte, můžete ignorovat níže uvedených kroků **ukončení povolit šifrování SSL v existující aplikační bráně** a přejít přímo na **seznamu povolených certifikátů pro servery back-end** oddíl. Pokud zvolíte ten, proveďte tyto kroky. 

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Povolení ukončení protokolu SSL v existující aplikační bráně

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. Vyberte **naslouchacích procesů** v levé nabídce.

3. Výběr mezi **základní** a **Multi-Site** naslouchacího procesu podle vašich požadavků.

4. V části **protokol**vyberte **HTTPS**. Podokno pro **certifikát** se zobrazí.

5. Nahrajte certifikát PFX, který máte v úmyslu používat pro ukončení protokolu SSL mezi brány klienta a aplikace.

   > [!NOTE]
   > Pro účely testování můžete použít certifikát podepsaný svým držitelem. Pro produkční úlohy byste neměli používat certifikát podepsaný svým držitelem. Zjistěte, jak [vytvořit certifikát podepsaný svým držitelem](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Přidat další požadovaná nastavení **naslouchací proces** podle vašich požadavků.

7. Výběrem **OK** konfiguraci uložte.

### <a name="whitelist-certificates-for-backend-servers"></a>Seznam povolených certifikáty pro servery back-endu

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. Vyberte **nastavení HTTP** v levé nabídce. Můžete buď certifikáty seznamu povolených IP adres v back-endu existující HTTP nastavení nebo vytvořte nové nastavení protokolu HTTP. V následující krok, budeme seznamu povolených certifikátů pro výchozí nastavení protokolu HTTP, **appGatewayBackendHttpSettings**.

3. Select **appGatewayBackendHttpSettings**.

4. V části **protokol**vyberte **HTTPS**. Podokno pro **ověřovací certifikáty back-endu** se zobrazí. 

5. V části **ověřovací certifikáty back-endu**, zvolte **vytvořit nový**.

6. Zadejte vhodný **název**.

7. Nahrát certifikát pomocí **CER nahrát certifikát** pole.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Certifikát zadaný v tomto kroku by měl být veřejný klíč certifikátu .pfx, který je k dispozici na back-endu. Exportujte certifikát nainstalovaný na back endového serveru ve formátu deklarace identity, legitimaci a odůvodnění (CER) (nikoliv kořenový certifikát) a použít ho v tomto kroku. Tento krok seznamů povolených back-end pomocí služby application gateway.

8. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
