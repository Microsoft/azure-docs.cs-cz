---
title: Rychlý Start – přidání volání VOIP do aplikace pro Android pomocí komunikačních služeb Azure
description: V tomto kurzu se naučíte používat komunikační služby Azure s voláním klientské knihovny pro Android.
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: a387261b392ea6718941f5eabe889e0c1a41fd5a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750911"
---
V tomto rychlém startu se dozvíte, jak spustit volání pomocí komunikačních služeb Azure, které volají klientskou knihovnu pro Android.

> [!NOTE]
> Tento dokument používá verzi 1.0.0-beta. 8 volání klientské knihovny.

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

Vyberte minimální klientskou knihovnu "rozhraní API 26: Android 8,0 (Oreo)" nebo vyšší.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Snímek obrazovky s vybranou možností prázdná aktivita na obrazovce šablony projektu 2":::


### <a name="install-the-package"></a>Instalace balíčku

<!-- TODO: update with instructions on how to download, install and add package to project -->
Vyhledejte na úrovni projektu Build. Gradle a ujistěte se, že jste přidali `mavenCentral()` do seznamu úložišť v části `buildscript` a. `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Pak v sestavení na úrovni modulu přidejte následující řádky do částí závislosti a Androidu.

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>Přidání oprávnění k manifestu aplikace

Aby bylo možné požádat o oprávnění požadovaná pro volání, musí být nejprve deklarována v manifestu aplikace ( `app/src/main/AndroidManifest.xml` ). Obsah souboru nahraďte následujícím:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>Nastavení rozložení aplikace

Jsou vyžadovány dva vstupy: textový vstup pro ID volaného a tlačítko pro vložení volání. Ty lze přidat prostřednictvím návrháře nebo úpravou XML rozložení. Vytvoří tlačítko s ID `call_button` a textovým zadáním `callee_id` . Přejděte na ( `app/src/main/res/layout/activity_main.xml` ) a nahraďte obsah souboru následujícím:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Vytvoření generování a vazeb hlavní aktivity aktivity

Pomocí rozložení vytvořené vazby je možné přidat a také základní generování uživatelského rozhraní aktivity. Aktivita bude zpracovávat požadavky na běhová oprávnění, vytvoření agenta volání a umístění volání při stisknutí tlačítka. Každá z nich bude zahrnuta do vlastní části. `onCreate`Metoda bude přepsána k vyvolání `getAllPermissions` a `createAgent` také k přidání vazeb pro tlačítko volání. K tomu dojde pouze jednou při vytvoření aktivity. Další informace o nástroji `onCreate` najdete v příručce pro [pochopení životního cyklu aktivit](https://developer.android.com/guide/components/activities/activity-lifecycle).

Přejděte na **MainActivity. Java** a nahraďte obsah následujícím kódem:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
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
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> Při návrhu aplikace zvažte, že se tato oprávnění vyžadují. Oprávnění by se měla požadovat, když jsou potřeba, a ne předem. Další informace najdete v [příručce k oprávněním pro Android.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce komunikačních služeb Azure, které volají klientskou knihovnu:

| Název                                  | Popis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient je hlavní vstupní bod pro volání klientské knihovny.|
| CallAgent | CallAgent se používá ke spouštění a správě volání. |
| CommunicationUserCredential | CommunicationUserCredential se používá jako přihlašovací údaje tokenu pro vytvoření instance CallAgent.|
| CommunicationIdentifier | CommunicationIdentifier se používá jako jiný typ účastníka, který by mohl být součástí volání.|

## <a name="create-an-agent-from-the-user-access-token"></a>Vytvoření agenta z přístupového tokenu uživatele

S tokenem uživatele může být vytvořena instance ověřeného agenta volání. Obecně se tento token vygeneruje ze služby s ověřováním specifickým pro aplikaci. Další informace o tokenech přístupu uživatele najdete v příručce k [uživatelským tokenům](../../access-tokens.md) . V případě rychlého startu nahraďte `<User_Access_Token>` uživatelským tokenem, který jste vygenerovali pro váš prostředek služby Azure Communication Service.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>Spuštění volání pomocí agenta volání

Umístění volání lze provést prostřednictvím agenta volání a stačí pouze poskytnout seznam ID volaný a možnosti volání. Pro rychlý Start se použije výchozí možnosti volání bez videa a jedno volané ID z textového vstupu.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.call(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>Spusťte aplikaci a zavolejte robota s odezvou.

Aplikaci teď můžete spustit pomocí tlačítka Spustit aplikaci na panelu nástrojů (Shift + F10). Ověřte, že je možné volat voláním `8:echo123` . Předem zaznamenaná zpráva se spustí a znovu se vrátí zpět na vaši zprávu.

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="Snímek obrazovky znázorňující dokončenou aplikaci":::

## <a name="sample-code"></a>Příklad kódu

Ukázkovou aplikaci si můžete stáhnout z [GitHubu](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling) .
