---
title: Použití profilů Wi-Fi se zařízeními Azure Stack Edge Mini R
description: Popisuje, jak vytvořit profily Wi-Fi pro zařízení Azure Stack Edge Mini R na vysoce zabezpečených podnikových sítích a v osobních sítích.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050449"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Použití profilů Wi-Fi se zařízeními Azure Stack Edge Mini R

Tento článek popisuje, jak používat profily bezdrátové sítě (Wi-Fi) s Azure Stack hraničních zařízení v R.

Postup přípravy Wi-Fi profilu závisí na typu bezdrátové sítě:

- V případě osobní sítě Wi-Fi Protected Access 2 (WPA2), jako je například domácí síť nebo Wi-Fi otevřené hotspoty, budete moci stáhnout a používat stávající profil bezdrátové sítě se stejným heslem, které používáte s ostatními zařízeními.

- V podnikovém prostředí s vysokým zabezpečením budete mít přístup k zařízení přes síť WPA2-Enterprise. U tohoto typu sítě má každý klientský počítač odlišný profil Wi-Fi a bude ověřený prostřednictvím certifikátů. Aby bylo možné určit požadovanou konfiguraci, budete muset spolupracovat se správcem vaší sítě.

Pojednává o požadavcích na profilování pro oba typy sítě později.

V obou případech je velmi důležité zajistit, aby profil splňoval požadavky na zabezpečení vaší organizace předtím, než otestujete nebo použijete profil s vaším zařízením.

## <a name="about-wi-fi-profiles"></a>Informace o Wi-Fich profilech

Profil Wi-Fi obsahuje identifikátor SSID (Service Set **Identifier),** klíč hesla a informace o zabezpečení potřebné k připojení Azure Stack hraničního zařízení R k bezdrátové síti.

Následující příklad kódu ukazuje základní nastavení profilu, který se má použít v typické bezdrátové síti:

* `SSID` je název sítě.

* `name` je uživatelsky přívětivý název pro Wi-Fi připojení. To je jméno, které uživatelé uvidí při procházení dostupných připojení na svém zařízení.

