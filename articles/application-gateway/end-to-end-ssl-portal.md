---
title: Konfigurace kompletního šifrování TLS pomocí portálu
titleSuffix: Azure Application Gateway
description: Naučte se, jak pomocí Azure Portal vytvořit Aplikační bránu s koncovým šifrováním TLS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 33240d1f44d2f26569791f72a3d5fc3a6656a757
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808038"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Konfigurace kompletního TLS pomocí Application Gateway s portálem

Tento článek popisuje, jak pomocí Azure Portal nakonfigurovat komplexní šifrování TLS (Transport Layer Security), dříve označované jako šifrování SSL (Secure Sockets Layer) (SSL), prostřednictvím SKU Azure Application Gateway v1.

> [!NOTE]
> SKU Application Gateway v2 vyžaduje důvěryhodné kořenové certifikáty pro povolení ucelené konfigurace.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Než začnete

Pro konfiguraci kompletního protokolu TLS s aplikační bránou potřebujete certifikát pro bránu. Pro back-endové servery jsou také vyžadovány certifikáty. Certifikát brány se používá k odvození symetrického klíče v souladu se specifikací protokolu TLS. Symetrický klíč se pak použije k šifrování a dešifrování provozu odeslaného do brány. 

U komplexního šifrování TLS musí být ve službě Application Gateway povolený správný back-end Server. Pro povolení tohoto přístupu nahrajte do aplikační brány veřejný certifikát back-end serverů, označovaný také jako certifikáty pro ověřování (V1) nebo důvěryhodné kořenové certifikáty (v2). Přidáním certifikátu zajistíte, aby brána Application Gateway komunikovala pouze se známými back-end instancemi. Tato konfigurace dále zabezpečuje ucelenou komunikaci.

Další informace najdete v tématu [Přehled ukončení protokolu TLS a koncového šifrování TLS s Application Gateway](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Vytvoření nové aplikační brány s komplexním protokolem TLS

Pokud chcete vytvořit novou aplikační bránu s koncovým šifrováním TLS, musíte nejdřív povolit ukončení protokolu TLS při vytváření nové aplikační brány. Tato akce povolí šifrování TLS pro komunikaci mezi klientem a aplikační bránou. Potom budete muset v nastavení protokolu HTTP umístit na seznam Bezpeční příjemci certifikáty pro back-endové servery. Tato konfigurace umožňuje šifrování TLS pro komunikaci mezi aplikační bránou a back-end servery. Který provádí komplexní šifrování TLS.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Povolit ukončení protokolu TLS při vytváření nové aplikační brány

Další informace najdete v tématu [Povolení ukončení protokolu TLS při vytváření nové aplikační brány](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Přidání ověřování/kořenových certifikátů back-endové serverů

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce na levé straně vyberte **Nastavení http** . Při vytváření služby Application Gateway Azure automaticky vytvořil výchozí nastavení HTTP, **appGatewayBackendHttpSettings**. 

3. Vyberte **appGatewayBackendHttpSettings**.

4. V části **protokol**vyberte **https**. Zobrazí se podokno pro **certifikáty pro ověřování back-end nebo důvěryhodné kořenové certifikáty** .

5. Vyberte, že chcete **vytvořit novou** IP adresu.

6. Do pole **název** zadejte vhodný název.

7. V poli **Odeslat certifikát CER** vyberte soubor certifikátu.

   Pro aplikační brány Standard a WAF (V1) byste měli odeslat veřejný klíč certifikátu back-end serveru ve formátu. cer.

   ![Přidání certifikátu](./media/end-to-end-ssl-portal/addcert.png)

   Pro Standard_v2 a WAF_v2 aplikační brány byste měli do formátu. cer nahrát kořenový certifikát serveru back-end. Pokud je back-end certifikát vydaný dobře známou certifikační autoritou (CA), můžete zaškrtnout políčko **použít dobře známý certifikát certifikační autority** a pak nemusíte nahrávat certifikát.

   ![Přidat důvěryhodný kořenový certifikát](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Kořenový certifikát](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Vyberte **Uložit**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Povolení kompletního TLS pro existující Aplikační bránu

Pokud chcete nakonfigurovat existující Aplikační bránu s komplexním šifrováním TLS, musíte nejdřív v naslouchací službě povolit ukončení protokolu TLS. Tato akce povolí šifrování TLS pro komunikaci mezi klientem a aplikační bránou. Pak vložte tyto certifikáty pro back-endové servery do nastavení HTTP v seznamu bezpečných příjemců. Tato konfigurace umožňuje šifrování TLS pro komunikaci mezi aplikační bránou a back-end servery. Který provádí komplexní šifrování TLS.

K povolení ukončení protokolu TLS budete muset použít naslouchací proces s protokolem HTTPS a certifikátem. Můžete použít buď existující naslouchací proces, který splňuje tyto podmínky, nebo vytvořit nový naslouchací proces. Pokud zvolíte možnost předchozí, můžete ignorovat následující část povolení ukončení protokolu TLS v existující bráně Application Gateway a přejít přímo na oddíl přidat ověřování/důvěryhodné kořenové certifikáty pro back-end servery.

Pokud zvolíte druhou možnost, použijte postup uvedený v následujícím postupu.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Povolení ukončení protokolu TLS v existující aplikační bráně

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce na levé straně vyberte **naslouchací procesy** .

3. V závislosti na vašich požadavcích vyberte buď službu Listener **Basic** , nebo naslouchací proces **více webů** .

4. V části **protokol**vyberte **https**. Zobrazí se podokno pro **certifikát** .

5. Nahrajte certifikát PFX, který hodláte použít pro ukončení TLS mezi klientem a aplikační bránou.

   > [!NOTE]
   > Pro účely testování můžete použít certifikát podepsaný svým držitelem. Nedoporučuje se ale pro produkční úlohy, protože jsou těžší je spravovat a nejsou úplně zabezpečené. Další informace najdete v tématu [Vytvoření certifikátu podepsaného svým držitelem](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. V závislosti na vašich požadavcích přidejte další požadovaná nastavení pro **naslouchací proces**.

7. Výběrem **OK** konfiguraci uložte.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Přidání ověřování/důvěryhodných kořenových certifikátů back-endové servery

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce na levé straně vyberte **Nastavení http** . Můžete buď umístit certifikáty do stávajícího nastavení back-endu HTTP na seznamu bezpečných příjemců, nebo vytvořit nové nastavení HTTP. (V dalším kroku se do seznamu bezpečných příjemců přidá certifikát pro výchozí nastavení HTTP **appGatewayBackendHttpSettings**.)

3. Vyberte **appGatewayBackendHttpSettings**.

4. V části **protokol**vyberte **https**. Zobrazí se podokno pro **certifikáty pro ověřování back-end nebo důvěryhodné kořenové certifikáty** . 

5. Vyberte, že chcete **vytvořit novou** IP adresu.

6. Do pole **název** zadejte vhodný název.

7. V poli **Odeslat certifikát CER** vyberte soubor certifikátu.

   Pro aplikační brány Standard a WAF (V1) byste měli odeslat veřejný klíč certifikátu back-end serveru ve formátu. cer.

   ![Přidání certifikátu](./media/end-to-end-ssl-portal/addcert.png)

   Pro Standard_v2 a WAF_v2 aplikační brány byste měli do formátu. cer nahrát kořenový certifikát serveru back-end. Pokud je back-end certifikát vydaný dobře známou certifikační autoritou, můžete zaškrtnout políčko **použít dobře známý certifikát certifikační autority** a pak nemusíte nahrávat certifikát.

   ![Přidat důvěryhodný kořenový certifikát](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
