---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060482"
---
### <a name="create-the-react-native-solution"></a>Vytvoření nativního řešení reakce

1. V `Terminal` nástroji aktualizujte nástroje prostředí vyžadované pro práci s reagující nativním příkazem pomocí následujících příkazů:

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. V nástroji `Terminal` Spusťte následující příkaz, pokud máte nainstalováno rozhraní příkazového `React Native` řádku pro odinstalaci. Použijte `npx` k automatickému přístupu k dostupné nejnovější verzi nativního rozhraní příkazového řádku pro reakci:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > Reakce na nativní má vestavěné rozhraní příkazového řádku. Místo toho, abyste mohli globálně instalovat a spravovat konkrétní verzi rozhraní příkazového řádku, doporučujeme, abyste měli přístup k aktuální verzi za běhu pomocí `npx` , která se dodává s Node.js. V `npx react-native <command>` případě nástroje se aktuální stabilní verze rozhraní příkazového řádku stáhne a spustí v době, kdy se příkaz spustí.

1. Přejděte do složky projekty, kde chcete vytvořit novou aplikaci. Šablonu založenou na TypeScript použijte zadáním `--template` parametru:

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Spusťte server Metro, který sestaví sady JavaScript a monitoruje jakékoli aktualizace kódu a aktualizuje sady prostředků v reálném čase:

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Spusťte aplikaci pro iOS a ověřte instalaci. Než spustíte následující příkaz, ujistěte se, že jste spustili simulátor iOS nebo zařízení s iOS.

    ```bash
    npx react-native run-ios
    ```

1. Spusťte aplikaci pro Android a ověřte instalaci. Pro konfiguraci emulátoru nebo zařízení s Androidem, který by měl mít přístup k nativnímu serveru Metro, se vyžaduje několik dalších kroků. Následující příkazy generují počáteční sadu JavaScriptu pro Android a umístí je do složky assets.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    Tento skript bude předem nasazen s původní verzí aplikace. Po nasazení nakonfigurujte emulátor nebo zařízení pro přístup k serveru Metro zadáním IP adresy serveru. Spusťte následující příkaz, který sestaví a spustí aplikaci pro Android:

    ```bash
    npx react-native run-android
    ```

    V aplikaci klikněte na `CMD+M` (emulátor) nebo zatřeste zařízení, aby se naplnila nastavení pro vývojáře, přejděte na `Settings`  >  `Change Bundle Location` adresu a zadejte IP adresu serveru Metro s výchozím portem: `<metro-server-ip-address>:8081` .

1. V `App.tsx` souboru použijte jakoukoli změnu rozložení stránky, uložte ji a proveďte tuto změnu automaticky v aplikacích pro iOS i Android.

    > [!NOTE]
    > Průvodce nastavením podrobného vývojového prostředí je k dispozici v [oficiální dokumentaci](https://reactnative.dev/docs/environment-setup) .

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Aby tato ukázka fungovala, potřebujete následující tři balíčky:

1. [Reakce na nativní nabízená oznámení iOS](https://www.npmjs.com/package/@react-native-community/push-notification-ios)  -  [GitHub projektu](https://github.com/react-native-community/push-notification-ios)

    Tento balíček se vytvořil, když se PushNotificationIOS oddělí od nativního reagující na reakci. Balíček nativně implementuje nabízená oznámení pro iOS a poskytuje reakci nativní rozhraní pro přístup k ní. Spusťte následující příkaz pro instalaci balíčku:

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [Reakce na nativní nabízená oznámení mezi platformami](https://www.npmjs.com/package/react-native-push-notification)

    Tento balíček implementuje místní a vzdálená oznámení v systémech iOS a Android pro různé platformy. Spusťte následující příkaz pro instalaci balíčku:

    ```bash
    yarn add react-native-push-notification
    ```

1. [Balíček informací o zařízení](https://www.npmjs.com/package/react-native-device-info) Balíček poskytuje informace o zařízení v modulu runtime. Použijte ho k definování identifikátoru zařízení, který se používá k registraci nabízeného oznámení. Spusťte následující příkaz pro instalaci balíčku:

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>Implementace komponent pro různé platformy

1. Vytvořit a implementovat `DemoNotificationHandler` :

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. Vytvořit a implementovat `DemoNotificationService` :

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. Vytvořit a implementovat `DemoNotificationRegistrationService` :

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. Nakonfigurujte aplikaci. Otevřete `package.json` a přidejte následující definici skriptu:

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Pak spusťte tento skript, který zkopíruje výchozí konfiguraci do `config` složky.

    ```bash
    yarn configure
    ```

    Posledním krokem je aktualizovat konfigurační soubor zkopírovaný v předchozím kroku s použitím informací o přístupu k rozhraní API. Zadejte `apiKey` `apiUrl` parametry a:

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>Implementace uživatelského rozhraní pro různé platformy

1. Definovat rozložení stránky

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Použít styly

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Inicializovat součást stránky

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Definice obslužných rutin kliknutí na tlačítko

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. Popisovač přijal registrace tokenů a nabízená oznámení.

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```