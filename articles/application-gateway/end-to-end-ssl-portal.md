---
title: Konfigurace komplexního šifrování TLS pomocí portálu
titleSuffix: Azure Application Gateway
description: Zjistěte, jak pomocí portálu Azure vytvořit aplikační bránu s komplexním šifrováním TLS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133015"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Konfigurace komplexního protokolu TLS pomocí aplikační brány s portálem

Tento článek popisuje, jak pomocí portálu Azure ke konfiguraci šifrování TLS (End-to-end Transport Layer Security), dříve známéjako šifrování SSL (Secure Sockets Layer), prostřednictvím Azure Application Gateway v1 SKU.

> [!NOTE]
> SKU aplikace v2 vyžaduje důvěryhodné kořenové certifikáty pro povolení konfigurace od konce.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="before-you-begin"></a>Než začnete

Chcete-li nakonfigurovat komplexní protokol TLS s aplikační bránou, potřebujete pro bránu certifikát. Certifikáty jsou vyžadovány také pro servery back-end. Certifikát brány se používá k odvození symetrického klíče v souladu se specifikací protokolu TLS. Symetrický klíč se pak používá k šifrování a dešifrování přenosů odeslaných do brány. 

Pro šifrování TLS od konce musí být v bráně aplikace povoleny pravé servery back-end. Chcete-li tento přístup povolit, nahrajte do aplikační brány veřejný certifikát serverů back-end, označovaných také jako ověřovací certifikáty (v1) nebo důvěryhodné kořenové certifikáty (v2). Přidání certifikátu zajistí, že aplikační brána komunikuje pouze se známými back-endovými instancemi. Tato konfigurace dále zabezpečuje komunikaci mezi koncovými soubory.

