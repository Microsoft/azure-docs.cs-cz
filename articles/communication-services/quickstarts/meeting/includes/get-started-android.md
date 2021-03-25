---
title: Rychlý Start – přidání spojení s týmy, které se účastní aplikace pro Android pomocí komunikačních služeb Azure
description: V tomto rychlém startu se dozvíte, jak používat knihovny Azure Communications Services pro Android.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5ac4c53550468d33e9ed533303749d29e772d766
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108467"
---
V tomto rychlém startu se dozvíte, jak připojit týmy na schůzku pomocí knihovny Azure Communications Services pro Android.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio)pro vytváření aplikací pro Android.
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- [Přístupový token uživatele](../../access-tokens.md) pro komunikační službu Azure

## <a name="setting-up"></a>Nastavení

### <a name="create-an-android-app-with-an-empty-activity"></a>Vytvoření aplikace pro Android s prázdnou aktivitou

V Android Studio vyberte spustit nový projekt Android Studio.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Snímek obrazovky s tlačítkem ' spustit nový Android Studio projekt ' vybraný v Android Studio.":::

V části telefon a tablet vyberte šablonu projektu "prázdná aktivita".

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Snímek obrazovky s vybranou možností prázdná aktivita na obrazovce šablony projektu":::

Pojmenujte projekt `TeamsEmbedAndroidGettingStarted` , nastavte jazyk na Java a vyberte minimální sadu SDK "rozhraní API 21: Android 5,0 (Lupa)" nebo vyšší.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Snímek obrazovky s vybranou možností prázdná aktivita na obrazovce šablony projektu 2":::


### <a name="install-the-azure-package"></a>Instalace balíčku Azure

V buildu na úrovni aplikace přidejte následující řádky do částí závislosti a Androidu.

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Instalace balíčku pro vložení týmů

Stáhněte `MicrosoftTeamsSDK` balíček.

Pak rozbalte složku MicrosoftTeamsSDK do složky aplikace projekty. Například `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Přidat týmy vloží balíček do sestavení. Gradle

Na úrovni aplikace přidejte na `build.gradle` konec souboru následující řádek:

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Synchronizovat projekt se soubory Gradle.

### <a name="create-application-class"></a>Vytvořit třídu aplikace

Vytvořte nový soubor třídy Java s názvem `TeamsEmbedAndroidGettingStarted` . To bude třída aplikace, která musí být rozšířena `TeamsSDKApplication` . [Dokumentace k Androidu](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Snímek obrazovky s informacemi o tom, kde vytvořit třídu aplikace v Android Studio":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Aktualizovat motivy

Nastavte název stylu `AppTheme` v `theme.xml` `theme.xml (night)` souborech i.

:::image type="content" source="../media/android/theme-settings.png" alt-text="Snímek obrazovky zobrazující soubory theme.xml v Android Studio":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Přidání oprávnění k manifestu aplikace

Přidejte `RECORD_AUDIO` oprávnění k manifestu aplikace ( `app/src/main/AndroidManifest.xml` ):  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>Přidat název aplikace a motiv do manifestu aplikace

Do manifestu přidejte ' xmlns: Tools = " http://schemas.android.com/tools ".

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

Přidání `.TeamsEmbedAndroidGettingStarted` do `android:name` , `android:name` `tools:replace` a změna `android:theme` na `@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>Nastavení rozložení aplikace

Vytvoří tlačítko s ID `join_meeting` . Přejděte na ( `app/src/main/res/layout/activity_main.xml` ) a nahraďte obsah souboru následujícím:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Vytvoření generování a vazeb hlavní aktivity aktivity

Pomocí vytvořeného rozložení lze přidat základní uživatelské rozhraní aktivity spolu s požadovanými vazbami. Aktivita bude zpracovávat požadavky na běhová oprávnění, vytvořit klienta schůzky a připojit se ke schůzce při stisknutí tlačítka. Každá z nich bude zahrnuta do vlastní části. 

