---
title: Známé problémy s Azure Percept
description: Další informace o známých problémech Azure Percept a jejich řešení
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: 49ac497505930d82a3ab8e90fb3f386cc1741dc7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605055"
---
# <a name="known-issues"></a>Známé problémy

Pokud narazíte na některý z těchto problémů, není nutné otevřít chybu. Pokud máte problémy s některým z alternativních řešení, otevřete prosím problém.

|Plošný|Popis problému|Alternativní řešení|
|-------|---------|---------|
| Prostředí připojování | Nejde dokončit prostředí připojování, pokud není nakonfigurované Wi-Fi zařízení (přihlášení k Azure se nepodaří). | 1. [SSH do vaší služby Azure PERCEPT DK](./how-to-ssh-into-percept-dk.md). <br> 2. Identifikujte a zkopírujte IP adresu sítě Ethernet pro zařízení. <br> 3. Připojte se k prostředí pro registraci pomocí adresy URL založené na síti Ethernet. |
| Prostředí připojování | Kliknutím na odkazy v smlouvě EULA (licenční smlouva) se někdy neotevře nová webová stránka. | Zkopírujte odkaz a otevřete ho v samostatném okně prohlížeče. |
| Prostředí připojování | Při připojení k mobilnímu Wi-Fi hotspotu nejde pracovat prostřednictvím prostředí pro připojování. | Připojte zařízení přímo k SoftAP, Wi-Fi síti nebo k síti přes Ethernet. |
| Wi-Fi | SoftAP se někdy může odpojit nebo zmizet. | Zkoumáme se.  Restartování zařízení se většinou vrátí do. |
| Wi-Fi | Tlačítko hardware, které přepíná Wi-Fi SoftAP a vypnuto, někdy nefunguje. | Pokračujte stisknutím tlačítka nebo restartováním zařízení. |
| Wi-Fi | Po připojení k Wi-Fi se uživatelům může zobrazit zpráva: <br> "Tato Wi-Fi síť používá starší standard zabezpečení." | SoftAP DevKit používá šifrovací algoritmus WEP. |
| Wi-Fi | Nepovedlo se připojit k SoftAP z počítače s Windows 10 a zobrazí se tato chybová zpráva: <br> Nejde se připojit k této síti | Restartujte DevKit i počítač. |
| Aktualizace zařízení | Kontejnery se po aktualizaci OTA nespustí. | Připojte se přes SSH k zařízení a restartujte IoT Edge kontejner pomocí tohoto příkazu: `systemctl restart iotedge` . Tato akce restartuje všechny kontejnery. |
| Aktualizace zařízení | Uživatelům se může zobrazit zpráva, že se aktualizace nezdařila, i když byla úspěšná. | Potvrďte aktualizaci tak, že přejdete do vlákna zařízení DevKit v IoT Hub a zkontrolujete hodnotu `swVersion` . Tato oprava je opravena po první aktualizaci. |
| Aktualizace zařízení | Uživatelé můžou po první aktualizaci přijít o nastavení Wi-Fi připojení. | Po aktualizaci nastavte Wi-Fi připojení, spusťte prostředí pro zprovoznění. Tato oprava je opravena po první aktualizaci. |
| Aktualizace zařízení | Po provedení aktualizace OTA se uživatelé již nebudou moci přihlásit přes SSH pomocí dříve vytvořených uživatelských účtů a nové uživatele SSH nelze vytvořit prostřednictvím prostředí pro registraci. Tento problém má vliv na systémy, které provádějí aktualizace OTA z následujících verzí předinstalovaných imagí: 2020.110.114.105 a 2020.109.101.105. | Chcete-li obnovit profily uživatelů, proveďte tyto kroky po aktualizaci OTA: <br> Připojte se přes [SSH k vašemu DevKit](./how-to-ssh-into-percept-dk.md) jako uživatelské jméno "root". Pokud jste zakázali přihlašovací jméno uživatele root "SSH" prostřednictvím prostředí pro registraci, musíte ho znovu povolit. Po úspěšném připojení spustit tento příkaz: <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> Chcete-li obnovit předchozí Domovská data uživatele, spusťte následující příkaz: <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Aktualizace zařízení | Po provedení aktualizace OTA dojde ke ztrátě skupin aktualizací. | Aktualizujte značku zařízení podle následujících [pokynů](./how-to-update-over-the-air.md#create-a-device-update-group). |
| Instalační program sady nástrojů pro vývojáře | Nepovinná instalace Caffe může selhat, pokud Docker v systému správně neběží. | Ujistěte se, že je Docker nainstalovaný a spuštěný, a pak zkuste instalaci Caffe zopakovat. |
| Instalační program sady nástrojů pro vývojáře | Volitelná instalace CUDA se v nekompatibilních systémech nezdařila. | Před spuštěním instalačního programu ověřte kompatibilitu systému s CUDA. |
| Docker, síť, IoT Edge | Pokud vaše interní síť používá 172. x. x. x, kontejnery Docker se nepodaří připojit k IoT Edge. | Přidejte speciální oddíl BIP do/etc/Docker/daemon.jsv souboru jako tento: `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Odkazy "Zobrazit stream" v rámci Azure Percept Studio neotevřou nové okno zobrazující webový Stream zařízení. | 1. Otevřete [Azure Portal](https://portal.azure.com) a vyberte **IoT Hub**. <br> 2. klikněte na IoT Hub, ke kterému je zařízení připojené. <br> 3. v části **Automatická správa zařízení** na stránce IoT Hub vyberte **IoT Edge** . <br> 4. ze seznamu vyberte zařízení. <br> 5. v horní části stránky zařízení vyberte **nastavit moduly** . <br> 6. Pokud chcete modul odstranit, klikněte na ikonu koše vedle **HostIpModule** . <br> 7. Pokud chcete akci potvrdit, klikněte na **zkontrolovat + vytvořit** a pak **vytvořit**. <br> 8. Otevřete [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) a v levém panelu nabídek klikněte na **zařízení** . <br> 9. ze seznamu vyberte své zařízení. <br> 10. na kartě **vize** klikněte na **Zobrazit datový proud zařízení**. Vaše zařízení stáhne novou verzi HostIpModule a otevře kartu prohlížeče s webovým datovým proudem vašeho zařízení. |