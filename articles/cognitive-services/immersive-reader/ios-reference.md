---
title: Referenční dokumentace sady moderní čtečky pro iOS
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro sadu iOS SDK pro moderní čtečku
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 1f85740c358bd0949fed9c954537f0926538995d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388118"
---
# <a name="immersive-reader-sdk-reference-for-ios"></a>Referenční dokumentace sady pro moderní čtečku pro iOS

Moderní čtečka SDK pro iOS je CocoaPod, která umožňuje integrovat moderní čtečku do vaší aplikace pro iOS.

## <a name="functions"></a>Functions

Sada SDK zpřístupňuje jedinou funkci `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`.

### <a name="launchimmersivereader"></a>launchImmersiveReader

Spustí moderní čtečku spuštěním kontroleru zobrazení v aplikaci pro iOS.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>Parametry

| Name (Název) | Typ | Popis |
| ---- | ---- |------------ |
| `navController` | UINavigationController | Navigační kontroler pro aplikaci iOS, ze které se volá funkce |
| `token` | Řetězec | Ověřovací token Azure AD. Podívejte se na [postupy ověřování Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | Řetězec | Vlastní subdoména prostředku pro moderní čtečku v Azure. Podívejte se na [postupy ověřování Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Obsah](#content) | Objekt obsahující obsah, který se má zobrazit v moderní čtečce. |
| `options` | [Možnosti](#options) | Možnosti pro konfiguraci určitého chování moderního čtecího zařízení. Volitelné. |
| `onSuccess` | ()-> void | Uzávěr, který je vyvolán při úspěšném spuštění moderního čtecího zařízení. |
| `onFailure` | (chyba _: [Chyba](#error))-> void | Uzávěr, který je vyvolán, když se nepovede načíst moderní čtecí zařízení. Tento uzávěr vrátí objekt [`Error`](#error) , který představuje kód chyby a chybovou zprávu přidruženou k selhání. Další informace najdete v tématu [kódy chyb](#error-codes). |

## <a name="types"></a>Druhy

### <a name="content"></a>Obsah

Obsahuje obsah, který se zobrazí v moderní čtečce.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>Podporované typy MIME

| Typ MIME | Popis |
| --------- | ----------- |
| Text/prostý | Prostý text. |
| Application/MathML + XML | Jazyk MathML (Matematická Markup Language). [Další informace](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Možnosti

Obsahuje vlastnosti, které konfigurují určité chování moderního čtecího zařízení.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Chyba

Obsahuje informace o chybě.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>Kódy chyb

| Kód | Popis |
| ---- | ----------- |
| BadArgument | Zadaný argument je neplatný, podrobnosti naleznete `message`. |
| prodlev | V rámci zadaného časového limitu se nepovedlo načíst moderní čtečku. |
| TokenExpired | Platnost zadaného tokenu vypršela. |
| Omezené | Překročilo se omezení četnosti volání. |
| InternalError | Došlo k vnitřní chybě v rámci kontroleru zobrazení moderního čtečky. Podrobnosti najdete v tématu `message`.|

## <a name="os-version-support"></a>Podpora verzí operačního systému

Sada moderní čtečky iOS SDK je podporovaná pro iOS 9,0 nebo novější, na iPadu a na iPhonu.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu iOS SDK pro moderní čtečku na GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [Rychlý Start: Vytvoření aplikace pro iOS, která spustí moderní čtečku (SWIFT)](./ios-quickstart.md)