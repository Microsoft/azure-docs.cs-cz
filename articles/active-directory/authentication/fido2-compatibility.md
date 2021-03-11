---
title: Podpora prohlížeče pro FIDO2 ověřování s heslem | Azure Active Directory
description: Prohlížeče a kombinace operačních systémů podporují FIDO2 ověřování pomocí hesla pro aplikace, které používají Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632097"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Podpora prohlížeče pro FIDO2 ověřování s nehesly

Azure Active Directory povoluje použití [klíčů zabezpečení FIDO2](./concept-authentication-passwordless.md#fido2-security-keys) jako zařízení s neplatným heslem. Dostupnost ověřování FIDO2 pro účty Microsoft byla [oznámena v 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Jak je popsáno v oznámení, musí být implementovány některé volitelné funkce a rozšíření FIDO2 CTAP pro podporu zabezpečeného ověřování pomocí účtů Microsoft a Azure Active Directory. Následující diagram ukazuje, které prohlížeče a kombinace operačních systémů podporují ověřování bez hesla pomocí ověřovacích klíčů FIDO2 s Azure Active Directory.

## <a name="supported-browsers"></a>Podporované prohlížeče

Tato tabulka ukazuje podporu ověřování Azure Active Directory (Azure AD) a účtů Microsoft (MSA). Účty Microsoft vytváří uživatelé pro služby, jako je Xbox, Skype nebo Outlook.com. Mezi podporované typy zařízení patří **USB**, bezkontaktní komunikace (**NFC**) a Bluetooth s nízkou spotřebou (v **poli).**

| Operační systém | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | Bulk | USB | NFC | Bulk | USB | NFC | Bulk |
| **Windows**  | ![Chrome podporuje rozhraní USB ve Windows pro účty AAD.][y] | ![Chrome podporuje NFC ve Windows pro účty AAD.][y] | ![Chrome podporuje tabulku v systému Windows pro účty AAD.][y] | ![Edge podporuje USB ve Windows pro účty AAD.][y] | ![Edge podporuje NFC v systému Windows pro účty AAD.][y] | ![Edge podporuje tabulku v systému Windows pro účty AAD.][y] | ![Firefox podporuje USB ve Windows pro účty AAD.][y] | ![Firefox podporuje NFC ve Windows pro účty AAD.][y] | ![Firefox podporuje tabulku ve Windows pro účty AAD.][y] |
| **macOS**  | ![Chrome podporuje rozhraní USB v macOS pro účty AAD.][y] | ![Chrome nepodporuje NFC v macOS pro účty AAD.][n] | ![Chrome nepodporuje tabulku macOS pro účty AAD.][n] | ![Edge podporuje USB v macOS pro účty AAD.][y] | ![Edge nepodporuje NFC v macOS pro účty AAD.][n] | ![Edge nepodporuje tabulku macOS pro účty AAD.][n] | ![Firefox nepodporuje USB v macOS pro účty AAD.][n] | ![Firefox nepodporuje NFC v macOS pro účty AAD.][n] | ![Firefox nepodporuje tabulku macOS pro účty AAD.][n] |
| **Linux**  | ![Chrome podporuje rozhraní USB v systému Linux pro účty AAD.][y] | ![Chrome nepodporuje NFC v systémech Linux pro účty AAD.][n] | ![Chrome nepodporuje tabulku v systému Linux pro účty AAD.][n] | ![Edge nepodporuje pro účty AAD služby USB v systému Linux.][n] | ![Edge nepodporuje NFC v systémech Linux pro účty AAD.][n] | ![Edge nepodporuje tabulku v systému Linux pro účty AAD.][n] | ![Firefox nepodporuje pro účty AAD služby USB v systému Linux.][n] | ![Firefox nepodporuje NFC v systémech Linux pro účty AAD.][n] | ![Firefox nepodporuje tabulku v systému Linux pro účty AAD.][n] |



## <a name="unsupported-browsers"></a>Nepodporované prohlížeče

Následující kombinace operačních systémů a prohlížečů nejsou podporovány, ale probíhá šetření budoucí podpory a testování. Pokud chcete zobrazit další podpora operačního systému a prohlížeče, zachovejte prosím svůj názor pomocí nástroje pro zpětnou vazbu k produktu v dolní části stránky.

| Operační systém | Prohlížeč |
| ---- | ---- |
| iOS | Safari, Brave |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>Minimální verze prohlížeče

Níže jsou uvedené minimální požadavky na verzi prohlížeče. 

| Prohlížeč | Minimální verze |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 verze 1903<sup>1</sup> |
| Firefox | 66 |

<sup>1</sup> Všechny verze nového Microsoft Edge na bázi Chromu podporují Fido2. Do 1903 se přidala podpora pro Microsoft Edge starší verze.

## <a name="next-steps"></a>Další kroky
[Povolit přihlašování k bezpečnostnímu klíči s nezabezpečenými hesly (Preview)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
