---
title: Rychlý Start – konfigurace šifrování mezi koncovými protokoly SSL pomocí Azure Application Gateway-Azure Portal | Microsoft Docs
description: Naučte se, jak pomocí Azure Portal vytvořit Aplikační bránu s koncovým šifrováním SSL.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: ba31b5ebf83edcd08060a2acc3b5639a521e2729
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243663"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurace kompletního protokolu SSL pomocí Application Gateway s portálem

Tento článek popisuje, jak pomocí Azure Portal nakonfigurovat šifrování mezi koncovými SSL (Secure Sockets Layer) (SSL) prostřednictvím SKU Azure Application Gateway v1.

> [!NOTE]
> SKU Application Gateway v2 vyžaduje důvěryhodné kořenové certifikáty pro povolení ucelené konfigurace.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Před zahájením

Pro konfiguraci kompletního protokolu SSL s aplikační bránou potřebujete certifikát pro bránu. Pro back-endové servery jsou také vyžadovány certifikáty. Certifikát brány se používá k odvození symetrického klíče v souladu se specifikací protokolu SSL. Symetrický klíč se pak použije k šifrování a dešifrování provozu odeslaného do brány. 

U kompletního šifrování SSL musí být ve službě Application Gateway povolený správný back-end Server. Pro povolení tohoto přístupu nahrajte do aplikační brány veřejný certifikát back-end serverů, označovaný také jako certifikáty pro ověřování (V1) nebo důvěryhodné kořenové certifikáty (v2). Přidáním certifikátu zajistíte, aby brána Application Gateway komunikovala pouze se známými back-end instancemi. Tato konfigurace dále zabezpečuje ucelenou komunikaci.

