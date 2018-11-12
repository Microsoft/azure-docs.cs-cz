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
ms.openlocfilehash: 8c8f3cd67186450fdcf65c177ea0353d297a3b01
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264030"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generování souboru s žádostí o podepsání certifikátu

Služba Apple Push Notification Service (APNS) používá k ověřování nabízených oznámení certifikáty. Pokud chcete vytvořit nabízený certifikát pro odesílání a přijímání oznámení, postupujte podle těchto pokynů. Další informace o těchto konceptech najdete v oficiální dokumentaci ke službě [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Vygenerujte soubor s žádostí o podepsání certifikátu (CSR), který Apple používá k vygenerování podepsaného nabízeného certifikátu.

1. Na Macu spusťte nástroj **Keychain Access**. Můžete ho otevřít na Launchpadu ve složce **Nástroje** nebo **Jiné**.
2. Klikněte na **Keychain Access**, rozbalte **Průvodce certifikací**, klikněte na **Vyžádat certifikát od certifikační autority...**.

    ![Použití nástroje Keychain Access k vyžádání nového certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Vyberte svoji **uživatelskou e-mailovou adresu** a **běžný název**, zkontrolujte, jestli je vybraná možnost **Uloženo na disk**, a potom klikněte na **Pokračovat**. Pole **E-mailová adresa CA** není povinné, takže ho můžete nechat prázdné.

    ![Požadované informace o certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Do pole **Uložit jako** zadejte název souboru CSR, pomocí možnosti **Kam** vyberte umístění a potom klikněte na **Uložit**.

    ![Volba názvu souboru pro certifikát](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Tato akce uloží soubor CSR do vybraného umístění. Výchozím umístěním je plocha. Zapamatujte si umístění tohoto souboru.

V dalším kroku svou aplikaci zaregistrujete u Applu, povolíte nabízená oznámení a odešlete exportovaný soubor CSR k vytvoření nabízeného certifikátu.

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení

Abyste mohli odesílat nabízená oznámení do aplikace systému iOS, musíte aplikaci zaregistrovat u Applu a také ji musíte zaregistrovat pro nabízená oznámení.  

1. Pokud jste aplikaci ještě nezaregistrovali, přejděte na stránky [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) na webu Apple Developer Center, přihlaste se pomocí Apple ID, klikněte na **Identifiers** (Identifikátory), potom na **App IDs** (ID aplikací) a nakonec klikněte na znak **+** a zaregistrujte novou aplikaci.

    ![Stránka ID aplikací na portálu zřizování iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Aktualizujte následující tři pole týkající se nové aplikace a potom klikněte na **Continue** (Pokračovat):

    * **Name** (Název): V části **App ID Description** (Popis ID aplikace) zadejte do pole **Name** (Název) popisný název aplikace.
    * **Bundle Identifier** (Identifikátor svazku): V části **Explicit App ID** (Explicitní ID aplikace) zadejte **identifikátor svazku** ve formě `<Organization Identifier>.<Product Name>`, jak je uvedeno v [Průvodci distribucí aplikace](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Použité hodnoty *Organization Identifier* (Identifikátor organizace) a *Product Name* (Název produktu) musí odpovídat identifikátoru organizace a názvu produktu, které použijete při vytváření projektu prostředí XCode. Na následujícím snímku se jako identifikátor organizace používá *NotificationHubs* a jako název produktu se používá *GetStarted*. Ujistěte se, že tyto hodnoty odpovídají hodnotám, které použijete v projektu XCode. To vám umožní používat správný profil publikování s XCode.
    * **Push Notifications** (Nabízená oznámení): V části **App Services** (Služby aplikací) zaškrtněte možnost **Push Notifications** (Nabízená oznámení).

    ![Formulář pro registraci nového ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

    Tato akce vygeneruje ID aplikace a vyzve vás k potvrzení tohoto údaje. Kliknutím na **Register** (Zaregistrovat) potvrďte nové ID aplikace.

    Po kliknutí na **Register** (Zaregistrovat) se zobrazí obrazovka **Registration complete** (Registrace je dokončena), která je na následujícím obrázku. Klikněte na **Done** (Hotovo).

    ![Dokončení registrace ID aplikace se zobrazením oprávnění](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

3. V centru pro vývojáře v části App IDs (ID aplikací) najděte vytvořené ID aplikace a klikněte na jeho řádek.

    ![Seznam ID aplikací](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Kliknutím na ID aplikace zobrazíte podrobnosti o aplikaci. V dolní části klikněte na tlačítko **Edit** (Upravit).

    ![Úprava stránky ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Přejděte do dolní části obrazovky a klikněte v části **Development Push SSL Certificate** (Vývojový certifikát pro nabízená oznámení SSL) na tlačítko **Create Certificate...** (Vytvořit certifikát...).

    ![Tlačítko Vytvořit certifikát pro ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zobrazí se průvodce **Add iOS Certificate** (Přidání certifikátu iOS).

    > [!NOTE]
    > Tento kurz používá vývojový certifikát. Stejný postup se používá při registraci produkčního certifikátu. Dejte pozor, abyste při odesílání oznámení používali stejný typ certifikátu.

5. Klikněte na **Choose File** (Zvolit soubor), přejděte do umístění, kam jste uložili soubor CSR vytvořený v první úloze, a potom klikněte na **Generate** (Generovat).

    ![Stránka pro nahrání vygenerovaného souboru CSR certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Když portál vytvoří certifikát, klikněte na tlačítko **Download** (Stáhnout) a potom na **Done** Hotovo).

    ![Stránka pro stažení vygenerovaného certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Tím certifikát stáhnete a uložíte do počítače do složky **se staženými soubory**.

    ![Vyhledání souboru certifikátu ve složce stažených souborů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Ve výchozím nastavení má stažený soubor vývojářského certifikátu název **aps_development.cer**.

7. Poklikejte na stažený nabízený certifikát **aps_development.cer**.

    Tato akce nainstaluje nový certifikát do Klíčenky, jak je znázorněno na následujícím obrázku:

    ![Seznam certifikátů nástroje Keychain Access zobrazující nový certifikát](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Název ve vašem certifikátu se může lišit, ale bude mu předcházet text **Apple Development iOS Push Services**.

8. V nástroji Keychain Access, klikněte pravým tlačítkem na nový nabízený certifikát, který jste vytvořili v kategorii **Certifikáty**. Klikněte na **Exportovat**, zadejte název souboru, vyberte formát **.p12** a potom klikněte na **Uložit**.

    ![Export certifikátu ve formátu p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Poznamenejte si název souboru a umístění exportovaného certifikátu .p12. Budete ho potřebovat k povolení ověřování pomocí služby APNS.

    > [!NOTE]
    > V tomto kurzu vytvoříme soubor QuickStart.p12. Váš název souboru a umístění se můžou lišit.

## <a name="create-a-provisioning-profile-for-the-app"></a>Vytvoření zřizovacího profilu pro aplikaci

1. Na stránkách [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) vyberte **Provisioning Profiles** (Zřizovací profily), potom **All** (Všechny) a nakonec kliknutím na tlačítko **+** (plus) vytvořte nový profil. Zobrazí se průvodce **Add iOS Provisioning Profile** (Přidání zřizovacího profilu iOS):

    ![Seznam zřizovacích profilů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. V části **Development** (Vývoj) vyberte jako typ zřizovacího profilu **iOS App Development** (Vývoj aplikací pro iOS) a klikněte na **Continue** (Pokračovat).

3. Potom v rozevíracím seznamu **App ID** (ID aplikace) vyberte vytvořené ID aplikace a klikněte na **Continue** (Pokračovat).

    ![Výběr ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. Na obrazovce **Select certificates** (Výběr certifikátů) vyberte svůj obvyklý vývojářský certifikát, kterým podepisujete kód, a klikněte na **Continue** (Pokračovat). Tento certifikát není nabízený certifikát, který jste vytvořili.

    ![Výběr certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Potom vyberte **zařízení**, která chcete použít pro testování, a klikněte na **Continue** (Pokračovat).

    ![Výběr zařízení](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Nakonec do pole **Název profilu** zadejte název profilu a klikněte na **Generovat**.

    ![Volba názvu zřizovacího profilu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

7. Po vytvoření nového zřizovacího profilu si ho kliknutím stáhněte a nainstalujte na svém vývojovém počítači s XCode. Potom klikněte na **Done** (Hotovo).

    ![Stažení zřizovacího profilu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
