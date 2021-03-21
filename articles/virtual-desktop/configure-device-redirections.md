---
title: Konfigurace přesměrování zařízení – Azure
description: Jak nakonfigurovat přesměrování zařízení pro virtuální počítač s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b60a6e53e8fecd71885204690231776ff69fc08f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018371"
---
# <a name="configure-device-redirections"></a>Konfigurace přesměrování zařízení

Konfigurace přesměrování zařízení pro prostředí virtuálních počítačů s Windows umožňuje používat tiskárny, zařízení USB, mikrofony a další periferní zařízení ve vzdálené relaci. Některé přesměrování zařízení vyžadují změny vlastností protokol RDP (Remote Desktop Protocol) (RDP) i Zásady skupiny nastavení.

## <a name="supported-device-redirections"></a>Podporovaná přesměrování zařízení

Každý klient podporuje různé přesměrování zařízení. Úplný seznam podporovaných přesměrování zařízení pro každého klienta najdete v části [porovnání klientů](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) .

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Přizpůsobení vlastností protokolu RDP pro fond hostitelů

Další informace o přizpůsobení vlastností protokolu RDP pro fond hostitelů pomocí PowerShellu nebo Azure Portal najdete v části [Vlastnosti protokolu RDP](customize-rdp-properties.md). Úplný seznam podporovaných vlastností protokolu RDP najdete v části [podporované nastavení souboru RDP](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Nastavit přesměrování zařízení

K nakonfigurování přesměrování zařízení můžete použít následující vlastnosti protokolu RDP a nastavení Zásady skupiny.

### <a name="audio-input-microphone-redirection"></a>Přesměrování vstupu zvuku (mikrofon)

Nastavte následující vlastnost RDP pro konfiguraci přesměrování zvukového vstupu:

- `audiocapturemode:i:1` povolí přesměrování zvukového vstupu.
- `audiocapturemode:i:0` Zakáže přesměrování zvukového vstupu.

### <a name="audio-output-speaker-redirection"></a>Přesměrování zvukového výstupu (mluvčí)

Pro konfiguraci přesměrování výstupu zvuku nastavte následující vlastnost RDP:

- `audiomode:i:0` povolí přesměrování zvukového výstupu.
- `audiomode:i:1` nebo `audiomode:i:2` Zakažte přesměrování výstupu zvuku.

### <a name="camera-redirection"></a>Přesměrování kamery

Pro konfiguraci přesměrování kamery nastavte následující vlastnost RDP:

- `camerastoredirect:s:*` přesměruje všechny kamery.
- `camerastoredirect:s:` Zakáže přesměrování kamery.

>[!NOTE]
>I v případě `camerastoredirect:s:` , že je vlastnost zakázaná, mohou být místní fotoaparáty přesměrovány prostřednictvím `devicestoredirect:s:` Vlastnosti. Chcete-li úplně zakázat nastavení přesměrování kamery `camerastoredirect:s:` a buď nastavit `devicestoredirect:s:` nebo definovat některé podmnožiny zařízení Plug and Play, která neobsahují fotoaparát.

Můžete také přesměrovat konkrétní kamery pomocí středníku seznamu KSCATEGORY_VIDEO_CAMERA rozhraní, například `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` . 

### <a name="clipboard-redirection"></a>Přesměrování schránky

Pro konfiguraci přesměrování schránky nastavte následující vlastnost RDP:

- `redirectclipboard:i:1` povolí přesměrování schránky.
- `redirectclipboard:i:0` Zakáže přesměrování schránky.

### <a name="com-port-redirections"></a>Přesměrování portu COM

Pro konfiguraci přesměrování portu COM nastavte následující vlastnost RDP:

- `redirectcomports:i:1` povolí přesměrování portu COM.
- `redirectcomports:i:0` Zakáže přesměrování portu COM.

### <a name="usb-redirection"></a>Přesměrování USB

Nejdřív nastavte následující vlastnost RDP tak, aby umožňovala přesměrování zařízení USB:

- `usbdevicestoredirect:s:*` povolí přesměrování zařízení USB.
- `usbdevicestoredirect:s:` Zakáže přesměrování zařízení USB.

Potom na místním zařízení uživatele nastavte následující Zásady skupiny:

- Přejděte k   >  **zásadám** konfigurace počítače >  **šablony pro správu**  >  **součásti systému Windows**  >  **Vzdálená plocha**  >  **připojení ke vzdálené ploše klient**  >  **RemoteFX USB přesměrování zařízení**.
- Vyberte možnost **Povolit přesměrování protokolu RDP dalších podporovaných zařízení USB RemoteFX z tohoto počítače**.
- Vyberte možnost **povoleno** a potom zaškrtněte políčko **Správci a uživatelé v seznamu přístupová práva přesměrování USB RemoteFX** .
- Vyberte **OK**.

### <a name="plug-and-play-device-redirection"></a>Přesměrování zařízení Plug and Play

Pro konfiguraci přesměrování zařízení Plug and Play nastavte následující vlastnost RDP:

- `devicestoredirect:s:*` povolí přesměrování všech zařízení Plug and Play.
- `devicestoredirect:s:` Zakáže přesměrování zařízení Plug and Play.

Můžete také vybrat konkrétní zařízení Plug and Play pomocí seznamu středníkem oddělených, například `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Přesměrování místního disku

Pro konfiguraci přesměrování místní jednotky nastavte následující vlastnost RDP:

- `drivestoredirect:s:*` povolí přesměrování všech diskových jednotek.
- `drivestoredirect:s:` Zakáže přesměrování na místní disk.

Můžete také vybrat konkrétní jednotky pomocí seznamu středníkem oddělených, například `drivestoredirect:s:C:;E:;` .

### <a name="printer-redirection"></a>Přesměrování tiskárny

Pro konfiguraci přesměrování tiskárny nastavte následující vlastnost RDP:

- `redirectprinters:i:1` povolí přesměrování tiskárny.
- `redirectprinters:i:0` Zakáže přesměrování tiskárny.

### <a name="smart-card-redirection"></a>Přesměrování čipové karty

Pro konfiguraci přesměrování čipové karty nastavte následující vlastnost RDP:

- `redirectsmartcards:i:1` povolí přesměrování čipové karty.
- `redirectsmartcards:i:0` Zakáže přesměrování čipové karty.