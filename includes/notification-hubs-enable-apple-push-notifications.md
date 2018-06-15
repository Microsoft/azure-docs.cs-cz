---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/11/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 08ff4b2190b26471d7b1ac1850ce89f889b8c256
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33814726"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generování souboru s žádostí o podepsání certifikátu
Služba Apple Push Notification Service (APNS) používá k ověřování nabízených oznámení certifikáty. Pokud chcete vytvořit nabízený certifikát pro odesílání a přijímání oznámení, postupujte podle těchto pokynů. Další informace o těchto konceptech najdete v oficiální dokumentaci ke službě [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Vygenerujte soubor s žádostí o podepsání certifikátu (CSR), který Apple používá k vygenerování podepsaného nabízeného certifikátu.

1. V Macu spusťte nástroj Keychain Access. Můžete ho otevřít na Launchpadu ve složce **Nástroje** nebo **Jiné**.
2. Klikněte na **Keychain Access**, rozbalte **Průvodce certifikací**, klikněte na **Vyžádat certifikát od certifikační autority...**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Vyberte svoji **uživatelskou e-mailovou adresu** a **běžný název**, zkontrolujte, jestli je vybraná možnost **Uloženo na disk**, a potom klikněte na **Pokračovat**. Pole **E-mailová adresa CA** není povinné, takže ho můžete nechat prázdné.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Do pole **Uložit jako** zadejte název souboru CSR, pomocí možnosti **Kam** vyberte umístění a potom klikněte na **Uložit**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Tato akce uloží soubor CSR do vybraného umístění. Výchozím umístěním je plocha. Zapamatujte si umístění tohoto souboru.

V dalším kroku svou aplikaci zaregistrujete u Applu, povolíte nabízená oznámení a odešlete exportovaný soubor CSR k vytvoření nabízeného certifikátu.

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení
Abyste mohli odesílat nabízená oznámení do aplikace systému iOS, musíte aplikaci zaregistrovat u Applu a také ji musíte zaregistrovat pro nabízená oznámení.  

1. Pokud jste aplikaci ještě nezaregistrovali, přejděte na stránky <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> na webu Apple Developer Center, přihlaste se pomocí Apple ID, klikněte na **Identifiers** (Identifikátory), potom na **App IDs** (ID aplikací) a nakonec klikněte na znak **+** a zaregistrujte novou aplikaci.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. Aktualizujte následující tři pole týkající se nové aplikace a potom klikněte na **Continue** (Pokračovat):
   
   * **Name** (Název): V části **App ID Description** (Popis ID aplikace) zadejte do pole **Name** (Název) popisný název aplikace.
   * **Bundle Identifier** (Identifikátor svazku): V části **Explicit App ID** (Explicitní ID aplikace) zadejte **identifikátor svazku** ve formě `<Organization Identifier>.<Product Name>`, jak je uvedeno v [Průvodci distribucí aplikace](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Použité hodnoty *Organization Identifier* (Identifikátor organizace) a *Product Name* (Název produktu) musí odpovídat identifikátoru organizace a názvu produktu, které použijete při vytváření projektu prostředí XCode. Na následujícím snímku se jako identifikátor organizace používá *NotificationHubs* a jako název produktu se používá *GetStarted*. Ujistěte se, že tyto hodnoty odpovídají hodnotám, které použijete v projektu XCode. To vám umožní používat správný profil publikování s XCode. 
   * **Push Notifications** (Nabízená oznámení): V části **App Services** (Služby aplikací) zaškrtněte možnost **Push Notifications** (Nabízená oznámení).
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      Tato akce vygeneruje ID aplikace a vyzve vás k potvrzení tohoto údaje. Kliknutím na **Register** (Zaregistrovat) potvrďte nové ID aplikace.
     
      Po kliknutí na **Register** (Zaregistrovat) se zobrazí obrazovka **Registration complete** (Registrace je dokončena), která je na následujícím obrázku. Klikněte na **Done** (Hotovo).
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. V centru pro vývojáře v části App IDs (ID aplikací) najděte vytvořené ID aplikace a klikněte na jeho řádek.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      Kliknutím na ID aplikace zobrazíte podrobnosti o aplikaci. V dolní části klikněte na tlačítko **Edit** (Upravit).
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. Přejděte do dolní části obrazovky a klikněte v části **Development Push SSL Certificate** (Vývojový certifikát pro nabízená oznámení SSL) na tlačítko **Create Certificate...** (Vytvořit certifikát...).
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      Zobrazí se průvodce Add iOS Certificate (Přidání certifikátu iOS).
   
   > [!NOTE]
   > Tento kurz používá vývojový certifikát. Stejný postup se používá při registraci produkčního certifikátu. Dejte pozor, abyste při odesílání oznámení používali stejný typ certifikátu.
   > 
   > 
3. Klikněte na **Choose File** (Zvolit soubor), přejděte do umístění, kam jste uložili soubor CSR vytvořený v první úloze, a potom klikněte na **Generate** (Generovat).
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. Když portál vytvoří certifikát, klikněte na tlačítko **Download** (Stáhnout) a potom na **Done** Hotovo).
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      Tím certifikát stáhnete a uložíte do počítače do složky se staženými soubory.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Ve výchozím nastavení má stažený soubor vývojářského certifikátu název **aps_development.cer**.
   > 
   > 
5. Poklikejte na stažený nabízený certifikát **aps_development.cer**.
   
      Tato akce nainstaluje nový certifikát do Klíčenky, jak je znázorněno na následujícím obrázku:
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > Název ve vašem certifikátu se může lišit, ale bude mu předcházet text **Apple Development iOS Push Services**.   
6. V nástroji Keychain Access, klikněte pravým tlačítkem na nový nabízený certifikát, který jste vytvořili v kategorii **Certifikáty**. Klikněte na **Exportovat**, zadejte název souboru, vyberte formát **.p12** a potom klikněte na **Uložit**.
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    Poznamenejte si název souboru a umístění exportovaného certifikátu .p12. Budete ho potřebovat k povolení ověřování pomocí služby APNS.
   
   > [!NOTE]
   > V tomto kurzu vytvoříme soubor QuickStart.p12. Váš název souboru a umístění se můžou lišit.
   
## <a name="create-a-provisioning-profile-for-the-app"></a>Vytvoření zřizovacího profilu pro aplikaci
1. Na stránkách <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> vyberte **Provisioning Profiles** (Zřizovací profily), potom **All** (Všechny) a nakonec kliknutím na tlačítko **+** vytvořte nový profil. Zobrazí se průvodce **Add iOS Provisiong Profile** (Přidání zřizovacího profilu iOS).
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. V části **Development** (Vývoj) vyberte jako typ zřizovacího profilu **iOS App Development** (Vývoj aplikací pro iOS) a klikněte na **Continue** (Pokračovat). 
3. Potom v rozevíracím seznamu **App ID** (ID aplikace) vyberte vytvořené ID aplikace a klikněte na **Continue** (Pokračovat).
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. Na obrazovce **Select certificates** (Výběr certifikátů) vyberte svůj obvyklý vývojářský certifikát, kterým podepisujete kód, a klikněte na **Continue** (Pokračovat). Tento certifikát není nabízený certifikát, který jste vytvořili.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Potom vyberte **zařízení**, která chcete použít pro testování, a klikněte na **Continue** (Pokračovat).
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Nakonec do pole **Název profilu** zadejte název profilu a klikněte na **Generovat**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. Po vytvoření nového zřizovacího profilu si ho kliknutím stáhněte a nainstalujte na svém vývojovém počítači s XCode. Potom klikněte na **Done** (Hotovo).
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
