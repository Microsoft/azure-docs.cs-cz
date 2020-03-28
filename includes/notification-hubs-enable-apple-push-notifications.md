---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bf2596f5a8e287799285f97f3d1be9f3fe10f644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123242"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generovat soubor žádosti o podpis certifikátu

Služba nabízených oznámení Apple (APNs) používá k ověření nabízených oznámení certifikáty. Pokud chcete vytvořit nabízený certifikát pro odesílání a přijímání oznámení, postupujte podle těchto pokynů. Další informace o těchto konceptech najdete v oficiální dokumentaci ke službě [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Vygenerujte soubor žádosti o podpis (CSR), který Společnost Apple používá ke generování podepsaného nabízeného certifikátu.

1. V Macu spusťte nástroj Keychain Access. Lze jej otevřít ze složky **Nástroje** nebo **Jiné** složky na launchpadu.

1. Vyberte **položku Přístup k řetězci klíčů**, rozbalte **položku Pomocník a**potom **vyberte možnost Požádat o certifikát od certifikační autority**.

    ![Použití nástroje Keychain Access k vyžádání nového certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Ve výchozím nastavení vybere aplikace Keychain Access první položku v seznamu. To může být problém, pokud jste v kategorii **Certifikáty** a **Apple Worldwide Developer Relations Certification Authority** není první položkou v seznamu. Před generováním zástupce oddělení služeb oddělení služeb nic neklíčite se ujistěte, že máte položku, která není klíčem klíče klíče, nebo zda je vybrán klíč **Certifikační autority pro vztahy s vývojáři společnosti Apple Worldwide Developer** Relations, a zda je vybrán klíč CSR (Žádost o podpis certifikátu).

1. Vyberte **uživatelskou e-mailovou adresu**, zadejte hodnotu **Common Name,** ujistěte se, že jste zadali **Uloženo na disk**, a pak vyberte **Pokračovat**. Ponechejte **e-mailovou adresu certifikační autority** prázdnou, protože není vyžadována.

    ![Požadované informace o certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Zadejte název souboru zástupce v **části Uložit jako**, vyberte umístění v části **Kde**a pak vyberte **Uložit**.

    ![Výběr názvu souboru pro certifikát](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Tato akce uloží soubor ZÁSTUPCE do vybraného umístění. Výchozí umístění je **Plocha**. Zapamatujte si umístění tohoto souboru.

Dále zaregistrujte aplikaci u společnosti Apple, povolte nabízená oznámení a nahrajte exportovnou csr a vytvořte nabízený certifikát.

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení

Pokud chcete odesílat nabízená oznámení do aplikace pro iOS, zaregistrujte svou aplikaci u společnosti Apple a také se zaregistrujte pro nabízená oznámení.  

1. Pokud jste si ještě aplikaci nezaregistrovali, přejděte na [portál zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) v Centru pro vývojáře Apple. Přihlaste se k portálu pomocí svého Apple ID a vyberte **Identifikátory**. Pak **+** vyberte zaregistrovat novou aplikaci.

    ![Stránka ID aplikací na portálu zřizování iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Na obrazovce **Registrovat nový identifikátor** vyberte přepínací tlačítko **ID aplikace.** Potom vyberte **Pokračovat**.

    ![Portál zřizování iOS registruje novou stránku ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Aktualizujte následující tři hodnoty pro novou aplikaci a pak vyberte **Pokračovat**:

   * **Popis**: Zadejte popisný název aplikace.

   * **ID balíčku:** Zadejte ID sady formuláře **Organization Identifier.Product Name,** jak je uvedeno v [Průvodci distribucí aplikací](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Hodnoty *identifikátoru organizace* a *názvu produktu* se musí shodovat s identifikátorem organizace a názvem produktu, který používáte při vytváření projektu Xcode. Na následujícím snímku obrazovky se jako identifikátor organizace používá hodnota **NotificationHubs** a jako název produktu se používá hodnota **GetStarted.** Ujistěte se, že hodnota **identifikátoru svazku** odpovídá hodnotě v projektu Xcode, aby Xcode používá správný profil publikování.

      ![Stránka ID aplikace registruje portál iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Nabízená oznámení**: Zkontrolujte možnost **Nabízená oznámení** v části **Možnosti.**

      ![Formulář pro registraci nového ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Tato akce vygeneruje ID aplikace a požádá vás o potvrzení informací. Vyberte **Pokračovat**a pak vyberte **Registrovat,** abyste potvrdili nové ID aplikace.

      ![Potvrdit nové ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Po výběru **možnosti Registrovat**se nové ID aplikace zobrazí jako řádková položka na stránce **Certifikáty, Identifikátory & profily.**

4. Na stránce **Certifikáty, identifikátory & profily** **vyhledejte**řádkovou položku ID aplikace, kterou jste právě vytvořili, a vyberte její řádek, aby se zobrazila obrazovka **Upravit konfiguraci ID aplikace.**

5. Posuňte se dolů na zaškrtnutou možnost **Nabízená oznámení** a pak vyberte **Konfigurovat,** chcete-li vytvořit certifikát.

    ![Úprava stránky ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. Zobrazí se okno **Apple Push Notification service SSL Certificates** . V části **Vývojový certifikát SSL** vyberte tlačítko **Vytvořit certifikát.**

    ![Tlačítko Vytvořit certifikát pro ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zobrazí se obrazovka **Vytvořit nový certifikát.**

    > [!NOTE]
    > Tento kurz používá vývojový certifikát. Stejný postup se používá při registraci produkčního certifikátu. Dejte pozor, abyste při odesílání oznámení používali stejný typ certifikátu.

1. Vyberte **Zvolit soubor**, vyhledejte umístění, do kterého jste uložili soubor ZÁSTUPCE z prvníúlohy, a potom poklepejte na název certifikátu, který chcete načíst. Potom vyberte **Pokračovat**.

1. Po portálu vytvoří certifikát, vyberte **tlačítko Stáhnout.** Uložte certifikát a zapamatujte si umístění, do kterého je uložen.

    ![Stránka pro stažení vygenerovaného certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikát se stáhne a uloží do počítače ve složce **Soubory ke stažení.**

    ![Vyhledání souboru certifikátu ve složce stažených souborů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Ve výchozím nastavení je stažený vývojový certifikát pojmenován **aps_development.cer**.

1. Poklikejte na stažený nabízený certifikát **aps_development.cer**. Tato akce nainstaluje nový certifikát do Klíčenky, jak je znázorněno na následujícím obrázku:

    ![Seznam certifikátů nástroje Keychain Access zobrazující nový certifikát](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > I když se název v certifikátu může lišit, bude předponou **s nabízenými službami pro vývoj iOS společnosti Apple**.

1. V nástroji Keychain Access, klikněte pravým tlačítkem na nový nabízený certifikát, který jste vytvořili v kategorii **Certifikáty**. Vyberte **Exportovat**, pojmenujte soubor, vyberte formát **.p12** a pak vyberte **Uložit**.

    ![Export certifikátu ve formátu p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Certifikát můžete chránit heslem, ale toto je volitelné. Pokud chcete obejít vytvoření hesla, klepněte na **tlačítko OK.** Poznamenejte si název souboru a umístění exportovaného certifikátu .p12. Používají se k povolení ověřování pomocí souborů APN.

    > [!NOTE]
    > Název a umístění souboru .p12 se může lišit od toho, co je znázorněno v tomto kurzu.

## <a name="create-a-provisioning-profile-for-the-app"></a>Vytvoření zřizovacího profilu pro aplikaci

1. Vraťte se na [portál zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), vyberte **certifikáty, identifikátory & profily**, vyberte **profily** v levé nabídce a pak vyberte **+** vytvoření nového profilu. Zobrazí se obrazovka **Registrovat nový zřizovací profil.**

1. Vyberte **vývoj aplikací pro iOS** v části **Vývoj** jako typ zřizovacího profilu a pak vyberte **Pokračovat**.

    ![Seznam zřizovacích profilů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Dále vyberte ID aplikace, které jste **vytvořili,** v rozevíracím seznamu ID aplikace a vyberte **Pokračovat**.

    ![Výběr ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. V okně Vybrat certifikáty vyberte vývojový certifikát, který používáte pro **podepisování** kódu, a vyberte **Pokračovat**. Tento certifikát není nabízený certifikát, který jste vytvořili. Pokud neexistuje, musíte jej vytvořit. Pokud certifikát existuje, přejděte k dalšímu kroku. Chcete-li vytvořit certifikát vývoje, pokud neexistuje:

    1. Pokud se zobrazí **možnost Žádné certifikáty nejsou k dispozici**, vyberte vytvořit **certifikát**.
    2. V části **Software** vyberte **položku Apple Development**. Potom vyberte **Pokračovat**.
    3. Na obrazovce **Vytvořit nový certifikát** vyberte Zvolit **soubor**.
    4. Přejděte k certifikátu **žádosti o podpis certifikátu,** který jste vytvořili dříve, vyberte ho a pak vyberte **Otevřít**.
    5. Vyberte **Pokračovat**.
    6. Stáhněte si certifikát vývoje a zapamatujte si umístění, do kterého je uložen.

1. Vraťte se na stránku **Certifikáty, Identifikátory & Profily,** v levé nabídce vyberte **Profily** a pak vyberte **+** vytvoření nového profilu. Zobrazí se obrazovka **Registrovat nový zřizovací profil.**

1. V okně **Vybrat certifikáty** vyberte právě vytvořený vývojový certifikát. Potom vyberte **Pokračovat**.

1. Dále vyberte zařízení, která chcete použít k testování, a vyberte **Pokračovat**.

1. Nakonec vyberte název profilu v **zřizování názvu profilu**a vyberte **generovat**.

    ![Volba názvu zřizovacího profilu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Po vytvoření nového profilu zřizování vyberte **stáhnout**. Zapamatujte si umístění, do kterého je uložen.

1. Přejděte do umístění profilu zřizování a poklepáním na něj nainstalujte do vývojového počítače Xcode.

## <a name="create-a-notification-hub"></a>Vytvoříte centrum oznámení.

V této části vytvoříte centrum oznámení a nakonfigurujete ověřování pomocí center pomocí příkazového systému .p12, který jste dříve vytvořili. Pokud chcete použít centrum oznámení, které jste už vytvořili, můžete přeskočit ke kroku 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurace centra oznámení s informacemi o kódech APN

1. V části **Notification Services**vyberte **Apple (APNS).**

1. Vyberte **Certifikát**.

1. Vyberte ikonu souboru.

1. Vyberte dříve exportovaný soubor P12 a pak vyberte **Otevřít**.

1. V případě potřeby zadejte správné heslo.

1. Vyberte režim **Izolovaný prostor**. **Produkční** režim použijte pouze v případě, že chcete zasílat nabízená oznámení uživatelům, kteří si zakoupili aplikaci z obchodu s aplikacemi.

    ![Konfigurace certifikační služby APNs na webu Azure Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Vyberte **Uložit**.

Nyní jste centrum oznámení nakonfigurovali pomocí kódů APN. Máte také připojovací řetězce pro registraci aplikace a odesílání nabízených oznámení.
