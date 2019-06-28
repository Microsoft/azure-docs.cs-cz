---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3e4549a21ec32f1a2c1c869c3b2e0bd8c2e4204e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446571"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generovat soubor žádosti o podepsání certifikátu

Apple Push Notification Service (APNs) používá certifikáty k ověřování nabízených oznámení. Pokud chcete vytvořit nabízený certifikát pro odesílání a přijímání oznámení, postupujte podle těchto pokynů. Další informace o těchto konceptech najdete v oficiální dokumentaci ke službě [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Generovat soubor žádosti (Podepsání certifikátu), který Apple používá k vygenerování podepsaného nabízeného certifikátu.

1. V Macu spusťte nástroj Keychain Access. Můžete otevřít z **nástroje** složky nebo **jiných** složky na Launchpad.

1. Vyberte **přístup do řetězce klíčů**, rozbalte **Průvodce certifikací**a pak vyberte **vyžádat certifikát od certifikační autority**.

    ![Použití nástroje Keychain Access k vyžádání nového certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Vyberte vaše **e-mailovou adresu uživatele**, zadejte vaše **běžný název** hodnoty, ujistěte se, že zadáte **uloženo na disk**a pak vyberte **pokračovat**. Ponechte **e-mailová adresa CA** prázdné, protože není to nutné.

    ![Požadované informace o certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Zadejte název souboru žádosti o podepsání certifikátu v **uložit jako**, vyberte umístění v **kde**a pak vyberte **Uložit**.

    ![Zvolte název souboru certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Tato akce uloží soubor CSR ve vybraném umístění. Výchozí umístění je **Desktop**. Zapamatujte si umístění tohoto souboru.

V dalším kroku svou aplikaci zaregistrovat u Applu, povolte nabízená oznámení a nahrajte exportovaný soubor CSR k vytvoření nabízeného certifikátu.

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení

Nabízená oznámení do aplikací pro iOS, registrace vaší aplikace u Applu a také zaregistrovat pro nabízená oznámení.  

1. Pokud jste aplikaci ještě nezaregistrovali, přejděte [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) na webu Apple Developer Center. Po tomto, přihlaste se pomocí Apple ID, vyberte **identifikátory**vyberte **App ID**a nakonec vyberte **+** a zaregistrujte novou aplikaci.

    ![Stránka ID aplikací na portálu zřizování iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Aktualizujte následující tři hodnoty pro novou aplikaci a pak vyberte **pokračovat**:

   * **Název**: Zadejte popisný název pro vaši aplikaci v **název** pole **popis ID aplikace** oddílu.

   * **Identifikátor sady prostředků**: V **explicitní ID aplikace** části, zadejte **identifikátor sady prostředků** formuláře `<Organization Identifier>.<Product Name>` jak je uvedeno v [průvodci distribucí aplikace](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). *Identifikátor organizace* a *název produktu* hodnoty musí odpovídat identifikátoru a produktu název organizace používat při vytváření projektu Xcode. Na následujícím snímku obrazovky *NotificationHubs* hodnota se používá jako identifikátor organizace a *GetStarted* hodnota se používá jako název produktu. Ujistěte se, **identifikátor sady prostředků** odpovídá hodnotě ve vašem projektu Xcode, aby použil Xcode správného profilu publikování.

   * **Nabízená oznámení**: Zkontrolujte **nabízená oznámení** možnost **App Services** oddílu.

     ![Formulář pro registraci nového ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Tato akce vygeneruje ID aplikace a požadavky, že potvrzení tohoto údaje. Vyberte **zaregistrovat** potvrďte nové ID aplikace.

     Po výběru **zaregistrovat**, uvidíte **dokončení registrace** obrazovky, jak je znázorněno na následujícím obrázku. Vyberte **Done** (Hotovo).

     ![Dokončení registrace ID aplikace se zobrazením oprávnění](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. V Centru pro vývojáře v rámci **App ID**, vyhledejte ID aplikace, kterou jste vytvořili a vyberte jeho řádek.

    ![Seznam ID aplikací](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Vyberte ID aplikace a zobrazte podrobnosti o aplikaci, vyberte **upravit** tlačítko dole.

    ![Úprava stránky ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Přejděte do dolní části obrazovky a vyberte **vytvořit certifikát** tlačítko **vývoj Push SSL Certificate** oddílu.

    ![Tlačítko Vytvořit certifikát pro ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Teď se vám **přidání certifikátu iOS** Pomocníka s nastavením.

    > [!NOTE]
    > Tento kurz používá vývojový certifikát. Stejný postup se používá při registraci produkčního certifikátu. Dejte pozor, abyste při odesílání oznámení používali stejný typ certifikátu.

1. Vyberte **zvolit soubor**, přejděte do umístění, kam jste uložili soubor CSR z první úkol a pak vyberte **generovat**.

    ![Stránka pro nahrání vygenerovaného souboru CSR certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. Když portál vytvoří certifikát, vyberte **Stáhnout** tlačítko a pak vyberte **provádí**.

    ![Stránka pro stažení vygenerovaného certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikát je stáhli a uložili do počítače ve vaší **stáhne** složky.

    ![Vyhledání souboru certifikátu ve složce stažených souborů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Ve výchozím nastavení, stažené vývojářského certifikátu název **aps_development.cer**.

1. Vyberte stažený nabízený certifikát **aps_development.cer**.

    Tato akce nainstaluje nový certifikát do Klíčenky, jak je znázorněno na následujícím obrázku:

    ![Seznam certifikátů nástroje Keychain Access zobrazující nový certifikát](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > I když může být jiný název v certifikátu, budou s předponou názvu **Apple Development iOS Push Services**.

1. V nástroji Keychain Access, klikněte pravým tlačítkem na nový nabízený certifikát, který jste vytvořili v kategorii **Certifikáty**. Vyberte **exportovat**, zadejte název souboru, vyberte **.p12** naformátovat a pak vyberte **Uložit**.

    ![Export certifikátu ve formátu p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Poznamenejte si název souboru a umístění exportovaného certifikátu .p12. Slouží k povolení ověřování v APNs.

    > [!NOTE]
    > Tento kurz vytvoří soubor s názvem **QuickStart.p12**. Váš název souboru a umístění se můžou lišit.

## <a name="create-a-provisioning-profile-for-the-app"></a>Vytvoření zřizovacího profilu pro aplikaci

1. V [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456)vyberte **zřizovací profily**vyberte **všechny**a pak vyberte **+** vytvořit nový profil. Zobrazí **přidat profil zřizování iOS** průvodce.

    ![Seznam zřizovacích profilů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Vyberte **vývoj aplikací pro iOS** pod **vývoj** zřizování typ profilu a vybrat **pokračovat**.

1. V dalším kroku vyberte ID aplikace, který jste vytvořili z **ID aplikace** rozevíracího seznamu a vyberte **pokračovat**.

    ![Výběr ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. V **vybrat certifikáty** okna, vyberte svůj obvyklý vývojářský certifikát, který jste použili pro podepisování kódu a vyberte **pokračovat**. Tento certifikát není certifikát push certificate, který jste vytvořili.

    ![Výběr certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. V dalším kroku vyberte zařízení, která chcete použít pro testování a vyberte **pokračovat**.

    ![Výběr zařízení](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Nakonec vyberte název profilu, který **název profilu**a vyberte **generovat**.

    ![Volba názvu zřizovacího profilu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Při vytvoření nového zřizovacího profilu zvolte stáhnout a nainstalovat ji na svém vývojovém počítači s Xcode. Potom vyberte **Done** (Hotovo).

    ![Stažení zřizovacího profilu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Vytvoříte centrum oznámení.

V této části Vytvoření centra oznámení a konfigurace ověřování pomocí služby APNs pomocí certifikátu push .p12, kterou jste vytvořili. Pokud chcete použít Centrum oznámení, kterou jste vytvořili, můžete přeskočit ke kroku 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurace centra oznámení s informacemi služby APN

1. V části **Notification Services** vyberte **Apple (APNS)** .

1. Vyberte **Certifikát**.

1. Vyberte ikonu souboru.

1. Vyberte soubor .p12, který jste dříve exportovali.

1. Zadejte správné heslo.

1. Vyberte režim **Sandbox**. **Produkční** režim použijte pouze v případě, že chcete zasílat nabízená oznámení uživatelům, kteří si zakoupili aplikaci z obchodu s aplikacemi.

    ![Konfigurace certifikační služby APNs na webu Azure Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

Teď nakonfigurujete centra oznámení s APNs. Také máte připojovací řetězce pro svou aplikaci zaregistrovat a odesílání nabízených oznámení.
