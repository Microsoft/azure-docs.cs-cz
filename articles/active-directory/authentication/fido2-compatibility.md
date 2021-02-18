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
ms.openlocfilehash: a8ed99338a10eb226823c4bd4857d812038ff632
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094513"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Podpora prohlížeče pro FIDO2 ověřování s nehesly

Azure Active Directory povoluje použití [klíčů zabezpečení FIDO2](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-keys) jako zařízení s neplatným heslem. Dostupnost ověřování FIDO2 pro účty Microsoft byla [oznámena v 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Jak je popsáno v oznámení, musí být pro podporu zabezpečeného ověřování s účty Microsoft a Azure Active Directory implementovány některé volitelné funkce a rozšíření pro FIDO2 CTAP specifikace. Následující diagram ukazuje, které prohlížeče a kombinace operačních systémů podporují ověřování bez hesla pomocí ověřovacích klíčů FIDO2 s Azure Active Directory.

## <a name="supported-browsers"></a>Podporované prohlížeče

Tato tabulka ukazuje podporu ověřování Azure Active Directory (Azure AD) a účtů Microsoft (MSA). Účty Microsoft vytváří uživatelé pro služby, jako je Xbox, Skype nebo Outlook.com. Mezi podporované typy zařízení patří **USB**, bezkontaktní komunikace (**NFC**) a Bluetooth s nízkou spotřebou (v **poli).**

|  | Chrome |  |  | Edge |  |  | Firefox |  |  |
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

## <a name="operating-system-versions-tested"></a>Testované verze operačního systému

Informace v tabulce výše byly testovány pro následující verze operačního systému.

| Operační systém | Poslední testovaná verze |
| --- | --- |
| Windows | Windows 10 20H2 |
| macOS | OS X 11 Big Sur |
| Linux | Pracovní stanice Fedora 32 |

## <a name="next-steps"></a>Další kroky
[Povolit přihlašování k bezpečnostnímu klíči s nezabezpečenými hesly (Preview)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
