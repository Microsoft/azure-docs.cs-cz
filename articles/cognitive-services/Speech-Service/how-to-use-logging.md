---
title: Protokolování sady Speech SDK – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak povolit protokolování v sadě Speech SDK (C++, C#, Python, objektiv-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 32715ad1a01366d7d56e6fa8129151b15c315e1d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504171"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Povolení protokolování v sadě Speech SDK

Protokolování do souboru je volitelnou funkcí sady Speech SDK. Během protokolování vývoje jsou k dispozici další informace a diagnostika z klíčových součástí sady Speech SDK. Dá se povolit nastavením vlastnosti `Speech_LogFilename` u objektu konfigurace rozpoznávání řeči na umístění a název souboru protokolu. Protokolování je zpracováváno statickou třídou v nativní knihovně sady Speech SDK. Protokolování můžete zapnout pro libovolnou funkci rozpoznávání sady Speech SDK nebo instance syntetizátoru. Všechny instance ve stejném procesu zapisují položky protokolu do stejného souboru protokolu.

> [!NOTE]
> K dispozici je protokolování, protože sada Speech SDK verze 1.4.0 ve všech podporovaných programovacích jazycích sady Speech SDK s výjimkou JavaScriptu.

## <a name="sample"></a>Ukázka

Název souboru protokolu je zadán v objektu konfigurace. Vezměte `SpeechConfig` jako příklad a za předpokladu, že jste vytvořili instanci s názvem `config` :

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Nástroj pro rozpoznávání můžete vytvořit z konfiguračního objektu. Tím se povolí protokolování pro všechny nástroje pro rozpoznávání.

> [!NOTE]
> Pokud vytvoříte `SpeechSynthesizer` z konfiguračního objektu, nebude protokolování povoleno. Pokud je povoleno protokolování v případě, budete také dostávat diagnostiku z `SpeechSynthesizer` .

## <a name="create-a-log-file-on-different-platforms"></a>Vytvoření souboru protokolu na různých platformách

V případě systému Windows nebo Linux může být soubor protokolu v jakékoli cestě, ke které má uživatel oprávnění k zápisu. Oprávnění k zápisu do umístění systému souborů v jiných operačních systémech můžou být ve výchozím nastavení omezená nebo omezená.

### <a name="universal-windows-platform-uwp"></a>Univerzální platforma Windows (UPW)

Aplikace UWP musí v jednom z umístění dat aplikace umístit soubory protokolu (místní, roaming nebo dočasné). Soubor protokolu se dá vytvořit v místní složce aplikace:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Další informace o oprávněních pro přístup k souborům pro aplikace UWP jsou k dispozici [zde](/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Soubor protokolu můžete uložit do interního úložiště, externího úložiště nebo adresáře mezipaměti. Soubory vytvořené v interním úložišti nebo v adresáři mezipaměti jsou pro aplikaci privátní. Je vhodnější vytvořit soubor protokolu v externím úložišti.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Výše uvedený kód uloží soubor protokolu do externího úložiště v kořenovém adresáři adresáře, který je specifický pro aplikaci. Uživatel má k souboru přístup pomocí Správce souborů (obvykle v aplikaci `Android/data/ApplicationName/logfile.txt` ). Soubor bude odstraněn při odinstalaci aplikace.

Musíte taky požádat o `WRITE_EXTERNAL_STORAGE` oprávnění v souboru manifestu:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Další informace o datech a úložištích souborů pro aplikace pro Android jsou k dispozici [zde](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

K dispozici jsou pouze adresáře v izolovaném prostoru aplikace. Soubory lze vytvořit v adresáři dokumenty, knihovny a dočasné adresáře. Soubory v adresáři dokumentů lze zpřístupnit uživateli. Následující fragment kódu ukazuje vytvoření souboru protokolu v adresáři dokumentu aplikace:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Chcete-li získat přístup k vytvořenému souboru, přidejte níže uvedené vlastnosti do `Info.plist` seznamu vlastností aplikace:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Další informace o systému souborů iOS najdete [tady](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)