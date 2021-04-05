---
title: Připojení k virtuálnímu počítači s Windows 10 nebo 7 – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí desktopového klienta Windows
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 59379dd9c3a41729466de269b52dfd3fb206eea9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368711"
---
# <a name="connect-with-the-windows-desktop-client"></a>Připojení s desktopovým klientem Windows

> Platí pro: Windows 10, Windows 10 IoT Enterprise a Windows 7.

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).

K prostředkům virtuálních klientů Windows se můžete dostat na zařízeních s Windows 10, Windows 10 IoT Enterprise a Windows 7 pomocí desktopového klienta Windows. Klient nepodporuje Window 8 ani Windows 8.1.

## <a name="install-the-windows-desktop-client"></a>Instalace klienta pro stolní počítače se systémem Windows

Vyberte klienta, který odpovídá vaší verzi Windows:

- [Windows 64 – bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 – bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [ARM64 Windows](https://go.microsoft.com/fwlink/?linkid=2098961)

Klienta můžete nainstalovat pro aktuálního uživatele, který nevyžaduje oprávnění správce, nebo může správce nainstalovat a nakonfigurovat klienta tak, aby k němu měli přístup všichni uživatelé zařízení.

Po instalaci je možné klienta spustit z nabídky Start, a to tak, že vyhledáte **vzdálenou plochu**.

> [!IMPORTANT]
> Virtuální počítač s Windows nepodporuje klienta připojení k aplikacím RemoteApp a vzdálené ploše (RADC) ani klienta Připojení ke vzdálené ploše (MSTSC).

## <a name="subscribe-to-a-workspace"></a>Přihlášení k odběru pracovního prostoru

Existují dva způsoby, jak se můžete přihlásit k odběru pracovního prostoru. Klient se může pokusit zjistit prostředky, které vám k dispozici z pracovního nebo školního účtu, nebo můžete přímo zadat adresu URL, kde se vaše prostředky nacházejí v případech, kdy je klient nemůže najít. Jakmile se přihlásíte k odběru pracovního prostoru, můžete spustit prostředky jedním z následujících způsobů:

- Přejděte do centra připojení a dvojím kliknutím na prostředek ho spusťte.
- Můžete také přejít do nabídky Start a vyhledat složku s názvem pracovního prostoru nebo zadat název prostředku do vyhledávacího panelu.

### <a name="subscribe-with-a-user-account"></a>Přihlášení k odběru pomocí uživatelského účtu

1. Na hlavní stránce klienta vyberte možnost **přihlásit k odběru**.
2. Po zobrazení výzvy se přihlaste pomocí svého uživatelského účtu.
3. Prostředky se zobrazí v centru připojení a jsou seskupeny podle pracovního prostoru.

>[!NOTE]
>Klient Windows se automaticky nastaví jako výchozí pro virtuální plochu Windows (Classic). Pokud však klient zjistí, že uživatel má také prostředky Azure Resource Manager, automaticky přidá prostředky nebo upozorní uživatele, že jsou k dispozici.

### <a name="subscribe-with-a-url"></a>Přihlášení k odběru pomocí adresy URL

1. Na hlavní stránce klienta vyberte možnost **Přihlásit se k odběru pomocí adresy URL**.
2. Zadejte adresu URL pracovního prostoru nebo svou e-mailovou adresu:
   - Pokud použijete **adresu URL pracovního prostoru**, použijte toho, kterého vám správce poskytl. Při přístupu k prostředkům z virtuálního počítače s Windows můžete použít jednu z těchto adres URL:
     - Virtuální plocha Windows (Classic): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Virtuální počítač s Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
     - Virtuální počítač s Windows (US Gov): `https://rdweb.wvd.azure.us/api/arm/feeddiscovery`
   - Pokud místo toho použijete pole **e-mail** , zadejte svou e-mailovou adresu. To klientovi oznamuje, aby vyhledal adresu URL přidruženou k vaší e-mailové adrese, pokud váš správce nastavil [zjišťování e-mailů](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Vyberte **Další**.
4. Po zobrazení výzvy se přihlaste pomocí svého uživatelského účtu.
5. Prostředky by se měly zobrazit v centru připojení seskupených podle pracovního prostoru.

## <a name="next-steps"></a>Další kroky

Další informace o použití desktopového klienta Windows najdete v tématu [Začínáme s klientem Windows pro stolní počítače](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).

Pokud jste správce, který vás zajímá o podrobnější informace o použití plochy Windows, přečtěte si článek [klient Windows Desktop pro správce](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).
