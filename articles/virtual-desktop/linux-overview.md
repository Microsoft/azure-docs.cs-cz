---
title: Linux Support virtuálních počítačů s Windows – Azure
description: Stručné informace o podpoře pro Linux pro virtuální počítače s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f91e130bfa83c6a9b116c05d7293aa70945e2dc2
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903540"
---
# <a name="linux-support"></a>Podpora Linuxu

K prostředkům virtuálních klientů s Windows můžete přistupovat ze zařízení se systémem Linux pomocí následujících podporovaných klientů, které poskytují naši klienti se systémem Linux tenkých klientů. Spolupracujeme s řadou partnerů, aby bylo možné podporovat Podporované klienty virtuálních počítačů s Windows na dalších operačních systémech a zařízeních se systémem Linux. Pokud budete chtít, aby virtuální počítač s Windows podporoval platformu Linux, která zde není uvedená, dejte nám prosím na naší [stránce UserVoice](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux)informace.

## <a name="connect-with-your-linux-device"></a>Připojení k zařízení se systémem Linux

Následující partneři mají schválená klienta virtuální plochy Windows pro Linux.

|Partner|Dokumentace k partnerům|Partnerská podpora|
|:------|:--------------------|:--------------|
|![Logo IGEL](./media/partners/igel.png)|[Dokumentace ke klientovi IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Podpora IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Co je sada Linux SDK?

Klienti se systémem Linux tenké klientů mohou pomocí rozhraní API sady Windows Virtual Desktop Linux SDK načíst kanály prostředků, připojit se k počítači nebo relace vzdálené aplikace a použít spoustu přesměrování, která naši klienti podporují. Sada SDK je kompatibilní s největším operačním systémem, který je založený na Ubuntu 18,04 nebo novějším.

### <a name="feature-support"></a>Podpora funkcí

Sada SDK podporuje více připojení k relacím na plochu a vzdálené aplikace. Jsou podporovány následující přesměrování:

| Přesměrování       | Podporováno |
| :---------------- | :-------: |
| Klávesnice          | &#10004;  |
| Myš             | &#10004;  |
| Zvuk v          | &#10004;  |
| Zvukový výstup         | &#10004;  |
| Schránka (text)  | &#10004;  |
| Schránka (obrázek) | &#10004;  |
| Schránka (soubor)  | &#10004;  |
| Pomocí         | &#10004;  |
| Jednotka nebo složka      | &#10004;  |

Sada SDK také podporuje více konfigurací zobrazení monitorování, pokud jsou monitory, které vyberete pro vaši relaci, souvislé.

Tento dokument aktualizujeme, protože přidáváme podporu pro nové funkce a přesměrování. Pokud chcete navrhnout nové funkce a další vylepšení, navštivte [stránku UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="next-steps"></a>Další kroky

Projděte si naši dokumentaci pro následující klienty:

- [Desktopový klient Windows](connect-windows-7-10.md)
- [Webový klient](connect-web.md)
- [Klient pro Android](connect-android.md)
- [Klient pro macOS](connect-macos.md)
- [Klient pro iOS](connect-ios.md)
