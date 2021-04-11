---
title: Připojení k virtuálnímu počítači s Windows 10 nebo 7 – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí desktopového klienta Windows
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 625662a6b67e7d30e6320fe7831e4fa7793b9c30
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447875"
---
# <a name="connect-with-the-windows-desktop-client"></a>Připojení s desktopovým klientem Windows

K prostředkům virtuálních klientů Windows se můžete dostat na zařízeních s Windows 10, Windows 10 IoT Enterprise a Windows 7 pomocí desktopového klienta Windows. 

> [!IMPORTANT]
> To nepodporuje Window 8 ani Windows 8.1.
> 
> To podporuje jenom Azure Resource Manager objekty, aby se podporovaly objekty bez Azure Resource Manager, přečtěte si téma [připojení pomocí klienta Windows Desktop (Classic)](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
> 
> Tato funkce nepodporuje klienta připojení k aplikacím RemoteApp a vzdálené ploše (RADC) ani klienta Připojení ke vzdálené ploše (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Instalace klienta pro stolní počítače se systémem Windows

Stáhněte klienta na základě vaší verze Windows:

- [Windows 64 – bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 – bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [ARM64 Windows](https://go.microsoft.com/fwlink/?linkid=2098961)

Během instalace pro určení přístupu vyberte jednu z těchto akcí:

- **Nainstalovat hned za vás**
- **Nainstalovat pro všechny uživatele tohoto počítače** (vyžaduje oprávnění správce)

Chcete-li po instalaci spustit klienta, použijte nabídku **Start** a vyhledejte **vzdálenou plochu**.

## <a name="subscribe-to-a-workspace"></a>Přihlášení k odběru pracovního prostoru

Pokud se chcete přihlásit k odběru pracovního prostoru, vyberte jednu z těchto akcí:

- Použijte pracovní nebo školní účet a načtěte si klienta, který bude vyhledávat dostupné prostředky.
- Použít konkrétní adresu URL prostředku

Pokud chcete prostředek spustit po přihlášení k odběru, přejděte do **centra připojení** a dvakrát klikněte na prostředek.

> [!TIP]
> Pokud chcete spustit prostředek z nabídky **Start** , můžete najít složku s názvem pracovního prostoru nebo zadat název prostředku do vyhledávacího panelu.

### <a name="use-a-user-account"></a>Použití uživatelského účtu

1. Na hlavní stránce vyberte **Přihlásit** se.
1. Po zobrazení výzvy se přihlaste pomocí svého uživatelského účtu.

Prostředky seskupené podle pracovního prostoru se zobrazí v **centru připojení**.

   > [!NOTE]
   > Klient Windows se automaticky nastaví jako výchozí pro virtuální plochu Windows (Classic). 
   > 
   > Pokud však klient zjistí další Azure Resource Manager prostředky, přidá je automaticky nebo upozorní uživatele, že jsou k dispozici.

### <a name="use-a-specific-url"></a>Použít konkrétní adresu URL

1. Na hlavní stránce vyberte **Přihlásit se k odběru pomocí adresy URL** .
1. Zadejte buď *adresu URL pracovního prostoru* , nebo *e-mailovou adresu*:
   - V poli **Adresa URL pracovního prostoru** použijte adresu URL poskytnutou vaším správcem.

   |Dostupné prostředky|URL|
   |-|-|
   |Windows Virtual Desktop (klasická verze)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Windows Virtual Desktop|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Virtuální počítač s Windows (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   
   - K **e-mailu** použijte svou e-mailovou adresu. 
      
   Klient zjistí adresu URL přidruženou k vašemu e-mailu za předpokladu, že váš správce povolil [zjišťování e-mailů](/windows-server/remote/remote-desktop-services/rds-email-discovery).

1. Vyberte **Další**.
1. Po zobrazení výzvy se přihlaste pomocí svého uživatelského účtu.

Prostředky seskupené podle pracovního prostoru se zobrazí v **centru připojení**.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat klienta, najdete v tématu [Začínáme s klientem pro stolní počítače se systémem Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).

Pokud si správce zajímá více o funkcích klienta, Projděte si [klienta Windows Desktop pro správce](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).
