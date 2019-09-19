---
title: Rychlý Start – konfigurace šifrování mezi koncovými protokoly SSL pomocí Azure Application Gateway-Azure Portal | Microsoft Docs
description: Naučte se, jak pomocí Azure Portal vytvořit Azure Application Gateway s koncovým šifrováním SSL.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097204"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurace kompletního protokolu SSL pomocí Application Gateway s portálem

V tomto článku se dozvíte, jak pomocí Azure Portal nakonfigurovat komplexní šifrování SSL s SKU Application Gateway v1.  

> [!NOTE]
> SKU Application Gateway v2 vyžaduje důvěryhodné kořenové certifikáty pro povolení ucelené konfigurace.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Před zahájením

Chcete-li nakonfigurovat kompletní protokol SSL s Application Gateway, musí být pro bránu vyžadován certifikát a pro back-end servery jsou vyžadovány certifikáty. Certifikát brány se používá pro odvození symetrického klíče podle specifikace protokolu SSL. Symetrický klíč se pak použije k šifrování a dešifrování provozu odeslaného do brány. U kompletního šifrování SSL musí být ve službě Application Gateway povolený správný back-end Server. Provedete to tak, že nahrajete veřejný certifikát back-end serverů, označovaný také jako certifikáty pro ověřování (V1) nebo důvěryhodné kořenové certifikáty (v2), do Application Gateway. Při přidání certifikátu se zajistí, že Application Gateway komunikuje pouze se známými back-endové instancemi. Tím se dále zabezpečuje koncová komunikace.

Další informace najdete v tématu [ukončení SSL a kompletní šifrování SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Vytvoření nové aplikační brány s koncovým protokolem SSL

Pokud chcete vytvořit novou aplikační bránu s koncovým šifrováním SSL, musíte nejdřív povolit ukončení protokolu SSL při vytváření nové aplikační brány. Tím se povolí šifrování SSL pro komunikaci mezi klientem a aplikační bránou. Pak budete muset povolit certifikáty pro back-end servery v nastavení HTTP, aby se povolilo šifrování SSL pro komunikaci mezi aplikační bránou a back-end servery a aby se dosáhlo kompletního šifrování SSL.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Povolit ukončení SSL při vytváření nové aplikační brány

V tomto článku najdete informace o tom, jak [Povolit ukončení protokolu SSL při vytváření nové aplikační brány](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>Přidat ověřování/kořenový certifikát back-end serverů

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce vlevo vyberte **Nastavení http** . Při vytváření služby Application Gateway Azure automaticky vytvořil výchozí nastavení HTTP, **appGatewayBackendHttpSettings**. 

3. Select **appGatewayBackendHttpSettings**.

4. V části **protokol**vyberte **https**. Zobrazí se podokno pro **certifikáty pro ověřování back-end nebo důvěryhodné kořenové certifikáty** . 

5. Vyberte **vytvořit novou**.

6. Zadejte vhodný **název**.

7. Vyberte soubor certifikátu pomocí pole **Odeslat certifikát CER** .

   Pro aplikační brány Standard a WAF (V1) byste měli odeslat veřejný klíč certifikátu back-end serveru ve formátu. cer.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Pro brány aplikací Standard_v2 a WAF_v2 byste měli načíst **kořenový certifikát** serveru back-end ve formátu. cer. Pokud je certifikát back-endu vydaný dobře známou certifikační autoritou, můžete zaškrtnout políčko použít dobře známý certifikát certifikační autority a nemusíte nahrávat certifikát.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Vyberte **Uložit**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Povolení kompletního protokolu SSL pro existující Aplikační bránu

Pokud chcete nakonfigurovat existující Aplikační bránu s komplexním šifrováním SSL, musíte nejdřív v naslouchací službě povolit ukončení protokolu SSL. Tím se povolí šifrování SSL pro komunikaci mezi klientem a aplikační bránou. Pak budete muset povolit certifikáty pro back-end servery v nastavení HTTP, aby se povolilo šifrování SSL pro komunikaci mezi aplikační bránou a back-end servery a aby se dosáhlo kompletního šifrování SSL.

Pro povolení ukončení protokolu SSL budete muset použít naslouchací proces s protokolem HTTPS a certifikátem. Proto můžete zvolit použití existujícího naslouchacího procesu s protokolem HTTPS a certifikátem nebo vytvořit nový naslouchací proces. V případě, že zvolíte předchozí krok, můžete ignorovat níže uvedené kroky a **Povolit tak ukončení protokolu SSL v existující aplikační bráně** a přímo přesunout do části **Přidat ověřování/důvěryhodné kořenové certifikáty pro back-endové servery** . Pokud zvolíte druhý postup, postupujte podle těchto kroků.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Povolit ukončení protokolu SSL v existující aplikační bráně

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce vlevo vyberte **naslouchací procesy** .

3. Podle vašeho požadavku si vyberte, jak se má vymezit naslouchací proces pro **více webů** .

4. V části **protokol**vyberte **https**. Zobrazí se podokno pro **certifikát** .

5. Nahrajte certifikát PFX, který máte v úmyslu použít pro ukončení protokolu SSL mezi klientem a aplikační bránou.

   > [!NOTE]
   > Pro účely testování můžete použít certifikát podepsaný svým držitelem. ale nedoporučuje se pro produkční úlohy, protože jsou těžší spravovat a nemusíte úplně zabezpečit. Naučte se [vytvořit certifikát podepsaný svým držitelem](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Podle vašeho požadavku přidejte další požadovaná nastavení pro **naslouchací proces** .

7. Výběrem **OK** konfiguraci uložte.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Přidání ověřování/důvěryhodných kořenových certifikátů back-endové servery

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce vlevo vyberte **Nastavení http** . Můžete buď vyřadit certifikáty v existujícím nastavení HTTP back-endu, nebo vytvořit nové nastavení HTTP. V tomto kroku provedeme certifikát pro výchozí nastavení HTTP **appGatewayBackendHttpSettings**.

3. Select **appGatewayBackendHttpSettings**.

4. V části **protokol**vyberte **https**. Zobrazí se podokno pro **certifikáty pro ověřování back-end nebo důvěryhodné kořenové certifikáty** . 

5. Vyberte **vytvořit novou**.

6. Zadejte vhodný **název**.

7. Vyberte soubor certifikátu pomocí pole **Odeslat certifikát CER** .

   Pro aplikační brány Standard a WAF (V1) byste měli odeslat veřejný klíč certifikátu back-end serveru ve formátu. cer.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Pro brány aplikací Standard_v2 a WAF_v2 byste měli načíst **kořenový certifikát** serveru back-end ve formátu. cer. Pokud je certifikát back-endu vydaný dobře známou certifikační autoritou, můžete zaškrtnout políčko použít dobře známý certifikát certifikační autority a nemusíte nahrávat certifikát.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