`onCreate`Metoda bude přepsána k vyvolání `getAllPermissions` a `createAgent` také k přidání vazeb pro `Join Meeting` tlačítko. K tomu dojde pouze jednou při vytvoření aktivity. Další informace o nástroji `onCreate` najdete v příručce pro [pochopení životního cyklu aktivit](https://developer.android.com/guide/components/activities/activity-lifecycle).

Přejděte na **MainActivity. Java** a nahraďte obsah následujícím kódem:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Požádat o oprávnění za běhu

Pro Android 6,0 a vyšší (úroveň rozhraní API 23) a `targetSdkVersion` 23 nebo vyšší se oprávnění udělují za běhu, a ne po instalaci aplikace. Aby to bylo možné podporovat, `getAllPermissions` lze implementovat volání `ActivityCompat.checkSelfPermission` a `ActivityCompat.requestPermissions` pro každé požadované oprávnění.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> Při návrhu aplikace zvažte, že se tato oprávnění vyžadují. Oprávnění by se měla požadovat, když jsou potřeba, a ne předem. Další informace najdete v [příručce k oprávněním pro Android.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce knihovny Azure Communications Services pro vložení:

| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| MeetingUIClient je hlavní vstupní bod do knihovny pro vkládání týmů. |
| MeetingJoinOptions | MeetingJoinOptions se používají pro konfigurovatelné možnosti, jako je zobrazované jméno. |
| CallState | CallState slouží k vytváření sestav o změnách stavu volání. Možnosti jsou následující: `connecting` ,, a `waitingInLobby` `connected` `ended` . |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>Vytvoření MeetingClient z přístupového tokenu uživatele

Pro klienta ověřené schůzky se dá vytvořit instance přístupového tokenu uživatele. Tento token obvykle generuje služba s ověřováním specifickým pro aplikaci. Další informace o tokenech přístupu uživatele najdete v průvodci [uživatelskými přístupovými tokeny](../../access-tokens.md) . V případě rychlého startu nahraďte `<USER_ACCESS_TOKEN>` uživatelským tokenem, který jste vygenerovali pro váš prostředek služby Azure Communication Service.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Aktualizace tokenu instalace

Vytvořte metodu, která se nedá volat `tokenRefresher` . Pak vytvořte `fetchToken` metodu pro získání tokenu uživatele. [Pokyny k tomu, jak to udělat, najdete tady.](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Získat odkaz na schůzku týmů

Odkaz na schůzku týmů lze načíst pomocí rozhraní Graph API. Tato část je podrobně popsána v [dokumentaci ke grafům](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Komunikační služby, které volá sadu SDK, přijímají úplný tým odkaz na schůzku. Tento odkaz se vrátí jako součást `onlineMeeting` prostředku, který je přístupný v rámci [ `joinWebUrl` vlastnosti](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) . můžete také získat požadované informace o schůzce z adresy URL **připojení ke schůzce** v rámci pozvánky na schůzku.

## <a name="start-a-meeting-using-the-meeting-client"></a>Spustit schůzku pomocí klienta schůzky

Připojení ke schůzce je možné provést prostřednictvím a `MeetingClient` , ale vyžaduje `meetingURL` a `JoinOptions` . Nahraďte `<MEETING_URL>` adresou URL týmů týmu.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>Spusťte aplikaci a připojte se ke schůzce.

Aplikaci teď můžete spustit pomocí tlačítka Spustit aplikaci na panelu nástrojů (Shift + F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Snímek obrazovky znázorňující dokončenou aplikaci":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Snímek obrazovky zobrazující dokončenou aplikaci po připojení ke schůzce":::

## <a name="sample-code"></a>Příklad kódu

Ukázkovou aplikaci si můžete stáhnout z [GitHubu](https://github.com/Azure-Samples/teams-embed-android-getting-started) .