Další informace najdete v [tématu Přehled ukončení TLS a ukončení tls s aplikační bránou](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Vytvoření nové aplikační brány s komplexním tls

Chcete-li vytvořit novou aplikační bránu s komplexním šifrováním TLS, musíte nejprve povolit ukončení TLS při vytváření nové aplikační brány. Tato akce umožňuje šifrování TLS pro komunikaci mezi klientem a aplikační bránou. Potom budete muset dát na seznam bezpečných příjemců certifikáty pro back-end servery v nastavení HTTP. Tato konfigurace umožňuje šifrování TLS pro komunikaci mezi aplikační bránou a servery back-end. To dosahuje end-to-end šifrování TLS.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Povolení ukončení TLS při vytváření nové aplikační brány

Další informace najdete v [tématu povolení ukončení TLS při vytváření nové brány aplikace](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Přidání ověřovacích/kořenových certifikátů serverů back-end

1. Vyberte **Všechny prostředky**a pak **vyberte myAppGateway**.

2. V nabídce na levé straně vyberte **nastavení protokolu HTTP.** Azure při vytvoření aplikační brány automaticky vytvořil výchozí nastavení **HTTP, appGatewayBackendHttpSettings**. 

3. Vyberte **appGatewayBackendHttpSettings**.

4. V části **Protokol**vyberte **možnost HTTPS**. Zobrazí se podokno pro **ověřovací certifikáty Back-end nebo Důvěryhodné kořenové certifikáty.**

5. Vyberte, že chcete **vytvořit novou** IP adresu.

6. Do pole **Název** zadejte vhodný název.

7. V poli **Nahrát certifikát CER** vyberte soubor certifikátu.

   Pro standardní a WAF (v1) aplikační brány, měli byste nahrát veřejný klíč vašeho back-end certifikátu serveru ve formátu CER.

   ![Přidání certifikátu](./media/end-to-end-ssl-portal/addcert.png)

   Pro Standard_v2 a WAF_v2 aplikačníbrány byste měli nahrát kořenový certifikát certifikátu serveru back-end ve formátu CER. Pokud je back-endový certifikát vydán známou certifikační autoritou( CA), můžete zaškrtnout políčko **Použít známý certifikát certifikační autority** a potom certifikát nemusíte nahrát.

   ![Přidání důvěryhodného kořenového certifikátu](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Kořenový certifikát](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Vyberte **Uložit**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Povolení komplexního tls pro existující aplikační bránu

Chcete-li nakonfigurovat existující aplikační bránu s komplexním šifrováním TLS, musíte nejprve povolit ukončení Protokolu TLS v naslouchací proces. Tato akce umožňuje šifrování TLS pro komunikaci mezi klientem a aplikační bránou. Potom tyto certifikáty pro servery back-end vložte do nastavení protokolu HTTP v seznamu Bezpečných příjemců. Tato konfigurace umožňuje šifrování TLS pro komunikaci mezi aplikační bránou a servery back-end. To dosahuje end-to-end šifrování TLS.

Budete muset použít naslouchací proces s protokolem HTTPS a certifikát pro povolení ukončení TLS. Můžete použít existující naslouchací proces, který splňuje tyto podmínky nebo vytvořit nový naslouchací proces. Pokud zvolíte první možnost, můžete ignorovat následující část "Povolit ukončení TLS v existující aplikační bráně" a přejít přímo do části "Přidat ověřovací/důvěryhodné kořenové certifikáty pro back-endové servery".

Pokud zvolíte druhou možnost, použijte kroky v následujícím postupu.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Povolení ukončení TLS v existující aplikační bráně

1. Vyberte **Všechny prostředky**a pak **vyberte myAppGateway**.

2. V nabídce na levé straně vyberte **Posluchači.**

3. V yberte **základní** nebo **vícewebový** naslouchací proces v závislosti na vašich požadavcích.

4. V části **Protokol**vyberte **možnost HTTPS**. Zobrazí se podokno **certifikátu.**

5. Nahrajte certifikát PFX, který chcete použít pro ukončení TLS mezi klientem a aplikační bránou.

   > [!NOTE]
   > Pro účely testování můžete použít certifikát podepsaný svým držitelem. To se však nedoporučuje pro produkční úlohy, protože je těžší je spravovat a nejsou zcela bezpečné. Další informace naleznete [v tématu vytvoření certifikátu podepsaného svým držitelem](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. V závislosti na požadavcích přidejte další požadovaná nastavení pro **posluchače**.

7. Výběrem **OK** konfiguraci uložte.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Přidání ověřovacích/důvěryhodných kořenových certifikátů serverů back-end

1. Vyberte **Všechny prostředky**a pak **vyberte myAppGateway**.

2. V nabídce na levé straně vyberte **nastavení protokolu HTTP.** Certifikáty můžete umístit do existujícího nastavení http back-end u seznamu Bezpečných příjemců nebo vytvořit nové nastavení HTTP. (V dalším kroku je certifikát pro výchozí nastavení **HTTP, appGatewayBackendHttpSettings**, přidán do seznamu Bezpečných příjemců.)

3. Vyberte **appGatewayBackendHttpSettings**.

4. V části **Protokol**vyberte **možnost HTTPS**. Zobrazí se podokno pro **ověřovací certifikáty Back-end nebo Důvěryhodné kořenové certifikáty.** 

5. Vyberte, že chcete **vytvořit novou** IP adresu.

6. Do pole **Název** zadejte vhodný název.

7. V poli **Nahrát certifikát CER** vyberte soubor certifikátu.

   Pro standardní a WAF (v1) aplikační brány, měli byste nahrát veřejný klíč vašeho back-end certifikátu serveru ve formátu CER.

   ![Přidání certifikátu](./media/end-to-end-ssl-portal/addcert.png)

   Pro Standard_v2 a WAF_v2 aplikačníbrány byste měli nahrát kořenový certifikát certifikátu serveru back-end ve formátu CER. Pokud je back-endový certifikát vydán známou certifikační autoritou, můžete zaškrtnout políčko **Použít známý certifikát certifikační autority** a pak certifikát nemusíte nahrát.

   ![Přidání důvěryhodného kořenového certifikátu](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