* Profil je nakonfigurován tak, aby automaticky připojil počítač k bezdrátové síti, pokud je v dosahu sítě ( `connectionMode`  =  `auto` ).

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Další informace o nastavení profilu Wi-Fi najdete v tématu **profil rozlehlé sítě** v článku [Přidání nastavení Wi-Fi pro zařízení s Windows 10 a novějším](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)a v tématu [Konfigurace profilu Cisco Wi-Fi](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Pokud chcete povolit bezdrátová připojení na Azure Stack hraničních zařízeních v R, nakonfigurujete port Wi-Fi na zařízení a pak do zařízení přidáte Wi-Fié profily. V podnikové síti nahrajete do zařízení také certifikáty. Pak se můžete připojit k bezdrátové síti z místního webového uživatelského rozhraní pro dané zařízení. Další informace najdete v tématu [Správa bezdrátového připojení na Azure Stack hraničním zařízení Mini R](./azure-stack-edge-mini-r-manage-wifi.md).

## <a name="profile-for-wpa2---personal-network"></a>Profil pro WPA2-osobní síť

U osobní sítě Wi-Fi Protected Access 2 (WPA2), jako je například domácí síť nebo Wi-Fi otevřené hotspoty, může používat stejný profil a stejné heslo. V domácí síti váš mobilní telefon a přenosný počítač používají pro připojení k síti stejný bezdrátový profil a heslo.

Například klient Windows 10 může pro vás vytvořit profil modulu runtime. Po přihlášení k bezdrátové síti budete vyzváni k zadání hesla Wi-Fi a po zadání tohoto hesla jste připojeni. V tomto prostředí není nutný žádný certifikát.

V tomto typu sítě budete moct exportovat profil Wi-Fi z vašeho přenosného počítače a pak ho přidat do mobilního zařízení v Azure Stack hraničních sítích R. Pokyny najdete v tématu [Export profilu Wi-Fi](#export-a-wi-fi-profile)níže.

> [!IMPORTANT]
> Před vytvořením profilu Wi-Fi pro zařízení s Azure Stack hraničním připojením v jazyce R se obraťte na správce sítě a zjistěte, jaké jsou požadavky na zabezpečení pro bezdrátové sítě v organizaci. Neměli byste na svém zařízení testovat nebo používat žádný profil Wi-Fi, dokud nebudete znáte, že bezdrátová síť splňuje požadavky.

## <a name="profiles-for-wpa2---enterprise-network"></a>Profily pro WPA2-podnikové sítě

V síti Wireless Protected Access 2 (WPA2) – Enterprise budete muset spolupracovat se správcem sítě a získat potřebný Wi-Fi profil a certifikát pro připojení Azure Stack hraničního zařízení R k síti.

V případě vysoce zabezpečených sítí může zařízení Azure používat protokol PEAP (Protected Extensible Authentication Protocol) s rozšiřitelným ověřováním pomocí protokolu EAP-TLS (Extensible Authentication Protocol-Transport Layer Security). Protokol PEAP s protokolem EAP-TLS používá ověřování počítače: klient a server používají certifikáty k vzájemnému ověření svých identit.

> [!NOTE]
> * Ověřování uživatelů pomocí protokolu PEAP Microsoft Challenge Handshake Authentication Protocol verze 2 (PEAP MSCHAPv2) není podporováno na Azure Stack hraničních zařízeních R.
> * Aby bylo možné získat přístup k funkci Azure Stackového Mini R, je vyžadováno ověřování EAP-TLS. Bezdrátové připojení, které jste nastavili pomocí služby Active Directory, nebude fungovat.

Správce sítě bude generovat jedinečný profil Wi-Fi a klientský certifikát pro každý počítač. Správce sítě rozhodne, zda použít samostatný certifikát pro každé zařízení nebo sdílený certifikát.

Pokud pracujete ve více než jednom fyzickém umístění na pracovišti, může správce sítě pro bezdrátová připojení poskytnout více než jeden profil Wi-Fi pro konkrétní lokalitu a certifikát.

V podnikové síti doporučujeme, abyste nastavení neměnili v profilech Wi-Fi, které poskytuje správce sítě. Jedinou úpravou, kterou budete chtít udělat, je nastavení automatického připojení. Další informace najdete v tématu [základní profil](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) v tématu Nastavení Wi-Fi pro Windows 10 a novější zařízení.

V podnikovém prostředí s vysokým zabezpečením je možné použít stávající profil bezdrátové sítě jako šablonu:

* Profil podnikové bezdrátové sítě si můžete stáhnout z pracovního počítače. Pokyny najdete v tématu [Export profilu Wi-Fi](#export-a-wi-fi-profile)níže.

* Pokud se ostatní ve vaší organizaci už v bezdrátové síti připojují ke svým Azure Stackm minimu zařízení R, můžou si stáhnout profil Wi-Fi ze svého zařízení. Pokyny najdete v tématu [stažení Wi-Fi Profile](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Export profilu Wi-Fi

Chcete-li exportovat profil pro rozhraní Wi-Fi v počítači, proveďte tyto kroky:

1. Bezdrátové profily v počítači zobrazíte tak, že v nabídce **Start** otevřete **příkazový řádek** (cmd.exe) a zadáte tento příkaz:

   `netsh wlan show profiles`

   Výstup bude vypadat zhruba takto:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. Pokud chcete exportovat profil, zadejte následující příkaz:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   Například následující příkaz uloží profil ContosoFTINET ve formátu XML do složky Staženés pro uživatele s názvem `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Přidat certifikát, Wi-Fi profil do zařízení

Po Wi-Fi profilů a certifikátů, které potřebujete, proveďte tyto kroky a nakonfigurujte Azure Stack hraničního zařízení R pro bezdrátová připojení:

1. V případě sítě WPA2-podnikové nahrajte na zařízení potřebné certifikáty podle pokynů v části [nahrávání certifikátů](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Nahrajte profil Wi-Fi na Mini zařízení R a pak se k němu připojte podle pokynů v části [Přidání, připojení k Wi-Fi profilu](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

## <a name="next-steps"></a>Další kroky

- Naučte se [Konfigurovat síť pro Azure Stack hraniční Mini R](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Naučte se, jak [spravovat Wi-Fi na Azure Stack hraničních počítačích na Mini R](azure-stack-edge-mini-r-manage-wifi.md).
