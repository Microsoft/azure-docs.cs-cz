---
title: Konfigurace vzájemného ověřování v Azure Application Gateway prostřednictvím portálu
description: Naučte se, jak nakonfigurovat Application Gateway pro vzájemné ověřování prostřednictvím portálu.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231394"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>Konfigurace vzájemného ověřování pomocí Application Gateway přes portál (Preview)

Tento článek popisuje, jak pomocí Azure Portal nakonfigurovat vzájemné ověřování v Application Gateway. Vzájemné ověřování znamená, Application Gateway ověřování klienta odesílajícího žádosti pomocí certifikátu klienta, který nahrajete do Application Gateway. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Než začnete

Ke konfiguraci vzájemného ověřování pomocí Application Gateway potřebujete klientský certifikát pro nahrání do brány. Klientský certifikát se použije k ověření certifikátu, ke kterému bude klient Application Gateway. Pro účely testování můžete použít certifikát podepsaný svým držitelem. Nedoporučuje se ale pro produkční úlohy, protože jsou těžší je spravovat a nejsou úplně zabezpečené. 

Další informace, zejména o tom, jaké typy klientských certifikátů můžete nahrát, najdete v tématu [Přehled vzájemného ověřování pomocí Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-new-application-gateway"></a>Vytvořit nový Application Gateway

Nejdřív vytvořte nový Application Gateway stejným způsobem jako obvykle prostřednictvím portálu – při vytváření nejsou potřeba žádné další kroky, abyste mohli vzájemné ověřování povolit. Další informace o tom, jak vytvořit Application Gateway na portálu, najdete v našem [kurzu rychlý](./quick-create-portal.md)Úvod k portálu.

## <a name="configure-mutual-authentication"></a>Konfigurace vzájemného ověřování 

Pokud chcete nakonfigurovat existující Application Gateway se vzájemným ověřováním, musíte nejdřív přejít na kartu **Nastavení SSL (Preview)** na portálu a vytvořit nový profil SSL. Při vytváření profilu SSL se zobrazí dvě karty: **ověřování klientů** a **zásady protokolu SSL**. Na kartě **ověřování klienta** nahrajete své klientské certifikáty. Karta **zásady protokolu SSL** slouží ke konfiguraci zásad protokolu SSL specifických pro naslouchací službu. Další informace najdete v části [Konfigurace zásad protokolu SSL konkrétního naslouchacího procesu](./application-gateway-configure-listener-specific-ssl-policy.md).

> [!IMPORTANT]
> Ujistěte se prosím, že jste nahráli celý řetěz certifikátů klientské certifikační autority do jednoho souboru a jenom jeden řetězec na soubor.

1. Vyhledejte **Application Gateway** na portálu, vyberte **aplikační brány** a klikněte na stávající Application Gateway.

2. V nabídce na levé straně vyberte **Nastavení SSL (Preview)** .

3. Kliknutím na symbol plus vedle **profily SSL** v horní části vytvoříte nový profil SSL.

4. Do pole **název profilu SSL** zadejte název. V tomto příkladu budeme volat náš profil SSL *applicationGatewaySSLProfile*. 

5. Zůstat na kartě **ověřování klienta** . Nahrajte certifikát PEM, který hodláte použít pro vzájemné ověřování mezi klientem a Application Gateway pomocí tlačítka **nahrát nový certifikát** . 

    Další informace o extrakci řetězů certifikátů certifikační autority důvěryhodných klientů k odeslání najdete v tématu [postup extrakce řetězů certifikátů certifikační autority důvěryhodných klientů](./mutual-authentication-certificate-management.md).

   > [!NOTE]
   > Pokud se nejedná o váš první profil SSL a nahráli jste další klientské certifikáty do svého Application Gateway, můžete v rozevírací nabídce zvolit opětovné použití existujícího certifikátu v bráně. 

6. Zaškrtněte políčko **ověřit rozlišující název vystavitele certifikátu klienta** pouze v případě, že chcete Application Gateway ověřit rozlišující název bezprostředního vystavitele klientského certifikátu. 

7. Zvažte přidání zásady konkrétního naslouchacího procesu. Pokyny najdete v tématu [nastavení zásad protokolu SSL specifických pro naslouchací](./application-gateway-configure-listener-specific-ssl-policy.md)službu.

8. Vyberte **Přidat** a uložte.
    > [!div class="mx-imgBorder"]
    > ![Přidat ověřování klientů do profilu SSL](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>Přidružení profilu SSL k naslouchacímu procesu

Teď, když jsme vytvořili profil SSL s nakonfigurovaným vzájemným ověřováním, musíme k naslouchacímu procesu přidružit profil SSL, aby bylo možné dokončit nastavení vzájemného ověřování. 

1. Přejděte na existující Application Gateway. Pokud jste právě dokončili výše uvedené kroky, nemusíte nic dělat. 

2. V nabídce na levé straně vyberte **naslouchací procesy** . 

3. Pokud ještě nemáte nastavený naslouchací proces HTTPS, klikněte na **Přidat naslouchací proces** . Pokud už máte naslouchací proces HTTPS, klikněte na něj v seznamu. 

4. Zadejte **název naslouchacího procesu**, **front-end IP adresu**, **Port**, **protokol** a další **nastavení https** podle svých požadavků.

5. Zaškrtněte políčko **Povolit profil SSL** , abyste mohli vybrat, který profil SSL se má přidružit k naslouchacímu procesu. 

6. Z rozevíracího seznamu vyberte profil SSL, který jste právě vytvořili. V tomto příkladu zvolíme profil SSL, který jsme vytvořili z předchozích kroků: *applicationGatewaySSLProfile*. 

7. Pokračujte v konfiguraci zbývajícího naslouchacího procesu tak, aby vyhovoval vašim požadavkům. 

8. Kliknutím na **Přidat** uložte nový naslouchací proces s přiřazeným profilem SSL. 

    > [!div class="mx-imgBorder"]
    > ![Přidružit profil SSL k novému naslouchacího procesu](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>Prodloužit platnost certifikátů certifikační autority klienta

V případě vypršení platnosti certifikátu klientské certifikační autority můžete certifikát v bráně aktualizovat pomocí následujících kroků: 

1. Přejděte do Application Gateway a v nabídce vlevo přejděte na kartu **Nastavení SSL (Preview)** . 
 
1. Vyberte existující profily SSL s klientským certifikátem, jehož platnost vypršela. 
 
1. Na kartě **ověřování klienta** vyberte **nahrát nový certifikát** a nahrajte nový certifikát klienta. 
 
1. Vyberte ikonu odpadkového koše vedle certifikátu, jehož platnost vypršela. Tato akce odebere přidružení tohoto certifikátu z profilu SSL. 

1. Opakujte kroky 2-4 výše s jakýmkoli jiným profilem SSL, který používal stejný klientský certifikát s prošlou platností. Z rozevírací nabídky v ostatních profilech SSL budete moct zvolit nový certifikát, který jste nahráli v kroku 3.

## <a name="next-steps"></a>Další kroky

- [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)