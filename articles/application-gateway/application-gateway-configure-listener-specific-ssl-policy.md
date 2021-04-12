---
title: Konfigurace zásad protokolu SSL specifických pro naslouchací proces na Azure Application Gateway prostřednictvím portálu
description: Informace o konfiguraci zásad protokolu SSL specifických pro naslouchací proces v Application Gateway prostřednictvím portálu
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231411"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>Konfigurace zásad protokolu SSL specifických pro naslouchací proces na Application Gateway prostřednictvím portálu (Preview)

Tento článek popisuje, jak použít Azure Portal ke konfiguraci zásad protokolu SSL specifických pro naslouchací proces na vašich Application Gateway. Zásady protokolu SSL specifické pro naslouchací proces umožňují nakonfigurovat konkrétní naslouchací procesy, aby na sebe navzájem používaly různé zásady protokolu SSL. Pořád budete moct nastavit výchozí zásadu protokolu SSL, kterou budou používat všechny posluchače, pokud je nepřepíše zásada SSL specifická pro naslouchací proces. 

> [!NOTE]
> Pouze skladové položky Standard_v2 a WAF_v2 podporují zásady pro naslouchací proces, protože zásady pro naslouchací proces jsou součástí profilů SSL a profily SSL jsou podporovány pouze v bránách v2. 



Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-new-application-gateway"></a>Vytvořit nový Application Gateway

Nejdřív vytvořte nový Application Gateway stejným způsobem jako obvykle prostřednictvím portálu – při vytváření konfigurace zásad protokolu SSL pro konkrétní naslouchací proces se nevyžadují žádné další kroky. Další informace o tom, jak vytvořit Application Gateway na portálu, najdete v našem [kurzu rychlý](./quick-create-portal.md)Úvod k portálu.

## <a name="set-up-a-listener-specific-ssl-policy"></a>Nastavení zásad protokolu SSL specifických pro naslouchací proces

Pokud chcete nastavit zásadu protokolu SSL pro konkrétní naslouchací proces, musíte nejdřív přejít na kartu **Nastavení SSL (Preview)** na portálu a vytvořit nový profil SSL. Při vytváření profilu SSL se zobrazí dvě karty: **ověřování klientů** a **zásady protokolu SSL**. Karta **zásady protokolu SSL** slouží ke konfiguraci zásad protokolu SSL specifických pro naslouchací proces. Karta **ověřování klienta** slouží k nahrání klientských certifikátů pro vzájemné ověřování – Další informace najdete v části [Konfigurace vzájemného ověřování](./mutual-authentication-portal.md).

> [!NOTE]
> Doporučujeme používat protokol TLS 1,2, protože TLS 1,2 bude v budoucnu pověřit. 

1. Vyhledejte **Application Gateway** na portálu, vyberte **aplikační brány** a klikněte na stávající Application Gateway.

2. V nabídce na levé straně vyberte **Nastavení SSL (Preview)** .

3. Kliknutím na symbol plus vedle **profily SSL** v horní části vytvoříte nový profil SSL.

4. Do pole **název profilu SSL** zadejte název. V tomto příkladu budeme volat náš profil SSL *applicationGatewaySSLProfile*. 

5. Přejdete na kartu **zásady protokolu SSL** a zaškrtněte políčko **Povolit zásadu protokolu SSL pro konkrétní naslouchací proces** . 

6. Nastavte zásady protokolu SSL specifické pro naslouchací proces podle vašich požadavků. Můžete si vybrat mezi předdefinovanými zásadami protokolu SSL a přizpůsobením vlastních zásad SSL. Další informace o zásadách protokolu SSL najdete v článku [Přehled zásad protokolu SSL](./application-gateway-ssl-policy-overview.md). Doporučujeme používat protokol TLS 1,2.

7. Vyberte **Přidat** a uložte.

    > [!NOTE]
    > Nemusíte konfigurovat ověřování klientů v profilu SSL, aby ho bylo možné přidružit k naslouchacího procesu. Můžete mít nakonfigurovanou jenom konfiguraci ověřování klientů, nebo jenom nakonfigurovat konkrétní zásadu SSL pro naslouchací službu nebo jak nakonfigurovanou v profilu SSL.  

    ![Přidat zásadu protokolu SSL konkrétního naslouchacího procesu do profilu SSL](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>Přidružení profilu SSL k naslouchacímu procesu

Teď, když jsme vytvořili profil SSL s použitím zásad protokolu SSL podle naslouchacího procesu, musíme k naslouchacímu procesu přidružit profil SSL, aby se zásady pro konkrétní naslouchací proces uvedly v akci. 

1. Přejděte na existující Application Gateway. Pokud jste právě dokončili výše uvedené kroky, nemusíte nic dělat. 

2. V nabídce na levé straně vyberte **naslouchací procesy** . 

3. Pokud ještě nemáte nastavený naslouchací proces HTTPS, klikněte na **Přidat naslouchací proces** . Pokud už máte naslouchací proces HTTPS, klikněte na něj v seznamu. 

4. Zadejte **název naslouchacího procesu**, **front-end IP adresu**, **Port**, **protokol** a další **nastavení https** podle svých požadavků.

5. Zaškrtněte políčko **Povolit profil SSL** , abyste mohli vybrat, který profil SSL se má přidružit k naslouchacímu procesu. 

6. V rozevíracím seznamu vyberte profil SSL, který jste vytvořili. V tomto příkladu zvolíme profil SSL, který jsme vytvořili z předchozích kroků: *applicationGatewaySSLProfile*. 

7. Pokračujte v konfiguraci zbývajícího naslouchacího procesu tak, aby vyhovoval vašim požadavkům. 

8. Kliknutím na **Přidat** uložte nový naslouchací proces s přiřazeným profilem SSL. 

    ![Přidružit profil SSL k novému naslouchacího procesu](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)