Další informace najdete v tématu [ukončení SSL a kompletní šifrování SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Vytvoření nové aplikační brány s koncovým protokolem SSL

Pokud chcete vytvořit novou aplikační bránu s koncovým šifrováním SSL, musíte nejdřív povolit ukončení protokolu SSL při vytváření nové aplikační brány. Tato akce povolí šifrování SSL pro komunikaci mezi klientem a aplikační bránou. Potom budete muset v nastavení protokolu HTTP umístit na seznam Bezpeční příjemci certifikáty pro back-endové servery. Tato konfigurace umožňuje šifrování SSL pro komunikaci mezi aplikační bránou a back-end servery. Který provádí kompletní šifrování SSL.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Povolit ukončení SSL při vytváření nové aplikační brány

Další informace najdete v tématu [Povolení ukončení protokolu SSL při vytváření nové aplikační brány](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Přidání ověřování/kořenových certifikátů back-endové serverů

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce na levé straně vyberte **Nastavení http** . Při vytváření služby Application Gateway Azure automaticky vytvořil výchozí nastavení HTTP, **appGatewayBackendHttpSettings**. 

3. Vyberte **appGatewayBackendHttpSettings**.

4. V části **protokol**vyberte **https**. Zobrazí se podokno pro **certifikáty pro ověřování back-end nebo důvěryhodné kořenové certifikáty** .

5. Vyberte **vytvořit novou**.

6. Do pole **název** zadejte vhodný název.

7. V poli **Odeslat certifikát CER** vyberte soubor certifikátu.

   Pro aplikační brány Standard a WAF (V1) byste měli odeslat veřejný klíč certifikátu back-end serveru ve formátu. cer.

   ![Přidat certifikát](./media/end-to-end-ssl-portal/addcert.png)

   Pro brány aplikací Standard_v2 a WAF_v2 byste měli do formátu. cer nahrát kořenový certifikát serveru back-end. Pokud je back-end certifikát vydaný dobře známou certifikační autoritou (CA), můžete zaškrtnout políčko **použít dobře známý certifikát certifikační autority** a pak nemusíte nahrávat certifikát.

   ![Přidat důvěryhodný kořenový certifikát](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Kořenový certifikát](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Vyberte **Uložit**.

## <a name="enable-end-to-end-ssl-for-an-existing-application-gateway"></a>Povolení kompletního protokolu SSL pro existující Aplikační bránu

Pokud chcete nakonfigurovat existující Aplikační bránu s komplexním šifrováním SSL, musíte nejdřív v naslouchací službě povolit ukončení protokolu SSL. Tato akce povolí šifrování SSL pro komunikaci mezi klientem a aplikační bránou. Pak vložte tyto certifikáty pro back-endové servery do nastavení HTTP v seznamu bezpečných příjemců. Tato konfigurace umožňuje šifrování SSL pro komunikaci mezi aplikační bránou a back-end servery. Který provádí kompletní šifrování SSL.

Budete muset použít naslouchací proces s protokolem HTTPS a certifikát pro povolení ukončení protokolu SSL. Můžete použít buď existující naslouchací proces, který splňuje tyto podmínky, nebo vytvořit nový naslouchací proces. Pokud zvolíte možnost předchozí, můžete ignorovat následující část povolení ukončení protokolu SSL v existující bráně Application Gateway a přejít přímo na oddíl přidat ověřování/důvěryhodné kořenové certifikáty pro back-end servery.

Pokud zvolíte druhou možnost, použijte postup uvedený v následujícím postupu.
### <a name="enable-ssl-termination-in-an-existing-application-gateway"></a>Povolení ukončení protokolu SSL v existující aplikační bráně

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce na levé straně vyberte **naslouchací procesy** .

3. V závislosti na vašich požadavcích vyberte buď službu Listener **Basic** , nebo naslouchací proces **více webů** .

4. V části **protokol**vyberte **https**. Zobrazí se podokno pro **certifikát** .

5. Nahrajte certifikát PFX, který hodláte použít pro ukončení SSL mezi klientem a aplikační bránou.

   > [!NOTE]
   > Pro účely testování můžete použít certifikát podepsaný svým držitelem. Nedoporučuje se ale pro produkční úlohy, protože jsou těžší je spravovat a nejsou úplně zabezpečené. Další informace najdete v tématu [Vytvoření certifikátu podepsaného svým držitelem](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. V závislosti na vašich požadavcích přidejte další požadovaná nastavení pro **naslouchací proces**.

7. Kliknutím na **tlačítko OK** uložte.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Přidání ověřování/důvěryhodných kořenových certifikátů back-endové servery

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce na levé straně vyberte **Nastavení http** . Můžete buď umístit certifikáty do stávajícího nastavení back-endu HTTP na seznamu bezpečných příjemců, nebo vytvořit nové nastavení HTTP. (V dalším kroku se do seznamu bezpečných příjemců přidá certifikát pro výchozí nastavení HTTP **appGatewayBackendHttpSettings**.)

3. Vyberte **appGatewayBackendHttpSettings**.

4. V části **protokol**vyberte **https**. Zobrazí se podokno pro **certifikáty pro ověřování back-end nebo důvěryhodné kořenové certifikáty** . 

5. Vyberte **vytvořit novou**.

6. Do pole **název** zadejte vhodný název.

7. V poli **Odeslat certifikát CER** vyberte soubor certifikátu.

   Pro aplikační brány Standard a WAF (V1) byste měli odeslat veřejný klíč certifikátu back-end serveru ve formátu. cer.

   ![Přidat certifikát](./media/end-to-end-ssl-portal/addcert.png)

   Pro brány aplikací Standard_v2 a WAF_v2 byste měli do formátu. cer nahrát kořenový certifikát serveru back-end. Pokud je back-end certifikát vydaný dobře známou certifikační autoritou, můžete zaškrtnout políčko **použít dobře známý certifikát certifikační autority** a pak nemusíte nahrávat certifikát.

   ![Přidat důvěryhodný kořenový certifikát](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány pomocí Azure CLI](./tutorial-manage-web-traffic-cli.md)
